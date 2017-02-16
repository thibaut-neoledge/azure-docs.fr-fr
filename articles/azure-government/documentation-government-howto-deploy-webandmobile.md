---
title: "Déployer vers Azure App Services avec Visual Studio 2015 | Microsoft Docs"
description: "Cet article décrit comment déployer une application web, une application API ou une application mobile sur Azure Government à l’aide de Visual Studio 2015 et du Kit de développement logiciel (SDK) Azure."
services: azure-government
cloud: gov
documentationcenter: 
author: sdubeymsft
manager: zakramer
ms.assetid: 8f9a3700-b9ee-43b7-b64d-2e6c3b57d4c0
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/03/2016
ms.author: sdubeymsft
translationtype: Human Translation
ms.sourcegitcommit: 40e2eea5c1a11dadb232c6ac7c224ca5fc847c02
ms.openlocfilehash: 9a5efdd4c94c7cfc7a63c478f1f5b8124a8cb5c6


---
# <a name="deploy-to-azure-app-services-using-visual-studio-2015"></a>Déployer vers Azure App Services avec Visual Studio 2015
Cet article décrit comment déployer une application Azure App Service (application API, application web, application mobile) sur Azure Government à l’aide de Visual Studio 2015.

## <a name="prerequisites"></a>Composants requis
* Consultez la rubrique [Configuration requise de Visual Studio] (../app-service-api/app-service-api-dotnet-get-started.md#prerequisites) pour installer et configurer Visual Studio 2015 et le Kit de développement logiciel (SDK) Azure.
* Suivez [ces instructions] (documentation-government-manage-subscriptions.md#connecting-via-visual-studio) pour configurer Visual Studio pour se connecter au compte Azure Government.

## <a name="open-app-project-in-visual-studio"></a>Ouvrez le projet d’application dans Visual Studio
* Ouvrez une solution ou un projet d’application dans Visual Studio, créez un projet en appliquant [ces instructions] (../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application) ou téléchargez un exemple d’application en appliquant [cette procédure] (../app-service-api/app-service-api-dotnet-get-started.md#download-the-sample-application).
* Exécutez l’application dans Visual Studio pour vérifier qu’elle fonctionne.

## <a name="deploy-to-azure-government"></a>Déploiement sur Azure Government
* Une fois que **Visual Studio est configuré de manière à se connecter au compte Azure Government** (déjà fait dans la section des conditions préalables), les instructions pour le déploiement vers les services d’application sont exactement les mêmes que pour Azure Public.
* Pour déployer l’application, procédez [comme suit] (../app-service-api/app-service-api-dotnet-get-started.md#a-idcreateapiappa-create-an-api-app-in-azure-and-deploy-code-to-it).

### <a name="references"></a>Références
* [Déployer une application web ASP.NET dans Azure App Service à l’aide de Visual Studio] (../app-service-web/web-sites-dotnet-get-started.md)
* Pour les autres méthodes de déploiement, consultez [Déployer votre application dans Azure App Service] (../app-service-web/web-sites-deploy.md)
* Pour la documentation générale d’App Service, consultez [App Service - Documentation d’API Apps] (../app-service-api/index.md)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au [blog Microsoft Azure Government](https://blogs.msdn.microsoft.com/azuregov/).



<!--HONumber=Jan17_HO1-->


