---
title: "Concepts de haute disponibilité dans une base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Cette rubrique fournit des informations de haute disponibilité lors de l’utilisation d’Azure Database pour PostgreSQL"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: 600896cf064770a5b294f874dc29081f0ce7d942
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql"></a>Concepts de haute disponibilité dans une base de données Azure pour PostgreSQL
Le service Azure Database pour PostgreSQL fournit un haut niveau de disponibilité garanti. Le contrat de niveau de service (SLA) est de 99,99 % selon la disponibilité générale. Il n’existe pratiquement aucun temps d’arrêt d’application lors de l’utilisation de ce service.

## <a name="high-availability"></a>Haute disponibilité
Le modèle de haute disponibilité (HA) est basé sur des mécanismes de basculement intégrés quand une interruption au niveau du nœud se produit. Une interruption au niveau du nœud peut se produire en raison d’une défaillance matérielle ou en réponse à un déploiement de service.

À tout moment, les modifications apportées à un serveur de base de données Azure Database pour PostgreSQL se produisent dans le contexte d’une transaction. Les modifications sont enregistrées de manière synchrone dans Stockage Azure lorsque la transaction est validée. En cas d’interruption au niveau du nœud, le serveur de base de données crée automatiquement un nouveau nœud et associe le stockage de données au nouveau nœud. Toutes les connexions actives sont supprimées et aucune transaction séquentielle n’est validée.

## <a name="application-retry-logic-is-essential"></a>La logique de nouvelle tentative d’application est essentielle
Il est important que des applications de base de données PostgreSQL soient conçues pour détecter et relancer les connexions interrompues et transactions ayant échoué. Lorsque l’application lance une nouvelle tentative, la connexion de l’application est redirigée de façon transparente vers l’instance nouvellement créée, qui remplace l’instance qui a échoué.

En interne dans Azure, une passerelle est utilisée pour rediriger les connexions vers la nouvelle instance. Lors d’une interruption, tout le processus de basculement prend généralement quelques dizaines de secondes. Étant donné que la redirection est gérée en interne par la passerelle, la chaîne de connexion externe reste la même pour les applications clientes.

## <a name="scaling-up-or-down"></a>Augmentation ou diminution d’échelle
Comme pour le modèle de haute disponibilité, lorsqu’une base de données PostgreSQL Azure voit son échelle augmentée ou réduite, une nouvelle instance de serveur avec la taille spécifiée est créée. Le stockage de données existant est détaché de l’instance d’origine et attaché à la nouvelle instance.

Pendant l’opération de mise à l’échelle, une interruption se produit pour les connexions de base de données. Les applications clientes sont déconnectées, et les transactions non validées en cours sont annulées. Une fois que l’application cliente réessaie d’établir la connexion ou établit une nouvelle connexion, la passerelle dirige la connexion vers l’instance qui vient d’être dimensionnée. 

## <a name="next-steps"></a>Étapes suivantes
- Vous trouverez une vue d’ensemble du service à la page [Vue d’ensemble de la base de données Azure pour PostgreSQL](overview.md).
