<properties 
	pageTitle="Prise en main des objets blob, des tables et des files d'attente Azure en 5 minutes" 
	description="Apprenez à développer rapidement les objets blob, les tables et les files d'attente Microsoft Azure à l'aide des rubriques de démarrage rapide Azure et Visual Studio." 
	services="storage" 
	documentationCenter=".net" 
	authors="Selcin" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/18/2015" 
	ms.author="selcint"/>

# Prise en main des objets blob, des tables et des files d'attente Azure en 5 minutes 

Ce didacticiel vous montre comment programmer rapidement pour des **objets blob Azure Storage**, des **tables** et des **files d'attente** en développant une application Azure simple dans Visual Studio. 

Ce didacticiel comprend deux scénarios principaux pour un démarrage rapide avec Azure Storage :

- Exécuter votre première application Azure Storage sur l'émulateur de stockage Azure
- Exécuter votre première application Azure Storage sur le service Azure Storage

Si vous souhaitez en savoir plus sur Azure Storage avant de vous plonger dans le code, consultez la page [Étapes suivantes][].

## Exécution de votre première application Azure Storage sur l'émulateur de stockage Azure

Cette section explique comment programmer pour les **objets blob Azure Storage**, les **tables** et les **files d'attente** en développant un exemple d'application qui accède à l'[émulateur de stockage Azure](https://msdn.microsoft.com/library/azure/hh403989.aspx). L'émulateur de stockage Microsoft Azure fournit un environnement local qui émule les services BLOB, de File d'attente et de Table Azure à des fins de développement. L'émulateur de stockage vous permet de tester localement votre application sur les services de stockage, et ce sans frais.

Pour terminer cette section, assurez-vous d'effectuer d'abord les tâches préalables suivantes :

1. Pour compiler et générer l'application, [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) doit être installé sur votre ordinateur. Si Visual Studio n'est pas installé, vous pouvez installer Visual Studio Express pour le Web lorsque vous installez le [Kit de développement logiciel (SDK) Azure 2.5 pour Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) ou une version ultérieure. 
2. Assurez-vous que vous disposez du [Kit de développement logiciel (SDK) Azure 2.5 pour Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) ou d'une version ultérieure sur votre ordinateur, puisqu'il inclut les exemples de projets de démarrage rapide d'Azure et la [bibliothèque cliente Azure Storage pour .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).  
3. Vérifiez si vous disposez de [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) sur votre ordinateur, car les exemples de projet du démarrage rapide Azure en ont besoin. Si vous ne savez pas quelle version de .NET Framework est installée sur votre ordinateur, consultez la rubrique [ Versions de .NET Framework installées](https://msdn.microsoft.com/vstudio/hh925568.aspx). Ou bien cliquez sur le bouton **Démarrer** ou appuyez sur la touche Windows, et tapez **panneau de configuration**. Ensuite, cliquez sur **Programmes** > **Programmes et fonctionnalités**. Vérifiez si .NET Framework 4.5 figure dans la liste des programmes installés.

Maintenant, nous allons créer une application Azure Storage simple à l'aide d'un des exemples de projet de démarrage rapide Azure dans Visual Studio. Ce didacticiel se concentre sur les exemples de projets de **stockage d'objets blob Azure**, de **tables Azure Storage** et de **files d'attente Azure Storage**. Pour chaque exemple de projet, les instructions suivantes s'appliquent, à ceci près que vous choisissez un autre modèle à l'étape 3.a :

1. Cliquez sur le bouton **Démarrer** ou appuyez sur la touche Windows, tapez Visual Studio 2013 ou Visual Studio Express 2013 pour le Web. Cliquez sur le programme pour commencer.
2. Dans le menu **Fichier**, cliquez sur **Nouveau projet**.
3. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Installé** > **Modèles** > **Visual C#** > **Cloud** > **Démarrages rapides** > **Services de données**.
	- 3.a.  Choisissez l'un des modèles suivants : Stockage d'objets blob Azure, de tables Azure Storage ou de files d'attente Azure Storage. 
	- 3.b. Vérifiez que la version cible sélectionnée est **.NET Framework 4.5**.	
	- 3.c. Selon le modèle que vous choisissez, donnez un nom à l'application, par exemple **DataBlobStorage**, **DataTableStorage** ou **DataStorageQueue**. Cliquez sur **OK**. Cette opération doit créer une solution dans Visual Studio. Consultez l'exemple dans la capture d'écran suivante :
	
	![Azure QuickStarts][Image1]

Nous vous encourageons à examiner le code source pour apprendre à programmer pour Azure Storage avant d'exécuter l'application. Pour consulter le code, dans Visual Studio, dans le menu **Affichage**, sélectionnez **Explorateur de solutions**. Ensuite, double-cliquez sur le fichier Program.cs. 

À présent, exécutez l'exemple d'application à l'aide de l'[émulateur de stockage Azure](https://msdn.microsoft.com/library/azure/hh403989.aspx) qui est installé dans le cadre du Kit de développement logiciel (SDK) Azure :

1.	Démarrez l'émulateur de stockage Azure : Cliquez sur le bouton **Démarrer** ou appuyez sur la touche Windows, et recherchez-le en tapant " émulateur de stockage Azure ". Sélectionnez-le dans la liste des applications pour le démarrer.
2.	Dans Visual Studio, dans le menu **Générer**, cliquez sur **Déployer la solution**. 
3.	Dans le menu **Déboguer**, appuyez sur **F11** pour exécuter la solution étape par étape ou appuyez sur **F5** pour exécuter la solution.

## Exécution de votre première application Azure Storage sur le service Azure Storage
Cette section explique comment programmer pour les **objets blob Azure Storage**, les **tables** et les **files d'attente** en développant un exemple d'application qui accède au [service Azure Storage](http://azure.microsoft.com/documentation/services/storage/).

Pour terminer cette section, assurez-vous d'effectuer d'abord les tâches préalables suivantes :

1. Pour compiler et générer l'application, [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) doit être installé sur votre ordinateur. Si Visual Studio n'est pas installé, vous pouvez installer Visual Studio Express pour le Web lorsque vous installez le [Kit de développement logiciel (SDK) Azure 2.5 pour Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) ou une version ultérieure. 
2. Assurez-vous que vous disposez du [Kit de développement logiciel (SDK) Azure 2.5 pour Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) ou d'une version ultérieure sur votre ordinateur, puisqu'il inclut les exemples de projets de démarrage rapide d'Azure et la [bibliothèque cliente Azure Storage pour .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).  
3. Vérifiez si vous disposez de [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) sur votre ordinateur, car les exemples de projet du démarrage rapide Azure en ont besoin. Si vous ne savez pas quelle version de .NET Framework est installée sur votre ordinateur, consultez la rubrique [ Versions de .NET Framework installées](https://msdn.microsoft.com/vstudio/hh925568.aspx). Ou bien cliquez sur le bouton **Démarrer** ou appuyez sur la touche Windows, et tapez **panneau de configuration**. Ensuite, cliquez sur **Programmes** > **Programmes et fonctionnalités**. Vérifiez si .NET Framework 4.5 figure dans la liste des programmes installés.
4.	Obtenez un abonnement Azure (si vous n'en avez pas encore) et créez aussi un compte de **stockage standard** :
	- Pour obtenir un abonnement Azure, consultez [Version d'évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/), [Options d'achat](http://azure.microsoft.com/pricing/purchase-options/) et [Offres spéciales membres](http://azure.microsoft.com/pricing/member-offers/) (pour les membres MSDN, Microsoft Partner Network, BizSpark et autres programmes Microsoft).
	- Pour créer un compte de **stockage standard** dans Azure, consultez la page [Création, gestion ou suppression d'un compte de stockage](storage-create-storage-account.md). **Remarque :** Il existe deux types de compte de stockage dans Azure : le compte de stockage Standard et le compte de stockage Premium. Le compte de stockage standard offre l'accès au stockage d'objets blob, de tables et de files d'attente Azure. Le compte de stockage Premium est actuellement disponible uniquement pour stocker des données sur les disques utilisés par les machines virtuelles Azure. Pour plus d'informations, consultez la page [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage-preview-portal.md).

Maintenant, nous allons créer une application Azure Storage simple à l'aide d'un des exemples de projet de démarrage rapide Azure dans Visual Studio. Ce didacticiel se concentre sur les exemples de projets de **stockage d'objets blob Azure**, de **tables Azure Storage** et de **files d'attente Azure Storage**. Pour chaque exemple de projet, les instructions suivantes s'appliquent, à ceci près que vous choisissez un autre modèle à l'étape 3.a :

1. Cliquez sur le bouton **Démarrer** ou appuyez sur la touche Windows, tapez Visual Studio 2013 ou Visual Studio Express 2013 pour le Web. Cliquez sur le programme pour commencer.
2. Dans le menu **Fichier**, cliquez sur **Nouveau projet**.
3. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Installé** > **Modèles** > **Visual C#** > **Cloud** > **Démarrages rapides** > **Services de données**.
	- 3.a. Choisissez l'un des modèles suivants : Stockage d'objets blob Azure, de tables Azure Storage ou de files d'attente Azure Storage. 
	- 3.b. Vérifiez que la version cible sélectionnée est **.NET Framework 4.5**.
	- 3.c. Selon le modèle que vous choisissez, donnez un nom à l'application, par exemple **DataBlobStorage**, **DataTableStorage** ou **DataStorageQueue**. Cliquez sur **OK**. Cette opération doit créer une solution dans Visual Studio. 

Nous vous encourageons à examiner le code source pour apprendre à programmer pour Azure Storage avant d'exécuter l'application. Pour consulter le code, dans Visual Studio, dans le menu **Affichage**, sélectionnez **Explorateur de solutions**. Ensuite, double-cliquez sur le fichier Program.cs. 

Exécutez ensuite l'exemple d'application :

1.	Dans Visual Studio, dans le menu **Affichage**, sélectionnez **Explorateur de solutions**. Double-cliquez sur le fichier App.config et supprimez les marques de commentaire de la chaîne de connexion pour le Kit de développement logiciel (SDK) de l'émulateur de stockage Azure : 

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Annulez les marques de commentaire de la chaîne de connexion du service Azure Storage et indiquez le nom du compte de stockage et la clé d'accès dans le fichier App.config :
	`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"` 

	Pour rechercher la clé d'accès et le nom du compte stockage, consultez [Présentation d'un compte de stockage](storage-whatis-account.md). 

3.	Une fois que vous avez fourni la clé d'accès et le nom du compte de stockage dans le fichier App.config, dans le menu **Fichier**, cliquez sur **Enregistrer tout** pour enregistrer tous les fichiers du projet. 
4.	Dans le menu **Générer**, cliquez sur **Générer la solution**. 
5.	Dans le menu **Déboguer**, appuyez sur **F11** pour exécuter la solution étape par étape ou appuyez sur **F5** pour exécuter la solution.


## Étapes suivantes
Dans ce didacticiel, vous avez appris comment programmer pour le stockage d'objets blob Azure, le stockage de tables Azure et de files d'attente de stockage Azure. 

Si vous souhaitez en savoir plus, suivez ces liens :

* [Introduction à Microsoft Azure Storage](storage-introduction.md)
* [Utilisation du stockage d'objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md)
* [Utilisation du stockage de tables à partir de .NET](storage-dotnet-how-to-use-tables.md)
* [Utilisation du service de stockage de files d'attente à partir de .NET](storage-dotnet-how-to-use-queues.md)
* [Documentation d'Azure Storage](http://azure.microsoft.com/documentation/services/storage/)
* [Référence MSDN d'Azure Storage](http://msdn.microsoft.com/library/azure/gg433040.aspx)
* [Bibliothèque cliente de stockage Azure](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [API REST d'Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png


<!--HONumber=47-->
