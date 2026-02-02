## bandit0

command: ssh username -p <port_number> \
ssh bandit0@bandit.labs.overthewire.org -p 2220

<img width="683" height="282" alt="image" src="https://github.com/user-attachments/assets/57d10bd1-0b96-4805-84a5-233e7a260c91" />

## bandit0 → bandit1

ssh into bandit1 using the password found in bandit0 \
The password for the next level is stored in a file called **readme** located in the home directory. 

<img width="1002" height="307" alt="Screenshot 2026-01-29 225422" src="https://github.com/user-attachments/assets/854fd7ab-0b74-4c57-9bcd-59e42f08beb0" />

## bandit1 → bandit2

The password for the next level is stored in a file called **-** located in the home directory

<img width="747" height="273" alt="image" src="https://github.com/user-attachments/assets/814009dd-7ad1-4d08-9fa6-444a3241e76d" />

## bandit2 → bandit3

The password for the next level is stored in a file called **--spaces in this filename--** located in the home directory

<img width="781" height="242" alt="image" src="https://github.com/user-attachments/assets/763a193b-d19d-48fd-a876-9fc925e29c0d" />

## bandit3 → bandit 4

The password for the next level is stored in a hidden file in the **inhere** directory.

<img width="671" height="350" alt="image" src="https://github.com/user-attachments/assets/8c900aa4-d9fb-4780-8ecc-754724692ff4" />

## bandit4 to bandit5

The password for the next level is stored in the only human-readable file in the **inhere** directory

<img width="568" height="287" alt="image" src="https://github.com/user-attachments/assets/b8b58100-5a5b-41b8-b7c8-938d54ef055c" />

<img width="420" height="282" alt="image" src="https://github.com/user-attachments/assets/93c7bbee-d6e5-4026-8f2c-1a4f73dddd36" />

- file is a shell command for reporting the type of data contained in a file.

## bandit5 to bandit6

The password for the next level is stored in a file somewhere under the **inhere** directory and has all of the following properties:
- human-readable
- 1033 bytes in size
- not executable

<img width="665" height="590" alt="image" src="https://github.com/user-attachments/assets/6a4f82e4-d0aa-41f8-9207-b3039b1246b9" />

## bandit6 to bandit7

The password for the next level is stored somewhere on the server and has all of the following properties:
- owned by user bandit7
- owned by group bandit6
- 33 bytes in size

<img width="762" height="108" alt="image" src="https://github.com/user-attachments/assets/f27a805c-f988-40cd-a74b-9dc6f2fbda79" />

## bandit7 to bandit8

The password for the next level is stored in the file data.txt next to the word millionth

<img width="503" height="72" alt="image" src="https://github.com/user-attachments/assets/85e111e9-be3c-4fa3-89f2-c5a861ae26e0" />

## bandit8 to bandit9

The password for the next level is stored in the file data.txt and is the only line of text that occurs only once

<img width="422" height="93" alt="image" src="https://github.com/user-attachments/assets/50d59197-521f-4355-865f-acb2aa7f3c3e" />

## bandit9 to bandit10

The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.

<img width="607" height="137" alt="image" src="https://github.com/user-attachments/assets/4de3015f-4ded-4102-8b74-964434aeab3f" />

## bandit10 to bandit11

The password for the next level is stored in the file data.txt, which contains base64 encoded data

<img width="766" height="127" alt="image" src="https://github.com/user-attachments/assets/b9d54a9f-961d-4eae-bb5f-45e4ccd0657b" />

## bandit11 to bandit12

The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

<img width="676" height="81" alt="image" src="https://github.com/user-attachments/assets/35f07887-6159-40ab-b7f8-b158c1efb054" />
