# TP Avancé : "Mission Ultime : Sauvegarde et Sécurisation"

## Contexte
Votre serveur critique est opérationnel, mais de nombreuses failles subsistent. Votre objectif est d'identifier les faiblesses, de sécuriser les données et d’automatiser les surveillances pour garantir un fonctionnement sûr à long terme.

---

## Objectifs
1. Surveiller les répertoires critiques pour détecter des modifications suspectes.
2. Identifier et éliminer des tâches malveillantes laissées par des attaquants.
3. Réorganiser les données pour optimiser l’espace disque avec LVM.
4. Automatiser les sauvegardes et surveillances avec des scripts robustes.
5. Configurer un pare-feu pour protéger les services actifs.

---

## Étape 1 : Analyse et nettoyage du serveur

1. **Lister les tâches cron pour détecter des backdoors** :
   - Analysez les tâches cron de tous les utilisateurs pour identifier celles qui semblent malveillantes.

```powershell
[root@vbox stark]# for user in $(cut -f1 -d: /etc/passwd); do     echo "Tâches cron pour l'utilisateur : $user";     crontab -u $user -l 2>/dev/null; done
Tâches cron pour l'utilisateur : root
Tâches cron pour l'utilisateur : bin
Tâches cron pour l'utilisateur : daemon
Tâches cron pour l'utilisateur : adm
Tâches cron pour l'utilisateur : lp
Tâches cron pour l'utilisateur : sync
Tâches cron pour l'utilisateur : shutdown
Tâches cron pour l'utilisateur : halt
Tâches cron pour l'utilisateur : mail
Tâches cron pour l'utilisateur : operator
Tâches cron pour l'utilisateur : games
Tâches cron pour l'utilisateur : ftp
Tâches cron pour l'utilisateur : nobody
Tâches cron pour l'utilisateur : tss
Tâches cron pour l'utilisateur : systemd-coredump
Tâches cron pour l'utilisateur : dbus
Tâches cron pour l'utilisateur : sssd
Tâches cron pour l'utilisateur : chrony
Tâches cron pour l'utilisateur : sshd
Tâches cron pour l'utilisateur : attacker
*/10 * * * * /tmp/.hidden_script
Tâches cron pour l'utilisateur : stark

[root@vbox stark]# crontab -u attacker -l
*/10 * * * * /tmp/.hidden_script

```
2. **Identifier et supprimer les fichiers cachés** :
   - Recherchez les fichiers cachés dans les répertoires 
   `/tmp`
```powershell 
[root@vbox stark]# cd /tmp*
[root@vbox tmp]# ls
malicious.sh
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-chronyd.service-IkysmV
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-dbus-broker.service-wCkZGU
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-irqbalance.service-LCtsMk
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-kdump.service-rTTXNS
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-systemd-logind.service-cBSTY8
[root@vbox tmp]# rm -r malicious.sh
rm: remove regular file 'malicious.sh'? yes
[root@vbox tmp]# ls
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-chronyd.service-IkysmV
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-dbus-broker.service-wCkZGU
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-irqbalance.service-LCtsMk
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-kdump.service-rTTXNS
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-systemd-logind.service-cBSTY8

[root@vbox tmp]# ls -alh
total 52K
drwxrwxrwt. 11 root     root     4.0K Nov 25 10:00 .
dr-xr-xr-x. 19 root     root     4.0K Nov 24 17:42 ..
drwxrwxrwt.  2 root     root     4.0K Nov 24 17:46 .ICE-unix
drwxrwxrwt.  2 root     root     4.0K Nov 24 17:46 .X11-unix
drwxrwxrwt.  2 root     root     4.0K Nov 24 17:46 .XIM-unix
drwxrwxrwt.  2 root     root     4.0K Nov 24 17:46 .font-unix
-rwxrwxrwx.  1 attacker attacker   18 Nov 24 18:24 .hidden_file
-rwxrwxrwx.  1 attacker attacker   17 Nov 24 18:11 .hidden_script
drwx------.  3 root     root     4.0K Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-chronyd.service-IkysmV
drwx------.  3 root     root     4.0K Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-dbus-broker.service-wCkZGU
drwx------.  3 root     root     4.0K Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-irqbalance.service-LCtsMk
drwx------.  3 root     root     4.0K Nov 25 08:39 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-kdump.service-rTTXNS
drwx------.  3 root     root     4.0K Nov 25 09:08 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-systemd-logind.service-cBSTY8

[root@vbox tmp]# rm -r .hidden_file
rm: remove regular file '.hidden_file'? y
[root@vbox tmp]# ls -alh
total 48K
drwxrwxrwt. 11 root     root     4.0K Nov 25 10:21 .
dr-xr-xr-x. 19 root     root     4.0K Nov 24 17:42 ..
drwxrwxrwt.  2 root     root     4.0K Nov 24 17:46 .ICE-unix
drwxrwxrwt.  2 root     root     4.0K Nov 24 17:46 .X11-unix
drwxrwxrwt.  2 root     root     4.0K Nov 24 17:46 .XIM-unix
drwxrwxrwt.  2 root     root     4.0K Nov 24 17:46 .font-unix
-rwxrwxrwx.  1 attacker attacker   17 Nov 24 18:11 .hidden_script
drwx------.  3 root     root     4.0K Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-chronyd.service-IkysmV
drwx------.  3 root     root     4.0K Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-dbus-broker.service-wCkZGU
drwx------.  3 root     root     4.0K Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-irqbalance.service-LCtsMk
drwx------.  3 root     root     4.0K Nov 25 08:39 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-kdump.service-rTTXNS
drwx------.  3 root     root     4.0K Nov 25 09:08 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-systemd-logind.service-cBSTY8

[root@vbox tmp]# rm -r .hidden_script
rm: remove regular file '.hidden_script'? y
[root@vbox tmp]# ls
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-chronyd.service-IkysmV
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-dbus-broker.service-wCkZGU
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-irqbalance.service-LCtsMk
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-kdump.service-rTTXNS
systemd-private-0298cf5cb5254db4952b3266ca23f9ff-systemd-logind.service-cBSTY8
```
   
, `/var/tmp`

```powershell
[root@vbox home]# cd /var
[root@vbox var]# ls
adm    crash  empty  games     lib    lock  mail  opt       run    tmp
cache  db     ftp    kerberos  local  log   nis   preserve  spool  yp
[root@vbox var]# cd tmp
[root@vbox tmp]# ls -alh
total 32K
drwxrwxrwt.  7 root     root     4.0K Nov 25 09:33 .
drwxr-xr-x. 19 root     root     4.0K Nov 24 17:46 ..
-rwxrwxrwx.  1 attacker attacker    7 Nov 24 20:10 .nop
drwx------.  3 root     root     4.0K Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-chronyd.service-Jv5PmK
drwx------.  3 root     root     4.0K Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-dbus-broker.service-iH7onQ
drwx------.  3 root     root     4.0K Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-irqbalance.service-f2bMt9
drwx------.  3 root     root     4.0K Nov 25 08:39 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-kdump.service-6r1mTI
drwx------.  3 root     root     4.0K Nov 25 09:08 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-systemd-logind.service-kQEgoT
[root@vbox tmp]# ls -al
total 32
drwxrwxrwt.  7 root     root     4096 Nov 25 09:33 .
drwxr-xr-x. 19 root     root     4096 Nov 24 17:46 ..
-rwxrwxrwx.  1 attacker attacker    7 Nov 24 20:10 .nop
drwx------.  3 root     root     4096 Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-chronyd.service-Jv5PmK
drwx------.  3 root     root     4096 Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-dbus-broker.service-iH7onQ
drwx------.  3 root     root     4096 Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-irqbalance.service-f2bMt9
drwx------.  3 root     root     4096 Nov 25 08:39 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-kdump.service-6r1mTI
drwx------.  3 root     root     4096 Nov 25 09:08 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-systemd-logind.service-kQEgoT
[root@vbox tmp]# rm -r .nop
rm: remove regular file '.nop'? yes
[root@vbox tmp]# ls -all
total 28
drwxrwxrwt.  7 root root 4096 Nov 25 10:17 .
drwxr-xr-x. 19 root root 4096 Nov 24 17:46 ..
drwx------.  3 root root 4096 Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-chronyd.service-Jv5PmK
drwx------.  3 root root 4096 Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-dbus-broker.service-iH7onQ
drwx------.  3 root root 4096 Nov 25 08:38 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-irqbalance.service-f2bMt9
drwx------.  3 root root 4096 Nov 25 08:39 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-kdump.service-6r1mTI
drwx------.  3 root root 4096 Nov 25 09:08 systemd-private-0298cf5cb5254db4952b3266ca23f9ff-systemd-logind.service-kQEgoT 

```
   
 et `/home`.

```powershell
 [root@vbox ~]# cd /home
[root@vbox home]# ls
attacker  hidden_data  stark
[root@vbox home]# rm -r hidden_data/ | rm -r attacker/
rm: descend into directory 'hidden_data/'? rm: descend into directory 'attacker/'? yes
rm: remove regular file 'hidden_data/file1.txt'?
rm: remove regular file 'hidden_data/base64_file.txt'? yes
rm: remove regular file 'hidden_data/file2.txt'? yes
rm: remove directory 'hidden_data/'? yes
rm: cannot remove 'hidden_data/': Directory not empty
[root@vbox home]# ls
attacker  hidden_data  stark
[root@vbox home]# rm -r attacker/
rm: descend into directory 'attacker/'? y
rm: remove regular file 'attacker/.bash_profile'? y
rm: remove regular file 'attacker/.bashrc'? y
rm: remove regular file 'attacker/.bash_logout'? y
rm: remove regular file 'attacker/.bash_history'? y
rm: remove regular file 'attacker/.hidden_file'? y
rm: remove directory 'attacker/'? y
[root@vbox home]# y
bash: y: command not found
[root@vbox home]# ls
hidden_data  stark
[root@vbox home]# $rm -r hidden_data/ -y
bash: -r: command not found
[root@vbox home]# $rm -r hidden_data/
bash: -r: command not found
[root@vbox home]# rm -r hidden_data/
rm: descend into directory 'hidden_data/'? -y
[root@vbox home]# ls
hidden_data  stark
[root@vbox home]# rm -r hidden_data/
rm: descend into directory 'hidden_data/'? yes
rm: remove regular file 'hidden_data/file1.txt'? yes
rm: remove directory 'hidden_data/'? yes
[root@vbox home]# ls
stark
[root@vbox home]# ls
stark
``` 

3. **Analyser les connexions réseau actives** :
   - Listez les connexions actives pour repérer d'éventuelles communications malveillantes.

```powershell 

[root@vbox home]# ss
Netid State   Recv-Q  Send-Q                  Local Address:Port          Peer Address:Port   Process
u_dgr ESTAB   0       0                 /run/systemd/notify 14467                    * 0
u_dgr ESTAB   0       0        /run/systemd/journal/dev-log 14480                    * 0
u_dgr ESTAB   0       0         /run/systemd/journal/socket 14482                    * 0
u_dgr ESTAB   0       0            /run/chrony/chronyd.sock 19765                    * 0
u_str ESTAB   0       0                                   * 21229                    * 23729
u_dgr ESTAB   0       0                                   * 24058                    * 24057
u_dgr ESTAB   0       0                                   * 19714                    * 14480
u_str ESTAB   0       0         /run/dbus/system_bus_socket 15948                    * 17969
u_str ESTAB   0       0                                   * 21269                    * 25726
u_str ESTAB   0       0                                   * 19715                    * 19716
u_str ESTAB   0       0         /run/systemd/journal/stdout 23729                    * 21229
u_dgr ESTAB   0       0                                   * 24049                    * 14482
u_str ESTAB   0       0                                   * 17888                    * 17889
u_str ESTAB   0       0         /run/dbus/system_bus_socket 19747                    * 17890
u_str ESTAB   0       0                                   * 17889                    * 17888
u_dgr ESTAB   0       0                                   * 24041                    * 14480
u_str ESTAB   0       0                                   * 27679                    * 27678
u_str ESTAB   0       0         /run/dbus/system_bus_socket 25726                    * 21269
u_str ESTAB   0       0                                   * 17891                    * 13655
u_str ESTAB   0       0                                   * 17717                    * 15872
u_dgr ESTAB   0       0                                   * 27673                    * 14480
u_str ESTAB   0       0                                   * 607                      * 19745
u_str ESTAB   0       0                                   * 20447                    * 0
u_str ESTAB   0       0         /run/systemd/journal/stdout 13644                    * 17876
u_str ESTAB   0       0         /run/systemd/journal/stdout 15872                    * 17717
u_str ESTAB   0       0                                   * 19716                    * 19715
u_str ESTAB   0       0                                   * 20797                    * 19767
u_dgr ESTAB   0       0                                   * 14468                    * 14469
u_str ESTAB   0       0                                   * 17969                    * 15948
u_dgr ESTAB   0       0                                   * 14783                    * 14467
u_str ESTAB   0       0         /run/dbus/system_bus_socket 19745                    * 607
u_str ESTAB   0       0                                   * 17890                    * 19747
u_str ESTAB   0       0                                   * 17876                    * 13644
u_dgr ESTAB   0       0                                   * 14469                    * 14468
u_str ESTAB   0       0                                   * 27678                    * 27679
u_dgr ESTAB   0       0                                   * 24057                    * 24058
u_str ESTAB   0       0         /run/systemd/journal/stdout 19767                    * 20797
u_str ESTAB   0       0         /run/systemd/journal/stdout 13655                    * 17891
u_str ESTAB   0       0                                   * 16203                    * 831
u_str ESTAB   0       0         /run/systemd/journal/stdout 18016                    * 16052
u_str ESTAB   0       0         /run/systemd/journal/stdout 21349                    * 24006
u_str ESTAB   0       0                                   * 13840                    * 13841
u_str ESTAB   0       0         /run/dbus/system_bus_socket 22841                    * 24061
u_dgr ESTAB   0       0                                   * 899                      * 14480
u_str ESTAB   0       0                                   * 15032                    * 18008
u_dgr ESTAB   0       0                                   * 16894                    * 14482
u_dgr ESTAB   0       0                                   * 18045                    * 14480
u_str ESTAB   0       0                                   * 15028                    * 16042
u_str ESTAB   0       0                                   * 13690                    * 19775
u_str ESTAB   0       0                                   * 13841                    * 13840
u_dgr ESTAB   0       0                                   * 16222                    * 16223
u_str ESTAB   0       0         /run/dbus/system_bus_socket 16042                    * 15028
u_dgr ESTAB   0       0                                   * 16223                    * 16222
u_dgr ESTAB   0       0                                   * 17886                    * 14482
u_dgr ESTAB   0       0                                   * 17729                    * 14482
u_str ESTAB   0       0                                   * 24061                    * 22841
u_str ESTAB   0       0         /run/systemd/journal/stdout 831                      * 16203
u_dgr ESTAB   0       0                                   * 25876                    * 14480
u_dgr ESTAB   0       0                                   * 21031                    * 14480
u_dgr ESTAB   0       0                                   * 13680                    * 14480
u_dgr ESTAB   0       0                                   * 615                      * 616
u_str ESTAB   0       0                                   * 24006                    * 21349
u_str ESTAB   0       0         /run/systemd/journal/stdout 18008                    * 15032
u_str ESTAB   0       0                                   * 16226                    * 13953
u_dgr ESTAB   0       0                                   * 17352                    * 14480
u_str ESTAB   0       0         /run/dbus/system_bus_socket 19775                    * 13690
u_dgr ESTAB   0       0                                   * 21254                    * 14482
u_dgr ESTAB   0       0                                   * 16214                    * 14482
u_dgr ESTAB   0       0                                   * 17733                    * 17734
u_dgr ESTAB   0       0                                   * 17734                    * 17733
u_dgr ESTAB   0       768                                 * 616                      * 615
u_str ESTAB   0       0         /run/dbus/system_bus_socket 13953                    * 16226
u_dgr ESTAB   0       0                                   * 18017                    * 14480
u_str ESTAB   0       0                                   * 16052                    * 18016
icmp6 UNCONN  0       0                                   *:ipv6-icmp                *:*
icmp6 UNCONN  0       0                                   *:ipv6-icmp                *:*
udp   ESTAB   0       0               192.168.56.104%enp0s8:bootpc      192.168.56.100:bootps
udp   ESTAB   0       0                    10.0.2.15%enp0s3:bootpc            10.0.2.2:bootps
tcp   ESTAB   0       52                     192.168.56.104:ssh           192.168.56.1:50486

```
---

## Étape 2 : Configuration avancée de LVM

1. **Créer un snapshot de sécurité pour `/mnt/secure_data`** :
   - Prenez un snapshot du volume logique `secure_data`.
```powershell 

   [root@vbox home]# sudo lvcreate --size 500.00mo --snapshot --name snap /dev/vg_secure/secure_data
  Logical volume "snap" created.
  Logical volume "snap" created.
```

2. **Tester la restauration du snapshot** :
   - Supprimez un fichier dans `/mnt/secure_data`.
   - Montez le snapshot et restaurez le fichier supprimé.

```powershell 

[root@vbox home]# cd /mnt/secure_data/
[root@vbox secure_data]# ls
lost+found  sensitive1.txt  sensitive2.txt
[root@vbox secure_data]# rm /mnt/secure_data/
lost+found/     sensitive1.txt  sensitive2.txt
[root@vbox secure_data]# rm /mnt/secure_data/sensitive2.txt
rm: remove regular file '/mnt/secure_data/sensitive2.txt'? y
[root@vbox secure_data]# cd
[root@vbox ~]# sudo mkdir /mnt/snapshot
[root@vbox ~]# sudo mount /dev/vg_secure/snap /mnt/snapshot
[root@vbox ~]#  cp /mnt/snapshot/sensitive2.txt /mnt/secure_data/
[root@vbox ~]#  sudo umount /mnt/snapshot/

  ```
3. **Optimiser l’espace disque** :
   - Si le volume logique `secure_data` est plein, étendez-le en ajoutant de l’espace à partir du groupe de volumes existant.
   ```powershell 
   [root@vbox ~]#  sudo vgdisplay
  --- Volume group ---
  VG Name               rl_vbox
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <4.00 GiB
  PE Size               4.00 MiB
  Total PE              1023
  Alloc PE / Size       1023 / <4.00 GiB
  Free  PE / Size       0 / 0
  VG UUID               HWHxR3-TW7t-BYFQ-bfPD-Mit8-Z9Ib-JSin2v

  --- Volume group ---
  VG Name               vg_secure
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  4
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               1
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               1020.00 MiB
  PE Size               4.00 MiB
  Total PE              255
  Alloc PE / Size       250 / 1000.00 MiB
  Free  PE / Size       5 / 20.00 MiB
  VG UUID               R7CdDl-kXfd-bMBh-4uqq-7YER-MErd-56VmQX
    [root@vbox ~]# lvextend --size +100Mo /dev/vg_secure/secure_data
  Snapshot origin volumes can be resized only while inactive: try lvchange -an.
  ```

---

## Étape 3 : Automatisation avec un script de sauvegarde

1. **Créer un script `secure_backup.sh`** :
   - Archive le contenu de `/mnt/secure_data` dans `/backup/secure_data_YYYYMMDD.tar.gz`.
   - Exclut les fichiers temporaires (.tmp, .log) et les fichiers cachés.
```powershell

 [root@vbox ~]# sudo nano /usr/local/bin/secure_backup.sh
 [contenu du nano]
  GNU nano 5.6.1                               /usr/local/bin/secure_backup.sh                                          #!/bin/bash

#Variables
SOURCEDIR="/mnt/secure_data"
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d%H%M)
BACKUPFILE="$BACKUP_DIR/secure_data$DATE.tar.gz"
MAX_BACKUPS=7
LOG_FILE="/var/log/backup.log"

#Création de l'archive en excluant les fichiers temporaires et cachés
tar --exclude='.tmp' --exclude='.log' --exclude='.*' -czf "$BACKUP_FILE" -C "$SOURCE_DIR" . >> "$LOG_FILE" 2>&1

#Fonction de rotation des sauvegardes
rotate_backups() {
    cd "$BACKUP_DIR" || exit
    if [ $(ls -1 | wc -l) -gt $MAX_BACKUPS ]; then
        ls -tp | grep -v '/$' | tail -n +$((MAX_BACKUPS + 1)) | xargs -I {} rm -- {}
    fi
}

#Appel de la fonction de rotation
rotate_backups

echo "Sauvegarde créée : $BACKUP_FILE" >> "$LOG_FILE"
```

3. **Testez le script** :
   - Exécutez le script manuellement et vérifiez que les archives sont créées correctement.
   ```powershell
   [root@vbox bin]# ./secure_backup.sh
   ```
4. **Automatisez avec une tâche cron** :
   - Planifiez le script pour qu’il s’exécute tous les jours à 3h du matin.
```powershell
[root@vbox bin]# crontab -l
(contenu du fichier)
0 3 * * * /chemin/vers/secure_backup.sh >> /var/log/secure_backup.log 2>&0
```

## Étape 4 : Surveillance avancée avec `auditd`

1. **Configurer auditd pour surveiller `/etc`** :
   - Ajoutez une règle avec `auditctl` pour surveiller toutes les modifications dans `/etc`.

   ```powershell

   [root@vbox stark]# sudo nano /etc/audit/rules.d/audit.rules
   (ligne ajouté dans le nano : -w /etc -p wa -k surveillance_etc)
   
    ```

2. **Tester la surveillance** :
   - Créez ou modifiez un fichier dans `/etc` et vérifiez que l’événement est enregistré dans les logs d’audit.
```powershell
[root@vbox stark]# sudo touch /etc/test_auditd_file
[root@vbox stark]# sudo chmod 600 /etc/test_auditd_file
[root@vbox stark]# sudo ausearch -k surveillance_etc
----
time->Mon Nov 25 18:49:24 2024
type=PROCTITLE msg=audit(1732556964.363:103): proctitle=617564697463746C002D77002F657463002D70007761002D6B007375727665696C6C616E63655F657463
type=SYSCALL msg=audit(1732556964.363:103): arch=c000003e syscall=44 success=yes exit=1076 a0=4 a1=7fffc1e60fe0 a2=434 a3=0 items=0 ppid=1527 pid=1529 auid=1001 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="auditctl" exe="/usr/sbin/auditctl" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key=(null)
type=CONFIG_CHANGE msg=audit(1732556964.363:103): auid=1001 ses=3 subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 op=add_rule key="surveillance_etc" list=4 res=1
----
time->Mon Nov 25 19:07:05 2024
type=PROCTITLE msg=audit(1732558025.112:118): proctitle=6E616E6F002F6574632F61756469742F72756C65732E642F61756469742E72756C6573
type=PATH msg=audit(1732558025.112:118): item=1 name="/etc/audit/rules.d/audit.rules" inode=28415 dev=fd:00 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:auditd_etc_t:s0 nametype=NORMAL cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=PATH msg=audit(1732558025.112:118): item=0 name="/etc/audit/rules.d/" inode=28325 dev=fd:00 mode=040750 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:auditd_etc_t:s0 nametype=PARENT cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=CWD msg=audit(1732558025.112:118): cwd="/home/stark"
type=SYSCALL msg=audit(1732558025.112:118): arch=c000003e syscall=257 success=yes exit=3 a0=ffffff9c a1=5573b4ebba00 a2=241 a3=1b6 items=2 ppid=1581 pid=1583 auid=1001 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="nano" exe="/usr/bin/nano" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="surveillance_etc"
----
time->Mon Nov 25 19:13:46 2024
type=PROCTITLE msg=audit(1732558426.757:141): proctitle=746F756368002F6574632F746573745F6175646974645F66696C65
type=PATH msg=audit(1732558426.757:141): item=1 name="/etc/test_auditd_file" inode=20772 dev=fd:00 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:etc_t:s0 nametype=CREATE cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=PATH msg=audit(1732558426.757:141): item=0 name="/etc/" inode=18 dev=fd:00 mode=040755 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:etc_t:s0 nametype=PARENT cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=CWD msg=audit(1732558426.757:141): cwd="/home/stark"
type=SYSCALL msg=audit(1732558426.757:141): arch=c000003e syscall=257 success=yes exit=3 a0=ffffff9c a1=7ffc6b7158e2 a2=941 a3=1b6 items=2 ppid=1614 pid=1616 auid=1001 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="surveillance_etc"
----
time->Mon Nov 25 19:13:55 2024
type=PROCTITLE msg=audit(1732558435.381:154): proctitle=746F756368002F6574632F746573745F6175646974645F66696C65
type=PATH msg=audit(1732558435.381:154): item=1 name="/etc/test_auditd_file" inode=20772 dev=fd:00 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:etc_t:s0 nametype=NORMAL cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=PATH msg=audit(1732558435.381:154): item=0 name="/etc/" inode=18 dev=fd:00 mode=040755 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:etc_t:s0 nametype=PARENT cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=CWD msg=audit(1732558435.381:154): cwd="/home/stark"
type=SYSCALL msg=audit(1732558435.381:154): arch=c000003e syscall=257 success=yes exit=3 a0=ffffff9c a1=7ffff4ef58e2 a2=941 a3=1b6 items=2 ppid=1620 pid=1622 auid=1001 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="surveillance_etc"
----
time->Mon Nov 25 19:14:27 2024
type=PROCTITLE msg=audit(1732558467.749:161): proctitle=63686D6F6400363030002F6574632F746573745F6175646974645F66696C65
type=PATH msg=audit(1732558467.749:161): item=0 name="/etc/test_auditd_file" inode=20772 dev=fd:00 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:etc_t:s0 nametype=NORMAL cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=CWD msg=audit(1732558467.749:161): cwd="/home/stark"
type=SYSCALL msg=audit(1732558467.749:161): arch=c000003e syscall=268 success=yes exit=0 a0=ffffff9c a1=5579fcbe6c50 a2=180 a3=0 items=1 ppid=1623 pid=1625 auid=1001 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="chmod" exe="/usr/bin/chmod" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="surveillance_etc"
```
3. **Analyser les événements** :
   - Recherchez les événements associés à la règle configurée et exportez les logs filtrés dans `/var/log/audit_etc.log`.
```powershell
[root@vbox stark]# sudo ausearch -k surveillance_etc > /var/log/audit_etc.log
[root@vbox stark]# cat /var/log/audit_etc.log
----
time->Mon Nov 25 18:49:24 2024
type=PROCTITLE msg=audit(1732556964.363:103): proctitle=617564697463746C002D77002F657463002D70007761002D6B007375727665696C6C616E63655F657463
type=SYSCALL msg=audit(1732556964.363:103): arch=c000003e syscall=44 success=yes exit=1076 a0=4 a1=7fffc1e60fe0 a2=434 a3=0 items=0 ppid=1527 pid=1529 auid=1001 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="auditctl" exe="/usr/sbin/auditctl" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key=(null)
type=CONFIG_CHANGE msg=audit(1732556964.363:103): auid=1001 ses=3 subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 op=add_rule key="surveillance_etc" list=4 res=1
----
time->Mon Nov 25 19:07:05 2024
type=PROCTITLE msg=audit(1732558025.112:118): proctitle=6E616E6F002F6574632F61756469742F72756C65732E642F61756469742E72756C6573
type=PATH msg=audit(1732558025.112:118): item=1 name="/etc/audit/rules.d/audit.rules" inode=28415 dev=fd:00 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:auditd_etc_t:s0 nametype=NORMAL cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=PATH msg=audit(1732558025.112:118): item=0 name="/etc/audit/rules.d/" inode=28325 dev=fd:00 mode=040750 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:auditd_etc_t:s0 nametype=PARENT cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=CWD msg=audit(1732558025.112:118): cwd="/home/stark"
type=SYSCALL msg=audit(1732558025.112:118): arch=c000003e syscall=257 success=yes exit=3 a0=ffffff9c a1=5573b4ebba00 a2=241 a3=1b6 items=2 ppid=1581 pid=1583 auid=1001 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="nano" exe="/usr/bin/nano" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="surveillance_etc"
----
time->Mon Nov 25 19:13:46 2024
type=PROCTITLE msg=audit(1732558426.757:141): proctitle=746F756368002F6574632F746573745F6175646974645F66696C65
type=PATH msg=audit(1732558426.757:141): item=1 name="/etc/test_auditd_file" inode=20772 dev=fd:00 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:etc_t:s0 nametype=CREATE cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=PATH msg=audit(1732558426.757:141): item=0 name="/etc/" inode=18 dev=fd:00 mode=040755 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:etc_t:s0 nametype=PARENT cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=CWD msg=audit(1732558426.757:141): cwd="/home/stark"
type=SYSCALL msg=audit(1732558426.757:141): arch=c000003e syscall=257 success=yes exit=3 a0=ffffff9c a1=7ffc6b7158e2 a2=941 a3=1b6 items=2 ppid=1614 pid=1616 auid=1001 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="surveillance_etc"
----
time->Mon Nov 25 19:13:55 2024
type=PROCTITLE msg=audit(1732558435.381:154): proctitle=746F756368002F6574632F746573745F6175646974645F66696C65
type=PATH msg=audit(1732558435.381:154): item=1 name="/etc/test_auditd_file" inode=20772 dev=fd:00 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:etc_t:s0 nametype=NORMAL cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=PATH msg=audit(1732558435.381:154): item=0 name="/etc/" inode=18 dev=fd:00 mode=040755 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:etc_t:s0 nametype=PARENT cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=CWD msg=audit(1732558435.381:154): cwd="/home/stark"
type=SYSCALL msg=audit(1732558435.381:154): arch=c000003e syscall=257 success=yes exit=3 a0=ffffff9c a1=7ffff4ef58e2 a2=941 a3=1b6 items=2 ppid=1620 pid=1622 auid=1001 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="surveillance_etc"
----
time->Mon Nov 25 19:14:27 2024
type=PROCTITLE msg=audit(1732558467.749:161): proctitle=63686D6F6400363030002F6574632F746573745F6175646974645F66696C65
type=PATH msg=audit(1732558467.749:161): item=0 name="/etc/test_auditd_file" inode=20772 dev=fd:00 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:etc_t:s0 nametype=NORMAL cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0
type=CWD msg=audit(1732558467.749:161): cwd="/home/stark"
type=SYSCALL msg=audit(1732558467.749:161): arch=c000003e syscall=268 success=yes exit=0 a0=ffffff9c a1=5579fcbe6c50 a2=180 a3=0 items=1 ppid=1623 pid=1625 auid=1001 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="chmod" exe="/usr/bin/chmod" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="surveillance_etc"
```

## Étape 5 : Sécurisation avec Firewalld

1. **Configurer un pare-feu pour SSH et HTTP/HTTPS uniquement** :
   - Autorisez uniquement les ports nécessaires pour SSH et HTTP/HTTPS.
   - Bloquez toutes les autres connexions.
   ```powershell
   [root@vbox stark]# sudo systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
     Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; preset: enabled)
     Active: active (running) since Mon 2024-11-25 18:43:25 CET; 49min ago
       Docs: man:firewalld(1)
   Main PID: 850 (firewalld)
      Tasks: 2 (limit: 48902)
     Memory: 45.7M
        CPU: 1.015s
     CGroup: /system.slice/firewalld.service
             └─850 /usr/bin/python3 -s /usr/sbin/firewalld --nofork --nopid

Nov 25 18:43:24 localhost systemd[1]: Starting firewalld - dynamic firewall daemon...
Nov 25 18:43:25 localhost systemd[1]: Started firewalld - dynamic firewall daemon.
[root@vbox stark]# sudo firewall-cmd --permanent --add-service=ssh
Warning: ALREADY_ENABLED: ssh
success
[root@vbox stark]# sudo firewall-cmd --permanent --add-service=http
success
[root@vbox stark]# sudo firewall-cmd --permanent --add-service=https
Warning: ALREADY_ENABLED: https
success
[root@vbox stark]# sudo firewall-cmd --set-default-zone=drop
success
[root@vbox stark]# sudo firewall-cmd --reload
success
[root@vbox stark]# firewall-cmd --list-all
drop (active)
  target: DROP
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services:
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
[root@vbox stark]# sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.100" reject'
success
[root@vbox stark]# sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" reject'
success
[root@vbox stark]# sudo firewall-cmd --reload
success
[root@vbox stark]# sudo firewall-cmd --list-rich-rules
rule family="ipv4" source address="192.168.1.0/24" reject
rule family="ipv4" source address="192.168.1.100" reject
[root@vbox stark]#  firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" service name="ssh" accept'                                                                                                     success
[root@vbox stark]# firewall-cmd --permanent --add-rich-rule='rule family="ipv4" service name="ssh" reject'
success
[root@vbox stark]# sudo firewall-cmd --reload
success
[root@vbox stark]# sudo firewall-cmd --list-rich-rules
rule family="ipv4" source address="192.168.1.0/24" service name="ssh" accept
rule family="ipv4" source address="192.168.1.0/24" reject
rule family="ipv4" source address="192.168.1.100" reject
rule family="ipv4" service name="ssh" reject


2. **Bloquer des IP suspectes** :
   - À l’aide des logs d’audit et des connexions réseau, bloquez les adresses IP malveillantes identifiées.

3. **Restreindre SSH à un sous-réseau spécifique** :
   - Limitez l’accès SSH à votre réseau local uniquement (par exemple, 192.168.x.x).

---
