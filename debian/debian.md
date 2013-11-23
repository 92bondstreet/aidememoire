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

