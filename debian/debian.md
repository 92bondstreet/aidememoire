# SERVEUR DEBIAN

*Configuration d'un serveur debian*

## <a name='sommaire'>Sommaire</a>

  1. [Première connexion](#start)
  1. [Protection initiale](#first_protec)
  1. [Heure du réseau](#hour)
  1. [Installation de package utile](#useful)
  1. [Suppression des services inutiles](#unuseful)
  1. [Protection](#protection)
  	1. [Firewall](#firewall)
  	1. [Scan de ports](#portsentry)
  	1. [Authentifications répétées](#fail2ban)
  	1. [Backdoors](#rkhunter)
  1. [Lecture de Logs](#logs)
  1. [Serveur FTP](#ftp)
  
  
## <a name='start'>Première connexion</a>

- **Windows**

On utilise PuttyTray pour la connexion SSH : <a href='https://puttytray.goeswhere.com'>https://puttytray.goeswhere.com</a>

On crée une connexion dite de rescue sur le port 22 (par défaut avant de le modifier)

```
1. Menu Session 
	nom-rescue 
	adresse IP + port 22 
2. Menu Connection 
	type SSH 
3. Menu Auto-login 
	newuser/password
```

- **Mac**

On utilise la commande ssh pour se connecter en SSH

```
ssh nom_utilisateur@adresse_ip -p numero_de_port
```

- **Mise à jour du systeme**

```
apt-get update
apt-get upgrade
```  	

**[[⬆]](#sommaire)**
  	
  	
## <a name='first_protec'>Protection initiale avant toute chose</a>

- **Modifier le mot de passe root**

```
passwd root
```

- **Installer un editeur** : vi ou nano

- **Configuration SSH**

Mofification du fichier ***/etc/ssh/sshd_config***

```
netstat -a pour vérifier si le port n'est pas pris
	
	Port 1234                  # Changer le port par défaut
	PermitRootLogin no         # Ne pas permettre de login en root
	Protocol 2                 # Protocole v2
	AllowUsers newuser         # N'autoriser qu'un utilisateur
	
Redemarrer /etc/init.d/ssh restart
```

- **windows**

On crée avec PuttyTray la session de connexion sans sauvergarder le password.

```
1. Menu Session 
	nom 
	adresse IP + port 1234 
2. Menu Connection 
	type SSH 
3. Menu Auto-login 
	newuser
```

- **On Teste les connections suivantes**

```	
	root + port 22
	user + port 22 
	root + port 1234
	newuser + port 1234
```

**[[⬆]](#sommaire)**


## <a name='hour'>Heure du réseau</a>

- **Afficher la date et l'heure du système**

```
date
```

- **Installer ntp**

```
apt-get install ntp

Ensuite on indique un serveur ndp : http://www.pool.ntp.org/
Pour la France : http://www.pool.ntp.org/zone/fr

nano /etc/ntp.conf 
	server 0.fr.pool.ntp.org
	server 1.fr.pool.ntp.org
	server 2.fr.pool.ntp.org
	server 3.fr.pool.ntp.org
/etc/init.d/ntp restart

date
```

**[[⬆]](#sommaire)**



## <a name='useful'>Liste de package utile à installer</a>

```
apt-get install p7zip
```

**[[⬆]](#sommaire)**

## <a name='unuseful'>Suppression des services inutiles</a>

```
/etc/init.d/portmap stop
update-rc.d -f portmap remove
apt-get remove portmap

/etc/init.d/nfs-common stop
update-rc.d -f nfs-common remove

update-rc.d -f inetd remove

apt-get remove ppp
```

**[[⬆]](#sommaire)**

## <a name='protection'>Protection</a>

### <a name='firewall'>Firewall : on filtre le trafic</a>

- **edition**

```
nano /etc/init.d/firewall
```

- **modification**

```
	#!/bin/sh

	# Vider les tables actuelles
	iptables -t filter -F

	# Vider les règles personnelles
	iptables -t filter -X

	# Interdire toute connexion entrante et sortante
	iptables -t filter -P INPUT DROP
	iptables -t filter -P FORWARD DROP
	iptables -t filter -P OUTPUT DROP

	# ---

	# Ne pas casser les connexions etablies
	iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
	iptables -A OUTPUT -m state --state RELATED,ESTABLISHED -j ACCEPT

	# Autoriser loopback
	iptables -t filter -A INPUT -i lo -j ACCEPT
	iptables -t filter -A OUTPUT -o lo -j ACCEPT

	# ICMP (Ping)
	iptables -t filter -A INPUT -p icmp -j ACCEPT
	iptables -t filter -A OUTPUT -p icmp -j ACCEPT

	# ---

	# SSH In
	iptables -t filter -A INPUT -p tcp --dport numero_du_port -j ACCEPT

	# SSH Out
	iptables -t filter -A OUTPUT -p tcp --dport numero_du_port -j ACCEPT

	# DNS In/Out
	iptables -t filter -A OUTPUT -p tcp --dport 53 -j ACCEPT
	iptables -t filter -A OUTPUT -p udp --dport 53 -j ACCEPT
	iptables -t filter -A INPUT -p tcp --dport 53 -j ACCEPT
	iptables -t filter -A INPUT -p udp --dport 53 -j ACCEPT

	# NTP Out
	iptables -t filter -A OUTPUT -p udp --dport 123 -j ACCEPT

	# HTTP + HTTPS Out
	iptables -t filter -A OUTPUT -p tcp --dport 80 -j ACCEPT
	iptables -t filter -A OUTPUT -p tcp --dport 443 -j ACCEPT
	iptables -t filter -A OUTPUT -p tcp --dport autre_port -j ACCEPT
	iptables -t filter -A OUTPUT -p tcp --dport encore_autre_port -j ACCEPT

	# HTTP + HTTPS In
	iptables -t filter -A INPUT -p tcp --dport 80 -j ACCEPT
	iptables -t filter -A INPUT -p tcp --dport 443 -j ACCEPT
	iptables -t filter -A INPUT -p tcp --dport autre_port -j ACCEPT
	iptables -t filter -A INPUT -p tcp --dport encore_autre_port -j ACCEPT
	iptables -t filter -A INPUT -p tcp --dport 8443 -j ACCEPT

	# FTP Out
	iptables -t filter -A OUTPUT -p tcp --dport 20:21 -j ACCEPT

	# FTP In
	modprobe ip_conntrack_ftp # ligne facultative avec les serveurs OVH
	iptables -t filter -A INPUT -p tcp --dport 20:21 -j ACCEPT
	iptables -t filter -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

	# Autorise le PING 
	iptables -t filter -A INPUT -p icmp -j ACCEPT 
	iptables -t filter -A OUTPUT -p icmp -j ACCEPT
```

- **redémarrage**

```
Excution du fichier
chmod +x /etc/init.d/firewall

Scripts de démarrage
update-rc.d firewall defaults
```

### <a name='portsentry'>Scan de ports</a>

- **Installation**

```
apt-get install portsentry
```

- **Configuration des adresses IP safe**

```
nano /etc/portsentry/portsentry.ignore.static
	
	# Vous même
	127.0.0.1/32
	88.191.164.206
```

- **Configuration**

```
nano /etc/portsentry/portsentry.conf

		# These port bindings are *ignored* for Advanced Stealth Scan Detection Mode.
		# Use these if you just want to be aware:
		TCP_PORTS="1,11,15,79,111,119,143,540,635,1080,1524,2000,5742,6667,12345,12346,20034,27665,31337,32771,32772,32773,32774,40421,49724,54320"
		UDP_PORTS="1,7,9,69,161,162,513,635,640,641,700,37444,34555,31335,32770,32771,32772,32773,32774,31337,54321"
		ADVANCED_PORTS_TCP="1024"
		ADVANCED_PORTS_UDP="1024"
		# By specifying ports here PortSentry will simply not respond to
		# incoming requests, in effect PortSentry treats them as if they are
		# actual bound daemons. The default ports are ones reported as
		# problematic false alarms and should probably be left alone for
		# all but the most isolated systems/networks.
		ADVANCED_EXCLUDE_TCP="113,139"
		ADVANCED_EXCLUDE_UDP="520,138,137,67"
		# This file is made from /etc/portsentry/portsentry.ignore.static
		IGNORE_FILE="/etc/portsentry/portsentry.ignore"
		HISTORY_FILE="/var/lib/portsentry/portsentry.history"
		BLOCKED_FILE="/var/lib/portsentry/portsentry.blocked"
		RESOLVE_HOST = "0"
		BLOCK_UDP="1"
		BLOCK_TCP="1"
		KILL_ROUTE="/sbin/route add -host $TARGET$ reject"
		KILL_ROUTE="/sbin/iptables -I INPUT -s $TARGET$ -j DROP && /sbin/iptables -I INPUT -s $TARGET$ -m limit --limit 3/minute --limit-burst 5 -j LOG --log-level DEBUG --log-prefix 'Portsentry: dropping: '"
		#KILL_HOSTS_DENY="ALL: $TARGET$ : DENY"
		SCAN_TRIGGER="0"
		#PORT_BANNER="** UNAUTHORIZED ACCESS PROHIBITED *** YOUR CONNECTION ATTEMPT HAS BEEN LOGGED. GO AWAY."
```

- **On relance**

```
/etc/init.d/portsentry restart
```

- **Crédits**

```
https://www.isalo.org/wiki.debian-fr/Portsentry
http://monblog.system-linux.net/blog/2011/05/08/securisation-dune-machine-avec-portsentry-et-fail2ban-plus-libapache2-mod-evasive/
```


**[[⬆]](#sommaire)**



