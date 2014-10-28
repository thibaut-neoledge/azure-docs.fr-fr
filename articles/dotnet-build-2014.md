<properties pageTitle="Azure Spring 2014 release highlights - .NET Dev Center" metaKeywords="azure .net sdk 2.3" description="Learn about the new tools and features available for Azure .NET developers." documentationCenter=".NET" title="Azure Spring 2014 release highlights" authors="mollybos" solutions="" manager="carolz" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="mollybos"></tags>

# Présentation de la version Azure Spring 2014

Cet article présente les nouveautés – outils, fonctionnalités et thèmes – présentées aux développeurs Azure .NET à la conférence Build 2014 et disponibles dans le Kit de développement logiciel (SDK) Azure pour .NET 2.3, Visual Studio 2013 avec Update 2 et dans d'autres versions sorties au printemps.

**Obtenir les outils :**

-   [Visual Studio 2013 Update 2 RC][Visual Studio 2013 Update 2 RC]
-   [Kit de développement logiciel (SDK) Azure 2.3][Kit de développement logiciel (SDK) Azure 2.3]
-   [Azure PowerShell][Azure PowerShell]
-   [Interface de ligne de commande interplateforme Azure][Interface de ligne de commande interplateforme Azure]

Pour plus d'informations sur les nouveaux outils du printemps 2014, consultez les [notes de publication relatives au Kit de développement logiciel (SDK) Azure pour .NET 2.3][notes de publication relatives au Kit de développement logiciel (SDK) Azure pour .NET 2.3] et la [page des mises à jour produit Visual Studio 2013][page des mises à jour produit Visual Studio 2013].

[Regardez les vidéos de build][Regardez les vidéos de build] en diffusion à la demande.

## Sommaire

-   [Développement et publication Web][Développement et publication Web]
-   [Diagnostics et débogage][Diagnostics et débogage]
-   [Gestion des services Azure dans Visual Studio][Gestion des services Azure dans Visual Studio]
-   [Automatisation avec PowerShell][Automatisation avec PowerShell]
-   [Développement mobile avec .NET][Développement mobile avec .NET]
-   [SCL 3.0 et nouvel émulateur de stockage][SCL 3.0 et nouvel émulateur de stockage]
-   [Gestionnaire de ressources][Gestionnaire de ressources]

## <span id="webdeploy"></span></a>Développement et publication Web

Le Kit de développement logiciel (SDK) Azure 2.3 et Visual Studio 2013 Update 2 RC incluent plusieurs mises à jour qui facilitent le développement et la publication web avec Azure. Vous pouvez maintenant créer un site web ou une machine virtuelle Azure en créant une nouvelle application web. Lorsque vous êtes prêt à publier votre site web, vous pouvez utiliser la boîte de dialogue de publication web mise à jour ou les scripts PowerShell ajoutés à votre solution pour déployer directement votre site sur un site web ou une machine virtuelle Azure. Pour plus d'informations et pour accéder aux didacticiels qui expliquent comment utiliser les nouvelles fonctionnalités, consultez les ressources suivantes :

-   [Prise en main d'Azure et ASP.NET][Prise en main d'Azure et ASP.NET]
-   [Prise en main des outils Azure pour Visual Studio][Prise en main des outils Azure pour Visual Studio]
-   [Création de projets Web ASP.NET dans Visual Studio 2013][Création de projets Web ASP.NET dans Visual Studio 2013]
-   [Build 2014 : Nouveautés ASP.NET et web dans Visual Studio 2013 Update 2 et ultérieure (vidéo)][Build 2014 : Nouveautés ASP.NET et web dans Visual Studio 2013 Update 2 et ultérieure (vidéo)]

## <span id="diagnostics"></span></a>Diagnostics et débogage

Diagnostiquez à distance les problèmes liés aux applications en utilisant la nouvelle fonctionnalité de débogage à distance pour les machines virtuelles et le nouveau débogage de code natif :

-   [Débogage d'un service cloud ou d'une machine virtuelle dans Visual Studio][Débogage d'un service cloud ou d'une machine virtuelle dans Visual Studio]

Emulator Express est le nouvel émulateur local léger pour Cloud Services. Apprenez à l'utiliser pour tester Cloud Services sur votre machine locale :

-   [Utilisation d'Emulator Express pour exécuter et déboguer Cloud Services][Utilisation d'Emulator Express pour exécuter et déboguer Cloud Services]

Une fois le Kit de développement logiciel (SDK) 2.3 Azure installé, vous pouvez voir et modifier vos fichiers à distance directement à partir de l'Explorateur de serveurs, ainsi qu'afficher les fichiers journaux de vos sites. Lorsque vous enregistrez un fichier modifié, il est enregistré à nouveau sur votre site sans avoir besoin de le publier. Pour plus d'informations, consultez les rubriques :

-   [Résolution des problèmes liés à Sites Web Azure dans Visual Studio][Résolution des problèmes liés à Sites Web Azure dans Visual Studio]

## <span id="service-management"></span></a>Gestion des services Azure dans Visual Studio

Utilisez la gestion optimisée des machines virtuelles dans Visual Studio, et bénéficiez de la possibilité de créer des machines virtuelles dans l'IDE :

-   [Création d'une machine virtuelle à partir de l'Explorateur de serveurs][Création d'une machine virtuelle à partir de l'Explorateur de serveurs]
-   [Accès aux machines virtuelles Azure à partir de l'Explorateur de serveurs][Accès aux machines virtuelles Azure à partir de l'Explorateur de serveurs]

Plusieurs nouveautés vous permettent de gérer plus efficacement d'autres services Azure à partir de l'Explorateur de serveurs. Pour plus d'informations, consultez les rubriques :

-   [Consultation des ressources Service Bus avec l'Explorateur de serveurs Visual Studio][Consultation des ressources Service Bus avec l'Explorateur de serveurs Visual Studio]
-   [Consultation des ressources de stockage avec l'Explorateur de serveurs][Consultation des ressources de stockage avec l'Explorateur de serveurs]

## <span id="automation"></span></a>Automatisation à l'aide de PowerShell et des API

Installez Azure Powershell pour utiliser les nouvelles cmdlets pour Sites Web, WebJobs, etc. Utilisez davantage l'automatisation personnalisée à l'aide de l'API Gestion des services pour .NET. Pour plus d'informations, consultez les rubriques :

-   [Installation et configuration d’Azure PowerShell][Installation et configuration d’Azure PowerShell]
-   [Documentation Azure PowerShell][Documentation Azure PowerShell]
-   [Build 2014 : Automatisation en tout lieu avec de nouveaux Kits de développement logiciel (SDK), outils et services dans Azure (vidéo)][Build 2014 : Automatisation en tout lieu avec de nouveaux Kits de développement logiciel (SDK), outils et services dans Azure (vidéo)]

Créez des scripts PowerShell directement dans Visual Studio, et utilisez-les pour automatiser la création de votre environnement :

-   [Utilisation de scripts Windows PowerShell pour publier des environnements de développement et de test][Utilisation de scripts Windows PowerShell pour publier des environnements de développement et de test]

## <span id="mobile"></span></a>Développement mobile avec .NET

Azure Mobile Services propose désormais une option qui permet d'utiliser les serveurs principaux .NET pour vos applications mobiles ciblant les plateformes mobiles, y compris Windows Store, Windows Phone, iOS et Android. Pour plus d'informations, consultez :

-   [Couverture Cloud : les services mobiles Azure .NET principaux (vidéo)][Couverture Cloud : les services mobiles Azure .NET principaux (vidéo)]
-   [Centre de développement mobile Azure][Centre de développement mobile Azure]

Visual Studio 2013 Update 2 inclut également une nouvelle prise en charge du développement mobile, notamment la prise en charge du débogage distant et l'intégration de Notification Hubs à l'Explorateur de serveurs. Pour plus d'informations, consultez les pages suivantes :

-   [Démarrage rapide : ajouter un service mobile][Démarrage rapide : ajouter un service mobile]
-   [Comment envoyer des notifications Push à une application en cours d’exécution avec Visual Studio][Comment envoyer des notifications Push à une application en cours d’exécution avec Visual Studio]
-   [Comment créer des API personnalisées et des travaux planifiés dans un service mobile][Comment créer des API personnalisées et des travaux planifiés dans un service mobile]

## <span id="storage"></span></a>Bibliothèque cliente de stockage Azure 3.0

Le Kit de développement logiciel (SDK) Azure 2.3 inclut un émulateur de stockage mis à jour. De plus, la bibliothèque cliente de stockage 3.0 est intégrée aux modèles de projet compris dans le Kit de développement logiciel (SDK).

Pour plus d'informations, consultez les pages suivantes :

-   [Bibliothèque cliente de stockage Azure 3.0][Bibliothèque cliente de stockage Azure 3.0]
-   [Introduction à Azure Storage][Introduction à Azure Storage]
-   [Build 2014 : Microsoft Azure Storage – Nouveautés, meilleures pratiques et modèles (vidéo)][Build 2014 : Microsoft Azure Storage – Nouveautés, meilleures pratiques et modèles (vidéo)]
-   [Microsoft Azure Storage @ BUILD 2014][Microsoft Azure Storage @ BUILD 2014]

## <span id="arm"></span></a>Gestionnaire de ressources

Le Gestionnaire de ressources est une nouvelle infrastructure qui permet de déployer et gérer des applications sur différentes ressources. Faites un essai avec le Gestionnaire de ressources en utilisant le nouvel éditeur JSON, les nouveaux cmdlets PowerShell et le nouveau support CLI. Pour plus d'informations, consultez les ressources suivantes :

-   [Utilisation d'Azure PowerShell avec le Gestionnaire de ressources][Utilisation d'Azure PowerShell avec le Gestionnaire de ressources]
-   [Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources][Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources]
-   [Build 2014 : Modèle de groupe de ressources Azure : une gestion moderne du cloud moderne (vidéo)][Build 2014 : Modèle de groupe de ressources Azure : une gestion moderne du cloud moderne (vidéo)]

  [Visual Studio 2013 Update 2 RC]: http://aka.ms/vs2013update2rc
  [Kit de développement logiciel (SDK) Azure 2.3]: http://www.windowsazure.com/fr-fr/downloads/
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9811175
  [Interface de ligne de commande interplateforme Azure]: http://go.microsoft.com/?linkid=9828653
  [notes de publication relatives au Kit de développement logiciel (SDK) Azure pour .NET 2.3]: http://go.microsoft.com/fwlink/p/?LinkId=393548
  [page des mises à jour produit Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=272487
  [Regardez les vidéos de build]: http://go.microsoft.com/fwlink/?LinkId=394377&clcid=0x409
  [Développement et publication Web]: #webdeploy
  [Diagnostics et débogage]: #diagnostics
  [Gestion des services Azure dans Visual Studio]: #service-management
  [Automatisation avec PowerShell]: #automation
  [Développement mobile avec .NET]: #mobile
  [SCL 3.0 et nouvel émulateur de stockage]: #storage
  [Gestionnaire de ressources]: #arm
  [Prise en main d'Azure et ASP.NET]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-dotnet-get-started/
  [Prise en main des outils Azure pour Visual Studio]: http://msdn.microsoft.com/fr-fr/library/azure/ff687127.aspx
  [Création de projets Web ASP.NET dans Visual Studio 2013]: http://asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio
  [Build 2014 : Nouveautés ASP.NET et web dans Visual Studio 2013 Update 2 et ultérieure (vidéo)]: http://channel9.msdn.com/Events/Build/2014/3-602
  [Débogage d'un service cloud ou d'une machine virtuelle dans Visual Studio]: http://msdn.microsoft.com/fr-fr/library/azure/ff683670.aspx
  [Utilisation d'Emulator Express pour exécuter et déboguer Cloud Services]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn339018.aspx
  [Résolution des problèmes liés à Sites Web Azure dans Visual Studio]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio
  [Création d'une machine virtuelle à partir de l'Explorateur de serveurs]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn569263.aspx
  [Accès aux machines virtuelles Azure à partir de l'Explorateur de serveurs]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj131259.aspx
  [Consultation des ressources Service Bus avec l'Explorateur de serveurs Visual Studio]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj149828.aspx
  [Consultation des ressources de stockage avec l'Explorateur de serveurs]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff683677.aspx
  [Installation et configuration d’Azure PowerShell]: http://www.windowsazure.com/fr-fr/documentation/articles/install-configure-powershell/
  [Documentation Azure PowerShell]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156055.aspx
  [Build 2014 : Automatisation en tout lieu avec de nouveaux Kits de développement logiciel (SDK), outils et services dans Azure (vidéo)]: http://channel9.msdn.com/Events/Build/2014/3-621
  [Utilisation de scripts Windows PowerShell pour publier des environnements de développement et de test]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn642480.aspx
  [Couverture Cloud : les services mobiles Azure .NET principaux (vidéo)]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-137-The-Azure-Mobile-Services-NET-Backend-with-Yavor-Georgiev
  [Centre de développement mobile Azure]: /fr-fr/develop/mobile/
  [Démarrage rapide : ajouter un service mobile]: http://msdn.microsoft.com/fr-fr/library/windows/apps/xaml/dn629482.aspx
  [Comment envoyer des notifications Push à une application en cours d’exécution avec Visual Studio]: http://msdn.microsoft.com/fr-fr/library/windows/apps/xaml/dn614131.aspx
  [Comment créer des API personnalisées et des travaux planifiés dans un service mobile]: http://msdn.microsoft.com/fr-fr/library/windows/apps/xaml/dn614130.aspx
  [Bibliothèque cliente de stockage Azure 3.0]: http://go.microsoft.com/fwlink/?LinkId=394927
  [Introduction à Azure Storage]: /fr-fr/documentation/articles/storage-introduction/
  [Build 2014 : Microsoft Azure Storage – Nouveautés, meilleures pratiques et modèles (vidéo)]: http://channel9.msdn.com/Events/Build/2014/3-628
  [Microsoft Azure Storage @ BUILD 2014]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/08/microsoft-azure-storage-build-2014.aspx
  [Utilisation d'Azure PowerShell avec le Gestionnaire de ressources]: http://go.microsoft.com/fwlink/?LinkID=394767
  [Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources]: /fr-fr/documentation/articles/xplat-cli-azure-resource-manager/
  [Build 2014 : Modèle de groupe de ressources Azure : une gestion moderne du cloud moderne (vidéo)]: http://channel9.msdn.com/Events/Build/2014/2-607
