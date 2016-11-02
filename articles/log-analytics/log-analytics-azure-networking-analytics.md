<properties
    pageTitle="Solution d’analytique du réseau Azure dans Log Analytics | Microsoft Azure"
    description="Vous pouvez utiliser la Solution d’analytique du réseau Azure dans Log Analytics pour consulter les journaux de groupes de sécurité réseau et de passerelle d’application Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="richrund"/>


# <a name="azure-networking-analytics-(preview)-solution-in-log-analytics"></a>Solution d’analytique du réseau Azure (version préliminaire) dans Log Analytics

>[AZURE.NOTE] Il s’agit d’une [solution en version préliminaire](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Vous pouvez utiliser la solution d’analytique du réseau Azure dans Log Analytics pour examiner les journaux de passerelle d’application et de groupes de sécurité réseau Azure.

Vous pouvez activer la journalisation pour les journaux de passerelle d’application et de groupes de sécurité réseau Azure. Ces journaux sont écrits dans le Stockage Blob, où Log Analytics peut les indexer à des fins de recherche et d’analyse.

Les journaux pris en charge pour les passerelles d’application sont les suivants :

+ ApplicationGatewayAccessLog
+ ApplicationGatewayPerformanceLog

Les fichiers journaux suivants sont pris en charge pour les groupes de sécurité réseau :

+ NetworkSecurityGroupEvent
+ NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>Installer et configurer la solution

Pour installer et configurer la solution d’analytique du réseau Azure, suivez les instructions suivantes :

1.  Activez la journalisation des diagnostics pour les ressources à analyser :
  + [Application Gateway](../application-gateway/application-gateway-diagnostics.md)
  + [Groupe de sécurité réseau](../virtual-network/virtual-network-nsg-manage-log.md)
2.  Configurez Log Analytics pour lire les journaux du Stockage Blob en procédant de la manière décrite dans [Fichiers JSON dans le Stockage Blob](../log-analytics/log-analytics-azure-storage-json.md).
3.  Activez la solution d’analytique du réseau Azure en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).  

Si vous n’activez pas la journalisation des diagnostics pour un type de ressource particulier, les panneaux de tableau de bord pour cette ressource sont vides.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Examiner les détails de la collecte de données d’analytique du réseau Azure

La solution d’analytique du réseau Azure collecte des journaux de diagnostic à partir du Stockage Blob Azure pour les passerelles d’application et groupes de sécurité réseau Azure.
Aucun agent n’est requis pour la collecte de données.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte pour la solution d’analytique du réseau Azure.

| Plateforme | Agent direct | Agent Systems Center Operations Manager (SCOM) | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | Fréquence de collecte |
|---|---|---|---|---|---|---|
|Microsoft Azure|![Non](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Non](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Oui](./media/log-analytics-azure-networking/oms-bullet-green.png)|            ![Non](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Non](./media/log-analytics-azure-networking/oms-bullet-red.png)| 10 minutes|

## <a name="use-azure-networking-analytics"></a>Utiliser la solution d’analytique du réseau Azure

Après avoir installé la solution, vous pouvez afficher le résumé des erreurs client et serveur pour vos passerelles d’application analysées en utilisant la vignette **Analytique du réseau Azure** dans la page **Vue d’ensemble** de Log Analytics.

![Image de la vignette Analytique du réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

Après avoir cliqué sur la vignette **Vue d’ensemble**, vous pouvez consulter des récapitulatifs de vos journaux et en approfondir des détails pour les catégories suivantes :

+ Journaux d’accès à la passerelle d’application
  - Erreurs client et serveur pour les journaux d’accès à la passerelle d’application
  - Nombre de demandes par heure pour chaque passerelle d’application
  - Nombre d’échecs de demande par heure pour chaque passerelle d’application
  - Erreurs de l’agent utilisateur pour les passerelles d’application
+ Performances de la passerelle d’application
  - Intégrité de l’hôte pour la passerelle d’application
  - Nombre maximal et 95e centile pour les échecs de demande de passerelle d’application
+ Flux bloqués de groupe de sécurité réseau
  - Règles de groupe de sécurité réseau avec flux bloqués
  - Adresses MAC avec flux bloqués
+ Flux autorisés de groupe de sécurité réseau
  - Règles de groupe de sécurité réseau avec flux autorisés
  - Adresses MAC avec flux autorisés


![Image du tableau de bord d’analytique du réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![Image du tableau de bord d’analytique du réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![Image du tableau de bord d’analytique du réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![Image du tableau de bord d’analytique du réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>Pour afficher les détails d’un résumé du journal

1. Dans la page **Vue d’ensemble**, cliquez sur la vignette **Analytique du réseau Azure**.
2. Sur le tableau de bord de la solution d’**analytique du réseau Azure**, passez en revue les informations résumées de l’un des panneaux de type de modification, puis cliquez sur l’une d’entre elles pour afficher des informations détaillées dans la page Recherche de journal.

    Sur l’une des pages de recherche de journal, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherches de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.

## <a name="next-steps"></a>Étapes suivantes

- Utilisez [Recherches de journal dans Log Analytics](log-analytics-log-searches.md) pour afficher des données détaillées d’analytique du réseau Azure.



<!--HONumber=Oct16_HO2-->


