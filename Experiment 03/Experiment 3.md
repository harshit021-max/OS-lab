# Experiment 3

## Packet capture and analysis using Wireshark

## Aim

To capture traffic from the correct network interface and identify protocol layers, addresses, ports, request/response pairs and the effect of HTTPS encryption.

## Theory

Wireshark captures packets and decodes their protocol fields. Windows uses Npcap for capture. An interface determines which traffic is visible, so a Wi-Fi session should be examined on the active Wi-Fi adapter. Promiscuous mode does not guarantee visibility of every device's traffic on a switched or encrypted network.

Encapsulation places application data inside transport segments or datagrams, network packets and link-layer frames. Ethernet addresses support local-link delivery. IP addresses support routing. TCP and UDP ports distinguish application endpoints. ARP resolves IPv4 neighbours on a local link, while DNS resolves names to records such as A and AAAA addresses.

TCP establishes a connection using SYN, SYN/ACK and ACK. Sequence numbers identify stream positions and acknowledgements support reliable delivery. ICMP echo requests and replies support reachability tests. HTTPS protects application content with TLS, while addresses, sizes and timing generally remain observable. Without decryption material, the captured application payload cannot simply be read as plaintext.

A capture filter limits what is recorded. A display filter selects what is shown from a saved or live capture. Hiding packets with a display filter does not remove them from the capture file.

## Requirements

Wireshark, Npcap on Windows, a suitable capture interface and permission to capture the selected traffic.

## Commands and filters extracted from the PDF

Generate the demonstrations from PDF pages 12 and 14 in a Windows command prompt.

```shell
ping 8.8.8.8
nslookup example.com
```

The PDF's HTTPS and HTTP comparison uses these browser addresses on pages 19 and 21.

```text
https://example.com
http://neverssl.com
```

Enter the following filters individually in Wireshark's display-filter bar. They are transcribed from PDF pages 11, 17 and 24.

```text
icmp
dns
tcp
udp
tls
http
arp
tcp.flags.syn==1 && tcp.flags.ack==0
ip.addr == 192.168.1.15
ip.src == 192.168.1.15
ip.dst == 192.168.1.15
tcp.port == 443
udp.port == 53
icmp && ip.addr == 8.8.8.8
```

The sample HTTP request shown on PDF page 21 is application data rather than a command.

```http
GET / HTTP/1.1
Host: neverssl.com
```

## Procedure

1. Start capture on the adapter carrying the test traffic.
2. Generate ping traffic. Match echo requests and replies by identifier and sequence number.
3. Run the DNS query. Inspect query names, transaction IDs, answer records and resolver addresses.
4. Open a new TCP-based web connection. Identify the handshake and inspect ports, flags and sequence numbers.
5. Compare a plaintext HTTP request with TLS application data. Browsers may use QUIC over UDP for some HTTPS connections, so inspect the actual transport.
6. Inspect ARP and distinguish the gateway's local MAC address from the remote server's IP address.
7. Open Statistics > Protocol Hierarchy, Endpoints and Conversations.
8. Stop capture and save `packet_capture_experiment.pcapng`.

## Observations and result

For representative packets, record protocol, source, destination, ports where applicable, length and relevant fields. DNS may be cached or encrypted, and ARP may already be cached, so an empty filter alone does not prove a protocol is absent from the network. The exercise connects captured traffic to the network stack and demonstrates the difference between observable metadata and encrypted content.

## Conclusion

Packet analysis connects application activity with transport, network and link-layer behaviour. Comparing HTTP and HTTPS illustrates how encryption protects application content while some communication metadata remains observable. Reliable conclusions depend on selecting the correct interface, applying suitable filters and interpreting the captured packets in context.

## Viva questions

- How do capture filters and display filters differ?
- What do a DNS transaction ID and a TCP sequence number identify?
- Why are some packet fields visible during HTTPS communication?

## Sources

`Wireshark_Packet_Analysis.pdf`, PDF pages 2–26. Existing practical evidence is in [Experiment3.pdf](Experiment3.pdf).
