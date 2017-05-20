---
title: "Utilisation de .NET Core dans l’application web Azure App Service sur Linux | Microsoft Docs"
description: "Utilisation de .NET Core dans l’application web Azure App Service sur Linux."
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
ms.date: 05/07/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3608ddf86c3d8010b577e6f745dcd5cef016acd9
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="using-net-core-in-azure-web-app-on-linux"></a>Utilisation de .NET Core dans l’application web Azure sur Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Lors de la dernière mise à jour de notre serveur principal, nous avons introduit la prise en charge de .NET Core v.1.0. En définissant la configuration de votre application web Linux, vous pouvez modifier la pile d’applications.


## <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI) ##

À l’aide de l’[interface de ligne de commande (CLI) d’Azure la plus récente](https://docs.microsoft.com/cli/azure/install-azure-cli), vous pouvez utiliser la commande **azure webapp config set** pour modifier la pile d’applications. Voici un exemple :

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Le fichier **aspnetcore.dll** correspond au dll de votre application. Vous pouvez utiliser le nom de votre choix dans votre application.

Cette opération charge de l’image .Net Core et démarre votre application web. Vous pouvez vérifier que les paramètres ont été correctement définis à l’aide de la commande **azure webapp config show**. Voici un exemple :

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce que l’application web Azure sur Linux ?](app-service-linux-intro.md)
* [Création d’applications web dans l’application web Azure sur Linux](./app-service-linux-how-to-create-web-app.md)
* [Interface de ligne de commande multiplateforme d’Azure Web Apps](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [FAQ Application web Azure App Service sur Linux](app-service-linux-faq.md)
