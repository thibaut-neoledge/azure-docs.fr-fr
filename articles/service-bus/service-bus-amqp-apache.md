<properties 
   pageTitle="Comment installer Apache Qpid Proton-C sur une machine virtuelle Linux | Microsoft Azure"
   description="Comment créer une machine virtuelle CentOS Linux à l'aide de machines virtuelles Azure et comment générer et installer la bibliothèque Apache Qpid Proton-C."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="sethm" />

# Installation d’Apache Qpid Proton-C sur une machine virtuelle Linux Azure

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Cette section montre comment créer une machine virtuelle Linux CentOS à l'aide de machines virtuelles Azure et comment télécharger, créer et installer la bibliothèque Apache Qpid Proton-C avec des liaisons de langage Python et PHP. Après avoir effectué ces étapes, vous pourrez exécuter les exemples Python et PHP inclus dans ce guide.

La première étape s’effectue à l'aide du [portail Azure][]. La capture d'écran suivante montre comment créer une machine virtuelle CentOS nommée « scott-centos » :

![Proton sur une machine virtuelle Linux Azure][0]

Après l’approvisionnement, le portail affiche les informations suivantes :

![Proton sur une machine virtuelle Linux Azure][1]

Pour vous connecter à l'ordinateur, vous devez connaître le port de point de terminaison pour SSH. Vous pouvez obtenir cette valeur à partir du portail en sélectionnant la machine virtuelle nouvellement créée puis en cliquant sur l’onglet **Points de terminaison**. La capture d'écran suivante montre que le port SSH public pour cet ordinateur est 57146.

![Proton sur une machine virtuelle Linux Azure][2]

Vous pouvez maintenant vous connecter à l'ordinateur à l'aide de SSH. Cet exemple utilise l'outil PuTTY, comme le montre la capture d'écran suivante :

![Proton sur une machine virtuelle Linux Azure][3]

Pour les applications Python et PHP, cet exemple utilise les bibliothèques clientes Proton d'Apache. Ces bibliothèques sont disponibles en téléchargement sur [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Le fichier Lisez-moi du package de distribution explique les étapes requises pour installer les dépendances et créer Proton. Voici un résumé des étapes :

1.  Modifiez le fichier de configuration yum (/etc/YUM.conf) et commentez l'exclusion des mises à jour des en-têtes de noyau (# exclude = kernel*). Cette étape est nécessaire pour installer le compilateur gcc.

2.  Installez les packages requis :

	```
	# required dependencies 
	yum install gcc cmake libuuid-devel
	
	# dependencies needed for ssl support
	yum install openssl-devel
	
	# dependencies needed for bindings
	yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
	
	# dependencies needed for python docs
	yum install epydoc
	```

1.  Téléchargez la bibliothèque Proton :

	```
	[azureuser@this-user ~]$ wget http://www.bizdirusa.com/mirrors/apache/qpid/proton/0.4/qpid-proton-0.4.tar.gz 
		--2013-05-23 21:27:55-- http://www.bizdirusa.com/mirrors/apache/qpid/proton/0.4/qpid-proton-0.4.tar.gz 
		Resolving www.bizdirusa.com... 205.186.175.195 
		Connecting to www.bizdirusa.com|205.186.175.195|:80... connected. 
		HTTP request sent, awaiting response... 200 OK 
		Length: 456693 (446K) [application/x-gzip] 
		Saving to: âqpid-proton-0.4.tar.gzâ

		100%[======================================>] 456,693 --.-K/s in 0.06s

		2015-05-23 21:27:55 (6.84 MB/s) - qpid-proton-0.4.tar.gz
	```

1.  Extrayez le code Proton de l'archive de distribution :

	```
	tar xvfz qpid-proton-0.4.tar.gz
	```

1.  Générez et installez le code à l'aide de la procédure suivante, extraite du fichier Lisez-moi :

	```
	From the directory where you found this README file:	
	
	mkdir build cd build
			
	# Set the install prefix. You may need to adjust depending on your		
	# system.		
	cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
			
	# Omit the docs target if you do not wish to build or install		
	# documentation.		
	make all docs
			
	# Note that this step will require root privileges.		
	make install
	```

Une fois ces étapes effectuées, Proton est installé sur l'ordinateur et prêt à être utilisé.

## Étapes suivantes

Prêt à en savoir plus ? Visitez les liens suivants :

- [Vue d’ensemble d’AMQP de Service Bus]

[Vue d’ensemble d’AMQP de Service Bus]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[portail Azure]: http://manage.windowsazure.com

<!---HONumber=Oct15_HO3-->