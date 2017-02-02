---
title: "Solution d’analytique du réseau Azure dans Log Analytics | Microsoft Docs"
description: "Vous pouvez utiliser la Solution d’analytique du réseau Azure dans Log Analytics pour consulter les journaux de groupes de sécurité réseau et de passerelle d’application Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/1/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: a86819102797b0e243d28cd9ddb3d2c88c74bfca
ms.openlocfilehash: 7ea593885c1b380236a49ec030c00ad19097e2fa


---
# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Solution d’analytique du réseau Azure (version préliminaire) dans Log Analytics

Vous pouvez utiliser Azure Networking Analytics dans Log Analytics pour vérifier ce qui suit :

* Journaux Azure Application Gateway
* Les métriques Application Gateway Azure, et 
* Journaux des groupes de sécurité réseau Azure.

> [!NOTE]
> Azure Networking Analytics est une [solution en version préliminaire](log-analytics-add-solutions.md#preview-management-solutions-and-features).
> 
> 

Pour utiliser la solution, activez les diagnostics pour les journaux d’Application Gateway Azure et les groupes de sécurité réseau Azure, et dirigez les diagnostics vers un espace de travail Log Analytics. Il n’est pas nécessaire d’écrire les journaux dans le stockage Blob Azure.

Les journaux pris en charge pour les passerelles d’application sont les suivants :

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Les métriques prises en charge pour les passerelles d’application sont les suivantes :

* Débit de 5 minutes

Les fichiers journaux suivants sont pris en charge pour les groupes de sécurité réseau :

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter
* NetworkSecurityGroupFlowEvent

## <a name="install-and-configure-the-solution"></a>Installer et configurer la solution
Pour installer et configurer la solution d’analytique du réseau Azure, suivez les instructions suivantes :

1. Activez la journalisation des diagnostics pour les ressources à analyser :
   * [Application Gateway](../application-gateway/application-gateway-diagnostics.md)
   * [Groupe de sécurité réseau](../virtual-network/virtual-network-nsg-manage-log.md)
2. Activez la solution d’analytique du réseau Azure en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).  

Le script PowerShell suivant fournit un exemple illustrant comment activer la journalisation des diagnostics pour les passerelles Application Gateway et les groupes de sécurité réseau 
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```


Si vous n’activez pas la journalisation des diagnostics pour un type de ressource particulier, les panneaux de tableau de bord pour cette ressource sont vides.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Examiner les détails de la collecte de données d’analytique du réseau Azure
La solution de gestion Azure Networking Analytics collecte des journaux de diagnostic directement depuis les instances d’Application Gateway Azure et groupes de sécurité réseau Azure. Il n’est pas nécessaire d’écrire les journaux dans le stockage Blob Azure, et aucun agent n’est requis pour la collecte de données.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte pour la solution d’analytique du réseau Azure.

| Plateforme | Agent direct | Agent Systems Center Operations Manager | Microsoft Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Microsoft Azure |![Non](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Non](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Oui](./media/log-analytics-azure-networking/oms-bullet-green.png) |![Non](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Non](./media/log-analytics-azure-networking/oms-bullet-red.png) |10 minutes |

## <a name="use-azure-networking-analytics"></a>Utiliser la solution d’analytique du réseau Azure
Après avoir installé la solution, vous pouvez afficher le résumé des erreurs client et serveur pour vos passerelles d’application analysées en utilisant la vignette **Analytique du réseau Azure** dans la page **Vue d’ensemble** de Log Analytics.

![Image de la vignette Analytique du réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

Après avoir cliqué sur la vignette **Vue d’ensemble**, vous pouvez consulter des récapitulatifs de vos journaux et en approfondir des détails pour les catégories suivantes :

* Journaux d’accès à la passerelle d’application
  * Erreurs client et serveur pour les journaux d’accès à la passerelle d’application
  * Nombre de demandes par heure pour chaque passerelle d’application
  * Nombre d’échecs de demande par heure pour chaque passerelle d’application
  * Erreurs de l’agent utilisateur pour les passerelles d’application
* Performances de la passerelle d’application
  * Intégrité de l’hôte pour la passerelle d’application
  * Nombre maximal et 95e centile pour les échecs de demande de passerelle d’application
* Flux bloqués de groupe de sécurité réseau
  * Règles de groupe de sécurité réseau avec flux bloqués
  * Adresses MAC avec flux bloqués
* Flux autorisés de groupe de sécurité réseau
  * Règles de groupe de sécurité réseau avec flux autorisés
  * Adresses MAC avec flux autorisés

![Image du tableau de bord d’analytique du réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![Image du tableau de bord d’analytique du réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![Image du tableau de bord d’analytique du réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![Image du tableau de bord d’analytique du réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>Pour afficher les détails d’un résumé du journal
1. Dans la page **Vue d’ensemble**, cliquez sur la vignette **Analytique du réseau Azure**.
2. Sur le tableau de bord de la solution d’**analytique du réseau Azure**, passez en revue les informations résumées de l’un des panneaux de type de modification, puis cliquez sur l’une d’entre elles pour afficher des informations détaillées dans la page Recherche de journal.
   
    Sur l’une des pages de recherche de journal, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherches de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.

## <a name="next-steps"></a>Étapes suivantes
* Utilisez [Recherches de journal dans Log Analytics](log-analytics-log-searches.md) pour afficher des données détaillées d’analytique du réseau Azure.




<!--HONumber=Dec16_HO1-->


