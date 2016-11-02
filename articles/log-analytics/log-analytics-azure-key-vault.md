<properties
    pageTitle="Solution Azure Key Vault dans Log Analytics | Microsoft Azure"
    description="La solution Azure Key Vault dans Log Analytics permet de consulter les journaux d’Azure Key Vault logs."
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
    ms.date="07/12/2016"
    ms.author="richrund"/>


# <a name="azure-key-vault-(preview)-solution-in-log-analytics"></a>Solution Azure Key Vault (version préliminaire) dans Log Analytics

>[AZURE.NOTE] Il s’agit d’une [solution en version préliminaire](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Vous pouvez utiliser la solution Azure Key Vault dans Log Analytics pour consulter les journaux AuditEvent d’Azure Key Vault.

Vous pouvez activer la journalisation des événements d’audit pour Azure Key Vault. Ces journaux sont écrits dans le Stockage Blob Azure, où Log Analytics peut les indexer à des fins de recherche et d’analyse.

## <a name="install-and-configure-the-solution"></a>Installer et configurer la solution

Suivez les instructions suivantes pour installer et configurer la solution Azure Key Vault :

1.  Activez la [journalisation des diagnostics pour les ressources de Key Vault](../key-vault/key-vault-logging.md) à analyser.
2.  Configurez Log Analytics pour lire les journaux du Stockage Blob en suivant la procédure décrite dans [Fichiers JSON dans le Stockage Blob](../log-analytics/log-analytics-azure-storage-json.md).
3.  Activez la solution Azure Key Vault en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).  

## <a name="review-azure-key-vault-data-collection-details"></a>Examiner les détails de la collecte de données par Azure Key Vault

La solution Azure Key Vault collecte des journaux de diagnostics à partir du Stockage Blob Azure pour Azure Key Vault.
Aucun agent n’est requis pour la collecte de données.

Le tableau suivant présente les méthodes de collecte des données et d’autres détails sur le mode de collecte de données pour Azure Key Vault.

| Plateforme | Agent direct | Agent Systems Center Operations Manager (SCOM) | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | Fréquence de collecte |
|---|---|---|---|---|---|---|
|Microsoft Azure|![Non](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Non](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Oui](./media/log-analytics-azure-keyvault/oms-bullet-green.png)|            ![Non](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Non](./media/log-analytics-azure-keyvault/oms-bullet-red.png)| 10 minutes|

## <a name="use-azure-key-vault"></a>Utiliser Azure Key Vault

Après avoir installé la solution, vous pouvez afficher la synthèse des états de demande dans le temps pour vos Key Vault analysés à l’aide de la vignette **Azure Key Vault** sur la page **Présentation** de Log Analytics.

![Image de la vignette Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Après avoir cliqué sur la vignette **Présentation**, vous pouvez consulter des récapitulatifs de vos journaux et en approfondir des détails pour les catégories suivantes :

- Volume de toutes les opérations de Key Vault dans le temps
- Volumes des opérations en échec dans le temps
- Latence opérationnelle moyenne par opération
- Qualité de service des opérations, avec le nombre d’opérations qui prennent plus de 1 000 ms et la liste de ces opérations

![Image du tableau de bord d’Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Image du tableau de bord d’Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Pour afficher les détails d’une opération

1. Dans la page **Présentation**, cliquez sur la vignette **Azure Key Vault**.
2. Sur le tableau de bord **Azure Key Vault**, examinez les informations résumées dans l’un des panneaux, puis cliquez sur l’une d’elles pour afficher des informations détaillées dans la page Recherche de journal.

    Sur l’une des pages de recherche de journal, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherches de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.

## <a name="log-analytics-records"></a>Enregistrements Log Analytics

La solution Azure Key Vault analyse les enregistrements de type **KeyVaults** qui sont collectés à partir des [journaux AuditEvent](..\key-vault\key-vault-logging.md) dans les Diagnostics Azure.  Les propriétés de ces enregistrements figurent dans le tableau suivant.  

| Propriété | Description |
|:--|:--|
| Type | *KeyVaults* |
| SourceSystem | *AzureStorage* |
| callerIpAddress | Adresse IP du client qui a effectué la demande. |
| Catégorie      | Pour les journaux de coffre de clés, AuditEvent est la seule valeur disponible.|
| CorrelationId | GUID facultatif que le client peut transférer pour mettre en corrélation les journaux côté client avec les journaux côté service (Key Vault). |
| DurationMs | Délai nécessaire pour répondre à la demande API REST, en millisecondes. La latence du réseau n’étant pas incluse dans ce chiffre, le temps mesuré côté client peut ne pas correspondre à cette durée. |
| HttpStatusCode_d | Code d’état HTTP retourné par la demande. |
| Id_s       | ID unique de la demande. |
| Identity_o | Identité issue du jeton qui a été présenté lors de la création de la demande de l’API REST. Il s’agit généralement d’un « utilisateur », d’« un principal de service » ou d’une combinaison « utilisateur + appId », comme dans le cas d’une demande résultant d’une applet de commande Azure PowerShell. |
| Nom d'opération      | Nom de l’opération, comme décrit dans [journalisation d’Azure Key Vault](..\key-vault\key-vault-logging.md)|
| operationVersion      | Version d’API REST demandée par le client.|
| RemoteIPLatitude | Latitude du client qui a effectué la demande. |
| RemoteIPLongitude | Longitude du client qui a effectué la demande. |
| RemoteIPCountry | Pays du client qui a effectué la demande.  |
| RequestUri_s | URI de la demande. |
| Ressource   | Nom du Key Vault. |
| ResourceGroup | Groupe de ressources du Key Vault. |
| ResourceId | ID de ressource Azure Resource Manager Pour les journaux de coffre de clés, il s’agit toujours de l’ID de ressource du coffre de clés. |
| ResourceProvider | *MICROSOFT.KEYVAULT* |
| ResultSignature  | État HTTP.|
| ResultType      | Résultat de la demande d’API REST.|
| SubscriptionId | ID de l’abonnement Azure contenant le Key Vault. |


## <a name="next-steps"></a>Étapes suivantes

- Utilisez les [recherches de journal dans Log Analytics](log-analytics-log-searches.md) pour afficher les données détaillées de Azure Key Vault.



<!--HONumber=Oct16_HO2-->


