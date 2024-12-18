# Lost Legacy - TryHackMe CTF Write-up

**Room Link:** [Lost Legacy](https://tryhackme.com/r/room/lostlegacy)

Our objective in this challenge is to follow our companions in their quest for a lost civilization while avoiding being misled by a traitor. Let's dive into the steps to solve this challenge.

---

## 1. Initial Enumeration

### Nmap Scan
We begin by scanning the target machine for open ports and services:

```bash
nmap -sCV -p- <IP>
```

The scan reveals three open ports:
- **22** (SSH)
- **80** (HTTP)
- **221** (FTP)

---

## 2. FTP Exploration

The FTP service on port 221 allows anonymous login. Connecting to the server:

```bash
ftp <IP> 221
```

After logging in, we find a file named `note.txt`. Upon reading it, the note leads us to a deception. It seems to be a trick by the traitor, Oliver.

Looking deeper into the FTP directory with the `ls -la` command, we discover a hidden file named `.secret_note`. Reading this file reveals:
- A piece of text
- Something encrypted (likely Base64 encoded)

Decoding the Base64 string:

```bash
echo "<base64_string>" | base64 -d
```

This gives us:
- The first flag (`flag.txt`)
- A secret web directory

---

## 3. Web Directory Discovery

Navigating to the secret directory in a browser returns a 404 error. However, inspecting the source code of the page reveals a hidden path. Following this new path leads us to a validation page.

---

## 4. Exploiting the Validation Page

The validation page initially seems resistant to command injection attempts. Inspecting the page source code further reveals a JavaScript-based validation mechanism.

The JavaScript code contains:
- A password
- A set of credentials

By extracting these directly from the JavaScript, we now have a username and password.

---

## 5. SSH Access

Using the credentials obtained from the validation page, we log in to the server via SSH:

```bash
ssh <username>@<IP>
```

Upon logging in, we locate and read the `user.txt` file to retrieve the second flag.

---

## 6. Privilege Escalation

Enumerating the system further, we use the `sudo -l` command to check our sudo privileges. It reveals that we can execute `/usr/bin/at` as root.

Using [GTFOBins](https://gtfobins.github.io/gtfobins/at/), we find a way to escalate privileges:

```bash
echo "<command>" | at now
```

This allows us to execute commands as root. Once elevated, we access the `root.txt` file to retrieve the final flag.

---

## Conclusion

This challenge demonstrates the importance of careful enumeration and source code analysis. By thoroughly investigating every step, we successfully avoided the traitor's deception and achieved root access.

### Key Takeaways:
- Always check hidden files and directories.
- Source code analysis can reveal critical information.
- Utilize tools like GTFOBins for privilege escalation.

Thank you for reading! I hope this write-up helps you in your CTF journey!
