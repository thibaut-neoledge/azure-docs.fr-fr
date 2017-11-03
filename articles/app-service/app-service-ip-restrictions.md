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
ms.date: 10/23/2017
ms.author: byvinyal
ms.openlocfilehash: 5fbd308e9f037038ad867f3d242da6573bc67081
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2017
---
# <a name="azure-app-service-static-ip-restrictions"></a>Restrictions d’adresse IP statique avec Azure App Service #

Les restrictions d’adresse IP permettent de définir une liste d’adresses IP pouvant accéder à votre application. La liste d’autorisation peut inclure des adresses IP individuelles ou une plage d’adresses IP définie par un masque de sous-réseau.

Quand une demande à destination de l’application est générée à partir d’un client, l’adresse IP est recherchée dans la liste d’autorisation. Si l’adresse IP n’est pas dans la liste, l’application répond avec un code d’état [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

Les restrictions IP sont définies dans le fichier web.config que votre application consomme lors de l’exécution. Dans certaines circonstances, un module peut être exécuté avant la logique de restrictions IP dans le pipeline HTTP. Dans ce cas, la requête échoue avec un autre code d’erreur HTTP.

Les restrictions d’adresse IP sont évaluées sur les mêmes instances de plan App Service que celles assignées à votre application.

Pour ajouter une règle de restriction d’adresse IP à votre application, dans le menu, ouvrez **Réseau**>**Restrictions d’adresse IP**, puis cliquez sur **Configurer des restrictions d’adresse IP**.

![Restrictions d’adresse IP] (media/app-service-ip-restrictions/ip-restrictions.png)

Vous pouvez alors consulter la liste des règles de restriction d’adresse IP définies pour votre application.

![Liste des restrictions d’adresse IP](media/app-service-ip-restrictions/browse-ip-restrictions.png)

Vous pouvez cliquer sur **[+] Ajouter** pour ajouter une nouvelle règle de restriction d’adresse IP.

![Ajouter des restrictions d’adresse IP](media/app-service-ip-restrictions/add-ip-restrictions.png)
