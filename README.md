DaemonTools is an excellent piece of software created by Daniel J. Bernstein, and composed of several tools used to launch and monitor processes in a unix environment. They are fast, reliable, and are my favourites whenever I get to wear the devops hat to setup a new server. Sadly, there's no default package available for distros like Amazon Linux or CentOS, so here are the steps involved for setting them up manually.

Service Example: Using daemontools to start and monitor a Jenkins server in Linux
Let's say we want Jenkins to be started and monitored by daemontools. We would create a the following files and directories in a temporary directory (let's say /tmp):

/tmp/jenkins/log/run (mode 0700)
/tmp/jenkins/env/JAVA_HOME (mode 0600)
/tmp/jenkins/run (mode 0700)

The env/JAVA_HOME file would contain the path to where the JVM is installed:

/usr/java
The log/run file could look like:
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/command
exec 2>&1
mkdir -p /home/jenkins/log
chown -R jenkins:jenkins /home/jenkins/log

exec envuidgid jenkins multilog t s10485760 n5 '!tai64nlocal' /home/jenkins/log
And the run file would look like:

#!/bin/bash
exec 2>&1
exec setuidgid jenkins envdir /etc/service/jenkins/env /usr/java/bin/java -Xmx2048m  -XX:MaxPermSize=2048m -Dhudson.plugins.git.GitSCM.verbose="true" -jar /home/jenkins/jenkins.war --httpPort=4433 --httpsPort=-1 --httpListenAddress=127.0.0.1 --logfile=/home/jenkins/log/jenkins.log

Once we create all the files, we can just mv /tmp/jenkins /etc/service and Jenkins will be started instantaneously by daemontools without further delay.
