---
created: 2024-05-30 16:17
author: Tomasz Olejarczuk
title: Ubuntu check ports
date: 30-05-2024
subtitle: null
titlepage: true
titlepage-text-color: 2a2f31
titlepage-rule-color: 2a2f31
titlepage-background: null
titlepage-rule-height: null
page-background: Bins/Pandoc-extras/backgrounds/background9.pdf
titlepage-logo: null
logo-width: null
toc: true
toc-own-page: true
header-left: \hspace{1cm}
header-center: \leftmark
header-right: Page \thepage
footer-left: \thetitle
footer-center: null
footer-right: \theauthor
tags: null
updated: 2024-05-30T16:17
modified: Thursday, 30th May 2024, 16:17:26
---

# Ubuntu check ports

In Ubuntu, you can use the following methods to see which ports are being used by which applications:

**1. Using the `netstat` command:**

* This command displays network connections, routing tables, interface statistics, and more.
* To list all open ports and the processes using them, run the following command in your terminal:

````bash
sudo netstat -tulpn
````

* In this command:
  
  * `-t` shows TCP connections
  * `-u` shows UDP connections
  * `-l` shows only listening sockets
  * `-p` shows the process ID and name
  * `-n` shows numerical addresses instead of resolving hostnames
* To filter for a specific port, use `grep`. For example, to see which process is using port 80, run:

````bash
sudo netstat -tulpn | grep :80
````

**2. Using the `lsof` command:**

* This command lists open files and the processes that have opened them.
* To see which processes are using which network ports, run:

````bash
sudo lsof -i -P -n
````

* In this command:
  
  * `-i` shows network files
  * `-P` shows the port number instead of the service name
  * `-n` shows numerical addresses instead of resolving hostnames
* To filter for a specific port, use `grep`. For example, to see which process is using port 80, run:

````bash
sudo lsof -i -P -n | grep :80
````

**3. Using the `ss` command:**

* This is a newer tool that can replace `netstat`. It offers similar functionality but may be faster and provide more detailed information.
* To list all open ports and the processes using them, run:

````bash
sudo ss -tulpn
````

* The options are the same as with `netstat`.

**Example output:**

You will see a list similar to this:

````
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      1168/mysqld
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1053/sshd
````

This output shows that the `mysqld` process (MySQL database server) is listening on port 3306 and the `sshd` process (SSH daemon) is listening on port 22.

I hope this helps! Let me know if you have any other questions.

---

Last Modified: `=dateformat(this.file.mtime, "DDDD, HH:mm")`

#### Tags:

`=this.file.tags`

````dataview
List FROM #kubernetes
````
