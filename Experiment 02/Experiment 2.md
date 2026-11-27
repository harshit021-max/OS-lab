# Experiment 2

## Port scanning and information gathering using Nmap

## Aim

To identify reachable hosts, compare TCP and UDP port states, and distinguish a port's conventional service name from an actively identified service.

## Theory

An IP address identifies a network interface, while a transport port identifies an application endpoint. Host discovery asks whether a host responds. Port scanning tests selected endpoints. Version detection sends additional probes to identify the application answering on an open port.

A TCP SYN scan sends the first packet of a TCP handshake. A SYN/ACK usually indicates an open port, while a reset indicates a closed port. Missing responses or filtering errors can produce a filtered classification. A TCP connect scan completes a connection through the operating system when a SYN scan is unavailable.

UDP has no connection handshake. A UDP response establishes that a service answered. ICMP port-unreachable indicates a closed UDP port. Silence can mean that a service is listening but did not answer the probe, or that a firewall dropped it, producing `open|filtered`.

| State | Interpretation |
| --- | --- |
| `open` | An application is accepting communication |
| `closed` | The host responded, but no service is listening on that port |
| `filtered` | Filtering prevents a definite open/closed decision |
| `open|filtered` | The probe cannot distinguish an open port from filtering |

## Requirements

Nmap, Ncat and an isolated lab network or the local machine. The PDF uses Windows with Npcap and an administrator terminal. On Linux, raw-packet scans require suitable privileges.

## Commands extracted from the PDF

### Installation and discovery checks

PDF pages 11–13 use these commands. Replace the sample LAN address with the assigned lab host. The subnet example belongs to an isolated VM network.

```shell
nmap --version
ipconfig
nmap -sn 192.168.61.238
nmap -sn 192.168.56.0/24
nmap 127.0.0.1
```

### Controlled TCP listener and scans

Start the listener in one terminal. Run the scans in another, stop the listener with Ctrl+C, then repeat the port scan. These commands come from PDF pages 14–17.

```shell
ncat -l -k 8000
```

```shell
nmap -p 8000 127.0.0.1
nmap -sS -p 8000 127.0.0.1
nmap -sS --reason -p 8000 127.0.0.1
nmap -sS -p 22,80,443,445,8000 127.0.0.1
nmap -sV 127.0.0.1
nmap -sV -p 135,445 127.0.0.1
```

The optional HTTP demonstration replaces the Ncat listener with a Python HTTP server. Stop the previous listener first so port 8000 is free.

```shell
python -m http.server 8000
```

```shell
nmap -sV -p 8000 127.0.0.1
```

### Controlled UDP listener and scans

Run the listener separately from the scans. PDF page 18 supplies the commands.

```shell
ncat -u -l -k 9999
```

```shell
nmap -sU -p 9999 127.0.0.1
nmap -sU -p 53,123,161 127.0.0.1
```

## Procedure and observations

1. Record the host address, subnet mask, gateway and Nmap version.
2. Perform discovery and distinguish reachability from port availability.
3. Start Ncat, scan TCP port 8000 and record its state and `--reason`.
4. Stop Ncat and repeat the scan to observe the transition to closed, subject to firewall behaviour.
5. Compare a basic service label with the output of `-sV` against the HTTP server.
6. Repeat the listener experiment with UDP and explain any ambiguous state.

The label `http-alt` on port 8000 is a port-name association. Ncat does not become an HTTP server because that label appears. Service probes provide additional identification evidence.

## Result

The experiment demonstrates host discovery, controlled open/closed TCP states and the ambiguity of UDP responses. Record observed results from the actual run.

## Conclusion

Host discovery, port scanning and service detection answer different questions about a system's network exposure. The controlled listener examples explain how TCP port states change with service availability and why UDP results can remain ambiguous. Scan findings should be interpreted alongside response reasons, firewall behaviour and the actual service configuration.

## Viva questions

- Why can a silent UDP service appear `open|filtered`?
- How does `-sV` add information beyond `-p`?
- Why should the TCP listener and scanner use separate terminals?

## Sources

`Port Scanning and Information Gathering.pdf`, PDF pages 3–23. Existing practical evidence is in [Experiment2.pdf](Experiment2.pdf).
