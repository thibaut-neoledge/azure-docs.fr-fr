<properties linkid="manage-services-how-to-deploy-websites" pageTitle="How to deploy an Azure Website" metaKeywords="Azure deploy publish web site" description="Learn what methods are available for deploying content to an Azure Website." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Website" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/02/2014" ms.author="tdykstra" />

# Déploiement d'un site Web Azure

Vous disposez de plusieurs options pour déployer vos contenus dans un site Web Azure. Cette rubrique présente rapidement chaque option et propose des liens renvoyant vers des informations supplémentaires.

-   [Déploiement à partir d'un système de contrôle du code source hébergé sur le cloud][Déploiement à partir d'un système de contrôle du code source hébergé sur le cloud]
    -   Visual Studio Online (VSO)
    -   Sites Web référentiels avec Git
    -   Sites Web référentiels avec Mercurial
    -   Dropbox
-   [Déploiement à partir d'un environnement de développement intégré (IDE)][Déploiement à partir d'un environnement de développement intégré (IDE)]
    -   Visual Studio
    -   WebMatrix
-   [Déploiement à l'aide d'un utilitaire FTP][Déploiement à l'aide d'un utilitaire FTP]
-   [Déploiement à partir d'un système de contrôle du code source local][Déploiement à partir d'un système de contrôle du code source local]
    -   Team Foundation Server (TFS)
    -   Référentiels Git ou Mercurial locaux
-   [Utilisation des outils en ligne de commande et de l'API de gestion REST Azure][Utilisation des outils en ligne de commande et de l'API de gestion REST Azure]
    -   MSBuild
    -   Scripts FTP
    -   Windows PowerShell
    -   API de gestion .NET
    -   Ligne de commande interplateforme (xpat-cli)
    -   Ligne de commande Web Deploy

## <a name="cloud"></a>Déploiement à partir d'un système de contrôle du code source hébergé sur le cloud

La meilleure méthode pour déployer un site Web consiste à configurer un [flux de livraison continu][flux de livraison continu] intégré à votre [système de contrôle du code source][système de contrôle du code source]. L'automatisation renforce l'efficacité du processus de développement, ainsi que la gestion et la fiabilité de vos processus de sauvegarde et de restauration.

Si vous n'avez pas encore de contrôle de code source, la méthode la plus simple pour commencer est d'utiliser un système de contrôle de code source hébergé sur le cloud.

### <a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online][Visual Studio Online] (anciennement Team Foundation Service) est une solution Microsoft basée sur le cloud pour le contrôle du code source et la collaboration d'équipe. Ce service est gratuit pour une équipe allant jusqu'à 5 développeurs. Vous pouvez configurer VSO pour qu'il effectue des envois continus vers un site Web Azure et votre référentiel peut utiliser [Git ou TFVC][Visual Studio Online].

Pour plus d'informations, consultez les ressources suivantes :

-   [Envoi vers Azure en continu avec VSO et TFVC][Envoi vers Azure en continu avec VSO et TFVC]. Bref didacticiel pas à pas montrant comment configurer des envois continus de VSO vers un site Web Azure, en utilisant TFVC. TFVC est l'option de contrôle du code source centralisée dans VSO, contrairement à Git, qui est l'option de contrôle du code source distribuée.
-   [Livraison continue sur Azure au moyen de VSO et TFVC][Livraison continue sur Azure au moyen de VSO et TFVC]. Semblable au didacticiel précédent, mais celui-ci présente également les étapes pour demander un compte VSO et archiver un projet dans le contrôle de code source
-   [Livraison continue sur Azure au moyen de Visual Studio Online et Git][Livraison continue sur Azure au moyen de Visual Studio Online et Git]. Semblable au didacticiel précédent, si ce n'est qu'il utilise Git et non TFVC.

### <a name="git"></a>Sites Web référentiels avec Git

[MySQL][Visual Studio Online] est un système de contrôle de code source distribué populaire. Azure dispose de fonctionnalités intégrées qui facilitent l'automatisation du déploiement vers un site Web Azure depuis des sites référentiels basés sur le Web qui stockent des référentiels Git, notamment [GitHub][GitHub], [CodePlex][CodePlex] et [BitBucket][BitBucket]. L'avantage de l'utilisation de Git pour le déploiement est qu'il est assez facile de revenir à un déploiement précédent le cas échéant.

Pour plus d'informations, consultez les ressources suivantes :

-   [Publication à partir du contrôle de code source sur des sites Web Azure][Publication à partir du contrôle de code source sur des sites Web Azure]. Permet d'apprendre à utiliser Git pour publier directement à partir de votre ordinateur local sur un site Web Azure (dans Azure, cette méthode de publication est appelée Git local). Montre également comment activer le déploiement continu de référentiels Git à partir de GitHub, CodePlex ou BitBucket.
-   [Déploiement vers des sites Web avec GitHub au moyen de Kudu][Déploiement vers des sites Web avec GitHub au moyen de Kudu]. Vidéo de Scott Hanselman et David Ebbo montrant comment déployer un site Web directement depuis GitHub vers un site Web Azure.
-   [Forum Azure pour Git, Mercurial et DropBox][Forum Azure pour Git, Mercurial et DropBox].

### <a name="mercurial"></a>Sites Web référentiels avec Mercurial

Si vous utilisez [Mercurial][Mercurial] comme système de contrôle de code source et que vous stockez votre référentiel dans [CodePlex][CodePlex] ou [BitBucket][BitBucket], vous pouvez utiliser des fonctionnalités intégrées dans des sites Web Azure pour déployer automatiquement vos contenus.

Pour plus d'informations sur le déploiement avec Mercurial, consultez les ressources suivantes :

-   [Publication à partir du contrôle de code source sur des sites Web Azure][Publication à partir du contrôle de code source sur des sites Web Azure]. Bien que ce didacticiel montre comment publier un référentiel Git, le processus est similaire pour les référentiels Mercurial hébergés dans CodePlex ou BitBucket.
-   [Forum Azure pour Git, Mercurial et DropBox][Forum Azure pour Git, Mercurial et DropBox].

### <a name="dropbox"></a>Dropbox

[Dropbox][Dropbox] n'est pas un système de contrôle de code source, mais si vous stockez votre code source dans Dropbox, vous pouvez automatiser le déploiement à partir de votre compte Dropbox.

-   [Déploiement vers Windows Azure avec Dropbox][Déploiement vers Windows Azure avec Dropbox]. Explique comment utiliser le portail de gestion Azure pour configurer un déploiement Dropbox.
-   [Dropbox et sites Web Azure][Dropbox et sites Web Azure]. Cette vidéo vous guide durant le processus de connexion d'un dossier DropBox à un site Web Azure, et montre à quel point vous pouvez rapidement rendre un site Web fonctionnel ou en assurer la maintenance en utilisant un simple déploiement par glisser-déplacer.
-   [Forum Azure pour Git, Mercurial et Dropbox][Forum Azure pour Git, Mercurial et DropBox].

## <a name="ide"></a>Déploiement à partir d'un environnement de développement intégré (IDE)

[Visual Studio][Visual Studio] et [WebMatrix][WebMatrix] sont des environnements de développement intégrés Microsoft (ou IDE) que vous pouvez utiliser pour le développement Web. Ils fournissent tous deux des fonctionnalités intégrées facilitant le déploiement de sites Web Azure. De même, ils peuvent tous deux utiliser [Web Deploy][Web Deploy] pour automatiser les tâches liées au déploiement, comme le déploiement d'une base de données et les modifications de chaînes de connexion. Enfin, ils peuvent tous deux être déployés en utilisant [FTP ou FTPS][FTP ou FTPS].

WebMatrix est rapide à installer et facile à maîtriser, mais Visual Studio offre bien plus de fonctionnalités pour utiliser les sites Web Azure. Dans l'IDE Visual Studio, vous pouvez créer, arrêter, démarrer et supprimer les sites Web Azure, afficher des fichiers journaux pendant leur création en temps réel, procéder à un débogage à distance, etc. Visual Studio intègre également des systèmes de contrôle de code source tels que [Visual Studio Online][1], [Team Foundation Server][Team Foundation Server] et les [référentiels Git][référentiels Git].

### <a name="vs"></a>Visual Studio

Pour plus d'informations sur le déploiement dans des sites Web Azure depuis Visual Studio, consultez les ressources suivantes :

-   [Prise en main d'Azure et ASP.NET][Prise en main d'Azure et ASP.NET]. Explique comment créer et déployer un projet Web ASP.NET MVC simple en utilisant Visual Studio et Web Deploy.
-   [Déploiement d'Azure WebJobs vers les sites Web Azure][Déploiement d'Azure WebJobs vers les sites Web Azure]. Explique comment configurer les projets d'application console pour qu'ils se déploient sous forme de WebJobs.
-   [Déploiement d'une application ASP.NET MVC 5 sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure][Déploiement d'une application ASP.NET MVC 5 sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure]. Explique comment créer et déployer un projet Web ASP.NET MVC avec une base de données SQL, en utilisant Visual Studio, Web Deploy et Entity Framework Code First Migrations.
-   [Présentation d'un déploiement Web pour Visual Studio et ASP.NET][Présentation d'un déploiement Web pour Visual Studio et ASP.NET]. Une introduction de base au déploiement Web avec Visual Studio. Ancien, mais contient des informations toujours pertinentes, notamment une présentation des options de déploiement d'une base de données ainsi qu'une application Web et une liste de tâches de déploiement supplémentaires que vous devrez effectuer, ou pour lesquelles vous devrez configurer manuellement Visual Studio. Cette rubrique traite du déploiement de manière générale et non uniquement du déploiement vers les sites Web Azure.
-   [Déploiement Web ASP.NET en utilisant Visual Studio][Déploiement Web ASP.NET en utilisant Visual Studio]. Une série de didacticiels en 12 parties présentant un ensemble de tâches de déploiement plus complet que le reste de cette liste.
-   [Déploiement d'un site Web ASP.NET dans Azure avec Visual Studio 2012 directement depuis un référentiel Git][Déploiement d'un site Web ASP.NET dans Azure avec Visual Studio 2012 directement depuis un référentiel Git]. Montre comment déployer un projet Web ASP.NET dans Visual Studio, en utilisant le plug-in Git pour valider le code sur Git et connecter Azure au référentiel Git.

### <a name="webmatrix"></a>WebMatrix

Pour plus d'informations sur le déploiement dans des sites Web Azure depuis WebMatrix, consultez les ressources suivantes :

-   [Développement et déploiement d'un site Web avec Microsoft WebMatrix][Développement et déploiement d'un site Web avec Microsoft WebMatrix]. Montre comment créer un site Web ASP.NET simple en utilisant un modèle WebMatrix et en le déployant sur un site Web Azure en utilisant WebMatrix et Web Deploy.
-   [Génération et déploiement d'un site Web Node.js dans Azure avec WebMatrix][Génération et déploiement d'un site Web Node.js dans Azure avec WebMatrix].
-   [Création et déploiement d'un site Web Azure PHP-MySQL avec WebMatrix][Création et déploiement d'un site Web Azure PHP-MySQL avec WebMatrix].
-   [WebMatrix 3 : Git intégré et déploiement dans Azure][WebMatrix 3 : Git intégré et déploiement dans Azure]. Utilisation de WebMatrix pour procéder au déploiement depuis un référentiel de contrôle de code source Git.

## <a name="ftp"></a>Déploiement à l'aide d'un utilitaire FTP

Indépendamment de l'IDE que vous utilisez, vous pouvez également déployer des contenus dans votre site en utilisant le [FTP][FTP ou FTPS] pour copier des fichiers. Vous pouvez facilement créer des informations d'identification FTP pour un site Web Azure, puis les utiliser dans n'importe quelle application fonctionnant avec le FTP, notamment les navigateurs tels qu'Internet Explorer et les utilitaires gratuits et complets tels que [FileZilla][FileZilla]. Les sites Web Azure prennent également en charge le protocole FTPS, plus sécurisé.

Bien que les utilitaires FTP permettent de copier facilement vos fichiers de site Web dans Azure, ils n'effectuent ou ne coordonnent pas automatiquement de tâches de déploiement telles que le déploiement d'une base de données ou la modification de chaînes de connexion. De même, de nombreux outils FTP ne comparent pas les fichiers source et de destination afin d'ignorer la copie des fichiers qui n'ont pas été modifiés. Pour des sites volumineux, le fait de toujours copier tous les fichiers peut allonger la durée des déploiements, même dans le cas d'une mise à jour mineure, puisque tous les fichiers sont toujours copiés.

Pour plus d'informations, consultez les ressources suivantes :

-   [Création et déploiement d'un site Web Azure PHP-MySQL avec FTP][Création et déploiement d'un site Web Azure PHP-MySQL avec FTP].
-   [Gestion des sites Web][Gestion des sites Web]. Contient des informations supplémentaires non incluses dans le didacticiel sur la configuration des informations d'identification FTP.

## <a name="onpremises"></a>Déploiement à partir d'un système de contrôle du code source local

Si vous utilisez TFS, Git ou Mercurial dans un référentiel local (qui n'est pas hébergé sur le cloud), vous pouvez déployer directement depuis votre référentiel vers vos sites Web Azure.

### <a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server est une solution Microsoft locale pour le contrôle du code source et la collaboration d'équipe. Vous pouvez configurer TFS pour qu'il procède à des livraisons continues vers un site Web Azure.

Pour plus d'informations, consultez les ressources suivantes :

-   [Livraison continue pour Cloud Services dans Azure][Livraison continue pour Cloud Services dans Azure]. Ce document concerne un service cloud Azure, mais une partie de son contenu concerne aussi les sites Web.

### <a name="gitmercurial"></a>Référentiels Git ou Mercurial locaux

Dans Azure, vous pouvez entrer l'URL de n'importe quel référentiel utilisant Git ou Mercurial, afin d'effectuer un déploiement depuis cet emplacement. Vous pouvez également procéder à un envoi depuis un référentiel Git local vers un site Web Azure.

Pour plus d'informations, consultez les ressources suivantes :

-   [Publication à partir du contrôle de code source sur des sites Web Azure][Publication à partir du contrôle de code source sur des sites Web Azure]. Permet d'apprendre à utiliser Git pour publier directement à partir de votre ordinateur local sur un site Web Azure (dans Azure, cette méthode de publication est appelée Git local). Montre également comment activer le déploiement continu de référentiels Git à partir de GitHub, CodePlex ou BitBucket.
-   [Publication vers des sites Web Azure à partir de n'importe quel référentiel git/hg repo][Publication vers des sites Web Azure à partir de n'importe quel référentiel git/hg repo]. Blog tenu par David Ebbo qui détaille la fonctionnalité « Référentiel externe » des sites Web Azure.
-   [Forum Azure pour Git, Mercurial et Dropbox][Forum Azure pour Git, Mercurial et DropBox].
-   [Déploiement de DEUX sites Web sur Azure à partir d'un référentiel Git][Déploiement de DEUX sites Web sur Azure à partir d'un référentiel Git]. Billet de blog de Scott Hanselman.

## <a name="commandline"></a> Utilisation des outils en ligne de commande et de l'API de gestion REST Azure

Il est toujours recommandé d'automatiser votre workflow de développement, mais si vous ne pouvez pas le faire directement dans votre système de contrôle de code source, vous pouvez le configurer manuellement en utilisant les outils en ligne de commande. Cela implique généralement l'utilisation de plusieurs outils ou infrastructures, car un déploiement implique souvent l'utilisation de fonctions de gestion de site, ainsi que la copie de contenus.

Azure simplifie les tâches de gestion de site qui vous attendent lors d'un déploiement, en fournissant une API de gestion REST et plusieurs infrastructures facilitant l'utilisation de l'API.

### <a name="msbuild"></a>MSBuild

Si vous utilisez l'[IDE Visual Studio][IDE Visual Studio] pour le développement, vous pouvez utiliser [MSBuild][MSBuild] pour automatiser tout ce que vous pouvez faire dans votre IDE. Vous pouvez configurer MSBuild pour utiliser [Web Deploy][2] ou le [FTP/FTPS][Déploiement à l'aide d'un utilitaire FTP] pour copier vos fichiers. Web Deploy peut également automatiser de nombreuses autres tâches de déploiement, comme le déploiement des bases de données.

Pour plus d'informations sur le déploiement en ligne de commande avec MSBuild, consultez les ressources suivantes :

-   [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande][Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande]. Une série d'une dizaine de didacticiels sur le déploiement dans Azure avec Visual Studio. Montre comment utiliser la ligne de commande pour le déploiement après la configuration de profils de publication dans Visual Studio.
-   [Présentation de Microsoft Build Engine : utilisation de MSBuild et Team Foundation Build][MSBuild]. Manuel comportant des chapitres sur l'utilisation de MSBuild pour le déploiement.

### <a name="ftp2"></a>Scripts FTP

Vous pouvez créer facilement des informations d'identification [FTP/FTPS][FTP ou FTPS] pour un site Web Azure, puis les utiliser avec des scripts de commandes FTP.

Pour plus d'informations, consultez les ressources suivantes :

-   [Utilisation des scripts de commandes FTP][Utilisation des scripts de commandes FTP].

### <a name="powershell"></a>Windows PowerShell

Vous pouvez utiliser des fonctions de déploiement MSBuild ou FTP à partir de [Windows PowerShell][Windows PowerShell]. Ce faisant, vous pouvez également utiliser une collection de cmdlets Windows PowerShell facilitant l'appel de l'API de gestion REST Azure.

Pour plus d'informations, consultez les ressources suivantes :

-   [Tout automatiser (développement d'applications de cloud concrètes avec Azure)][Tout automatiser (développement d'applications de cloud concrètes avec Azure)]. Chapitre d'un livre électronique expliquant comment l'exemple d'application contenu dans le livre utilise des scripts Windows PowerShell pour créer un environnement de test Azure et y procéder à un déploiement. Consultez la section [Ressources][Ressources] pour obtenir des liens vers une documentation Azure PowerShell supplémentaire.

### <a name="api"></a>API de gestion .NET

Vous pouvez écrire un code C# pour utiliser des fonctions MSBuild ou FTP pour vos déploiements. Ce faisant, vous pouvez accéder à l'API REST de gestion Azure pour utiliser des fonctions de gestion de site.

Pour plus d'informations, consultez les ressources suivantes :

-   [Tout automatiser avec les bibliothèques de gestion Azure et .NET][Tout automatiser avec les bibliothèques de gestion Azure et .NET]. Présente l'API de gestion .NET et fournit des liens vers d'autres documentations.

### <a name="cli"></a>Ligne de commande interplateforme (xpat-cli)

Vous pouvez utiliser la ligne de commande avec des ordinateurs Mac ou Linux pour vos déploiements en utilisant le FTP. Ce faisant, vous pouvez également accéder à l'API de gestion REST Azure en utilisant l'interface en ligne de commande interplateforme (xpat-cli). Vous pouvez également utiliser xpat-cli sur des ordinateurs Windows.

Pour plus d'informations, consultez les ressources suivantes :

-   [Outils en ligne de commande][Outils en ligne de commande]. Page du portail Azure.com qui fournit des informations sur les outils en ligne de commande.

### <a name="webdeploy"></a>Ligne de commande Web Deploy

[Web Deploy][Web Deploy] est un logiciel Microsoft pour le déploiement dans IIS qui fournit des fonctionnalités de synchronisation des fichiers intelligentes et effectue ou coordonne également de nombreuses autres tâches liées au déploiement qui ne peuvent pas être automatisées lorsque vous utilisez le FTP. Par exemple, Web Deploy peut déployer une nouvelle base de données ou mettre à jour une base de données existante avec votre site Web. Web Deploy peut également réduire le délai de mise à jour d'un site existant, en copiant uniquement les fichiers modifiés. Microsoft WebMatrix, Visual Studio, Visual Studio Online et Team Foundation Server prennent en charge le logiciel Web Deploy intégré, mais vous pouvez également utiliser Web Deploy directement depuis la ligne de commande pour automatiser le déploiement. Les commandes Web Deploy sont très efficaces, mais leur apprentissage peut être long.

Pour plus d'informations, consultez les ressources suivantes :

-   [Outils de déploiement Web][Outils de déploiement Web]. Documentation officielle sur le site Microsoft TechNet. Ancien, mais constitue un bon point de départ.
-   [Utilisation de Web Deploy][Utilisation de Web Deploy]. Documentation officielle sur le site Microsoft IIS.NET. Également ancien, mais constitue un bon point de départ.
-   [StackOverflow][StackOverflow]. L'endroit idéal pour obtenir des informations plus récentes sur l'utilisation de Web Deploy à partir de la ligne de commande.
-   [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande][Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande]. MSBuild est le moteur de génération utilisé par Visual Studio, mais il peut aussi être utilisé à partir de la ligne de commande pour déployer des applications Web dans des sites Web Azure. Ce didacticiel fait partie d'une série traitant principalement des déploiements Visual Studio.

## <a name="nextsteps"></a>Étapes suivantes

Dans certains cas, vous souhaiterez probablement pouvoir basculer entre une version intermédiaire et de production de votre site Web. Vous pouvez le faire en utilisant la fonctionnalité de déploiement intermédiaire des sites Web Azure. Pour plus d’informations, consultez la page [Déploiement intermédiaire dans Sites Web Microsoft Azure][Déploiement intermédiaire dans Sites Web Microsoft Azure].

Posséder un programme de sauvegarde et de restauration établi représente une partie essentielle de tout flux de travail de déploiement. Pour plus d'informations concernant la fonctionnalité de sauvegarde et de restauration des sites Web Azure, consultez [Sauvegarde des sites Web Azure][Sauvegarde des sites Web Azure].

Pour plus d'informations concernant d'autres rubriques de déploiement, consultez la section Déploiement de la [documentation des sites Web Azure][documentation des sites Web Azure].

  [Déploiement à partir d'un système de contrôle du code source hébergé sur le cloud]: #cloud
  [Déploiement à partir d'un environnement de développement intégré (IDE)]: #ide
  [Déploiement à l'aide d'un utilitaire FTP]: #ftp
  [Déploiement à partir d'un système de contrôle du code source local]: #onpremises
  [Utilisation des outils en ligne de commande et de l'API de gestion REST Azure]: #commandline
  [flux de livraison continu]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [système de contrôle du code source]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [Visual Studio Online]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs
  [Envoi vers Azure en continu avec VSO et TFVC]: http://www.visualstudio.com/fr-fr/learn/continuous-delivery-in-vs
  [Livraison continue sur Azure au moyen de VSO et TFVC]: /fr-fr/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [Livraison continue sur Azure au moyen de Visual Studio Online et Git]: http://azure.microsoft.com/fr-fr/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
  [GitHub]: http://www.github.com
  [CodePlex]: http://www.codeplex.com/
  [BitBucket]: https://bitbucket.org/
  [Publication à partir du contrôle de code source sur des sites Web Azure]: /fr-fr/documentation/articles/web-sites-publish-source-control/
  [Déploiement vers des sites Web avec GitHub au moyen de Kudu]: /fr-fr/documentation/videos/deploying-to-azure-from-github/
  [Forum Azure pour Git, Mercurial et DropBox]: http://social.msdn.microsoft.com/Forums/windowsazure/fr-fr/home?forum=azuregit
  [Mercurial]: http://mercurial.selenic.com/
  [Dropbox]: https://www.dropbox.com/
  [Déploiement vers Windows Azure avec Dropbox]: http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx
  [Dropbox et sites Web Azure]: http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites
  [Visual Studio]: http://www.visualstudio.com/fr-fr/downloads/download-visual-studio-vs.aspx
  [WebMatrix]: http://www.microsoft.com/web/webmatrix/
  [Web Deploy]: http://www.iis.net/downloads/microsoft/web-deploy
  [FTP ou FTPS]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [1]: #vso
  [Team Foundation Server]: #tfs
  [référentiels Git]: #git
  [Prise en main d'Azure et ASP.NET]: /fr-fr/develop/net/tutorials/get-started/
  [Déploiement d'Azure WebJobs vers les sites Web Azure]: /fr-fr/documentation/articles/websites-dotnet-deploy-webjobs/
  [Déploiement d'une application ASP.NET MVC 5 sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure]: /fr-fr/develop/net/tutorials/web-site-with-sql-database/
  [Présentation d'un déploiement Web pour Visual Studio et ASP.NET]: http://msdn.microsoft.com/fr-fr/library/dd394698.aspx
  [Déploiement Web ASP.NET en utilisant Visual Studio]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction
  [Déploiement d'un site Web ASP.NET dans Azure avec Visual Studio 2012 directement depuis un référentiel Git]: http://www.dotnetcurry.com/ShowArticle.aspx?ID=881
  [Développement et déploiement d'un site Web avec Microsoft WebMatrix]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-dotnet-using-webmatrix/
  [Génération et déploiement d'un site Web Node.js dans Azure avec WebMatrix]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Création et déploiement d'un site Web Azure PHP-MySQL avec WebMatrix]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-php-mysql-use-webmatrix/
  [WebMatrix 3 : Git intégré et déploiement dans Azure]: http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD
  [FileZilla]: https://filezilla-project.org/
  [Création et déploiement d'un site Web Azure PHP-MySQL avec FTP]: /fr-fr/documentation/articles/web-sites-php-mysql-deploy-use-ftp/
  [Gestion des sites Web]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-manage#ftp-credentials
  [Livraison continue pour Cloud Services dans Azure]: /fr-fr/develop/net/common-tasks/continuous-delivery/
  [Publication vers des sites Web Azure à partir de n'importe quel référentiel git/hg repo]: http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html
  [Déploiement de DEUX sites Web sur Azure à partir d'un référentiel Git]: http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx
  [IDE Visual Studio]: #vs
  [MSBuild]: http://msbuildbook.com/
  [2]: #webdeploy
  [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment
  [Utilisation des scripts de commandes FTP]: http://support.microsoft.com/kb/96269
  [Windows PowerShell]: http://msdn.microsoft.com/fr-fr/library/dd835506.aspx
  [Tout automatiser (développement d'applications de cloud concrètes avec Azure)]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Ressources]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources
  [Tout automatiser avec les bibliothèques de gestion Azure et .NET]: http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx
  [Outils en ligne de commande]: /fr-fr/downloads/#cmd-line-tools
  [Outils de déploiement Web]: http://technet.microsoft.com/fr-fr/library/dd568996
  [Utilisation de Web Deploy]: http://www.iis.net/learn/publish/using-web-deploy
  [StackOverflow]: http://www.stackoverflow.com
  [Déploiement intermédiaire dans Sites Web Microsoft Azure]: /fr-fr/documentation/articles/web-sites-staged-publishing/
  [Sauvegarde des sites Web Azure]: /fr-fr/documentation/articles/web-sites-backup/
  [documentation des sites Web Azure]: /fr-fr/documentation/services/web-sites/
