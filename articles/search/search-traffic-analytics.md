---
title: "Fonctionnalité Rechercher l’analyse du trafic pour la Recherche Azure | Microsoft Docs"
description: "Activez la recherche de l’analyse du trafic pour Azure Search, un service de recherche hébergé dans le cloud sur Microsoft Azure pour dévoiler des informations sur vos utilisateurs et vos données."
services: search
documentationcenter: 
author: bernitorres
manager: pablocas
editor: 
ms.assetid: b31d79cf-5924-4522-9276-a1bb5d527b13
ms.service: search
ms.devlang: multiple
ms.workload: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: betorres
translationtype: Human Translation
ms.sourcegitcommit: ebfed89674dc132bd5d93f34a8b5ed5ab12bd73e
ms.openlocfilehash: cd6c1d094f8601a06642a2a84ef1234a29a8f058

---

# <a name="enabling-and-using-search-traffic-analytics"></a>Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic
La fonctionnalité Rechercher l’analyse du trafic disponible dans Azure Search vous permet de gagner en visibilité dans votre service de recherche et de dévoiler des informations sur les utilisateurs et leur comportement. Lorsque vous activez cette fonctionnalité, vos données de service de recherche sont copiées vers le compte de stockage de votre choix. Ces données incluent vos journaux du service de recherche et les mesures opérationnelles agrégées que vous pouvez traiter et manipuler pour une analyse plus poussée.

## <a name="how-to-enable-search-traffic-analytics"></a>Activation de la fonctionnalité Rechercher l’analyse du trafic
Vous aurez besoin d’un compte de stockage situé dans la même région et le même abonnement que votre service de recherche.

> [!IMPORTANT]
> Des frais standard s’appliquent à ce compte de stockage.
>
>

Vous pouvez activer la recherche de l’analyse du trafic sur le portail ou via PowerShell. Une fois activé, les données commencent à circuler vers votre compte de stockage en 5 à 10 minutes dans ces deux conteneurs d’objets blob :

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


### <a name="a-using-the-portal"></a>A. Utiliser le portail
Ouvrez votre service de recherche Azure dans le [portail Azure](http://portal.azure.com). L’option Rechercher l’analyse du trafic est disponible sous Paramètres.

![][1]

Définissez l’état sur **Activé**, sélectionnez le compte de Stockage Azure à utiliser et choisissez les données à copier : journaux et/ou mesures. Nous vous recommandons de copier les journaux et les mesures.
Vous pouvez définir la stratégie de rétention de vos données entre 1 et 365 jours. Si vous ne souhaitez pas conserver les données indéfiniment, définissez la durée de rétention (jours) sur 0.

![][2]

### <a name="b-using-powershell"></a>B. Utiliser PowerShell
Tout d’abord, assurez-vous que les derniers [applets de commande Azure PowerShell](https://github.com/Azure/azure-powershell/releases) sont installés.

Puis, munissez-vous des ID de ressource de votre service de recherche et de votre compte de stockage. Vous pouvez les trouver dans le portail en accédant à Paramètres -> Propriétés -> ResourceId.

![][3]

```PowerShell
Login-AzureRmAccount
$SearchServiceResourceId = "Your Search service resource id"
$StorageAccountResourceId = "Your Storage account resource id"
Set-AzureRmDiagnosticSetting -ResourceId $SearchServiceResourceId StorageAccountId $StorageAccountResourceId -Enabled $true
```

## <a name="understanding-the-data"></a>Vue d’ensemble des données
Les données sont stockées dans des objets blob Azure Storage au format JSON.

Il y a un seul objet blob par heure et par conteneur.

Exemple de chemin d’accès : `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### <a name="logs"></a>Journaux
Les objets blob de journaux contiennent les journaux du trafic de votre service de recherche.
Chaque objet blob a un objet racine appelé **enregistrements** qui contient un tableau d’objets du journal.
Chaque objet blob comporte des enregistrements relatifs à l’ensemble de l’opération qui s’est déroulée au cours de la même heure.

#### <a name="log-schema"></a>Schéma du journal
| Nom | Type | Exemple | Remarques |
| --- | --- | --- | --- |
| time |datetime |« 2015-12-07T00:00:43.6872559Z » |Horodatage de l’opération |
| resourceId |string |«/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE » |Votre ID de ressource |
| operationName |string |« Query.Search » |Nom de l’opération |
| operationVersion |string |"2016-09-01" |Version d’API utilisée |
| category |string |« OperationLogs » |constant |
| resultType |string |« Success » |Valeurs possibles : Réussite ou Échec |
| resultSignature |int |200 |Code de résultat HTTP |
| durationMS |int |50 |Durée de l’opération en millisecondes |
| properties |objet |consultez le tableau suivant |Objet contenant des données propres à l’opération |

#### <a name="properties-schema"></a>Schéma de propriétés
| Nom | Type | Exemple | Remarques |
| --- | --- | --- | --- |
| Description |string |« GET /indexes(’content’)/docs » |Point de terminaison de l’opération |
| Interroger |string |"?search=AzureSearch&$count=true&api-version=2016-09-01" |Paramètres de requête |
| Documents |int |42 |Nombre de documents traités |
| IndexName |string |« testindex » |Nom de l’index associé à l’opération |

### <a name="metrics"></a>Mesures
Les objets blob de mesures contiennent des valeurs agrégées pour votre service de recherche.
Chaque fichier a un seul objet racine appelé **records** qui contient un tableau d’objets de mesure. Cet objet racine contient les mesures de chaque minute pendant laquelle les données étaient disponibles.

Mesures disponibles :

* SearchLatency : délai nécessaire au service de recherche pour traiter les requêtes de recherche, agrégé par minute.
* SearchQueriesPerSecond : nombre de requêtes de recherche reçues par seconde, agrégé par minute.
* ThrottledSearchQueriesPercentage : pourcentage de requêtes de recherche qui ont été limitées, agrégé par minute.

> [!IMPORTANT]
> La limitation se produit lorsque trop de requêtes sont envoyées, épuisant ainsi la capacité de ressource configurée du service. Vous pouvez ajouter d’autres réplicas à votre service.
>
>

#### <a name="metrics-schema"></a>Schéma de mesures
| Nom | Type | Exemple | Remarques |
| --- | --- | --- | --- |
| resourceId |string |«/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE » |Votre ID de ressource |
| metricName |string |« Latency » |Nom de la mesure |
| time |datetime |« 2015-12-07T00:00:43.6872559Z » |Horodatage de l’opération |
| average |int |64 |Valeur moyenne des échantillons bruts dans l’intervalle de temps de la mesure |
| minimum |int |37 |Valeur minimale des échantillons bruts dans l’intervalle de temps de la mesure |
| maximum |int |78 |Valeur maximale des échantillons bruts dans l’intervalle de temps de la mesure |
| total |int |258 |Valeur totale des échantillons bruts dans l’intervalle de temps de la mesure |
| count |int |4 |Nombre d’échantillons bruts utilisés pour générer la mesure |
| timegrain |string |« PT1M » |Fragment de temps de la mesure au format ISO 8601 |

Toutes les mesures sont consignées dans des intervalles d’une minute. Chaque mesure expose des valeurs minimales, maximales et moyennes par minute.

Dans le cas de la mesure SearchQueriesPerSecond, la valeur minimale correspondra à la valeur la plus faible des requêtes de recherche par seconde qui a été enregistrée pendant cette minute. Il en va de même pour la valeur maximale. La moyenne représentera l’agrégat de ces valeurs pour toute la minute.
Imaginez ce scénario : pendant une minute, vous pouvez avoir 58 secondes de charge très élevée, qui représentera votre valeur SearchQueriesPerSecond maximale, puis 58 secondes de charge moyenne, et enfin une seconde avec une seule requête, qui représentera la valeur minimale.

Pour ThrottledSearchQueriesPercentage, les valeurs minimales, maximales, moyennes et totales seront identiques : il s’agit du pourcentage de requêtes de recherche qui ont été limitées, en fonction du nombre total de requêtes de recherche pendant une minute.

## <a name="analyzing-your-data"></a>Analyse de vos données
Les données sont situées dans votre propre compte de stockage et nous vous encourageons à les explorer de la manière qui vous convient le mieux.

Comme point de départ, nous vous recommandons d’utiliser [Power BI](https://powerbi.microsoft.com) pour explorer et visualiser vos données. Vous pouvez facilement vous connecter à votre compte de Stockage Azure et rapidement commencer à analyser vos données.

#### <a name="power-bi-online"></a>Power BI en ligne
[Pack de contenu Power BI](https://app.powerbi.com/getdata/services/azure-search): créez un tableau de bord Power BI et un ensemble de rapports Power BI qui affichent automatiquement vos données et fournissent un éclairage visuel sur votre service de recherche. Consultez la [page d’aide du pack de contenu](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-search/).

![][4]

#### <a name="power-bi-desktop"></a>Power BI Desktop
[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop) : explorez vos données et créez vos propres visualisations pour vos données. Consultez la requête de démarrage dans la section suivante :

1. Ouvrez un nouveau rapport Power BI Desktop.
2. Sélectionnez Obtention des données -> Plus...

    ![][5]
3. Sélectionnez Stockage d’objets blob Microsoft Azure et Se connecter.

    ![][6]
4. Entrez le nom et la clé de votre compte de stockage.
5. Sélectionnez « insight-journaux-operationlogs » et « insights-metrics-pt1m », puis cliquez sur Modifier
6. L’éditeur de requête s’ouvre, vérifiez que « insight-logs-operationlogs » est sélectionnée sur la gauche. Ouvrez maintenant l’éditeur avancé en sélectionnant Afficher -> Éditeur avancé

    ![][7]
7. Conservez les deux premières lignes et remplacez le reste par la requête suivante :

   > # <a name="insights-logs-operationlogs-sourcenameinsights-logs-operationlogsdata"></a>"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
   > # <a name="sorted-rows-tablesortinsights-logs-operationlogsdate-modified-orderdescending"></a>"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
   > # <a name="kept-first-rows-tablefirstnsorted-rows744"></a>"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # <a name="removed-columns-tableremovecolumnskept-first-rowsname-extension-date-accessed-date-modified-date-created-attributes-folder-path"></a>"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # <a name="parsed-json-tabletransformcolumnsremoved-columnsjsondocument"></a>"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # <a name="expanded-content-tableexpandrecordcolumnparsed-json-content-records-records"></a>"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # <a name="expanded-records-tableexpandlistcolumnexpanded-content-records"></a>"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # <a name="expanded-records1-tableexpandrecordcolumnexpanded-records-records-time-resourceid-operationname-operationversion-category-resulttype-resultsignature-durationms-properties-time-resourceid-operationname-operationversion-category-resulttype-resultsignature-durationms-properties"></a>"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
   > # <a name="expanded-properties-tableexpandrecordcolumnexpanded-records1-properties-description-query-indexname-documents-description-query-indexname-documents"></a>"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
   > # <a name="renamed-columns-tablerenamecolumnsexpanded-propertiestime-datetime-resourceid-resourceid-operationname-operationname-operationversion-operationversion-category-category-resulttype-resulttype-resultsignature-resultsignature-durationms-duration"></a>"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
   > # <a name="added-custom2-tableaddcolumnrenamed-columns-queryparameters-each-uripartshttptmp-query"></a>"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
   > # <a name="expanded-queryparameters-tableexpandrecordcolumnadded-custom2-queryparameters-query-query1"></a>"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
   > # <a name="expanded-query1-tableexpandrecordcolumnexpanded-queryparameters-query1-search-skip-top-count-api-version-searchmode-filter-search-skip-top-count-api-version-searchmode-filter"></a>"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
   > # <a name="removed-columns1-tableremovecolumnsexpanded-query1operationversion"></a>"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
   > # <a name="changed-type-tabletransformcolumntypesremoved-columns1datetime-type-datetimezone-resourceid-type-text-operationname-type-text-category-type-text-resulttype-type-text-resultsignature-type-text-duration-int64type-description-type-text-query-type-text-indexname-type-text-documents-int64type-search-type-text-skip-int64type-top-int64type-count-type-logical-api-version-type-text-searchmode-type-text-filter-type-text"></a>"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
   > # <a name="inserted-date-tableaddcolumnchanged-type-date-each-datetimedatedatetime-type-date"></a>"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
   > # <a name="duplicated-column-tableduplicatecolumninserted-date-resourceid-copy-of-resourceid"></a>"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
   > # <a name="split-column-by-delimiter-tablesplitcolumnduplicated-columncopy-of-resourceidsplittersplittextbyeachdelimiter-null-truecopy-of-resourceid1-copy-of-resourceid2"></a>"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
   > # <a name="changed-type1-tabletransformcolumntypessplit-column-by-delimitercopy-of-resourceid1-type-text-copy-of-resourceid2-type-text"></a>"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
   > # <a name="removed-columns2-tableremovecolumnschanged-type1copy-of-resourceid1"></a>"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
   > # <a name="renamed-columns1-tablerenamecolumnsremoved-columns2copy-of-resourceid2-servicename"></a>"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
   > # <a name="lowercased-text-tabletransformcolumnsrenamed-columns1servicename-textlower"></a>"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
   > # <a name="added-custom-tableaddcolumnlowercased-text-daysfromtoday-each-durationdaysdatetimezoneutcnow---datetime"></a>"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
   > # <a name="changed-type2-tabletransformcolumntypesadded-customdaysfromtoday-int64type"></a>"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
   > commencer
   >
   > # <a name="changed-type2"></a>"Changed Type2"
   >
8. Cliquez sur Terminé.
9. Sélectionnez maintenant « insights-metrics-pt1m » dans la liste de requêtes sur la gauche, puis ouvrez à nouveau l’éditeur avancé. Conservez les deux premières lignes et remplacez le reste par la requête suivante :

   > # <a name="insights-metrics-pt1m1-sourcenameinsights-metrics-pt1mdata"></a>"insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
   > # <a name="sorted-rows-tablesortinsights-metrics-pt1m1date-modified-orderdescending"></a>"Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
   > # <a name="kept-first-rows-tablefirstnsorted-rows744"></a>"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # <a name="removed-columns-tableremovecolumnskept-first-rowsname-extension-date-accessed-date-modified-date-created-attributes-folder-path"></a>"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # <a name="parsed-json-tabletransformcolumnsremoved-columnsjsondocument"></a>"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # <a name="expanded-content-tableexpandrecordcolumnparsed-json-content-records-records"></a>"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # <a name="expanded-records-tableexpandlistcolumnexpanded-content-records"></a>"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # <a name="expanded-records1-tableexpandrecordcolumnexpanded-records-records-resourceid-metricname-time-average-minimum-maximum-total-count-timegrain-resourceid-metricname-time-average-minimum-maximum-total-count-timegrain"></a>"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
   > # <a name="filtered-rows-tableselectrowsexpanded-records1-each-metricname-latency"></a>"Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
   > # <a name="removed-columns1-tableremovecolumnsfiltered-rowstimegrain"></a>"Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
   > # <a name="renamed-columns-tablerenamecolumnsremoved-columns1time-datetime-resourceid-resourceid-metricname-metricname-average-average-minimum-minimum-maximum-maximum-total-total-count-count"></a>"Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
   > # <a name="changed-type-tabletransformcolumntypesrenamed-columnsresourceid-type-text-metricname-type-text-datetime-type-datetimezone-average-type-number-minimum-int64type-maximum-int64type-total-int64type-count-int64type"></a>"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
   > Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
   >
   > # <a name="changed-type1-tabletransformcolumntypesroundingaverage-type-number"></a>"Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
   > # <a name="inserted-date-tableaddcolumnchanged-type1-date-each-datetimedatedatetime-type-date"></a>"Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
   > commencer
   >
   > # <a name="inserted-date"></a>"Inserted Date"
   >
10. Cliquez sur Terminé, puis sélectionnez Fermer et appliquer dans l’onglet Accueil.

11. Vos données pour les 30 derniers jours sont maintenant prêtes à être consommées. Continuez et créez quelques [visualisations](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-report-view/).

## <a name="next-steps"></a>Étapes suivantes
Découvrez plus en détail la syntaxe de recherche et les paramètres de requête. Pour plus d’informations, consultez la rubrique [Recherche de documents (API REST Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx) .

En savoir plus sur la création de rapports exceptionnels. Pour plus d’informations, consultez [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/).

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/ResourceId.png
[4]: ./media/search-traffic-analytics/Dashboard.png
[5]: ./media/search-traffic-analytics/GetData.png
[6]: ./media/search-traffic-analytics/BlobStorage.png
[7]: ./media/search-traffic-analytics/QueryEditor.png



<!--HONumber=Nov16_HO3-->


