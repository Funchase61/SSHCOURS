# README-SSH-TEST

# Table of contents

- [Introduction](#Introduction)
- [Manipulations list](#Manipulations-list)
- [Problems encountered and solutions](#Problems-encountered-and-solutions)
- [Theorical analysis](#Theorical-analysis)
- [Conclusion](#Conclusion)
- [Resources](#Resources)
  
# Introduction

To start somewhere, here we're gonna talk about creating a SSH server on a distant server (Ionos servers on Ubuntu) and learn how to protect its datas against cyber villains by using different way to communicate with it and protecting connections.

# Manipulations list

## 1. Connecting to your server

> At that moment you should have created a server on ionos, it can possibly be done with another provider or your own vps

First, we'll try to connect on the `root`[^1] of the server on a terminal.
You will need the ip adress of your server and the password it was given/chosen.
Now proceed like that :
[^1]: Mot anglais pour définir la "racine" donc le point de départ de l'architecture meme des données dans un environnement informatique. ici root représente une entité avec des privilège absolue
```
ssh root@server_adress_ip
```
Then enter your password to acces it. Wonderfull ! your now connected as `root`[^1] in your server !

You can try some command to test the connection :
```
ls
pwd
whoami 
```
Now that you know you're connected, let's try to create an username to navigate as yourself in your vps.
So you'll need to add a new user to your vps with a command and give it a name.
```
adduser "name-user"
```
It will ask you a new password then some informations about you. that's not mandatory to fill everything, you can just press enter until your account is created.

## 2. Generate SSH Keys

Now you'll need to create SSH keys to create an unique authentification for your account and deny any other desktop to connect to your account on your server without those keys. How to create Keys ? you'll need that command to generate it :

```
ssh-keygen -t ed25519
```
It will create 2 keys, one public and one private. The private one will stay in your desktop and will serve as your authentification pass. The public one will be sent to your server and serve as an exemple of what it should accept as "keys" from any users. So now how to send that public one to the server, time to write that command :
```
ssh-copy-id name-user@server_adress_ip
```
After that you should be recognized by your server with your key and it will ask you the passphrase you chosen when u created the pair of keys.

Now that your key authentification is working, for more protection you will desactivate authentification by password, it will only connect your account by key authentification. even if another desktop have the password of your account it will be unable to enter your server cause of the missing key in their desktop.

So you'll proceed like that : 
```
sudo nano /etc/ssh/sshd_config
```
after that you will be in the ssh's configuration file, there you can allow different ports and change some rules but we will see that a bit later. for now we want to make authentification by password unactive.

find the line :
```
#PasswordAuthentification yes
```
then change it for a "no" and delete the " # " to make it work. # is a sign to make the line you write as a commentary and not a code line so it will not be recognized as an instruction. Don't forget to save when you're quitting this

Now restart the service ssh to update it :

```
sudo systemctl restart ssh
```

Now you can try to connect into your server, you'll see that it will authenticate you only by your key automatically and not ask for a password.

## 3. SFTP and SCP transfer
Time to introduce some ways to interact with your server. let's talk about Scp and Sftp. what are they ? :

SFTP (Secure File Transfer Protocol) and SCP (Secure Copy Protocol) are both protocols used for transferring files securely between a local machine and a remote server.

SFTP: A network protocol that works over SSH (Secure Shell) to provide a secure and interactive method for file transfer. It allows you to navigate the file system on the remote server, upload/download files, and manage directories.

SCP: A simpler protocol also based on SSH, used for transferring files securely between systems. Unlike SFTP, SCP only allows file transfer, without the ability to browse directories or perform other file management tasks.

Both use encryption to ensure that the data and credentials are protected during transmission.

Now how do we use these ? at first will see how to transfer files with scp.

> You should be on your local desktop and not in the remote server when you are doing this

Create a random file you can write anything you want in it and give it a name :

```
echo "This is a test file" > file.txt
```
Send it to your server now and declaring where you want it to go : 

```
scp file.txt user-name@server_adress_ip:/home/username/
```
You can verify on your server if it worked by searching with a command we used sooner `ls`.

That's everything you need to know about SCP. Now time to see SFTP !

Connect to your remote server using SFTP : 

```
sftp user-name@server_adress_ip
```
Now you can freely navigate into your directories with `cd` and  `ls` on your remote server, when you are in the right directory where you want to send your file transfer it with this command :

```
put file.txt
```

And Voila ! now you have two file.txt in `/home/username/`
You'll see that sftp is a more versatile way to transfer files and directories

## 4. Creating a SSH Tunnel and redirecting ports

Using SSH tunneling and port forwarding is beneficial because it secures communication between your local machine and a remote server. Here’s why it’s useful:

All data transmitted through the tunnel is encrypted via SSH, protecting sensitive information from being intercepted.

You can securely access services (like web applications or databases) running on a remote server, even if those services are not directly exposed to the internet.

SSH tunneling can help you access remote services through restrictive firewalls by routing traffic through a secure, encrypted connection.

In short, it adds a layer of security and flexibility when accessing remote resources.

we will try to redirect local port 8080 to the remote server's port 80 : The command for this is :
```
ssh -L 8080:localhost:80 user-name@server_adress_ip
```
This will forwwards the local port 8080 to port 80 on the remote server

Now try to access it with your browser and type in the search bar `http://localhost:8080` , normaly it shouldn't work, because you're missing an image for your web page, so there is nothing to browse.

So you need to install one, there we will use Nginx :

```
sudo apt install nginx
```

remember to do this on the remote server and not in local of your desktop.

It should work now ! you did create a port forwarding well done ! 


## 5. Securing SHH server

Changing the default SSH port from 22 to 2222 (or any other non-standard port) is a simple but effective way to enhance security and reduce the risk of automated attacks. Here's why:

Port 22 is the default port for SSH, so it’s frequently targeted by attackers using automated tools that scan servers for open port 22 to attempt brute-force login attempts. By changing the port to a non-standard one (2222,2234,2343... etc), you reduce the likelihood of such scans detecting and attacking your SSH service.

Now how it's done : 

```
sudo nano /etc/ssh/sshd_config
```
we're returning in the menu we saw before and there, we will change the port listening by our server, it's written :
```
#Port 22
```
So you will add one rule `Port 2222`

Save and quit and restart service ssh like we saw before.

it's time to introduce you something else before trying to connect to that port, UFW !

Ufw is what we call a firewall it allows or deny connection to certain ports, it's needed to go further ! Install Ufw on the remote server :

```
sudo apt install ufw
```
Now that it is installed here is some commands to use it : 

```
sudo ufw status
```
it will show you every rules about your ports you denied or allowed by ufw

```
sudo ufw reload
```
this one will reload ufw and apply your changes

```
sudo ufw enable(or disable)
```
i think i don't really need to explain you what it will do but .. ! we never know ! enable : will active ufw and disable : will make ufw unactive

and the last and the most important :

```
sudo ufw allow (anyport)
```
it will allow a port you choosing to.

Comingback to what we were doing before installing ufw. now we need to allow the service itself SSH and the port needed so here 2222 :

```
sudo ufw allow 2222
sudo ufw allow 'OpenSSH'
```
restart the service ssh with the command we saw before then try to connect to your server using the port we allowed, remember to quit your server to do it:

```
ssh -p 2222 user-name@server_adress_ip
```

well done your now connected to your server by the port 2222 !
If it didn't work let's read [Problems encountered and solutions](#Problems-encountered-and-solutions)

Now that we're connected to another port it gives a better protection BUT we need even more protection to our server, hackers are smart ! so we will now install Fail2ban ! but what it is ?

Fail2ban is a security tool that helps protect your server from brute-force attacks by monitoring log files and automatically banning IP addresses that show malicious behavior, like repeated failed login attempts.

Install Fail2ban :

```
sudo apt install fail2ban
```
now that it is installed you need to make it work for ssh service and precise what port to protect from brute forcing :
```
sudo nano /etc/fail2ban/jail.local
```
jail.local should be empty but you will add some rules ! 

```
[sshd]
enabled = true
port = 2222
```

save & quit and like every other application we used restart it : 

```
sudo systemctl restart fail2ban
```

Now your server is protected from brute force on that port, try it yourself and try to connect with a wrong password in a row.

You server is now ready and PROTECTED ! well done.

# Problems encountered and solutions

Here we will talk about the problem that can happen with the port 2222. i didn't say it before but Ionos do have a native firewall. and it can be in conflict with ufw so you'll need to precise the port 2222 in ionos firewall on the website to fix it ! it should work after that.

How and normaly you need to allow port for 80 and 443 those are http and https one but it seems that they are already native to the ionos firewall and nginx should work directly even without ufw rules.

# Theorical analysis

SSH encryption is crucial for securing data during remote access and communication over potentially insecure networks. Here's why SSH encryption is important for data security:
- SSH uses strong encryption algorithms (e.g., AES, RSA) to ensure that data transmitted between the client and the server is encrypted. This means that even if someone intercepts the communication, they cannot read or understand the data without the decryption key.
- SSH also uses public-key cryptography to authenticate the server, ensuring that the client is connecting to the legitimate server and not a malicious one (e.g., during a man-in-the-middle attack). This prevents users from unknowingly sending sensitive information to attackers.
- SSH enables secure file transfer through protocols like SCP and SFTP, ensuring that files sent over the network are encrypted, thus protecting them from unauthorized access or tampering.
- SSH encryption can also be combined with public key authentication, which is more secure than password-based authentication. This method uses cryptographic keys to authenticate users, making brute-force attacks much harder for attackers to execute.

Changing the default SSH port from 22 to a non-standard port (like 2222)and adding Fail2ban enhances security for the following reasons:

- Port 22 is the default port for SSH, making it a common target for attackers using automated tools to scan for open SSH ports. By changing the port to a less commonly used one, such as 2222, you reduce the likelihood that automated attacks will target your server, since many attackers focus on scanning common ports like 22.
- Fail2ban automatically bans IP addresses that have too many failed login attempts within a short period. This prevents attackers from guessing passwords through brute-force methods, significantly increasing your server's security.

## Observation with wireshark !

Wireshark is a powerful open-source network protocol analyzer that allows you to capture and inspect data packets in real time as they travel across a network. It is widely used for troubleshooting, analyzing, and learning about network protocols and security.

- You can capture live traffic and monitor it as it happens, which is useful for diagnosing issues like high latency, packet loss, or specific protocol failures.
- You can spot issues like packet loss, which results in retransmissions, by analyzing the TCP sequence numbers and acknowledgment numbers.
- You can see that SSH is a good protocol cause every datas identified with wireshark are unreadable
- Wireshark captures data at the packet level, giving you detailed visibility into each packet sent or received on a network. This allows for deep analysis of what’s happening in a network at a very granular level.

# Conclusion

Everystep from Port forwarding, installing Fail2ban, Allow and deny some ports by ufw and using SSH protocol will serve to protect your datas and integrity of your remote server. In world where hackers become even more unpredictable you need thousands of layers of protections to protect your datas.
Here it's only a childplay but imagine if we talk about datas center from a hospital, it could be a disaster that's why we need to learn to protect ourselves even more and it starts with a good password ! :)

# Resources

-[Wikipedia](#https://www.wikipedia.org/)
-[ChatGPT](#https://openai.com/chatgpt/) Warning ! : Use it as a big dictionary, it's really powerfull and you can learn even faster with it

