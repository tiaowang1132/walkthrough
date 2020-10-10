# pwnlab-walkthrough

## Tool:Vmware,nmap,nikto,nc

Use nat type and discover the host by:

```bash
discover -r 
```

## Information Gathering

### Port scan

Use nmap to do a full scan on the target. Use command:

```bash
 nmap -Pn -sS —stats-every 3m —max-restries 1 —max-scan-delay 20 —defeat-rst-ratelimit -T4 -p- [ip]
```

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled.png)

Do a detail scan on those ports

```bash
nmap -nvv -sSV -p 80,111,3306,48637 --version-intensity 9 -A [ip]
```

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%201.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%201.png)

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%202.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%202.png)

### Nikto scan

Use nikto to scan the target

```bash
nikto -h [ip]
```

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%203.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%203.png)

There is a path seems interesting /images/?pattern=/etc/*. Try LFI. failed.

Found a login page

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%204.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%204.png)

Use the LFI cheatsheet

> [https://highon.coffee/blog/lfi-cheat-sheet/](https://highon.coffee/blog/lfi-cheat-sheet/)

Try php://filter/convert.base64-encode/resource= on the path 'index', 'config', 'upload','login'

found a code which shows there is a LFI vulnnerability.

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%205.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%205.png)

in the config file  there is a credential for mysql.

connect the mysql. and get this

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%206.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%206.png)

Decode the first pass by base64. use to login

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%207.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%207.png)

Well, it is a upload page.

upload a php shell to the server, but found there is a filter.

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%208.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%208.png)

see the upload file through the LFI.

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%209.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%209.png)

We can see from the code that there are some rules to prevent upload shell.

Use burpsuite to modify the request.(change the extension, Type and file header)

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2010.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2010.png)

Listen the local port by nc. Try to browser the path we got from the response. but fail to get shell.

There must something missed. After some time to search.

found the rule in index file.(also read by LFI)

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2011.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2011.png)

It shows that we should access the shell by add cookie. Use burpsuite modify the request like this:

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2012.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2012.png)

got shell

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2013.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2013.png)

## privilege escape

Try to find the suid command

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2014.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2014.png)

Nothing noticeable.

change to user kane (3 of the user we get from mysql),check what can I get from /home 

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2015.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2015.png)

there is a file "msgmike" can be execute by anyone, so run it.

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2016.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2016.png)

It seems that run the cat command. So try to bind "cat" to "sh"(don't forget to change the PATH)

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2017.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2017.png)

change to mike. Also get in the mike directory under /home . there is a file msg2root.

That must be the way to root.

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2018.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2018.png)

It seems to run the command through message. so try to do like this:

![pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2019.png](pwnlab-walkthrough%205d2d3a73837b47bb92872616fcf498ed/Untitled%2019.png)

Get root.