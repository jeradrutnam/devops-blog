---
layout: post
title: "MySQL Multi Master replication with Galera on ubuntu"
description: "MySQL/Galera is synchronous multi-master cluster for MySQL/InnoDB database. The Galere replicate all the data accross the whole cluster using mysqldump and rsync"
categories: [High]
image:
  thumb: "images/cover-small2.png"
  title: "images/covre2.jpg"
tags: [Galera,High]
---

<section id="table-of-contents" class="toc">
  <header>
    <h3 class="delta">Galera Contents</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section>

##Galera Introduction `2 Node`
MySQL/Galera is synchronous multi-master cluster for MySQL/InnoDB database. The application layer engage with the mysql instanece commit the transaction into Galera and which will replicate all the data accross the whole cluster using `mysqldump and rsync`. A commit is called an RBS event.



<figure class="half">
	<img src="/images/galera/galera-cluster.png">
	<figcaption>Diagram of galera</figcaption>
</figure>


 {% highlight bash %}

        Clients
         |   |  |
        V  V  V
  ,----------------.
  |                    |   
  |   application |<-- MySQL server
  |                    |
  ================== <-- wsrep API
  | wsrep provider | <-- Galera
  `----------------'
            |
           V
Replication to other nodes

{% endhighlight %}


##How does it work?


 * Application writes on any node
 * The commit is replicated through the other nodes. Each transaction had an ID
 * True parallel replication, on row level and ID check

**Most relevant `features` on Galera Cluster:**

* Synchronous replication
* Easy to scale
* Automatic node joining
* Automatic membership control, failed nodes drop from the cluster
* Active-active multi-master topology
* No SPOF
* Performance oriented

##Master-master replication

{% highlight bash %}
    ,-------------.
   | application|
    `-------------'
        | | |        DB backups
       ,-------.    ,-------.     ,-------.
      |node1|  | node2|   | node3|
      `-------'    `-------'     `-------'
       <===== cluster nodes replication =====>
{% endhighlight %}

Local name resolution:

{% highlight bash linenos=table %}
127.0.0.1   localhost
127.0.0.1   galera-node01
127.0.1.1   galera-node01
192.168.1.1     galera-node01
192.168.1.2     galera-node02
{% endhighlight %}

NIC parameters: Do this on Both servers with the relavant ip.

{% highlight ruby  linenos=table %}
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
address 192.168.1.1
netmask 255.0.0.0
{% endhighlight %}


>Install `Codership patched MySQL version` & `Galera replicator`  
`MySQL`: [https://launchpad.net/codership-mysql/+download](https://launchpad.net/codership-mysql/+download)

`Galera replicator` : [https://launchpad.net/galera/+download](https://launchpad.net/galera/+download)

**`WARNING:` do those manipulations on both nodes**

>Download the `.deb`

{% highlight bash  linenos=table%}
galera-node01:~$https://launchpad.net/codership-mysql/5.5/5.5.29-23.7.3/+download/mysql-server-wsrep-5.5.29-23.7.3-amd64.deb
galera-node01:~$https://launchpad.net/galera/2.x/23.2.4/+download/galera-23.2.4-amd64.deb
galera-node01:~$ls
 mysql-server-wsrep-5.5.29-23.7.3-amd64.deb  galera-23.2.4-amd64.deb
{% endhighlight %}

>Install them
{% highlight bash  linenos=table%}
galera-node01:~$sudo dpkg -i  mysql-server-wsrep-5.5.29-23.7.3-amd64.deb  galera-23.2.4-amd64.deb
{% endhighlight %}

>Fix the dependency issue:
galera-node01:~$ sudo apt-get -f install -y

##MySQL Secure configuration

{% highlight ruby %}
galera-node01:~$ sudo mysqld_safe &
galera-node01:~$ sudo mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MySQL to secure it, we'll need the current
password for the root user.  If you've just installed MySQL, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MySQL
root user without the proper authorisation.

Set root password? [Y/n] Y
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!

By default, a MySQL installation has an anonymous user, allowing anyone
to log into MySQL without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] Y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] Y
 ... Success!

By default, MySQL comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] Y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] Y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MySQL
installation should now be secure.

Thanks for using MySQL!
{% endhighlight %}

>Modify the bind-address parameter with the host-only NIC (eth0):

alera-node01:~$ sudo sed -i s/'127.0.0.1'/'10.0.0.1'/ /etc/mysql/my.cnf

 **The sed command simply does this on the node 01:**

` Do this on the second node.`

##Galera configuration

We configure the Galera replicator in **/etc/mysq/conf.d/wsrep.cnf** with:


wsrep_provider=/usr/lib/galera/libgalera_smm.so
wsrep_cluster_address="gcomm://"
wsrep_sst_method=rsync

>On the second node:

wsrep_provider=/usr/lib/galera/libgalera_smm.so
wsrep_cluster_address="gcomm://10.0.0.1"
wsrep_sst_method=rsync

>We kill the temporary process and relaunch MySQL from the INIT script:

galera-node01:~$ sudo killall mysqld_safe
galera-node01:~$ sudo service mysql restart
 * Stopping MySQL database server mysqld
   ...done.
 * Starting MySQL database server mysqld
   ...done.

>Enter the MySQL shell, check the MySQL and wsrep version:

????

>The most important value is:

`wsrep_ready | ON`

If wsrep_ready is ON, it works! You also have to check the wsrep_cluster_size parameter. If wsrep_ready is ON and wsrep_cluster_size is egal to number of the nodes, then your cluster is properly working. Sometimes wsrep_ready is ON and your wsrep_cluster_size is egal to 1. This situation is pretty bad because you obviously are in a split brain situation. To solve this verify your gcomm:// urls and restart MySQL. I wrote a simple bash script to check this:

The simpliest way to avoid any split-brain situation is to setup the garbd daemon like so. Ideally the daemon will be running on an extra server, a load-balancer for example:

$ garbd -a gcomm://10.0.0.1:4567 -g my_wsrep_cluster -l /tmp/1.out -d

Simply choose one of your 2 nodes and garbd will do the rest. It will automatically connect to the second node and will be a member of the cluster. Thus your wsrep_cluster_size variable should be 3. If the dedicated link between the 2 nodes goes down, garbd will act as a replication relay, this can be really useful.


{% highlight ruby %}
mysql> SHOW STATUS LIKE 'wsrep_cluster_size' ;"
+-------------------------+-------+
| Variable_name       | Value |
+------------------------+-------+
| wsrep_cluster_size|   3     |
+-------------------------+-------+
{% endhighlight %}

>This script will perform a simple check of your galera state:


{% highlight ruby %}
#!/bin/bash
echo "Enter your MySQL user"
read MYSQL_USER

echo "Enter your MySQL password"
stty -echo
read MYSQL_PASSWD
stty echo

STATUS=$(mysql -u$MYSQL_USER -p$MYSQL_PASSWD -N -s -e "show status like 'wsrep_ready';" | awk '{print $2}')
SIZE=$(mysql -u$MYSQL_USER -p$MYSQL_PASSWD -N -s -e "show status like 'wsrep_cluster_size' ;" | awk '{print $2}' | sed -n '2p')

if [[ ${STATUS} = "ON" ]] ; then
  if [[ ${SIZE} -lt 2 ]] ; then
          echo "Split-brain!"
  else
      echo "Galera is perfectly working"
  fi
else
        echo "The replication is NOT working"
fi
{% endhighlight %}


>The connection is well established between the nodes:

galera-node01:~$ sudo netstat -plantu | grep mysqld | grep ESTABLISHED
tcp    0   0 10.0.0.1:4567     10.0.0.2:43370     ESTABLISHED 15082/mysqld
tcp    0   0 10.0.0.1:39691    10.0.0.10:4567     ESTABLISHED 13929/mysqld

>And garbd also:
$ sudo netstat -plantu | grep garb
tcp        0      0 0.0.0.0:4567            0.0.0.0:*               LISTEN      7026/garbd
tcp        0      0 10.0.0.10:4567        10.0.0.1:39691        ESTABLISHED 7026/garbd
tcp        0      0 10.0.0.10:4567        10.0.0.1:45749        ESTABLISHED 7026/garbd

**Here the 10.0.0.10 address is the address of the third machine, the one which host the garbd daemon. Check the replication by creating databases, tables and fields :).**

Important note: when your 2 node replication is setup, change the address of the node with this gcomm:// address by gcomm://other_node_address. You only have to use this address during the installation process. In our setup, it’s the node 01.

>At the end you should have this:

*`node 01`: gcomm://10.0.0.2
*`node 02`: gcomm://10.0.0.1

If the node 01 goes down, the second node will continue to perform commits, when the node 01 will get back to life it will automatically synchronise with the node 02 and vice et versa. A kind of ‘cross replication’.

##Add a joiner node

<figure class="half">
	<img src="/images/galera/add-node.png">
	<figcaption>Diagram of galera</figcaption>
</figure>

When you add a new node, the new one is called `joiner` and the node which replicate is called `donnor`. To add a new node you have to:

* Install the codership MySQL version
* Install Galera
* Configure the gcomm:// address with the donnor IP address

After that:

*    The new node will request the donnor using a SST request.
*   The state of the requested node will change from JOINED to DONOR
*   The donnor, will perform a mysqldump or a rsync sync. It depends on the SST methods in use. The SST method also implies several things. For instance both methods mysqldump and rsync are blocking option. It means that during the operation the tables will be lock on the DONOR node and only on this node, the other nodes of the cluster will be available on write. The donor will have a READ-ONLY state until the end of the process.
*    The donnor will load this dump on the new node.

## SSL replication

Since the 0.8 version Galera provides the SSL replication, it prevents man-in-the-middle attacks and also a powerful authentication system based on certificates. It’s a really convenient way to allow specific nodes to connect the cluster. For this purpose we have to active those options:

{%  highlight ruby %}
mysql> SHOW VARIABLES LIKE 'wsrep_provider_options' \G;
*************************** 1. ROW ***************************
{% endhighlight %}

>Generate your certs and copy them to the others nodes:


{%  highlight bash %}
galera-node01:~/cert$ openssl req -new -x509 -days 365000 -nodes -keyout key.pem -out cert.pem
Generating a 1024 bit RSA private key
.......................++++++
.................++++++
writing new private key to 'key.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (eg, YOUR name) []:
Email Address []:

galera-node01:~/cert$ scp *pem ubuntu@galera-node02:/home/ubuntu
ubuntu@192.168.146.160's password:
cert.pem
key.pem

galera-node01:~/cert$ scp *pem ubuntu@galera-node03:/home/ubuntu
ubuntu@192.168.146.160's password:
cert.pem
key.pem

{% endhighlight %}

>We also modify the /etc/mysql/conf.d/wsrep.cnf file and enable the wsrep_provider_optionsparameter:

**`wsrep_provider_options="socket.ssl_cert = <chemin_cert_file>; socket.ssl_key = <chemin_key_file>"`**

Be careful, here we have to restart from the beginning. Why? If we configure SSL, it means we have to do it on each server. Thereby we will modify each configuration file for each server. What will happened?


* Modify the configuration for SSL on the node 01
* Relaunch MySQL
* The node 01 try to connect to the other using SSL
* Oh snap! Doesn’t work :(
* Each serve will go down, one by one

>The only way is to start like this:

{%  highlight bash %}
$ sudo mysqld --wsrep_cluster_address=gcomm:// --wsrep_sst_method=rsync --wsrep_provider_options="socket.ssl_cert = <chemin_cert_file>; socket.ssl_key = <chemin_key_file>" &
{% endhighlight %}

>Un-allowed node will end up with this log message:

[ERROR] WSREP: handshake failed for 0xa9660b8: 1

>We re-create the cluster! Now launch all the node one by one. During the connection, here some trace about ssl negociation:

120312 16:08:07 [Note] WSREP: gcomm: connecting to group 'my_wsrep_cluster', peer '10.0.0.1:'
120312 16:08:07 [Note] WSREP: SSL handshake successful, remote endpoint ssl://10.0.0.1:4567 local endpoint ssl://10.0.0.2:41636 cipher: AES128-SHA compression: zlib compression

>We can also check that in MySQL, this is not the real output, I just kept the lines which make sense:

$ mysql -uroot -proot -e "SHOW VARIABLES LIKE 'wsrep_provider_options';" -E
Value: socket.ssl = YES; socket.ssl_ca = /home/ubuntu/cert/cert.pem;
socket.ssl_cert = /home/ubuntu/cert/cert.pem
; socket.ssl_cipher = AES128-SHA; socket.ssl_compression = YES
; socket.ssl_key = /home/ubuntu/cert/key.pem

>Actives connections between all nodes:

ubuntu@galera-node01:~$ sudo netstat -plantu | grep 10.0
tcp        0      0 10.0.0.1:4567           10.0.0.3:32820          ESTABLISHED 5783/mysqld
tcp        0      0 10.0.0.1:4567           10.0.0.2:41640          ESTABLISHED 5783/mysqld

##Conclusion

How galera prevents `split-brain`

Split-brain is a state in which all the nodes of a cluster cannot determinate there membership. If a node is down for a couple of second due to a network issue, an other may think that this one is down and want to take the lead and launch the service. Each node in the cluster may mistakenly decide to run the service but the others nodes are still running. This will cause data corruption and duplicate instance. The solution offered by Galera is to use a little daemon called garbd, a stateless daemon create to avoid split-brain. This daemon is a part of the Galera replicator. As I previously said, the garbd daemon will be a member of the cluster and can act as a replication relay if the dedicated link between the nodes goes down.



{%  highlight bash %}
                    ,---------.
                   | garbd  |
                    `---------'
          ,---------.  |     ,---------.
         | clients |  |     | clients |
         `---------'   |     `---------'
                    \    |     /
                     \ ,---. /
                     ('     `)
                    ( WAN )
                     (.      ,)
                     / `---' \
                    /          \
         ,---------.           ,---------.
        | node1 |           | node2 |
         `---------'           `---------'
        Data Center 1         Data Center 2
{%  endhighlight %}

>What happens if a node goes down?

No matter your topology (2 or more node). For example, if the node 02 goes down, the one with the gcomm://another_node_ipaddress, the data will keep be replicate to the other node. When the node 02 will go back, the data will be commited.

>Change your gcomm:// address without downtime

{%  highlight bash %}
mysql> SHOW VARIABLES LIKE 'wsrep_cluster_address';
+-----------------------+------------------+
| Variable_name         | VALUE            |
+-----------------------+------------------+
| wsrep_cluster_address | gcomm://10.0.0.3 |
+-----------------------+------------------+
1 ROW IN SET (0.00 sec)

mysql> SET GLOBAL wsrep_cluster_address='gcomm://10.0.0.2';
Query OK, 0 ROWS affected (3.51 sec)

mysql> SHOW VARIABLES LIKE 'wsrep_cluster_address';
+-----------------------+------------------+
| Variable_name         | VALUE            |
+-----------------------+------------------+
| wsrep_cluster_address | gcomm://10.0.0.2 |
+-----------------------+------------------+
1 ROW IN SET (0.00 sec)
{%  endhighlight %}

>Check your version

{%  highlight bash %}
mysql> SHOW GLOBAL VARIABLES LIKE 'version';
+---------------+--------+
| Variable_name | VALUE  |
+---------------+--------+
| version       | 5.5.20 |
+---------------+--------+
1 ROW IN SET (0.00 sec)

{%  endhighlight %}

>Check your wsrep version:


{%  highlight bash %}
mysql> SHOW GLOBAL STATUS LIKE 'wsrep_provider_version';
+------------------------+--------------+
| Variable_name          | VALUE        |
+------------------------+--------------+
| wsrep_provider_version | 23.2.0(r120) |
+------------------------+--------------+
1 ROW IN SET (0.00 sec)

{%  endhighlight %}

>For listing the whole options simply use:

**`mysql> SHOW VARIABLES LIKE 'wsrep%' ;" -E`** on mysql terminal.


In my next article, I will setup HAProxy on top of this cluster. HAProxy will provide load-balancing and a virtual IP address.
