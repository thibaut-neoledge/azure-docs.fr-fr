---
title: "Ressources de documentation relatives à Azure WebJobs"
description: "Ressources recommandées pour apprendre à utiliser Azure WebJobs et le Kit de développement logiciel (SDK) WebJobs."
services: app-service
documentationcenter: .net
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: ed005e56-4334-4641-a5e5-15435c2be36b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: fc5c87e45f3c92172f01a462451bc145ce8b0b16
ms.lasthandoff: 03/27/2017


---
# <a name="azure-webjobs-documentation-resources"></a>Ressources de documentation relatives à Azure WebJobs
## <a name="overview"></a>Vue d’ensemble
Cette rubrique fournit des liens vers des ressources de documentation sur l’utilisation d’Azure WebJobs et du Kit de développement logiciel (SDK) Azure WebJobs. Azure WebJobs permet d'exécuter facilement des scripts ou des programmes sous la forme de processus d'arrière-plan dans le contexte d'une [application web App Service, application API ou application mobile](../app-service/app-service-value-prop-what-is.md). Vous pouvez télécharger et exécuter un fichier exécutable comme cmd, bat, exe (.NET), ps1, sh, php, py, js et jar. Ces programmes s’exécutent en tant que tâches WebJobs selon une planification (cron) ou en continu.

L’objectif du [kit de développement logiciel (SDK) WebJobs](websites-webjobs-resources.md) consiste à simplifier le code que vous écrivez pour les tâches web courantes, telles que le traitement d’image, le traitement de la file d’attente, l’agrégation RSS, la maintenance des fichiers et l’envoi des messages électroniques. Le kit de développement logiciel (SDK) WebJobs dispose de fonctionnalités intégrées fonctionnant avec le stockage Azure et Service Bus et servant à planifier des tâches, à gérer des erreurs et à nombreux autres scénarios courants. En outre, il est évolutif et il existe un [référentiel open source contenant les extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). [Azure Functions](../azure-functions/functions-overview.md) (actuellement en version préliminaire) est basé sur une version du Kit de développement logiciel (SDK) WebJobs qui fonctionne avec C# script, Node.js et autres langages. 

[!INCLUDE [app-service-web-webjobs-corenote](../../includes/app-service-web-webjobs-corenote.md)]

La création, le déploiement et la gestion des tâches web WebJobs sont parfaitement compatibles avec les outils intégrés dans Visual Studio. Vous pouvez créer les tâches web WebJobs à partir de modèles, les publier et les gérer (exécuter/arrêter/analyser/déboguer). 

Le tableau de bord WebJobs dans le portail Azure fournit de puissantes fonctionnalités de gestion qui vous donnent un contrôle total sur l’exécution des tâches WebJobs, notamment la possibilité d’appeler des fonctions individuelles dans WebJobs. Le tableau de bord affiche également les runtimes de fonction et la sortie d'enregistrement. 

## <a name="getstarted"></a>Prise en main de WebJobs et du Kit de développement logiciel (SDK) WebJobs
* [Présentation d’Azure WebJobs](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs est extraordinaire, utilisez-le dès maintenant !](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Billet de blog de Troy Hunt).
* [Fonctionnalités Azure WebJobs](https://azure.microsoft.com/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Présentation du Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk.md)
* [Conseils de travaux d’arrière-plan par Microsoft Patterns and Practices](/documentation/articles/best-practices-background-jobs/)
* [Annonce de la version 1.1.0 RTM du Kit de développement logiciel (SDK) Microsoft Azure WebJobs](https://azure.microsoft.com/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure](websites-dotnet-webjobs-sdk-get-started.md)
* [Utilisation du stockage de file d’attente Microsoft Azure avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Utilisation du stockage d’objets blob Azure avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Utilisation du stockage de tables Microsoft Azure avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Utilisation de Microsoft Azure Service Bus avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-service-bus.md)
* [Aide-mémoire du Kit de développement logiciel (SDK) Azure WebJobs (téléchargement au format PDF)](https://go.microsoft.com/fwlink/p/?linkid=845558)
* [Documentation sur les paramètres de WebJobs dans GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs)
* Vidéos
  * [WebJobs et Kit de développement logiciel (SDK) WebJobs](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
  * [Série de vidéos Azure WebJobs sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
  * [Présentation des outils de WebJobs pour Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
  * [Outils de WebJobs et débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
  * [Mise à jour d'Azure WebJobs avec Pranav Rastogi - Extensibilité dans la version 1.1](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

Consultez également les sections suivantes sur le [déploiement de WebJobs](#deploy) ainsi que le [test et le débogage de WebJobs](#debug).

## <a name="deploy"></a>Déploiement de WebJobs
* [Déploiement d’Azure WebJobs à l’aide de Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Déploiement de WebJobs à l’aide du portail Azure](web-sites-create-web-jobs.md)
* [Activation de la ligne de commande ou livraison en continu d’Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Déploiement avec Git d’une application de console .NET sur Azure à l’aide de WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Déploiement d’un WebJob F# dans Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Déploiement de services personnalisés en tant que tâches web Azure](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Vidéos
  * [Présentation des outils de WebJobs pour Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
  * [Outils de WebJobs et débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

## <a name="schedule"></a>Planification de WebJobs
* [Boîte de dialogue Ajouter une tâche web Azure](websites-dotnet-deploy-webjobs.md#configure)
* [Créer une tâche WebJob planifiée dans le portail Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Liaison d’une tâche du planificateur à une tâche web](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Planification de tâches web Azure avec des expressions cron](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Planification de fonctions WebJobs individuelles à l'aide du Kit de développement logiciel (SDK) TimerTrigger WebJobs](websites-dotnet-webjobs-sdk.md#schedule)

## <a name="debug"></a>Test et débogage de WebJobs
* [Nouvelles fonctionnalités de développement et de débogage pour Azure WebJobs dans Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Afficher le tableau de bord WebJobs](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Écriture de journaux à l’aide du Kit de développement logiciel (SDK) WebJobs et affichage de ces derniers dans le tableau de bord](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Débogage à distance de WebJobs](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Qui a écrit cet objet blob ?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Code d’hébergement interactif dans le cloud](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Ajout de Trace à Azure WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Analyser, diagnostiquer et dépanner Microsoft Azure Storage](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* Vidéos
  * [Outils de WebJobs et débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

## <a name="scale"></a>Mise à l’échelle de WebJobs
* [Mise à l’échelle de votre application web avec Sites Web Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure App Service : création de l’architecture d’applications web prêtes à être utilisées par les entreprises à grande échelle](https://channel9.msdn.com/Events/Build/2014/3-626). Couvre la mise à l’échelle d’applications web avec WebJobs, notamment le Kit de développement logiciel (SDK) WebJobs.
* Vidéos
  * [Montée en puissance des tâches web](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

## <a name="additional"></a>Autres ressources liées à WebJobs
* [Billet de blog GA sur Azure WebJobs de Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Exécution de tâches web Powershell sur Azure App Service](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Réception d’une notification à la fin de la tâche web déclenchée par Azure](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Stratégie de rétention de sauvegarde d’une application web avec WebJobs](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Lenteur d’Azure Web Apps et de Cloud Services à la première requête](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Montre comment utiliser WebJobs pour simuler la fonctionnalité AlwaysOn qui n’est disponible que pour le niveau de tarification Standard.
* [Arrêt progressif de WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Pour plus d’informations sur l’arrêt progressif du Kit de développement logiciel (SDK) WebJobs, consultez la rubrique [Arrêt progressif](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).
* [Sauvegardes automatiques avec Azure WebJobs et AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Vidéos
  * [Vidéos sur WebJobs de Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
  * [Série de vidéos Azure WebJobs sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

## <a name="additionalsdk"></a>Ressources supplémentaires du Kit de développement logiciel (SDK) WebJobs
* [Notes de publication du Kit de développement logiciel (SDK) WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [Code source du Kit de développement logiciel (SDK) WebJobs](https://github.com/Azure/azure-webjobs-sdk)
* [Code source pour les extensions de Kit de développement logiciel (SDK) WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions) avec [guide détaillé pour le modèle d'extensibilité](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).  
* [Code source du script du Kit de développement logiciel (SKDK) WebJobs](https://github.com/Azure/azure-webjobs-sdk-script/) (utilisé pour [Azure Functions](../azure-functions/functions-overview.md))
* [Tâche web pour télécharger des fichiers FREB vers Azure Storage à l’aide du Kit de développement logiciel (SDK) WebJobs](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hébergement de tâches web Azure avec les avantages de la journalisation à partir d’une tâche web Azure hébergée](http://bypassion.dk/?p=510)
* [Création d’un outil d’importation de données avec Azure WebJobs](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Vue d’ensemble d’Azure Functions](../azure-functions/functions-overview.md)
* Vidéos
  * [Série de vidéos Azure WebJobs sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

## <a name="samples"></a>Exemple d’application WebJobs
* [Exemples d’application fournis par l’équipe WebJobs sur GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Application web Azure simple avec un serveur principal WebJobs utilisant le Kit de développement logiciel (SDK) WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Illustre l'utilisation des tâches web WebJobs planifiées et liées aux événements. Consultez le billet de blog [Reconstruction de SiteMonitR à l’aide du Kit de développement logiciel (SDK) Azure WebJobs](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

## <a name="blogs"></a>Blogs
* [Blog Azure](/blog)
* [Blog d’Amit Apple](http://blog.amitapple.com/). Consacré à WebJobs (et non au Kit de développement logiciel [SDK]).
* [Blog de Magnus Mårtensson](http://magnusmartensson.com/)

## <a name="gethelp"></a>Obtention d’aide sur WebJobs
* [StackOverflow pour WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow pour le Kit de développement logiciel (SDK) WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow pour Azure Functions](http://stackoverflow.com/questions/tagged/azure-functions)
* [Forum Azure et ASP.NET](http://forums.asp.net/1247.aspx)
* [Forum sur Azure App Service Web Apps (en anglais)](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Site UserVoice d’Azure Web Apps](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/). Utilisez le hashtag #AzureWebJobs.
* [Signaler un problème ou un bogue WebJobs](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)


