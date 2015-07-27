<properties 
	pageTitle="Familiarisez-vous avec Azure Storage en cinq minutes | Microsoft Azure" 
	description="Développez rapidement les objets blob, les tables et les files d'attente Microsoft Azure à l'aide des rubriques de démarrage rapide Azure, de Visual Studio et de l’émulateur d’Azure Storage. Exécutez votre première application Azure Storage en cinq minutes." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="05/28/2015" 
	ms.author="tamram;selcint"/>

# Familiarisez-vous avec Azure Storage en cinq minutes 

Il est facile de commencer à développer sur Azure Storage. Ce didacticiel vous montre comment faire fonctionner rapidement une application Azure Storage. Nous allons voir deux scénarios qui vous aideront à tirer le meilleur parti d’Azure Storage :

- [Exécution locale de votre première application Azure Storage sur l’émulateur d’Azure Storage](#run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator)
- [Exécution de votre première application Azure Storage sur Azure Storage dans le cloud](#run-your-first-azure-storage-application-against-azure-storage-in-the-cloud)

Si vous souhaitez en savoir plus sur Azure Storage avant de vous plonger dans le code, consultez la page [Étapes suivantes](#next-steps).

## Configuration requise

Vérifiez que les conditions préalables suivantes sont remplies avant de commencer :

1. Pour compiler et générer l’application, [Visual Studio](https://www.visualstudio.com/) doit être installé sur votre ordinateur. 
2. Installez la dernière version du [Kit de développement logiciel (SDK) Azure pour .NET](http://azure.microsoft.com/downloads/). Le Kit de développement logiciel (SDK) inclut des exemples de projets de démarrage rapide Azure, l'émulateur de stockage Azure et la [bibliothèque cliente de stockage Azure pour .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).
3. Assurez-vous que [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) est installé sur votre ordinateur, car cette application est nécessaire aux exemples de projets de démarrage rapide Azure que nous allons utiliser dans ce didacticiel. Si vous ne savez pas quelle version de .NET Framework est installée sur votre ordinateur, consultez la rubrique [Comment déterminer les versions .NET Framework installées](https://msdn.microsoft.com/vstudio/hh925568.aspx). Sinon, cliquez sur le bouton **Démarrer** ou appuyez sur la touche Windows, et tapez **Panneau de configuration**. Ensuite, cliquez sur **Programmes** > **Programmes et fonctionnalités**, et vérifiez si .NET Framework 4.5 est répertorié parmi les programmes installés.

La dernière version des fichiers binaires de la bibliothèque cliente d’Azure Storage est disponible sur [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/).


## Exécution locale de votre première application Azure Storage sur l’émulateur d’Azure Storage

Lorsque vous développez une application qui utilise Azure Storage, vous pouvez l’exécuter dans l’[émulateur d’Azure Storage](storage-use-emulator.md). L'émulateur de stockage fournit un environnement local qui émule les services BLOB, de File d'attente et de Table Azure à des fins de développement. Vous pouvez utiliser l'émulateur de stockage pour tester votre application de stockage localement, sans souscrire d’abonnement Azure ni de compte de stockage et sans frais.

Pour l’essayer, nous allons créer une application Azure Storage simple à l’aide d’un des exemples de projet de démarrage rapide Azure dans Visual Studio. Ce didacticiel se concentre sur les exemples de projets de **stockage d’objets blob Azure**, de **tables Azure Storage** et de **files d’attente Azure Storage**.

1. Démarrez Visual Studio.
2. Dans le menu **Fichier**, cliquez sur **Nouveau projet**.
3. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Installé** > **Modèles** > **Visual C#** > **Cloud** > **Démarrages rapides** > **Services de données**.
	- 3\.a. Choisissez l’un des modèles suivants : Stockage d’objets blob Azure, de tables Azure Storage ou de files d’attente Azure Storage. 
	- 3\.b. Vérifiez que la version cible sélectionnée est **.NET Framework 4.5**.	
	- 3\.c. Donnez un nom à votre projet et créez la nouvelle solution Visual Studio, comme indiqué :
	
	![Démarrages rapides Azure][Image1]

Il peut être utile d’examiner le code source avant d'exécuter l'application. Pour ce faire, dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de solutions**. Ensuite, double-cliquez sur le fichier Program.cs.

Ensuite, exécutez l'exemple d'application dans l'émulateur d’Azure Storage :

1.	Appuyez sur le bouton **Démarrer** ou sur la touche Windows, recherchez l’*émulateur d’Azure Storage*, puis lancez l’application.
2.	Dans le menu **Générer** de Visual Studio, cliquez sur **Générer la solution**. 
3.	Dans le menu **Déboguer**, appuyez sur **F11** pour exécuter la solution étape par étape ou appuyez sur **F5** pour exécuter la solution du début à la fin.

## Exécution de votre première application Azure Storage sur Azure Storage dans le cloud

Pour exécuter votre application dans Azure Storage sur le cloud, vous aurez besoin d'un abonnement Azure et d’un compte de stockage, si vous n’en possédez pas déjà :

- Pour obtenir un abonnement Azure, consultez [Version d'évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/), [Options d'achat](http://azure.microsoft.com/pricing/purchase-options/) et [Offres spéciales membres](http://azure.microsoft.com/pricing/member-offers/) (pour les membres MSDN, Microsoft Partner Network, BizSpark et autres programmes Microsoft).
- Pour créer un compte de stockage dans Azure, consultez la page [Création, gestion ou suppression d'un compte de stockage](storage-create-storage-account.md).

Une fois votre compte créé, vous pouvez créer une application Azure Storage simple à l’aide d’un des exemples de projet de démarrage rapide Azure dans Visual Studio. Ce didacticiel se concentre sur les exemples de projets de **stockage d’objets blob Azure**, de **tables Azure Storage** et de **files d’attente Azure Storage**.

1. Démarrez Visual Studio.
2. Dans le menu **Fichier**, cliquez sur **Nouveau projet**.
3. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Installé** > **Modèles** > **Visual C#** > **Cloud** > **Démarrages rapides** > **Services de données**.
	- 3\.a. Choisissez l’un des modèles suivants : Stockage d’objets blob Azure, de tables Azure Storage ou de files d’attente Azure Storage.
	- 3\.b. Vérifiez que la version cible sélectionnée est **.NET Framework 4.5**.
	- 3\.c. Donnez un nom à votre projet et créez la nouvelle solution Visual Studio. 

Il peut être utile d’examiner le code source avant d'exécuter l'application. Pour ce faire, dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de solutions**. Ensuite, double-cliquez sur le fichier Program.cs.

Exécutez ensuite l'exemple d'application :

1.	Dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de solutions**. Double-cliquez sur le fichier App.config et supprimez les marques de commentaire de la chaîne de connexion pour le Kit de développement logiciel (SDK) de l'émulateur de stockage Azure :

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Annulez les marques de commentaire de la chaîne de connexion du service Azure Storage et indiquez le nom du compte de stockage et la clé d'accès dans le fichier App.config :`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

	Pour rechercher la clé d'accès et le nom du compte stockage, consultez [Présentation d'un compte de stockage](storage-whatis-account.md).

3.	Une fois que vous avez fourni la clé d'accès et le nom du compte de stockage dans le fichier App.config, dans le menu **Fichier**, cliquez sur **Enregistrer tout** pour enregistrer tous les fichiers du projet.
4.	Dans le menu **Générer**, cliquez sur **Générer la solution**.
5.	Dans le menu **Déboguer**, appuyez sur **F11** pour exécuter la solution étape par étape ou appuyez sur **F5** pour exécuter la solution.


## Étapes suivantes

Pour en savoir plus sur Azure Storage, consultez les ressources suivantes :

* [Introduction à Microsoft Azure Storage](storage-introduction.md)
* [Utilisation du stockage d’objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md)
* [Utilisation du stockage de tables à partir de .NET](storage-dotnet-how-to-use-tables.md)
* [Utilisation du service de stockage de files d’attente à partir de .NET](storage-dotnet-how-to-use-queues.md)
* [Documentation d’Azure Storage](http://azure.microsoft.com/documentation/services/storage/)
* [Bibliothèque cliente d’Azure Storage](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [API REST d’Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
 

<!---HONumber=July15_HO3-->