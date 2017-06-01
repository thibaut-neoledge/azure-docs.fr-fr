---
title: "Affichage des journaux de diagnostic d’Azure Data Lake Analytics | Microsoft Docs"
description: "Comprendre comment configurer les journaux de diagnostic et y accéder pour Azure Data Lake Analytics  "
services: data-lake-analytics
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 71a839d9c076d33c404a56bf0544666dc86eaa4d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Accès aux journaux de diagnostic d’Azure Data Lake Analytics

Découvrez comment activer la journalisation de diagnostic pour votre compte Data Lake Analytics et comment afficher les journaux collectés pour votre compte.

Les organisations peuvent activer la journalisation de diagnostic pour leur compte Azure Data Lake Analytics, afin de collecter des pistes d’audit d’accès aux données. Ces journaux fournissent des informations comme :

* Une liste des utilisateurs qui ont accédé aux données.
* La fréquence à laquelle les données sont consultées.
* La quantité de données stockées dans le compte.

## <a name="enable-logging"></a>Activation de la journalisation

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).

2. Ouvrez votre compte Data Lake Analytics et sélectionnez **Journaux de Diagnostic** dans la __section Surveillance__. Ensuite, sélectionnez __Activer les diagnostics__.

    ![Activer les diagnostics pour collecter des journaux d’audit et de requêtes](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Dans les __Paramètres de diagnostic__, définissez le statut sur __Activé__ et sélectionnez les options de journalisation.

    ![Activer les diagnostics pour collecter des journaux d’audit et de requêtes](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Activer les journaux de diagnostic")

   * Définissez **État** sur **Activé** pour activer la journalisation de diagnostic.

   * Vous pouvez choisir de stocker/traiter les données de deux manières différentes.

     * Sélectionnez __Archive to a storage account (Archiver dans un compte de stockage)__ pour stocker les journaux dans un compte de stockage Azure. Utilisez cette option si vous souhaitez archiver les données. Si vous sélectionnez cette option, vous devez fournir un compte de stockage Azure dans lequel enregistrer les journaux.

     * Sélectionnez **Stream to an Event hub (Transmettre à un Event Hub)** pour transmettre les données journalisées à un Event Hub Azure. Utilisez cette option si vous disposez d’un pipeline de traitement en aval pour analyser les journaux entrants en temps réel. Si vous sélectionnez cette option, vous devez fournir les informations relatives au Event Hub Azure que vous souhaitez utiliser.

     * Sélectionnez __Send to Log Analytics (Envoyer à Log Analytics)__ pour envoyer les données au service Log Analytics. Utilisez cette option si vous souhaitez utiliser Log Analytics pour collecter et analyser les journaux.
   * Spécifiez si vous souhaitez obtenir des journaux d’audit ou des journaux de demande ou les deux.

   * Spécifiez le nombre de jours pendant lesquels les données doivent être conservées.

   * Cliquez sur **Save**.

        > [!NOTE]
        > Vous devez sélectionner __Archive to a storage account (Archiver dans un compte de stockage)__, __Stream to an Event hub (Transmettre à un Event Hub)__ ou __Send to Log Analytics (Envoyer à Log Analytics)__ avant de cliquer sur le bouton __Enregistrer__.

Une fois que vous avez activé les paramètres de diagnostic, vous pouvez revenir au panneau __Journaux de diagnostic__ pour afficher les journaux.

## <a name="view-logs"></a>afficher les journaux ;

Il existe deux manières d’afficher les données de journal de votre compte Data Lake Analytics :

* À partir des paramètres de compte Data Lake Analytics
* À partir du compte de Stockage Azure dans lequel les données sont stockées

### <a name="using-the-data-lake-analytics-settings-view"></a>Utilisation de la vue des paramètres Data Lake Analytics

1. Dans le volet de votre compte Data Lake Analytics, sélectionnez **Journaux de diagnostic**, puis sélectionnez l’entrée pour laquelle afficher les journaux.

    ![Afficher la journalisation des diagnostics](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Afficher les journaux de diagnostic")

2. Les journaux sont classés par **journaux d’audit** et **journaux de requêtes**.

    ![entrées de journal](./media/data-lake-analytics-diagnostic-logs/diagnostic-log-entries.png)

   * Les journaux de demande capturent chaque demande d’API effectuée sur le compte Data Lake Analytics.
   * Les journaux d’audit sont similaires aux journaux de demande, mais ils fournissent une analyse beaucoup plus détaillée des opérations effectuées sur le compte Data Lake Analytics. Par exemple, un simple appel d’API de chargement dans les journaux de demande peut entraîner plusieurs opérations « Ajouter » dans les journaux d’audit.

3. Cliquez sur le lien **Télécharger** d’une entrée de journal pour le télécharger.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>À partir du compte de Stockage Azure qui contient des données de journal

1. Ouvrez le panneau du compte de Stockage Azure associé à Data Lake Analytics pour la journalisation, puis cliquez sur Objets blob. Le panneau **Service Blob** répertorie deux conteneurs.

    ![Afficher la journalisation des diagnostics](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Afficher les journaux de diagnostic")

   * Le conteneur **insights-logs-audit** contient les journaux d’audit.
   * Le conteneur **insights-logs-requests** contient les journaux de demande.
2. Les journaux sont stockés dans ces conteneurs selon la structure suivante.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > Le panneau `##` dans le chemin d’accès contiennent l’année, le mois, le jour et l’heure auxquels le journal a été créé. Data Lake Analytics crée un fichier toutes les heures, par conséquent, `m=` contient toujours une valeur de `00`.

    Par exemple, le chemin d’accès complet à un journal d’audit peut être :

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    De même, le chemin d’accès complet à un journal de demande peut être :

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Structure journal

Les journaux d’audit et de demande sont au format JSON. Dans cette section, nous examinons la structure JSON des journaux de demande et d’audit.

### <a name="request-logs"></a>journaux de demande

Voici un exemple d’entrée dans le journal de demande au format JSON. Chaque objet blob a un objet racine appelé **enregistrements** qui contient un tableau d’objets du journal.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Schéma du journal de requête

| Name | Type | Description |
| --- | --- | --- |
| time |Chaîne |L’horodatage (heure UTC) du journal. |
| resourceId |Chaîne |L’ID de la ressource sur laquelle l’opération a eu lieu. |
| category |Chaîne |La catégorie du journal. Par exemple, **Demandes**. |
| operationName |Chaîne |Le nom de l’opération qui est journalisée. Par exemple, GetAggregatedJobHistory. |
| resultType |Chaîne |L’état de l’opération. Par exemple, 200. |
| callerIpAddress |Chaîne |L’adresse IP du client qui a effectué la demande. |
| correlationId |Chaîne |L’ID du journal. Cette valeur peut être utilisée pour regrouper un ensemble d’entrées de journal associées |
| identité |Object |L’identité qui a généré le journal. |
| properties |JSON |Consultez la section suivante (Schéma des propriétés de journal de demande) pour plus d’informations |

#### <a name="request-log-properties-schema"></a>Schéma des propriétés de journal de demande

| Name | Type | Description |
| --- | --- | --- |
| HttpMethod |Chaîne |La méthode HTTP utilisée pour l’opération. Par exemple, GET. |
| Chemin |Chaîne |Le chemin d’accès vers l’emplacement où l’opération a eu lieu. |
| RequestContentLength |int |La longueur du contenu de la demande HTTP. |
| ClientRequestId |Chaîne |L’ID qui identifie de façon unique la demande. |
| StartTime |Chaîne |L’heure à laquelle le serveur a reçu la demande. |
| EndTime |Chaîne |L’heure à laquelle le serveur a envoyé une réponse. |

### <a name="audit-logs"></a>Journaux d’audit

Voici un exemple d’entrée dans le journal d’audit au format JSON. Chaque objet blob a un objet racine appelé **records** qui contient un tableau d’objets du journal

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schéma du journal d’audit

| Name | Type | Description |
| --- | --- | --- |
| time |Chaîne |L’horodatage (heure UTC) du journal. |
| resourceId |Chaîne |L’ID de la ressource sur laquelle l’opération a eu lieu. |
| category |Chaîne |La catégorie du journal. Par exemple, **Audit**. |
| operationName |Chaîne |Le nom de l’opération qui est journalisée. Par exemple, JobSubmitted. |
| resultType |Chaîne |Un sous-état de l’état de la tâche (operationName). |
| resultSignature |Chaîne |Informations supplémentaires sur l’état de la tâche (operationName). |
| identité |Chaîne |L’utilisateur qui a demandé l’opération. Par exemple, susan@contoso.com. |
| properties |JSON |Consultez la section suivante (Schéma des propriétés de journal d’audit) pour plus d’informations |

> [!NOTE]
> **resultType** et **resultSignature** fournissent des informations sur le résultat d’une opération et contiennent uniquement une valeur si une opération est terminée. Par exemple, ils contiennent une valeur quand **operationName** contient une valeur de **JobStarted** ou **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schéma des propriétés de journal d’audit

| Name | Type | Description |
| --- | --- | --- |
| JobId |Chaîne |L’ID affecté à la tâche. |
| JobName |Chaîne |Le nom fourni pour la tâche. |
| JobRunTime |Chaîne |Le runtime utilisé pour traiter la tâche. |
| SubmitTime |Chaîne |L’heure (UTC) à laquelle la tâche a été envoyée. |
| StartTime |Chaîne |L’heure à laquelle l’exécution de la tâche a commencé après la soumission (UTC). |
| EndTime |Chaîne |L’heure à laquelle la tâche s’est terminée. |
| Parallélisme |Chaîne |Le nombre d’unités Data Lake Analytics demandées pour cette tâche lors de l’envoi. |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime** et **Parallelism** fournissent des informations sur une opération et contiennent uniquement une valeur si une opération a démarré ou s’est terminée. Par exemple, **SubmitTime** contient une valeur après **qu’operationName** indique **JobSubmitted**.

## <a name="process-the-log-data"></a>Traiter les données de journal

Azure Data Lake Analytics fournit un exemple de traitement et d’analyse des données de journal. Vous trouverez l’exemple à l’adresse [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Data Lake Analytics](data-lake-analytics-overview.md)

