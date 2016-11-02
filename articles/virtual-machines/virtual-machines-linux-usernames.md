<properties 
	pageTitle="Sélection de noms d'utilisateur pour Linux | Microsoft Azure" 
	description="Apprenez à sélectionner des noms d'utilisateur pour une machine virtuelle Linux dans Azure." 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2016" 
	ms.author="szark"/>



#Sélection de noms d'utilisateur pour Linux dans Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Quand vous configurez une machine virtuelle Linux sur Azure, vous devez spécifier le nom d’utilisateur non racine que vous pourrez utiliser ultérieurement pour vous connecter à la machine virtuelle. Vous pouvez choisir le nom du nouvel utilisateur, ou en cas d’approvisionnement via le portail Azure Classic, vous pouvez accepter le nom par défaut, « azureuser ».

Dans la plupart des cas, ce nouvel utilisateur n’existe pas dans l’image de base et sera créé pendant le processus d’approvisionnement. Si l’utilisateur existe dans l’image de machine virtuelle de base, l’agent Linux Azure configure simplement le mot de passe et/ou la clé SSH pour cet utilisateur selon les informations indiquées lors de la création de la machine virtuelle.

**Toutefois**, Linux définit un ensemble de noms d’utilisateur à ne pas utiliser pour la création de nouveaux utilisateurs. Le processus d’approvisionnement **échoue** si vous essayez d’approvisionner une machine virtuelle Linux via un utilisateur système existant, défini comme utilisateur avec un ID utilisateur de 0 à 99. L’utilisateur `root`, présentant l’ID utilisateur 0, en est un bon exemple.

 - Voir aussi [Base standard Linux : plages d’ID utilisateur](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html).

Voici une liste d’utilisateurs système intégrés courants pour CentOS et Ubuntu que vous devez éviter d’utiliser lors de l’approvisionnement d’une machine virtuelle Linux sur Azure. Cette liste n’est qu’un exemple. Reportez-vous à la documentation relative à votre distribution pour vous assurer que le nom d’utilisateur que vous choisissez n’est pas en conflit avec un utilisateur système existant.


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

 

<!---HONumber=AcomDC_0713_2016-->