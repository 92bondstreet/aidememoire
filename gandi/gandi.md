# Nom de domaine chez Gandi

*Utilisation d'un nom de domaine Gandi avec un serveur hébergé par un tiers.*

## <a name='sommaire'>Sommaire</a>

1. [Accès à la section zone](#start)
1. [Configuration de la zone](#zone)
1. [Association du nom de domaine](#domaine)

## <a name='start'>Accès à la section zone</a>

- Connection à [https://www.gandi.net/admin](https://www.gandi.net/admin)

- Dans la section Domaine, cliquez sur Zone DNS

```
Url correspondante : /admin/domain/zone/list
```

- Créér une zone puis la nommmer

## <a name='zone'>Configuration de la zone</a>

Nom | Type | Valeur | TTL
--- | --- | ---
www | A | xx.xxx.xx.xxx | 3h
@ | A | xx.xxx.xx.xxx | 3h


## <a name='domaine'>Association du nom de domaine</a>

- On choisit le nom de domaine

- Edition de la section **Fichier de zone**

```
Zone active:  XXXXXX Changer
```

En cliquand sur changer, vous pouvez alors renseigner le nom de la zone précedemment créée.
