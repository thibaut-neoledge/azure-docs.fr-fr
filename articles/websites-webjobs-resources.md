<properties 
	pageTitle="Azure WebJobs - Ressources recommandées" 
	description="Ressources recommandées pour apprendre à utiliser Azure WebJobs et le Kit de développement logiciel (SDK) WebJobs." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="tdykstra"/>

#Azure WebJobs - Ressources recommandées

Cette rubrique fournit des liens vers des ressources de documentation sur l'utilisation d'Azure WebJobs et du Kit de développement logiciel (SDK) Azure WebJobs. Azure WebJobs fournit un moyen facile pour exécuter des scripts ou des programmes en tant que processus en arrière-plan dans Sites Web Azure. Vous pouvez télécharger et exécuter un fichier exécutable comme cmd, bat, exe (.NET), ps1, sh, php, py, js et jar. Ces programmes s'exécutent en tant que tâches Web WebJobs selon une planification (cron) ou en continu.

Le Kit de développement logiciel (SDK) Azure WebJobs facilite l'utilisation d'Azure Storage. Il comporte une liaison et un système de déclencheur qui fonctionne avec les objets blob Microsoft Azure Storage, les files d'attente et les tables ainsi que les files d'attente Service Bus.

La création, le déploiement et la gestion des tâches Web WebJobs sont parfaitement compatibles avec les outils intégrés dans Visual Studio. Vous pouvez créer les tâches Web WebJobs à partir de modèles, les publier et les gérer (exécuter/arrêter/analyser/déboguer). 

Le tableau de bord WebJobs dans le portail de gestion Azure fournit des puissantes fonctionnalités de gestion qui vous donnent un contrôle total sur l'exécution des tâches Web WebJobs, notamment la possibilité d'appeler des fonctions individuelles dans WebJobs. Le tableau de bord affiche également les runtimes de fonction et la sortie d'enregistrement. 

## Sommaire

* [Prise en main de WebJobs](#getstarted)
* [Déploiement de WebJobs](#deploy)
* [Test et débogage de WebJobs](#debug)
* [Autres ressources liées à WebJobs](#additional)
* [Ressources supplémentaires du Kit de développement logiciel (SDK) WebJobs](#additionalsdk)
* [Exemples d'applications WebJobs](#samples)
* [Blogs](#blogs) 
* [Obtention d'aide sur WebJobs](#gethelp)

##<a name="getstarted"></a>Prise en main de WebJobs

* [Présentation d'Azure WebJobs](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Fonctionnalités Azure WebJobs](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Présentation du Kit de développement logiciel (SDK) WebJobs](../websites-dotnet-webjobs-sdk/)
* [Annonce de la version finale 1.0.0 du Kit de développement logiciel WebJobs Microsoft Azure](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure](../websites-dotnet-webjobs-sdk-get-started/)
* [Utilisation du stockage de file d'attente Azure avec le Kit de développement logiciel (SDK) WebJobs](../websites-dotnet-webjobs-sdk-storage-queues-how-to)
* [Utilisation du stockage de d'objets blob Azure avec le Kit de développement logiciel (SDK) WebJobs](../websites-dotnet-webjobs-sdk-storage-blobs-how-to)
* [Utilisation du stockage de tables Azure avec le Kit de développement logiciel (SDK) WebJobs](../websites-dotnet-webjobs-sdk-storage-tables-how-to)
* [Utilisation de Service Bus Azure avec le Kit de développement logiciel (SDK) WebJobs](../websites-dotnet-webjobs-sdk-service-bus)
* [Présentation du Kit de développement logiciel Azure WebJobs (télécharger le PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* Vidéos
	* [WebJobs et le Kit de développement logiciel (SDK) WebJobs](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Série de vidéos Azure WebJobs sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Présentation des outils relatifs à WebJobs pour Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Outils relatifs à WebJobs et débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

Consultez également les sections suivantes sur le [déploiement de WebJobs](#deploy) et le [test et débogage de WebJobs](#debug).

##<a name="deploy"></a>Déploiement de WebJobs

* [Déploiement d'Azure WebJobs vers les sites Web Azure](../websites-dotnet-deploy-webjobs/)
* [Déploiement de WebJobs à l'aide du portail de gestion Azure](../web-sites-create-web-jobs/)
* [Activation de la ligne de commande ou livraison en continu d'Azure WebJobs](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Déploiement Git d'une application console .NET vers Azure à l'aide de WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/) 
* Vidéos
	* [Présentation des outils relatifs à WebJobs pour Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Outils relatifs à WebJobs et débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="debug"></a>Test et débogage de WebJobs

* [Fonctionnalités de débogage pour Azure WebJobs dans Visual Studio, destinées aux nouveaux développeurs](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Résolution des problèmes liés à Sites Web Azure dans Visual Studio](../web-sites-dotnet-troubleshoot-visual-studio/)
* [Qui a écrit cet objet blob ?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Code d'hébergement interactif dans le cloud](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Obtention d'un tableau de bord pour le développement local avec le Kit de développement logiciel (SDK) WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Ajout de Trace à Sites Web Azure et WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage](../storage-monitoring-diagnosing-troubleshooting/)
* Vidéos
	* [Outils relatifs à WebJobs et débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="additional"></a>Autres ressources liées à WebJobs

* [Billet de blog GA sur Azure WebJobs par Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Documentation de paramètres WebJobs dans GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* [Documentation de paramètres WebJobs dans GitHub](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Notification au déclenchement des tâches Web WebJobs terminées](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Sites Web Azure : Architecture d'applications Web Ready-For-Business, à grande échelle](https://channel9.msdn.com/Events/Build/2014/3-626). Couvre la mise à l'échelle de Sites Web Azure avec WebJobs, notamment le Kit de développement logiciel (SDK) WebJobs.
* [Stratégie de rétention de sauvegarde du site Web simple avec WebJobs](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Lenteur des sites Web et services cloud Azure à la première demande](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Montre comment utiliser WebJobs pour simuler la fonctionnalité AlwaysOn qui est disponible uniquement pour le niveau Standard du service Sites Web.
* [Arrêt progressif des tâches Web WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Pour plus d'informations sur l'arrêt progressif du Kit de développement logiciel (SDK) WebJobs, consultez la rubrique [Arrêt progressif](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful).
* [Mise à l'échelle de votre application Web avec Sites Web Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* Vidéos
	* [Montée en charge de WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)
	* [Vidéos sur WebJobs par Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Série de vidéos Azure WebJobs sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Ressources supplémentaires du Kit de développement logiciel (SDK) WebJobs

* [Paramètres des déclencheurs, liaisons et itinéraires dans Azure WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx) 
* Liaisons d'Azure Storage
	* [Objets blob](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [Files d'attente](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [Tables](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [Fonctionnement de [BlobTrigger]](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Liaisons avancées avec le Kit de développement logiciel (SDK) Azure WebJobs](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [Tâche Web WebJob pour télécharger des fichiers FREB vers Azure Storage à l'aide du Kit de développement logiciel (SDK) WebJobs](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hébergement de WebJobs en dehors d'Azure, avec les avantages de la journalisation à partir d'une tâche Web WebJobs Azure hébergée](http://bypassion.dk/?p=510)
* [Création d'un outil d'importation de données avec Azure WebJobs](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Vidéos
	* [Série de vidéos Azure WebJobs sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Exemple d'applications WebJobs

* [Exemple d'applications fourni par l'équipe WebJobs sur GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Site Web simple Azure avec un serveur principal WebJobs utilisant le Kit de développement logiciel (SDK) WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Illustre l'utilisation des tâches Web WebJobs planifiées et liées aux événements. Consultez le billet de blog [Reconstruction de SiteMonitR à l'aide du Kit de développement logiciel (SDK) Azure WebJobs](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogs

* [Blog Azure](/blog)
* [Blog d'Amit Apple](http://blog.amitapple.com/). Se concentre sur WebJobs (pas sur le Kit de développement logiciel (SDK)).
* [Blog de Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Obtention d'aide sur WebJobs

* [StackOverflow pour WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow pour le Kit de développement logiciel (SDK) WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Forum Azure et ASP.NET](http://forums.asp.net/1247.aspx)
* [Forum Sites Web Azure](http://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazurewebsitespreview)
* [Site UserVoice de Sites Web Azure](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Utilisez le hashtag #AzureWebJobs.


<!--HONumber=42-->
