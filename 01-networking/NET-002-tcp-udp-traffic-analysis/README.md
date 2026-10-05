# NET-002 — TCP vs UDP Traffic Analysis

## Hiring Claim
After reviewing this artifact, a hiring manager has evidence that I can establish and analyze TCP and UDP traffic, interpret socket state, explain TCP sequence and acknowledgment behavior, identify connection establishment and teardown, and show with packet captures how UDP behaves when sent to an open port versus a closed port, including the ICMP Port Unreachable a closed port returns.

## Employer Skills Demonstrated
- TCP/IP traffic analysis
- TCP three-way handshake analysis
- TCP sequence and acknowledgment reasoning
- TCP socket-state analysis
- TCP application-data validation
- TCP connection teardown and TIME-WAIT
- UDP socket and datagram analysis
- Ephemeral-port analysis
- ICMP Destination Unreachable analysis
- Linux `ss`, `nc`, `tcpdump`, and TShark
- Client/server troubleshooting
- Evidence handling and technical documentation

## Scenario / Objective
The objective was to compare TCP and UDP behavior on the same physical lab network using controlled client/server traffic between ENVY and Yoda.

The testing focused on behavior observable on the wire and in operating-system socket tables: ephemeral-port selection, TCP establishment, socket state, sequence/ACK behavior, application data, teardown, UDP delivery, and UDP closed-port error behavior.

## Environment
| System | Role | Address |
|---|---|---|
| ENVY / Fedora | Client, traffic generator, packet capture | `10.10.20.101/24` |
| Yoda / Proxmox | TCP and UDP server | `10.10.20.10/24` |
| TL-SG108E | Layer 2 switching | Lab switch |
| netcat (`nc`) | Controlled TCP/UDP client and server | Application traffic |
| `ss` | Socket-state validation | Linux |
| `tcpdump` | Packet capture and analysis; PCAPs written to `~/net-002-evidence/` on ENVY (screenshots 09, 10) | ENVY |
| TShark | Public evidence extraction | Victus (the TShark runs aren't shown in the screenshots) |

## Ephemeral-Port Baseline
ENVY's configured IPv4 ephemeral-port range was:

```text
32768 60999
```

I didn't capture that check as a screenshot.

During testing ENVY selected:

| Test | Source Port |
|---|---:|
| TCP connection | `54000` |
| UDP open-port | `46681` |
| UDP closed-port | `34871` |

All three are inside that range.

# Part 1 — TCP

## TCP Socket State and Four-Tuple
Yoda ran a controlled TCP service on `10.10.20.10:8080`.

Screenshot 01 shows two `ss` checks on Yoda: `ss -ltnp` with a `LISTEN` socket on `10.10.20.10:8080`, and `ss -tanp` with an `ESTAB` socket from `10.10.20.101:54000`.

![Yoda LISTEN and ESTAB sockets](evidence/screenshots/01-yoda-listen-and-established-sockets.png)

ENVY selected source port `54000`, producing this four-tuple:

```text
Source IP:        10.10.20.101
Source Port:      54000
Destination IP:   10.10.20.10
Destination Port: 8080
```

ENVY showed the corresponding `ESTAB` socket:

![ENVY established TCP socket](evidence/screenshots/02-envy-tcp-established-socket.png)

**Correction (October 5, 2026):** I originally wrote that Yoda kept the listening socket alongside the established socket and that the listener stayed available for more clients. Screenshot 01 doesn't show that. The `LISTEN` line belongs to `nc` pid `525246` and the `ESTAB` line belongs to `nc` pid `526231`, so the two outputs came from different `nc` runs. The `ss -tanp` output shows no `LISTEN` line at all. Plain `nc -l` accepts one connection and stops listening, so this screenshot does not show `LISTEN` and `ESTAB` at the same time.

## TCP Three-Way Handshake
The live packet capture showed:

```text
ENVY                                      YODA

        SYN ---------------------------->
            <-------------------- SYN, ACK
        ACK ---------------------------->

                 ESTABLISHED
```

Observed values included:

```text
ENVY -> Yoda
Flags [S]
seq 2705272252

Yoda -> ENVY
Flags [S.]
seq 2996551558
ack 2705272253

ENVY -> Yoda
Flags [.]
relative seq 1
relative ack 1
```

The SYN consumes one sequence number, so Yoda acknowledged ENVY's initial sequence number plus one.

![TCP handshake, data, and ACK sequence](evidence/screenshots/03-tcp-handshake-data-ack-sequence.png)

## TCP Application Data and Acknowledgment
ENVY sent `PROVE-TCP` plus a newline, producing a 10-byte payload.

The capture showed:

```text
ENVY -> Yoda
Flags [P.]
seq 1:11
ack 1
length 10

Yoda -> ENVY
Flags [.]
seq 1
ack 11
length 0
```

ACK `11` means bytes through sequence 10 were received and byte 11 is expected next.

Yoda displayed the payload:

![Yoda received TCP payload](evidence/screenshots/04-yoda-received-tcp-payload.png)

A later one-byte transmission advanced ENVY's relative sequence range from `11` to `12`, and Yoda acknowledged `12`.

TCP acknowledgments therefore track the next expected byte rather than simply counting packets.

## Independent Sequence Spaces
Each endpoint maintains its own TCP sequence space. Immediately before teardown, ENVY was operating around relative sequence `12/13` while Yoda was around `1/2`.

An ACK refers to the next sequence number expected from the opposite endpoint; the two hosts do not share one sequence counter.

## TCP Teardown
ENVY initiated the close. The capture showed a three-segment graceful teardown:

```text
ENVY                                      YODA

FIN, ACK
seq 12, ack 1 -------------------------->

            <-------------------- FIN, ACK
                           seq 1, ack 13

ACK
seq 13, ack 2 -------------------------->
```

![TCP FIN teardown sequence](evidence/screenshots/05-tcp-fin-teardown-sequence.png)

Yoda combined its acknowledgment of ENVY's FIN with its own FIN.

Although the FIN carried zero application payload bytes, FIN consumes one TCP sequence number:

```text
ENVY FIN seq 12 -> Yoda ACK 13
Yoda FIN seq 1  -> ENVY ACK 2
```

## TIME-WAIT
After ENVY initiated the close, its socket table showed the connection in `TIME-WAIT`.

![ENVY TCP teardown state](evidence/screenshots/06-envy-tcp-teardown-state.png)

This provides host-level evidence of the TCP lifecycle after the packet-level FIN exchange.

# Part 2 — UDP

## UDP Listener State
Yoda ran a UDP service on `10.10.20.10:9090`.

The socket table showed an `UNCONN` socket rather than a TCP-style LISTEN/ESTAB lifecycle.

![Yoda UDP listener](evidence/screenshots/07-yoda-udp-9090-listener.png)

The UDP socket could receive datagrams without first establishing a transport-layer connection to ENVY.

## UDP Delivery to an Open Port
ENVY sent `PROVE-UDP` plus a newline, producing a 10-byte UDP payload.

The raw capture contained one UDP datagram:

```text
10.10.20.101:46681 -> 10.10.20.10:9090
UDP
length 10
```

Yoda received the payload:

![Yoda received UDP payload](evidence/screenshots/08-yoda-received-udp-payload.png)

The public TShark extraction is stored in `evidence/udp-open-port-summary.txt`.

![UDP single datagram and no persistent session](evidence/screenshots/09-udp-single-datagram-no-session.png)

After the one-shot client completed, ENVY had no matching persistent UDP client socket to display.

## No UDP Transport Acknowledgment
The open-port capture contained the datagram sent from ENVY to Yoda but no UDP transport-layer acknowledgment returned from Yoda.

The capture filter for this test was `host 10.10.20.10 and udp port 9090`, so it only shows that no UDP traffic came back on port 9090. It would not have recorded an ICMP reply or other traffic from Yoda.

An application can implement its own acknowledgments, retries, or reliability on top of UDP. UDP itself does not provide TCP-style connection establishment, sequence-number reliability, transport acknowledgments, retransmission, ordered delivery, or FIN teardown.

# Part 3 — UDP to a Closed Port

## Closed-Port Test
I stopped the UDP/9090 listener before this test. I didn't capture an `ss` check after stopping it. The empty `ss -lunp | grep ':9090'` in screenshot 08 was run before the listener started, not after it stopped. The ICMP Port Unreachable below is what shows nothing was bound to UDP/9090 on Yoda when the datagram arrived.

ENVY then sent `CLOSED-UDP` plus a newline, producing an 11-byte UDP datagram.

The capture showed:

```text
ENVY:34871 -> Yoda:9090
UDP
length 11
```

followed by:

```text
Yoda -> ENVY
ICMP Destination Unreachable
UDP port 9090 unreachable
```

![UDP closed port and ICMP unreachable](evidence/screenshots/10-udp-closed-port-icmp-unreachable.png)

The public TShark extraction is stored in `evidence/udp-closed-port-summary.txt`.

## Why ICMP Appears in a UDP Test
UDP itself did not acknowledge or reject the datagram. Yoda's IP stack generated a separate ICMP error indicating that the UDP destination port was unreachable.

The ICMP error includes information from the offending packet so the receiving host can associate the error with the traffic that caused it.

```text
UDP open port:
ENVY -> UDP datagram -> Yoda
No UDP transport acknowledgment

UDP closed port:
ENVY -> UDP datagram -> Yoda
ENVY <- ICMP Port Unreachable <- Yoda
```

The ICMP response is not a UDP acknowledgment; it is a separate network-layer error-reporting mechanism.

# TCP vs UDP — Observed Results

| Behavior | TCP Test | UDP Test |
|---|---|---|
| Server port | `8080` | `9090` |
| Client source port | `54000` | `46681` open / `34871` closed |
| IP protocol number | TCP `6` | UDP `17` |
| Connection handshake | SYN → SYN/ACK → ACK | None |
| Server socket state | `LISTEN`, then `ESTAB` (separate `nc` runs) | `UNCONN` |
| Client established state | `ESTAB` | None in one-shot test |
| Open-port payload | 10 bytes | 10 bytes |
| Transport acknowledgment | ACK advanced to `11` | None |
| Sequence tracking | Yes | No TCP-style sequence space |
| Graceful teardown | FIN/ACK exchange | None |
| Post-close state observed | `TIME-WAIT` | No equivalent session state |
| Closed-port behavior | Not tested | ICMP Port Unreachable observed |

## Key Technical Conclusions
TCP demonstrated ephemeral client-port selection, four-tuple identification, SYN/SYN-ACK/ACK establishment, independent sequence spaces, byte-oriented acknowledgments, persistent established socket state, FIN sequence consumption, graceful teardown, and `TIME-WAIT`.

UDP demonstrated ephemeral client-port selection, a bound `UNCONN` server socket, one-shot datagram delivery, no TCP-style transport acknowledgment or established session, no FIN teardown, and ICMP Port Unreachable when the destination UDP port was closed.

## Evidence Handling
The two UDP PCAPs are retained locally under `evidence/raw/`, which is excluded from version control.

Public evidence consists of TShark-derived Layer 3/4 summaries and screenshots required to support the findings. Persistent Layer 2 identifiers and unrelated raw packet data are not published when unnecessary to the hiring claim.

## Evidence Index
| Evidence | Purpose |
|---|---|
| `01-yoda-listen-and-established-sockets.png` | Yoda TCP/8080 `LISTEN` socket and an `ESTAB` socket, from different `nc` runs |
| `02-envy-tcp-established-socket.png` | ENVY TCP client socket and four-tuple |
| `03-tcp-handshake-data-ack-sequence.png` | TCP establishment, payload sequence range, ACK behavior |
| `04-yoda-received-tcp-payload.png` | Application data received by Yoda |
| `05-tcp-fin-teardown-sequence.png` | Three-segment FIN/ACK teardown |
| `06-envy-tcp-teardown-state.png` | ENVY `TIME-WAIT` after client-initiated close |
| `07-yoda-udp-9090-listener.png` | UDP socket in `UNCONN` state |
| `08-yoda-received-udp-payload.png` | UDP payload received by Yoda |
| `09-udp-single-datagram-no-session.png` | One UDP datagram and no persistent client session |
| `10-udp-closed-port-icmp-unreachable.png` | Closed UDP port and ICMP Port Unreachable |
| `udp-open-port-summary.txt` | TShark-derived open-port UDP evidence |
| `udp-closed-port-summary.txt` | TShark-derived closed-port UDP/ICMP evidence |

## Status
**PROVEN**

TCP and UDP behavior was generated on the physical lab network, validated through operating-system socket state and packet evidence, compared directly, and documented with focused public evidence.
