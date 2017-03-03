---
title: Utilisation de .NET Core dans Azure App Service Web Apps sur Linux | Microsoft Docs
description: Utilisation de .NET Core dans Azure App Service Web Apps sur Linux.
keywords: azure app service, application web, dotnet, core, linux, oss
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 769de52a85d1d5078b2ba583e94cabd22b0fde65
ms.lasthandoff: 02/17/2017


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Utilisation de .NET Core dans Web Apps sur Linux #

Lors de la dernière mise à jour de notre serveur principal, nous avons introduit la prise en charge de .NET Core v.1.0. En définissant la configuration de votre application web Linux, vous pouvez modifier la pile d’applications.


## <a name="using-xplat-cli"></a>Utilisation de l’interface de ligne de commande XPlat ##

À l’aide de l’interface de ligne de commande multiplateforme d’Azure la plus récente, vous pouvez utiliser la commande **azure webapp config set** pour modifier la pile d’applications. Voici un exemple :

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Le fichier **aspnetcore.dll** correspond au dll de votre application. Vous pouvez utiliser le nom de votre choix dans votre application.

Cette opération charge de l’image .Net Core et démarre votre application web. Vous pouvez vérifier que les paramètres ont été correctement définis à l’aide de la commande **azure webapp config show**. Voici un exemple :

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce qu’App Service sur Linux ?](app-service-linux-intro.md)
* [Création d’applications Web dans App Service sur Linux](./app-service-linux-how-to-create-a-web-app.md)
* [Interface de ligne de commande multiplateforme d’Azure Web Apps](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [FAQ Azure App Service Web Apps sous Linux](app-service-linux-faq.md)
