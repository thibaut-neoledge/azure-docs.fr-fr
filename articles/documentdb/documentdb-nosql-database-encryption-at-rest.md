---
title: "Chiffrement de base de données NoSQL au repos - Azure DocumentDB | Microsoft Docs"
description: "Découvrez la façon dont Azure DocumentDB fournit un chiffrement par défaut de toutes les données NoSQL."
services: documentdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 9c97f76d581935cb85f33b0016c192ded3b6f880
ms.contentlocale: fr-fr
ms.lasthandoff: 04/28/2017


---

# <a name="documentdb-nosql-database-encryption-at-rest"></a>Chiffrement de base de données NoSQL DocumentDB au repos

Le chiffrement au repos est une expression qui se réfère en général au chiffrement des données situées sur des appareils de stockage non volatile, comme des disques SSD et des disques durs.  DocumentDB stocke ses bases de données principales sur disque SSD, et ses sauvegardes et médias joints dans des blobs Azure souvent appuyés par un disque dur.  Avec la publication du chiffrement au repos pour DocumentDB, l’ensemble de vos bases de données, médias joints et sauvegardes sont maintenant chiffrés.  Cela signifie que vos données sont à présent chiffrées en transit (sur le réseau) et au repos (stockage non volatile), ce qui vous donne un chiffrement de bout en bout.

En tant que fournisseur de service PaaS, nous avons travaillé dur pour rendre DocumentDB très facile à utiliser.  Dans cet esprit, toutes les données utilisateur stockées dans DocumentDB sont chiffrées au repos et pendant le transport sans que l’utilisateur ait à intervenir.  En d’autres termes, le chiffrement au repos est activé par défaut.  Il n’existe aucun contrôle à activer ou désactiver et nous avons fourni cette fonctionnalité tout en continuant à répondre à notre [contrat SLA spécifiant la disponibilité et les performances](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/).

## <a name="how-does-encryption-at-rest-work"></a>Fonctionnement du chiffrement au repos

Le chiffrement au repos est implémenté à l’aide d’un certain nombre de technologies de sécurité, y compris les systèmes de stockage de clés sécurisés, les réseaux chiffrés et les API de chiffrement.  Le schéma suivant montre la façon dont le stockage des données chiffrées et la gestion des clés sont séparés.  Les systèmes qui déchiffrent et traitent les données doivent communiquer avec les systèmes de gestion de clés.

![Schéma montrant la conception](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

Le flux de base d’une demande de l’utilisateur est le suivant :
- Le compte d’utilisateur de base de données est prêt et les clés de stockage sont récupérées via une demande transmise au fournisseur de ressources du service de gestion.
- Un utilisateur crée une connexion à DocumentDB via le transport HTTPS sécurisé (voir les Kits de développement logiciel pour les détails).
- L’utilisateur envoie un document JSON à stocker sur la connexion sécurisée créée précédemment.
- Le document JSON est indexé à moins que l’utilisateur ait désactivé l’indexation.
- Le document JSON et les données d’index sont écrits dans le stockage sécurisé.
- Les données sont lues régulièrement à partir du stockage sécurisé et sauvegardées dans le magasin de blobs chiffré Azure.

## <a name="frequently-asked-questions"></a>Forum Aux Questions

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>Q : Quel est le coût de Stockage Azure si SSE est activé ?
R : Aucun coût supplémentaire n’est facturé.

### <a name="q-who-manages-the-encryption-keys"></a>Q : Qui gère les clés de chiffrement ?
R : Les clés sont gérées par Microsoft.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Q : Puis-je utiliser mes propres clés de chiffrement ?
A : DocumentDB étant un service PaaS, nous avons travaillé dur pour le rendre très facile à utiliser.  Nous avons constaté que cette question est souvent demandée en rapport avec la conformité aux standards tels que PCI-DSS.  Dans le cadre du développement de cette fonctionnalité, nous avons travaillé avec les auditeurs de conformité pour garantir que DocumentDB répond aux besoins des clients sans que ceux-ci doivent gérer les clés eux-mêmes.
C’est pourquoi nous offrons actuellement aux utilisateurs la possibilité de s’occuper eux-mêmes de la gestion des clés.

### <a name="q-what-regions-have-encryption-turned-on"></a>Q : Dans quelles régions le chiffrement est-il activé ?
R : Le chiffrement est activé dans toutes les régions DocumentDB pour l’ensemble des données utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Pour une vue d’ensemble de la sécurité de DocumentDB et connaître les dernières améliorations, consultez [Sécurité de base de données NoSQL DocumentDB](documentdb-nosql-database-security.md).

Pour plus d’informations sur les certifications Microsoft, consultez le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/en-us/support/trust-center/).

