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

> At that moment you should have created a server on ionos, it can possibly be done with another provider or your own vps

First, we'll try to connect on the `root`[^1] of the server on a terminal.
You will need the ip adress of your server and the password it was given/chosen.
Now proceed like that :
[^1]: Mot anglais pour définir la "racine" donc le point de départ de l'architecture meme des données dans un environnement informatique. ici root représente une entité avec des privilège absolue
```
ssh root@server_adress_ip
```
Then enter your password to acces it. Wonderfull ! your now connected as `root`[^1] in your server !
![It should appear like that :](image.jpg)

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
it will ask you a new password then some informations about you. that's not mandatory to fill everything, you can just press enter until your account is created.



# Manipulations list

# Problems encountered and solutions

# Theorical analysis

# Conclusion

# Resources
