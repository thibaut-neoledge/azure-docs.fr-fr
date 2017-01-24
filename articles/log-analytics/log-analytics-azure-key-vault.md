---
title: Solution Azure Key Vault dans Log Analytics | Microsoft Docs
description: "La solution Azure Key Vault dans Log Analytics permet de consulter les journaux d’Azure Key Vault logs."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 5aae95a78e604acc5f0189d5df62620d65d29857
ms.openlocfilehash: bb9ece6382c22f4c1b7905048647434fc7cee98a


---
# <a name="azure-key-vault-analytics-preview-solution-in-log-analytics"></a>Solution Azure Key Vault Analytics (version préliminaire) dans Log Analytics

Vous pouvez utiliser la solution Azure Key Vault dans Log Analytics pour consulter les journaux AuditEvent d’Azure Key Vault.

> [!NOTE]
> Azure Key Vault Analytics est une [solution en version préliminaire](log-analytics-add-solutions.md#preview-management-solutions-and-features).
> 
> 

Pour l’utiliser, vous devez activer la journalisation des diagnostics d’Azure Key Vault et diriger les diagnostics vers un espace de travail Log Analytics. Il n’est pas nécessaire d’écrire les journaux dans le stockage Blob Azure.

## <a name="install-and-configure-the-solution"></a>Installer et configurer la solution
Suivez les instructions suivantes pour installer et configurer la solution Azure Key Vault :

1. Utilisez `Set-AzureRmDiagnosticSetting` pour activer la journalisation des diagnostics pour les ressources de Key Vault à analyser : 
2. Activez la solution Azure Key Vault en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). 

Le script PowerShell suivant fournit un exemple illustrant comment activer la journalisation des diagnostics pour le Key Vault :
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```
 
 

## <a name="review-azure-key-vault-data-collection-details"></a>Examiner les détails de la collecte de données par Azure Key Vault
La solution Azure Key Vault collecte des journaux de diagnostics directement à partir de Key Vault.
Il n’est pas nécessaire d’écrire les journaux dans le stockage Blob Azure, et aucun agent n’est requis pour la collecte de données.

Le tableau suivant présente les méthodes de collecte des données et d’autres détails sur le mode de collecte de données pour Azure Key Vault.

| Plateforme | Agent direct | Agent Systems Center Operations Manager | Microsoft Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Microsoft Azure |![Non](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Non](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Oui](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![Non](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Non](./media/log-analytics-azure-keyvault/oms-bullet-red.png) | à l'arrivée |

## <a name="use-azure-key-vault"></a>Utiliser Azure Key Vault
Après avoir installé la solution, vous pouvez afficher la synthèse des états de demande dans le temps pour vos Key Vault analysés à l’aide de la vignette **Azure Key Vault** sur la page **Vue d’ensemble** de Log Analytics.

![Image de la vignette Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Après avoir cliqué sur la vignette **Vue d’ensemble**, vous pouvez consulter des récapitulatifs de vos journaux et en approfondir des détails pour les catégories suivantes :

* Volume de toutes les opérations de Key Vault dans le temps
* Volumes des opérations en échec dans le temps
* Latence opérationnelle moyenne par opération
* Qualité de service des opérations, avec le nombre d’opérations qui prennent plus de 1 000 ms et la liste de ces opérations

![Image du tableau de bord d’Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Image du tableau de bord d’Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Pour afficher les détails d’une opération
1. Dans la page **Vue d’ensemble**, cliquez sur la vignette **Azure Key Vault**.
2. Sur le tableau de bord **Azure Key Vault**, examinez les informations résumées dans l’un des panneaux, puis cliquez sur l’une d’elles pour afficher des informations détaillées dans la page Recherche de journal.
   
    Sur l’une des pages de recherche de journal, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherches de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.

## <a name="log-analytics-records"></a>Enregistrements Log Analytics
La solution Azure Key Vault analyse les enregistrements de type **KeyVaults** qui sont collectés à partir des [journaux AuditEvent](../key-vault/key-vault-logging.md) dans les Diagnostics Azure.  Les propriétés de ces enregistrements figurent dans le tableau suivant :  

| Propriété | Description |
|:--- |:--- |
| Type |*AzureDiagnostics* |
| SourceSystem |*Microsoft Azure* |
| callerIpAddress |Adresse IP du client qui a effectué la demande. |
| Catégorie | *AuditEvent* |
| CorrelationId |GUID facultatif que le client peut transférer pour mettre en corrélation les journaux côté client avec les journaux côté service (Key Vault). |
| DurationMs |Délai nécessaire pour répondre à la demande API REST, en millisecondes. La latence du réseau n’étant pas incluse dans ce chiffre, le temps mesuré côté client peut ne pas correspondre à cette durée. |
| httpStatusCode_d |Code d’état HTTP retourné par la demande (par exemple, *200*) |
| id_s |ID unique de la demande. |
| identity_claim_appid_g | GUID de l’ID d’application |
| Nom d'opération |Nom de l’opération, comme décrit dans [journalisation d’Azure Key Vault](../key-vault/key-vault-logging.md) |
| operationVersion |Version d’API REST demandée par le client (par exemple *2015-06-01*) |
| requestUri_s |URI de la demande. |
| Ressource |Nom du Key Vault. |
| ResourceGroup |Groupe de ressources du Key Vault. |
| ResourceId |ID de ressource Azure Resource Manager Pour les journaux de coffre de clés, il s’agit de l’ID de ressource du coffre de clés. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |État HTTP (par exemple *OK*) |
| ResultType |Résultat de la demande de l’API REST (par exemple, *Réussite*) |
| SubscriptionId |ID de l’abonnement Azure contenant le Key Vault. |

## <a name="next-steps"></a>Étapes suivantes
* Utilisez les [recherches de journal dans Log Analytics](log-analytics-log-searches.md) pour afficher les données détaillées de Azure Key Vault.




<!--HONumber=Dec16_HO1-->


