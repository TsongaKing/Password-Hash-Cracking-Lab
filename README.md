Password Hash Cracking Lab
Overview
This project documents a hands-on cybersecurity lab focused on identifying and cracking different password hash types using industry-standard password auditing tools.

The objective was to understand how password hashing works, how weak passwords can be recovered using dictionary attacks, and how salts and keys affect cracking methods.

This exercise demonstrates practical skills used in security testing, incident response, and authentication security analysis.

Objectives
Identify different cryptographic hash types

Understand the impact of salting and HMAC on password security

Use password auditing tools to recover weak passwords

Perform dictionary attacks using common wordlists

Verify cracked hashes using cryptographic tools

Tools Used
Tool	Purpose
Hashcat	GPU/CPU accelerated password recovery tool
John the Ripper	Password cracking and auditing tool
CyberChef	Cryptographic analysis and verification tool
Kali Linux	Operating system with pre-installed security tools
rockyou.txt	Common password wordlist for dictionary attacks
Hash Types Investigated
During the lab, several common password hashing algorithms were analyzed and cracked:

Hash Type	Description	Hashcat Mode
MD5	Legacy 128-bit hashing algorithm commonly found in old databases	0
NTLM	Windows authentication hash used in Microsoft environments	1000
SHA256	256-bit secure cryptographic hashing algorithm	1400
SHA512	512-bit strong hashing algorithm often used in secure systems	1700
bcrypt	Adaptive password hashing with cost factor; resistant to brute force	3200
HMAC-SHA1	Keyed-hash message authentication code using SHA1	160
Methodology
1. Hash Identification
Each hash was analyzed to determine the correct algorithm before cracking. Identification was done by examining:

Hash length (number of characters)

Character set (hexadecimal vs. special characters)

Prefix indicators (e.g., $2a$ for bcrypt, $6$ for SHA512crypt)

Context (where the hash came from)

Correct identification is critical because cracking tools require the correct mode to work efficiently.

2. Dictionary Attack
A dictionary attack was performed using the rockyou.txt password list, which contains over 14 million leaked passwords.

Basic Hashcat syntax:

bash
hashcat -m [MODE] -a 0 hash.txt /usr/share/wordlists/rockyou.txt
Parameters:

Option	Meaning
-m	Hash type mode
-a 0	Dictionary attack
hash.txt	File containing target hashes
rockyou.txt	Password wordlist
3. Verification
Recovered passwords were verified by recreating the hash using CyberChef and comparing the output to the original hash. Matching outputs confirm successful cracking.

Challenges Faced & Solutions
Challenge	Solution
Hashcat "Token length exception"	Corrected hash format to hash:key for HMAC modes
rockyou.txt not found	Located at /usr/share/wordlists/rockyou.txt; extracted with sudo gunzip
Choosing correct hash mode	Used hash length and prefixes to identify algorithm
HMAC format requirements	Learned that HMAC cracking requires both hash AND key
Command Examples
MD5 Cracking
bash
echo "5f4dcc3b5aa765d61d8327deb882cf99" > hash.txt
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
NTLM Cracking
bash
echo "b4b9b02e6f09a9bd760f388b67351e2b" > ntlm.txt
hashcat -m 1000 -a 0 ntlm.txt /usr/share/wordlists/rockyou.txt
SHA256 Cracking
bash
echo "e24df70c9d9c81d60f0e475be740a6cee28744087976f74974d4390396ce36f1" > sha256.txt
hashcat -m 1400 -a 0 sha256.txt /usr/share/wordlists/rockyou.txt
SHA512 Cracking
bash
echo "faa2b8b7cd11d908f101df15a0b12d4c05a89abc9604df0f275a4fc9a00280027c95c4b0e1dfa314b2c4224e820146568205ffd1e58eb7bf6fd07dfe79b83060" > sha512.txt
hashcat -m 1700 -a 0 sha512.txt /usr/share/wordlists/rockyou.txt
bcrypt Cracking
bash
echo '$2a$06$7yoU3Ng8dHTXphAg913cyO6Bjs3K5lBnwq5FJyA6d01pMSrddr1ZG' > bcrypt.txt
hashcat -m 3200 -a 0 bcrypt.txt /usr/share/wordlists/rockyou.txt
HMAC-SHA1 Cracking (with key)
bash
# Format: hash:key
echo 'e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme' > hmac.txt
hashcat -m 160 -a 0 hmac.txt /usr/share/wordlists/rockyou.txt -o cracked.txt
John the Ripper Alternative
bash
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show hash.txt
Viewing Cracked Passwords
bash
# Check Hashcat potfile
cat ~/.hashcat/hashcat.potfile

# Check John potfile
cat ~/.john/john.pot

# Show Hashcat results
hashcat -m 160 --show hmac.txt
Results
All target hashes were successfully cracked using dictionary attacks with the rockyou.txt wordlist:

Hash	Password	Algorithm
5f4dcc3b5aa765d61d8327deb882cf99	password	MD5
b4b9b02e6f09a9bd760f388b67351e2b	[NTLM password]	NTLM
e24df70c9d9c81d60f0e475be740a6cee28744087976f74974d4390396ce36f1	sadierose	SHA256
faa2b8b7cd11d908f101df15a0b12d4c05a89abc9604df0f275a4fc9a00280027c95c4b0e1dfa314b2c4224e820146568205ffd1e58eb7bf6fd07dfe79b83060	class1999	SHA512
$2a$06$7yoU3Ng8dHTXphAg913cyO6Bjs3K5lBnwq5FJyA6d01pMSrddr1ZG	85208520	bcrypt
e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme	481616481616	HMAC-SHA1
Verification with CyberChef
All cracked passwords were verified using CyberChef to ensure accuracy:

HMAC-SHA1 Verification Example:
Navigate to CyberChef

Add HMAC operation

Configure:

Key: tryhackme

Algorithm: SHA1

Input: 481616481616

Output matches: e5d8870e5bdd26602cab8dbe07a942c8669e56d6 ✓

SHA256 Verification Example:
Add SHA256 operation

Input: sadierose

Output matches target hash ✓

Key Lessons Learned
Password Weakness
Weak passwords contained in common wordlists can be cracked within seconds using dictionary attacks. Even complex-looking numeric passwords like 481616481616 are vulnerable if they appear in wordlists.

Importance of Salting
Salts make precomputed attacks such as rainbow tables infeasible by ensuring that identical passwords produce different hashes.

HMAC Security
HMAC adds a secret key to the hashing process, increasing security compared to standard hashes. However, if the key is known (as in this lab), the password can still be cracked.

Tool Proficiency
Learning to use tools like Hashcat and John the Ripper is essential for:

Penetration testing

Digital forensics

Password security auditing

Incident response

Hash Format Matters
Tools require precise formatting. The hash:key format for HMAC cracking was essential for success.

Skills Demonstrated
✅ Hash type identification

✅ Linux command-line proficiency

✅ Password cracking techniques

✅ Dictionary attacks

✅ Hash verification methods

✅ Security tool usage (Hashcat, John, CyberChef)

✅ Problem-solving and troubleshooting

Next Steps
Future work could include:

Attempting salted MD5 and SHA256 cracking

Exploring rule-based attacks with Hashcat

Generating and using rainbow tables

Cracking more complex hash formats (e.g., TrueCrypt, LUKS)

Building custom wordlists with CeWL

Understanding GPU acceleration for faster cracking

Author
Phangasasa Muhlaba

Aspiring Cloud Security Engineer focused on:

Microsoft Azure security

DevSecOps practices

Cloud infrastructure protection

Cybersecurity fundamentals

Acknowledgments
TryHackMe for the hash cracking challenges

Hashcat and John the Ripper development teams

The cybersecurity community for sharing knowledge and wordlists

This project was completed for educational purposes to understand password security and auditing techniques.

