### Syslog Server

------

#### Sources:

[Raspberry Pi as a Syslog server](https://eang.it/use-the-raspberry-pi-as-a-syslog-server-using-rsyslog/)

[Raspberry Pi System Logging and Loggly](http://blog.scphillips.com/posts/2015/05/raspberry-pi-system-logging-and-loggly/)

howtoforge [Setup Rsyslog Server on Ubuntu 18.04 LTS](https://www.howtoforge.com/how-to-setup-rsyslog-server-on-ubuntu-1804/)

howtoforge [Centralized Log Server with Rsyslog in Debian 9](https://www.howtoforge.com/tutorial/rsyslog-centralized-log-server-in-debian-9/)

------

#### Steps:

Linux raspberrypi 4.19.97-v7+ #1294 SMP Thu Jan 30 13:15:58 GMT 2020 armv7l

```
pi@raspberrypi:~ $ cat /etc/issue
Raspbian GNU/Linux 10 \n \l

pi@raspberrypi:~ $ cat /etc/debian_version
10.4
```

Using rsyslog

In order to verify if Rsyslog daemon is started on a system execute the below command, depending on your distribution version:

`systemctl status rsyslog.service`

make the following changes to the rsyslog.conf file

`sudo nano /etc/rsyslog.conf`

Allow UDP log message reception on 514 port. By default, the UDP port is used by syslog to send-receive messages.

Uncomment the following lines:

```
$ModLoad imudp
$UDPServerRun 514
```

If you scroll down towards the bottom of the file we can see that it references to use all ‘conf’ files under /etc/rsyslog.d/

Create/touch a new file calling it ’something.conf’. Append the file with the following lines of code:

```
pi@raspberrypi:/etc/rsyslog.d $ cat ubnt.conf
$template TmplAuth,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log"
*.*  ?TmplAuth
& stop
```
Note about newer versions of rsyslog
In recent versions of rsyslog, you may need to replace the deprecated ~ with stop, i.e. your configuration file will end with & stop instead of & ~.

We can also try this:

```
$template Incoming-logs,"/var/log/testing_docker/%PROGRAMNAME%.log"
if $programname startswith 'docker' then -?Incoming-logs
```

Useful filters:
```
$hostname
$programname
$msg
$syslogseverity
```
Operators:
```
== (equals)
contains
and
or
```

For Ubuntu, also?:

Change permissions of the /var/log/ directory so syslog user can make changes, create files etc.

Restart rsyslog with:

`sudo service rsyslog restart`

`sudo systemctl restart rsyslog`

To check which rsyslog sockets in listening state are opened on a Debian Linux system, you can execute the netstat command with root privileges. Pass the results via a filter utility, such as grep.

`sudo netstat –tulpn | grep rsyslog`

Rotating log files TBD