<properties
   pageTitle="Protection du service SQL Azure dans Azure Security Center | Microsoft Azure"
   description="Ce document traite des recommandations d’Azure Security Center qui peuvent vous aider à protéger le service SQL Azure et à rester en conformité avec les stratégies de sécurité."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# Protection du service SQL Azure dans Azure Security Center

Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires. Ces recommandations s’appliquent aux types de ressources Azure : machines virtuelles, mise en réseau, applications et SQL.

Cet article traite des recommandations qui s’appliquent au service SQL Azure. Les recommandations relatives au service SQL Azure se concentrent autour de l’audit des bases de données et des serveurs SQL Azure et de l’activation du chiffrement pour les bases de données SQL. Utilisez le tableau ci-dessous pour mieux comprendre les recommandations disponibles pour le service SQL et leurs effets.

## Recommandations disponibles pour le service SQL

|Recommandation|Description|
|-----|-----|
|[Activer l’audit des serveurs SQL](security-center-enable-auditing-on-sql-servers.md)|Recommande l’activation de l’audit pour les serveurs SQL Azure (service Azure SQL uniquement ; ne comprend pas les serveurs SQL exécutés sur des machines virtuelles).|
|[Activer l’audit des bases de données SQL](security-center-enable-auditing-on-sql-databases.md)|Recommande l’activation de l’audit pour les bases de données SQL Azure (service Azure SQL uniquement ; ne comprend pas les serveurs SQL exécutés sur des machines virtuelles).|
|[Activer le chiffrement transparent des données des bases de données SQL](security-center-enable-transparent-data-encryption.md)|Recommande l’activation du chiffrement pour les bases de données SQL (service Azure SQL uniquement).|

## Voir aussi

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

- [Protection de vos machines virtuelles dans Azure Security Center](security-center-virtual-machine-recommendations.md)
- [Protection de vos applications dans Azure Security Center](security-center-application-recommendations.md)
- [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.

<!---HONumber=AcomDC_0810_2016-->