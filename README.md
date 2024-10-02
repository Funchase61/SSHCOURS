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

That's everything you need to know about SCP. now time to see SFTP !

Connect to your remote server using SFTP : 

```
sftp user-name@server_adress_ip
```
now you can freely navigate into your directories with `cd` and  `ls` on your remote server, when you are in the right directory where you want to send your file transfer it with this command :

```
put file.txt
```

and Voila ! now you have two file.txt in `/home/username/`
you'll see that sftp is a more versatile way to transfer files and directories





## 4. Creating a SSH pipeline and redirecting ports
## 5. Securing SHH server


# Problems encountered and solutions

# Theorical analysis

# Conclusion

# Resources
