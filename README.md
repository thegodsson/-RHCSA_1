# -RHCSA_1

Gestion du système de fichier
-----------------------------

A) Disque dur et table de partition

- Les types de disques
   HDD:
  - disque dur magnétique : dd, ou hdd
  - vitesse entre 3600 et 15 000 tours par minutes
  - Interface : SATA III: half duplex, SAS: full duplex, disque chaînable, FC5fibre channel): FIbre optique, cuivre
  SDD: Solid state drive:
    - electronique
    - les performances st exelante, le côut est beaucoup plus importanat
  
Les disque dur sont attachés à des controleurs de disque, ces contôleur pilotent les opérations des disques et les trasferts de données.
- Les controleurs de disques
  - sata, scsi, sas
   sata: est un standard : support le HotAdd , ajout de disque à chaud.
         L'interface standard des contrôleur STATA est applelée (Advanced Host Controller Interface (AHCI)
         Le sata c'est du half duplex, on a un cable qui envoit et qui recoit des données, il ne pas pas faire les 2 en même temps
   sas ( Serial Attached SCSI) , un standard qui utilise le jeu de commandes SCSI
        Connexions plus fiables et plus rapide, full duplex
        Des débits exclusifs 
        Jusqu'a 128 disques
        Plus chers 
        compatible sata
        Les disque sas, s'utilise sur de gros serveurs, il peuvent être utilisé avec des disque hdd ou sata.
     - 
- Les types de partitionnement
  - MBR , GPT
  
  Une partition, est un espace dédé à recevoir des fichiers via un système de fichier.
  Chaque patition dispose de son système de fichier : ntfs, ext4, xfs, ...
  
  Un disque dur peut contenir plusieurs partitions.
  
  Le nombre de partitions est en fonction du type de table de partitonement
  
  Avec l'avènement de l'UEFI, remplacement du BIOS
  
  On trouve 2 types de tables de patitionement : MBR ET GPT
  
  MBR:
  
  Master Boot record "zone amorce"
  
  - Le MBR à une taille de 512 octets
  - Le MBR contient principalement 2 éléments:
  
    - Le programme de boot que le bios lancera
    - La table des patitions
    
  - La table des partitions principale contient 4 descripteurs, il est donc limités à 4 partitions
  - On peut creer une partition étendue parmi les 4 partitions principales et créer de nouvelles partitions logiques à l'intérieur

Le MBR contient la table des 4 partitions principales du disque, le MBR , ne contient pas des partitions logiques.
Parmi les 4 patitions principales, une seule peut être une partition étendue (divisée en partition logiques)

SI une patition étendue est créee sur le disque dur , la table de ses partitions logiques est enregistrée dans le premier secteur de la partition étendue.


GPT:

GUID Partition Table

Il fait patie du standar EFI (Extensib=le firware Interface

- Les information concernant la table de partitionement sont stockées dans un entête GPT , mais conserve une entrée MBR
- Il y a deux GPT sur le dique dur, l'un primaire, l'autre secondaire (Sauvegarde du premier)
- GPT gère les disques durs et partitions jusqu'a 2Zo (2,2To en MBR)
- GPT permet presque un nombre illimité de partitions, la limite est la taille de la table de partition, par défaut 128 partitions


B) Partitionner les disque avec fdisk MBR GPT

Voir fichietr en pj

- Partitionement avec fdisk

  

- Partitionement avec parted --> Plus lié au gpt
- 
[root@srvcentos7 ~]# ls -l /dev/sdb*
brw-rw----. 1 root disk 8, 16 Mar  8 11:58 /dev/sdb
brw-rw----. 1 root disk 8, 17 Mar  8 11:58 /dev/sdb1
brw-rw----. 1 root disk 8, 18 Mar  8 11:58 /dev/sdb2
[root@srvcentos7 ~]# blkid
/dev/sda1: UUID="a81b3181-905b-44c3-8292-4186a52cb4e5" TYPE="xfs"
/dev/sda2: UUID="AREBkl-EDxP-PGSR-02Sg-1hTm-pAQS-BJzcRP" TYPE="LVM2_member"
/dev/mapper/cl-root: UUID="53aae40a-d661-4cb5-9890-136fd1e7f3f9" TYPE="xfs"
/dev/mapper/cl-swap: UUID="b6a4a8b7-b3c1-40eb-a357-d8023106be84" TYPE="swap"
/dev/sdb1: PARTLABEL="VM" PARTUUID="21ec8c43-f91c-4c68-977e-b460870c5a7e"
/dev/sdb2: PARTLABEL="VAR" PARTUUID="b3ecdd26-90e1-491d-85c4-458bdecf5595"
[root@srvcentos7 ~]#



- Partitionement avec Gparted



C) Introduction aux systèmes de fichiers

1) C'est quoi un système de fichier ?
- Un bon FS vas nous offrir la journalisation.
- Une bonne gestion de la fragmentation
Pour faire simple. Lorsqu'un utilisateur accède àun fichier, il accède à une inode.
L'inode est un numéro unique qui permet d'identifier le ficheir ou répertoire.

L'inode contient:
  - Les permissions ou droit d'accès
  - Le créateur ou propiétaire
  - Nombre de blocs utilisés par le fichier
  - Le moyen de retrouver les parties du fichier
  - L'horodatage: la dates d'accès , de création de modifications
  - Le nombre d'inodes détermine le nombre de fichiers sur le FS
  - Les inodes ne contiennent pas les nom du fichier, mais les métadonnées

3) Les systèmes de fichiers sous red hat
ext2 
ext3 : ajout de la journalisation
ext4: Grande capacités et gestions des extents (pace réservé pour un fichier
xfs (Utilisé par default sur redhat 7) : Grande capacité, gestion des extents, haute performances:
Btrfs: snaphot, clone, extension, compresion, auto réparation, raid natif
zfs: snapshot , clone , extension, déduplication, comprésion, auto réparation, raid natif
Et quelques systèmes de fichier réseau:
  NFS, SMB, GusterFS, CEphFs

5) Creer son système de fichier
mkfs + le type de FS:
exemple :

ext4:
mkfs.ext4 /dev/sdb1

xfs:
mkfs.xfs /dev/sdb1

xfs + labell:
mkfs.xfs -L var /dev/sdb1



7) Monter son système de fichier
8) Le fichier /etc/fstab

exemple d'entré fstab:
UUID="860c107e-3ad0-494d-a914-6a86133175bd"     /xfs_sdb1       xfs     defaults(option, on ne met pas d'option)        0 0
périphérique                                    oû je la monte  système de fichier    option     1er chiffre(dump, ici on ne fait pas de dump) 2ème chiffre(vérification des partitions, 0 pas de vérification, 1 vérification du 1er disque)

UUID="d95436bc-be52-4722-aa10-6d1d47519a76"     /ext4_sdb2      ext4    defaults        0 2 (2 pour vérification du disque, mais après l'autre disque)

Utilisation du label:
LABEL="VM"      /xfs_sdb1       xfs     defaults        0 1

Tout est dans le fichier "FS" en pj

D) Vérifier er réparer un FS

NB: La partition doit être démonter pour la vérifier

Vérifier un fs ext4:
fsck.ext4 /dev/sdb2

-f mode vérification approfondie (pas seulement le journal)

Réparer un fs ext4:
fsck.ext4 /dev/sdb2

-y : mode réparation automatique

voir fichier en pj

Réparer un fs ext4:


E) Le FHS - File Hierarchy Standard

Norme de la la hiarchie des système de fichiers définit:
   - l'arborescenece
   - Le contenu des proncipaux répertoires des systèmes de fichiers



F) Propiété des fichiers

 - Les propriét



G) Les droits d'accès

[root@srvcentos7 ~]# ls -l /usr/bin/passwd
-rwsr-xr-x. 1 root root 27856 Apr  1  2020 /usr/bin/passwd
[root@srvcentos7 ~]# su - jm
Last login: Tue Mar  8 14:16:29 CET 2022 from 192.168.203.117 on pts/1
[jm@srvcentos7 ~]$ passwd
Changing password for user jm.
Changing password for jm.
(current) UNIX password:
[jm@srvcentos7 ~]$ exit
logout
[root@srvcentos7 ~]# su - jm
Last login: Tue Mar  8 14:20:18 CET 2022 on pts/1
[jm@srvcentos7 ~]$ touch f1
[jm@srvcentos7 ~]$ ls -l f1
-rw-rw-r--. 1 jm jm 0 Mar  8 14:22 f1
[jm@srvcentos7 ~]$ exit
logout
[root@srvcentos7 ~]# chmod 4755 /home/jm/f1
[root@srvcentos7 ~]# ls -l /home/jm/f1
-rwsr-xr-x. 1 jm jm 0 Mar  8 14:22 /home/jm/f1
[root@srvcentos7 ~]# groupadd net-admin
[root@srvcentos7 ~]# usermod -a jm -G net-admin
[root@srvcentos7 ~]# id jm
uid=1000(jm) gid=1000(jm) groups=1000(jm),1001(net-admin)
[root@srvcentos7 ~]# groupadd net-sys
[root@srvcentos7 ~]# usermod -a jm -G net-sys
[root@srvcentos7 ~]# mkdir file_pour_jm
[root@srvcentos7 ~]# chown jm:net-sys file_pour_jm/
[root@srvcentos7 ~]# ls -ld file_pour_jm/
drwxr-xr-x. 2 jm net-sys 6 Mar  8 14:26 file_pour_jm/
[root@srvcentos7 ~]# tocuh file_pour_jm/exec
bash: tocuh: command not found...
Similar command is: 'touch'
[root@srvcentos7 ~]# touch file_pour_jm/exec
[root@srvcentos7 ~]# chmod 755 file_pour_jm/exec
[root@srvcentos7 ~]# ls -l file_pour_jm/
total 0
-rwxr-xr-x. 1 root root 0 Mar  8 14:27 exec
[root@srvcentos7 ~]# chmod 2755 file_pour_jm/
[root@srvcentos7 ~]# ls -l file_pour_jm/
total 0
-rwxr-xr-x. 1 root root 0 Mar  8 14:27 exec
[root@srvcentos7 ~]# ls -ld file_pour_jm
drwxr-sr-x. 2 jm net-sys 18 Mar  8 14:27 file_pour_jm
[root@srvcentos7 ~]# touch file_pour_jm/exec2
[root@srvcentos7 ~]# chmod 755 file_pour_jm/^C
[root@srvcentos7 ~]# ls -l file_pour_jm/
total 0
-rwxr-xr-x. 1 root root    0 Mar  8 14:27 exec
-rw-r--r--. 1 root net-sys 0 Mar  8 14:29 exec2
[root@srvcentos7 ~]#





H) Rechercher des fichiers

-atime: date de dernier accès
-mtime: date de dernière modification
-ctime: date de création

 136  find /root -user jm
  137  find /root -user jm -exec ls -l {} \;
  138  find /root -user jm -exec  {} \;
  139  find /root -user jm   {} \;
  140  find / -user jm
  141  find / -user jm >/dev/null
  142  find / -user jm 2>/dev/null
  143  find / -perm -400 2>/dev/null
  144  find / -perm -4000  2>/dev/null
  145  find / -perm -4000 -o -2000  2>/dev/null
  146  find / -perm -4000 -o -perm -2000  2>/dev/null
  147  find / -iname "f*" -a -user jm -exec cp {} /tmp/ \; 2>/dev/null
  153  find / -iname "f*" -a -user jm -exec cp {} /tmp/ \; 2>/dev/null
  155  history | grep find
  157  history | grep find
[root@srvcentos7 ~]#


Gestion des processus
----------------------

top et ps


- ps
  
- top
u , puis taper le nom de l'user , il vas afficher que les processus de cet use, 
u, entrer, vas remettre tout les processus
f, on vas pouvoiur ajouter ou retirer des champ à top, et au meme endroit on peut avec "s" , sur le champ voulu trié a partir du champ voulu , par exemple si on veut trier sur la mémoire , on vas sur le champ mémoire , qu'on sélectionne et on tape "s" en haut on voit que top vas afficher la mémoire en priorité, exuite on fait "q" pour sortir.

On peut donc par exemple reéfinir la priorité d'un process avec un "r" , puis on note le pid, et puis on change la priorité., je peut également arrété un processus avec un k, puis 15, ou un 9(dangereux)

[root@srvcentos7 ~]# kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
[root@srvcentos7 ~]# ps -aux | grep clock
jm       11365  5.9  4.7 668236 23540 ?        Sl   15:27   0:01 /usr/bin/gnome-clocks --gapplication-service
root     11409  0.0  0.1 112812   980 pts/2    R+   15:27   0:00 grep --color=auto clock
[root@srvcentos7 ~]# kill -STOP 11365
[root@srvcentos7 ~]# kill -CONT 11365
[root@srvcentos7 ~]# kill -19 11365
[root@srvcentos7 ~]# kill -18 11365
[root@srvcentos7 ~]# kill -19 11365
[root@srvcentos7 ~]# kill -15 11365
[root@srvcentos7 ~]# ps -aux | grep clock
jm       11365  3.3  4.7 668236 23540 ?        Tl   15:27   0:06 /usr/bin/gnome-clocks --gapplication-service
root     11567  0.0  0.1 112812   984 pts/2    R+   15:30   0:00 grep --color=auto clock
[root@srvcentos7 ~]# kill -KILL 11365
[root@srvcentos7 ~]# kill --help
-bash: kill: -help: invalid signal specification
[root@srvcentos7 ~]# killall --help
Usage: killall [-Z CONTEXT] [-u USER] [ -eIgiqrvw ] [ -SIGNAL ] NAME...
       killall -l, --list
       killall -V, --version

  -e,--exact          require exact match for very long names
  -I,--ignore-case    case insensitive process name match
  -g,--process-group  kill process group instead of process
  -y,--younger-than   kill processes younger than TIME
  -o,--older-than     kill processes older than TIME
  -i,--interactive    ask for confirmation before killing
  -l,--list           list all known signal names
  -q,--quiet          don't print complaints
  -r,--regexp         interpret NAME as an extended regular expression
  -s,--signal SIGNAL  send this signal instead of SIGTERM
  -u,--user USER      kill only process(es) running as USER
  -v,--verbose        report if the signal was successfully sent
  -V,--version        display version information
  -w,--wait           wait for processes to die
  -Z,--context REGEXP kill only process(es) having context
                      (must precede other arguments)

[root@srvcentos7 ~]#


   
