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



GESTION DES SERVICES AVEC SYSTEMD
----------------------------------

Processus de démmarge architecture x86 :
----------------------------------------

- Le BIOS et L'UEFI
- Grub: Grand Unified BootLoader
- Initrd : InitialRamdisk
- Systemd

Systemd utilise comme pour sysinit le principe de niveau d'exécution


Gérer les services avec Systemd
---------------------------------

systemd se base sur la notion d'unités:

- systemd
- les units de systemd
[root@srvcentos7 ~]# systemctl list-units
  UNIT                                                                                     LOAD   ACTIVE SUB       DESCRIPTION
  proc-sys-fs-binfmt_misc.automount                                                        loaded active running   Arbitrary Executable File Formats File System Automount Point
  sys-devices-pci0000:00-0000:00:01.1-ata2-host1-target1:0:0-1:0:0:0-block-sr0.device      loaded active plugged   VBOX_CD-ROM
  sys-devices-pci0000:00-0000:00:03.0-net-enp0s3.device                                    loaded active plugged   82540EM Gigabit Ethernet Controller (PRO/1000 MT Desktop Adapter)
  sys-devices-pci0000:00-0000:00:05.0-sound-card0.device                                   loaded active plugged   82801AA AC'97 Audio Controller
  sys-devices-pci0000:00-0000:00:0d.0-ata3-host2-target2:0:0-2:0:0:0-block-sda-sda1.device loaded active plugged   VBOX_HARDDISK 1
  sys-devices-pci0000:00-0000:00:0d.0-ata3-host2-target2:0:0-2:0:0:0-block-sda-sda2.device loaded active plugged   VBOX_HARDDISK 2
  sys-devices-pci0000:00-0000:00:0d.0-ata3-host2-target2:0:0-2:0:0:0-block-sda.device      loaded active plugged   VBOX_HARDDISK
  sys-devices-pci0000:00-0000:00:0d.0-ata4-host3-target3:0:0-3:0:0:0-block-sdb-sdb1.device loaded active plugged   VBOX_HARDDISK VM
  sys-devices-pci0000:00-0000:00:0d.0-ata4-host3-target3:0:0-3:0:0:0-block-sdb-sdb2.device loaded active plugged   VBOX_HARDDISK var
  sys-devices-pci0000:00-0000:00:0d.0-ata4-host3-target3:0:0-3:0:0:0-block-sdb.device      loaded active plugged   VBOX_HARDDISK
  sys-devices-platform-serial8250-tty-ttyS0.device                                         loaded active plugged   /sys/devices/platform/serial8250/tty/ttyS0
  sys-devices-platform-serial8250-tty-ttyS1.device                                         loaded active plugged   /sys/devices/platform/serial8250/tty/ttyS1
  sys-devices-platform-serial8250-tty-ttyS2.device                                         loaded active plugged   /sys/devices/platform/serial8250/tty/ttyS2
  sys-devices-platform-serial8250-tty-ttyS3.device                                         loaded active plugged   /sys/devices/platform/serial8250/tty/ttyS3
  sys-devices-virtual-block-dm\x2d0.device                                                 loaded active plugged   /sys/devices/virtual/block/dm-0
  sys-devices-virtual-block-dm\x2d1.device                                                 loaded active plugged   /sys/devices/virtual/block/dm-1
  sys-devices-virtual-net-virbr0.device                                                    loaded active plugged   /sys/devices/virtual/net/virbr0
  sys-devices-virtual-net-virbr0\x2dnic.device                                             loaded active plugged   /sys/devices/virtual/net/virbr0-nic
  sys-module-configfs.device                                                               loaded active plugged   /sys/module/configfs
  sys-module-fuse.device                                                                   loaded active plugged   /sys/module/fuse
  sys-subsystem-net-devices-enp0s3.device                                                  loaded active plugged   82540EM Gigabit Ethernet Controller (PRO/1000 MT Desktop Adapter)
  sys-subsystem-net-devices-virbr0.device                                                  loaded active plugged   /sys/subsystem/net/devices/virbr0
  sys-subsystem-net-devices-virbr0\x2dnic.device                                           loaded active plugged   /sys/subsystem/net/devices/virbr0-nic
● -.mount                                                                                  masked active mounted   -.mount
● boot.mount                                                                               masked active mounted   boot.mount
  dev-hugepages.mount                                                                      loaded active mounted   Huge Pages File System
  dev-mqueue.mount                                                                         loaded active mounted   POSIX Message Queue File System
  ext4_sdb2.mount                                                                          loaded active mounted   /ext4_sdb2
  proc-sys-fs-binfmt_misc.mount                                                            loaded active mounted   Arbitrary Executable File Formats File System
  run-user-1000-gvfs.mount                                                                 loaded active mounted   /run/user/1000/gvfs
● run-user-1000.mount                                                                      masked active mounted   run-user-1000.mount
  sys-fs-fuse-connections.mount                                                            loaded active mounted   FUSE Control File System
  sys-kernel-config.mount                                                                  loaded active mounted   Configuration File System
  sys-kernel-debug.mount                                                                   loaded active mounted   Debug File System
● var-lib-nfs-rpc_pipefs.mount                                                             masked active mounted   var-lib-nfs-rpc_pipefs.mount
  xfs_sdb1.mount                                                                           loaded active mounted   /xfs_sdb1
  brandbot.path                                                                            loaded active waiting   Flexible branding
  cups.path                                                                                loaded active waiting   CUPS Printer Service Spool
  systemd-ask-password-plymouth.path                                                       loaded active waiting   Forward Password Requests to Plymouth Directory Watch
  systemd-ask-password-wall.path                                                           loaded active waiting   Forward Password Requests to Wall Directory Watch
  session-1.scope                                                                          loaded active running   Session 1 of user jm
  session-17.scope                                                                         loaded active running   Session 17 of user jm
  session-2.scope                                                                          loaded active running   Session 2 of user jm
  abrt-ccpp.service                                                                        loaded active exited    Install ABRT coredump hook
  abrt-oops.service                                                                        loaded active running   ABRT kernel log watcher
  abrt-xorg.service                                                                        loaded active running   ABRT Xorg log watcher
  abrtd.service                                                                            loaded active running   ABRT Automated Bug Reporting Tool
  accounts-daemon.service                                                                  loaded active running   Accounts Service
  alsa-state.service                                                                       loaded active running   Manage Sound Card State (restore and store)
  atd.service                                                                              loaded active running   Job spooling tools
  auditd.service                                                                           loaded active running   Security Auditing Service
  avahi-daemon.service                                                                     loaded active running   Avahi mDNS/DNS-SD Stack
  blk-availability.service                                                                 loaded active exited    Availability of block devices
  bolt.service                                                                             loaded active running   Thunderbolt system service
  colord.service                                                                           loaded active running   Manage, Install and Generate Color Profiles
  crond.service                                                                            loaded active running   Command Scheduler
  cups.service                                                                             loaded active running   CUPS Printing Service
  dbus.service                                                                             loaded active running   D-Bus System Message Bus
  firewalld.service                                                                        loaded active running   firewalld - dynamic firewall daemon
  fwupd.service                                                                            loaded active running   Firmware update daemon
  gdm.service                                                                              loaded active running   GNOME Display Manager
  gssproxy.service                                                                         loaded active running   GSSAPI Proxy Daemon
  iscsi-shutdown.service                                                                   loaded active exited    Logout off all iSCSI sessions on shutdown
● kdump.service                                                                            loaded failed failed    Crash recovery kernel arming
  kmod-static-nodes.service                                                                loaded active exited    Create list of required static device nodes for the current kernel
  ksm.service                                                                              loaded active exited    Kernel Samepage Merging
  ksmtuned.service                                                                         loaded active running   Kernel Samepage Merging (KSM) Tuning Daemon
  libstoragemgmt.service                                                                   loaded active running   libstoragemgmt plug-in server daemon
  libvirtd.service                                                                         loaded active running   Virtualization daemon
  lvm2-lvmetad.service                                                                     loaded active running   LVM2 metadata daemon
  lvm2-monitor.service                                                                     loaded active exited    Monitoring of LVM2 mirrors, snapshots etc. using dmeventd or progress polling
  lvm2-pvscan@8:2.service                                                                  loaded active exited    LVM2 PV scan on device 8:2
  mcelog.service                                                                           loaded active running   Machine Check Exception Logging Daemon
  ModemManager.service                                                                     loaded active running   Modem Manager
  netcf-transaction.service                                                                loaded active exited    Rollback uncommitted netcf network config change transactions
  network.service                                                                          loaded active exited    LSB: Bring up/down networking
  NetworkManager-wait-online.service                                                       loaded active exited    Network Manager Wait Online
  NetworkManager.service                                                                   loaded active running   Network Manager
  packagekit.service                                                                       loaded active running   PackageKit Daemon
  polkit.service                                                                           loaded active running   Authorization Manager
  postfix.service                                                                          loaded active running   Postfix Mail Transport Agent
  rhel-dmesg.service                                                                       loaded active exited    Dump dmesg to /var/log/dmesg
  rhel-domainname.service                                                                  loaded active exited    Read and set NIS domainname from /etc/sysconfig/network
  rhel-import-state.service                                                                loaded active exited    Import network configuration from initramfs
  rhel-readonly.service                                                                    loaded active exited    Configure read-only root support
  rngd.service                                                                             loaded active running   Hardware RNG Entropy Gatherer Daemon
  rsyslog.service                                                                          loaded active running   System Logging Service
  rtkit-daemon.service                                                                     loaded active running   RealtimeKit Scheduling Policy Service
  smartd.service                                                                           loaded active running   Self Monitoring and Reporting Technology (SMART) Daemon
  sshd.service                                                                             loaded active running   OpenSSH server daemon
  sysstat.service                                                                          loaded active exited    Resets System Activity Logs
  systemd-journal-flush.service                                                            loaded active exited    Flush Journal to Persistent Storage
  systemd-journald.service                                                                 loaded active running   Journal Service
  systemd-logind.service                                                                   loaded active running   Login Service
  systemd-modules-load.service                                                             loaded active exited    Load Kernel Modules
  systemd-random-seed.service                                                              loaded active exited    Load/Save Random Seed
  systemd-remount-fs.service                                                               loaded active exited    Remount Root and Kernel File Systems
  systemd-sysctl.service                                                                   loaded active exited    Apply Kernel Variables
  systemd-tmpfiles-setup-dev.service                                                       loaded active exited    Create Static Device Nodes in /dev
  systemd-tmpfiles-setup.service                                                           loaded active exited    Create Volatile Files and Directories
  systemd-udev-settle.service                                                              loaded active exited    udev Wait for Complete Device Initialization
  systemd-udev-trigger.service                                                             loaded active exited    udev Coldplug all Devices
  systemd-udevd.service                                                                    loaded active running   udev Kernel Device Manager
  systemd-update-utmp.service                                                              loaded active exited    Update UTMP about System Boot/Shutdown
  systemd-user-sessions.service                                                            loaded active exited    Permit User Sessions
  systemd-vconsole-setup.service                                                           loaded active exited    Setup Virtual Console
  tuned.service                                                                            loaded active running   Dynamic System Tuning Daemon
  udisks2.service                                                                          loaded active running   Disk Manager
  upower.service                                                                           loaded active running   Daemon for power management
  wpa_supplicant.service                                                                   loaded active running   WPA Supplicant daemon
  -.slice                                                                                  loaded active active    Root Slice
  machine.slice                                                                            loaded active active    Virtual Machine and Container Slice
  system-getty.slice                                                                       loaded active active    system-getty.slice
  system-lvm2\x2dpvscan.slice                                                              loaded active active    system-lvm2\x2dpvscan.slice
  system-selinux\x2dpolicy\x2dmigrate\x2dlocal\x2dchanges.slice                            loaded active active    system-selinux\x2dpolicy\x2dmigrate\x2dlocal\x2dchanges.slice
  system.slice                                                                             loaded active active    System Slice
  user-1000.slice                                                                          loaded active active    User Slice of jm
  user.slice                                                                               loaded active active    User and Session Slice
  avahi-daemon.socket                                                                      loaded active running   Avahi mDNS/DNS-SD Stack Activation Socket
  cups.socket                                                                              loaded active running   CUPS Printing Service Sockets
  dbus.socket                                                                              loaded active running   D-Bus System Message Bus Socket
  dm-event.socket                                                                          loaded active listening Device-mapper event daemon FIFOs
  iscsid.socket                                                                            loaded active listening Open-iSCSI iscsid Socket
  iscsiuio.socket                                                                          loaded active listening Open-iSCSI iscsiuio Socket
  lvm2-lvmetad.socket                                                                      loaded active running   LVM2 metadata daemon socket
  lvm2-lvmpolld.socket                                                                     loaded active listening LVM2 poll daemon socket
  rpcbind.socket                                                                           loaded active listening RPCbind Server Activation Socket
  systemd-initctl.socket                                                                   loaded active listening /dev/initctl Compatibility Named Pipe
  systemd-journald.socket                                                                  loaded active running   Journal Socket
  systemd-shutdownd.socket                                                                 loaded active listening Delayed Shutdown Socket
  systemd-udevd-control.socket                                                             loaded active running   udev Control Socket
  systemd-udevd-kernel.socket                                                              loaded active running   udev Kernel Socket
  virtlockd.socket                                                                         loaded active listening Virtual machine lock manager socket
  virtlogd.socket                                                                          loaded active listening Virtual machine log manager socket
  dev-mapper-cl\x2dswap.swap                                                               loaded active active    /dev/mapper/cl-swap
  basic.target                                                                             loaded active active    Basic System
  cryptsetup.target                                                                        loaded active active    Local Encrypted Volumes
  getty.target                                                                             loaded active active    Login Prompts
  graphical.target                                                                         loaded active active    Graphical Interface
  local-fs-pre.target                                                                      loaded active active    Local File Systems (Pre)
  local-fs.target                                                                          loaded active active    Local File Systems
  multi-user.target                                                                        loaded active active    Multi-User System
  network-online.target                                                                    loaded active active    Network is Online
  network-pre.target                                                                       loaded active active    Network (Pre)
  network.target                                                                           loaded active active    Network
  nfs-client.target                                                                        loaded active active    NFS client services
  nss-user-lookup.target                                                                   loaded active active    User and Group Name Lookups
  paths.target                                                                             loaded active active    Paths
  remote-fs-pre.target                                                                     loaded active active    Remote File Systems (Pre)
  remote-fs.target                                                                         loaded active active    Remote File Systems
  rpc_pipefs.target                                                                        loaded active active    rpc_pipefs.target
  slices.target                                                                            loaded active active    Slices
  sockets.target                                                                           loaded active active    Sockets
  sound.target                                                                             loaded active active    Sound Card
  swap.target                                                                              loaded active active    Swap
  sysinit.target                                                                           loaded active active    System Initialization
  timers.target                                                                            loaded active active    Timers
  systemd-tmpfiles-clean.timer                                                             loaded active waiting   Daily Cleanup of Temporary Directories

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.

158 loaded units listed. Pass --all to see loaded but inactive units, too.
To show all installed unit files use 'systemctl list-unit-files'.

- Gestion des services
- Gestions de cibles (target), gérer les niveaux d'éxécution
[root@srvcentos7 ~]# ls -l /etc/systemd/
total 28
-rw-r--r--.  1 root root  720 Jan 13 17:54 bootchart.conf
-rw-r--r--.  1 root root  615 Jan 13 17:54 coredump.conf
-rw-r--r--.  1 root root  983 Jan 13 17:54 journald.conf
-rw-r--r--.  1 root root  957 Jan 13 17:54 logind.conf
drwxr-xr-x. 17 root root 4096 Feb 28 12:23 system
-rw-r--r--.  1 root root 1552 Jan 13 17:54 system.conf
drwxr-xr-x.  2 root root    6 Jan 13 17:54 user
-rw-r--r--.  1 root root 1127 Jan 13 17:54 user.conf
[root@srvcentos7 ~]# ls -l /etc/systemd/system
total 8
drwxr-xr-x. 2 root root   82 Feb 28 12:23 basic.target.wants
drwxr-xr-x. 2 root root   31 Feb 28 11:02 bluetooth.target.wants
lrwxrwxrwx. 1 root root   41 Feb 28 11:02 dbus-org.bluez.service -> /usr/lib/systemd/system/bluetooth.service
lrwxrwxrwx. 1 root root   41 Feb 28 11:02 dbus-org.fedoraproject.FirewallD1.service -> /usr/lib/systemd/system/firewalld.service
lrwxrwxrwx. 1 root root   44 Feb 28 11:10 dbus-org.freedesktop.Avahi.service -> /usr/lib/systemd/system/avahi-daemon.service
lrwxrwxrwx. 1 root root   44 Feb 28 11:11 dbus-org.freedesktop.ModemManager1.service -> /usr/lib/systemd/system/ModemManager.service
lrwxrwxrwx. 1 root root   46 Feb 28 11:02 dbus-org.freedesktop.NetworkManager.service -> /usr/lib/systemd/system/NetworkManager.service
lrwxrwxrwx. 1 root root   57 Feb 28 11:02 dbus-org.freedesktop.nm-dispatcher.service -> /usr/lib/systemd/system/NetworkManager-dispatcher.service
lrwxrwxrwx. 1 root root   36 Feb 28 11:24 default.target -> /lib/systemd/system/graphical.target
drwxr-xr-x. 2 root root   87 Feb 28 11:00 default.target.wants
drwxr-xr-x. 2 root root   38 Feb 28 11:11 dev-virtio\x2dports-org.qemu.guest_agent.0.device.wants
lrwxrwxrwx. 1 root root   35 Feb 28 11:07 display-manager.service -> /usr/lib/systemd/system/gdm.service
drwxr-xr-x. 2 root root   32 Feb 28 11:00 getty.target.wants
drwxr-xr-x. 2 root root   65 Feb 28 11:28 graphical.target.wants
drwxr-xr-x. 2 root root   35 Feb 28 12:23 local-fs.target.wants
drwxr-xr-x. 2 root root 4096 Feb 28 12:23 multi-user.target.wants
drwxr-xr-x. 2 root root   48 Feb 28 12:12 network-online.target.wants
drwxr-xr-x. 2 root root   26 Feb 28 11:08 printer.target.wants
drwxr-xr-x. 2 root root   31 Feb 28 11:03 remote-fs.target.wants
drwxr-xr-x. 2 root root  188 Feb 28 11:10 sockets.target.wants
drwxr-xr-x. 2 root root   36 Feb 28 11:10 spice-vdagentd.target.wants
drwxr-xr-x. 2 root root 4096 Feb 28 12:23 sysinit.target.wants
drwxr-xr-x. 2 root root   44 Feb 28 11:00 system-update.target.wants
[root@srvcentos7 ~]# ls -l /etc/systemd/system/multi-user.target.wants/
total 0
lrwxrwxrwx. 1 root root 41 Feb 28 11:03 abrt-ccpp.service -> /usr/lib/systemd/system/abrt-ccpp.service
lrwxrwxrwx. 1 root root 37 Feb 28 11:01 abrtd.service -> /usr/lib/systemd/system/abrtd.service
lrwxrwxrwx. 1 root root 41 Feb 28 11:01 abrt-oops.service -> /usr/lib/systemd/system/abrt-oops.service
lrwxrwxrwx. 1 root root 43 Feb 28 11:02 abrt-vmcore.service -> /usr/lib/systemd/system/abrt-vmcore.service
lrwxrwxrwx. 1 root root 41 Feb 28 11:02 abrt-xorg.service -> /usr/lib/systemd/system/abrt-xorg.service
lrwxrwxrwx. 1 root root 35 Feb 28 11:11 atd.service -> /usr/lib/systemd/system/atd.service
lrwxrwxrwx. 1 root root 38 Feb 28 11:03 auditd.service -> /usr/lib/systemd/system/auditd.service
lrwxrwxrwx. 1 root root 44 Feb 28 11:10 avahi-daemon.service -> /usr/lib/systemd/system/avahi-daemon.service
lrwxrwxrwx. 1 root root 37 Feb 28 12:23 brandbot.path -> /usr/lib/systemd/system/brandbot.path
lrwxrwxrwx. 1 root root 37 Feb 28 11:01 crond.service -> /usr/lib/systemd/system/crond.service
lrwxrwxrwx. 1 root root 33 Feb 28 11:08 cups.path -> /usr/lib/systemd/system/cups.path
lrwxrwxrwx. 1 root root 36 Feb 28 11:08 cups.service -> /usr/lib/systemd/system/cups.service
lrwxrwxrwx. 1 root root 42 Feb 28 11:11 irqbalance.service -> /usr/lib/systemd/system/irqbalance.service
lrwxrwxrwx. 1 root root 37 Feb 28 11:02 kdump.service -> /usr/lib/systemd/system/kdump.service
lrwxrwxrwx. 1 root root 35 Feb 28 11:03 ksm.service -> /usr/lib/systemd/system/ksm.service
lrwxrwxrwx. 1 root root 40 Feb 28 11:03 ksmtuned.service -> /usr/lib/systemd/system/ksmtuned.service
lrwxrwxrwx. 1 root root 46 Feb 28 11:02 libstoragemgmt.service -> /usr/lib/systemd/system/libstoragemgmt.service
lrwxrwxrwx. 1 root root 40 Feb 28 11:03 libvirtd.service -> /usr/lib/systemd/system/libvirtd.service
lrwxrwxrwx. 1 root root 38 Feb 28 11:11 mcelog.service -> /usr/lib/systemd/system/mcelog.service
lrwxrwxrwx. 1 root root 41 Feb 28 11:02 mdmonitor.service -> /usr/lib/systemd/system/mdmonitor.service
lrwxrwxrwx. 1 root root 44 Feb 28 11:11 ModemManager.service -> /usr/lib/systemd/system/ModemManager.service
lrwxrwxrwx. 1 root root 49 Feb 28 12:07 netcf-transaction.service -> /usr/lib/systemd/system/netcf-transaction.service
lrwxrwxrwx. 1 root root 46 Feb 28 11:02 NetworkManager.service -> /usr/lib/systemd/system/NetworkManager.service
lrwxrwxrwx. 1 root root 41 Feb 28 11:03 nfs-client.target -> /usr/lib/systemd/system/nfs-client.target
lrwxrwxrwx. 1 root root 39 Feb 28 11:11 postfix.service -> /usr/lib/systemd/system/postfix.service
lrwxrwxrwx. 1 root root 40 Feb 28 11:00 remote-fs.target -> /usr/lib/systemd/system/remote-fs.target
lrwxrwxrwx. 1 root root 46 Feb 28 12:23 rhel-configure.service -> /usr/lib/systemd/system/rhel-configure.service
lrwxrwxrwx. 1 root root 36 Feb 28 11:11 rngd.service -> /usr/lib/systemd/system/rngd.service
lrwxrwxrwx. 1 root root 39 Feb 28 11:10 rsyslog.service -> /usr/lib/systemd/system/rsyslog.service
lrwxrwxrwx. 1 root root 38 Feb 28 11:11 smartd.service -> /usr/lib/systemd/system/smartd.service
lrwxrwxrwx. 1 root root 36 Feb 28 11:10 sshd.service -> /usr/lib/systemd/system/sshd.service
lrwxrwxrwx. 1 root root 39 Feb 28 11:10 sysstat.service -> /usr/lib/systemd/system/sysstat.service
lrwxrwxrwx. 1 root root 37 Feb 28 11:10 tuned.service -> /usr/lib/systemd/system/tuned.service
lrwxrwxrwx. 1 root root 40 Feb 28 11:03 vmtoolsd.service -> /usr/lib/systemd/system/vmtoolsd.service
[root@srvcentos7 ~]# ls -l /etc/systemd/system/
basic.target.wants/                                      dbus-org.freedesktop.NetworkManager.service              getty.target.wants/                                      remote-fs.target.wants/
bluetooth.target.wants/                                  dbus-org.freedesktop.nm-dispatcher.service               graphical.target.wants/                                  sockets.target.wants/
dbus-org.bluez.service                                   default.target                                           local-fs.target.wants/                                   spice-vdagentd.target.wants/
dbus-org.fedoraproject.FirewallD1.service                default.target.wants/                                    multi-user.target.wants/                                 sysinit.target.wants/
dbus-org.freedesktop.Avahi.service                       dev-virtio\x2dports-org.qemu.guest_agent.0.device.wants/ network-online.target.wants/                             system-update.target.wants/
dbus-org.freedesktop.ModemManager1.service               display-manager.service                                  printer.target.wants/
[root@srvcentos7 ~]# ls -l /etc/systemd/system/basic.target.wants/
total 0
lrwxrwxrwx. 1 root root 41 Feb 28 11:02 firewalld.service -> /usr/lib/systemd/system/firewalld.service
lrwxrwxrwx. 1 root root 41 Feb 28 11:11 microcode.service -> /usr/lib/systemd/system/microcode.service
lrwxrwxrwx. 1 root root 42 Feb 28 12:23 rhel-dmesg.service -> /usr/lib/systemd/system/rhel-dmesg.service


Chaque service vas se basé sur le service précédent pour démmarer un eput le voir dans:

[root@srvcentos7 ~]# vi /usr/lib/systemd/system/


exemple: le mode graphique

[root@srvcentos7 ~]# cat /usr/lib/systemd/system/graphical.target
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=Graphical Interface
Documentation=man:systemd.special(7)
Requires=multi-user.target
Wants=display-manager.service
Conflicts=rescue.service rescue.target
After=multi-user.target rescue.service rescue.target display-manager.service
AllowIsolate=yes
[root@srvcentos7 ~]#

Il vas démmaré que si le mode si le mode multi utilisateur est démmaré et il vas démmaré après ce mode



- le type service : estions de démons
- le type target : regroupe plusieurs unités et permet de définir le niveau d'éxécution


-  le type mount : gestion des systèmes de fichier en relation avec fstab
-  le type snapshot : permet de sauvegarder et de restaurer l'état des services.










   
