<properties urlDisplayName="Install MongoDB" pageTitle="Installation de MongoDB sur une machine virtuelle Windows Server" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Apprenez à installer MongoDB sur une machine virtuelle Azure exécutant Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="kathydav" />

#Installation de MongoDB sur une machine virtuelle exécutant Windows Server

[MongoDB][MongoDB] est une base de données NoSQL libre qui offre des performances élevées.  Vous pouvez créer une machine virtuelle exécutant Windows Server à partir de la bibliothèque d'images du [portail de gestion Azure][AzureManagementPortal].  Vous pouvez alors installer et configurer une base de données MongoDB sur la machine virtuelle.

Cet article présente les opérations suivantes :

- créer, à l'aide du portail de gestion, une machine virtuelle Windows Server à partir de la galerie ;
- se connecter à la machine virtuelle via le Bureau à distance ;
- associer un disque de données à la machine virtuelle ;
- installer MongoDB sur la machine virtuelle.

## Création d'une machine virtuelle exécutant Windows Server

Vous trouverez ci-dessous des instructions générales. Vous pouvez les modifier en créant des points de terminaison afin d'autoriser l'accès à distance à MongoDB. (Vous pouvez aussi créer la machine virtuelle ultérieurement, tel que décrit après les instructions d'installation de MongoDB.) Dans la dernière page de l'Assistant, ajoutez un point de terminaison et configurez-le comme suit :

- Nommez-le **Mongo**
- Utilisez le protocole **TCP**
- Définissez les ports privé et public sur **27017**.
 
[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## Association d'un disque de données
Pour fournir un stockage pour la machine virtuelle, associez un disque de données, puis initialisez-le de façon à ce qu'il puisse être utilisé par Windows. Il est possible d'associer un disque existant si vous avez déjà des données à utiliser, ou un disque vide.

[WACOM.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

Pour des instructions sur la façon d'initialiser le disque, consultez la section " Initialisation d'un nouveau disque de données sous Windows Server " dans la rubrique [Association d'un disque de données avec une machine virtuelle](http://azure.microsoft.com/fr-fr/documentation/articles/storage-windows-attach-disk/).

## Installation et exécution de MongoDB sur la machine virtuelle 

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Résumé
Ce didacticiel vous a montré comment créer une machine virtuelle Windows Server, vous y connecter à distance et y associer un disque de données.  Vous avez également appris à installer et à configurer MongoDB sur la machine virtuelle Windows. Pour plus d'informations sur MongoDB, consultez la [documentation MongoDB][MongoDocs].

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com

<!--HONumber=35.1-->
