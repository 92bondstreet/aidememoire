# SERVEUR DEBIAN

*Configuration d'un serveur debian*

## <a name='sommaire'>Sommaire</a>

  1. [Première connexion](#start)
  1. [Protection initiale](#)
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


Session - dedibox : IP + port 9281 + connexion type SSH + auto-login newuser

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
  	