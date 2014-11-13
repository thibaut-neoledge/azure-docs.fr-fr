<properties urlDisplayName="User Names in Linux" pageTitle="S&eacute;lection de noms d'utilisateur pour Linux dans Azure" metaKeywords="" description="Apprenez &agrave; s&eacute;lectionner des noms d'utilisateur pour une machine virtuelle&nbsp;Linux dans&nbsp;Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="S&eacute;lection de noms d'utilisateur pour Linux dans Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark" />

# Sélection de noms d'utilisateur pour Linux dans Azure

Lorsque vous créez une instance de machine virtuelle Linux dans Azure, vous pouvez sélectionner le nom d'utilisateur à approvisionner (le nom d'utilisateur par défaut est *azureuser*). Dans la plupart des cas, ce nouvel utilisateur n'existera pas dans l'image de base et sera créé pendant le processus d'approvisionnement. Dans certains cas, si l'utilisateur existe déjà dans l'image de machine virtuelle de base, l'agent Linux Azure configure simplement le mot de passe (et/ou la clé SSH) pour cet utilisateur selon les données de configuration d'approvisionnement indiquées lors de la création de la machine virtuelle.

**Toutefois**, Linux définit un ensemble de noms d'utilisateur à ne pas utiliser pour la création de nouveaux utilisateurs. L'approvisionnement **échoue** si vous essayez d'approvisionner une machine virtuelle Linux en utilisant un utilisateur système existant, défini comme utilisateur avec un ID utilisateur de 0 à 99. Exemple typique : utilisateur `root` avec l'UID 0.

-   Voir aussi : [Base standard Linux : plages d'ID utilisateur][Base standard Linux : plages d'ID utilisateur]

Les listes suivantes présentent les noms d’utilisateur qui ne doivent pas être utilisés pour approvisionner une machine virtuelle Linux. Il est recommandé de **ne pas utiliser ces noms d'utilisateur** lors de l'approvisionnement d'une machine virtuelle Linux, car cela pourrait faire échouer le processus d'approvisionnement.

## openSUSE

-   abrt
-   adm
-   audio
-   bin
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   test
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

## SLES

-   audio
-   bin
-   cdrom
-   console
-   daemon
-   dialout
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   haldaemon
-   kmem
-   lp
-   lp
-   mail
-   maildrop
-   man
-   messagebus
-   modem
-   news
-   news
-   nobody
-   nogroup
-   polkituser
-   postfix
-   public
-   root
-   shadow
-   sshd
-   sys
-   test
-   trusted
-   tty
-   users
-   utmp
-   uucp
-   uuidd
-   video
-   wheel
-   www
-   wwwrun
-   xok

## CentOS

-   abrt
-   adm
-   audio
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   test
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

## Ubuntu

-   adm
-   admin
-   audio
-   backup
-   bin
-   cdrom
-   crontab
-   daemon
-   dialout
-   dip
-   disk
-   fax
-   floppy
-   fuse
-   games
-   gnats
-   irc
-   kmem
-   landscape
-   libuuid
-   list
-   lp
-   mail
-   man
-   messagebus
-   mlocate
-   netdev
-   news
-   nobody
-   nogroup
-   operator
-   plugdev
-   proxy
-   root
-   sasl
-   shadow
-   src
-   ssh
-   sshd
-   staff
-   sudo
-   sync
-   sys
-   syslog
-   tape
-   tty
-   users
-   utmp
-   uucp
-   video
-   voice
-   whoopsie
-   www-data

  [Base standard Linux : plages d'ID utilisateur]: http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html
