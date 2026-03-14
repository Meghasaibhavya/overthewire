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

## 🟢 Bandit 8 → Bandit 9
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
*In Progress...*
