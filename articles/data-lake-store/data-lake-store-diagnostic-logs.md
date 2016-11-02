<properties 
   pageTitle="Affichage des journaux de diagnostic d’Azure Data Lake Store | Microsoft Azure" 
   description="Comprendre comment configurer les journaux de diagnostic et y accéder pour Azure Data Lake Store " 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/19/2016"
   ms.author="nitinme"/>

# Accès aux journaux de diagnostic d’Azure Data Lake Store

Découvrez comment activer la journalisation de diagnostic pour votre compte Data Lake Store et comment afficher les journaux collectés pour votre compte.

Les organisations peuvent activer la journalisation de diagnostic pour leur compte Azure Data Lake Store afin de collecter des pistes d’audit d’accès aux données qui fournissent des informations telles que la liste des utilisateurs qui accèdent aux données, la fréquence à laquelle les données sont consultées, la quantité de données stockée sur le compte, etc.

## Composants requis

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Activez votre abonnement Azure** pour la version d’évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- **Compte Azure Data Lake Store**. Suivez les instructions de [Prise en main d'Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md).

## Activer la journalisation de diagnostic pour votre compte Data Lake Store

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).

2. Ouvrez votre compte Data Lake Store et dans le panneau de votre compte Data Lake Store, cliquez sur **Paramètres**, puis cliquez sur **Paramètres de diagnostic**.

3. Dans le panneau **Diagnostic**, apportez les modifications suivantes pour configurer la journalisation de diagnostic.

	![Activation de la journalisation de diagnostic](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Activer la journalisation des diagnostics")

	* Définissez **État** sur **Activé** pour activer la journalisation de diagnostic.
	* Vous pouvez choisir de stocker/traiter les données de deux manières différentes.
		* Sélectionnez l’option **Export to Event Hub** (Exporter vers un concentrateur d’événements) pour transmettre les données de journal à un concentrateur d’événements Azure. Vous allez probablement utiliser cette option si vous disposez d’un pipeline de traitement en aval pour analyser les journaux entrants en temps réel. Si vous sélectionnez cette option, vous devez fournir les informations relatives au concentrateur d’événements Azure que vous souhaitez utiliser.
		* Sélectionnez l’option **Export to Storage Account** (Exporter vers le compte de stockage) pour stocker les journaux sur un compte de stockage Azure. Utilisez cette option si vous souhaitez archiver les données qui seront traitées par lots à une date ultérieure. Si vous sélectionnez cette option, vous devez fournir un compte de stockage Azure sur lequel enregistrer les journaux.
	* Spécifiez si vous souhaitez obtenir des journaux d’audit ou des journaux de demande ou les deux.
	* Spécifiez le nombre de jours pendant lesquels les données doivent être conservées.
	* Cliquez sur **Save**.

Une fois que vous avez activé les paramètres de diagnostic, vous pouvez consulter les journaux dans l’onglet **Journaux de diagnostic**.

## Afficher les journaux de diagnostic de votre compte Data Lake Store

Il existe deux manières d’afficher les données de journal de votre compte Data Lake Store :

* à partir de la vue des paramètres Data Lake Store ;
* à partir du compte Azure Storage dans lequel les données sont stockées.

### Utilisation de la vue des paramètres Data Lake Store

1. Dans le panneau **Paramètres** de votre compte Data Lake Store, cliquez sur **Journaux de diagnostic**.

	![Afficher la journalisation de diagnostic](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Afficher les journaux de diagnostic")

2. Dans le panneau **Journaux de diagnostic**, vous devez voir les journaux classés par **journaux d’audit** et **journaux de demande**.
	* Les journaux de demande capturent chaque demande d’API effectuée sur le compte Data Lake Store.
	* Les journaux d’audit sont similaires aux journaux de demande, mais ils fournissent une analyse beaucoup plus détaillée des opérations effectuées sur le compte Data Lake Store. Par exemple, un simple appel d’API de chargement dans les journaux de demande peut entraîner plusieurs opérations « Ajouter » dans les journaux d’audit.

3. Cliquez sur le lien **Télécharger** en regard de chaque entrée de journal pour le télécharger.

### À partir du compte Azure Storage qui contient des données de journal

1. Ouvrez le panneau du compte Azure Storage associé au Data Lake Store pour la journalisation, puis cliquez sur Objets blob. Le panneau **Service Blob** répertorie deux conteneurs.

	![Afficher la journalisation de diagnostic](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Afficher les journaux de diagnostic")

	* Le conteneur **insights-logs-audit** contient les journaux d’audit.
	* Le conteneur **insights-logs-requests** contient les journaux de demande.

2. Les journaux sont stockés dans ces conteneurs selon la structure suivante.

	![Afficher la journalisation de diagnostic](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Afficher les journaux de diagnostic")

	Par exemple, le chemin d’accès complet à un journal d’audit peut être `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

	De même, le chemin d’accès complet à un journal de demande peut être `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## Comprendre la structure des données de journal

Les journaux d’audit et de demande sont au format JSON. Dans cette section, nous examinons la structure JSON des journaux de demande et d’audit.

### Journaux de demande

Voici un exemple d’entrée dans le journal de demande au format JSON. Chaque objet blob a un objet racine appelé **enregistrements** qui contient un tableau d’objets du journal.

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-07T21:02:53.456Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
			 "category": "Requests",
			 "operationName": "GETCustomerIngressEgress",
			 "resultType": "200",
			 "callerIpAddress": "::ffff:1.1.1.1",
			 "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
			 "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
			 "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
		}
		,
		. . . .
	  ]
	}

#### Schéma du journal de requête

| Name | Type | Description |
|-----------------|--------|--------------------------------------------------------------------------------|
| time | Chaîne | L’horodatage (heure UTC) du journal. |
| resourceId | Chaîne | L’ID de la ressource sur laquelle l’opération a eu lieu. |
| category | Chaîne | La catégorie du journal. Par exemple, **Demandes**. |
| operationName | Chaîne | Le nom de l’opération qui est journalisée. Par exemple, getfilestatus. |
| resultType | Chaîne | L’état de l’opération. Par exemple, 200. |
| callerIpAddress | Chaîne | L’adresse IP du client qui a effectué la demande. |
| correlationId | String | L’ID du journal qui peut être utilisé pour regrouper un ensemble d’entrées de journal associées. |
| identité | Object | L’identité qui a généré le journal. |
| properties | JSON | Voir les détails ci-dessous. |

#### Schéma des propriétés de journal de demande

| Name | Type | Description |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod | Chaîne | La méthode HTTP utilisée pour l’opération. Par exemple, GET. |
| Chemin | Chaîne | Le chemin d’accès vers l’emplacement où l’opération a eu lieu. |
| RequestContentLength | int | La longueur du contenu de la demande HTTP. |
| ClientRequestId | Chaîne | L’ID qui identifie de façon unique la demande. |
| StartTime | Chaîne | L’heure à laquelle le serveur a reçu la demande. |
| EndTime | Chaîne | L’heure à laquelle le serveur a envoyé une réponse. |

### Journaux d’audit

Voici un exemple d’entrée dans le journal d’audit au format JSON. Chaque objet blob a un objet racine appelé **records** qui contient un tableau d’objets du journal

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-08T19:08:59.359Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
			 "category": "Audit",
			 "operationName": "SeOpenStream",
			 "resultType": "0",
			 "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
			 "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
			 "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
		}
		,
		. . . .
	  ]
	}

#### Schéma du journal d’audit

| Name | Type | Description |
|-----------------|--------|--------------------------------------------------------------------------------|
| time | Chaîne | L’horodatage (heure UTC) du journal. |
| resourceId | Chaîne | L’ID de la ressource sur laquelle l’opération a eu lieu. |
| category | Chaîne | La catégorie du journal. Par exemple, **Audit**. |
| operationName | Chaîne | Le nom de l’opération qui est journalisée. Par exemple, getfilestatus. |
| resultType | Chaîne | L’état de l’opération. Par exemple, 200. |
| correlationId | String | L’ID du journal qui peut être utilisé pour regrouper un ensemble d’entrées de journal associées. |
| identité | Object | L’identité qui a généré le journal. |
| properties | JSON | Voir les détails ci-dessous. |

#### Schéma des propriétés de journal d’audit

| Name | Type | Description |
|------------|--------|------------------------------------------|
| StreamName | String | Le chemin d’accès vers l’emplacement où l’opération a eu lieu. |


## Exemples de traitement des données de journal

Azure Data Lake Store fournit un exemple de traitement et d’analyse des données de journal. Vous trouverez l’exemple à l’adresse [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).


## Voir aussi

- [Présentation d'Azure Data Lake Store](data-lake-store-overview.md)
- [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0914_2016-->