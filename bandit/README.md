# OverTheWire: Bandit Walkthrough

A comprehensive walkthrough of the Bandit wargame, demonstrating hands-on proficiency in Linux command-line operations, file system navigation, network communication, privilege escalation, and core cybersecurity concepts. This project is part of my hands-on preparation for cybersecurity roles, particularly in SOC analysis and penetration testing.

## Skills Gained
- Linux command-line usage and shell navigation
- File permissions, ownership, and SetUID exploitation
- Data encoding and decoding (Base64, ROT13, Hexdump)
- Network communication (Netcat, SSL/TLS, Nmap)
- Compression and archival handling (Gzip, Bzip2, Tar)
- Basic enumeration and service reconnaissance
- Privilege escalation concepts
- Cron job analysis and exploitation
- Git repository forensics (commit history, branches, tags)
- Shell escape and restricted environment bypass techniques

## Bandit 0

### Objective
Log into the game server via SSH.

### Concept
SSH (Secure Shell) is a cryptographic network protocol used to securely access remote machines. The `-p` flag specifies a non-standard port.

### Command(s)
```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

### Explanation
The default SSH port is 22. Here, the server listens on port 2220, so the `-p 2220` flag is required to establish the connection. Credentials are provided as part of the username in the format `user@host`.

### Security Relevance
SSH is the industry standard for secure remote administration. Understanding SSH authentication — including password-based and key-based methods — is fundamental to both offensive and defensive security work.

<img width="816" height="316" alt="image" src="https://github.com/user-attachments/assets/0b2f2be1-8674-45c4-a259-71c1c719ce88" />

---

## Bandit 0 → Bandit 1

### Objective
Locate the password stored in a file named `readme` in the home directory.

### Concept
Basic file navigation using `ls` and reading file contents using `cat`.

### Command(s)
```bash
cat readme
```

### Explanation
`cat` (concatenate) outputs the contents of a file to standard output. The `readme` file resides in the default login directory, so no path traversal is needed.

<img width="949" height="230" alt="image" src="https://github.com/user-attachments/assets/9a917fc3-61c5-46c5-ae46-4ee0cfbe7b2b" />

---

## Bandit 1 → Bandit 2

### Objective
Read the contents of a file named `-`.

### Concept
Handling special characters in filenames. A bare `-` is interpreted by most shells as stdin/stdout rather than a filename.

### Command(s)
```bash
cat ./-
# or
cat < -
```

### Explanation
The shell treats `-` as a special token representing standard input. To force it to be interpreted as a literal filename, a relative path prefix (`./`) is prepended, making the intent unambiguous to the shell parser.

### Security Relevance
Awareness of how shells parse special characters is critical in penetration testing and secure scripting. Improper handling of filenames with special characters can lead to command injection vulnerabilities.

<img width="407" height="151" alt="image" src="https://github.com/user-attachments/assets/c8daf6e4-6a5e-4762-9789-1300906ff132" />

---

## Bandit 2 → Bandit 3

### Objective
Read the contents of a file named `spaces in this filename`.

### Concept
Shell escaping and quoting to handle filenames that contain whitespace.

### Command(s)
```bash
cat ./--spaces in this filename--'
```

### Explanation
The shell uses spaces as argument delimiters by default. Wrapping the filename in double quotes or escaping each space with a backslash (`\ `) instructs the shell to treat the entire string as a single argument.

### Security Relevance
Improperly sanitized filenames or input strings with spaces can bypass naive security checks and scripts. Understanding shell quoting is essential when writing secure bash scripts or handling untrusted input.

<img width="651" height="128" alt="image" src="https://github.com/user-attachments/assets/c8e0cba5-e155-48b8-8df5-685c9c92d042" />

---

## Bandit 3 → Bandit 4

### Objective
Locate a hidden file inside the `inhere` directory.

### Concept
On Unix systems, files prefixed with a dot (`.`) are hidden from standard directory listings. The `ls -a` flag reveals them.

### Command(s)
```bash
ls -a inhere/
cat inhere/.[filename]
```

### Explanation
`ls` without flags omits dotfiles by convention. The `-a` (all) flag disables this filter and reveals hidden files. This behavior is a common Unix convention, not a security feature — hidden files are visible to any user with read access to the directory.

### Security Relevance
Attackers frequently hide malicious files or backdoors using dotfile naming conventions. During forensic analysis or incident response, checking for hidden files is a standard step.

<img width="814" height="272" alt="image" src="https://github.com/user-attachments/assets/1f56447d-761b-4b1d-8a83-6fe519b36ca6" />

---

## Bandit 4 → Bandit 5

### Objective
Identify the only human-readable file among multiple files in the `inhere` directory.

### Concept
Using the `file` command to determine the type of data stored in each file.

### Command(s)
```bash
file inhere/*
```

### Explanation
The `file` utility inspects a file's magic bytes (the initial bytes of a file that identify its format) and reports the file type. By running it against a glob (`*`), all files are evaluated simultaneously. The one reported as "ASCII text" contains the human-readable password.

### Security Relevance
Identifying file types by content rather than extension is a core skill in digital forensics and malware analysis. Attackers often disguise file types by renaming them, so relying on extensions alone is insufficient.

<img width="786" height="385" alt="image" src="https://github.com/user-attachments/assets/7a489f4d-9c33-4d5a-a8c2-197c38c6d895" />

---

## Bandit 5 → Bandit 6

### Objective
Locate a file with specific properties: human-readable, 1033 bytes in size, and not executable.

### Concept
Using `find` with multiple filters to locate files based on metadata rather than name.

### Command(s)
```bash
find inhere/ -type f -size 1033c ! -executable
```

### Explanation
The `find` command supports chaining multiple conditions: `-type f` restricts results to regular files, `-size 1033c` matches files of exactly 1033 bytes (`c` = bytes), and `! -executable` excludes files with the executable permission bit set. The file satisfying all three conditions contains the password.

### Security Relevance
`find` with attribute-based filters is widely used in security auditing to identify files with unusual permissions, suspicious sizes, or unexpected executability — all common indicators of tampering or misconfiguration.

<img width="679" height="213" alt="image" src="https://github.com/user-attachments/assets/2c0182cf-6baa-4192-8151-7dbc35ac468b" />

---

## Bandit 6 → Bandit 7

### Objective
Locate a file anywhere on the server owned by user `bandit7`, group `bandit6`, with a size of 33 bytes.

### Concept
System-wide file search using ownership and size filters, combined with error suppression.

### Command(s)
```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

### Explanation
Searching from the filesystem root (`/`) causes many "Permission Denied" errors for directories the current user cannot access. Redirecting stderr to `/dev/null` (`2>/dev/null`) suppresses this noise, leaving only the matching result visible. The `-user` and `-group` flags filter by file ownership metadata.

### Security Relevance
Ownership-based file searches are a standard technique during privilege escalation and post-exploitation enumeration. Identifying files owned by privileged users but readable by others is a common method for discovering sensitive data or misconfigurations.

<img width="927" height="127" alt="image" src="https://github.com/user-attachments/assets/d1aa6388-f07e-45ec-bdf5-4817b899e2ba" />

---

## Bandit 7 → Bandit 8

### Objective
Extract the password from a large file, located on the line next to the word "millionth".

### Concept
Using `grep` to search for a specific string within a file.

### Command(s)
```bash
grep "millionth" data.txt
```

### Explanation
`grep` scans each line of a file for a pattern match and outputs the matching lines. Rather than manually reading thousands of lines, this command instantly returns the single line containing "millionth" along with the password on the same line.

<img width="499" height="75" alt="image" src="https://github.com/user-attachments/assets/69d0a74a-3883-4469-9d43-4b94a18666eb" />

---

## Bandit 8 → Bandit 9

### Objective
Find the only line of text in `data.txt` that appears exactly once.

### Concept
Combining `sort` and `uniq` to identify unique lines in a file.

### Command(s)
```bash
sort data.txt | uniq -u
```

### Explanation
`uniq -u` filters out all lines that appear more than once, printing only lines that are truly unique. However, `uniq` only compares adjacent lines, so the file must first be sorted with `sort` to group identical lines together before `uniq` can work correctly.

<img width="516" height="100" alt="image" src="https://github.com/user-attachments/assets/4129ca10-df96-4f0a-b4d9-d902a6015cd1" />

---

## Bandit 9 → Bandit 10

### Objective
Extract human-readable strings from a binary file.

### Concept
Using the `strings` command to pull printable character sequences from non-text files.

### Command(s)
```bash
strings data.txt | grep "=="
```

### Explanation
Binary files contain a mix of non-printable bytes and embedded text. The `strings` command extracts sequences of printable characters of a minimum length (default: 4). Piping the output to `grep "=="` further filters for lines matching the password format described in the challenge.

### Security Relevance
`strings` is a foundational tool in malware analysis and binary reverse engineering. It is used to extract embedded URLs, error messages, API keys, and other artifacts from compiled executables or obfuscated files.

<img width="477" height="136" alt="image" src="https://github.com/user-attachments/assets/ff4daa11-7ea6-4018-90ef-7e9cb8bc6eec" />

---

## Bandit 10 → Bandit 11

### Objective
Decode a Base64-encoded file to retrieve the password.

### Concept
Base64 is an encoding scheme that represents binary data using only printable ASCII characters.

### Command(s)
```bash
base64 -d data.txt
```

### Explanation
The `-d` flag instructs the `base64` utility to decode rather than encode. Base64 is commonly used to safely transmit binary data over text-based protocols. It is an encoding scheme, not an encryption method — any data encoded in Base64 can be trivially decoded without a key.

### Security Relevance
Base64 encoding is frequently used by malware and attackers to obfuscate payloads, commands, and exfiltrated data. Recognizing and decoding Base64 is an essential skill in log analysis, incident response, and CTF competitions.

<img width="698" height="111" alt="image" src="https://github.com/user-attachments/assets/918dd0c5-4351-454e-8961-7a876ae40d0a" />

---

## Bandit 11 → Bandit 12

### Objective
Decode a file encrypted with ROT13.

### Concept
ROT13 is a Caesar cipher that substitutes each letter with the letter 13 positions after it in the alphabet. It is its own inverse — applying ROT13 twice returns the original text.

### Command(s)
```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

### Explanation
The `tr` (translate) command substitutes characters according to a mapping. The mapping `'A-Za-z'` → `'N-ZA-Mn-za-m'` shifts each uppercase and lowercase letter by 13 positions, implementing the ROT13 substitution. No key is involved — the transformation is purely positional.

### Security Relevance
Simple substitution ciphers like ROT13 and Caesar cipher provide no real security and are trivially reversible. Understanding their weaknesses illustrates why modern cryptography relies on computationally infeasible problems rather than simple transformations.

<img width="687" height="107" alt="image" src="https://github.com/user-attachments/assets/31db1351-663e-438d-bd72-e7354c7d1d9b" />

---

## Bandit 12 → Bandit 13

### Objective
Recover the password from a file that has been hexdumped and then compressed multiple times using different algorithms.

### Concept
This level involves iterative file type identification and decompression across multiple formats: Gzip, Bzip2, and Tar.

### Command(s)
```bash
# Step 1: Reverse the hexdump
xxd -r data.txt > data.bin

# Step 2: Identify file type and decompress iteratively
file data.bin
mv data.bin data.gz && gunzip data.gz
file data
mv data data.bz2 && bunzip2 data.bz2
file data
# ... repeat until plain text is reached
```

### Explanation
The file was first converted to a hexdump for storage, so `xxd -r` reverses it back into binary. From there, the `file` command is used at each step to identify the current compression format. The file is renamed with the appropriate extension and then decompressed using the corresponding tool (`gunzip` for Gzip, `bunzip2` for Bzip2, `tar xf` for Tar archives). This process is repeated until a plain ASCII text file containing the password is obtained.

<img width="581" height="195" alt="Image" src="https://github.com/user-attachments/assets/a568ba15-a61d-4014-a94b-1c35a93110e5" />
<br>
<img width="630" height="256" alt="Image" src="https://github.com/user-attachments/assets/e1bb3967-3f91-4fda-bc9d-f2499d2b9650" />
<br>
<img width="573" height="217" alt="Image" src="https://github.com/user-attachments/assets/981c2aa4-65bd-412e-a588-dc5324bc634a" />
<br>
<img width="582" height="158" alt="Image" src="https://github.com/user-attachments/assets/0200cc60-b6a8-4eb0-b060-cfb5d8e2b2cd" />
<br>
<img width="1367" height="381" alt="Image" src="https://github.com/user-attachments/assets/50f9a09d-118d-4039-97d6-31ceb56bdf71" />
<br>
<img width="533" height="180" alt="Image" src="https://github.com/user-attachments/assets/49238f4b-5d7e-480a-b967-c03061871656" />

---

## Bandit 13 → Bandit 14

### Objective
Authenticate to the next level using a private SSH key instead of a password.

### Concept
Public Key Infrastructure (PKI) and SSH identity files. SSH supports key-based authentication as an alternative (and more secure) method to passwords.

### Command(s)
```bash
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
cat /etc/bandit_pass/bandit14
```

### Explanation
The `-i` (identity file) flag instructs SSH to use the specified private key for authentication instead of a password. The server holds the corresponding public key for `bandit14`, and the cryptographic handshake proves possession of the private key without transmitting it. Once authenticated as `bandit14`, the password can be read from the protected file.

### Security Relevance
SSH key-based authentication is significantly more secure than passwords. Private keys cannot be guessed through brute force in the way passwords can. However, improperly stored or world-readable private key files represent a serious vulnerability — a common misconfiguration found during real-world penetration tests.

<img width="698" height="262" alt="Image" src="https://github.com/user-attachments/assets/366ef92b-1081-47a2-8c48-d43162cedf89" />
<br>
<img width="504" height="80" alt="image" src="https://github.com/user-attachments/assets/8c368bca-0c15-43a3-96bf-6916a22017ef" />

---

## Bandit 14 → Bandit 15

### Objective
Submit the current level's password to a local service on port 30000 to receive the next password.

### Concept
Using Netcat (`nc`) to communicate with a TCP network service.

### Command(s)
```bash
echo [password] | nc localhost 30000
```

### Explanation
Netcat is a versatile networking utility that can read from and write to network connections using TCP or UDP. By connecting to `localhost` on port 30000 and sending the current password, the daemon running on that port validates it and responds with the next level's password.

### Security Relevance
Netcat is a staple tool in penetration testing for banner grabbing, port scanning, establishing reverse shells, and transferring files. Understanding how to interact with raw network services is foundational to network-based exploitation.

<img width="797" height="109" alt="image" src="https://github.com/user-attachments/assets/82d5f287-f287-4560-b0fc-8453b8adc2a3" />

---

## Bandit 15 → Bandit 16

### Objective
Submit the current password to port 30001 over an SSL/TLS-encrypted connection.

### Concept
Using `openssl s_client` to interact with services requiring encrypted communication.

### Command(s)
```bash
echo [password] | openssl s_client -connect localhost:30001 -ign_eof
```

### Explanation
Unlike plain Netcat, `openssl s_client` performs a TLS handshake before transmitting data. The `-ign_eof` flag keeps the connection open after receiving EOF from stdin, allowing the server's response to be read. This is necessary when the service requires an encrypted channel and will reject plaintext connections.

### Security Relevance
SSL/TLS is the foundational encryption protocol for secure internet communication (HTTPS, SMTPS, etc.). Understanding how to initiate and inspect TLS connections is essential for security testing, including certificate validation checks and identifying weak cipher suites.

<img width="722" height="275" alt="Image" src="https://github.com/user-attachments/assets/88cd77c4-63e9-4612-a649-c4c1e495222b" />
<br>
<img width="574" height="174" alt="Image" src="https://github.com/user-attachments/assets/1ac5875b-8ab7-4876-97e9-baec1352f4f4" />

---

## Bandit 16 → Bandit 17

### Objective
Identify which port in the range 31000–32000 is running an SSL service that will return credentials, then connect to it.

### Concept
Network service reconnaissance using Nmap to identify open ports and their service types.

### Command(s)
```bash
nmap -sV -p 31000-32000 localhost
openssl s_client -connect localhost:[port]
```

### Explanation
Nmap's `-sV` flag performs service/version detection, identifying which ports are speaking SSL/TLS versus plain protocols. Once the correct port is identified, `openssl s_client` is used to connect, submit the current password, and receive the private SSH key for the next level in return.

### Security Relevance
Port and service scanning is a core step in the reconnaissance phase of any penetration test. Identifying exposed services, their versions, and protocols allows an attacker or security assessor to map the attack surface and prioritize targets.

<img width="617" height="221" alt="Screenshot 2026-04-20 141534" src="https://github.com/user-attachments/assets/6bca23b7-9d25-45ac-a30b-f18259a85093" />
<br>
<img width="960" height="732" alt="Screenshot 2026-04-20 143143" src="https://github.com/user-attachments/assets/87dbbbe6-b21d-46c9-a1ea-05204cc308ea" />

---

## Bandit 17 → Bandit 18

### Objective
Identify the single line that differs between `passwords.old` and `passwords.new`.

### Concept
Using the `diff` utility to compare two files and highlight changes.

### Command(s)
```bash
diff passwords.old passwords.new
```

### Explanation
`diff` compares two files line-by-line and outputs the differences. Lines prefixed with `<` belong to the first file and those with `>` to the second. The line marked as new in `passwords.new` is the updated password for the next level.

### Security Relevance
File diffing is a critical technique in incident response and integrity monitoring. Comparing system files (such as `/etc/passwd` or configuration files) against a known-good baseline can reveal unauthorized modifications.

<img width="709" height="193" alt="Screenshot 2026-04-20 143900" src="https://github.com/user-attachments/assets/34c4b55d-5a88-4258-bc36-1c84c35786de" />

---

## Bandit 18 → Bandit 19

### Objective
Read the `readme` file despite a modified `.bashrc` that immediately logs the user out upon login.

### Concept
Executing non-interactive commands directly over SSH to bypass a hostile shell environment.

### Command(s)
```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
```

### Explanation
SSH allows commands to be passed directly as arguments, bypassing the interactive shell entirely. Because the command runs before the full shell environment (including `.bashrc`) is initialized, the logout trap in `.bashrc` is never triggered.

### Security Relevance
Restricted or modified shell environments are a common defense-in-depth measure. Bypassing them via non-interactive SSH is a well-known technique in penetration testing. This also illustrates why shell environments should not be relied upon as a security boundary.

<img width="615" height="302" alt="Screenshot 2026-04-20 144636" src="https://github.com/user-attachments/assets/06114a00-bc5b-4996-84eb-0e8fc4464eef" />
<br>
<img width="687" height="298" alt="image" src="https://github.com/user-attachments/assets/96205ce6-b7e2-4bb4-8012-6696cf857f19" />

---

## Bandit 19 → Bandit 20

### Objective
Use a SetUID binary to read a file accessible only to a higher-privileged user.

### Concept
Understanding the Linux SetUID (SUID) permission bit, which causes an executable to run with the file owner's privileges rather than the caller's.

### Command(s)
```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

### Explanation
The `bandit20-do` binary has the SUID bit set and is owned by `bandit20`. When executed, it runs with `bandit20`'s effective user ID, granting temporary access to files that `bandit20` owns. This is used to read the password file at `/etc/bandit_pass/bandit20`, which is normally inaccessible to `bandit19`.

### Security Relevance
Misconfigured SUID binaries are one of the most common local privilege escalation vectors in Linux systems. During a penetration test, identifying SUID files with `find / -perm -4000` is a standard step. Any SUID binary that allows arbitrary command execution can be exploited to gain root access.

<img width="299" height="84" alt="Screenshot 2026-04-20 144934" src="https://github.com/user-attachments/assets/583f3ec2-9682-46db-84f9-a2a9322810f5" />
<br>
<img width="805" height="161" alt="Screenshot 2026-04-20 145058" src="https://github.com/user-attachments/assets/b24b3b97-5c58-4e5a-9e52-f26c26e68e93" />

---

## Bandit 20 → Bandit 21

### Objective
Use a SetUID binary that connects to a local port, reads a password, and returns the next one — requiring a listener to already be running.

### Concept
Inter-process communication using Netcat as a background listener combined with a SetUID binary.

### Command(s)
```bash
echo [current_password] | nc -l -p 1234 &
./suconnect 1234
```

### Explanation
The `suconnect` binary connects to a specified local port, expects the current password, and if correct, sends back the next password. A Netcat listener is started in the background (`&`) on a chosen port to serve the password. The SetUID binary connects, validates it, and responds with the next level's credentials.

### Security Relevance
This level demonstrates the concept of inter-process communication and background tasks — foundational knowledge for understanding how backdoors, reverse shells, and C2 (command-and-control) channels operate.

<img width="809" height="167" alt="Screenshot 2026-04-20 145359" src="https://github.com/user-attachments/assets/46680315-335f-41f0-bd61-2fb797a3ca9e" />

---

## Bandit 21 → Bandit 22

### Objective
Identify a cron job and trace it to a script that writes the next password to a readable file.

### Concept
Scheduled task analysis using cron — the Unix job scheduler.

### Command(s)
```bash
ls /etc/cron.d/
cat /etc/cron.d/cronjob_bandit22
cat /usr/bin/cronjob_bandit22.sh
cat /tmp/[output_file]
```

### Explanation
Cron jobs are scheduled tasks defined in `/etc/cron.d/` or user crontabs. Inspecting the cron configuration reveals a script running periodically as `bandit22`. Reading that script shows it writes the password to a file in `/tmp/`. Accessing that file yields the password.

### Security Relevance
Cron jobs running as privileged users are a common attack surface. A writable cron script or a cron job that references a world-writable directory can be exploited to achieve privilege escalation — a technique frequently encountered in real-world CTFs and penetration tests.

<img width="813" height="248" alt="Screenshot 2026-04-20 145727" src="https://github.com/user-attachments/assets/40e41a7c-7a26-4775-93dc-d4ee86fa40d0" />

---

## Bandit 22 → Bandit 23

### Objective
Reverse-engineer a cron script that generates a password-storing filename via MD5 hashing.

### Concept
Script analysis and manual replication of a hash-based file path generation scheme.

### Command(s)
```bash
cat /usr/bin/cronjob_bandit23.sh
echo I am user bandit23 | md5sum | cut -d ' ' -f 1
cat /tmp/[md5_hash]
```

### Explanation
The cron script generates a filename by MD5-hashing the string `"I am user bandit23"` and writing the password to `/tmp/[hash]`. By replicating this logic manually — running the same `echo` through `md5sum` — the output filename is determined, and the password can be read from that file.

### Security Relevance
Hash functions are deterministic, meaning the same input always produces the same output. This level illustrates the danger of using predictable inputs to generate "secret" values. An attacker who understands the generation logic can replicate it — a flaw underlying many weak token and session ID generation schemes.

<img width="822" height="303" alt="Screenshot 2026-04-20 150114" src="https://github.com/user-attachments/assets/803ac5d8-57eb-4822-86cb-375fc3f2ed79" />
<br>
<img width="774" height="125" alt="Screenshot 2026-04-20 150207" src="https://github.com/user-attachments/assets/19adef42-2ac5-422a-b129-a2308ef1394e" />

---

## Bandit 23 → Bandit 24

### Objective
Write and deploy a custom script into a cron-monitored directory that executes as `bandit24` and exfiltrates the password.

### Concept
Exploiting a cron job that automatically executes any script placed in a specific writable directory.

### Command(s)
```bash
# Script deployed to /var/spool/bandit24/
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/mydir/password
```

### Explanation
A cron job runs periodically as `bandit24` and executes all scripts found in `/var/spool/bandit24/`. By writing a shell script to that directory that reads `bandit24`'s password and writes it to a world-readable location in `/tmp/`, the password is captured when the cron job next runs. File permissions must be set correctly to allow execution.

### Security Relevance
This is a textbook cron-based privilege escalation scenario. In real environments, cron jobs that execute scripts from world-writable directories are serious vulnerabilities. An unprivileged user can plant a malicious script that runs as root or another privileged user.

<img width="899" height="600" alt="Screenshot 2026-04-20 151401" src="https://github.com/user-attachments/assets/aec9a14d-8315-4f54-8670-889f71091f4f" />
<br>
<img width="960" height="361" alt="Screenshot 2026-04-20 152403" src="https://github.com/user-attachments/assets/1b51d755-ef15-47e7-a7d5-026ff1ea095e" />
<br>
<img width="687" height="145" alt="Screenshot 2026-04-20 152724" src="https://github.com/user-attachments/assets/2efce61a-bef9-41ea-b3e5-0e33f3cb8210" />

---

## Bandit 24 → Bandit 25

### Objective
Brute-force a 4-digit numeric PIN to authenticate to a network daemon on port 30002.

### Concept
Automated credential testing by generating all possible PIN combinations and submitting them to a service.

### Command(s)
```bash
for i in {0000..9999}; do echo "[password] $i"; done | nc localhost 30002
```

### Explanation
A bash loop generates all 10,000 possible 4-digit PINs (0000–9999), combining each with the known password and piping the entire stream into a Netcat connection. The daemon evaluates each pair and responds with the next password when the correct PIN is found.

### Security Relevance
This level demonstrates the fundamental weakness of short numeric PINs against brute-force attacks. A 4-digit PIN has only 10,000 possible values — trivially exhausted even without tooling. Rate limiting, account lockout policies, and longer/more complex secrets are standard mitigations in production systems.

<img width="939" height="183" alt="image" src="https://github.com/user-attachments/assets/1e5830de-db19-40e3-a925-388bb1ee9b53" />

---

## Bandit 25 → Bandit 26

### Objective
Escape from a restricted shell that uses `more` as the login shell.

### Concept
Exploiting interactive pager programs to break out of a restricted environment.

### Command(s)
```
# 1. Resize terminal window to ~2 lines tall before connecting
# 2. SSH in — the 'more' pager activates due to the small terminal
# 3. Press 'v' inside more to open Vim
# 4. In Vim: :set shell=/bin/bash  then  :shell
```

### Explanation
The `bandit26` account's login shell is set to a custom script that invokes `more` to display a text file. When the terminal is small enough, `more` enters interactive mode instead of exiting immediately. From within `more`, pressing `v` opens the file in Vim. Vim's `:shell` command can then be used to spawn a real shell — but the restricted shell must first be overridden using `:set shell=/bin/bash`.

### Security Relevance
Restricted shells and limited environments are a common defense mechanism. However, many standard Unix utilities (Vim, less, man, awk, find) have documented shell escape sequences. This level illustrates why allowlisting available programs in restricted environments must be done carefully — seemingly benign tools can become escape vectors.

<img width="665" height="229" alt="Screenshot 2026-04-23 164446" src="https://github.com/user-attachments/assets/7a0e71da-f03d-497f-ad15-6618f3ef521d" />
<br>
<img width="665" height="229" alt="Screenshot 2026-04-23 164446" src="https://github.com/user-attachments/assets/e4b2c31c-4472-45d1-b403-5d2893d83abb" />

---

## Bandit 26 → Bandit 27

### Objective
Use a SetUID binary available in the `bandit26` home directory to read the next password.

### Concept
Leveraging a SUID binary to execute commands as a higher-privileged user.

### Command(s)
```bash
./bandit27-do cat /etc/bandit_pass/bandit27
```

### Explanation
The `bandit27-do` binary is a SetUID executable owned by `bandit27`. Running it with `cat /etc/bandit_pass/bandit27` as the argument executes that command with `bandit27`'s privileges, allowing the otherwise inaccessible password file to be read.

<img width="713" height="205" alt="image" src="https://github.com/user-attachments/assets/e63d5e3e-337f-4c50-81da-16da2cba073a" />

---

## Bandit 27 → Bandit 28

### Objective
Clone an SSH-hosted Git repository and find the password within it.

### Concept
Git repository cloning over SSH, including handling non-standard port configurations.

### Command(s)
```bash
git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo /tmp/myrepo
cat /tmp/myrepo/README
```

### Explanation
Git supports SSH as a transport protocol. The repository URI includes the port (`2220`) explicitly since the SSH service runs on a non-default port. Once cloned, the repository contents are examined for the password.

### Security Relevance
Git repositories frequently contain sensitive information — API keys, credentials, internal URLs, and configuration secrets. Exposed repositories (on misconfigured servers or accidentally made public) are a common real-world data leakage vector.

<img width="892" height="406" alt="image" src="https://github.com/user-attachments/assets/6be32f58-5c5f-42e0-84b8-cc127462f32f" />
<br>
<img width="684" height="353" alt="image" src="https://github.com/user-attachments/assets/7fd69448-51f2-4bda-86db-113c77e553c1" />

---

## Bandit 28 → Bandit 29

### Objective
Recover a password that was committed to a Git repository and subsequently redacted.

### Concept
Inspecting Git commit history to find previously exposed sensitive data.

### Command(s)
```bash
git log -p
```

### Explanation
`git log -p` displays the full commit history along with the diff (patch) for each commit. Even after a sensitive value is removed or replaced in a later commit, it remains visible in the repository's history. The redacted password can be found in an earlier commit's diff.

### Security Relevance
This is one of the most common real-world security issues affecting development teams. Secrets committed to Git — even briefly — persist in the history indefinitely unless explicitly purged. Tools like `trufflehog`, `gitleaks`, and `git-secrets` are used in security pipelines to detect credentials in commit histories.

<img width="378" height="181" alt="image" src="https://github.com/user-attachments/assets/71c85614-dbf1-4b34-a381-a95b0a1c3ab6" />
<br>
<img width="936" height="376" alt="image" src="https://github.com/user-attachments/assets/3c696bcc-ee0d-4782-9313-5382818aed72" />
<br>
<img width="933" height="357" alt="image" src="https://github.com/user-attachments/assets/301a9f4d-c362-4b2e-802f-1ede1306bf8f" />

---

## Bandit 29 → Bandit 30

### Objective
Locate a password hidden in a non-default Git branch.

### Concept
Enumerating and switching between remote Git branches.

### Command(s)
```bash
git branch -a
git checkout [target-branch]
cat README.md
```

### Explanation
`git branch -a` lists both local and remote-tracking branches. The password is stored in a branch other than `main` — switching to that branch with `git checkout` makes its files accessible. This reflects a real scenario where sensitive content exists in feature or development branches not meant for public consumption.

### Security Relevance
Developers sometimes store credentials or test data in non-production branches, assuming they are not accessible. In publicly hosted repositories, all branches are equally visible. Auditing all branches of a repository — not just `main` — is necessary for a thorough security review.

<img width="433" height="189" alt="image" src="https://github.com/user-attachments/assets/0fdb96d1-dc56-4263-996e-563a0c39f70a" />
<br>
<img width="948" height="610" alt="image" src="https://github.com/user-attachments/assets/69fce6e0-1aae-4d3c-8441-7ddb1f8b4895" />
<br>
<img width="611" height="360" alt="image" src="https://github.com/user-attachments/assets/411e43a2-497f-4c87-b2e9-bef9277dae72" />

---

## Bandit 30 → Bandit 31

### Objective
Retrieve a password hidden within a Git tag.

### Concept
Git tags are references to specific commits and can carry associated data objects, including annotated tags with messages.

### Command(s)
```bash
git tag
git show [tag_name]
```

### Explanation
`git tag` lists all tags in the repository. `git show [tag_name]` displays the tag's content, which in this case contains the password. Unlike branches, tags are often overlooked during a repository audit, making them a subtle location for hidden data.

### Security Relevance
Git tags are another repository component that can inadvertently expose sensitive data. A thorough security audit of a repository must inspect tags, stashes, and notes — not only branches and commit history.

<img width="333" height="154" alt="image" src="https://github.com/user-attachments/assets/79359bb7-5af5-4113-9536-e11a45852744" />

---

## Bandit 31 → Bandit 32

### Objective
Push a specific file to a remote repository, bypassing a `.gitignore` rule that prevents it from being tracked.

### Concept
Forcing Git to track files that are excluded by `.gitignore` using the `-f` (force) flag.

### Command(s)
```bash
echo "May I come in?" > key.txt
git add -f key.txt
git commit -m "Add key"
git push origin master
```

### Explanation
`.gitignore` instructs Git to ignore certain file patterns. `git add -f` overrides this exclusion and stages the file regardless. Once committed and pushed, the remote server processes the submission and returns the next password in the push response.

### Security Relevance
`.gitignore` is not a security control — it is a convenience feature. Any ignored file can be force-added to the repository. Sensitive files should never rely on `.gitignore` as their only protection; they should be excluded through access controls, secrets management tools, or pre-commit hooks.

<img width="466" height="352" alt="Screenshot 2026-04-23 173332" src="https://github.com/user-attachments/assets/51a01b2f-dca2-4835-addb-4bb5c8bea4fb" />
<br>
<img width="715" height="509" alt="image" src="https://github.com/user-attachments/assets/3273ba7a-bb5e-4226-80e3-eb396a43ba5a" />

---

## Bandit 32 → Bandit 33

### Objective
Escape an uppercase-converting shell that blocks all standard commands.

### Concept
Using shell positional parameters to reference the current shell binary without typing its name.

### Command(s)
```
$0
```

### Explanation
The restricted shell converts all input to uppercase before executing it, rendering commands like `sh`, `bash`, and `cat` unusable. However, `$0` is a special shell variable that expands to the name of the currently running shell. Since `$0` is not alphabetic, it is not uppercased and can be used to invoke a new, unrestricted shell. From there, the password is read normally.

### Security Relevance
This level demonstrates that restricted shells must account for all possible input vectors — not just command names. Special variables, positional parameters, and shell builtins can bypass naive filtering mechanisms. Building a truly secure restricted environment requires careful design and is notoriously difficult.

<img width="672" height="351" alt="image" src="https://github.com/user-attachments/assets/e1f7fab0-b394-4c94-9e2e-7341f15c61f8" />

---

## Conclusion

Completing the Bandit wargame provided hands-on exposure to a broad range of Linux and cybersecurity fundamentals. 
