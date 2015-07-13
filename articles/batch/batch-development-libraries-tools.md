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
<p> Récupérez ces bibliothèques et outils pour développer des applications Azure Batch et Batch Apps.

## Batch

+ [Bibliothèque cliente Batch pour .NET (NuGet)](http://www.nuget.org/packages/Azure.Batch/) : développez des applications clientes pour exécuter des travaux à l’aide du service Batch
+ [Bibliothèque de gestion Batch (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) : gérez les comptes de service Batch
+ [Batch Explorer](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) : application et exemple de GUI permettant de parcourir, d’utiliser et de mettre à jour les principaux éléments d’un compte Batch, y compris les travaux et les tâches, les machines virtuelles de tâche et les fichiers associés, ainsi que les pools.

> [AZURE.TIP]Batch Explorer est un outil formidable si vous débutez avec Batch ou que vous souhaitez analyser ou résoudre des problèmes liés à l’activité de Batch. Comme il s’agit d’un exemple de code, le code source vous montre comment les fonctionnalités sont implémentées à l’aide de l’API .NET de Batch. Consultez ce [billet de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Batch Apps

+ [Kit de développement logiciel (SDK) Batch Apps Cloud (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) : permettez aux applications de se décharger des travaux auprès du service Batch
+ [Extension Visual Studio Batch Apps (galerie Visual Studio)](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) : créez des applications cloud dans Visual Studio à l’aide du Kit de développement logiciel (SDK) Batch Apps Cloud
+ [Kit de développement logiciel (SDK) Batch Apps Client (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) : interagissez avec les applications qui peuvent se décharger des travaux auprès du service Batch
+ [Client Python de Batch Apps](https://github.com/Azure/azure-batch-apps-python) (GitHub) : module client Python permettant d’interagir avec des applications configurées dans un service Batch Apps.
+ [Exemple de Batch Apps Blender](https://github.com/Azure/azure-batch-apps-blender) (GitHub) : complément du logiciel open source de rendu Blender qui utilise le Kit de développement logiciel (SDK) Batch Apps et un client Python pour configurer une plateforme de rendu dans le cloud.


## Ressources supplémentaires

+ [Exemples de code](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Forum Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Prise en main de la bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=62-->