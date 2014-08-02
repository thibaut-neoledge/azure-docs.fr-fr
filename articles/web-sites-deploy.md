<properties linkid="manage-services-how-to-deploy-websites" urlDisplayName="How to deploy an Azure Web Site" pageTitle="How to deploy an Azure Web Site" metaKeywords="Azure deploy publish web site" description="Learn what methods are available for deploying content to an Azure Web Site." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Web Site" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

Déploiement d'un site Web Azure
===============================

Vous disposez de plusieurs options pour déployer vos contenus dans un site Web Azure. Cette rubrique présente rapidement chaque option et propose des liens renvoyant vers des informations supplémentaires.

-   [Déploiement à partir d'un système de contrôle du code source hébergé sur le cloud](#cloud)
    -   Visual Studio Online (VSO)
    -   Sites Web référentiels utilisant Git
    -   Sites Web référentiels utilisant Mercurial
    -   Dropbox
-   [Déploiement à partir d'un environnement de développement intégré (IDE)](#ide)
    -   Visual Studio
    -   WebMatrix
-   [Déploiement à l'aide d'un utilitaire FTP](#ftp)
-   [Déploiement à partir d'un système de contrôle du code source local](#onpremises)
    -   Team Foundation Server (TFS)
    -   Référentiels Git ou Mercurial locaux
-   [Utilisation des outils en ligne de commande et de l'API de gestion REST Azure](#commandline)
    -   MSBuild
    -   Scripts FTP
    -   Windows PowerShell
    -   API de gestion .NET
    -   Ligne de commande interplateforme (xpat-cli)
    -   Ligne de commande Web Deploy

Déploiement à partir d'un système de contrôle du code source hébergé sur le cloud
---------------------------------------------------------------------------------

La meilleure méthode pour déployer un site Web consiste à configurer un [flux de livraison continu](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) intégré à votre [système de contrôle du code source](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control). L'automatisation renforce l'efficacité du processus de développement, ainsi que la gestion et la fiabilité de vos processus de sauvegarde et de restauration.

Si vous n'avez pas encore de contrôle de code source, la méthode la plus simple pour commencer est d'utiliser un système de contrôle de code source hébergé sur le cloud.

### Visual Studio Online (VSO)

[Visual Studio Online](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) (anciennement Team Foundation Service) est une solution Microsoft basée sur le cloud pour le contrôle du code source et la collaboration d'équipe. Ce service est gratuit pour une équipe allant jusqu'à 5 développeurs. Vous pouvez configurer VSO pour qu'il effectue des envois continus vers un site Web Azure et votre référentiel peut utiliser [Git ou TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Pour plus d'informations, consultez les ressources suivantes :

-   [Envoi vers Azure en continu](http://www.visualstudio.com/en-us/learn/continuous-delivery-in-vs). Didacticiel pas à pas montrant comment configurer des envois continus de VSO vers un site Web Azure, en utilisant TFVC. TFVC est l'option de contrôle du code source centralisée dans VSO, contrairement à Git, qui est l'option de contrôle du code source distribuée.
-   [Livraison continue sur Azure au moyen de Visual Studio Online](/en-us/documentation/articles/cloud-services-continuous-delivery-use-vso/). Explique comment se connecter à VSO, archiver un projet vers un contrôle de code source et le configurer pour un envoi continu (déploiement automatique) vers Azure. Écrit pour les services cloud, mais la plupart du processus de configuration du déploiement dans Visual Studio Online est semblable pour les sites Web.

### Sites Web référentiels utilisant Git

[MySQL](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) est un système de contrôle de code source distribué populaire. Azure dispose de fonctionnalités intégrées qui facilitent l'automatisation du déploiement vers un site Web Azure depuis des sites référentiels basés sur le Web qui stockent des référentiels Git, notamment [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) et [BitBucket](https://bitbucket.org/). L'avantage de l'utilisation de Git pour le déploiement est qu'il est assez facile de revenir à un déploiement précédent le cas échéant.

Pour plus d'informations, consultez les ressources suivantes :

-   [Publication à partir du contrôle de code source sur des sites Web Azure](/en-us/documentation/articles/web-sites-publish-source-control/). Permet d'apprendre à utiliser Git pour publier directement à partir de votre ordinateur local sur un site Web Azure (dans Azure, cette méthode de publication est appelée Git local). Montre également comment activer le déploiement continu de référentiels Git à partir de GitHub, CodePlex ou BitBucket.
-   [Forum Azure pour Git, Mercurial et DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

### Sites Web référentiels utilisant Mercurial

Si vous utilisez [Mercurial](http://mercurial.selenic.com/) comme système de contrôle de code source et que vous stockez votre référentiel dans [CodePlex](http://www.codeplex.com/) ou [BitBucket](https://bitbucket.org/), vous pouvez utiliser des fonctionnalités intégrées dans des sites Web Azure pour déployer automatiquement vos contenus.

Pour plus d'informations sur le déploiement avec Mercurial, consultez les ressources suivantes :

-   [Publication à partir du contrôle de code source sur des sites Web Azure](/en-us/documentation/articles/web-sites-publish-source-control/). Bien que ce didacticiel montre comment publier un référentiel Git, le processus est similaire pour les référentiels Mercurial hébergés dans CodePlex ou BitBucket.
-   [Forum Azure pour Git, Mercurial et DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

### DropBox

[DropBox](https://www.dropbox.com/) n'est pas un système de contrôle de code source, mais si vous stockez votre code source dans DropBox, vous pouvez automatiser le déploiement à partir de votre compte DropBox.

-   [Déploiement vers Windows Azure avec Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Explique comment utiliser le portail de gestion Azure pour configurer un déploiement DropBox.
-   [DropBox et sites Web Azure](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). Cette vidéo vous guide durant le processus de connexion d'un dossier DropBox à un site Web Azure, et montre à quel point vous pouvez rapidement rendre un site Web fonctionnel ou en assurer la maintenance en utilisant un simple déploiement par glisser-déplacer.
-   [Forum Azure pour Git, Mercurial et DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

Déploiement à partir d'un environnement de développement intégré (IDE)
----------------------------------------------------------------------

[Visual Studio](http://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) et [WebMatrix](http://www.microsoft.com/web/webmatrix/) sont des environnements de développement intégrés Microsoft (ou IDE) que vous pouvez utiliser pour le développement Web. Ils fournissent tous deux des fonctionnalités intégrées facilitant le déploiement de sites Web Azure. De même, ils peuvent tous deux utiliser [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) pour automatiser les tâches liées au déploiement, comme le déploiement d'une base de données et les modifications de chaînes de connexion. Enfin, ils peuvent tous deux être déployés en utilisant [FTP ou FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

WebMatrix est rapide à installer et facile à maîtriser, mais Visual Studio offre bien plus de fonctionnalités pour utiliser les sites Web Azure. Dans l'IDE Visual Studio, vous pouvez créer, arrêter, démarrer et supprimer les sites Web Azure, afficher des fichiers journaux pendant leur création en temps réel, procéder à un débogage à distance, etc. Visual Studio intègre également des systèmes de contrôle de code source tels que [Visual Studio Online](#vso), [Team Foundation Server](#tfs) et les [référentiels Git](#git).

### Visual Studio

Pour plus d'informations sur le déploiement dans des sites Web Azure depuis Visual Studio, consultez les ressources suivantes :

-   [Prise en main d'Azure et ASP.NET](/en-us/develop/net/tutorials/get-started/). Explique comment créer et déployer un projet Web ASP.NET MVC simple en utilisant Visual Studio et Web Deploy.
-   [Déploiement d'une application ASP.NET MVC 5 sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/). Explique comment créer et déployer un projet Web ASP.NET MVC avec une base de données SQL, en utilisant Visual Studio, Web Deploy et Entity Framework Code First Migrations.
-   [Présentation d'un déploiement Web pour Visual Studio et ASP.NET](http://msdn.microsoft.com/en-us/library/dd394698.aspx). Une introduction de base au déploiement Web avec Visual Studio. Ancien, mais contient des informations toujours pertinentes, notamment une présentation des options de déploiement d'une base de données ainsi qu'une application Web et une liste de tâches de déploiement supplémentaires que vous devrez effectuer, ou pour lesquelles vous devrez configurer manuellement Visual Studio. Cette rubrique présente une vue d'ensemble du déploiement, pas seulement du déploiement dans des sites Web Azure.
-   [Déploiement Web ASP.NET en utilisant Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Une série de didacticiels en 12 parties présentant un ensemble de tâches de déploiement plus complet que le reste de cette liste.
-   [Déploiement d'un site Web ASP.NET dans Azure avec Visual Studio 2012 directement depuis un référentiel Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Montre comment déployer un projet Web ASP.NET dans Visual Studio, en utilisant le plug-in Git pour valider le code sur Git et connecter Azure au référentiel Git.

### WebMatrix

Pour plus d'informations sur le déploiement dans des sites Web Azure depuis WebMatrix, consultez les ressources suivantes :

-   [Développement et déploiement d'un site Web avec Microsoft WebMatrix](http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-using-webmatrix/). Montre comment créer un site Web ASP.NET simple en utilisant un modèle WebMatrix et en le déployant sur un site Web Azure en utilisant WebMatrix et Web Deploy.
-   [Génération et déploiement d'un site Web Node.js dans Azure avec WebMatrix](http://www.windowsazure.com/en-us/documentation/articles/web-sites-nodejs-use-webmatrix/).
-   [Création et déploiement d'un site Web Azure PHP-MySQL avec WebMatrix](http://www.windowsazure.com/en-us/documentation/articles/web-sites-php-mysql-use-webmatrix/).
-   [WebMatrix 3 : Git intégré et déploiement dans Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Utilisation de WebMatrix pour procéder au déploiement depuis un référentiel de contrôle de code source Git.

Déploiement à l'aide d'un utilitaire FTP
----------------------------------------

Indépendamment de l'IDE que vous utilisez, vous pouvez également déployer des contenus dans votre site en utilisant le [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) pour copier des fichiers. Vous pouvez facilement créer des informations d'identification FTP pour un site Web Azure, puis les utiliser dans n'importe quelle application fonctionnant avec le FTP, notamment les navigateurs tels qu'Internet Explorer et les utilitaires gratuits et complets tels que [FileZilla](https://filezilla-project.org/). Les sites Web Azure prennent également en charge le protocole FTPS, plus sécurisé.

Bien que les utilitaires FTP permettent de copier facilement vos fichiers de site Web dans Azure, ils n'effectuent ou ne coordonnent pas automatiquement de tâches de déploiement telles que le déploiement d'une base de données ou la modification de chaînes de connexion. De même, de nombreux outils FTP ne comparent pas les fichiers source et de destination afin d'ignorer la copie des fichiers qui n'ont pas été modifiés. Pour des sites volumineux, le fait de toujours copier tous les fichiers peut allonger la durée des déploiements, même dans le cas d'une mise à jour mineure, puisque tous les fichiers sont toujours copiés.

Pour plus d'informations, consultez les ressources suivantes :

-   [Création et déploiement d'un site Web Azure PHP-MySQL avec FTP](/en-us/documentation/articles/web-sites-php-mysql-deploy-use-ftp/).
-   [Gestion des sites Web](http://www.windowsazure.com/en-us/documentation/articles/web-sites-manage/). Contient des informations supplémentaires non incluses dans le didacticiel sur la configuration des informations d'identification FTP. Consultez les commentaires en base de page pour obtenir les informations manquantes dans le document sur l'obtention des informations d'identification.

Déploiement à partir d'un système de contrôle du code source local
------------------------------------------------------------------

Si vous utilisez TFS, Git ou Mercurial dans un référentiel local (qui n'est pas hébergé sur le cloud), vous pouvez déployer directement depuis votre référentiel vers vos sites Web Azure.

### Team Foundation Server (TFS)

Team Foundation Server est une solution Microsoft locale pour le contrôle du code source et la collaboration d'équipe. Vous pouvez configurer TFS pour qu'il procède à des livraisons continues vers un site Web Azure.

Pour plus d'informations, consultez les ressources suivantes :

-   [Livraison continue pour Cloud Services dans Azure](/en-us/develop/net/common-tasks/continuous-delivery/). Ce document concerne un service cloud Azure, mais une partie de son contenu concerne aussi les sites Web.

### Référentiels Git ou Mercurial locaux

Dans Azure, vous pouvez entrer l'URL de n'importe quel référentiel utilisant Git ou Mercurial, afin d'effectuer un déploiement depuis cet emplacement. Vous pouvez également procéder à un envoi depuis un référentiel Git local vers un site Web Azure.

Pour plus d'informations, consultez les ressources suivantes :

-   [Publication à partir du contrôle de code source sur des sites Web Azure](/en-us/documentation/articles/web-sites-publish-source-control/). Permet d'apprendre à utiliser Git pour publier directement à partir de votre ordinateur local sur un site Web Azure (dans Azure, cette méthode de publication est appelée Git local). Montre également comment activer le déploiement continu de référentiels Git à partir de GitHub, CodePlex ou BitBucket.
-   [Publication vers des sites Web Azure à partir de n'importe quel référentiel git/hg repo](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog tenu par David Ebbo qui détaille la fonctionnalité « Référenciel externe » des sites Web Azure.
-   [Forum Azure pour Git, Mercurial et DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
-   [Déploiement de DEUX sites Web sur Azure à partir d'un référentiel Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Billet de blog de Scott Hanselman.

Utilisation des outils en ligne de commande et de l'API de gestion REST Azure
-----------------------------------------------------------------------------

Il est toujours recommandé d'automatiser votre workflow de développement, mais si vous ne pouvez pas le faire directement dans votre système de contrôle de code source, vous pouvez le configurer manuellement en utilisant les outils en ligne de commande. Cela implique généralement l'utilisation de plusieurs outils ou infrastructures, car un déploiement implique souvent l'utilisation de fonctions de gestion de site, ainsi que la copie de contenus.

Azure simplifie les tâches de gestion de site qui vous attendent lors d'un déploiement, en fournissant une API de gestion REST et plusieurs infrastructures facilitant l'utilisation de l'API.

### <a name=msbuild></a>MSBuild

Si vous utilisez l'[IDE Visual Studio](#vs) pour le développement, vous pouvez utiliser [MSBuild](http://msbuildbook.com/) pour automatiser tout ce que vous pouvez faire dans votre IDE. Vous pouvez configurer MSBuild pour utiliser [Web Deploy](#webdeploy) ou le [FTP/FTPS](#ftp) pour copier vos fichiers. Web Deploy peut également automatiser de nombreuses autres tâches de déploiement, comme le déploiement des bases de données.

Pour plus d'informations sur le déploiement en ligne de commande avec MSBuild, consultez les ressources suivantes :

-   [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Une série d'une dizaine de didacticiels sur le déploiement dans Azure avec Visual Studio. Montre comment utiliser la ligne de commande pour le déploiement après la configuration de profils de publication dans Visual Studio.
-   [Présentation de Microsoft Build Engine : utilisation de MSBuild et Team Foundation Build](http://msbuildbook.com/). Manuel comportant des chapitres sur l'utilisation de MSBuild pour le déploiement.

### Scripts FTP

Vous pouvez créer facilement des informations d'identification [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) pour un site Web Azure, puis les utiliser avec des scripts de commandes FTP.

Pour plus d'informations, consultez les ressources suivantes :

-   [Utilisation des scripts de commandes FTP](http://support.microsoft.com/kb/96269).

### Windows PowerShell

Vous pouvez utiliser des fonctions MSBuild ou FTP à partir de [Windows PowerShell](http://msdn.microsoft.com/en-us/library/dd835506.aspx). Ce faisant, vous pouvez également utiliser une collection de cmdlets Windows PowerShell facilitant l'appel de l'API de gestion REST Azure.

Pour plus d'informations, consultez les ressources suivantes :

-   [Tout automatiser (développement d'applications de cloud concrètes avec Azure)](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Chapitre d'un livre électronique expliquant comment l'exemple d'application contenu dans le livre utilise des scripts Windows PowerShell pour créer un environnement de test Azure et y procéder à un déploiement. Consultez la section [Ressources](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) pour obtenir des liens vers une documentation Azure PowerShell supplémentaire.

### API de gestion .NET

Vous pouvez écrire un code C\# pour utiliser des fonctions MSBuild ou FTP pour vos déploiements. Ce faisant, vous pouvez accéder à l'API REST de gestion Azure pour utiliser des fonctions de gestion de site.

Pour plus d'informations, consultez les ressources suivantes :

-   [Tout automatiser avec les bibliothèques de gestion Azure et .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Présente l'API de gestion .NET et fournit des liens vers d'autres documentations.

### Ligne de commande interplateforme (xpat-cli)

Vous pouvez utiliser la ligne de commande avec des ordinateurs Mac ou Linux pour vos déploiements en utilisant le FTP. Ce faisant, vous pouvez également accéder à l'API de gestion REST Azure en utilisant l'interface en ligne de commande interplateforme (xpat-cli). Vous pouvez également utiliser xpat-cli sur des ordinateurs Windows.

Pour plus d'informations, consultez les ressources suivantes :

-   [Outils en ligne de commande](/en-us/downloads/#cmd-line-tools). Page du portail Azure.com qui fournit des informations sur les outils en ligne de commande.

### Ligne de commande Web Deploy

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) est un logiciel Microsoft pour le déploiement dans IIS qui fournit des fonctionnalités de synchronisation des fichiers intelligentes et effectue ou coordonne également de nombreuses autres tâches liées au déploiement qui ne peuvent pas être automatisées lorsque vous utilisez le FTP. Par exemple, Web Deploy peut déployer une nouvelle base de données ou mettre à jour une base de données existante avec votre site Web. Web Deploy peut également réduire le délai de mise à jour d'un site existant, en copiant uniquement les fichiers modifiés. Microsoft WebMatrix, Visual Studio, Visual Studio Online et Team Foundation Server prennent en charge le logiciel Web Deploy intégré, mais vous pouvez également utiliser Web Deploy directement depuis la ligne de commande pour automatiser le déploiement. Les commandes Web Deploy sont très efficaces, mais leur apprentissage peut être long.

Pour plus d'informations, consultez les ressources suivantes :

-   [Outils de déploiement Web](http://technet.microsoft.com/en-us/library/dd568996). Documentation officielle sur le site Microsoft TechNet. Ancien, mais constitue un bon point de départ.
-   [Utilisation de Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Documentation officielle sur le site Microsoft IIS.NET. Également ancien, mais constitue un bon point de départ.
-   [StackOverflow](http://www.stackoverflow.com). L'endroit idéal pour obtenir des informations plus récentes sur l'utilisation de Web Deploy à partir de la ligne de commande.
-   [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild est le moteur de génération utilisé par Visual Studio, mais il peut aussi être utilisé à partir de la ligne de commande pour déployer des applications Web dans des sites Web Azure. Ce didacticiel fait partie d'une série traitant principalement des déploiements Visual Studio.

Étapes suivantes
----------------

Pour plus d'informations, consultez la section Déploiement de la [documentation des sites Web Azure](/en-us/documentation/services/web-sites/).

