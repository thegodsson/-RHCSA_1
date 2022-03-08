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


F) Propiété des fichiers

G) Les droits d'accès

H) Rechercher des fichiers

