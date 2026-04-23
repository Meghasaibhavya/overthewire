# OverTheWire: Bandit Walkthrough

A collection of solutions and technical notes for the Bandit wargame, focusing on Linux command-line mastery and security basics.

## Bandit 0
**Objective:** Log into the game server via SSH.
* **Command:** `ssh bandit0@bandit.labs.overthewire.org -p 2220`
<img width="683" height="282" alt="image" src="https://github.com/user-attachments/assets/57d10bd1-0b96-4805-84a5-233e7a260c91" />

## Bandit 0 → Bandit 1
**Objective:** Locate the password in a file named `readme`.
* **Concept:** Basic file navigation and the `cat` command.
<img width="1002" height="307" alt="Screenshot 2026-01-29 225422" src="https://github.com/user-attachments/assets/854fd7ab-0b74-4c57-9bcd-59e42f08beb0" />

## Bandit 1 → Bandit 2
**Objective:** Read a file named `-`.
* **Concept:** Handling special characters. Using `./-` or `< -` to prevent the shell from interpreting the dash as a command flag.
<img width="747" height="273" alt="image" src="https://github.com/user-attachments/assets/814009dd-7ad1-4d08-9fa6-444a3241e76d" />

## Bandit 2 → Bandit 3
**Objective:** Access a file with spaces in the filename: `spaces in this filename`.
* **Concept:** Shell escaping. Use quotes `"filename"` or backslashes `\ ` to handle spaces.
<img width="781" height="242" alt="image" src="https://github.com/user-attachments/assets/763a193b-d19d-48fd-a876-9fc925e29c0d" />

## Bandit 3 → Bandit 4
**Objective:** Find a hidden file in the `inhere` directory.
* **Concept:** Use `ls -a` to view hidden files (files starting with a dot `.`).
<img width="671" height="350" alt="image" src="https://github.com/user-attachments/assets/8c900aa4-d9fb-4780-8ecc-754724692ff4" />

## Bandit 4 → Bandit 5
**Objective:** Identify the only human-readable file in a directory.
* **Tool:** `file`. I used this command to check the data type of every file until I found "ASCII text."
<img width="568" height="287" alt="image" src="https://github.com/user-attachments/assets/b8b58100-5a5b-41b8-b7c8-938d54ef055c" />
<img width="420" height="282" alt="image" src="https://github.com/user-attachments/assets/93c7bbee-d6e5-4026-8f2c-1a4f73dddd36" />

## Bandit 5 → Bandit 6
**Objective:** Find a file based on specific properties: 1033 bytes, not executable, human-readable.
* **Command:** `find inhere/ -type f -size 1033c ! -executable`
<img width="665" height="590" alt="image" src="https://github.com/user-attachments/assets/6a4f82e4-d0aa-41f8-9207-b3039b1246b9" />

## Bandit 6 → Bandit 7
**Objective:** Find a file owned by a specific user/group on the entire server.
* **Command:** `find / -user bandit7 -group bandit6 -size 33c 2>/dev/null`
* **Note:** Redirected errors to `/dev/null` to filter out "Permission Denied" noise.
<img width="762" height="108" alt="image" src="https://github.com/user-attachments/assets/f27a805c-f988-40cd-a74b-9dc6f2fbda79" />

## Bandit 7 → Bandit 8
**Objective:** Extract password next to the word "millionth."
* **Command:** `grep "millionth" data.txt`
<img width="503" height="72" alt="image" src="https://github.com/user-attachments/assets/85e111e9-be3c-4fa3-89f2-c5a861ae26e0" />

## Bandit 8 → Bandit 9
**Objective:** Find the only unique line of text in a file.
* **Command:** `sort data.txt | uniq -u`
* **Concept:** `uniq` only works on sorted data.
<img width="422" height="93" alt="image" src="https://github.com/user-attachments/assets/50d59197-521f-4355-865f-acb2aa7f3c3e" />

## Bandit 9 → Bandit 10
**Objective:** Extract human-readable strings from a binary file.
* **Command:** `strings data.txt | grep "=="`
<img width="607" height="137" alt="image" src="https://github.com/user-attachments/assets/4de3015f-4ded-4102-8b74-964434aeab3f" />

## Bandit 10 → Bandit 11
**Objective:** Decode Base64 data.
* **Command:** `base64 -d data.txt`
<img width="766" height="127" alt="image" src="https://github.com/user-attachments/assets/b9d54a9f-961d-4eae-bb5f-45e4ccd0657b" />

## Bandit 11 → Bandit 12
**Objective:** Decode a ROT13 encrypted file.
* **Command:** `tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt`
<img width="676" height="81" alt="image" src="https://github.com/user-attachments/assets/35f07887-6159-40ab-b7f8-b158c1efb054" />

## Bandit 12 → Bandit 13
**Objective:** Decompress a file multiple times (Gzip, Bzip2, Tar).
**Process:** Used `xxd -r` to reverse the hexdump, then repeatedly used `file` to check the compression type and renamed/decompressed accordingly.

**Step 1: Reversing the Hexdump**<br>

<img width="581" height="195" alt="Image" src="https://github.com/user-attachments/assets/a568ba15-a61d-4014-a94b-1c35a93110e5" />
<br>

**Step 2: Identifying and Decompressing Gzip**<br>

<img width="630" height="256" alt="Image" src="https://github.com/user-attachments/assets/e1bb3967-3f91-4fda-bc9d-f2499d2b9650" />
<br>

**Step 3: Handling Bzip2 Layers**<br>

<img width="573" height="217" alt="Image" src="https://github.com/user-attachments/assets/981c2aa4-65bd-412e-a588-dc5324bc634a" />
<br>

**Step 4: Extracting Tar Archives**<br>

<img width="582" height="158" alt="Image" src="https://github.com/user-attachments/assets/0200cc60-b6a8-4eb0-b060-cfb5d8e2b2cd" />
<br>

**Step 5: Final Decompressions**<br>

<img width="1367" height="381" alt="Image" src="https://github.com/user-attachments/assets/50f9a09d-118d-4039-97d6-31ceb56bdf71" />
<br>

**Step 6: Locating the Password File**<br>

<img width="1335" height="213" alt="Image" src="https://github.com/user-attachments/assets/8d4f7f75-2100-4cd7-9563-452e264bc171" />
<br>

**Step 7: Final Password Retrieval**<br>

<img width="533" height="180" alt="Image" src="https://github.com/user-attachments/assets/49238f4b-5d7e-480a-b967-c03061871656" />

## Bandit 13 → Bandit 14
**Objective:** Use a private SSH key for authentication.
* **Concept:** Understanding Private/Public Key Infrastructure (PKI) and using the `-i` (identity file) flag.

**Identifying the Private Key:**<br>

<img width="649" height="258" alt="Image" src="https://github.com/user-attachments/assets/044e29dc-cb83-49ef-a7a0-addc638b2a24" />
<br>

**Executing SSH with Identity File:**<br>

<img width="698" height="262" alt="Image" src="https://github.com/user-attachments/assets/366ef92b-1081-47a2-8c48-d43162cedf89" />
<br>

**Retrieving the Bandit 14 Password:**<br>

<img width="455" height="68" alt="Image" src="https://github.com/user-attachments/assets/bf2e2921-8e93-47a4-8995-6d3b6b1da71a" />

---

## Bandit 14 → Bandit 15
**Objective:** Submit current password to a local port to get the next one.
* **Command:** `nc localhost 30000` (Netcat)

<img width="352" height="148" alt="Image" src="https://github.com/user-attachments/assets/07809e8d-a057-434a-b784-a24beac8cc2b" />

---

## Bandit 15 → Bandit 16
**Objective:** Submit password over SSL/TLS.
* **Tool:** `openssl s_client`
* **Concept:** Learning how to interact with services that require encrypted handshakes rather than plain text.

**Initiating the SSL Connection:**<br>

<img width="722" height="275" alt="Image" src="https://github.com/user-attachments/assets/88cd77c4-63e9-4612-a649-c4c1e495222b" />
<br>

**Authenticated Response:**<br>

<img width="574" height="174" alt="Image" src="https://github.com/user-attachments/assets/1ac5875b-8ab7-4876-97e9-baec1352f4f4" />

## Bandit 16 → Bandit 17
**Objective:** Scan a port range (31000–32000) to find an SSL service that returns credentials.
* **Process:** Performed service reconnaissance via Nmap to identify ports speaking SSL/TLS.
  
<img width="617" height="221" alt="Screenshot 2026-04-20 141534" src="https://github.com/user-attachments/assets/6bca23b7-9d25-45ac-a30b-f18259a85093" />
<br>
<img width="960" height="732" alt="Screenshot 2026-04-20 143143" src="https://github.com/user-attachments/assets/87dbbbe6-b21d-46c9-a1ea-05204cc308ea" />

## Bandit 17 → Bandit 18
**Objective:** Isolate the only modified line between `passwords.old` and `passwords.new`.
* **Concept:** Utilizing the `diff` utility to identify content changes between two files.
  
<img width="709" height="193" alt="Screenshot 2026-04-20 143900" src="https://github.com/user-attachments/assets/34c4b55d-5a88-4258-bc36-1c84c35786de" />

## Bandit 18 → Bandit 19
**Objective:** Bypass a modified `.bashrc` that triggers a logout upon login.
* **Concept:** Executing non-interactive commands directly via SSH to read the file before the shell environment terminates.
  
<img width="615" height="302" alt="Screenshot 2026-04-20 144636" src="https://github.com/user-attachments/assets/06114a00-bc5b-4996-84eb-0e8fc4464eef" />
<br>
<img width="621" height="294" alt="Screenshot 2026-04-20 144645" src="https://github.com/user-attachments/assets/a978ea22-36cc-4268-96b8-0b3c05b83120" />

## Bandit 19 → Bandit 20
**Objective:** Utilize a SetUID binary to escalate privileges and read a protected file.
* **Concept:** Understanding Linux SetUID bits, which execute a file with the owner's permissions.
  
<img width="299" height="84" alt="Screenshot 2026-04-20 144934" src="https://github.com/user-attachments/assets/583f3ec2-9682-46db-84f9-a2a9322810f5" />
<br>
<img width="805" height="161" alt="Screenshot 2026-04-20 145058" src="https://github.com/user-attachments/assets/b24b3b97-5c58-4e5a-9e52-f26c26e68e93" />

## Bandit 20 → Bandit 21
**Objective:** Exchange credentials by establishing a local listener for a SetUID binary.
* **Concept:** Inter-process communication and background tasks using Netcat.
  
<img width="809" height="167" alt="Screenshot 2026-04-20 145359" src="https://github.com/user-attachments/assets/46680315-335f-41f0-bd61-2fb797a3ca9e" />

## Bandit 21 → Bandit 22
**Objective:** Analyze a cron job schedule to find where a password-retrieving script is located.
* **Concept:** Scheduled tasks (cron) and shell script analysis.
  
<img width="813" height="248" alt="Screenshot 2026-04-20 145727" src="https://github.com/user-attachments/assets/40e41a7c-7a26-4775-93dc-d4ee86fa40d0" />

## Bandit 22 → Bandit 23
**Objective:** Reverse-engineer an automated script that generates passwords via MD5 hashing.
* **Process:** Replicated the script's logic manually to generate the corresponding password hash for the target user.
  
<img width="822" height="303" alt="Screenshot 2026-04-20 150114" src="https://github.com/user-attachments/assets/803ac5d8-57eb-4822-86cb-375fc3f2ed79" />
<br>
<img width="774" height="125" alt="Screenshot 2026-04-20 150207" src="https://github.com/user-attachments/assets/19adef42-2ac5-422a-b129-a2308ef1394e" />

## Bandit 23 → Bandit 24
**Objective:** Deploy a script into a cron-monitored directory to capture credentials.
* **Concept:** Exploiting automated cron execution through writable directories.
  
<img width="899" height="600" alt="Screenshot 2026-04-20 151401" src="https://github.com/user-attachments/assets/aec9a14d-8315-4f54-8670-889f71091f4f" />
<br>
<img width="960" height="361" alt="Screenshot 2026-04-20 152403" src="https://github.com/user-attachments/assets/1b51d755-ef15-47e7-a7d5-026ff1ea095e" />
<br>
<img width="687" height="145" alt="Screenshot 2026-04-20 152724" src="https://github.com/user-attachments/assets/2efce61a-bef9-41ea-b3e5-0e33f3cb8210" />

## Bandit 24 → Bandit 25
**Objective:** Brute-force a 4-digit numeric PIN on a network daemon.
* **Command:** `for i in {0000..9999}; do echo "[password] $i"; done | nc localhost 30002`
  
<img width="939" height="183" alt="image" src="https://github.com/user-attachments/assets/1e5830de-db19-40e3-a925-388bb1ee9b53" />

## Bandit 25 → Bandit 26
**Objective:** Perform an environment escape from a restricted shell running `more`.
* **Concept:** Exploiting interactive pagers. By resizing the terminal to 2 lines, the `more` command pauses, allowing a `v` escape into Vim and subsequently spawning a Bash shell.
  
<img width="665" height="229" alt="Screenshot 2026-04-23 164446" src="https://github.com/user-attachments/assets/7a0e71da-f03d-497f-ad15-6618f3ef521d" />
<br>
<img width="665" height="229" alt="Screenshot 2026-04-23 164446" src="https://github.com/user-attachments/assets/e4b2c31c-4472-45d1-b403-5d2893d83abb" />

## Bandit 26 → Bandit 27
**Objective:** Extract the Level 27 password using the `bandit27-do` SetUID binary.
* **Command:** `./bandit27-do cat /etc/bandit_pass/bandit27`
  
<img width="713" height="205" alt="image" src="https://github.com/user-attachments/assets/e63d5e3e-337f-4c50-81da-16da2cba073a" />

## Bandit 27 → Bandit 28
**Objective:** Clone an SSH-hosted Git repository to a local machine.
* **Concept:** Git repository management over non-standard ports.
  
<img width="892" height="406" alt="image" src="https://github.com/user-attachments/assets/6be32f58-5c5f-42e0-84b8-cc127462f32f" />
<br>
<img width="684" height="353" alt="image" src="https://github.com/user-attachments/assets/7fd69448-51f2-4bda-86db-113c77e553c1" />

## Bandit 28 → Bandit 29
**Objective:** Investigate Git commit history to find redacted credentials.
* **Command:** `git log -p`
  
<img width="378" height="181" alt="image" src="https://github.com/user-attachments/assets/71c85614-dbf1-4b34-a381-a95b0a1c3ab6" />
<br>
<img width="936" height="376" alt="image" src="https://github.com/user-attachments/assets/3c696bcc-ee0d-4782-9313-5382818aed72" />
<br>
<img width="933" height="357" alt="image" src="https://github.com/user-attachments/assets/301a9f4d-c362-4b2e-802f-1ede1306bf8f" />

## Bandit 29 → Bandit 30
**Objective:** Locate a password hidden in a remote Git branch.
* **Process:** Used `git branch -a` to view all branches and `git checkout` to switch to the target branch.
  
<img width="433" height="189" alt="image" src="https://github.com/user-attachments/assets/0fdb96d1-dc56-4263-996e-563a0c39f70a" />
<br>
<img width="948" height="610" alt="image" src="https://github.com/user-attachments/assets/69fce6e0-1aae-4d3c-8441-7ddb1f8b4895" />
<br>
<img width="611" height="360" alt="image" src="https://github.com/user-attachments/assets/411e43a2-497f-4c87-b2e9-bef9277dae72" />

## Bandit 30 → Bandit 31
**Objective:** Identify password credentials hidden within Git tags.
* **Command:** `git tag` followed by `git show [tag_name]`.

<img width="333" height="154" alt="image" src="https://github.com/user-attachments/assets/79359bb7-5af5-4113-9536-e11a45852744" />

## Bandit 31 → Bandit 32
**Objective:** Push a required file to the remote repository while bypassing `.gitignore`.
* **Concept:** Utilizing `git add -f` to force-track ignored files.

<img width="466" height="352" alt="image" src="https://github.com/user-attachments/assets/276760ef-f62f-4224-acf7-978c1fc438b4" />
<br>
<img width="715" height="509" alt="image" src="https://github.com/user-attachments/assets/3273ba7a-bb5e-4226-80e3-eb396a43ba5a" />

## Bandit 32 → Bandit 33
**Objective:** Escape a restricted uppercase-only shell using positional parameters.
* **Concept:** Invoking the original shell via `$0` to bypass the filter.

<img width="672" height="351" alt="image" src="https://github.com/user-attachments/assets/e1f7fab0-b394-4c94-9e2e-7341f15c61f8" />
