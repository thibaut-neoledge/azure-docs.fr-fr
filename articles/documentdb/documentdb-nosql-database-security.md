---
title: "Sécurité de la base de données NoSQL - Azure DocumentDB | Microsoft Docs"
description: "Découvrez comment Azure DocumentDB garantit la protection de la base de données et la sécurité des données pour vos données NoSQL."
keywords: "sécurité de la base de données NoSQL, sécurité des informations, sécurité des données, chiffrement de base de données, protection de base de données, stratégies de sécurité, tests de sécurité"
services: documentdb
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: a02a6a82-3baf-405c-9355-7a00aaa1a816
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 71668ceedca16647f814866ea9cf068f73e5daf6
ms.contentlocale: fr-fr
ms.lasthandoff: 04/28/2017


---

# <a name="documentdb-nosql-database-security"></a>Sécurité de la base de données NoSQL DocumentDB

Cet article décrit les meilleures pratiques en matière de sécurité de la base de données NoSQL et les fonctionnalités clés offertes par Azure DocumentDB pour vous aider à prévenir et détecter les violations de la base de données et à y répondre.
 
## <a name="whats-new-in-documentdb-security"></a>Nouveautés de DocumentDB en matière de sécurité

Un chiffrement au repos est désormais disponible pour les documents stockés sur DocumentDB dans toutes les régions Azure, à l’exception des régions du secteur public, d’Azure en Chine et d’Azure en Allemagne. Les régions restantes seront activées la semaine prochaine, en même temps que le chiffrement au repos des sauvegardes. Un chiffrement au repos est automatiquement appliqué aux clients nouveaux et existants dans ces régions. Vous n’avez pas à configurer quoi que ce soit. Vous bénéficiez des mêmes performances remarquables en matière de temps de latence, de débit, de disponibilité et de fonctionnalité qu’auparavant, avec l’avantage de savoir que vos données sont en lieu sûr et sécurisée grâce à un chiffrement au repos.

## <a name="how-do-i-secure-my-nosql-database"></a>Comment sécuriser ma base de données NoSQL ? 

La sécurité des données est une responsabilité partagée entre vous, le client et le fournisseur de votre base de données. Selon le fournisseur de base de données que vous choisissez, la part de votre responsabilité peut varier. Si vous choisissez une solution locale, vous devez fournir tous les éléments de la protection de point de terminaison à la sécurité physique de votre matériel, ce qui n’est pas une tâche facile. Si vous choisissez un fournisseur de base de données de cloud PaaS comme Azure DocumentDB, votre responsabilité est considérablement réduite. L’image suivante, tirée du livre blanc Microsoft [Shared Responsibilities for Cloud Computing](https://aka.ms/sharedresponsibility) (Responsabilités partagées en matière de cloud computing), illustre la manière dont votre responsabilité diminue en faisant appel à un fournisseur PaaS tel qu’Azure DocumentDB.

![Responsabilités du client et du fournisseur de base de données](./media/documentdb-nosql-database-security/nosql-database-security-responsibilities.png)

Le diagramme ci-dessus présente les éléments de sécurité de niveau supérieur sur le cloud. Sur quels points devez-vous particulièrement vous pencher dans le cadre de votre solution de base de données NoSQL ? Et comment comparer les solutions entre elles ? 

Nous vous recommandons d’utiliser la liste de vérification suivante répertoriant les exigences en fonction desquelles vous pouvez comparer les systèmes de base de données NoSQL :

- Sécurité du réseau et paramètres de pare-feu
- Authentification des utilisateurs et contrôles utilisateur affinés
- Possibilité de répliquer des données globalement en cas de défaillances régionales
- Possibilité d’effectuer des basculements d’un centre de données vers un autre
- Réplication locale des données dans un centre de données
- Sauvegardes automatiques des données
- Restauration des données supprimées à partir de sauvegardes
- Protection et isolement des données sensibles
- Surveillance des attaques
- Réponse aux attaques
- Possibilité de délimiter géographiquement les données pour respecter les restrictions de gouvernance des données
- Protection physique des serveurs dans les centres de données protégés

Bien que cela paraisse évident, de récents exemples de [violations de base de données à grande échelle](http://thehackernews.com/2017/01/mongodb-database-security.html) nous rappellent l’importance simple mais critique des exigences suivantes :
- Serveurs corrigés et régulièrement mis à jour
- Chiffrement HTTPS par défaut/SSL
- Comptes administratifs avec des mots de passe forts

## <a name="how-does-azure-documentdb-secure-my-database"></a>Comment Azure DocumentDB sécurise-t-il ma base de données ?

Reprenons la liste précédente : combien de ces exigences de sécurité Azure DocumentDB garantit-il ? Toutes, sans exception.

Examinons à présent chacune d’entre elles en détail.

|Exigence de sécurité|Approche de la sécurité de DocumentDB|
|---|---|---|
|Sécurité du réseau|L’utilisation d’un pare-feu IP est la première couche de protection pour sécuriser votre base de données NoSQL. DocumentDB prend en charge les contrôles d’accès basés sur une stratégie IP pour la prise en charge du pare-feu entrant. Les contrôles d’accès basés sur IP sont semblables aux règles de pare-feu utilisées par les systèmes de base de données traditionnels, mais ils sont développés de sorte qu’un compte de base de données DocumentDB est uniquement accessible à partir d’un ensemble d’ordinateurs ou de services cloud approuvés. <br><br>DocumentDB vous permet d’activer une adresse IP spécifique (168.61.48.0), une plage d’adresses IP (168.61.48.0/8) et des combinaisons d’adresses et de plages IP. <br><br>Toutes les demandes provenant d’ordinateurs ne figurant pas sur cette liste autorisée sont bloquées par DocumentDB. Les demandes issues d’ordinateurs et de services cloud approuvés doivent ensuite terminer le processus d’authentification pour bénéficier d’un contrôle d’accès sur les ressources.<br><br>Pour en savoir plus, consultez [Prise en charge du pare-feu dans DocumentDB](documentdb-firewall-support.md).|
|Autorisation|DocumentDB utilise un code d’authentification de message basé sur le hachage (HMAC) pour l’autorisation. <br><br>Chaque demande est hachée à l’aide de la clé secrète du compte et le hachage encodé en base 64 suivant est envoyé avec chaque appel à DocumentDB. Pour valider la demande, le service DocumentDB utilise la clé secrète et les propriétés appropriées pour générer un hachage, puis il compare la valeur à celle de la demande. Si les deux valeurs correspondent, l’opération est autorisée et la demande est traitée. Dans le cas contraire, un échec d’autorisation est généré et la demande est rejetée.<br><br>Vous pouvez utiliser une [clé principale](documentdb-secure-access-to-data.md#master-keys) ou un [jeton de ressource](documentdb-secure-access-to-data.md#resource-tokens) autorisant un accès précis à une ressource telle qu’un document.<br><br>Pour en savoir plus, consultez [Sécurisation de l’accès aux données DocumentDB](documentdb-secure-access-to-data.md).|
|Utilisateurs et autorisations|À l’aide de la [clé principale](#master-key) du compte, vous pouvez créer des ressources d’utilisateur et d’autorisation par base de données. Un [jeton de ressource](#resource-token) est associé à une autorisation dans une base de données et détermine si l’utilisateur a accès (en lecture-écriture, en lecture seule ou aucun accès) à une ressource d’application dans la base de données. Les ressources d’application comprennent les collections, les documents, les pièces jointes, les procédures stockées, les déclencheurs et les fonctions définies par l’utilisateur. La jeton de ressource est ensuite utilisé lors de l’authentification pour autoriser ou refuser l’accès à la ressource.<br><br>Pour en savoir plus, consultez [Sécurisation de l’accès aux données DocumentDB](documentdb-secure-access-to-data.md).|
|Intégration d’Active Directory (RBAC)| Vous pouvez également fournir l’accès au compte de base de données à l’aide du contrôle d’accès (IAM) dans le portail Azure. IAM fournit un contrôle d’accès basé sur les rôles et s’intègre à Active Directory. Vous pouvez utiliser les rôles intégrés ou personnalisés pour les individus et les groupes, comme illustré dans l’image suivante.<br><br>![Contrôle d’accès (IAM) dans le portail Azure - Démonstration de la sécurité de la base de données NoSQL](./media/documentdb-nosql-database-security/nosql-database-security-identity-access-management-iam-rbac.png)|
|Réplication mondiale|DocumentDB offre une diffusion mondiale clé en main, ce qui vous permet de répliquer vos données dans n’importe quel centre de données d’Azure dans le monde en un seul clic. La réplication mondiale vous permet une mise à l’échelle globale et fournit un accès à faible latence à vos données dans le monde entier.<br><br>En matière de sécurité, la réplication mondiale garantit la protection des données contre les défaillances régionales.<br><br>Pour en savoir plus, consultez [Distribution mondiale des données avec DocumentDB](documentdb-distribute-data-globally.md).|
|Basculements régionaux|Si vous avez répliqué vos données dans plusieurs centres de données, DocumentDB bascule automatiquement vos opérations si un centre de données régional passe hors connexion. Vous pouvez créer une liste des régions de basculement prioritaires comportant les régions dans lesquelles vos données sont répliquées. <br><br>Pour en savoir plus, consultez [Basculements régionaux automatiques pour la continuité des activités dans DocumentDB](documentdb-regional-failovers.md).|
|Réplication locale|Même au sein d’un centre de données unique, DocumentDB réplique automatiquement les données pour garantir une haute disponibilité et vous donne la possibilité de choisir des [niveaux de cohérence](documentdb-consistency-levels.md). Cela assure un  [contrat SLA avec une disponibilité de 99,99 %](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) et comporte une garantie financière, ce qu’aucun autre service de base de données NoSQL ne peut fournir.|
|Sauvegardes en ligne automatisées|Les bases de données DocumentDB sont régulièrement sauvegardées et stockées dans un magasin géoredondant. <br><br>Pour en savoir plus, consultez [Sauvegarde et restauration en ligne automatiques avec DocumentDB](documentdb-online-backup-and-restore.md).|
|Restauration de données supprimées|Les sauvegardes en ligne automatisées peuvent être utilisées pour récupérer des données que vous avez accidentellement supprimées jusqu’à environ 30 jours après l’événement. <br><br>Pour en savoir plus, consultez [Sauvegarde et restauration en ligne automatiques avec DocumentDB](documentdb-online-backup-and-restore.md).|
|Protection et isolement des données sensibles|Toutes les données stockées dans les régions répertoriées dans la section [Nouveautés](#whats-new) sont désormais chiffrées au repos.<br><br>Les informations d’identification personnelle et d’autres données confidentielles peuvent être isolées dans des collections spécifiques et un accès en lecture-écriture ou en lecture seule peut être restreint à des utilisateurs spécifiques.|
|Surveillance des attaques|À l’aide des enregistrements d’audit et des journaux d’activité, vous pouvez surveiller les activités normales et anormales de votre compte. Vous pouvez afficher les opérations qui ont été effectuées sur vos ressources, la personne qui a initié l’opération, le moment auquel l’opération a été réalisée, l’état de l’opération et bien plus encore.<br><br>![Journaux d’activité pour Azure DocumentDB](./media/documentdb-nosql-database-security/nosql-database-security-application-logging.png)|
|Réponse aux attaques|Une fois que vous avez contacté le support Azure pour signaler une attaque potentielle, un processus de réponse aux incidents en 5 étapes est lancé. L’objectif de ce processus en 5 étapes est de restaurer aussi rapidement que possible dans des conditions normales les opérations et la sécurité du service suite à la détection d’un problème et au lancement d’une investigation.<br><br>Pour en savoir plus, consultez [Microsoft Azure Security Response in the Cloud](https://aka.ms/securityresponsepaper) (Réponse de Microsoft Azure en matière de sécurité dans le cloud).|
|Délimitation géographique|DocumentDB garantit la gouvernance et la conformité des données pour les régions souveraines (par exemple, en Allemagne, en Chine, pour le gouvernement américain).|
|Installations protégées|Dans DocumentDB, les données sont stockées sur des disques SSD dans les centres de données protégées d’Azure.<br><br>Pour en savoir plus, consultez les [centres de données Microsoft globaux](https://www.microsoft.com/en-us/cloud-platform/global-datacenters).|
|Chiffrement HTTPS/SSL/TLS|Toutes les interactions client-service de DocumentDB appliquent un chiffrement SSL/TLS 1.2, tout comme les réplications au sein des centres de données ou entre différents centres de données.|
|Chiffrement au repos|Toutes les données stockées dans DocumentDB sont chiffrées au repos. Pour en savoir plus, consultez [DocumentDB encryption at rest (Chiffrement de DocumentDB au repos](.\documentdb-nosql-database-encryption-at-rest.md)|
|Serveurs corrigés|En tant que base de données NoSQL gérée, DocumentDB ne nécessite aucune gestion ou correction des serveurs, cela se fait automatiquement.|
|Comptes administratifs avec des mots de passe forts|Il est difficile de croire que nous devions encore mentionner cette exigence, mais contrairement à certains de nos concurrents, il est impossible d’avoir un compte d’administrateur sans mot de passe dans DocumentDB.<br><br> La sécurité via SSL et l’authentification basée sur un secret HMAC sont intégrées par défaut.|
|Certifications de sécurité et de protection des données|DocumentDB détient les certifications [ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001), [EUMC (European Model Clauses)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) et [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA). Des certifications supplémentaires sont en cours.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les clés principales et les jetons de ressource, consultez [Sécurisation de l’accès aux données DocumentDB](documentdb-secure-access-to-data.md).

Pour plus d’informations sur les certifications Microsoft, consultez le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/en-us/support/trust-center/).

