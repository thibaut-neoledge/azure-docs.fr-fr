<properties 
	pageTitle="Déploiement d'un site Web Azure" 
	description="Découvrez les méthodes disponibles pour le déploiement de contenu vers Sites Web Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2015" 
	ms.author="tdykstra"/>

#Déploiement d'un site Web Azure

Vous disposez de plusieurs options pour déployer vos contenus sur un site Web Azure. Cette rubrique présente rapidement chaque option et propose des liens renvoyant vers des informations supplémentaires.

* [Déploiement à partir d'un système de contrôle du code source hébergé sur le cloud](#cloud)
	* Visual Studio Online
	* Sites Web référentiels avec Git
	* Sites Web référentiels avec Mercurial
	* Dropbox
* [Déploiement à partir d'un environnement de développement intégré (IDE)](#ide)
	* Visual Studio
	* WebMatrix
* [Déploiement à l'aide d'un utilitaire FTP](#ftp)
* [Déploiement à partir d'un système de contrôle du code source local](#onpremises)
	* Team Foundation Server (TFS)
	* Référentiels Git ou Mercurial locaux
* [Utilisation des outils en ligne de commande et de l'API de gestion REST Azure](#commandline)
	* MSBuild
	* Scripts FTP
	* Windows PowerShell
	* API de gestion .NET
	* Interface de ligne de commande interplateforme (xpat-cli)
	* Ligne de commande Web Deploy


##<a name="cloud"></a>Déploiement à partir d'un système de contrôle du code source hébergé sur le cloud

La meilleure méthode pour déployer un site Web consiste à configurer un [flux de livraison continu](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) intégré à votre [système de contrôle du code source](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control). L'automatisation renforce l'efficacité du processus de développement, ainsi que la gestion et la fiabilité de vos processus de sauvegarde et de restauration. 

Si vous n'avez pas encore de contrôle de code source, la méthode la plus simple pour commencer est d'utiliser un système de contrôle de code source hébergé sur le cloud.

###<a name="vso"></a>Visual Studio Online

[Visual Studio Online](http://www.visualstudio.com/) (anciennement Team Foundation Service) est une solution Microsoft basée sur le cloud pour le contrôle du code source et la collaboration d'équipe. Ce service est gratuit pour une équipe allant jusqu'à 5 développeurs. Vous pouvez effectuer des envois continus vers un site Web Azure et votre référentiel peut utiliser [Git ou TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Pour plus d'informations, consultez les ressources suivantes :

* [Livraison continue sur Azure au moyen de Visual Studio Online et TFVC](../cloud-services-continuous-delivery-use-vso/). Didacticiel étape par étape qui montre comment configurer la diffusion en continu à partir de Visual Studio Online dans un site Web Azure, à l'aide de TFVC. TFVC est l'option de contrôle du code source centralisée, contrairement à Git, qui est l'option de contrôle du code source distribuée.
* [Livraison continue sur Azure au moyen de Visual Studio Online et Git](../cloud-services-continuous-delivery-use-vso-git/). Semblable au didacticiel précédent, si ce n'est qu'il utilise Git et non TFVC.

###<a name="git"></a>Sites Web référentiels avec Git

[MySQL](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) est un système de contrôle de code source distribué populaire. Azure dispose de fonctionnalités intégrées qui facilitent l'automatisation du déploiement vers un site Web Azure depuis des sites référentiels basés sur le Web qui stockent des référentiels Git, notamment [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) et [BitBucket](https://bitbucket.org/). L'avantage de l'utilisation de Git pour le déploiement est qu'il est assez facile de revenir à un déploiement précédent le cas échéant. 

Pour plus d'informations, consultez les ressources suivantes :

* [Publication à partir du contrôle de code source dans Sites Web Azure avec Git](../web-sites-publish-source-control/). Permet d'apprendre à utiliser Git pour publier directement à partir de votre ordinateur local sur un site Web Azure (dans Azure, cette méthode de publication est appelée Git local). Montre également comment activer le déploiement continu de référentiels Git à partir de GitHub, CodePlex ou BitBucket.
* [Déploiement vers des sites Web avec GitHub au moyen de Kudu](/fr-fr/documentation/videos/deploying-to-azure-from-github/). Vidéo de Scott Hanselman et David Ebbo montrant comment déployer un site Web directement depuis GitHub vers un site Web Azure.
* [Bouton Deploy to Azure pour Azure](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog sur une méthode pour déclencher le déploiement à partir d'un référentiel Git.
* [Forum Azure pour Git, Mercurial et Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="mercurial"></a>Sites Web référentiels avec Mercurial

Si vous utilisez [Mercurial](http://mercurial.selenic.com/) comme système de contrôle de code source et que vous stockez votre référentiel dans [CodePlex](http://www.codeplex.com/) ou [BitBucket](https://bitbucket.org/), vous pouvez utiliser des fonctionnalités intégrées dans des sites Web Azure pour déployer automatiquement vos contenus.

Pour plus d'informations sur le déploiement avec Mercurial, consultez les ressources suivantes :

* [Publication à partir du contrôle de code source dans Sites Web Azure avec Git](../web-sites-publish-source-control/). Bien que ce didacticiel montre comment publier un référentiel Git, le processus est similaire pour les référentiels Mercurial hébergés dans CodePlex ou BitBucket.
* [Forum Azure pour Git, Mercurial et Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="dropbox"></a>Dropbox

[Dropbox](https://www.dropbox.com/) n'est pas un système de contrôle de code source, mais si vous stockez votre code source dans Dropbox, vous pouvez automatiser le déploiement à partir de votre compte Dropbox.

* [Déploiement sur Web Sites Azure à partir de Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Explique comment utiliser le portail de gestion Azure pour configurer un déploiement Dropbox.
* [Déploiement Dropbox vers Sites Web Azure](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). Cette vidéo vous guide durant le processus de connexion d'un dossier DropBox à un site Web Azure, et montre à quel point vous pouvez rapidement rendre un site Web fonctionnel ou en assurer la maintenance en utilisant un simple déploiement par glisser-déplacer.
* [Forum Azure pour Git, Mercurial et Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).







##<a name="ide"></a>Déploiement à partir d'un environnement de développement intégré (IDE)

[Visual Studio](http://www.visualstudio.com/) et [WebMatrix](http://www.microsoft.com/web/webmatrix/) sont des environnements de développement intégrés Microsoft (ou IDE) que vous pouvez utiliser pour le développement Web. Ils fournissent tous deux des fonctionnalités intégrées facilitant le déploiement de sites Web Azure.  De même, ils peuvent tous deux utiliser [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) pour automatiser les tâches liées au déploiement, comme le déploiement d'une base de données et les modifications de chaînes de connexion. Enfin, ils peuvent tous deux être déployés en utilisant [FTP ou FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol)). 

WebMatrix est rapide à installer et facile à maîtriser, mais Visual Studio offre bien plus de fonctionnalités pour utiliser Sites Web Azure. Dans l'IDE Visual Studio, vous pouvez créer, arrêter, démarrer et supprimer les sites Web Azure, afficher des fichiers journaux pendant leur création en temps réel, procéder à un débogage à distance, etc. Visual Studio intègre également des systèmes de contrôle de code source tels que [Visual Studio Online](#vso), [Team Foundation Server](#tfs) et des [référentiels Git](#git).

###<a name="vs"></a>Visual Studio

Pour plus d'informations sur le déploiement dans des sites Web Azure depuis Visual Studio, consultez les ressources suivantes :

* [Prise en main d'Azure et ASP.NET](../web-sites-dotnet-get-started/). Explique comment créer et déployer un projet Web ASP.NET MVC simple en utilisant Visual Studio et Web Deploy.
* [Déploiement d'Azure WebJobs vers les sites Web Azure](../websites-dotnet-deploy-webjobs/). Explique comment configurer les projets d'application console pour qu'ils se déploient sous forme de tâches Web WebJobs.  
* [Déploiement d'une application ASP.NET MVC 5 sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/). Explique comment créer et déployer un projet Web ASP.NET MVC avec une base de données SQL, en utilisant Visual Studio, Web Deploy et Entity Framework Code First Migrations.
* [Présentation d'un déploiement Web pour Visual Studio et ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Une introduction de base au déploiement Web avec Visual Studio. Ancien, mais contient des informations toujours pertinentes, notamment une présentation des options de déploiement d'une base de données ainsi qu'une application Web et une liste de tâches de déploiement supplémentaires que vous devrez effectuer, ou pour lesquelles vous devrez configurer manuellement Visual Studio. Cette rubrique traite du déploiement de manière générale et non uniquement du déploiement vers les sites Web Azure.
* [Déploiement Web ASP.NET en utilisant Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Une série de didacticiels en 12 parties présentant un ensemble de tâches de déploiement plus complet que le reste de cette liste. Certaines fonctionnalités de déploiement Azure ont été ajoutées depuis la rédaction du didacticiel, mais des notes ajoutées ultérieurement expliquent ce qu'il manque. 
* [Déploiement d'un site Web ASP.NET dans Azure avec Visual Studio 2012 directement depuis un référentiel Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Montre comment déployer un projet Web ASP.NET dans Visual Studio, en utilisant le plug-in Git pour valider le code sur Git et connecter Azure au référentiel Git. À partir de Visual Studio 2013, la prise en charge Git est intégrée et ne nécessite pas l'installation d'un plug-in.

###<a name="webmatrix"></a>WebMatrix

Pour plus d'informations sur le déploiement dans des sites Web Azure depuis WebMatrix, consultez les ressources suivantes :

* [Développement et déploiement d'un site Web avec Microsoft WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-using-webmatrix/). Montre comment créer un site Web ASP.NET simple en utilisant un modèle WebMatrix et en le déployant sur un site Web Azure en utilisant WebMatrix et Web Deploy.
* [Génération et déploiement d'un site Web Node.js dans Azure avec WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-use-webmatrix/).
* [Création et déploiement d'un site Web Azure PHP-MySQL avec WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-php-mysql-use-webmatrix/).
* [WebMatrix 3 : Git intégré et déploiement dans Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Utilisation de WebMatrix pour procéder au déploiement depuis un référentiel de contrôle de code source Git.

##<a name="ftp"></a>Déploiement à l'aide d'un utilitaire FTP

Indépendamment de l'IDE que vous utilisez, vous pouvez également déployer des contenus dans votre site en utilisant le [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) pour copier des fichiers. Vous pouvez facilement créer des informations d'identification FTP pour un site Web Azure, puis les utiliser dans n'importe quelle application fonctionnant avec le FTP, notamment les navigateurs tels qu'Internet Explorer et les utilitaires gratuits et complets tels que [FileZilla](https://filezilla-project.org/).  Les sites Web Azure prennent également en charge le protocole FTPS, plus sécurisé. 

Bien que les utilitaires FTP permettent de copier facilement vos fichiers de site Web dans Azure, ils n'effectuent ou ne coordonnent pas automatiquement de tâches de déploiement telles que le déploiement d'une base de données ou la modification de chaînes de connexion. De même, de nombreux outils FTP ne comparent pas les fichiers source et de destination afin d'ignorer la copie des fichiers qui n'ont pas été modifiés. Pour des sites volumineux, le fait de toujours copier tous les fichiers peut allonger la durée des déploiements, même dans le cas d'une mise à jour mineure, puisque tous les fichiers sont toujours copiés.

Pour plus d'informations, consultez les ressources suivantes :

* [Création et déploiement d'un site Web Azure PHP-MySQL avec FTP](../web-sites-php-mysql-deploy-use-ftp/). 
* [Gestion des sites Web](../web-sites-manage/#ftp-credentials). Contient des informations supplémentaires non incluses dans le didacticiel sur la configuration des informations d'identification FTP. 


##<a name="onpremises"></a>Déploiement à partir d'un système de contrôle du code source local

Si vous utilisez TFS, Git ou Mercurial dans un référentiel local (qui n'est pas hébergé sur le cloud), vous pouvez déployer directement depuis votre référentiel vers vos sites Web Azure.

###<a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server est une solution Microsoft locale pour le contrôle du code source et la collaboration d'équipe. Vous pouvez configurer TFS pour qu'il procède à des livraisons continues vers un site Web Azure.

Pour plus d'informations, consultez les ressources suivantes :

* [Remise continue pour Cloud Services dans Azure](../cloud-services-dotnet-continuous-delivery/). Ce document concerne un service cloud Azure, mais une partie de son contenu concerne aussi les sites Web.

###<a name="gitmercurial"></a>Référentiels Git ou Mercurial locaux

Dans Azure, vous pouvez entrer l'URL de n'importe quel référentiel utilisant Git ou Mercurial, afin d'effectuer un déploiement depuis cet emplacement. Vous pouvez également procéder à un envoi depuis un référentiel Git local vers un site Web Azure. 

Pour plus d'informations, consultez les ressources suivantes :

* [Publication à partir du contrôle de code source dans Sites Web Azure avec Git](../web-sites-publish-source-control/). Permet d'apprendre à utiliser Git pour publier directement à partir de votre ordinateur local sur un site Web Azure (dans Azure, cette méthode de publication est appelée Git local). Montre également comment activer le déploiement continu de référentiels Git à partir de GitHub, CodePlex ou BitBucket.
* [Publication vers des sites Web Azure à partir de n'importe quel référentiel git/hg repo](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog qui détaille la fonctionnalité " Référentiel externe " des sites Web Azure.
* [Forum Azure pour Git, Mercurial et Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
* [Déploiement de DEUX sites Web sur Azure à partir d'un référentiel Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Billet de blog de Scott Hanselman.











##<a name="commandline"></a>Utilisation des outils en ligne de commande et de l'API de gestion REST Azure

Il est toujours recommandé d'automatiser votre workflow de développement, mais si vous ne pouvez pas le faire directement dans votre système de contrôle de code source, vous pouvez le configurer manuellement en utilisant les outils en ligne de commande. Cela implique généralement l'utilisation de plusieurs outils ou infrastructures, car un déploiement implique souvent l'utilisation de fonctions de gestion de site, ainsi que la copie de contenus.

Azure simplifie les tâches de gestion de site qui vous attendent lors d'un déploiement, en fournissant une API de gestion REST et plusieurs infrastructures facilitant l'utilisation de l'API.

###<a name=msbuild></a>MSBuild

Si vous utilisez l'[IDE Visual Studio](#vs) pour le développement, vous pouvez utiliser  [MSBuild](http://msbuildbook.com/) pour automatiser tout ce que vous pouvez faire dans votre IDE. Vous pouvez configurer MSBuild pour utiliser [Web Deploy](#webdeploy) ou le [FTP/FTPS](#ftp) pour copier vos fichiers. Web Deploy peut également automatiser de nombreuses autres tâches de déploiement, comme le déploiement des bases de données.

Pour plus d'informations sur le déploiement en ligne de commande avec MSBuild, consultez les ressources suivantes :

* [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Une série d'une dizaine de didacticiels sur le déploiement dans Azure avec Visual Studio. Montre comment utiliser la ligne de commande pour le déploiement après la configuration de profils de publication dans Visual Studio.
* [Présentation de Microsoft Build Engine : utilisation de MSBuild et Team Foundation Build](http://msbuildbook.com/). Manuel comportant des chapitres sur l'utilisation de MSBuild pour le déploiement.

###<a name="ftp2"></a>Scripts FTP

Vous pouvez créer facilement des informations d'identification [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) pour un site Web Azure, puis les utiliser avec des scripts de commandes FTP.

Pour plus d'informations, consultez les ressources suivantes :

* [Utilisation des scripts de commandes FTP](http://support.microsoft.com/kb/96269).

###<a name="powershell"></a>Windows PowerShell

Vous pouvez utiliser des fonctions de déploiement MSBuild ou FTP à partir de [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Ce faisant, vous pouvez également utiliser une collection de cmdlets Windows PowerShell facilitant l'appel de l'API de gestion REST Azure.

Pour plus d'informations, consultez les ressources suivantes :

* [Tout automatiser (développement d'applications de cloud concrètes avec Azure)](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Chapitre d'un livre électronique expliquant comment l'exemple d'application contenu dans le livre utilise des scripts Windows PowerShell pour créer un environnement de test Azure et y procéder à un déploiement. Consultez la section [Ressources](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) pour obtenir des liens vers une documentation Azure PowerShell supplémentaire.
* [Utilisation de scripts Windows PowerShell pour publier des environnements de développement et de test](http://msdn.microsoft.com/library/dn642480.aspx). Comment utiliser les scripts de déploiement Windows PowerShell générés par Visual Studio.

###<a name="api"></a>API de gestion .NET

Vous pouvez écrire du code C# pour utiliser des fonctions MSBuild ou FTP pour vos déploiements. Ce faisant, vous pouvez accéder à l'API REST de gestion Azure pour utiliser des fonctions de gestion de site.

Pour plus d'informations, consultez les ressources suivantes :

* [Tout automatiser avec les bibliothèques de gestion Azure et .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Présente l'API de gestion .NET et fournit des liens vers d'autres documentations.

###<a name="cli"></a>Interface de ligne de commande interplateforme (xplat-cli)

Vous pouvez utiliser la ligne de commande avec des ordinateurs Mac ou Linux pour vos déploiements en utilisant le FTP. Ce faisant, vous pouvez également accéder à l'API de gestion REST Azure en utilisant l'interface en ligne de commande interplateforme (xplat-cli). Vous pouvez également utiliser xplat-cli sur des ordinateurs Windows.

Pour plus d'informations, consultez les ressources suivantes :

* [Outils en ligne de commande](/fr-fr/downloads/#cmd-line-tools). Page du portail Azure.com qui fournit des informations sur les outils en ligne de commande.

###<a name="webdeploy"></a>Ligne de commande Web Deploy

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) est un logiciel Microsoft pour le déploiement dans IIS qui fournit des fonctionnalités de synchronisation des fichiers intelligentes et effectue ou coordonne également de nombreuses autres tâches liées au déploiement qui ne peuvent pas être automatisées lorsque vous utilisez le FTP. Par exemple, Web Deploy peut déployer une nouvelle base de données ou mettre à jour une base de données existante avec votre site Web. Web Deploy peut également réduire le délai de mise à jour d'un site existant, en copiant uniquement les fichiers modifiés. Microsoft WebMatrix, Visual Studio, Visual Studio Online et Team Foundation Server prennent en charge le logiciel Web Deploy intégré, mais vous pouvez également utiliser Web Deploy directement depuis la ligne de commande pour automatiser le déploiement. Les commandes Web Deploy sont très efficaces, mais leur apprentissage peut être long.

Pour plus d'informations, consultez les ressources suivantes :

* [Sites Web Azure simples : déploiement](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo sur un outil qu'il a créé pour faciliter l'utilisation de Web Deploy.
* [Outil de déploiement Web](http://technet.microsoft.com/library/dd568996). Documentation officielle sur le site Microsoft TechNet. Ancien, mais constitue un bon point de départ.
* [Utilisation de Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Documentation officielle sur le site Microsoft IIS.NET. Également ancien, mais constitue un bon point de départ.
* [StackOverflow](http://www.stackoverflow.com). L'endroit idéal pour obtenir des informations plus récentes sur l'utilisation de Web Deploy à partir de la ligne de commande.
* [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild est le moteur de génération utilisé par Visual Studio, mais il peut aussi être utilisé à partir de la ligne de commande pour déployer des applications Web dans des sites Web Azure. Ce didacticiel fait partie d'une série traitant principalement des déploiements Visual Studio.

##<a name="nextsteps"></a>Étapes suivantes

Dans certains cas, vous souhaiterez probablement pouvoir basculer entre une version intermédiaire et de production de votre site Web. Vous pouvez le faire en utilisant la fonctionnalité de déploiement intermédiaire des sites Web Azure. Pour plus d'informations, consultez la page [Déploiement intermédiaire dans Sites Web Microsoft Azure](../web-sites-staged-publishing/).

Posséder un programme de sauvegarde et de restauration établi représente une partie essentielle de tout flux de travail de déploiement. Pour plus d'informations concernant la fonctionnalité de sauvegarde et de restauration des sites Web Azure, consultez [Sauvegardes des sites Web Azure](../web-sites-backup/).  

Pour plus d'informations sur l'utilisation du contrôle d'accès basé sur les rôles d'Azure pour gérer l'accès au déploiement de sites Web, consultez la page [RBAC et publication de sites Web Azure](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing).

Pour plus d'informations concernant d'autres rubriques de déploiement, consultez la section Déploiement de la [documentation des sites Web Azure](/fr-fr/documentation/services/web-sites/).


<!--HONumber=42-->
