---
title: "Présentation du Kit de développement logiciel (SDK) Azure pour .NET"
description: "Découvrez ce qui est inclus dans le Kit de développement logiciel (SDK) .NET Azure."
documentationcenter: .net
author: chrissfanos
editor: mollybos
services: 
ms.assetid: f70279ac-7c56-4d6a-9882-352a8b644a4b
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/30/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 94c4929c84a671eca210a0e6b53eda5019cc0951
ms.lasthandoff: 03/25/2017


---
# <a name="what-is-the-azure-sdk-for-net"></a>Présentation du Kit de développement logiciel (SDK) Azure pour .NET
## <a name="overview"></a>Vue d'ensemble
Le Kit de développement logiciel (SDK) Azure pour .NET est un ensemble d'outils Visual Studio, d'outils en ligne de commande, de fichiers binaires runtime et de bibliothèques clientes qui vous aident à développer, à tester et à déployer des applications s'exécutant dans Azure. Cet article présente en détail ce que vous obtenez lorsque vous installez le Kit de développement logiciel (SDK). Vous pouvez le télécharger à partir de la [page de téléchargements Azure](https://azure.microsoft.com/downloads/).

Le Kit de développement logiciel (SDK) Azure pour .NET comprend également des [bibliothèques clientes permettant d'utiliser les services Azure](http://go.microsoft.com/fwlink/?LinkId=510472). Celles-ci sont installées séparément au moyen de NuGet.

## <a id="included"></a>Éléments inclus dans le Kit de développement logiciel (SDK) Azure pour .NET
Le Kit de développement logiciel (SDK) Azure pour .NET installe les produits suivants :

* [Visual Studio Community Edition 2015](#vwd)
* [Émulateur de stockage Microsoft Azure](#stgemulator)
* [Outils de stockage Microsoft Azure](#stgtools)
* [Outils de création Microsoft Azure](#auth)
* [Émulateur Microsoft Azure](#emulator)
* [Outils HDInsight pour Visual Studio et pilote ODBC Hive de Microsoft](#hdinsight)
* [Bibliothèques Microsoft Azure pour .NET](#libraries)
* [Kit de développement logiciel (SDK) de Microsoft Azure Mobile App](#mobile)
* [Microsoft Azure PowerShell](#ps)
* [Outils Microsoft Azure pour Microsoft Visual Studio](#tools)
* [Microsoft ASP.NET et outils web pour Visual Studio](#wte)
* [Outils Microsoft Azure Data Lake pour Visual Studio](#datalake)

### <a id="vwd"></a>Visual Studio Community Edition 2015
Si vous ne disposez pas de Visual Studio sur votre ordinateur, le Kit de développement logiciel (SDK) installe [Visual Studio Community Edition 2015](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a id="stgemulator"></a>Émulateur de stockage Microsoft Azure
L' [Émulateur de stockage Azure](http://msdn.microsoft.com/library/hh403989.aspx) utilise une instance SQL Server et le système de fichiers local pour simuler Azure Storage (files d'attente, tables, objets blob), de façon à vous permettre de les tester en local.

### <a id="stgtools"></a>Outils de stockage Microsoft Azure
Ils permettent d'installer [AzCopy](http://aka.ms/AzCopy), un outil de ligne de commande que vous pouvez utiliser pour transférer des données vers et depuis un compte de stockage Azure.

### <a id="auth"></a>Outils de création Microsoft Azure
Les éléments suivants sont inclus :

* L' [outil en ligne de commande CSPack](http://msdn.microsoft.com/library/gg432988.aspx) permettant de créer des packages de déploiement.
* L' [outil en ligne de commande CSEncrypt](http://msdn.microsoft.com/library/hh404001.aspx) permettant de chiffrer des mots de passe utilisés pour accéder aux instances de rôle de service cloud par le biais d'une connexion Bureau à distance.
* Les fichiers binaires runtime dont les projets de service cloud ont besoin pour communiquer avec leur environnement d'exécution et à des fins de diagnostic. Ces fichiers binaires ne sont pas disponibles dans les packages NuGet.

### <a id="emulator"></a>Émulateur Microsoft Azure
L' [Émulateur Azure](http://msdn.microsoft.com/library/dn339018.aspx) simule l'environnement de service cloud pour vous permettre de tester les projets de service cloud en local sur votre ordinateur avant de les déployer vers Azure.

### <a id="hdinsight"></a>Outils HDInsight pour Visual Studio et pilote ODBC Hive de Microsoft
Les outils HDInsight dans l'Explorateur de serveurs vous permettent de naviguer dans les bases de données Hive et de lier des comptes de stockage pour les clusters HDInsight, de créer des tables ainsi que de créer et d'envoyer des requêtes Hive. Pour plus d'informations, consultez la page [Prise en main de HDInsight Hadoop Tools pour Visual Studio](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

### <a id="libraries"></a>Bibliothèques Microsoft Azure pour .NET
Les éléments suivants sont inclus :

* Les packages NuGet pour Azure Storage, Service Bus et le Service de mise en cache qui sont stockés sur votre ordinateur de façon à ce que Visual Studio puisse créer des projets de service cloud en mode hors connexion.
* Un plug-in Visual Studio permettant aux projets [In-Role Cache](http://msdn.microsoft.com/library/dn386103.aspx) de s'exécuter en local dans Visual Studio.

### <a id="mobile"></a>Kit de développement logiciel (SDK) de Microsoft Azure Mobile App
Outils permettant de travailler avec [Azure App Service Mobile Apps](app-service-mobile/app-service-mobile-value-prop.md).

### <a id="ps"></a>Microsoft Azure PowerShell
Azure PowerShell vous permet d' [automatiser la création et le déploiement de l'environnement Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

### <a id="tools"></a>Outils Microsoft Azure pour Microsoft Visual Studio
Ces rubriques vous permettent d'utiliser les ressources Azure, principalement Cloud Services et Virtual Machines :

* [Création, ouverture et publication de projets de service cloud](cloud-services/cloud-services-dotnet-get-started.md)
* [Création de packages de déploiement pour les projets de service cloud](http://msdn.microsoft.com/library/ff683672.aspx)
* [Création de machines virtuelles Azure lors de la création de projets web](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Création de scripts PowerShell lors de la création de machines virtuelles](http://msdn.microsoft.com/library/dn642480.aspx)
* [Affichage et gestion des paramètres de projet de service cloud dans les fenêtres Propriétés du projet Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx)
* Affichage et gestion des [services cloud](http://msdn.microsoft.com/library/ff683675.aspx), des [machines virtuelles](http://msdn.microsoft.com/library/jj131259.aspx) et de [Service Bus](http://msdn.microsoft.com/library/jj149828.aspx) dans l'Explorateur de serveurs
* [Exécution en mode débogage à distance pour les services cloud et les machines virtuelles](http://msdn.microsoft.com/library/ff683670.aspx)
* [Automatisation de l’approvisionnement des ressources à l'aide de projets de déploiement de groupe de ressources Azure](https://msdn.microsoft.com/library/dn872471.aspx)

### <a id="wte"></a>Outils Microsoft App Service pour Visual Studio
Les rubriques suivantes vous permettent d'utiliser Sites Web Azure :

* [Publication de projets web sur Sites Web Azure](app-service-web/web-sites-dotnet-get-started.md)
* [Publication de projets d'application console sur Tâches web Azure](app-service-web/websites-dotnet-deploy-webjobs.md)
* [Création de ressources Sites Web Azure et Base de données SQL lors de la création ou de la publication d'un projet web](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* [Création de scripts de déploiement PowerShell lors de la création de nouveaux sites web](http://msdn.microsoft.com/library/dn642480.aspx)
* [Gestion et dépannage de Sites Web Azure dans l'Explorateur de serveurs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement)
* [Exécution en mode débogage à distance pour les sites et les tâches web](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)

> [!NOTE]
> Il n'est pas nécessaire d'installer le Kit de développement logiciel (SDK) Azure pour .NET pour pouvoir utiliser ces fonctionnalités ; celles-ci sont également incluses dans les mises à jour Visual Studio.
> 
> 

## <a id="datalake"></a>Outils Microsoft Azure Data Lake pour Visual Studio
Pour plus d’informations, consultez le [didacticiel : Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a id="notincluded"></a>Éléments non inclus lorsque vous installez le Kit de développement logiciel (SDK) Azure pour .NET
Voici quelques éléments qui peuvent vous intéresser pour le développement d'Azure et qui ne sont pas inclus lorsque vous installez le Kit de développement logiciel (SDK). Les plus importants sont les suivants :

* [Bibliothèques clientes](http://go.microsoft.com/fwlink/?LinkId=510472)
  
    Le Kit de développement logiciel (SDK) Azure inclut des bibliothèques clientes permettant d'utiliser les services Azure, mais toutes ne sont pas installées avec le Kit de développement logiciel (SDK). Si votre application requiert une bibliothèque cliente qui n'est pas installée par le Kit de développement logiciel (SDK), vous pouvez l'obtenir sur [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Si votre application utilise une bibliothèque cliente qui n'est pas installée par le Kit de développement logiciel (SDK), il est conseillé de la mettre à jour avec la version actuelle disponible sur NuGet.org.
  
      **Local copies of client libraries.** The Azure SDK for .NET copies to your computer the NuGet packages for some Azure client libraries, such as Storage, Service Bus, and Caching. These client libraries are automatically included in new cloud service projects, so the local NuGet packages enable Visual Studio to create projects even if you're not connected to the Internet. Client libraries are generally updated more frequently than new SDK versions are released, so the client libraries at NuGet.org are often more current than what you get with the SDK.
  
    **Modèles de projet incluant des bibliothèques clientes** Seuls les modèles de projet [Azure Cloud Service](cloud-services/cloud-services-dotnet-get-started.md) et Azure App Service [Mobile Apps](app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) incluent automatiquement certaines bibliothèques clientes. Pour accéder à d'autres bibliothèques ou modèles, installez les [packages NuGet de bibliothèques clientes](http://go.microsoft.com/fwlink/?LinkId=510472) dont vous avez besoin.
* [Modèles de projet Mobile Apps](app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
  
    Les modèles Mobile Apps sont uniquement disponibles dans Visual Studio 2013 Update 2 et versions ultérieures. Ils ne sont pas disponibles dans Visual Studio 2012 ou versions antérieures, ni dans Visual Studio 2013 Update 1 ou versions antérieures, même si vous installez le Kit de développement logiciel (SDK) Azure pour .NET.

## <a id="faq"></a>Forum Aux Questions
* [De nombreuses fonctionnalités Azure se trouvent déjà dans Visual Studio. Dois-je installer le Kit de développement logiciel (SDK) Azure pour .NET ?](#azinvs)
* [Je souhaite une bibliothèque cliente. Dois-je installer le Kit de développement logiciel (SDK) Azure pour .NET pour l'obtenir ?](#clientlib)
* [Où puis-je trouver des versions plus anciennes du Kit de développement logiciel (SDK) Azure pour .NET ?](#olderversions)
* [Quelle est la stratégie en matière de cycle de vie du Kit de développement logiciel (SDK) Azure pour .NET ?](#lifecycle)
* [Avec quelles versions de système d'exploitation invité le Kit de développement logiciel (SDK) pour .NET est-il compatible ?](#guestos)
* [Comment puis-je désinstaller le Kit de développement logiciel (SDK) Azure pour .NET ?](#uninstall)

### <a id="azinvs"></a>De nombreuses fonctionnalités Azure se trouvent déjà dans Visual Studio. Dois-je installer le Kit de développement logiciel (SDK) Azure pour .NET ?
Il est conseillé d'installer le Kit de développement logiciel (SDK) si vous voulez effectuer des tâches de développement pour Azure au moyen des outils les plus récents. Si vous préférez ne pas installer le Kit de développement logiciel (SDK), c'est possible si les conditions suivantes sont réunies :

* Vous avez installé la dernière version de [Visual Studio Update](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5).
* Vous effectuez du développement uniquement pour Sites Web Azure ou Mobile Services, mais pas pour Cloud Services ou Virtual Machines.
* Votre application n'utilise pas Storage, ou elle utilise Storage mais vous n'avez pas besoin de l'émulateur de stockage ou de l'outil AzCopy.

### <a id="clientlib"></a>Je souhaite une bibliothèque cliente. Dois-je installer le Kit de développement logiciel (SDK) Azure pour .NET pour l'obtenir ?
Le Kit de développement logiciel (SDK) installe les bibliothèques clientes uniquement pour vous permettre de créer des projets de service cloud même si vous n'êtes pas connecté à Internet. Des bibliothèques clientes récentes sont disponibles dans les packages NuGet sur [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Pour plus d'informations, consultez la rubrique [Éléments non inclus lorsque vous installez le Kit de développement logiciel (SDK) Azure pour .NET](#notincluded) plus haut dans ce document.

### <a id="olderversions"></a>Où puis-je trouver des versions plus anciennes du Kit de développement logiciel (SDK) Azure pour .NET ?
Pour des versions plus anciennes, consultez la page de téléchargements [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) .

### <a id="lifecycle"></a>Quelle est la stratégie en matière de cycle de vie du Kit de développement logiciel (SDK) Azure pour .NET ?
Consultez [Politique de support applicable aux services de cloud computing Microsoft Azure](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

### <a id="guestos"></a>Avec quelles versions de système d'exploitation invité le Kit de développement logiciel (SDK) pour .NET est-il compatible ?
Consultez [Versions du système d'exploitation invité d'Azure et matrice de compatibilité du Kit de développement logiciel (SDK)](http://msdn.microsoft.com/library/ee924680.aspx).

### <a id="uninstall"></a>Comment puis-je désinstaller le Kit de développement logiciel (SDK) Azure pour .NET ?
Chaque élément affiché dans cet article sous [Éléments inclus dans le Kit de développement logiciel (SDK) pour .NET](#included) est un programme distinct de la liste des programmes installés dans le panneau de configuration Windows **Programmes et fonctionnalités**.  Il n'existe aucun moyen de les désinstaller en tant que groupe. Vous devez désinstaller individuellement de chaque programme.

Lorsque vous avez le Kit de développement logiciel (SDK) Azure pour .NET déjà installé et que vous installez une nouvelle version, il n'est généralement pas nécessaire de désinstaller l'ancienne version. Dans la plupart des cas, l'installation du Kit de développement logiciel (SDK) met à jour un programme existant, mais il n’ajoute pas un nouveau programme en laissant l’ancien.

Toutefois, si vous souhaitez supprimer les programmes inutiles d'une version antérieure, désinstallez uniquement les programmes qui indiquent le numéro de l’ancienne version et désinstallez-les seulement si ce même programme avec une version plus récente est présent. Par exemple, après la mise à jour de la version 2.5 à 2.6, vous pouvez voir les versions 2.5 et 2.6 des « outils Microsoft Azure pour Microsoft Visual Studio 2013 » et vous pouvez désinstaller la version 2.5. Mais vous ne verrez peut-être que la version 2.5 des « outils de création Microsoft Azure », et il n’est pas recommandé de la désinstaller.

Notez que les numéros de version dans les titres de programme affichés dans **Programmes et fonctionnalités** peuvent prêter à confusion.  Par exemple, la version 2.6 du Kit de développement logiciel (SDK) contient la « version V1.0 du Kit de développement logiciel (SDK) de Microsoft Azure Mobile App » si vous installez le Kit de développement logiciel (SDK) pour Visual Studio 2013 et la « version V2.0 du Kit de développement logiciel (SDK) de Microsoft Azure Mobile App » pour Visual Studio 2015. Dans ce cas, la version n’est pas celle du Kit de développement logiciel (SDK) mais un indicateur de la version Visual Studio pour laquelle le programme s’applique.

Cet article ne répertorie pas tous les programmes compris dans toutes les versions antérieures du Kit de développement logiciel (SDK) Azure. Il existe d’autres programmes que vous pouvez désinstaller depuis les versions antérieures du Kit de développement logiciel (SDK). En effet, ces versions antérieures incluent parfois des programmes omis dans les versions ultérieures. Par exemple, la version 2.5 installe les « outils Azure Resource Manager pour Visual Studio » mais elle n’est pas répertoriée dans la liste de cet article car elle n’est plus affichée comme un programme distinct dans **Programmes et fonctionnalités**.  Cet article répertorie uniquement les programmes qui sont inclus dans la version 2.6 du Kit de développement logiciel (SDK) pour .NET.

> **Remarque :** certains programmes compris dans le Kit de développement logiciel (SDK) peuvent également être installés séparément dans d'autres contextes et peuvent être nécessaires même si vous n'avez pas besoin du Kit de développement logiciel (SDK) complet. Cela peut également être le cas pour des programmes qui ont été installés par des versions antérieures du Kit de développement logiciel (SDK) mais qui ont été omis dans les versions ultérieures du Kit de développement logiciel (SDK). Lorsque vous désinstallez des programmes, veillez à éviter la suppression d’éléments toujours essentiels sur votre ordinateur.
> 
> 

## <a id="versions"></a>Versions
Pour voir quelle est la version actuelle ou pour télécharger des versions antérieures, consultez la page [Historique des version du Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) .

## <a id="resources"></a>Ressources
Pour télécharger l’actuel Kit de développement logiciel (SDK) Azure pour .NET ou une bibliothèque cliente, consultez la [page de téléchargements Azure](https://azure.microsoft.com/downloads/).

Pour le code source du Kit de développement logiciel (SDK) Azure pour .NET, y compris les bibliothèques clientes, consultez [GitHub.com/Azure](https://github.com/azure/).

Pour la documentation de référence de la bibliothèque cliente Azure, consultez [Référence Azure .NET](https://azure.microsoft.com/documentation/api/).


