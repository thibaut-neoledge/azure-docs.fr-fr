<properties
	pageTitle="Installation de MongoDB sur une machine virtuelle Windows Server"
	description="Apprenez à installer MongoDB sur une machine virtuelle Azure exécutant Windows Server."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="dkshir"/>

#Installation de MongoDB sur une machine virtuelle exécutant Windows Server

[MongoDB][MongoDB] est une base de données NoSQL libre qui offre des performances élevées. Vous pouvez créer une machine virtuelle exécutant Windows Server à partir de la bibliothèque d'images du [portail de gestion Azure][AzureManagementPortal]. Vous pouvez alors installer et configurer une base de données MongoDB sur la machine virtuelle.


## Création d’une machine virtuelle exécutant Windows Server

Pour créer une machine virtuelle, procédez comme suit :

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

> [AZURE.NOTE]Vous pouvez ajouter un point de terminaison pour MongoDB lors de la création de l’ordinateur virtuel et le configurer : attribuez-lui le nom **Mongo**, utilisez**TCP**comme protocole et attribuez aux ports publics et privés la valeur **27017**.

## Association d’un disque de données
Pour fournir un stockage pour la machine virtuelle, associez un disque de données, puis initialisez-le de façon à ce qu'il puisse être utilisé par Windows. Il est possible d’associer un disque existant si vous avez déjà des données à utiliser, ou un disque vide.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Pour obtenir des instructions sur l’initialisation du disque, consultez la page « Initialisation d’un nouveau disque de données sous Windows Server », sous [Rattachement d’un disque de données à une machine virtuelle Windows](storage-windows-attach-disk.md).

## Installation et exécution de MongoDB sur la machine virtuelle

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Résumé
Ce didacticiel vous a montré comment créer une machine virtuelle Windows Server, vous y connecter à distance et y associer un disque de données. Vous avez également appris à installer et à configurer MongoDB sur la machine virtuelle Windows. Vous pouvez maintenant accéder à MongoDB sur la machine virtuelle Windows en suivant les rubriques avancées dans [Documentation MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com

<!---HONumber=July15_HO3-->