<properties 
	pageTitle="Fonctionnalité Rechercher l’analyse du trafic pour Azure Search | Microsoft Azure" 
	description="Activez la recherche de l’analyse du trafic pour Azure Search, un service de recherche hébergé dans le cloud sur Microsoft Azure pour dévoiler des informations sur vos utilisateurs et vos données." 
	services="search" 
	documentationCenter="" 
	authors="bernitorres" 
	manager="pablocas" 
	editor=""
/>

<tags 
	ms.service="search" 
	ms.devlang="multiple" 
	ms.workload="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="12/11/2015" 
	ms.author="betorres"
/>


# Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic #

La fonctionnalité Rechercher l’analyse du trafic disponible dans Azure Search vous permet de gagner en visibilité dans votre service de recherche et de dévoiler des informations sur les utilisateurs et leur comportement. Lorsque vous activez cette fonctionnalité, vos données de service de recherche sont copiées vers le compte de stockage de votre choix. Ces données incluent vos journaux du service de recherche et les mesures opérationnelles agrégées. Une fois vos données d’utilisation disponibles, vous pouvez les traiter et les manipuler à votre guise.


## Activation de la fonctionnalité Rechercher l’analyse du trafic ##

### 1\. Utiliser le portail ###
Ouvrez votre service Azure Search dans le [portail Azure](http://portal.azure.com). L’option Rechercher l’analyse du trafic est disponible sous Paramètres.

![][1]

Sélectionnez-la pour ouvrir un nouveau panneau. Définissez l’état sur **Activé**, sélectionnez le compte Azure Storage dans lequel sont copiées vos données et choisissez les données à copier : journaux et/ou mesures. Nous vous recommandons de copier les journaux et les mesures.

![][2]


> [AZURE.IMPORTANT]Le compte de stockage doit être situé dans la même région et le même abonnement que votre service de recherche.
> 
> Des frais standard s’appliquent à ce compte de stockage.

### 2\. Utiliser PowerShell ###

Vous pouvez également activer cette fonctionnalité en exécutant les applets de commande PowerShell suivantes.

```PowerShell
Login-AzureRmAccount
Set-AzureRmDiagnosticSetting -ResourceId <SearchService ResourceId> StorageAccountId <StorageAccount ResourceId> -Enabled $true
```

-   **SearchService ResourceId** : ```
/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Search/searchServices/<searchServiceName>
```

 
-  **StorageAccount ResourceId** : cet élément est disponible dans le portail, sous Paramètres -> Propriétés -> ResourceId ```
New: /subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>
OR
Classic: /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ClassicStorage/storageAccounts/<storageAccountName>
```

----------

Une fois activé, les données commencent à circuler vers votre compte de stockage en 5 à 10 minutes. Deux nouveaux conteneurs apparaissent dans votre stockage d’objets Blob Storage :

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


## Vue d’ensemble des données ##

Les données sont stockées dans des objets blob Azure Storage au format JSON.

Il y a un seul objet blob par heure et par conteneur.
  
Exemple de chemin d’accès : `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### Journaux ###

Les objets blob de journaux contiennent les journaux du trafic de votre service de recherche.

Chaque objet blob a un objet racine appelé **records** qui contient un tableau d’objets du journal

####Schéma du journal####

Nom |Type |Exemple |Remarques 
------|-----|----|-----
time |datetime |« 2015-12-07T00:00:43.6872559Z » |Horodatage de l’opération
resourceId |string |« /SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE » |Votre ID de ressource
operationName |string |« Query.Search » |Nom de l’opération
operationVersion |string |« 2015-02-28 »|Version d’API utilisée
category |string |« OperationLogs » |constant 
resultType |string |« Success » |Valeurs possibles : Réussite ou Échec 
resultSignature |int |200 |Code de résultat HTTP 
durationMS |int |50 |Durée de l’opération en millisecondes 
properties |objet |voir ci-dessous |Objet contenant des données propres à l’opération

####Schéma de propriétés####

|Nom |Type |Exemple |Remarques|
|------|-----|----|-----|
|Description|string |« GET /indexes(’content’)/docs » |Point de terminaison de l’opération |
|Interroger |string |« ?search=AzureSearch&$count=true&api-version=2015-02-28 » |Paramètres de requête |
|Documents |int |42 |Nombre de documents traités|
|IndexName |string |« testindex »|Nom de l’index associé à l’opération |

### Mesures ###

Les objets blob de mesures contiennent des valeurs agrégées pour votre service de recherche. Chaque fichier a un seul objet racine appelé **records** qui contient un tableau d’objets de mesure

Mesures disponibles :

- Latence

####Schéma de mesures####

|Nom |Type |Exemple |Remarques|
|------|-----|----|-----|
|resourceId |string |« /SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE » |Votre ID de ressource |
|metricName |string |« Latency » |Nom de la mesure |
|time|datetime |« 2015-12-07T00:00:43.6872559Z » |Horodatage de l’opération |
|average |int |64|Valeur moyenne des échantillons bruts dans l’intervalle de temps de la mesure |
|minimum |int |37 |Valeur minimale des échantillons bruts dans l’intervalle de temps de la mesure |
|maximum |int |78 |Valeur maximale des échantillons bruts dans l’intervalle de temps de la mesure |
|total |int |258 |Valeur totale des échantillons bruts dans l’intervalle de temps de la mesure |
|count |int |4 |Nombre d’échantillons bruts utilisés pour générer la mesure |
|timegrain |string |« PT1M » |Fragment de temps de la mesure au format ISO 8601|

## Analyse de vos données ##

Les données sont situées dans votre propre compte de stockage et nous vous encourageons à les explorer de la manière qui vous convient le mieux.

Comme point de départ, nous vous recommandons d’utiliser [Power BI Desktop](https://powerbi.microsoft.com/fr-FR/desktop) pour explorer et visualiser vos données. Vous pouvez facilement vous connecter à votre compte Azure Storage et rapidement commencer à analyser vos données.

Consultez l’exemple de requête suivant qui permet de créer vos propres rapports dans Power BI Desktop.

### Instructions ###

1. Ouvrez un nouveau rapport Power BI Desktop.
2. Sélectionnez Obtention des données -> Plus...

	![][3]

3. Sélectionnez Stockage d’objets blob Microsoft Azure et Se connecter.

	![][4]

4. Entrez le nom et la clé de votre compte de stockage.
5. Cliquez avec le bouton droit sur insight-logs-operationlogs et sélectionnez Charger.
6. L’éditeur de requête s’ouvre. Ouvrez maintenant l’éditeur avancé en sélectionnant Afficher -> Éditeur avancé

	![][5]

7. Conservez les deux premières lignes et remplacez le reste par la requête suivante :

	>     #"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
	>     #"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
	>     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",288),
	>     #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
	>     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
	>     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
	>     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
	>     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
	>     #"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
	>     #"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
	>     #"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
	>     #"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
	>     #"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
	>     #"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
	>     #"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
	>     #"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
	>     #"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
	>     #"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
	>     #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
	>     #"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
	>     #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
	>     #"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
	>     #"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
	>     #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
	>     in
	>     #"Changed Type2"

8. Cliquez sur Terminé, puis sélectionnez Fermer & appliquer dans l’onglet Accueil.

9. Vos données sont maintenant prêtes à être utilisées. Continuez et créez quelques [visualisations](https://powerbi.microsoft.com/fr-FR/documentation/powerbi-desktop-report-view/).

## Étapes suivantes ##

Découvrez plus en détail la syntaxe de recherche et les paramètres de requête. Pour plus d’informations, consultez la rubrique [Recherche de documents (API REST Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

En savoir plus sur la création de rapports exceptionnels. Pour en savoir plus, consultez la rubrique [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/fr-FR/documentation/powerbi-desktop-getting-started/).

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/GetData.png
[4]: ./media/search-traffic-analytics/BlobStorage.png
[5]: ./media/search-traffic-analytics/QueryEditor.png

<!---HONumber=AcomDC_1217_2015-->