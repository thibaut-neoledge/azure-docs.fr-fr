<properties 
	pageTitle="Définition d’entrées | Microsoft Azure" 
	description="Présentation des entrées Stream Analytics" 
	keywords="big data analytics,cloud service,internet of things,managed service,stream processing,streaming analytics,streaming data"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# Présentation des entrées Stream Analytics

Les entrées Azure Stream Analytics sont définies en tant que connexion à une source de données. Stream Analytics propose une intégration de pointe aux sources Azure Event Hub et au stockage d’objets blob externes et internes à l’abonnement Azure dans lequel votre tâche est exécutée. Comme les données sont transmises à la source de données, elles sont utilisées par la tâche Stream Analytics et traitées en temps réel. Les entrées sont divisées en deux types distincts : les entrées de flux de données et les entrées de données de référence.

## Entrées de flux de données

Un flux de données est une séquence illimitée d’événements arrivant au fil du temps. Les tâches Stream Analytics doivent contenir au moins une entrée de flux de données qui sera traitée et transformée par la tâche. Le stockage d’objets blob Azure et Azure Event Hubs sont pris en charge en tant que sources d’entrée de flux de données. Les hubs d'événements Azure sont utilisés pour collecter des flux d'événements à partir de plusieurs appareils et services, comme par exemple les flux d'activités sur les réseaux sociaux, des informations boursière ou des données de capteurs. Le stockage d’objets blob Azure peut être utilisé comme source d’entrée pour la réception de données en bloc en tant que flux.

## Entrées de données de référence

Stream Analytics prend en charge un second type d'entrée : les données de référence. Il s’agit de données auxiliaires, statiques ou variant lentement au fil du temps, qui sont généralement utilisées pour effectuer des corrélations et des recherches. Le stockage d’objets blob Azure est la seule source d’entrée prise en charge pour les données de référence. Les objets blob de source de données de référence sont limités à une taille de 50 Mo.

Pour savoir comment créer des entrées de données de référence, consultez [Utiliser les données de référence](./articles/stream-analytics-use-reference-data.md).

## Création d’un flux de données d’entrée de hub d’événements

[Event Hubs](https://azure.microsoft.com/services/event-hubs/) est un service de réception d’événements de publication/d’abonnement hautement évolutif. Il peut collecter des millions d’événements par seconde afin que vous puissiez traiter et analyser les grandes quantités de données générées par vos appareils connectés et vos applications. Il s’agit des entrées les plus couramment utilisées pour Stream Analytics. Event Hubs et Stream Analytics fournissent au client une solution complète pour des analyses en temps réel. Event Hubs permet aux clients d’alimenter Azure en événements en temps réel et les tâches Stream Analytics peuvent traiter ces événements en temps réel. Par exemple, les clients peuvent envoyer des clics web, des lectures de capteurs ou des journaux des événements en ligne sur Event Hubs et créer des tâches Stream Analytics pour utiliser Event Hubs comme flux de données d’entrée pour les opérations de filtrage, d’agrégation et de corrélation en temps réel.

Il est important de noter que l’horodatage par défaut des événements issus de Event Hubs dans Stream Analytics est l’horodatage de l’arrivée de l’événement dans Event Hub, *EventEnqueuedUtcTime*. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

## Groupes de consommateurs

Chaque entrée Event Hub Stream Analytics doit être configurée de manière à disposer de son propre groupe de consommateurs. Quand une tâche contient une jointure réflexive ou plusieurs entrées, certaines entrées peuvent être lues par plus d'un lecteur en aval, ce qui impacte le total de lecteurs d'un même groupe de consommateurs. Pour éviter de dépasser la limite d'Event Hub de 5 lecteurs par groupe de consommateurs par partition, il est recommandé de désigner un groupe de consommateurs pour chaque tâche Stream Analytics. Notez qu’il existe également une limite de 20 groupes de consommateurs par hub d’événements. Pour plus d’informations, consultez [Guide de programmation Event Hubs](./articles/event-hubs-programming-guide.md).

## Configuration d’Event Hub comme un flux de données d’entrée

Le tableau ci-dessous explique chaque propriété de l’onglet des entrées Event Hub et fournit la description correspondante :

| Nom de la propriété | Description |
|-------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias d’entrée | Nom convivial qui servira dans la requête de tâche pour faire référence à cette entrée |
| Espace de noms Service Bus | Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un Event Hub, vous avez également créé un espace de noms Service Bus |
| Concentrateur d'événements | Nom de votre entrée Event Hub |
| Nom de la stratégie de l’Event Hub | Stratégie d’accès partagé, qui peut être créée dans l’onglet Configurer de l’Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès |
| Clé de la stratégie de l’Event Hub | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus |
| Groupe de consommateurs de l’Event Hub (facultatif) | Groupe de consommateurs qui absorbe les données du hub d’événements. En l’absence de spécification, les travaux Stream Analytics utilisent le groupe de consommateurs par défaut pour recevoir les données à partir du hub d’événements. Il est recommandé d’utiliser un groupe de consommateurs différent pour chaque tâche Stream Analytics |
| Format de sérialisation de l’événement | Pour s’assurer que vos requêtes fonctionnent comme prévu, Stream Analytics a besoin de connaître le format de sérialisation (JSON, CSV ou Avro) que vous utilisez pour les flux de données d’entrée |
| Encodage | UTF-8 est le seul format d’encodage actuellement pris en charge |

Lorsque vos données proviennent d’une source Event Hub, vous pouvez accéder à certains champs de métadonnées dans votre requête Stream Analytics. Le tableau ci-dessous répertorie les champs et fournit leur description.

| Propriété | Description |
|------------------------------|--------------------------------------------------------------------|
| System.EventProcessedUtcTime | Date et heure du traitement de l’événement par Stream Analytics |
| System.EventEnqueuedUtcTime | Date et heure de la réception de l’événement par Event Hubs |
| System.PartitionId | ID de partition de base zéro de l'adaptateur d'entrée |

Par exemple, vous pouvez écrire une requête comme suit :


    SELECT
    	System. EventProcessedUtcTime,
    	System. EventEnqueuedUtcTime,
    	System.PartitionId
    FROM Input

## Création d'une entrée de flux de données de stockage d'objets blob

Quand il est nécessaire de stocker de grandes quantités de données non structurées dans le cloud, le stockage d’objets blob offre une solution peu coûteuse et évolutive. Les données dans le [stockage d’objets blob](http://azure.microsoft.com/services/storage/blobs/) sont généralement considérées comme « au repos », mais elles peuvent être traitées comme un flux de données par Stream Analytics. Le traitement du journal est un scénario courant pour les entrées de stockage d'objets Blob avec Stream Analytics dans lequel la télémétrie est capturée à partir d'un système et doit être analysée et traitée pour extraire des données pertinentes.

Il est important de noter que l’horodatage par défaut des événements de stockage d’objets blob dans Stream Analytics est l’horodatage de la dernière modification de l’objet blob, *BlobLastModifiedUtcTime*. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

## Configuration du stockage d’objets blob comme un flux de données d’entrée

Le tableau ci-dessous explique chaque propriété de l’onglet des entrées de stockage d’objets blob et fournit la description correspondante :

<table>
<tbody>
<tr>
<td>Nom de la propriété</td>
<td>Description</td>
</tr>
<tr>
<td>Alias d’entrée</td>
<td>Nom convivial qui servira dans la requête de tâche pour faire référence à cette entrée.</td>
</tr>
<tr>
<td>Compte de stockage</td>
<td>Nom du compte de stockage dans lequel se trouvent vos fichiers blob.</td>
</tr>
<tr>
<td>Clé du compte de stockage</td>
<td>Clé secrète associée au compte de stockage.</td>
</tr>
<tr>
<td>Conteneur de stockage</td>
<td>Les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d’objets blob Microsoft Azure. Lorsque vous téléchargez un objet blob dans le service d'objets Blob, vous devez spécifier un conteneur pour cet objet blob.</td>
</tr>
<tr>
<td>Séquence d’octets préfixe du chemin d’accès [facultatif]</td>
<td>Chemin d’accès de fichier utilisé pour localiser vos objets blob dans le conteneur spécifié.<BR>Dans le chemin d’accès, vous pouvez choisir de spécifier une ou plusieurs instances de l’une des 3 variables suivantes&#160;:<BR>{date}, {time}, {partition}<BR>Exemple&#160;1&#160;: cluster1/logs/{date}/{time}/{partition}<BR>Exemple&#160;2&#160;: cluster1/logs/{date}</td>
</tr>
<tr>
<td>Format de la date [facultatif]</td>
<td>Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple&#160;: JJ/MM/AAAA</td>
</tr>
<tr>
<td>Format de l’heure [facultatif]</td>
<td>Si le jeton de l’heure est utilisé dans le chemin d’accès du préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisés. Actuellement, la seule valeur possible est&#160;HH.</td>
</tr>
<tr>
<td>Format de sérialisation de l’événement</td>
<td>Pour s’assurer que vos requêtes fonctionnent comme prévu, Stream Analytics a besoin de connaître le format de sérialisation (JSON, CSV ou Avro) que vous utilisez pour les flux de données d’entrée.</td>
</tr>
<tr>
<td>Encodage</td>
<td>Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge.</td>
</tr>
<tr>
<td>Délimiteur</td>
<td>Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge&#160;: virgule, point-virgule, espace, tabulation et barre verticale.</td>
</tr>
</tbody>
</table>

Lorsque vos données proviennent d’une source de stockage d’objets blob, vous pouvez accéder à certains champs de métadonnées dans votre requête Stream Analytics. Le tableau ci-dessous répertorie les champs et fournit leur description.

| Propriété | Description |
|--------------------------------|--------------------------------------------------------------------|
| System.BlobName | Nom de l'objet blob d'entrée d'où provient l'événement. |
| System.EventProcessedUtcTime | Date et heure du traitement de l’événement par Stream Analytics |
| System.BlobLastModifiedUtcTime | Date et heure de la dernière modification apportée à l'objet blob |
| System.PartitionId | ID de partition de base zéro de l'adaptateur d'entrée |

Par exemple, vous pouvez écrire une requête comme suit :


    SELECT
    	System.BlobName,
    	System.EventProcessedUtcTime,
    	System.BlobLastModifiedUtcTime
    FROM Input


## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes
Stream Analytics, un service géré d’analyse de diffusion en continu des données à partir de l’Internet des objets vous a été présenté. Pour en savoir plus sur ce service, consultez les rubriques suivantes :

- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l'API REST de gestion d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=Sept15_HO2-->