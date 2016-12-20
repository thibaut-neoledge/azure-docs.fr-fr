---
title: Utilisation de .NET Core dans Azure App Service Web Apps sur Linux | Microsoft Docs
description: Utilisation de .NET Core dans Azure App Service Web Apps sur Linux.
keywords: azure app service, application web, dotnet, core, linux, oss
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 317d6980d304cc503cc43358c4b91459d4abd1ba
ms.openlocfilehash: a54b7413c2eb6ae67b7424a8966d0cd82cd1ce6a


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Utilisation de .NET Core dans Web Apps sur Linux #

Lors de la dernière mise à jour de notre serveur principal, nous avons introduit la prise en charge de .NET Core v.1.0. En définissant la configuration de votre application web Linux, vous pouvez modifier la pile d’applications.


## <a name="using-xplat-cli"></a>Utilisation de l’interface de ligne de commande XPlat ##

À l’aide de l’interface de ligne de commande multiplateforme d’Azure la plus récente, vous pouvez utiliser la commande **azure webapp config set** pour modifier la pile d’applications. Voici un exemple :

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Notez que le fichier **aspnetcore.dll** correspond au dll de votre application. Vous pouvez utiliser le nom de votre choix dans votre application.

Cette opération entraînera le chargement de l’image .Net Core et le démarrage de votre application web. Vous pouvez vérifier que les paramètres ont été correctement définis à l’aide de la commande **azure webapp config show**. Voici un exemple :

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’App Service sur Linux](./app-service-linux-intro.md) 
* [Qu’est-ce qu’App Service sur Linux ?](app-service-linux-intro.md)
* [Création d’applications Web dans App Service sur Linux](./app-service-linux-how-to-create-a-web-app.md)
* [Interface de ligne de commande multiplateforme d’Azure Web Apps](app-service-web-app-azure-resource-manager-xplat-cli.md)



<!--HONumber=Nov16_HO3-->


