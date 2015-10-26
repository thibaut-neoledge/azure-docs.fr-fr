<properties 
    pageTitle="Déploiement de QuickBooks dans Azure RemoteApp | Microsoft Azure" 
    description="Découvrez comment partager QuickBooks avec Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/01/2015" 
    ms.author="elizapo" />



# Comment déployer QuickBooks dans Azure RemoteApp ?

Utilisez les informations suivantes pour partager QuickBooks comme une application dans Azure RemoteApp.


Vous pouvez partager QuickBooks 2015 Enterprise avec Azure RemoteApp dans une collection hybride ou de cloud. Le fichier d’entreprise doit résider sur une machine virtuelle qui exécute un serveur de base de données QuickBooks distinct des serveurs Azure RemoteApp. Ne stockez jamais le fichier d'entreprise sur votre image Azure RemoteApp car vous risquez de perte des données. Seul QuickBooks Enterprise prend en charge l’hébergement du fichier QuickBooks sur un partage externe avec le serveur de base de données QuickBooks accessible via des réseaux Windows standard.

> [AZURE.IMPORTANT]Le serveur de base de données QuickBooks qui héberge le fichier d’entreprise doit résider sur une machine virtuelle distincte située sur le même réseau virtuel que la collection Azure RemoteApp.

## Étapes pour déployer QuickBooks

1. Créez une machine virtuelle Azure et installez QuickBooks, le serveur de base de données QuickBooks, puis placez le fichier d'entreprise sur une machine virtuelle Azure. Veillez à configurer correctement les règles du pare-feu.
2. Installez QuickBooks sur une [image personnalisée](remoteapp-imageoptions.md) et créez une [collection Azure RemoteApp](remoteapp-collections.md) de cloud ou hybride au sein du même réseau virtuel où réside la machine virtuelle qui héberge le serveur de base de données QuickBooks contenant des fichiers d’entreprise. 
3.	[Publication](remoteapp-publish.md) d’une application QuickBooks aux utilisateurs
4.	Lancez le client QuickBooks hébergé sur Azure RemoteApp, naviguez à l'aide d'une mise en réseau Windows standard vers la machine virtuelle qui héberge le serveur de base de données QuickBooks, puis ouvrez le fichier d’entreprise. 

## Références de documentation

- QuickBooks - [Configurations prises en charge](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
- QuickBooks - [Options de déploiement](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

Vous pouvez également consulter ma présentation Ignite, [Bases de la gestion et de l’administration de Microsoft Azure RemoteApp](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) en avançant rapidement jusqu’à 1:02:45 pour accéder à la partie QuickBooks.

## Architecture de déploiement

![Déploiement de QuickBooks + Azure RemoteApp](./media/remoteapp-quickbooks/ra-quickbooks.png)

<!---HONumber=Oct15_HO3-->