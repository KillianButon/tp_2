#  Tp2

## I. Création et utilisation simples d'une VM CentOS

| Name   | ip           | MAC               | fonction |
| ------ | ------------ | ----------------- | -------- |
| lo     | 127.0.0.1/8  | 00:00:00:00:00:00 |  NAT     |
| enp0s3 | 10.0.2.15/24 | 08:00:27:3d:80:8a | Permet de communiquer avec soi même|
| enp0s8 | 10.2.1.2/24  | 08:00:27:d3:ec:02 | Permet de communiquer avec l'exterieur     |
![](https://i.imgur.com/DnhpI5n.png)

### 1

On change d'ip en 192.168.1.19
Puis on verrifie si on a bien changer d'ip avec ip a
![](https://i.imgur.com/5G4OPaA.png)
### 2
On vois bien que ca a changer en 192.168.1.19/24
![](https://i.imgur.com/Abx4jB1.png)

## II. Notion de ports

### 1. SSH
après avoir installé netstat je lance ceci:
![](https://i.imgur.com/wqndt4y.png)
on voit le port 22 en écoute.

On lance une installation avec cette commande pour être sur qu'il est installé:
sudo yum –y install openssh-server openssh-clients

ensuite on test:
PS C:\Users\killi> ssh -p 1111 kill@10.2.1.3
ssh: connect to host 10.2.1.3 port 1111: Connection timed out

est l'on peux voir que l'on est connecté.

j'ajoute le port souhaiter au firewall 
sudo firewall-cmd --add-port=4000/tcp --permanent
sudo firewall-cmd --add-port=2000/tcp --permanent

un port pour la vm et un pour connecté les deux aux même endroit.

voici la connection.
PS C:\Users\killi> ncat 10.2.1.3 2000
wow trop marrant

[root@localhost ~]# nc -l -p 2000
wow trop marrant

### 3. Wireshark

![](https://i.imgur.com/waGnGiE.png)

il y a trois messages sync qui équivaut a une demande de synchronisaton, sync ack qui est demande d'acceptation et ack qui signale qui a été installé.

### 4. Partie Groupe
## A Pc1 à Pc2
maintenant on va se ping : 

Envoi d’une requête 'Ping'  10.2.2.1 avec 32 octets de données :
Réponse de 10.2.2.1 : octets=32 temps<1ms TTL=127
Réponse de 10.2.2.1 : octets=32 temps<1ms TTL=127
Réponse de 10.2.2.1 : octets=32 temps<1ms TTL=127
Réponse de 10.2.2.1 : octets=32 temps<1ms TTL=127

Statistiques Ping pour 10.2.2.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
## A Pc2 à Pc1
Envoi d’une requête 'Ping'  10.2.1.1 avec 32 octets de données :
Réponse de 10.2.1.1 : octets=32 temps<1ms TTL=127
Réponse de 10.2.1.1 : octets=32 temps<1ms TTL=127
Réponse de 10.2.1.1 : octets=32 temps<1ms TTL=127
Réponse de 10.2.1.1 : octets=32 temps<1ms TTL=127

Statistiques Ping pour 10.2.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms

### maintenant que nos deux pc peuvent se ping on va passé de vm a pc et de vm a vm.

## C VM1 à Pc1

[root@localhost]# ping 10.2.1.1
PING 10.2.2.1 (10.2.2.1) 56(84) bytes of data.
64 bytes from 10.2.2.1: icmp_seq=1 ttl=64 time=3.2 ms
64 bytes from 10.2.2.1: icmp_seq=2 ttl=64 time=2.0 ms
64 bytes from 10.2.2.1: icmp_seq=1 ttl=64 time=1.20 ms
64 bytes from 10.2.1.1: icmp_seq=2 ttl=64 time=1.02 ms

--- 10.2.2.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.086/1.801/1.176/1.805 ms
## D VM2 à Pc2

[root@localhost]# ping 10.2.2.1
PING 10.2.2.1(10.2.1.1) 56(84) bytes of data.
64 bytes from 10.2.1.1: icmp_seq=1 ttl=64 time=2.9 ms
64 bytes from 10.2.1.1: icmp_seq=2 ttl=64 time=1.0 ms
64 bytes from 10.2.1.1: icmp_seq=1 ttl=64 time=3.9 ms
64 bytes from 10.2.1.1: icmp_seq=2 ttl=64 time=3.0 ms

--- 10.2.1.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.086/1.891/1.196/1.655 ms

## E Vm2 a Vm1

[root@localhost]# ping 10.2.2.10
PING 10.2.2.10(10.2.1.10) 56(84) bytes of data.
64 bytes from 10.2.1.10: icmp_seq=1 ttl=64 time=1.00 ms
64 bytes from 10.2.1.10: icmp_seq=2 ttl=64 time=3.12 ms
64 bytes from 10.2.1.10: icmp_seq=1 ttl=64 time=3.03 ms
64 bytes from 10.2.1.10: icmp_seq=2 ttl=64 time=1.19 ms

--- 10.2.1.10 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 2.086/0.891/0.696/0.125 ms

## 3  Configuration des noms de domaine

je n'es pas pu la faire malheuresement car mon second pc ne support pas de vm.