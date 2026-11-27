# Experiment 5

## Brute-force and dictionary password auditing

## Aim

To compare dictionary-based and exhaustive credential guessing against a local test SSH service and explain the effect of password choice, rate limits and additional authentication factors.

## Theory

A dictionary attack tests a finite list of candidate passwords. It succeeds only if a suitable candidate is present. A brute-force attack enumerates a defined search space. For an alphabet containing C characters and a fixed password length L, the number of combinations is C^L. A four-digit decimal PIN therefore has 10,000 combinations, including leading zeros.

For uniformly random, independent characters, the idealised entropy is L × log2(C) bits. Human-chosen passwords rarely follow that distribution, so the formula does not measure the actual strength of a predictable word. For lengths from a to b, the total search space is the sum of C^i for i from a through b.

Hydra performs online authentication attempts. Each guess must interact with a service, so network latency, authentication cost, connection limits and lockout rules affect the rate. Offline recovery instead tests local password verifiers and depends on the hash algorithm and available computation. A larger online task count can cause service errors rather than a proportional speed increase.

## Source and environment

No new Experiment 5 PDF was attached. This file uses the repository's existing [Experiment5 Theory](<Experiment5 Theory>) and [Experiment5.pdf](Experiment5.pdf). PDF pages 1–3 show a Kali VirtualBox environment. Pages 4–6 contain the command demonstrations below. They target `127.0.0.1`, the same lab machine.

## Commands and input extracted from the screenshots

The screenshots run from a root shell. The following commands reproduce the displayed command text. `worldlist.txt` is the spelling used in the screenshots.

### Dictionary input — PDF page 4

```shell
nano worldlist.txt
```

```text
password
password123
paassword111
password001
password99
password1
password991
```

### Local dictionary demonstration — PDF page 5

Use a disposable local account. `passwd` prompts interactively for the chosen lab password.

```shell
systemctl start ssh
useradd -m test2
passwd test2
hydra -l test2 -P worldlist.txt ssh://127.0.0.1
```

### Four-digit exhaustive demonstration — PDF page 6

```shell
systemctl start ssh
useradd -m test4
passwd test4
hydra -l test4 -x 4:4:1 ssh://127.0.0.1
```

`-l` supplies one username, `-P` supplies the password-list filename, and `-x 4:4:1` specifies lengths four through four with Hydra's digit character set. Account creation and service management need administrative privileges. Keep the exercise on the disposable local SSH service shown in the evidence.

## Procedure and observations

1. Check that the local SSH service is available and that password authentication is configured for the test account.
2. Prepare the small candidate list, set a corresponding lab password and run the dictionary demonstration.
3. Record whether a valid candidate was found, the target and the attempted account.
4. Create the separate four-digit test account and run the exhaustive demonstration.
5. Compare the number of candidates, elapsed time and service warnings.

The page-5 screenshot reports `test2` with `password123`. Page 6 reports `test4` with `0001`, but also displays restore-file, connection and incomplete-target warnings. Those warnings are part of the evidence and should not be reported as a clean completed run. These are recorded source outcomes, not new runs performed while writing this file.

## Defence and result

Long unique passwords reduce guessability. Rate limiting, progressive delays, monitoring and carefully designed lockout policies constrain online attempts. SSH public-key authentication can remove password guessing from the login path. MFA adds a further requirement, although compromised endpoints and session theft still require separate controls.

The two demonstrations illustrate the difference between a candidate list and a generated keyspace. Their outcomes depend on the deliberately weak local test credentials.

## Conclusion

Dictionary guessing tests a selected list of candidates, while brute-force guessing enumerates a defined set of possibilities. The supplied local SSH demonstrations show why weak test credentials are vulnerable to these approaches and why service limits affect online attempts. Long unique passwords, authentication controls and monitoring reduce the opportunity for successful guessing.

## Viva questions

- Why does a dictionary attack fail when the password is absent from the list?
- How many four-digit decimal strings include leading zeros?
- Why do online and offline guessing have different performance limits?
