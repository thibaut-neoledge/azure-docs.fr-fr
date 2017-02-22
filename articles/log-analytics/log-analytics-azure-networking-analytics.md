---
title: "Solution d’analytique du réseau Azure dans Log Analytics | Microsoft Docs"
description: "Vous pouvez utiliser la Solution d’analytique du réseau Azure dans Log Analytics pour consulter les journaux de groupes de sécurité réseau et de passerelle d’application Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: ewinner
editor: 
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 14df6b49d79aa1bf6f414070c60e7acac6578301
ms.openlocfilehash: 7267b41d5d1a7903a084eea2c813bc40249fbf6a


---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Solutions d’analyse réseaux Azure dans Log Analytics

Vous pouvez utiliser la solution d’analytique Azure Application Gateway dans Log Analytics pour consulter les éléments suivants :

* Journaux Azure Application Gateway
* Mesures Azure Application Gateway

Vous pouvez utiliser la solution d’analytique Azure Network Security Group dans Log Analytics pour consulter les éléments suivants :

* Journaux Azure Network Security Group

Pour utiliser les solutions, activez les diagnostics pour les journaux Azure Application Gateway et Azure Network Security Groups, et dirigez les diagnostics vers un espace de travail Log Analytics. Il n’est pas nécessaire d’écrire les journaux dans le stockage Blob Azure.

Vous pouvez activer les diagnostics et la solution correspondante pour Application Gateway, Networking Security Groups ou les deux.

Si vous n’activez pas la journalisation des diagnostics pour un type de ressource en particulier, mais que vous installez la solution, les panneaux du tableau de bord de cette ressource sont vides et affichent un message d’erreur.

> [!NOTE]
> En janvier 2017, la méthode prise en charge pour envoyer des journaux à Log Analytics à partir d’Application Gateways et de Network Security Groups a été modifiée. Si vous voyez la solution **Azure Networking Analytics (déconseillée)**, consultez [Migration à partir de l’ancienne solution Azure Networking](#migrating-from-the-old-networking-analytics-solution) pour connaître les étapes à suivre.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Consulter les détails de la collecte de données réseaux Azure
Les solutions de gestion de l’analytique Azure Application Gateway et l’analytique Network Security Group collectent des journaux de diagnostic directement à partir d’Azure Application Gateways et Network Security Groups. Il n’est pas nécessaire d’écrire les journaux dans le stockage Blob Azure, et aucun agent n’est requis pour la collecte de données.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte des données pour l’analytique Azure Application Gateway et l’analytique Network Security Group.

| Plateforme | Agent direct | Agent Systems Center Operations Manager | Microsoft Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Microsoft Azure |![Non](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Non](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Oui](./media/log-analytics-azure-networking/oms-bullet-green.png) |![Non](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Non](./media/log-analytics-azure-networking/oms-bullet-red.png) |si connecté |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Solution d’analytique Azure Application Gateway dans Log Analytics

Les journaux pris en charge pour les passerelles d’application sont les suivants :

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Les métriques prises en charge pour les passerelles d’application sont les suivantes :

* Débit de&5; minutes

### <a name="install-and-configure-the-solution"></a>Installer et configurer la solution
Pour installer et configurer la solution d’analytique Azure Application Gateway, suivez les instructions suivantes :

1. Activez la journalisation des diagnostics pour les [Application Gateways](../application-gateway/application-gateway-diagnostics.md) que vous voulez surveiller.
2. Activez la solution d’analytique Azure Application Gateway en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). 

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Activer les diagnostics Azure Application Gateway dans le portail

1. Dans le Portail Azure, accédez à la ressource Application Gateway à surveiller.
2. Sélectionnez *Journaux de diagnostic* pour ouvrir la page suivante.

   ![Image de la ressource Azure Application Gateway](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. Cliquez sur *Activer les diagnostics* pour ouvrir la page suivante.

   ![Image de la ressource Azure Application Gateway](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. Pour activer les diagnostics, cliquez sur *Activé* sous *État*.
5. Cochez la case à côté de l’option *Envoyer à Log Analytics*.
6. Sélectionnez un espace de travail Log Analytics existant ou créez-en un.
7. Cochez la case sous **Journal** pour chacun des types de journaux à collecter.
8. Cliquez sur *Enregistrer* pour activer la journalisation des diagnostics dans Log Analytics.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Activez les diagnostics de réseau Azure avec PowerShell

Le script PowerShell suivant fournit un exemple montrant comment activer la journalisation des diagnostics pour les Application Gateways.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Utiliser l’analytique Azure Application Gateway
![Image de la mosaïque d’analytique Azure Application Gateway](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

Une fois que vous avez cliqué sur la mosaïque **d’analytique Azure Application Gateway** dans la Vue d’ensemble, vous pouvez consulter les résumés de vos journaux et entrer dans les détails des catégories suivantes :

* Journaux d’accès à la passerelle d’application
  * Erreurs client et serveur pour les journaux d’accès à la passerelle d’application
  * Nombre de demandes par heure pour chaque passerelle d’application
  * Nombre d’échecs de demande par heure pour chaque passerelle d’application
  * Erreurs de l’agent utilisateur pour les passerelles d’application
* Performances de la passerelle d’application
  * Intégrité de l’hôte pour la passerelle d’application
  * Nombre maximal et 95e centile pour les échecs de demande de passerelle d’application

![Image du tableau de bord d’analytique Azure Application Gateway](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![Image du tableau de bord d’analytique Azure Application Gateway](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

Sur le tableau de bord **d’analytique Azure Application Gateway**, consultez les informations du résumé dans l’un des panneaux, puis cliquez sur l’un d’entre eux pour afficher des informations détaillées sur la page de recherche dans les journaux.
   
Sur l’une des pages de recherche de journal, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherches de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Solution d’analytique Azure Network Security Group dans Log Analytics

Les fichiers journaux suivants sont pris en charge pour les groupes de sécurité réseau :

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Installer et configurer la solution
Pour installer et configurer la solution d’analytique du réseau Azure, suivez les instructions suivantes :

1. Activez la journalisation des diagnostics pour les ressources [Network Security Group](../virtual-network/virtual-network-nsg-manage-log.md) que vous voulez surveiller.
2. Activez la solution d’analytique Azure Network Security Group en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). 

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Activer les diagnostics Azure Network Security Group dans le portail

1. Dans le Portail Azure, accédez à la ressource Network Security Group à surveiller.
2. Sélectionnez *Journaux de diagnostic* pour ouvrir la page suivante.

   ![Image de la ressource Azure Network Security Group](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. Cliquez sur *Activer les diagnostics* pour ouvrir la page suivante.

   ![Image de la ressource Azure Network Security Group](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. Pour activer les diagnostics, cliquez sur *Activé* sous *État*.
5. Cochez la case à côté de l’option *Envoyer à Log Analytics*.
6. Sélectionnez un espace de travail Log Analytics existant ou créez-en un.
7. Cochez la case sous **Journal** pour chacun des types de journaux à collecter.
8. Cliquez sur *Enregistrer* pour activer la journalisation des diagnostics dans Log Analytics.

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Activez les diagnostics de réseau Azure avec PowerShell

Le script PowerShell suivant fournit un exemple montrant comment activer la journalisation des diagnostics pour les groupes de sécurité réseau 
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Utiliser l’analytique Azure Network Security Group
Une fois que vous avez cliqué sur la mosaïque **d’analytique Azure Network Security Group** dans la Vue d’ensemble, vous pouvez consulter les résumés de vos journaux et entrer dans les détails des catégories suivantes :

* Flux bloqués de groupe de sécurité réseau
  * Règles de groupe de sécurité réseau avec flux bloqués
  * Adresses MAC avec flux bloqués
* Flux autorisés de groupe de sécurité réseau
  * Règles de groupe de sécurité réseau avec flux autorisés
  * Adresses MAC avec flux autorisés

![Image du tableau de bord d’analytique Azure Network Security Group](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![Image du tableau de bord d’analytique Azure Network Security Group](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

Sur le tableau de bord **d’analytique Azure Network Security Group**, consultez les informations du résumé dans l’un des panneaux, puis cliquez sur l’un d’entre eux pour afficher des informations détaillées sur la page de recherche dans les journaux.
   
Sur l’une des pages de recherche de journal, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherches de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migration à partir de l’ancienne solution Azure Networking
En janvier 2017, la méthode prise en charge pour envoyer des journaux à Log Analytics à partir d’Azure Application Gateways et Azure Network Security Groups a été modifiée. Ces modifications présentent les avantages suivants :
+ Les journaux sont écrits directement dans Log Analytics sans avoir besoin d’utiliser un compte de stockage.
+ La durée de latence est plus courte entre le moment où les journaux sont générés et celui où ils deviennent disponibles dans Log Analytics.
+ Le nombre d’étapes de configuration est réduit.
+ Tous les types de diagnostics Azure sont au même format.

Pour utiliser les solutions mises à jour :

1. [Configurez les diagnostics pour qu’ils soient directement envoyés à Log Analytics à partir d’Azure Application Gateways](#enable-azure-application-gateway-diagnostics-in-the-portal).
2. [Configurez les diagnostics pour qu’ils soient directement envoyés à Log Analytics à partir d’Azure Network Security Groups](#enable-azure-network-security-group-diagnostics-in-the-portal).
2. Activez la solution *d’analytique Azure Application Gateway* et *d’analytique Azure Network Security Group* en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).
3. Mettez à jour les requêtes, les tableaux de bord et les alertes enregistrés pour qu’ils utilisent le nouveau type de données.
  + Le type doit être AzureDiagnostics. Vous pouvez utiliser ResourceType pour filtrer les journaux réseaux Azure.
  
    | Au lieu du paramètre... | Utilisez : |
    | --- | --- |
    |`Type=NetworkApplicationgateways OperationName=ApplicationGatewayAccess`| `Type=AzureDiagnostics ResourceType=APPLICATIONGATEWAYS OperationName=ApplicationGatewayAccess` |
    |`Type=NetworkApplicationgateways OperationName=ApplicationGatewayPerformance` | `Type=AzureDiagnostics ResourceType=APPLICATIONGATEWAYS OperationName=ApplicationGatewayPerformance` |
    | `Type=NetworkSecuritygroups` | `Type=AzureDiagnostics ResourceType=NETWORKSECURITYGROUPS` |
    
   + Pour tous les champs dont le suffixe est \_s, \_d ou \_g, remplacez le premier caractère du nom par une lettre minuscule.
   + Pour tous les champs dont le suffixe est \_o, les données sont réparties en plusieurs champs, selon les noms de champs imbriqués.
4. Supprimez la solution *Azure Networking Analytics (déconseillée)*. 
  + Si vous utilisez PowerShell, utilisez `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`. 

Les données collectées avant la modification ne seront pas visibles dans la nouvelle solution. Vous pouvez continuer à interroger ces données à l’aide de l’ancien type et des anciens noms de champs.

## <a name="troubleshooting"></a>résolution des problèmes
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Étapes suivantes
* Utilisez les [Recherches dans les journaux dans Log Analytics](log-analytics-log-searches.md) pour afficher les données détaillées de diagnostics Azure.




<!--HONumber=Feb17_HO3-->


