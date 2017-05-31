---
title: "Chiffrement de base de données au repos - Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment Azure Cosmos DB assure le chiffrement par défaut de toutes les données."
services: cosmosdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8248b429cf9b7fdfc709a68c4d1e88b7b660020b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Chiffrement de base de données Azure Cosmos DB au repos

Le chiffrement au repos est une expression qui se réfère en général au chiffrement des données situées sur des appareils de stockage non volatile, comme des disques SSD et des disques durs.  Azure Cosmos DB stocke ses bases de données principales sur disque SSD, et ses sauvegardes et médias joints dans des objets blobs Azure souvent adossés à des disques durs.  Avec l’apparition du chiffrement au repos pour Azure Cosmos DB, l’ensemble de vos bases de données, médias attachés et sauvegardes sont maintenant chiffrés.  Cela signifie que vos données sont à présent chiffrées en transit (sur le réseau) et au repos (stockage non volatile), ce qui vous donne un chiffrement de bout en bout.

En tant que fournisseur de service PaaS, nous avons travaillé dur pour rendre Azure Cosmos DB très facile à utiliser.  Dans cet esprit, toutes les données utilisateur stockées dans Azure Cosmos DB sont chiffrées au repos et pendant le transport, sans que l’utilisateur ait à intervenir.  En d’autres termes, le chiffrement au repos est activé par défaut.  Il n’existe aucun contrôle à activer ou désactiver et nous avons fourni cette fonctionnalité tout en continuant à répondre à notre [contrat SLA spécifiant la disponibilité et les performances](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/).

## <a name="how-does-encryption-at-rest-work"></a>Fonctionnement du chiffrement au repos

Le chiffrement au repos est implémenté à l’aide d’un certain nombre de technologies de sécurité, y compris les systèmes de stockage de clés sécurisés, les réseaux chiffrés et les API de chiffrement.  Le schéma suivant montre la façon dont le stockage des données chiffrées et la gestion des clés sont séparés.  Les systèmes qui déchiffrent et traitent les données doivent communiquer avec les systèmes de gestion de clés.

![Schéma montrant la conception](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

Le flux de base d’une demande de l’utilisateur est le suivant :
- Le compte d’utilisateur de base de données est prêt et les clés de stockage sont récupérées via une demande transmise au fournisseur de ressources du service de gestion.
- Un utilisateur crée une connexion à Azure Cosmos DB via HTTPS/le transport sécurisé. Pour plus d’informations, consultez les kits de développement logiciel (SDK).
- L’utilisateur envoie un document JSON à stocker sur la connexion sécurisée créée précédemment.
- Le document JSON est indexé à moins que l’utilisateur ait désactivé l’indexation.
- Le document JSON et les données d’index sont écrits dans le stockage sécurisé.
- Les données sont lues régulièrement à partir du stockage sécurisé et sauvegardées dans le magasin de blobs chiffré Azure.

## <a name="frequently-asked-questions"></a>Forum Aux Questions

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>Q : Quel est le coût de Stockage Azure si SSE est activé ?
R : Aucun coût supplémentaire n’est facturé.

### <a name="q-who-manages-the-encryption-keys"></a>Q : Qui gère les clés de chiffrement ?
R : Les clés sont gérées par Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>Q : À quelle fréquence les clés de chiffrement tournent-elles ?
R : Microsoft a élaboré des recommandations internes, que DocumentDB respecte.  Bien qu’aucune instruction spécifique n’ait été publiée, Microsoft publie le SDL (Security Development Lifecycle) [https://www.microsoft.com/sdl/default.aspx] qui est considéré comme un sous-ensemble d’instructions internes et propose des conseils utiles pour les développeurs.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Q : Puis-je utiliser mes propres clés de chiffrement ?
R : Azure Cosmos DB est un service PaaS et nous avons beaucoup travaillé pour le rendre facile à utiliser.  Nous avons constaté que cette question est souvent demandée en rapport avec la conformité aux standards tels que PCI-DSS.  Dans le cadre du développement de cette fonctionnalité, nous avons collaboré avec des auditeurs de conformité pour garantir qu’Azure Cosmos DB réponde à leurs exigences et leur évite de gérer les clés eux-mêmes.
C’est pourquoi nous offrons actuellement aux utilisateurs la possibilité de s’occuper eux-mêmes de la gestion des clés.


### <a name="q-what-regions-have-encryption-turned-on"></a>Q : Dans quelles régions le chiffrement est-il activé ?
R : Le chiffrement est activé dans toutes les régions Azure Cosmos DB pour l’ensemble des données utilisateur.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Q : Le chiffrement modifie-t-il les SLA en termes de débit et de latence des performances ?
R : L’activation du chiffrement au repos sur tous les comptes (existants et nouveaux) ne modifie en rien les SLA en termes de performance.  Pour en savoir plus sur les dernières garanties, consultez la [page des SLA de performance](https://azure.microsoft.com/support/legal/sla/documentdb).

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Q : L’émulateur local prend-il en charge le chiffrement au repos ?
R : L’émulateur est un outil de développement/test autonome, qui n’exploite pas les services de gestion de clés que le service DocumentDB géré utilise. Nous vous conseillons d’activer BitLocker sur les lecteurs où vous stockez des données de test d’émulateur sensibles. L’émulateur autorise la modification du répertoire de données par défaut [https://docs.microsoft.com/azure/documentdb/documentdb-nosql-local-emulator] et l’utilisation d’un emplacement connu.

## <a name="next-steps"></a>Étapes suivantes

Pour une vue d’ensemble de la sécurité Azure Cosmos DB et des dernières améliorations, consultez [Sécurité de la base de données Azure Cosmos DB](documentdb-nosql-database-security.md).

Pour plus d’informations sur les certifications Microsoft, consultez le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/en-us/support/trust-center/).

