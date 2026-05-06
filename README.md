# Week-6-Lian-Yu

Step 1: Reconnaissance

nmap -sC -sV 10.48.165.77
Start with Nmap scan to check for open ports and running services.

<img width="648" height="509" alt="Screenshot 2026-04-24 184902" src="https://github.com/user-attachments/assets/d568cecb-b526-47b0-8919-1f770c11df1b" />
The result showed the 4 open port and 3 filter port. To explore deeper by try running discovery scan


Step 2 — Quick check of FTP

ftp 10.48.165.77
<img width="456" height="149" alt="image" src="https://github.com/user-attachments/assets/f16017e2-b182-4256-af65-ec80516c8e83" />
It will ask for credential password


Step 3 — Web Enumeration

<img width="847" height="526" alt="image" src="https://github.com/user-attachments/assets/e06cdca4-b20a-4d0d-a677-bcebd3645e50" />
visit http://10.48.165.77. Found a page titled ARROWVERSE with a big blurb about Oliver Queen / Lian Yu but didn’t contain the flag itself.

Step 4 — Directory enumeration with Gobuster
gobuster dir -u http://10.48.165.77/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
<img width="648" height="509" alt="Screenshot 2026-04-24 184902" src="https://github.com/user-attachments/assets/a382e959-13fb-458b-83f0-901f2a1d0375" />

Inside /island, the page source had a hidden comment: "go! go!".After some inspection, I noticed a hidden codeword: vigilante (same color as the background).

<img width="793" height="380" alt="Screenshot 2026-04-24 185021" src="https://github.com/user-attachments/assets/17e0f2b7-be9e-45a2-bb2a-882f3e213fbf" />


Step 5 — Continue bruteforcing discovered path

Because there a /island exist on it, try running a Gobuster on /island tol access more deeper
gobuster dir -u http://10.48.165.77/island/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
<img width="639" height="511" alt="Screenshot 2026-04-24 185319" src="https://github.com/user-attachments/assets/c9e14f41-6225-49f5-b201-374b4ae18bac" />
Result found /island/2100
<img width="834" height="576" alt="Screenshot 2026-04-24 185402" src="https://github.com/user-attachments/assets/34d04183-312b-4f45-b49b-8b1c4ff3019e" />

<img width="714" height="275" alt="image" src="https://github.com/user-attachments/assets/ee898ffc-fd6a-48d7-b0fd-130820574cbb" />
That files with the .ticket extension exist in this directory
Try running Gobuster again with the file extension flag -x .ticket.
gobuster dir -u http://10.48.165.77/island/2100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x .ticket -t 50 

<img width="644" height="508" alt="Screenshot 2026-04-24 185851" src="https://github.com/user-attachments/assets/7b0a4f84-eede-4c6c-9382-be73d36c7f47" />
Open http://10.48.165.77/island/2100/green_arrow.ticket
<img width="610" height="184" alt="Screenshot 2026-04-24 185928" src="https://github.com/user-attachments/assets/72356664-ad94-4db6-8b24-e44d595f75b3" />

 Step 6 — Decode the .ticket (Base58)
 
 The .ticket contents looked like Base58. Try using Base58 converter.
 <img width="939" height="506" alt="Screenshot 2026-04-24 190129" src="https://github.com/user-attachments/assets/b372f530-6bee-47a9-89d3-2905060a91af" />

 Step 7: FTP Access
 Logged into the FTP using vigilante as our username and the password we found
 ftp 10.48.165.77
 <img width="614" height="186" alt="Screenshot 2026-04-24 190306" src="https://github.com/user-attachments/assets/6e5ee875-cae9-4c7d-b7d2-97e0303ebae7" /> 

Username: vigilante
Password: !#th3h00d

<img width="608" height="225" alt="image" src="https://github.com/user-attachments/assets/a5555d32-f6b1-40c8-a96b-1e1551af5ada" />

get file-name
<img width="627" height="386" alt="Screenshot 2026-04-24 190544" src="https://github.com/user-attachments/assets/d2743965-4ec2-46e8-a861-3d19d89a5778" />

Now check the other_user file
<img width="619" height="129" alt="Screenshot 2026-04-24 191510" src="https://github.com/user-attachments/assets/3e94d463-4d9e-4deb-a130-7f4f37e8f6f1" />
<img width="644" height="506" alt="Screenshot 2026-04-24 191548" src="https://github.com/user-attachments/assets/5322c5c5-70eb-44a2-9163-a0173951b605" />


 Step 8 — Steganography Hunt in Images
 With three images downloaded, tested it with steghide
 <img width="621" height="282" alt="Screenshot 2026-04-24 191836" src="https://github.com/user-attachments/assets/f879662e-5775-43b9-8b27-536ad5866509" />
 Most all of them is Unsupported format error

 Step 9 — Fixing Broken Image Header
The error on Leave_me_alone.png hinted at corrupt file headers. Using a hex editor (HexEd) to fix it.

<img width="951" height="604" alt="image" src="https://github.com/user-attachments/assets/999fbf70-1cea-4055-b5eb-47c556f7935e" />

Try opening the file again.

<img width="854" height="492" alt="image" src="https://github.com/user-attachments/assets/bd6bfc38-a845-4e5b-a655-0fabf7ae573b" />

Step 10 — Cracking aa.jpg

<img width="543" height="135" alt="Screenshot 2026-04-24 192353" src="https://github.com/user-attachments/assets/57580405-3155-42bd-a4c6-7c799578383e" />

<img width="496" height="99" alt="Screenshot 2026-04-24 192454" src="https://github.com/user-attachments/assets/bf5b4c1f-a856-48fc-b1c0-084f19914437" />

<img width="546" height="700" alt="image" src="https://github.com/user-attachments/assets/2bc32824-582e-47ba-a7b4-31bd665ed52b" />


Step 11 — Extracting the Archive

<img width="501" height="155" alt="Screenshot 2026-04-24 192546" src="https://github.com/user-attachments/assets/cdefdb89-ee7c-4a1d-8bb1-31d4e0474634" />
That look like a credential password

Step 12 — Reading the Files

<img width="631" height="255" alt="Screenshot 2026-04-24 192603" src="https://github.com/user-attachments/assets/aefb0450-dfec-4eac-b3c9-fd7735f1c553" />


Step 13 — Gaining SSH Access
At this point we already collect a file called ".other_user" hinted  at Slade Wilson (Deathstroke) as another user and a password "M2tahuman" from stego extraction

Next we try SSH

<img width="628" height="517" alt="Screenshot 2026-04-24 192805" src="https://github.com/user-attachments/assets/0422d5ac-ef35-484e-82aa-2f9bc22beb46" />

Look like already succes to log on

Step 14 — Exploring the User’s Home Directory

<img width="561" height="229" alt="Screenshot 2026-04-24 192905" src="https://github.com/user-attachments/assets/282d494a-e1a5-4375-9ae5-bbda910adf62" />

Open the txt file

<img width="627" height="514" alt="Screenshot 2026-04-24 193232" src="https://github.com/user-attachments/assets/ecd80364-4a6f-4278-9856-b8f58b1dd490" />

That is first flag from the box

Step 16 — Looking for Privilege Escalation Paths

Since only user slade, need to check sudo privileges
<img width="625" height="160" alt="image" src="https://github.com/user-attachments/assets/60956bdb-1bf4-4d2d-bec9-67aa1d43f00f" />
Since pkexec allows executing commands as another user, it can attempt to spawn a root shell directly.

Exploitation:

<img width="550" height="113" alt="image" src="https://github.com/user-attachments/assets/51695efd-cd13-4578-a3c5-06bf566ad514" />

Looking for root.txt file

<img width="524" height="155" alt="image" src="https://github.com/user-attachments/assets/93147fa3-9544-4dbf-8fcb-8d8064a85376" />

Output
<img width="622" height="257" alt="image" src="https://github.com/user-attachments/assets/95e0f9a0-0281-4f83-89db-67d508ef1134" />



