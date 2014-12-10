# netcat

A short tutorial, on a very complicated and versatile tool.

## Table of Contents

1. Background
2. Capture the flag!
3. Basic Uses

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

### Capture the flag!

As with any Linux program, `netcat` comes with many optional features you can use. Here, I will go over the flags you will need to know in the following examples. However,
 if you are feeling anxious to start, feel free to jump ahead to an example, and reference this part of the tutorial like a cookbook!

| Flag | Description |
|:---:|:---:|
| -l | Tells `netcat` to listen to a port, rather than try to connect to it. |

### Basic Uses

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
