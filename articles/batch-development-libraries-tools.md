<properties 
	pageTitle="Outils et bibliothèques de développement Azure Batch" 
	description="Get the libraries and tools you need to develop Azure Batch and Batch Apps applications" 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="yidingz"/>

<tags 
	ms.service="batch" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="danlep"/>


# Outils et bibliothèques de développement Azure Batch 
<p> Obtenez ces bibliothèques et des outils pour développer des applications Azure lot et les applications de traitement par lots.

## Batch

+ [Lot Client Library for .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – développer des applications clientes pour exécuter des tâches avec le service de traitement par lots
+ [Bibliothèque de gestion des commandes](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – comptes de service de gérer le traitement par lots
+ [Explorer lot](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) – applications GUI exemple Parcourir, accéder et mettre à jour les éléments principaux dans un compte de traitement, y compris les travaux et tâches, tâches, des ordinateurs virtuels (TVMs) pools et des fichiers à une TVM.

> [AZURE.TIP]Explorateur de lot est un outil formidable si vous débutez au lot ou que vous souhaitez analyser ou dépanner les activités de traitement par lots. Car il s'agit d'un exemple de code, le code source vous montre comment les fonctionnalités sont implémentées à l'aide de l'API .NET de lot. Consultez le [billet de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Batch Apps

+ [Lot applications Cloud SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) – permettent aux applications de décharger le service de traitement des travaux
+ [Par lots des applications Visual Studio Extension](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (galerie Visual Studio –) Cloud-permettent aux applications dans Visual Studio en utilisant le Kit de développement logiciel de lot applications Cloud
+ [Lot applications Client SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) – interagir avec les applications activée pour décharger le service de traitement des travaux
+ [Lot applications Python Client](https://github.com/Azure/azure-batch-apps-python) (GitHub) - module de client Python pour interagir avec des applications définies dans un service des applications de traitement par lots
+ [Lot applications Blender exemple](https://github.com/Azure/azure-batch-apps-blender) (GitHub) - Addon à Blender logiciel open source rendu qui utilise le client de lot applications SDK et Python pour paramétrer une plate-forme de nuage de rendu


## Ressources supplémentaires

+ [Exemples de code](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Forum de lot Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Commencez avec la bibliothèque de commandes Azure pour .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=GIT-SubDir-->