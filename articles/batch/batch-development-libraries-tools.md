<properties
	pageTitle="Outils et bibliothèques de développement Azure Batch | Microsoft Azure"
	description="Récupérez les bibliothèques et outils nécessaires pour développer des applications Azure Batch"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="danlep"/>


# Outils et bibliothèques de développement Azure Batch
<p> Récupérez ces bibliothèques et outils pour développer des applications Azure Batch.

## Batch

+ [Bibliothèque cliente Batch pour .NET (NuGet)](http://www.nuget.org/packages/Azure.Batch/) : développez des applications clientes pour exécuter des travaux à l’aide du service Batch
+ [Bibliothèque de gestion Batch (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) : gérez les comptes Batch
+ [Batch Explorer](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub) : application et exemple de GUI permettant de parcourir, d’utiliser et de mettre à jour les principaux éléments d’un compte Batch, y compris les travaux et les tâches, les pools et nœuds de calcul, et les fichiers sur un nœud de calcul. Consultez ce [billet de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Batch Apps

+ [Kit de développement logiciel (SDK) Batch Apps Cloud (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/1.1.1-preview) : permettez aux applications de se décharger des travaux auprès du service Batch
+ [Extension Visual Studio Batch Apps (galerie Visual Studio)](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) : créez des applications cloud dans Visual Studio à l’aide du Kit de développement logiciel (SDK) Batch Apps Cloud
+ [Kit de développement logiciel (SDK) Batch Apps Client (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/2.3.0-preview) : interagissez avec les applications qui peuvent se décharger des travaux auprès du service Batch
+ [Client Python de Batch Apps](https://github.com/Azure/azure-batch-apps-python) (GitHub) : module client Python permettant d’interagir avec des applications configurées dans un service Batch Apps.

>[AZURE.IMPORTANT]Azure propose uniquement l’API Batch Apps sous forme de version préliminaire. Utilisez-la uniquement pour les projets de test ou de validation technique. Les fonctionnalités clés de Batch Apps seront intégrées dans l’API Batch dans les prochaines versions du service.

## Ressources supplémentaires

+ [Exemples de code](https://github.com/Azure/azure-batch-samples) (GitHub)
+ [Forum Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=July15_HO5-->