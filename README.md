# netcat

A short tutorial, on a very complicated and versatile tool. [Main Project Page](https://github.com/abarb014/nctutorial)

## Table of Contents

* [Background](#background)
* [Basic Usage](#basic-usage)
* [Flags](#flags)
* [Example Uses](#example-uses)
  - [Chat System](#chat-system)
  - [File Transfer](#file-transfer)
  - [Port Scanning](#port-scanning)
  - [Proxying and Port Forwarding](#proxying-and-port-forwarding)
* [Scripts](#scripts)

### Background

`netcat` is a service used to read a write from network connections, so it can be used for good, or it can be used for evil.`netcat` has been described as a "hacker" tool, 
 as well as a "swiss army knife". `netcat` can be explained by using another similar command called `telnet`. `telnet` does a lot of the same things as `netcat`, but it
 opens up a terminal-link prompt for the user to use. `netcat` does not, so you can automate tasks and make your life easier by using i/o redirection as well as piping
 with `netcat`.

 `netcat` and `telnet` both utilize an important part of the computer, and it is called a port. Ports in computer networking allow programs on the computer to share
 information with each other, or with someone else over the internet. Think of it like a bucket with two people on each side. One person puts something in the bucket, and
 the other person take the thing out. These explanations are vastly oversimplified, but they will do for our purposes. Another important note is port assignment. Many ports
 are registered for specific programs, so we should not use them for our tests. Luckily, most Linux kernels allow the ports in the range 32768 to 61000 for a wide variety of
 things.

### Basic Usage

To use the `netcat` command all you have to do is type `nc IPADDRESS PORT` at a terminal, where IPADDRESS is the IP you want to connect to, and PORT is the port number you
want to connect to. For most of this tutorial, we will connect to our own computers. The IP address of our own computer is `127.0.0.1` or more simply `localhost`. The port
can be anything in the range provided earlier (32760 - 61000). Some common ports are are 22 for file transfers (SSH), 80 for internet browsers (HTTP), 443 for secure internet
browsing (HTTP over SSL), and a fun one is 3724 for Xbox Live.

If you type `nc localhost 32981` you probably will not see anything in the output. This is because `netcat` is going to that port, checking if anything is there, and
returning. It will output what is on that port on the standard output. However, if you type `nc -l localhost 32981` it will appear as though your terminal has frozen. Let it 
go! It is fine. What you told `netcat` to do is to go to port 32981 and wait there until someone or somthing gives it some data. As soon as it recieves the data, `netcat`
will close. Now we can start looking at some optional features of `netcat` as well as some examples to illustrate them.

### Flags

As with any Linux program, `netcat` comes with many optional features you can use. Here, I will go over the flags you will need to know in the following examples. However,
 if you are feeling anxious to start, feel free to jump ahead to an example, and reference this part of the tutorial like a cookbook!

| Flag | Description |
|:---:|:---:|
| -l | Tells `netcat` to listen to a port, rather than try to connect to it. You cannot use this with the -p option. |
| -k | Forces `netcat` to stay open. This MUST be used with the -l option. |
| -z | Makes `netcat` scan for listeners. This cannot be used with the -l option. You must specify a port, or range of ports to scan. |
| -v | Gives `netcat` verbose (lots of big words) output. |
| -w | Specifies a timeout for `netcat`, but it has no purpose with the -l option. It must be immediately followed by a wait time in seconds. |

### Example Uses

#### Chat System

This is a VERY simple system, where you can talk to another terminal session open on your computer. What is the point of that you may ask? Well read on, and you just might
 learn how to do this with other computers on your network. However, I must tell you this is not encrypted. You are sending your mail unsealed through the internet, so
 anyone that wants to read it, and knows how, can do what they want.

 The first thing you will want to do is open up two terminal windows. One will act as the listener, and the other one will send the data. After the connection is
 established it will not matter who types; whatever is typed on the server will be sent to both computers.

 Set up one window as the listener:
 ```
 nc -l 32981 # Or any port number you prefer above 32768!
 ```
 What this does is it tells `netcat` to listen to port 32981 (Or whatever) and it will print what it recieves.

 On the other window, we will connect to the server:
 ```
 nc localhost 32981
 ```

 This window is now connected to the port, and you are free to transfer text between the two windows. You have a simple chat system! Yay! If you want to end the system, go
 ahead and just type CTRL-D on any window. This is cool and all, but why not try it with different computers? This requires one more step. You need to [find](http://lifehacker.com/5833108/how-to-find-your-local-and-external-ip-address) the local IP of the listening computer. Once you have it, go ahead and do the following commands.
 ```
 nc -l 32981 # This should be on the same computer as the two window experiment
 ```

 ```
 nc LISTENERS-LOCAL-IP 32981
 ```
 And voila! You have a super dangerous to use, but still cool chat system. 

#### File Transfer

This is another simple system that will show you how to transfer files with `netcat` as well as show you a little of piping with `netcat`. You will need two windows or two
computers like before. On one terminal, enter this:
```
cat file | nc -l 32981
```
Essentially, this tells `netcat` to listen on a given port and hold a file. Going back to the bucket analogy, one man takes a package with him and tosses it in the bucket
and waits until he sees someone else. Now the other window or computer will run this:
```
nc localhost 32981 > file # Where localhost can be the local IP address of the listener, if you are using two computers.
```
With this command, you connect to port 32981 on the listener computer, and you redirect what you find there into a file. A man shows up, takes what is in the bucket, and both
men leave. This command will close `netcat` on the listener because we did not specify to keep the connection open. This is a simple, but powerful example; you now know
that you can "load up" the server, and that you can redirect the output to files. Of course if we did not redirect the output, the contents of the file would have been
printed to standard output instead.

#### Port Scanning

Say you want to host your own website with your favorite web server. While you are getting everything set up, you run into an error! For some reason, your server cannot
start on port 80, the HTTP port. Instead of panicking or calling the cops, you probably should check to see if the port is in use by something else first.

Start by running this on a terminal:
```
nc -z localhost 80
```
This command checks to see if anything is listening on port 80, however the output can get a little tricky. First of all, the command will not print anything if there is
nothing listening on the port. This might confuse new users. If something IS listening, then you will get a message like the folowing:
```
Connection to localhost port 32981 [tcp/*] succeeded!
```

You can also use the -v flag to get better output. But you would know that if you read the flag section, right?

Port scanning also works on domain names! Here is an example:
```
abarb014@nctutorial$ nc -z -v -w 1 google.com 80
found 0 associations
found 1 connections:
     1: flags=82<CONNECTED,PREFERRED>
        outif en1
        src MY.IP.CENSORED port 52345
        dst 74.125.196.113 port 80
        rank info not available
        TCP aux info available

Connection to google.com port 80 [tcp/http] succeeded!
abarb014@nctutorial$ 
```
Notice here that I used two more flags. Their explanation will be in the flag section. From the output, you can see the source IP and port, as well as the destination IP and
port. I covered my IP and port for some security, but notice at the bottom it said the connection to google was a success! Yay! This is not as useful as checking your own
ports, but you might be able to check for some open ports on the computers that serve a website you do not particularly like. You can find out more about that on some shadier
websites, but as for this tutorial, we will be good little hackers.

Another important fact to note is you can specify a range of ports to check rather than just one!

#### Proxying and Port Forwarding

### Scripts
