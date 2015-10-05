<properties 
	pageTitle="Sélection de noms d'utilisateur pour Linux | Microsoft Azure" 
	description="Apprenez à sélectionner des noms d'utilisateur pour une machine virtuelle Linux dans Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="szark"/>



#Sélection de noms d'utilisateur pour Linux dans Azure#

Quand vous configurez une machine virtuelle Linux sur Azure, vous devez spécifier le nom d’utilisateur non racine que vous pourrez utiliser ultérieurement pour vous connecter à la machine virtuelle. Vous pouvez choisir le nom du nouvel utilisateur, ou en cas d’approvisionnement via le portail de gestion, vous pouvez accepter le nom par défaut, « azureuser ».

Dans la plupart des cas, ce nouvel utilisateur n’existe pas dans l’image de base et est créé pendant le processus d’approvisionnement. Si l'utilisateur existe dans l'image de machine virtuelle de base, l'agent Linux Azure configure simplement le mot de passe (et/ou la clé SSH) pour cet utilisateur selon les informations indiquées lors de la création de la machine virtuelle.

**Toutefois**, Linux définit un ensemble de noms d’utilisateur à ne pas utiliser pour la création de nouveaux utilisateurs. Le processus d’approvisionnement **échoue** si vous essayez d’approvisionner une machine virtuelle Linux via un utilisateur système existant, défini comme utilisateur avec un ID utilisateur de 0 à 99. L’utilisateur `root`, présentant l’ID utilisateur 0, en est un bon exemple.

 - Voir aussi [Base standard Linux : plages d’ID utilisateur](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html).

Voici les noms d’utilisateurs que vous ne devez pas utiliser pour approvisionner une machine virtuelle Linux. Nous vous recommandons de **ne pas utiliser ces noms d’utilisateur**, car le processus d’approvisionnement de machine virtuelle risquerait d’échouer.


## openSUSE
- abrt
- adm
- audio
- bin
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## SLES
- audio
- bin
- cdrom
- console
- daemon
- dialout
- disk
- floppy
- ftp
- ftp
- games
- haldaemon
- kmem
- lp
- lp
- mail
- maildrop
- man
- messagebus
- modem
- news
- news
- nobody
- nogroup
- polkituser
- postfix
- public
- root
- shadow
- sshd
- sys
- test
- trusted
- tty
- users
- utmp
- uucp
- uuidd
- video
- wheel
- www
- wwwrun
- xok

 
## CentOS
- abrt
- adm
- audio
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## Ubuntu
- adm
- admin
- audio
- backup
- bin
- cdrom
- crontab
- daemon
- dialout
- dip
- disk
- fax
- floppy
- fuse
- games
- gnats
- irc
- kmem
- landscape
- libuuid
- list
- lp
- mail
- man
- messagebus
- mlocate
- netdev
- news
- nobody
- nogroup
- operator
- plugdev
- proxy
- root
- sasl
- shadow
- src
- ssh
- sshd
- staff
- sudo
- sync
- sys
- syslog
- tape
- tty
- users
- utmp
- uucp
- video
- voice
- whoopsie
- www-data

 

<!---HONumber=Sept15_HO4-->