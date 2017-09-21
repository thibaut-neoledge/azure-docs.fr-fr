---
title: "Restrictions d’adresse IP avec Azure App Service | Microsoft Docs"
description: "Comment utiliser des restrictions d’adresse IP avec Azure App Service"
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/12/2017
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 83cdc42d412f646ddf1ecd1b65bf9aa46983b26b
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="azure-app-service-static-ip-restrictions"></a>Restrictions d’adresse IP statique avec Azure App Service #

Les restrictions d’adresse IP permettent de définir une liste d’adresses IP qui ne peuvent pas accéder à votre application. La liste de blocage peut inclure des adresses IP individuelles ou une plage d’adresses IP définie par un masque de sous-réseau.

Quand une demande à destination de l’application est générée à partir d’un client, l’adresse IP est recherchée dans la liste de blocage. S’il existe une correspondance, l’application répond avec un code d’état [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

Les restrictions d’adresse IP sont évaluées sur les mêmes instances de plan App Service que celles assignées à votre application.

Pour ajouter une règle de restriction d’adresse IP à votre application, dans le menu, ouvrez **Réseau**>**Restrictions d’adresse IP**, puis cliquez sur **Configurer des restrictions d’adresse IP**.

![Restrictions d’adresse IP] (media/app-service-ip-restrictions/ip-restrictions.png)

Vous pouvez alors consulter la liste des règles de restriction d’adresse IP définies pour votre application.

![Liste des restrictions d’adresse IP](media/app-service-ip-restrictions/browse-ip-restrictions.png)

Vous pouvez cliquer sur **[+] Ajouter** pour ajouter une nouvelle règle de restriction d’adresse IP.

![Ajouter des restrictions d’adresse IP](media/app-service-ip-restrictions/add-ip-restrictions.png)

