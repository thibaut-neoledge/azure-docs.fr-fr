---
title: "Exemples Azure PowerShell pour Azure Cosmos DB | Microsoft Docs"
description: "Exemples Azure PowerShell - Des scripts pour vous aider à créer et gérer des comptes Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8bf047bd19c5278bfff85cab63ea10a1838cc683
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemples Azure PowerShell pour Azure Cosmos DB

Le tableau suivant comprend des liens vers des exemples de scripts Azure PowerShell pour Azure Cosmos DB.

| |  |
|---|---|
|**Création d’un compte Azure Cosmos DB**||
|[Créer un compte d’API DocumentDB](scripts/create-database-account-powershell.md)| Crée un seul compte Azure Cosmos DB à utiliser avec l’API DocumentDB. |
|**Mettre à l’échelle Azure Cosmos DB**||
|[Répliquer un compte Azure Cosmos DB dans plusieurs régions et configurer les priorités de basculement](scripts/scale-multiregion-powershell.md)|Réplique les données de compte globalement dans plusieurs régions avec une priorité de basculement spécifié.|
|**Sécuriser Azure Cosmos DB**||
| [Obtenir les clés de compte](scripts/secure-get-account-key-powershell.md) | Obtient les clés primaire et secondaire d’écriture maître et les clés primaire et secondaire de lecture seule pour le compte.|
| [Obtenir la chaîne de connexion MongoDB](scripts/secure-mongo-connection-string-powershell.md) | Obtient la chaîne de connexion pour connecter votre application MongoDB à votre compte Azure Cosmos DB.|
|[Régénération des clés de compte](scripts/secure-regenerate-key-powershell.md)|Régénère la clé de maître ou en lecture seule pour le compte.|
|[Créer un pare-feu](scripts/create-firewall-powershell.md)| Crée une stratégie de contrôle d’accès des IP entrantes pour limiter l’accès au compte à un ensemble de machines et/ou services cloud approuvés.|
|**Haute disponibilité et récupération d'urgence, sauvegarde et restauration**||
|[Configurer la stratégie de basculement](scripts/ha-failover-policy-powershell.md)|Définit la priorité de basculement de chaque région dans laquelle le compte est répliqué.|
|||
