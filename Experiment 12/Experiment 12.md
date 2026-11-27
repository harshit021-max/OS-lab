# Experiment 12

## Next-generation firewall features using pfSense

## Aim

To place a client behind a pfSense VM, configure WAN and LAN interfaces, test an ordered firewall rule, and examine intrusion prevention and DNS filtering through add-on packages.

## Theory

A firewall enforces policy between networks using criteria such as interface, address, protocol, port and connection state. A stateful firewall tracks permitted connections so associated traffic can be handled consistently. For the ordinary interface rules used in this lab, ordering matters and a matching pass rule above a block can allow the traffic first. Existing states can also affect a test after rules change. See [pfSense rule methodology](https://docs.netgate.com/pfsense/en/latest/firewall/rule-methodology.html) for interface-rule processing and stateful reply handling.

Next-generation firewall capabilities can include deeper inspection, intrusion prevention, application awareness, filtering and central logging. This practical combines pfSense stateful rules with Suricata and pfBlockerNG. Installing packages alone does not establish that a particular inspection or blocking feature is enabled or effective.

Suricata examines traffic using detection rules and can support prevention when the chosen mode and interface configuration actually block it. pfBlockerNG adds address-list and DNS-based controls. DNSBL blocks or alters name resolution for selected domains. It is domain-level filtering and does not by itself inspect every URL path inside encrypted HTTPS sessions. Clients that use alternate resolution paths may require additional policy controls.

## Lab topology

| System/interface | VirtualBox attachment | Example configuration |
| --- | --- | --- |
| pfSense WAN | NAT | `em0`, DHCP address such as `10.0.2.15/24` |
| pfSense LAN | Internal Network `lan` | `em1`, `192.168.1.1/24` |
| Kali client | Internal Network `lan` | DHCP address such as `192.168.1.100` |

Both VMs run together. The two internal adapters must share the exact network name. Confirm interface assignment rather than assuming that NIC names always match the handout. The client must use pfSense as its path out of the LAN for the firewall test to be meaningful.

## Requirements

VirtualBox, the appropriate pfSense installation media, a pfSense VM with two adapters, and a separate client VM. Allocate resources suitable for the chosen pfSense release and installed packages. The handout's example allocation is 1 GB RAM and an 8 GB disk, rather than a universal sizing requirement.

## Procedure

1. Create a FreeBSD 64-bit VM, attach the pfSense installer and configure the NAT and internal adapters.
2. Install to the VM's disk, remove the installer media and reboot. Installer options depend on the release.
3. Assign WAN/LAN and verify the LAN address and DHCP service.
4. Move the client onto Internal Network `lan` and confirm its assigned address and route.
5. Open the local GUI, complete the wizard and change the initial administrator password. Select the India time-zone entry appropriate to the installation.
6. Review the private-network WAN option for the NAT-based lab. The handout disables RFC1918 and bogon blocking together. The private NAT address explains the RFC1918 exception, while a separate bogon exception should be justified by actual lab traffic and logs.
7. Add the following LAN rule above the broad default LAN pass rule. Enable rule logging, save and apply the change.

| Field | Value |
| --- | --- |
| Action | Block |
| Interface | LAN |
| Address family | IPv4 |
| Protocol | ICMP |
| Source | LAN subnets |
| Destination | Any |
| Logging | Enabled for the test rule |

8. Repeat the ping test with a fresh connection/state and inspect its matching firewall log. Remove only the relevant test state if an earlier state affects the comparison.
9. Install Suricata through the package manager, configure the selected inspection interface and rules, and verify its documented alert/block mode with an approved test.
10. Install and configure pfBlockerNG DNSBL. Point the lab client at the intended resolver, update the selected lists and test a designated blocked domain.
11. Record the firewall, Suricata and DNSBL logs separately. A missing ping reply alone does not identify which control caused it.

## Commands and configuration extracted from the PDF

The client-address check is from PDF page 17. The same ping command is used before and after the rule change on page 22.

```bash
ip -br addr
ping -c 3 8.8.8.8
```

The following inspection command is an added route check for the lab topology.

```bash
ip route
```

The source GUI address and initial credentials are shown on PDF page 18. Use the installation's actual initial credentials if they differ, then set a new administrator password in the setup wizard.

```text
GUI: https://192.168.1.1
Initial username in the handout: admin
Initial password in the handout: pfsense
```

The handout's interface example on PDF page 16 is configuration output, not a shell command.

```text
WAN (wan) -> em0 -> v4/DHCP4 : 10.0.2.15/24
LAN (lan) -> em1 -> v4 : 192.168.1.1/24
```

## Observations and result

Record the client address, default route, interface assignments, rule position and before/after ping measurements. The source example shows replies before the ICMP block and no replies afterwards. Confirm the matching logged block before attributing a failure to the rule. Log visibility depends on logging configuration, so the dashboard is not automatically a record of every allowed packet.

The exercise demonstrates routed policy enforcement and adds separately verifiable inspection and filtering capabilities. Document only the features actually configured and tested.

## Conclusion

The pfSense topology illustrates how a firewall placed in the client's network path can enforce ordered, stateful traffic policies. Suricata and pfBlockerNG provide additional inspection and filtering capabilities whose effectiveness depends on their configuration and deployment. Before-and-after measurements, routing checks and matching logs are needed to establish which configured control caused an observed result.

## Viva questions

- Why does the client need pfSense as its route out of the LAN?
- Why must the ICMP block precede the broad LAN pass rule?
- How can an existing state affect a repeated test?
- How does DNSBL differ from full URL or TLS-content inspection?

## Sources

`NextGen_Firewall_pfSense_Experiment.pdf`, PDF pages 2–28. The source contains GUI steps and diagnostic commands, rather than a standalone programming-language implementation.
