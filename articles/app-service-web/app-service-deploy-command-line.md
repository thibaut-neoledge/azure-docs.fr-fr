---
title: "Automatiser le déploiement de votre application Azure avec les outils en ligne de commande | Microsoft Docs"
description: "Découvrir des informations sur la façon dont vous pouvez déployer votre application Azure à partir de la ligne de commande"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 8b65980c-eb75-44a2-8e0f-f9eb9e617d16
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 283b1cfda82b4f96ad5148c522a4c9833cb4c381
ms.openlocfilehash: e0e2e65557911bcac06d4dc355f47e9331934f8a


---
# <a name="automate-deployment-of-your-azure-app-with-command-line-tools"></a>Automatiser le déploiement de votre application Azure avec les outils en ligne de commande
Vous pouvez automatiser le déploiement de votre application Azure avec les outils en ligne de commande. Cet article répertorie les outils disponibles et les liens utiles qui vous montrent comment les utiliser dans votre flux de travail de déploiement. 

## <a name="a-namemsbuildaautomate-deployment-with-msbuild"></a><a name="msbuild"></a>Automatiser le déploiement avec MSBuild
Si vous utilisez l’[IDE Visual Studio](#vs) pour le développement, vous pouvez utiliser [MSBuild](http://msbuildbook.com/) pour automatiser tout ce que vous pouvez faire dans votre IDE. Vous pouvez configurer MSBuild pour utiliser [Web Deploy](#webdeploy) ou [FTP/FTPS](#ftp) pour copier vos fichiers. Web Deploy peut également automatiser de nombreuses autres tâches de déploiement, comme le déploiement des bases de données.

Pour plus d'informations sur le déploiement en ligne de commande avec MSBuild, consultez les ressources suivantes :

* [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)(en anglais). Une série d'une dizaine de didacticiels sur le déploiement dans Azure avec Visual Studio. Montre comment utiliser la ligne de commande pour le déploiement après la configuration de profils de publication dans Visual Studio.
* [Présentation de Microsoft Build Engine : utilisation de MSBuild et Team Foundation Build](http://msbuildbook.com/). Manuel comportant des chapitres sur l'utilisation de MSBuild pour le déploiement.

## <a name="a-namepowershellaautomate-deployment-with-windows-powershell"></a><a name="powershell"></a>Automatiser le déploiement avec Windows PowerShell
Vous pouvez utiliser des fonctions de déploiement MSBuild ou FTP à partir de [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Ce faisant, vous pouvez également utiliser une collection de cmdlets Windows PowerShell facilitant l'appel de l'API de gestion REST Azure.

Pour plus d’informations, consultez les ressources suivantes :

* [Déployer une application Web liée à un référentiel GitHub](app-service-web-arm-from-github-provision.md)
* [Mettre en service une application Web avec une base de données SQL](app-service-web-arm-with-sql-database-provision.md)
* [Mise en service et déploiement de microservices de manière prévisible dans Azure](app-service-deploy-complex-application-predictably.md)
* [Tout automatiser (développement d'applications de cloud concrètes avec Azure)](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Chapitre d'un livre électronique expliquant comment l'exemple d'application contenu dans le livre utilise des scripts Windows PowerShell pour créer un environnement de test Azure et y procéder à un déploiement. Consultez la section [Ressources](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) pour obtenir des liens vers une documentation Azure PowerShell supplémentaire.
* [Utilisation des scripts Windows PowerShell pour la publication dans des environnements de développement et de test](../vs-azure-tools-publishing-using-powershell-scripts.md). Explique comment utiliser les scripts Windows PowerShell générés par Visual Studio.

## <a name="a-nameapiaautomate-deployment-with-net-management-api"></a><a name="api"></a>Automatiser le déploiement avec l’API de gestion .NET
Vous pouvez écrire un code C# pour utiliser des fonctions MSBuild ou FTP pour vos déploiements. Ce faisant, vous pouvez accéder à l'API REST de gestion Azure pour utiliser des fonctions de gestion de site.

Pour plus d'informations, consultez les ressources suivantes :

* [Tout automatiser avec les bibliothèques de gestion Azure et .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Présente l’API de gestion .NET et fournit des liens vers d’autres documentations.

## <a name="a-namecliadeploy-from-azure-command-line-interface-azure-cli"></a><a name="cli"></a>Déploiement à partir de l’interface de ligne de commande Azure
Vous pouvez utiliser la ligne de commande avec des ordinateurs Windows, Mac ou Linux pour vos déploiements en utilisant le FTP. Ce faisant, vous pouvez également accéder à l'API de gestion REST Azure en utilisant l'interface en ligne de commande interplateforme.

Pour plus d'informations, consultez les ressources suivantes :

* [Outils en ligne de commande Azure](https://azure.microsoft.com/downloads/). Page du portail Azure.com qui fournit des informations sur les outils en ligne de commande.

## <a name="a-namewebdeployadeploy-from-web-deploy-command-line"></a><a name="webdeploy"></a>Déploiement à partir de la ligne de commande Web Deploy
[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) est un logiciel Microsoft pour le déploiement dans IIS qui fournit des fonctionnalités de synchronisation des fichiers intelligentes et effectue ou coordonne également de nombreuses autres tâches liées au déploiement qui ne peuvent pas être automatisées lorsque vous utilisez le FTP. Par exemple, Web Deploy peut déployer une nouvelle base de données ou mettre à jour une base de données existante avec votre application web. Web Deploy peut également réduire le délai de mise à jour d'un site existant, en copiant uniquement les fichiers modifiés. Microsoft Visual Studio et Team Foundation Server prennent en charge le logiciel Web Deploy intégré, mais vous pouvez également utiliser Web Deploy directement depuis la ligne de commande pour automatiser le déploiement. Les commandes Web Deploy sont très efficaces, mais leur apprentissage peut être long.

## <a name="more-resources"></a>Autres ressources
Outre l’automatisation via la ligne de commande, une autre option de déploiement consiste à utiliser un service basé sur le cloud comme [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Pour plus d'informations, consultez la page [Déploiement d’applications ASP.NET sur des sites web Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

Pour plus d’informations sur les outils en ligne de commande, consultez les ressources suivantes :

* [Web Apps en toute simplicité : déploiement](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/)(en anglais). Blog de David Ebbo sur un outil, qu’il a conçu pour faciliter l’utilisation de Web Deploy.
* [Outils de déploiement Web](http://technet.microsoft.com/library/dd568996). Documentation officielle sur le site Microsoft TechNet. Ancien, mais constitue un bon point de départ.
* [Utilisation de Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Documentation officielle sur le site Microsoft IIS.NET. Également ancien, mais constitue un bon point de départ.
* [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)(en anglais). MSBuild est le moteur de génération utilisé par Visual Studio, mais il peut aussi être utilisé à partir de la ligne de commande pour déployer des applications web dans Web Apps. Ce didacticiel fait partie d'une série traitant principalement des déploiements Visual Studio.




<!--HONumber=Jan17_HO1-->


