# TP 1 
##  Configuration de deux machines CentOS7 configurée de façon basique. 

Changer le nom des hostnames 
```bash
vi /etc/hostname 
```
ensuite on reboot les 2 vm's 

puis a l'aide de la commande ```hostname``` on regarde qu'ils ont bien changé de nom 
pour la premiere vm 
```bash
[kiki@localhost ~]$ hostname
node1.tp1.b2
```
pour la deuxieme vm 
```bash
[kiki@localhost ~]$ hostname
node2.tp1.b2
```


sur les deux vm création d'un user admin :
```bash 
[kiki@node2 ~]$ useradd admin
[kiki@node2 ~]$ vi /etc/sudoers
```

On passe en ssh sur notre première vm 

```bash
PS C:\Users\killi> ssh node1@192.168.1.11 -p22
node1@192.168.1.11's password:
```
On passe en ssh sur notre deuxième vm 
```bash
PS C:\Users\killi> ssh node2@192.168.1.12 -p22
node1@192.168.1.12's password:
```

ensuite on partitione sur la node1 

```bash
[admin@node1 ~]$ lvcreate
  No command with matching syntax recognised.  Run 'lvcreate --help' for more information.
[admin@node1 ~]$ lvcreate -L 2000 -n volume1 data
  Logical volume "volume1" created.
[admin@node1 ~]$ lvscan
  ACTIVE            '/dev/centos/swap' [820,00 MiB] inherit
  ACTIVE            '/dev/centos/admin' [<6,20 GiB] inherit
  ACTIVE            '/dev/data/volume1' [1,95 GiB] inherit
[admin@node1 ~]$ lvcreate -L 3000 -n volume3 data
  Logical volume "volume3" created.
[admin@node1 ~]$ lvscan
  ACTIVE            '/dev/centos/swap' [820,00 MiB] inherit
  ACTIVE            '/dev/centos/admin' [<6,20 GiB] inherit
  ACTIVE            '/dev/data/volume1' [1,95 GiB] inherit
  ACTIVE            '/dev/data/volume3' [<2,93 GiB] inherit
  ```
 maintenant nous montons les partitions dans les dossiers site 1 et site 2
```bash
 
  [admin@node1 srv]$ mkfs -t ext4 /dev/data/Volume1
mke2fs 1.42.9 (28-Dec-2013)
Ne peut évaluer par stat() /dev/data/Volume1 --- Aucun fichier ou dossier de ce type

Le périphérique n'existe apparemment pas ; l'avez-vous spécifié
correctement ?
[admin@node1 srv]$ mkfs -t ext4 /dev/data/volume1
mke2fs 1.42.9 (28-Dec-2013)
Étiquette de système de fichiers=
Type de système d'exploitation : Linux
Taille de bloc=4096 (log=2)
Taille de fragment=4096 (log=2)
« Stride » = 0 blocs, « Stripe width » = 0 blocs
128000 i-noeuds, 512000 blocs
25600 blocs (5.00%) réservés pour le super utilisateur
Premier bloc de données=0
Nombre maximum de blocs du système de fichiers=524288000
16 groupes de blocs
32768 blocs par groupe, 32768 fragments par groupe
8000 i-noeuds par groupe
Superblocs de secours stockés sur les blocs :
        32768, 98304, 163840, 229376, 294912

Allocation des tables de groupe : complété
Écriture des tables d'i-noeuds : complété
Création du journal (8192 blocs) : complété
Écriture des superblocs et de l'information de comptabilité du système de
fichiers : complété

[admin@node1 srv]$ mkfs -t ext4 /dev/data/volume2
mke2fs 1.42.9 (28-Dec-2013)
Ne peut évaluer par stat() /dev/data/volume2 --- Aucun fichier ou dossier de ce type

Le périphérique n'existe apparemment pas ; l'avez-vous spécifié
correctement ?
[admin@node1 srv]$ mkfs -t ext4 /dev/data/Volume2
mke2fs 1.42.9 (28-Dec-2013)
Ne peut évaluer par stat() /dev/data/Volume2 --- Aucun fichier ou dossier de ce type

Le périphérique n'existe apparemment pas ; l'avez-vous spécifié
correctement ?
[admin@node1 srv]$ mkfs -t ext4 /dev/data/volume3
mke2fs 1.42.9 (28-Dec-2013)
Étiquette de système de fichiers=
Type de système d'exploitation : Linux
Taille de bloc=4096 (log=2)
Taille de fragment=4096 (log=2)
« Stride » = 0 blocs, « Stripe width » = 0 blocs
192000 i-noeuds, 768000 blocs
38400 blocs (5.00%) réservés pour le super utilisateur
Premier bloc de données=0
Nombre maximum de blocs du système de fichiers=786432000
24 groupes de blocs
32768 blocs par groupe, 32768 fragments par groupe
8000 i-noeuds par groupe
Superblocs de secours stockés sur les blocs :
        32768, 98304, 163840, 229376, 294912

Allocation des tables de groupe : complété
Écriture des tables d'i-noeuds : complété
Création du journal (16384 blocs) : complété
Écriture des superblocs et de l'information de comptabilité du système de
fichiers : complété

[admin@node1 srv]$ mount /dev/data/volume1 /srv/site1
[admin@node1 srv]$ mount /dev/data/volume3 /srv/site3
mount: le point de montage /srv/site3 n'existe pas
[admin@node1 srv]$ mount /dev/data/volume3 /srv/site2
```

maintenant nous montons la partition dans fstab 
```bash
[admin@node1 ~]$  14L, 626C written
[admin@node1 ~]$ mount -av
/                         : ignoré
/boot                     : déjà monté
swap                      : ignoré
/srv/site1                : déjà monté
/srv/site2                : déjà monté
```
```bash
/dev/data/volume1           /srv/site1              ext4    defaults        0 0
/dev/data/volume3           /srv/site2              ext4    defaults        0 0
```

Pour check si nous avos internet nous faisons un 
```bash
nmcli dev
```
```bash
[admin@node1 ~]$ nmcli dev
DEVICE  TYPE      STATE     CONNECTION
enp0s3  ethernet  connecté  enp0s3
enp0s8  ethernet  connecté  enp0s8
lo      loopback  non-géré  --
```
on curl de google.com pour voir si l'on arrive a avoir internet

```bash
[admin@node1 ~]$ curl google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```
maintenant nous devons ping nos deux vm entre elle 

```bash 
[admin@node1 ~]$ ping 192.168.1.12 avec 32 octets de données :
Réponse de 192.168.1.12 : octets=32 temps<1ms TTL=64
Réponse de 192.168.1.12 : octets=32 temps<1ms TTL=64
Réponse de 192.168.1.12 : octets=32 temps<1ms TTL=64
```

```bash 
[admin@node1 ~]$ ping 192.168.1.11
PING 192.168.1.11 (192.168.1.11) 56(84) bytes of data.
Réponse de 192.168.1.11 : octets=32 temps<1ms TTL=64
Réponse de 192.168.1.11 : octets=32 temps=1 ms TTL=64

Statistiques Ping pour 192.168.1.11:
    Paquets : envoyés = 2, reçus = 2, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 1ms, Moyenne = 0ms
on peut voir que nos 2 vm's ce ping 


## Install de nginx sous Centos7
Les package de Nginx sont dans les depos de Epel donc nous devons d'abord ajouter les depot via :

- Installer le serveur web NGINX sur node1.tp1.b2 (avec une commande yum install).

```bash
sudo yum install epel-release

```

```
puis nous installons nginx

```bash 
sudo yum install -y nginx

```

- NGINX servent deux sites web, chacun possède un fichier unique index.html, NGINX doit utiliser un utilisateur dédié que vous avez créé à cet effet

  ```bash
  [admin@node1 ~]$ cat /etc/nginx/nginx.conf
  worker_processes 1;
  error_log nginx_error.log;
  user web;
  events {
        worker_connections 1024;
  }
  http {
        server {
                listen 80;
                server_name node1.tp1.b2;

                location /site1 {
                        alias /srv/site1;
                }

                location /site2 {
                        alias /srv/site2;
                }
        }
  }

  [admin@node1 ~]\$
  ```

- les sites web doivent se trouver dans /srv/site1 et /srv/site2

  ```bash
    [admin@node1 srv]$ sudo tree
    .
    ├── site1
    │   ├── index.html
    │   └── lost+found
    └── site2
      ├── index.html
      └── lost+found

    4 directories, 2 files
    [admin@node1 srv]\$
  ```
on met les restriction pour l'utilisateur:
  ```bash
  [admin@node1 ~]$ sudo ls -al /srv/
  total 8
  drwxr-xr-x.  4 kiki kiki   32 Sep 23 17:31 .
  dr-xr-xr-x. 17 kiki kiki  237 Sep 22 14:21 ..
  dr--------.  3 web  web  4096 Sep 24 15:39 site1
  dr--------.  3 web  web  4096 Sep 24 15:39 site2
  [admin@node1 ~]
  ```

- NGINX utilise un utilisateur qui a été créé pour lui 

  ```bash
  [amin@node1 ~]$ sudo useradd web
  ```

- les sites doivent être servis en HTTPS sur le port 443 et en HTTP sur le port 80

  ```bash
  [admin@node1 ~]$ sudo firewall-cmd --zone=public --add-port=443/tcp --permanent
  success

  [admin@node1 ~]$ sudo firewall-cmd --permanent --zone=public --add-service=https
  success

  [admin@node1 ~]$ sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
  success

  [admin@node1 ~]$ sudo firewall-cmd --permanent --zone=public --add-service=http
  success

  [admin@node1 ~]$ sudo firewall-cmd --reload
  success
  [admin@node1 ~]$
  ```

 la machine node2 peut joindre les deux sites web:
Site 1 :
  ```bash
[serv@node2 ~]$ curl -Lk http://node1.tp1.b2/site1
<h1>Site1 </h1>
  ```
  Site 2 :
  ```bash
[serv@node2 ~]$ curl -Lk http://node1.tp1.b2/site2
<h1>Site2 </h1>
  ```
 ## scripting

#!/bin/bash

# Buton
# 28/09/2020
# Backup script

backup_time=$(date +%Y%m%d_%H%M)

saved_folder_path="${1}"

saved_folder="${saved_folder_path##*/}"

backup_name="${saved_folder}_${backup_time}"

tar -czf $backup_name.tar.gz --absolute-names $saved_folder_path

nbr_site1=`ls -l | grep -c site1_`
nbr_site2=`ls -l | grep -c site2_`

if [ "$nbr_site1" > 7 ]; then
        echo "ça marche"

fi

  ## Netdata
```bash
 bash <(curl -Ss https://my-netdata.io/kickstart.sh)
 ```
 ```bash
tester firewall-cmd --add-port=19999/tcp --permanent firewall-cmd --reload
```
pour pouvoir faire une alerte sur discord il nous faut créer un channel discord munis d'un webhook pour y insérer le lien dans notre conf netdata.
```bash
[admin@node1 ~]$ cat /etc/netdata/health_alarm_notify.conf
https://discordapp.com/api/webhooks/760166157487046696/KV_uChPKmhRNrsCwAmyXL-xWRztM7295hj7goYYdAtVpcjb9I83K_ig9hCxztxMzPbYJ
```