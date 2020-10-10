# Vulhub-firstileaks 1.3-walkthrough

## Tool:Vmware,nmap,nikto,nc

Set the network type of fristileaks as NAT, but I can't discover the target. After google, I notice that I have to set the mac address as 08:00:27:A5:A6:76 in the vmx file. Restart, it's ok:

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled.png)

## Information Gathering

### Port scan

Use nmap to do a full scan on the target. Use command:

```bash
 nmap -Pn -sS —stats-every 3m —max-restries 1 —max-scan-delay 20 —defeat-rst-ratelimit -T4 -p- [ip]
```

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%201.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%201.png)

Only one port open, do a detail scan on that port

```bash
nmap -nvv -sSV -p 80 --version-intensity 9 -A [ip]
```

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%202.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%202.png)

We can get some interesting clues on that. Try later.

### Nikto scan

Use nikto to scan the target

```bash
nikto -h [ip]
```

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%203.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%203.png)

So many information get. There is a /robots.txt path which contains 3 subpathes. 

use browser to check if there is any usefull information on each path. But nothing found except a hint “Keep Calm and Drink Fristi”. Given that the target has only one port 80 open. The only way to go through is find the right path. So just try ervery possible path. I tried /Calm /calm /Fristi /fristi. Finally I got the ritht path. that's fristi. 

## web attack

Browser this path, It's a login page.

When I face the login page, two methods occur in my head. sqli and weak credencial.

I tried some week password, but got nothing.

Try sqli:

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%204.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%204.png)

It's seemd there is no sqli.

Let's look for the source code of this page. I found some interesting code.

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%205.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%205.png)

It is obviously the base64 encoded string, so copy and decode the string

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%206.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%206.png)

Ok I got a string, usually there are two possibilities a path or a password

It’s a little long for a password, So I try as path first.

But got nothing

Try as a password, but what the usernname is?

tried some common usernames, failed. There must something missed. After a search, I found that in the source code, I found a name

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%207.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%207.png)

Have a try on that, logined.

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%208.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%208.png)

Upload a php shell to the server. But there is a filter to prevent upload php file.After tried many ways like Php, php.jpg, etc. I found the php.png works.

upload success. 

use nc listening on the local 444 port.

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%209.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%209.png)

Browse the file I uploaded.

got the shell

## privilege escape

uname -a check the linux version. found  it is vulnerable to the **dirtycow.** 

there are two exp in exploit-db about dirtycow, try the first [https://www.exploit-db.com/exploits/25444](https://www.exploit-db.com/exploits/25444)

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%2010.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%2010.png)

failed.

Try anther one,[https://www.exploit-db.com/exploits/40839](https://www.exploit-db.com/exploits/40839)

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%2011.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%2011.png)

change to the user we add:

![Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%2012.png](Vulhub-firstileaks%201%203-walkthrough%204305235d85ba4804ac56d51373b28db4/Untitled%2012.png)