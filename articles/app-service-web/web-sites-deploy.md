<properties
	pageTitle="Déploiement de votre application dans Azure App Service"
	description="Apprendre à déployer votre application sur Azure App Service"
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cephalin;tdykstra"/>
    
# Déploiement de votre application dans Azure App Service

Cet article vous aidera à déterminer la meilleure solution pour déployer les fichiers de votre application web, un serveur d’application mobile ou une application API sur [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), puis de vous orienter vers des articles et des blocs contenant des informations sur les procédures spécifiques à votre option préférée.

## <a name="overview"></a>Vue d’ensemble du processus de déploiement

Dès que vous avez créé ou configuré une application dans Azure App Service, avant de déployer un code quelconque, Azure App Service assure la gestion de l’infrastructure d’application pour vous (ASP.NET, PHP, Node.js, etc.). Certaines infrastructures sont activées par défaut tandis que d’autres, comme Java et Python, peuvent nécessiter une simple configuration avec coche pour l’activer. En outre, vous pouvez personnaliser votre infrastructure d’application, notamment la version de PHP ou le nombre de bits de votre runtime. Pour plus d’informations, consultez [Configurer votre application dans Azure App Service](web-sites-configure.md).

Comme vous n’avez pas à vous soucier du serveur web ou l’infrastructure d’application web, le déploiement de votre application sur le service d’application consiste à déployer votre code, les fichiers binaires, les fichiers de contenu et leur structure de répertoire respective sur l’annuaire [**/site/wwwroot** dans Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (ou le répertoire **/Data/Jobs** pour les tâches web). App Service prend en charge les trois processus de déploiement suivants.

- [FTP ou FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol) : utilisez votre outil compatible FTP ou FTPS favori pour déplacer vos fichiers vers Azure, de [FileZilla](https://filezilla-project.org) à des IDE complets comme [NetBeans](https://netbeans.org). Il s’agit d’un processus de téléchargement de fichier au sens strict. Aucun service supplémentaire n’est fourni par le Service d’application, notamment le contrôle de version, la gestion de structure de fichiers, etc. 
- [Kudu (Git/Mercurial)](https://github.com/projectkudu/kudu/wiki/Deployment) : le [moteur de déploiement](https://github.com/projectkudu/kudu/wiki) dans service d’application. Placez votre code directement dans Kudu depuis n’importe quel référentiel. Kudu offre également des services ajoutés à chaque fois que le code lui est transmis, notamment la gestion de version, la restauration de package, MSBuild et des [web hooks](https://github.com/projectkudu/kudu/wiki/Web-hooks) pour un déploiement continu et d’autres tâches Automation. Tous les services sont personnalisables et déclenchés 
    - à chaque fois que **git push** est exécuté à partir d’un référentiel Git,
	- à chaque fois que **hg push** est exécuté à partir d’un référentiel Mercurial configuré ou 
    - à chaque fois qu’un stockage cloud lié en tant que Dropbox ou OneDrive est synchronisé avec le Service d’application. 
- [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy) : outil qui automatise le déploiement de serveurs IIS. Déployez le code vers App Service directement à partir de vos outils Microsoft préférés tels que Visual Studio, WebMatrix et Visual Studio Team Services. Cet outil prend en charge le déploiement différentiel, la création de base de données, les transformations de chaînes de connexion, etc. Web Deploy diffère de Kudu parce que les fichiers binaires d’application sont générés avant d’être déployés vers Azure. Comme FTP, aucun service supplémentaire n’est fourni par le service d’application.

Les outils de développement web populaires prennent en charge un ou plusieurs de ces processus de déploiement. L’outil que vous choisissez détermine les processus de déploiement que vous pouvez exploiter, la fonctionnalité DevOps réelle à votre disposition dépend de la combinaison du processus de déploiement et les outils spécifiques que vous choisissez. Par exemple, si vous effectuez le déploiement Web à partir de [Visual Studio avec Azure SDK](#vspros), même si vous n’obtenez pas d’automatisation de la part de Kudu, vous obtenez une restauration de package et l’automatisation MSBuild dans Visual Studio. Le kit de développement logiciel Azure fournit également un Assistant simple d’utilisation pour vous aider à créer les ressources Azure dont vous avez besoin directement au sein de l’interface de Visual Studio.

>[AZURE.NOTE]Ces processus de déploiement n’[approvisionnent pas réellement les ressources Azure](resource-group-portal) dont votre application peut avoir besoin, tel que le plan App Service, l’application App Service et la base de données SQL. Toutefois, la plupart des articles sur les procédures montrent comment approvisionner l’application ET déployer votre code dessus de bout en bout. Vous trouverez également des options supplémentaires pour l’approvisionnement des ressources Azure dans la section [Automatiser le déploiement à l’aide des outils de ligne de commande](#automate).

## <a name="ftp"></a>Déploiement par copie manuelle des fichiers dans Azure
Si vous êtes habitué à copier manuellement votre contenu web sur les hébergeurs web, un flux de travail courant pour les développeurs PHP, vous pouvez utiliser un utilitaire [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) pour copier des fichiers, notamment l’Explorateur Windows ou [FileZilla](https://filezilla-project.org/).

La copie manuelle des fichiers utilise le protocole FTP pour le déploiement (voir [Vue d’ensemble du processus de déploiement](#overview)).

Les avantages de la copie manuelle de fichiers sont :

- les outils FTP sont familiers. 
- vous savez exactement où vont vos fichiers.
- la sécurité ajoutée avec FTPS.
- Il s’agit d’une solution de déploiement appropriée si vous aimez utiliser un outillage minimal de développement web (par exemple, développez des applications web à l’aide d’un bloc-notes).

Les inconvénients de la copie manuelle de fichiers sont :

- Vous êtes responsable du déploiement des fichiers dans les répertoires appropriés dans App Service.
- Aucun contrôle de version pour la restauration en cas de défaillance.
- De nombreux outils FTP ne sont pas dotés de la copie différentielle, et se contentent de copier tous les fichiers. Pour les grandes applications, les délais de déploiement sont très longs, même pour des mises à jour mineures.

### <a name="howtoftp"></a>Comment assurer le déploiement par copie manuelle des fichiers dans Azure
La copie de fichiers dans Azure fait appel à quelques étapes simples :

1. Créer des informations d’identification de déploiement pour votre application dans le [portail Azure](https://portal.azure.com). Pour ce faire, dans le panneau de votre application, cliquez sur **Paramètres** > **Informations d’identification du déploiement**.
2. Une fois que vous avez configuré les informations d’identification de déploiement, obtenez les informations de connexion FTP en accédant à **Paramètres** > **Propriétés**, puis en copiant les valeurs correspondant à **Utilisateur FTP/Développement**, **Nom d’hôte FTP** et **Nom de l’hôte FTPS**.
3. À partir de votre client FTP, utilisez les informations de connexion que vous avez recueillies pour vous connecter à votre application.
4. Copiez vos fichiers et la structure de répertoire qui leur correspond dans le répertoire [**/site/wwwroot** dans Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (ou dans le répertoire **/Data/Jobs** pour les tâches web).
5. Accédez à l’URL de votre application pour vérifier que l’application s’exécute correctement. 

Pour plus d'informations, consultez les ressources suivantes :

* [Créer une application Web PHP-MySQL dans Azure App Service et la déployer à l’aide de FTP](web-sites-php-mysql-deploy-use-ftp.md).
* [Utilisation des scripts de commandes FTP](http://support.microsoft.com/kb/96269).

## <a name="dropbox"></a>Déploiement par synchronisation avec un dossier cloud
Une bonne alternative à la [copie manuelle de fichiers](#ftp) consiste à synchroniser les fichiers et les dossiers avec App Service en utilisant un service de stockage hébergé sur le cloud tel que OneDrive et Dropbox. Dans le [portail Azure](https://portal.azure.com), vous pouvez configurer un dossier spécial dans votre stockage cloud, travailler avec votre code d’application et votre contenu dans ce dossier et à synchroniser avec App Service sur un simple clic.

La synchronisation avec un dossier de cloud utilise le processus de Kudu pour le déploiement (voir [Vue d’ensemble du processus de déploiement](#overview)).

Les avantages de la synchronisation avec un dossier cloud sont :

- la simplicité de déploiement. Les services tels que OneDrive et Dropbox fournissent des clients de synchronisation avec un ordinateur de bureau, de sorte que votre répertoire de travail local est également votre répertoire de déploiement.
- le déploiement sur un clic.
- Toutes les fonctionnalités de Kudu sont disponibles (par exemple, grâce au contrôle de version de déploiement, la restauration, la restauration de package, l’automatisation).
- Il s’agit d’une solution de déploiement appropriée si vous aimez l’outil minimal de développement web.

Les inconvénients de la synchronisation avec un dossier cloud sont :

- il ne s’agit pas d’une bonne solution pour un projet d’équipe.

### <a name="howtodropbox"></a>Déploiement par synchronisation avec un dossier cloud
 
* [Déploiement vers Web Apps à partir de Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx) (en anglais). Explique comment utiliser le [portail Azure](https://portal.azure.com) pour configurer un déploiement Dropbox.
* [Déploiement Dropbox vers Web Apps](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites) (en anglais). Cette vidéo vous guide durant le processus de connexion d’un dossier DropBox vers une application web et montre à quel point vous pouvez rapidement rendre une application web fonctionnelle ou en assurer la maintenance en utilisant un simple déploiement par glisser-déplacer.
* [Forum Azure pour Git, Mercurial et Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

## Déployer avec un IDE
Si vous utilisez déjà [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) avec un [kit de développement logiciel Azure](https://azure.microsoft.com/downloads/) ou WebMatrix ou d’autres suites IDE comme [Xcode](https://developer.apple.com/xcode/) et [Eclipse](https://www.eclipse.org), vous pouvez assurer un déploiement sur Azure directement depuis l’intérieur de votre IDE. Cette option est idéale pour un développeur individuel.

Visual Studio prend en charge les trois processus de déploiement (FTP, Git et Web Deploy), selon votre préférence, tandis que les autres IDE peuvent déployer App Service s’ils sont dotés de l’intégration de FTP ou de Git (voir [Vue d’ensemble du processus de déploiement](#overview)).

Les avantages du déploiement à l’aide d’un IDE sont :

- La réduction potentielle des outils de votre cycle de vie d’application de bout en bout. Développer, déboguer, suivre et déployer votre application dans Azure sans sortir de votre IDE. 

Les inconvénients du déploiement à l’aide d’un IDE sont les suivants :

- Une complexité accrue des outils.
- Nécessite toujours un système de contrôle de code source en cas de projet d’équipe.

<a name="vspros"></a> Voici d’autres avantages du déploiement à l’aide de Visual Studio avec le kit de développement logiciel Azure :

- Le kit de développement logiciel Azure favorise nettement les ressources Azure dans Visual Studio. Créer, supprimer, modifier, démarrer et arrêter les applications, interroger la base de données SQL du serveur principal, déboguer l’application Azure en direct, entre autres. 
- Modification en direct des fichiers de code sur Azure.
- Débogage en direct des applications sur Azure.
- Explorateur Azure intégré.
- Déploiement différentiel uniquement. 

###<a name="vs"></a>Déploiement direct à partir de Visual Studio

* [Prise en main d'Azure et ASP.NET](web-sites-dotnet-get-started.md). Explique comment créer et déployer un projet Web ASP.NET MVC simple en utilisant Visual Studio et Web Deploy.
* [Déployer des tâches Web à l’aide de Visual Studio](websites-dotnet-deploy-webjobs.md). Explique comment configurer les projets d'application console pour qu'ils se déploient sous forme de tâches web WebJobs.  
* [Créer une application ASP.NET MVC 5 avec authentification et base de données SQL et la déployer dans Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Explique comment créer et déployer un projet Web ASP.NET MVC avec une base de données SQL, en utilisant Visual Studio, Web Deploy et Entity Framework Code First Migrations.
* [Présentation d'un déploiement Web pour Visual Studio et ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Une introduction de base au déploiement Web avec Visual Studio. Ancien, mais contient des informations toujours pertinentes, notamment une présentation des options de déploiement d'une base de données ainsi qu'une application Web et une liste de tâches de déploiement supplémentaires que vous devrez effectuer, ou pour lesquelles vous devrez configurer manuellement Visual Studio. Cette rubrique traite du déploiement de manière générale et non uniquement du déploiement vers Web Apps.
* [Déploiement Web ASP.NET en utilisant Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Une série de didacticiels en 12 parties présentant un ensemble de tâches de déploiement plus complet que le reste de cette liste. Certaines fonctionnalités de déploiement Azure ont été ajoutées depuis la rédaction de ce didacticiel. Cependant, les notes ajoutées par la suite expliquent les éléments manquants.
* [Déploiement d'un site Web ASP.NET dans Azure avec Visual Studio 2012 directement depuis un référentiel Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Montre comment déployer un projet Web ASP.NET dans Visual Studio, en utilisant le plug-in Git pour valider le code sur Git et connecter Azure au référentiel Git. À partir de Visual Studio 2013, la prise en charge Git est intégrée et ne nécessite pas l’installation d’un plug-in.

###<a name="webmatrix"></a>Déploiement direct depuis WebMatrix

* [Génération et déploiement d'un site Web Node.js dans Azure avec WebMatrix](web-sites-nodejs-use-webmatrix.md).
* [Création et déploiement d’un site Web Azure PHP-MySQL avec WebMatrix](web-sites-php-mysql-use-webmatrix.md).
* [WebMatrix 3 : Git intégré et déploiement dans Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Utilisation de WebMatrix pour procéder au déploiement depuis un référentiel de contrôle de code source Git.

## <a name="onprem"></a>Déploiement à partir d’un système de contrôle du code source local

Si vous travaillez dans une équipe de développement, quelle que soit la taille, et utilisez un système de gestion de code source (SCM) local [Team Foundation Server](https://www.visualstudio.com/products/tfs-overview-vs.aspx) (TFS), [Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) ou [Mercurial](http://mercurial.selenic.com/), vous pouvez configurer App Service pour intégrer votre référentiel et déployer directement sur App Service dans votre flux de travail de contrôle source. Si vous utilisez TFS, vous pouvez également le configurer pour un déploiement continu sur App Service.

TFS utilise Web Deploy pour assurer le déploiement sur App Service, tandis que le déploiement à partir des référentiels Git/Mercurial se sert de Kudu (voir [Vue d’ensemble du processus de déploiement](#overview)).

Les avantages du déploiement à partir d’un système de contrôle source local :

- Prise en charge du déploiement dans n’importe quelle infrastructure de langage ou client Git ou Mercurial, notamment [Xcode](https://developer.apple.com/xcode/) et [Eclipse](https://www.eclipse.org).
- Déploiement spécifique de branche, possibilité de déployer différentes versions dans des [emplacements](web-sites-staged-publishing) distincts.
- Il convient aux équipes de développement de toute taille.

Inconvénients du déploiement à partir d’un système de contrôle source local :

- Besoin de connaître le système SCM choisi.
- Peut fournir davantage de fonctions et de fonctionnalités que nécessaire.
- Manque de solutions clé en main pour un déploiement continu et un déploiement spécifique de branche dans les outils client Git et Mercurial. 

Autres avantages du déploiement à l’aide de TFS :

- Intégration continue (CI) des versions, des tests et du déploiement.
- Outils de collaboration intégrés qui fonctionnent avec l’IDE ou l’éditeur existant.
- Prise en charge de Git pour le contrôle de version décentralisé ou le contrôle de version Team Foundation pour un contrôle de version centralisé (CVTF) précis. 
- Des outils riches pour un déploiement agile.
- Des intégrations prêtes à l’emploi pour [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [Azure Service Bus](/services/service-bus/) et bien d’autres encore. 
- [Team Foundation Server Express](https://www.microsoft.com/download/details.aspx?id=48259) est gratuit pour une équipe pouvant compter jusqu’à 5 développeurs.

###<a name="tfs"></a>Déploiement continu avec TFS

* [Livraison continue pour Cloud Services dans Azure](../cloud-services-dotnet-continuous-delivery.md). Ce document concerne un service cloud Azure, mais une partie de son contenu concerne aussi Web Apps.

###<a name="gitmercurial"></a>Déploiement d’un référentiel Git ou Mercurial local

* [Publication à partir du contrôle de code source sur Web Apps avec Git](web-sites-publish-source-control.md). Permet d’apprendre à utiliser Git pour publier directement à partir de votre ordinateur local vers une application web (dans Azure, cette méthode de publication est appelée Git local). Montre également comment activer le déploiement continu de référentiels Git à partir de GitHub, CodePlex ou BitBucket.
* [Publication vers Web Apps à partir de n’importe quel référentiel git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html) (en anglais). Blog qui explique la fonctionnalité « Référentiel externe » de Web Apps.
* [Forum Azure pour Git, Mercurial et Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).
* [Déploiement de DEUX sites Web sur Azure à partir d'un référentiel Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Billet de blog de Scott Hanselman.

## Déploiement à partir d’un système de contrôle source hébergé sur le cloud
Si vous travaillez dans une équipe de développement (quelle que soit sa taille) et utilisez un service de gestion de code source (SCM) basé sur le cloud [Visual Studio Team Services](http://www.visualstudio.com/) (anciennement Visual Studio Online), [GitHub](https://www.github.com), [GitLab](https://gitlab.com), [BitBucket](https://bitbucket.org/), [CodePlex](https://www.codeplex.com/), [Codebase](https://www.codebasehq.com) et [Kiln](https://www.fogcreek.com/kiln/), vous pouvez configurer le Service d’application pour l’intégrer à votre référentiel et déployer en continu.

Pour assurer le déploiement sur App Service, Visual Studio Team Services utilise Web Deploy, tandis que le déploiement à partir des référentiels en ligne utilise Kudu (voir [Vue d’ensemble du processus de déploiement](#overview)).

Les avantages du déploiement à partir d’un système de contrôle source sont :

- La prise en charge de l’infrastructure de n’importe quel langage.
- Le déploiement en continu sans configuration de référentiels Git et Mercurial. 
- Le déploiement de branche spécifique, la possibilité de déployer différentes branches à différents [emplacements](web-sites-staged-publishing).
- Il convient aux équipes de développement de toute taille.

Inconvénients du déploiement à partir d’un service de contrôle de source de cloud :

- Besoin de connaître le service SCM choisi.
- Peut fournir davantage de fonctions et de fonctionnalités que nécessaire.

Voici d’autres avantages du déploiement à l’aide de Visual Studio Team Services :

- Gratuit pour une équipe de jusqu’à 5 développeurs.
- Intégration continue (CI) des versions, des tests et du déploiement.
- Outils de collaboration intégrés qui fonctionnent avec l’IDE ou l’éditeur existant.
- Prise en charge de Git pour le contrôle de version décentralisé ou le contrôle de version Team Foundation pour un contrôle de version centralisé (CVTF) précis. 
- Des outils riches pour un déploiement agile.
- Des intégrations prêtes à l’emploi pour [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [Azure Service Bus](/services/service-bus/) et bien d’autres encore. 
- Tableaux de bord complets pour les rapports en faciles avec [connexion à Power BI](https://www.visualstudio.com/get-started/report/report-on-vso-with-power-bi-vs).

###<a name="vsts"></a>Déploiement continu avec Visual Studio Team Services

- [Livraison continue sur Azure au moyen de Visual Studio Team Services et TFVC](../cloud-services-continuous-delivery-use-vso.md). Didacticiel pas à pas montrant comment configurer des livraisons continues de Visual Studio Team Services et TFVC vers une application web, en utilisant TFVC. 
- [Diffusion continue sur Azure au moyen de Visual Studio Team Services et Git](../cloud-services-continuous-delivery-use-vso-git.md). Semblable au didacticiel précédent, si ce n'est qu'il utilise Git et non TFVC.

###<a name="cloudgitmercurial"></a>Déploiement à partir d’un référentiel Git ou Mercurial local hébergé sur le cloud

- [Publication à partir du contrôle de code source sur Web Apps avec Git](web-sites-publish-source-control.md). Activation du déploiement en continu de référentiels à partir de GitHub, CodePlex ou BitBucket. Bien que ce didacticiel montre comment publier un référentiel Git, le processus est similaire pour les référentiels Mercurial hébergés dans CodePlex ou BitBucket.
- [Déploiement vers Web Apps avec GitHub au moyen de Kudu](http://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/). Vidéo de Scott Hanselman et David Ebbo montrant comment déployer une application web directement depuis GitHub vers Service App.
- [Bouton Deploy to Azure pour Web Apps](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog sur une méthode permettant de lancer le déploiement à partir d’un référentiel Git.
- [Forum Azure pour Git, Mercurial et Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

Pour plus d’informations, consultez les ressources suivantes :

- [Publication à partir du contrôle de code source sur Web Apps avec Git](web-sites-publish-source-control.md). Permet d’apprendre à utiliser Git pour publier directement à partir de votre ordinateur local sur Web Apps (dans Azure, cette méthode de publication est appelée Git local). 

## <a name="automate"></a>Automatiser le déploiement à l’aide d’outils de ligne de commande

* [Automatiser le déploiement avec MSBuild](#msbuild)
* [Copier des fichiers avec des scripts et des outils FTP](#ftp)
* [Automatiser le déploiement avec Windows PowerShell](#powershell)
* [Automatiser le déploiement avec l’API de gestion .NET](#api)
* [Déploiement à partir de l’interface de ligne de commande Azure](#cli)
* [Déploiement à partir de la ligne de commande Web Deploy](#webdeploy)
* [Utilisation des scripts de commandes FTP](http://support.microsoft.com/kb/96269).
 
Une autre option de déploiement consiste à utiliser un service basé sur le cloud comme [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Pour plus d'informations, consultez la page [Déploiement d’applications ASP.NET sur des sites web Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatiser le déploiement avec MSBuild

Si vous utilisez l'[IDE Visual Studio](#vs) pour le développement, vous pouvez utiliser [MSBuild](http://msbuildbook.com/) pour automatiser tout ce que vous pouvez faire dans votre IDE. Vous pouvez configurer MSBuild pour utiliser [Web Deploy](#webdeploy) ou le [FTP/FTPS](#ftp) pour copier vos fichiers. Web Deploy peut également automatiser de nombreuses autres tâches de déploiement, comme le déploiement des bases de données.

Pour plus d'informations sur le déploiement en ligne de commande avec MSBuild, consultez les ressources suivantes :

* [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment) (en anglais). Une série d'une dizaine de didacticiels sur le déploiement dans Azure avec Visual Studio. Montre comment utiliser la ligne de commande pour le déploiement après la configuration de profils de publication dans Visual Studio.
* [Présentation de Microsoft Build Engine : utilisation de MSBuild et Team Foundation Build](http://msbuildbook.com/). Manuel comportant des chapitres sur l'utilisation de MSBuild pour le déploiement.

###<a name="powershell"></a>Automatiser le déploiement avec Windows PowerShell

Vous pouvez utiliser des fonctions de déploiement MSBuild ou FTP à partir de [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Ce faisant, vous pouvez également utiliser une collection de cmdlets Windows PowerShell facilitant l'appel de l'API de gestion REST Azure.

Pour plus d’informations, consultez les ressources suivantes :

* [Déployer une application Web liée à un référentiel GitHub](app-service-web-arm-from-github-provision.md)
* [Mettre en service une application Web avec une base de données SQL](app-service-web-arm-with-sql-database-provision.md)
* [Mise en service et déploiement de microservices de manière prévisible dans Azure](app-service-deploy-complex-application-predictably.md)
* [Tout automatiser (développement d'applications de cloud concrètes avec Azure)](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Chapitre d'un livre électronique expliquant comment l'exemple d'application contenu dans le livre utilise des scripts Windows PowerShell pour créer un environnement de test Azure et y procéder à un déploiement. Consultez la section [Ressources](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) pour obtenir des liens vers une documentation Azure PowerShell supplémentaire.
* [Utilisation des scripts Windows PowerShell pour la publication dans des environnements de développement et de test](http://msdn.microsoft.com/library/dn642480.aspx). Explique comment utiliser les scripts Windows PowerShell générés par Visual Studio.

###<a name="api"></a>Automatiser le déploiement avec l’API de gestion .NET

Vous pouvez écrire un code C# pour utiliser des fonctions MSBuild ou FTP pour vos déploiements. Ce faisant, vous pouvez accéder à l'API REST de gestion Azure pour utiliser des fonctions de gestion de site.

Pour plus d'informations, consultez les ressources suivantes :

* [Tout automatiser avec les bibliothèques de gestion Azure et .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Présente l’API de gestion .NET et fournit des liens vers d’autres documentations.

###<a name="cli"></a>Déploiement à partir de l’interface de ligne de commande Azure (Azure CLI)

Vous pouvez utiliser la ligne de commande avec des ordinateurs Windows, Mac ou Linux pour vos déploiements en utilisant le FTP. Ce faisant, vous pouvez également accéder à l'API de gestion REST Azure en utilisant l'interface en ligne de commande interplateforme.

Pour plus d’informations, consultez les ressources suivantes :

* [Outils en ligne de commande Azure](/downloads/#cmd-line-tools). Page du portail Azure.com qui fournit des informations sur les outils en ligne de commande.

###<a name="webdeploy"></a>Déploiement à partir de la ligne de commande Web Deploy

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) est un logiciel Microsoft pour le déploiement dans IIS qui fournit des fonctionnalités de synchronisation des fichiers intelligentes et effectue ou coordonne également de nombreuses autres tâches liées au déploiement qui ne peuvent pas être automatisées lorsque vous utilisez le FTP. Par exemple, Web Deploy peut déployer une nouvelle base de données ou mettre à jour une base de données existante avec votre application web. Web Deploy peut également réduire le délai de mise à jour d'un site existant, en copiant uniquement les fichiers modifiés. Microsoft WebMatrix, Visual Studio, Visual Studio Team Services et Team Foundation Server prennent en charge le logiciel Web Deploy intégré, mais vous pouvez également utiliser Web Deploy directement depuis la ligne de commande pour automatiser le déploiement. Les commandes Web Deploy sont très efficaces, mais leur apprentissage peut être long.

Pour plus d'informations, consultez les ressources suivantes :

* [Web Apps en toute simplicité : déploiement](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/) (en anglais). Blog de David Ebbo sur un outil, qu’il a conçu pour faciliter l’utilisation de Web Deploy.
* [Outils de déploiement Web](http://technet.microsoft.com/library/dd568996). Documentation officielle sur le site Microsoft TechNet. Ancien, mais constitue un bon point de départ.
* [Utilisation de Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Documentation officielle sur le site Microsoft IIS.NET. Également ancien, mais constitue un bon point de départ.
* [StackOverflow](http://www.stackoverflow.com). L'endroit idéal pour obtenir des informations plus récentes sur l'utilisation de Web Deploy à partir de la ligne de commande.
* [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment) (en anglais). MSBuild est le moteur de génération utilisé par Visual Studio, mais il peut aussi être utilisé à partir de la ligne de commande pour déployer des applications web dans Web Apps. Ce didacticiel fait partie d'une série traitant principalement des déploiements Visual Studio.

##<a name="nextsteps"></a>Étapes suivantes

Dans certains cas, vous souhaiterez probablement pouvoir basculer facilement entre une version intermédiaire et de production de votre application web. Pour plus d’informations, consultez [Configurer des environnements intermédiaires pour les applications Web dans Azure App Service](web-sites-staged-publishing.md).

Posséder un programme de sauvegarde et de restauration établi représente une partie essentielle de tout flux de travail de déploiement. Pour plus d’informations concernant la fonctionnalité de sauvegarde et de restauration de Web Apps, consultez [Sauvegarder une application Web dans Azure App Service](web-sites-backup.md).

Pour plus d’informations sur l’utilisation du contrôle d’accès en fonction du rôle Azure afin de gérer l’accès au déploiement Web Apps, consultez [RBAC et publication Web Apps](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing) (en anglais).

Pour plus d’informations concernant d’autres rubriques de déploiement, consultez la section Déploiement figurant dans la [documentation Web Apps](/documentation/services/web-sites/).

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
 

<!---HONumber=AcomDC_0114_2016-->