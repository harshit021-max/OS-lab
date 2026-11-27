# Experiment 8

## Intrusion detection using Snort

## Aim

To configure Snort 3 as a network intrusion detection system, load a local ICMP rule, generate known traffic and interpret the resulting alerts.

## Theory

A network IDS observes packets on an interface and compares decoded traffic with detection rules. A host IDS examines activity on an individual system. Signature-based rules describe recognised patterns, while anomaly detection looks for deviations from expected behaviour. A match is evidence that a rule condition occurred and requires interpretation in context.

Snort captures traffic, decodes protocols, applies configured inspection and detection logic, and sends alerts to an output mechanism. A rule header defines an action, protocol, source, direction and destination. Options supply details such as a message, signature identifier and revision. A broad ICMP rule can match ordinary diagnostic traffic, so an alert does not by itself demonstrate an attack.

IDS operation observes and alerts. Prevention requires a deployment and configuration that can block traffic, such as an inline IPS path. Running a passive alert command does not make it a blocking firewall.

## Requirements

The supplied PDF uses Snort 3 on a Kali VM. Confirm the version with `snort -V`, the actual interface with `ip -br addr`, and the gateway with `ip route`. Its example has `eth0`, VM address `10.0.2.15` and gateway `10.0.2.2`. Adapt these values to the lab. Snort 2 configuration syntax differs from the Snort 3 Lua configuration used here. See the [Snort 3 configuration guide](https://docs.snort.org/start/configuration) for the configuration-file format and `-c` option.

## Commands and rule extracted from the PDF

### Setup — PDF pages 10–12

```bash
sudo apt update
sudo apt install snort -y
snort -V
ip -br addr
ip route
mkdir -p ~/snort-lab
cd ~/snort-lab
nano local.rules
```

Enter the source rule in `local.rules`.

```text
alert icmp any any -> any any (msg:"LAB: ICMP traffic detected"; sid:1000001; rev:1;)
```

| Rule part | Meaning |
| --- | --- |
| `alert` | Generate an alert for a match |
| `icmp` | Select ICMP traffic |
| `any any` | Any source address and port field |
| `-> any any` | Any destination address and port field |
| `msg` | Human-readable alert message |
| `sid:1000001` | Local signature identifier |
| `rev:1` | Rule revision |

### Validation and capture — PDF pages 14–16

The first command is the PDF's main-configuration test. The capture command explicitly adds the local rule through `-R`. Replace `/home/kali` if the actual home directory differs.

```bash
sudo snort -T -c /etc/snort/snort.lua
sudo snort -q -c /etc/snort/snort.lua \
  -R /home/kali/snort-lab/local.rules -i eth0 -A alert_fast
```

A useful additional validation includes the same local rule that will be used during capture. This is an implementation check added to the source procedure.

```bash
sudo snort -T -c /etc/snort/snort.lua \
  -R /home/kali/snort-lab/local.rules
```

Keep capture running and generate traffic from a second terminal.

```bash
ping -c 4 10.0.2.2
```

### Save alerts — PDF page 19

Stop the earlier capture before starting this logging run. Generate test traffic again, then stop capture with Ctrl+C before reviewing the completed log.

```bash
sudo snort -q -c /etc/snort/snort.lua -R /home/kali/snort-lab/local.rules \
  -i eth0 -A alert_fast 2>&1 | tee ~/snort-lab/alerts.log
cat ~/snort-lab/alerts.log
grep -c "LAB:" ~/snort-lab/alerts.log
```

## Observations and result

The source alert on PDF page 17 contains a timestamp, `[1:1000001:1]`, the rule message, protocol and source/destination addresses. The bracketed values identify generator, signature and revision. Four echo requests and four replies can yield eight matching IPv4 alerts when both directions are visible, but packet loss, background traffic and the configured rules affect the actual total.

Record the version, interface, rule, validation result, test traffic and matching alert lines. The result demonstrates that a known rule can detect known traffic. A successful configuration test alone does not establish that the intended interface was captured or that the local rule matched.

## Conclusion

A local Snort rule provides a clear link between a defined traffic pattern and the alert it generates. The ICMP example highlights the need to validate the configuration, capture the correct interface and examine matching alert evidence. An alert requires contextual interpretation, and preventing traffic requires an appropriate blocking deployment.

## Viva questions

- Why can an ordinary ping produce an IDS alert?
- What do `sid` and `rev` identify?
- Why should the configuration test include the local rule file?
- What additional deployment capability is needed to prevent traffic?

## Sources

`Snort_IDS_Experiment.pdf`, PDF pages 2–21. Existing practical evidence is in [Experiment8.pdf](Experiment8.pdf). The new handout's Snort 3 procedure is used for this Markdown file.
