<properties pageTitle="What is the Azure .NET SDK" metaKeywords="azure .net sdk" description="Learn what is included in the Azure .NET SDK." documentationCenter=".NET" title="What is the Azure .NET SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Présentation du Kit de développement logiciel (SDK) Azure pour .NET

Le Kit de développement logiciel (SDK) Azure pour .NET est un ensemble d'outils Visual Studio, d'outils en ligne de commande, de fichiers binaires runtime et de bibliothèques clientes qui vous aident à développer, à tester et à déployer des applications s'exécutant dans Azure. Cet article présente en détail ce que vous obtenez lorsque vous installez le Kit de développement logiciel (SDK). Vous pouvez le télécharger à partir de la [page de téléchargements Azure](/en-us/downloads/).

Le Kit de développement logiciel (SDK) Azure pour .NET comprend également des bibliothèques clientes permettant d'utiliser les services Azure. Celles-ci sont installées séparément au moyen de [NuGet](http://go.microsoft.com/fwlink/?LinkId=510472).

## Sommaire

-   [Éléments inclus lorsque vous installez le Kit de développement logiciel (SDK) Azure pour .NET](#included)
-   [Éléments non inclus lorsque vous installez le Kit de développement logiciel (SDK) Azure pour .NET](#notincluded)
-   [Forum Aux Questions](#faq)
-   [Ressources](#resources)

## <span id="included"></span></a>Éléments inclus dans le Kit de développement logiciel (SDK) Azure pour .NET

Le Kit de développement logiciel (SDK) Azure pour .NET installe les produits suivants :

-   [Visual Studio Express pour le web](#vwd)
-   [Microsoft ASP.NET et outils web pour Visual Studio](#wte)
-   [Outils Microsoft Azure pour Microsoft Visual Studio](#tools)
-   [Outils de création Microsoft Azure](#auth)
-   [Émulateur Microsoft Azure](#emulator)
-   [Émulateur de stockage Microsoft Azure](#stgemulator)
-   [Outils de stockage Microsoft Azure](#stgtools)
-   [Bibliothèques Microsoft Azure pour .NET](#libraries)
-   [Complément de publication LightSwitch Azure pour Visual Studio](#ls)

### <span id="vwd"></span></a>Visual Studio Express pour le web

Si vous ne disposez pas de Visual Studio sur votre ordinateur, le Kit de développement logiciel (SDK) installe [Visual Studio Express pour le web](http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx).

### <span id="wte"></span></a>Microsoft ASP.NET et outils Web pour Visual Studio

Les rubriques suivantes vous permettent d'utiliser Sites Web Azure :

-   [Publication de projets web sur Sites Web Azure](../web-sites-dotnet-get-started/)
-   [Publication de projets d'application console sur Tâches web Azure](../websites-dotnet-deploy-webjobs/)
-   [Création de ressources Sites Web Azure et Base de données SQL lors de la création ou de la publication d'un projet web](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/)
-   [Création de scripts de déploiement PowerShell lors de la création de nouveaux sites web](http://msdn.microsoft.com/en-us/library/dn642480.aspx)
-   [Gestion et dépannage de Sites Web Azure dans l'Explorateur de serveurs](../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement)
-   [Exécution en mode débogage à distance pour les sites et les tâches web](../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug)

> [WACOM.NOTE] Vous ne devez pas installer le Kit de développement logiciel (SDK) Azure pour .NET pour pouvoir utiliser ces fonctionnalités ; celles-ci sont également incluses dans les mises à jour Visual Studio.

### <span id="tools"></span></a>Outils Microsoft Azure pour Microsoft Visual Studio

Les rubriques suivantes vous permettent d'utiliser Azure Cloud Services et Azure Virtual Machines :

-   [Création, ouverture et publication de projets de service cloud](../cloud-services-dotnet-get-started/)
-   [Création de packages de déploiement pour les projets de service cloud](http://msdn.microsoft.com/en-us/library/ff683672.aspx)
-   [Création de machines virtuelles Azure lors de la création de projets web](../virtual-machines-dotnet-create-visual-studio-powershell/)
-   [Création de scripts PowerShell lors de la création de machines virtuelles](http://msdn.microsoft.com/en-us/library/dn642480.aspx)
-   [Affichage et gestion des paramètres de projet de service cloud dans les fenêtres Propriétés du projet Visual Studio](http://msdn.microsoft.com/en-us/library/ee405486.aspx)
-   Affichage et gestion des [services cloud](http://msdn.microsoft.com/en-us/library/ff683675.aspx), des [machines virtuelles](http://msdn.microsoft.com/en-us/library/jj131259.aspx) et de [Service Bus](http://msdn.microsoft.com/en-us/library/jj149828.aspx) dans l'Explorateur de serveurs
-   [Exécution en mode débogage à distance pour les services cloud et les machines virtuelles](http://msdn.microsoft.com/en-us/library/ff683670.aspx)

### <span id="auth"></span></a>Outils de création Microsoft Azure

Les éléments suivants sont inclus :

-   L'[outil en ligne de commande CSPack](http://msdn.microsoft.com/en-us/library/gg432988.aspx) permettant de créer des packages de déploiement.
-   L'[outil en ligne de commande CSEncrypt](http://msdn.microsoft.com/en-us/library/hh404001.aspx) permettant de chiffrer des mots de passe utilisés pour accéder aux instances de rôle de service cloud par le biais d'une connexion Bureau à distance.
-   Les fichiers binaires runtime dont les projets de service cloud ont besoin pour communiquer avec leur environnement d'exécution et à des fins de diagnostic. Ces fichiers binaires ne sont pas disponibles dans les packages NuGet.

### <span id="emulator"></span></a>Émulateur Microsoft Azure

L'[Émulateur Azure](http://msdn.microsoft.com/en-us/library/dn339018.aspx) simule l'environnement de service cloud pour vous permettre de tester les projets de service cloud en local sur votre ordinateur avant de les déployer vers Azure.

### <span id="stgemulator"></span></a>Émulateur de stockage Microsoft Azure

L'[Émulateur de stockage Azure](http://msdn.microsoft.com/en-us/library/hh403989.aspx) utilise une instance SQL Server et le système de fichiers local pour simuler Azure Storage (files d'attente, tables, objets blob), de façon à vous permettre de les tester en local.

### <span id="stgtools"></span></a>Outils de stockage Microsoft Azure

Ils permettent d'installer [AzCopy](http://aka.ms/AzCopy), un outil en ligne de commande que vous pouvez utiliser pour transférer des données vers et depuis un compte de stockage Azure.

### <span id="libraries"></span></a>Bibliothèques Microsoft Azure pour .NET

Les éléments suivants sont inclus :

-   Les packages NuGet pour Azure Storage, Service Bus et le Service de mise en cache qui sont stockés sur votre ordinateur de façon à ce que Visual Studio puisse créer des projets de service cloud en mode hors connexion.
-   Un plug-in Visual Studio permettant aux projets [In-Role Cache](http://msdn.microsoft.com/en-us/library/dn386103.aspx) de s'exécuter en local dans Visual Studio.

### <span id="ls"></span></a>Complément de publication LightSwitch Azure pour Visual Studio

Il vous permet de [publier des projets LightSwitch sur Sites Web Azure](http://msdn.microsoft.com/en-us/library/jj131261.aspx). Le complément LightSwitch est inclus dans les mises à jour de Visual Studio ainsi que le Kit de développement logiciel (SDK) Azure pour .NET. L'installation de ce Kit permet de s'assurer que vous disposez de la version la plus récente du complément.

## <span id="notincluded"></span></a>Éléments non inclus lorsque vous installez le Kit de développement logiciel (SDK) Azure pour .NET

Voici quelques éléments qui vous intéresseront peut-être pour le développement d'Azure qui ne sont pas inclus lorsque vous installez le Kit de développement logiciel (SDK). Les plus importants sont les suivants :

-   [Bibliothèques clientes](http://go.microsoft.com/fwlink/?LinkId=510472)

    Le Kit de développement logiciel (SDK) Azure inclut des bibliothèques clientes permettant d'utiliser les services Azure, mais toutes ne sont pas installées avec le Kit de développement logiciel (SDK). Si votre application requiert une bibliothèque cliente qui n'est pas installée par le Kit de développement logiciel (SDK), vous pouvez l'obtenir sur [NuGet.org][NuGet]. Si votre application utilise une bibliothèque cliente qui n'est pas installée par le Kit de développement logiciel (SDK), il est conseillé de la mettre à jour avec la version actuelle disponible sur NuGet.org.

    **Copies locales de bibliothèques clientes.** Le Kit de développement logiciel (SDK) Azure pour .NET copie les packages NuGet sur votre ordinateur pour certaines bibliothèques clientes Azure, telles que Storage, Service Bus et Mise en cache. Ces dernières sont automatiquement incluses dans les nouveaux projets de service cloud, de sorte que les packages NuGet locaux permettent à Visual Studio de créer des projets même si vous n'êtes pas connecté à Internet. Les bibliothèques clientes sont généralement mises à jour à un rythme plus rapide que celui de la publication de nouvelles versions du Kit de développement logiciel (SDK), de sorte que les bibliothèques clientes sur NuGet.org sont souvent plus récentes que celles obtenues avec le Kit de développement logiciel (SDK).

    **Modèles de projet incluant des bibliothèques clientes** Seuls les modèles de projet [Azure Cloud Services](../cloud-services-dotnet-get-started/) et[Azure Mobile Services](../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/) incluent automatiquement certaines bibliothèques clientes. Pour accéder à d'autres bibliothèques ou modèles, installez les [packages NuGet de bibliothèques clientes](http://go.microsoft.com/fwlink/?LinkId=510472) dont vous avez besoin.

-   [Azure PowerShell](../install-configure-powershell/)

    Azure PowerShell vous permet d'[automatiser la création et le déploiement de l'environnement Azure][].

-   [Modèles de projet Azure Mobile Services](../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/).

    Les modèles Mobile Services sont uniquement disponibles dans Visual Studio 2013 Update 2 et versions ultérieures. Ils ne sont pas disponibles dans Visual Studio 2012 ou versions antérieures, ni dans Visual Studio 2013 Update 1 ou versions antérieures, même si vous installez le Kit de développement logiciel (SDK) Azure pour .NET.

## <span id="faq"></span></a>Forum Aux Questions (FAQ)

-   [De nombreuses fonctionnalités Azure se trouvent déjà dans Visual Studio. Dois-je installer le Kit de développement logiciel (SDK) Azure pour .NET ?](#azinvs)
-   [Je souhaite une bibliothèque cliente. Dois-je installer le Kit de développement logiciel (SDK) Azure pour .NET pour l'obtenir ?](#clientlib)
-   [Où puis-je trouver des versions plus anciennes du Kit de développement logiciel (SDK) Azure pour .NET ?](#olderversions)
-   [Quelle est la stratégie en matière de cycle de vie du Kit de développement logiciel (SDK) Azure pour .NET ?](#lifecycle)
-   [Avec quelles versions de système d'exploitation invité le Kit de développement logiciel (SDK) pour .NET est-il compatible ?](#guestos)

### <span id="azinvs"></span></a>De nombreuses fonctionnalités Azure se trouvent déjà dans Visual Studio. Dois-je installer le Kit de développement logiciel (SDK) Azure pour .NET ?

Il est conseillé d'installer le Kit de développement logiciel (SDK) si vous voulez effectuer des tâches de développement pour Azure au moyen des outils les plus récents. Si vous préférez ne pas installer le Kit de développement logiciel (SDK), c'est possible si les conditions suivantes sont réunies :

-   Vous avez installé la dernière version de [Visual Studio Update](http://www.visualstudio.com/en-us/downloads/download-visual-studio-vs#DownloadFamilies_5).
-   Vous effectuez du développement uniquement pour Sites Web Azure ou Mobile Services, mais pas pour Cloud Services ou Virtual Machines.
-   Votre application n'utilise pas Storage, ou elle utilise Storage mais vous n'avez pas besoin de l'émulateur de stockage ou de l'outil AzCopy.

### <span id="clientlib"></span></a>Je souhaite une bibliothèque cliente. Dois-je installer le Kit de développement logiciel (SDK) Azure pour .NET pour l'obtenir ?

Le Kit de développement logiciel (SDK) installe les bibliothèques clientes uniquement pour vous permettre de créer des projets de service cloud même si vous n'êtes pas connecté à Internet. Des bibliothèques clientes récentes sont disponibles dans les packages NuGet sur [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Pour plus d'informations, consultez la rubrique [Éléments non inclus lorsque vous installez le Kit de développement logiciel (SDK) Azure pour .NET](#notincluded) plus haut dans ce document.

### <span id="olderversions"></span></a>Où puis-je trouver des versions plus anciennes du Kit de développement logiciel (SDK) Azure pour .NET ?

Pour des versions plus anciennes, consultez la page de téléchargements [Kit de développement logiciel (SDK) Azure pour .NET](/en-us/downloads/archive-net-downloads/).

### <span id="lifecycle"></span></a>Quelle est la stratégie en matière de cycle de vie du Kit de développement logiciel (SDK) Azure pour .NET ?

Consultez [Politique de support applicable aux services de cloud computing Microsoft Azure](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

### <span id="guestos"></span></a>Avec quelles versions de système d'exploitation invité le Kit de développement logiciel (SDK) pour .NET est-il compatible ?

Consultez [Versions du système d'exploitation invité d'Azure et matrice de compatibilité du Kit de développement logiciel (SDK)](http://msdn.microsoft.com/en-us/library/ee924680.aspx).

## <span id="resources"></span></a>Ressources

Pour télécharger le Kit de développement logiciel (SDK) Azure pour .NET ou une bibliothèque cliente, consultez la [page de téléchargements Azure](/en-us/downloads/).

Pour le code source du Kit de développement logiciel (SDK) Azure pour .NET, y compris les bibliothèques clientes, consultez [GitHub.com/Azure](https://github.com/azure/).

Pour la documentation de référence de la bibliothèque cliente Azure, consultez [Référence Azure .NET](/en-us/develop/net/reference/).
