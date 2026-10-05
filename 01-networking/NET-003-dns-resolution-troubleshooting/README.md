# NET-003 — DNS Resolution and Troubleshooting

## Hiring Claim
> After reviewing this artifact, a hiring manager has evidence that I can trace DNS queries from a local stub to more than one upstream resolver on a dual-homed Linux host, read a DNS-over-TCP/53 exchange at the sequence/ACK level, and tell a negative DNS answer (NXDOMAIN) apart from a resolver that never answers.

## Skills Demonstrated
- DNS troubleshooting and packet analysis
- `systemd-resolved` / local stub analysis
- Multi-interface resolver-path analysis
- UDP/53 and TCP/53
- A, AAAA, CNAME, MX, TXT, NS, and SOA interpretation
- NXDOMAIN vs NOERROR/empty-answer reasoning
- DNS timeout troubleshooting
- TTL/cache analysis
- `dig`, `resolvectl`, `ip route`, `tcpdump`, and TShark
- TCP sequence/ACK analysis applied to DNS

## Environment
ENVY was dual-homed during testing.

| Link | Address | DNS |
|---|---|---|
| Wi-Fi (`wlo1`) | `192.168.1.3` | `1.1.1.1`, `1.0.0.1` |
| Lab Ethernet | `10.10.20.101` | `10.10.20.1`, domain `lan` |

`/etc/resolv.conf` pointed applications to the local `systemd-resolved` stub at `127.0.0.53`.

## Normal Resolution Through the Local Stub
A normal `dig example.com` query reported `SERVER: 127.0.0.53#53 (UDP)`. That proves the application queried the local stub, but not which upstream resolver was used.

The packet capture exposed the path. The sanitized extraction is in `evidence/dns-normal-resolution-summary.txt`.

For the A lookup:

```text
Application: 127.0.0.1:44308 -> 127.0.0.53:53
Lab path:    10.10.20.101:49642 -> 10.10.20.1:53
Wi-Fi path:  192.168.1.3:35762 -> 1.0.0.1:53
```

Both upstream resolvers returned `172.66.147.243` and `104.20.23.154`. In frame order, the lab-side response appears first, then the stub's answer to the application, then the Cloudflare response. The timestamp column in the extraction is empty, so frame order is the only timing evidence I kept.

A later AAAA query repeated the dual-upstream behavior through `10.10.20.1` and `1.0.0.1`.

### Finding
Route metrics alone did not explain DNS behavior. `systemd-resolved` had both links available for DNS, and packet evidence showed queries over both paths.

## Direct Resolver Validation
Direct queries bypassed the stub:

```text
dig @10.10.20.1 example.com A  -> 10.10.20.1#53 (UDP)
dig @1.0.0.1 example.com A     -> 1.0.0.1#53 (UDP)
```

Route lookup confirmed:

```text
10.10.20.1 dev <lab NIC> src 10.10.20.101
1.0.0.1 via 192.168.1.1 dev wlo1 src 192.168.1.3
```

Correction (October 5, 2026): this line originally read `dev lab-Ethernet`, which is not a kernel interface name. I relabeled it as `<lab NIC>`. In NET-005, the same ENVY lab NIC shows as `enp0s20f0u3u3c2`, but I didn't capture the `ip route get` output for this project, so I can't show the exact name it printed here.

This separated the application-facing stub, upstream resolver behavior, and direct resolver queries governed by routing.

## DNS over TCP/53
A DNS query was deliberately forced over TCP:

```text
dig +tcp @1.0.0.1 example.com A
```

The result explicitly showed `SERVER: 1.0.0.1#53 (TCP)`.

![Forced DNS TCP query](evidence/screenshots/02-dig-forced-tcp53-result.png)

The 11-packet capture demonstrated SYN/SYN-ACK/ACK establishment, a DNS query, a DNS response, acknowledgments, and FIN teardown.

![DNS over TCP full transaction](evidence/screenshots/01-dns-tcp53-full-transaction.png)

The sanitized packet evidence is in `evidence/dns-tcp53-summary.txt`.

The DNS query carried 54 bytes of TCP payload at relative `seq 1`; the resolver acknowledged `55`. The DNS response carried 74 bytes and the client acknowledged `75`. During teardown, FIN advanced the client sequence from `55` to `56` and the server sequence from `75` to `76`.

### Finding
DNS is not limited to UDP/53. This controlled test carried the complete DNS request and response inside a TCP/53 connection and applied the TCP sequence/ACK behavior demonstrated in NET-002 to an application protocol.

## DNS Record Investigation
Direct queries were used to examine common records:

| Record | Meaning |
|---|---|
| A | IPv4 address |
| AAAA | IPv6 address |
| CNAME | Canonical-name alias |
| MX | Mail exchanger |
| TXT | Text associated with a DNS name, often policy/verification |
| NS | Authoritative name servers |
| SOA | Zone authority and administrative parameters |

A `google.com` investigation returned multiple A/AAAA addresses, an MX target, NS records, and TXT records including SPF and service/domain-verification data. A direct `www.google.com CNAME` query returned no CNAME result, so no alias was inferred.

## Negative DNS Answers

### NOERROR with No Answer
A deliberately nonexistent hostname under `example.com` returned:

```text
status: NOERROR
ANSWER: 0
AUTHORITY: 1
```

The response included the zone SOA. DNS successfully processed the request but did not return the requested record.

Correction (October 5, 2026): a name that doesn't exist would normally return NXDOMAIN. NOERROR with an empty answer is NODATA, which normally means "the name exists, but not with that record type." The likely explanation is that the `example.com` zone is hosted by a provider that answers missing names with NOERROR/NODATA instead of NXDOMAIN. Cloudflare does this as part of its compact DNSSEC negative answers, and the `example.com` addresses returned in this project are Cloudflare addresses. I didn't capture DNSSEC records for this query, so this is the likely explanation, not a proven one. I also didn't keep a screenshot or extraction of this query.

### NXDOMAIN
A query under the reserved `.invalid` namespace returned:

```text
status: NXDOMAIN
ANSWER: 0
AUTHORITY: 1
```

![DNS NXDOMAIN response](evidence/screenshots/03-dns-nxdomain-response.png)

The resolver was reachable and returned a valid negative DNS response. NXDOMAIN therefore does not mean that DNS itself is unavailable.

## DNS Server Timeout
A controlled unused lab address (`10.10.20.250`) was queried directly with a two-second timeout and one attempt. No DNS response was received:

```text
communications error to 10.10.20.250#53: timed out
no servers could be reached
```

![DNS server timeout](evidence/screenshots/04-dns-server-timeout.png)

The preceding ping also received no reply, but ping failure alone was not treated as proof that a host was absent because ICMP can be filtered.

`10.10.20.250` is on ENVY's connected `10.10.20.0/24` network, so ENVY has to resolve it with ARP before it can send the query. With no host at that address, the likely failure point is ARP: no reply means the DNS query never had a destination MAC to go to. I didn't capture ARP or the neighbor table for this test, so that part isn't evidenced.

| Condition | DNS response? | Interpretation |
|---|---:|---|
| Successful answer | Yes | Requested data returned |
| NOERROR / empty answer | Yes | Query processed, requested data not returned |
| NXDOMAIN | Yes | Queried name does not exist |
| Timeout | No | No DNS response before timeout |

## TTL and Local Cache Observation
After flushing `systemd-resolved` caches, a first `example.net` query through `127.0.0.53` took 22 ms. An immediate second query through the same stub took 0 ms, behavior consistent with local caching.

System-wide cache counters also changed, but they include unrelated host DNS activity and were not attributed solely to the controlled queries. Returned TTL values were not treated as a simple single-cache countdown because multiple upstream paths and distributed recursive infrastructure were involved.

## Background Traffic and Scope
The broad `port 53` capture also contained unrelated DNS traffic generated by Fedora and applications. The controlled transaction was isolated by query name and correlated using addresses, ports, DNS transaction IDs, timestamps, and responses.

A capture filter defines what is collected; it does not automatically define which packets belong to the investigation.

## Checksum-Offload Observation
In screenshot 01, every outbound packet from `192.168.1.3` shows `cksum ... (incorrect -> ...)`, while every inbound packet from `1.0.0.1` shows `(correct)`. This was not treated as malformed DNS traffic because locally captured outbound packets may be observed before NIC checksum offload completes calculation.

## Evidence Limits
These parts of the README describe work I did, but I didn't keep public evidence for them:

- The `dig example.com` result showing `SERVER: 127.0.0.53#53 (UDP)` and the `/etc/resolv.conf` stub setting. The extraction shows traffic to `127.0.0.53`, but not the dig output or the file.
- The direct `dig @10.10.20.1` and `dig @1.0.0.1` UDP queries and the `ip route get` output.
- The `google.com` A/AAAA/MX/NS/TXT results and the `www.google.com CNAME` query.
- The NOERROR/NODATA query under `example.com`.
- The cache flush and the 22 ms vs 0 ms timing comparison.
- ARP or neighbor-table state for the `10.10.20.250` timeout.

## Evidence Handling
Original PCAPs remain local under `evidence/raw/` and are excluded from version control. Public packet evidence is TShark-derived and limited to fields needed to support the findings.

## Evidence Index
| Evidence | Purpose |
|---|---|
| `01-dns-tcp53-full-transaction.png` | Complete DNS-over-TCP transaction |
| `02-dig-forced-tcp53-result.png` | Application proof of TCP/53 |
| `03-dns-nxdomain-response.png` | Valid NXDOMAIN response |
| `04-dns-server-timeout.png` | No-response DNS failure |
| `dns-normal-resolution-summary.txt` | Stub and dual-upstream A/AAAA evidence |
| `dns-tcp53-summary.txt` | TCP handshake, DNS payload, ACK, and teardown evidence |

## Status
**PROVEN**

The stub-to-dual-upstream path, the TCP/53 transaction, the NXDOMAIN answer, and the timeout are all backed by extractions or screenshots. The record-type, cache, and NODATA observations are not, and are listed under Evidence Limits.

DNS resolution was traced from application to local stub and upstream resolvers; UDP and TCP transport behavior was validated; record and response semantics were investigated; and negative-answer versus no-response failures were distinguished using host and packet evidence.
