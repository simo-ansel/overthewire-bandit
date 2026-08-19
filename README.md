# OverTheWire Bandit — Walkthrough & Notes

[![Status](https://img.shields.io/badge/Status-Completed%20Levels_0%E2%86%9233-brightgreen)](#)
[![System](https://img.shields.io/badge/OS-Ubuntu_22.04_LTS-lightgrey)](#)
[![Last Updated](https://img.shields.io/badge/Updated-2025--10--29-blue)](#)

> Practical walkthrough and technical notes for the OverTheWire Bandit wargame.
> This repository documents the Linux, networking, authentication and privilege-escalation techniques used to solve levels 0–33.
> Passwords and challenge credentials are redacted.

---

## Introduction

Bandit is a Linux-based wargame designed to develop practical knowledge of Unix systems and fundamental cybersecurity concepts.

This repository documents the solutions for levels 0–33, with a focus on:

- Linux filesystem and permissions
- File enumeration
- Shell behaviour
- Text and binary analysis
- Encoding and compression
- SSH authentication
- TCP and TLS
- Network reconnaissance
- SUID binaries
- Cron jobs
- Privilege escalation
- Restricted shells
- Git history and repository analysis
- Basic automation and brute-force techniques

The goal is to document not only the commands used to complete each level, but also the underlying security concept demonstrated by the challenge.

---

## Tools

### Remote Access

```text
ssh
scp
```

### Networking

```text
nc
ncat
telnet
openssl s_client
nmap
ss
netstat
```

### Filesystem

```text
ls
cd
pwd
find
file
stat
du
mkdir
mktemp
cp
mv
rm
touch
chmod
```

### Text Processing

```text
grep
awk
sed
cut
sort
uniq
tr
wc
printf
echo
head
tail
```

### Binary and Encoding

```text
strings
xxd
base64
```

### Compression

```text
tar
gzip
gunzip
bzip2
bunzip2
```

### Shell and Process Management

```text
bash
sh
more
vim
jobs
bg
fg
timeout
```

### Git

```text
git clone
git log
git show
git tag
git branch
git checkout
git add
git commit
git push
git status
```

---

## Methodology

The general workflow used throughout Bandit was:

1. Enumerate the current environment.
2. Identify relevant files, processes or network services.
3. Inspect permissions, ownership and execution context.
4. Determine how the target behaves.
5. Identify the trust boundary involved.
6. Determine whether user-controlled input can cross that boundary.
7. Exploit the intended weakness.
8. Verify the result and move to the next level.

The challenge progressively introduces increasingly security-oriented variants of this process.

---

## Level 0 → 1

### Objective

Establish the initial SSH connection and locate the password for the next level.

### Method

Connect to the Bandit server on port 2220:

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

Read the `readme` file:

```bash
cat readme
```

### Takeaway

SSH is the primary remote-access mechanism used throughout the challenge. Always verify the target host, username and port before investigating the environment.

---

## Level 1 → 2

### Objective

Read a file whose name is `-`.

### Method

List the directory:

```bash
ls -la
```

Explicitly reference the filename:

```bash
cat ./-
```

### Takeaway

A filename beginning with `-` may be interpreted as a command-line option. Prefixing it with `./` forces the command to treat it as a path.

This is a basic example of how shell parsing affects command execution.

---

## Level 2 → 3

### Objective

Read a file containing spaces in its name.

### Method

Escape the spaces:

```bash
cat ./--spaces\ in\ this\ filename--
```

or quote the complete path:

```bash
cat "./--spaces in this filename--"
```

### Takeaway

The shell splits unquoted arguments on whitespace. Proper quoting and escaping are therefore essential when handling arbitrary filenames.

---

## Level 3 → 4

### Objective

Locate a hidden file inside the `inhere` directory.

### Method

```bash
cd inhere
ls -la
```

Read the hidden file:

```bash
cat ...Hiding-from-you
```

### Takeaway

Files beginning with `.` are not displayed by a standard `ls` invocation. Using `ls -la` is a reliable first step when enumerating a directory.

---

## Level 4 → 5

### Objective

Identify the only human-readable file among several candidates.

### Method

Inspect all files:

```bash
cd inhere
file ./-file*
```

The relevant file can then be read directly.

```bash
cat ./-file07
```

### Takeaway

`file` identifies the format of a file from its contents rather than relying solely on its filename.

This is useful when analysing unknown or deliberately misleading files.

---

## Level 5 → 6

### Objective

Find a readable, non-executable file with an exact size of 1033 bytes.

### Method

```bash
find . -type f -size 1033c ! -executable
```

Read the resulting file:

```bash
cat ./inhere/maybehere07/.file2
```

### Takeaway

`find` can combine multiple predicates to perform precise filesystem enumeration.

Important filters include:

```text
-type
-size
-user
-group
-perm
-executable
-name
```

---

## Level 6 → 7

### Objective

Locate a file anywhere on the filesystem matching specific ownership and size constraints.

### Method

```bash
find / \
    -user bandit7 \
    -group bandit6 \
    -size 33c \
    -type f \
    2>/dev/null
```

Read the matching file:

```bash
cat /var/lib/dpkg/info/bandit7.password
```

### Takeaway

Filesystem enumeration becomes significantly more powerful when combined with ownership, group and size constraints.

Redirecting permission errors to `/dev/null` also keeps large recursive searches manageable.

---

## Level 7 → 8

### Objective

Find the value associated with the keyword `millionth` in `data.txt`.

### Method

```bash
grep millionth data.txt
```

### Takeaway

`grep` is one of the most useful tools for quickly locating relevant data in large text files.

---

## Level 8 → 9

### Objective

Find the only line in `data.txt` that appears once.

### Method

```bash
sort data.txt | uniq -u
```

### Takeaway

`uniq` only detects adjacent duplicate lines, which is why sorting is performed first.

The pattern:

```text
sort | uniq
```

is a fundamental Unix technique for analysing repeated values.

---

## Level 9 → 10

### Objective

Identify the only human-readable string containing the password inside a binary file.

### Method

Extract printable strings:

```bash
strings data.txt
```

Filter the relevant output:

```bash
strings data.txt | grep -E '={2,}'
```

### Takeaway

`strings` is useful when analysing binary or mixed-content files for embedded text, credentials or other printable artefacts.

---

## Level 10 → 11

### Objective

Decode Base64 data contained in `data.txt`.

### Method

```bash
base64 -d data.txt
```

### Takeaway

Base64 is an encoding mechanism rather than encryption. Encoded data can be decoded without a secret key.

---

## Level 11 → 12

### Objective

Decode a ROT13-transformed password.

### Method

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

### Takeaway

ROT13 is a Caesar cipher with a fixed rotation and provides no meaningful confidentiality.

---

## Level 12 → 13

### Objective

Reconstruct and repeatedly decompress a file represented as a hexdump.

### Method

Create a temporary working directory:

```bash
mktemp -d
cd /tmp/tmp.XXXXXX
```

Reconstruct the binary file:

```bash
xxd -r data.txt data.bin
```

Identify its format:

```bash
file data.bin
```

Apply the appropriate decompression or extraction tool:

```text
gzip
bzip2
tar
```

Repeat:

```text
file
→ identify format
→ decompress or extract
→ file
→ repeat
```

until the resulting file is readable text.

### Takeaway

When analysing unknown files, identify the format from the data itself rather than relying on the filename or extension.

---

## Level 13 → 14

### Objective

Use an SSH private key provided by the current account to authenticate as `bandit14`.

### Method

Copy the private key to the local machine:

```bash
scp -P 2220 \
    bandit13@bandit.labs.overthewire.org:~/sshkey.private \
    .
```

Restrict its permissions:

```bash
chmod 600 sshkey.private
```

Connect using the key:

```bash
ssh -i sshkey.private \
    -p 2220 \
    bandit14@bandit.labs.overthewire.org
```

### Takeaway

Private SSH keys are sensitive credentials and should have restrictive filesystem permissions.

SSH will normally reject private keys that are accessible to other users.

---

## Level 14 → 15

### Objective

Send the current password to a local TCP service listening on port 30000.

### Method

```bash
cat /etc/bandit_pass/bandit14 | nc localhost 30000
```

### Takeaway

Netcat provides a simple way to interact with TCP services and is useful for understanding basic client-server communication.

---

## Level 15 → 16

### Objective

Communicate with a TLS-protected local service on port 30001.

### Method

Interactive:

```bash
openssl s_client -connect localhost:30001
```

Non-interactive:

```bash
printf "%s\n" "$(cat /etc/bandit_pass/bandit15)" |
    openssl s_client -connect localhost:30001 -quiet
```

### Takeaway

`openssl s_client` is useful for testing TLS services and interacting directly with encrypted application protocols.

The challenge demonstrates the distinction between plain TCP communication and TLS-protected communication.

---

## Level 16 → 17

### Objective

Enumerate local services listening on ports 31000–32000 and identify the TLS-enabled service that accepts the current password.

### Method

Scan the port range:

```bash
nmap -sV localhost -p 31000-32000
```

Inspect the relevant TLS service:

```bash
openssl s_client -connect localhost:<PORT>
```

Send the current password:

```bash
printf "%s\n" "$(cat /etc/bandit_pass/bandit16)" |
    openssl s_client -connect localhost:<PORT> -quiet
```

The correct service returns an RSA private key for the next level.

### Takeaway

This level combines service enumeration, protocol identification and TLS interaction.

The general workflow is:

```text
Scan
→ identify services
→ identify protocol
→ test relevant service
```

---

## Level 17 → 18

### Objective

Identify the single line changed between `passwords.old` and `passwords.new`.

### Method

```bash
diff passwords.old passwords.new
```

The changed line in `passwords.new` contains the password for the next level.

### Takeaway

`diff` is useful for identifying configuration changes, modified data and unexpected file modifications.

---

## Level 18 → 19

### Objective

Read the next password without opening an interactive SSH session.

The `bandit18` environment terminates the interactive shell during login.

### Method

Execute the required command directly through SSH:

```bash
ssh -p 2220 \
    bandit18@bandit.labs.overthewire.org \
    cat readme
```

### Takeaway

SSH does not require an interactive shell. Remote commands can be executed directly.

This is useful for automation and for environments where interactive shell initialization is restricted or modified.

---

## Level 19 → 20

### Objective

Use a SUID binary to execute a command with the privileges of its owner.

### Method

Inspect the provided executable:

```bash
./bandit20-do
```

Confirm its execution context:

```bash
./bandit20-do whoami
```

Read the protected password file:

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

### Takeaway

SUID binaries execute with the effective privileges of their owner.

Any SUID executable that exposes unsafe functionality can become a privilege-escalation vector.

---

## Level 20 → 21

### Objective

Exploit the `suconnect` binary by providing the current password through a local TCP service.

### Method

Start a local listener that returns the current password:

```bash
echo "<current-password>" | nc -l -p 2000 &
```

Run the SUID-enabled client:

```bash
./suconnect 2000
```

The service validates the supplied password and returns the next credential.

### Takeaway

This level demonstrates how local network services can interact with privileged processes.

The security boundary is not limited to filesystem access: network input can also become privileged input when a vulnerable process trusts it.

---

## Level 21 → 22

### Objective

Analyse a scheduled cron job and identify the temporary file containing the next password.

### Method

Inspect the cron configuration:

```bash
cat /etc/cron.d/cronjob_bandit22
```

Inspect the executed script:

```bash
cat /usr/bin/cronjob_bandit22.sh
```

The script copies the protected password into a temporary file with readable permissions.

Read the generated file from `/tmp`.

### Takeaway

Privileged cron jobs must carefully control both their output locations and the permissions of generated files.

Temporary directories are not inherently safe storage for sensitive data.

---

## Level 22 → 23

### Objective

Determine the predictable filename generated by the cron job and retrieve the resulting password.

### Method

Inspect the script:

```bash
cat /usr/bin/cronjob_bandit23.sh
```

The filename is derived deterministically from:

```text
I am user <username>
```

using MD5:

```bash
echo "I am user bandit23" | md5sum
```

Use the resulting hash to locate the generated file in `/tmp`.

### Takeaway

A deterministic naming scheme is not a security mechanism.

If the input, transformation and output format are known, the resulting identifier can be reproduced.

---

## Level 23 → 24

### Objective

Exploit a cron job that executes user-controlled files from a writable directory.

### Method

Inspect the cron configuration:

```bash
cat /etc/cron.d/cronjob_bandit24
```

Then inspect the script:

```bash
cat /usr/bin/cronjob_bandit24.sh
```

The script executes files placed in the relevant spool directory with elevated privileges.

A controlled script can therefore read the protected password and write it to a location accessible by the current user.

### Example

```bash
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/bandit24_password
```

Make the script executable and place it in the directory monitored by the cron job.

### Takeaway

Privileged scheduled tasks must never execute files from locations writable by unprivileged users.

This is a common Linux privilege-escalation pattern.

---

## Level 24 → 25

### Objective

Recover a four-digit PIN accepted by a local service together with the current password.

### Method

The possible PINs range from:

```text
0000
```

to:

```text
9999
```

Generate candidate inputs programmatically and stream them to the service.

Example:

```bash
for i in {0000..9999}; do
    printf "%s %04d\n" "<current-password>" "$i"
done | nc localhost 30002
```

Search the response for the successful attempt.

### Takeaway

A four-digit secret provides only 10,000 possible combinations.

Without sufficient rate limiting or account lockout, such a small search space can be exhaustively tested.

---

## Level 25 → 26

### Objective

Escape the restricted environment used by `bandit26`.

### Method

Inspect the account configuration:

```bash
cat /etc/passwd | grep bandit26
```

The configured shell points to:

```text
/usr/bin/showtext
```

Inspect the executable:

```bash
cat /usr/bin/showtext
```

It invokes the `more` pager.

When `more` is available, it can be used to launch an editor. The editor then provides another execution context from which a shell can be invoked.

The challenge can therefore be approached through:

```text
restricted shell
→ showtext
→ more
→ vim
→ shell
```

Once a normal shell is obtained, the protected file can be accessed.

### Takeaway

Restricted shells are only effective when every program available inside the environment is appropriately constrained.

A seemingly harmless pager or editor can become an execution primitive if it allows arbitrary commands.

---

## Level 26 → 27

### Objective

Use the privileged executable available after escaping the restricted shell to access the next password.

### Method

Enumerate the home directory:

```bash
ls -la
```

Identify the privileged executable and inspect its behaviour.

The intended mechanism is equivalent to the SUID technique used earlier: the executable performs an operation using elevated privileges.

### Takeaway

Privilege escalation techniques often recur in different forms. Recognising the underlying security property is more useful than memorising individual binaries.

---

## Level 27 → 28

### Objective

Clone a Git repository accessible through SSH and inspect its contents.

### Method

Clone the repository:

```bash
git clone \
    ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
```

Inspect the repository:

```bash
cd repo
ls -la
cat README.md
```

### Takeaway

Source-code repositories are part of an application's attack surface.

Always inspect repositories for:

- credentials
- configuration
- development artefacts
- historical information
- accidentally committed secrets

---

## Level 28 → 29

### Objective

Recover a password that was removed from the current repository state but remains in Git history.

### Method

Clone the repository and inspect its history:

```bash
git clone \
    ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo

cd repo
git log
```

Inspect the relevant commit:

```bash
git show <commit>
```

The removed value is visible in the commit diff.

### Takeaway

Deleting a secret from the current working tree does not remove it from Git history.

Credential exposure therefore requires history management and, when necessary, rewriting or invalidating compromised credentials.

---

## Level 29 → 30

### Objective

Find sensitive information stored in a non-default Git branch.

### Method

Inspect all branches:

```bash
git branch -a
```

Switch to the relevant branch:

```bash
git checkout dev
```

Inspect the branch contents:

```bash
cat README.md
```

### Takeaway

Security reviews of repositories should include branches, not just the default branch.

Development and testing branches are common sources of accidentally exposed credentials and unfinished security controls.

---

## Level 30 → 31

### Objective

Find information stored in Git tag metadata.

### Method

List available tags:

```bash
git tag
```

Inspect the relevant tag:

```bash
git show secret
```

### Takeaway

Repository metadata can contain sensitive information even when the working tree appears clean.

Git security reviews should consider commits, branches, tags and other repository objects.

---

## Level 31 → 32

### Objective

Submit a specific file to a Git repository while bypassing the repository's `.gitignore` rule.

### Method

Clone the repository:

```bash
git clone \
    ssh://bandit31-git@bandit.labs.overthewire.org:2220/home/bandit31-git/repo
cd repo
```

Create the required file:

```bash
echo "May I come in?" > key.txt
```

Inspect `.gitignore`:

```bash
cat .gitignore
```

Force-add the ignored file:

```bash
git add -f key.txt
```

Commit and push:

```bash
git commit -m "Add required key"
git push -u origin master
```

The remote validation hook processes the submission and returns the next credential.

### Takeaway

`.gitignore` controls normal Git tracking but does not prevent an explicitly forced addition.

The level also demonstrates server-side repository validation through Git hooks.

---

## Level 32 → 33

### Objective

Escape the uppercase command shell and obtain a normal shell.

### Method

The environment transforms ordinary command input before execution.

The shell still performs variable expansion, which provides an alternative execution path.

The `$0` shell parameter can be used to reference the current shell executable and escape the command filter.

Once the shell is escaped, normal commands can be executed, including:

```bash
cat /etc/bandit_pass/bandit33
```

### Takeaway

Command filters that operate on raw input can often be bypassed through shell parsing and expansion semantics.

Security restrictions should constrain capabilities at the execution level rather than relying solely on textual command filtering.

---

# Security Concepts Demonstrated

Bandit provides practical exposure to:

- Linux enumeration
- Shell parsing
- File permissions
- File ownership
- Information disclosure
- SSH authentication
- SSH key management
- TCP communication
- TLS
- Network reconnaissance
- SUID privilege escalation
- Cron-based privilege escalation
- Predictable identifiers
- Brute-force attacks
- Restricted shell escapes
- Git history analysis
- Git branch and tag enumeration
- Git hooks
- Basic security automation

---

# Key Lessons

Several recurring principles emerge from the challenge:

- Enumerate before attempting exploitation.
- File names, permissions and ownership are security-relevant information.
- Client-controlled input can cross security boundaries through unexpected interfaces.
- Privileged processes require careful control of their inputs and execution paths.
- Scheduled tasks can become privilege-escalation vectors when they execute attacker-controlled files.
- Small secret spaces are vulnerable to exhaustive search.
- Source-control history can retain information that is no longer visible.
- Restricted shells are only as strong as the programs available inside them.
- Understanding the underlying mechanism is more valuable than memorising individual commands.

---

# Conclusion

Completing Bandit 0–33 provides a practical foundation in Linux security and command-line based investigation.

The challenge progresses from basic filesystem and shell operations to networking, authentication, privilege escalation, scheduled execution, restricted environments and Git forensics.

The most important outcome is the development of a repeatable methodology:

```text
Enumerate
    ↓
Inspect
    ↓
Understand execution context
    ↓
Identify trust boundaries
    ↓
Determine what can be influenced
    ↓
Test the boundary
    ↓
Verify the result
```

This methodology is applicable far beyond CTF environments and forms part of the foundation for practical Linux and cybersecurity work.

---

# Environment

The walkthrough was originally performed using:

```text
Operating System: Ubuntu 22.04 LTS
Shell: Bash
Architecture: x86_64
```

Most commands rely on standard Unix/Linux utilities and can be adapted to other Linux distributions.

---

# Disclaimer

This repository documents activity performed against the intentionally vulnerable OverTheWire Bandit environment.

The techniques described are intended for educational purposes, CTFs, security laboratories and systems for which explicit authorization has been obtained.

Do not apply these techniques to systems without authorization.

---

# Credits

OverTheWire — Bandit

https://overthewire.org/wargames/bandit/

All original challenge content and infrastructure belong to OverTheWire and its respective authors.

This repository contains personal notes and walkthrough material created for educational purposes.

---

# Status

Bandit 0 → 33: Completed
