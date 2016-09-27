<properties
	pageTitle="Familiarisez-vous avec Azure Storage en cinq minutes | Microsoft Azure"
	description="Développez rapidement les objets blob, les tables et les files d'attente Microsoft Azure à l'aide des rubriques de démarrage rapide Azure, de Visual Studio et de l’émulateur d’Azure Storage. Exécutez votre première application Azure Storage en cinq minutes."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="09/20/2016"
	ms.author="dineshm;tamram"/>

# Familiarisez-vous avec Azure Storage en cinq minutes

## Vue d'ensemble

Il est facile de commencer à développer avec Azure Storage. Ce didacticiel vous montre comment faire fonctionner rapidement une application Azure Storage. Vous allez utiliser les modèles Démarrage rapide inclus dans le Kit de développement logiciel (SDK) Azure pour .NET Ces modèles contiennent du code prêt à l’exécution qui illustre des scénarios de programmation de base avec Azure Storage.

Pour en savoir plus sur Azure Storage avant de vous plonger dans le code, consultez la page [Étapes suivantes](#next-steps).

## Composants requis

Vérifiez que les conditions préalables suivantes sont remplies avant de commencer :

1. Pour compiler et générer l’application, [Visual Studio](https://www.visualstudio.com/) doit être installé sur votre ordinateur.

2. Installez la dernière version du [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/). Le Kit de développement logiciel (SDK) inclut des exemples de projets de démarrage rapide Azure, l'émulateur de stockage Azure et la [bibliothèque cliente de stockage Azure pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Assurez-vous que [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) est installé sur votre ordinateur, car cette application est nécessaire aux exemples de projets de démarrage rapide Azure que nous allons utiliser dans ce didacticiel.

	Si vous ne savez pas quelle version de .NET Framework est installée sur votre ordinateur, consultez la rubrique [Comment déterminer les versions .NET Framework installées](https://msdn.microsoft.com/vstudio/hh925568.aspx). Sinon, cliquez sur le bouton **Démarrer** ou appuyez sur la touche Windows, et tapez **Panneau de configuration**. Ensuite, cliquez sur **Programmes** > **Programmes et fonctionnalités**, et vérifiez si .NET Framework 4.5 est répertorié parmi les programmes installés.

4. Vous aurez besoin d’un abonnement Azure et d’un compte de stockage Azure.

    - Pour obtenir un abonnement Azure, consultez [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/), [Options d’achat](https://azure.microsoft.com/pricing/purchase-options/) et [Offres spéciales membres](https://azure.microsoft.com/pricing/member-offers/) (pour les membres de MSDN, Microsoft Partner Network, BizSpark et des autres programmes Microsoft).
    - Pour créer un compte de stockage dans Azure, consultez la page [Création d’un compte de stockage](storage-create-storage-account.md#create-a-storage-account).

## Exécution de votre première application Azure Storage sur Azure Storage dans le cloud

Une fois votre compte créé, vous pouvez créer une application Azure Storage simple à l’aide d’un des exemples de projet de démarrage rapide Azure dans Visual Studio. Ce didacticiel se concentre sur les exemples de projets pour Azure Storage : **Azure Storage : Blobs**, **Azure Storage : Fichiers**, **Azure Storage : Files d’attente** et **Azure Storage : Tables** :

1. Démarrez Visual Studio.
2. Dans le menu **Fichier**, cliquez sur **Nouveau projet**.
3. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Installé** > **Modèles** > **Visual C#** > **Cloud** > **Démarrages rapides** > **Services de données**. a. Choisissez l’un des modèles suivants : **Azure Storage : Blobs**, **Azure Storage : Fichiers**, **Azure Storage : Files d’attente** ou **Azure Storage : Tables**. b. Vérifiez que la version cible sélectionnée est **.NET Framework 4.5**.
	- 3\.c. Donnez un nom à votre projet et créez la nouvelle solution Visual Studio, comme indiqué :

	![Exemples de Démarrage rapide pour Azure][Image1]

Il peut être utile d’examiner le code source avant d'exécuter l'application. Pour ce faire, dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de solutions**. Ensuite, double-cliquez sur le fichier Program.cs.

Exécutez ensuite l'exemple d'application :

1.	Dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de solutions**. Ouvrez le fichier App.config et supprimez les marques de commentaire de la chaîne de connexion pour l'émulateur de stockage Azure :

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Annulez les marques de commentaire de la chaîne de connexion du service Azure Storage et indiquez le nom du compte de stockage et la clé d'accès dans le fichier App.config :`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

	Pour récupérer votre clé d’accès du compte de stockage, reportez-vous à [Gérer vos clés d’accès de stockage](storage-create-storage-account.md#manage-your-storage-access-keys).

3.	Une fois que vous avez fourni la clé d'accès et le nom du compte de stockage dans le fichier App.config, dans le menu **Fichier**, cliquez sur **Enregistrer tout** pour enregistrer tous les fichiers du projet.
4.	Dans le menu **Générer**, cliquez sur **Générer la solution**.
5.	Dans le menu **Déboguer**, appuyez sur **F11** pour exécuter la solution étape par étape ou appuyez sur **F5** pour exécuter la solution.


## Exécution locale de votre première application Azure Storage sur l’émulateur d’Azure Storage

L'[émulateur de stockage](storage-use-emulator.md) fournit un environnement local qui émule les services d’objets blob, de File d'attente et de Table Azure à des fins de développement. Vous pouvez utiliser l’émulateur de stockage pour tester votre application de stockage localement, sans souscrire d’abonnement Azure ni de compte de stockage et sans frais.

Pour l’essayer, nous allons créer une application Azure Storage simple à l’aide d’un des exemples de projet de démarrage rapide Azure dans Visual Studio. Ce didacticiel se concentre sur les exemples de projets de **stockage d’objets blob Azure**, de **tables Azure Storage** et de **files d’attente Azure Storage**.

1. Démarrez Visual Studio.
2. Dans le menu **Fichier**, cliquez sur **Nouveau projet**.
3. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Installé** > **Modèles** > **Visual C#** > **Cloud** > **Démarrages rapides** > **Services de données**. a. Choisissez l’un des modèles suivants : **Azure Storage : Blobs**, **Azure Storage : Fichiers**, **Azure Storage : Files d’attente** ou **Azure Storage : Tables**. b. Vérifiez que la version cible sélectionnée est **.NET Framework 4.5**. c. Donnez un nom à votre projet et créez la nouvelle solution Visual Studio, comme indiqué :

	![Exemples de Démarrage rapide pour Azure][Image1]

4.	Dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de solutions**. Ouvrez le fichier App.config et supprimez la marque de commentaire de la chaîne de connexion pour votre compte de stockage Azure si vous en avez ajouté une. Puis supprimez la marque de commentaire de la chaîne de connexion pour l’émulateur de stockage Azure :

	`<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Il peut être utile d’examiner le code source avant d'exécuter l'application. Pour ce faire, dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de solutions**. Ensuite, double-cliquez sur le fichier Program.cs.

Ensuite, exécutez l'exemple d'application dans l'émulateur d’Azure Storage :

1.	Appuyez sur le bouton **Démarrer** ou sur la touche Windows, recherchez l’*émulateur de Microsoft Azure Storage*, puis lancez l’application. Lorsque l’émulateur démarre, une icône s’affiche avec une notification dans la zone de notification de la barre des tâches Windows.
2.	Dans le menu **Générer** de Visual Studio, cliquez sur **Générer la solution**.
3.	Dans le menu **Déboguer**, appuyez sur **F11** pour exécuter la solution étape par étape ou appuyez sur **F5** pour exécuter la solution du début à la fin.

## Étapes suivantes

Pour en savoir plus sur Azure Storage, consultez les ressources suivantes :

* [Introduction à Microsoft Azure Storage](storage-introduction.md)
* [Prise en main de l’explorateur de stockage Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Prise en main d’Azure Blob Storage à l’aide de .NET](storage-dotnet-how-to-use-blobs.md)
* [Prise en main d’Azure Table Storage à l’aide de .NET](storage-dotnet-how-to-use-tables.md)
* [Prise en main d’Azure Queue Storage à l’aide de .NET](storage-dotnet-how-to-use-queues.md)
* [Prise en main d’Azure File Storage sur Windows](storage-dotnet-how-to-use-files.md)
* [Transfert de données avec l'utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
* [Documentation d'Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Bibliothèque cliente Microsoft Azure Storage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [API REST des services d’Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png

<!---HONumber=AcomDC_0921_2016-->