<properties
	pageTitle="Connexion de données : entrées de données d’événements depuis un flux d’événement | Microsoft Azure"
	description="En savoir plus sur la configuration d’une connexion de données à Stream Analytics nommée « entrées ». Les données incluent un flux de données de partir d’événements et également des données de référence."
	keywords="flux de données, connexion de données, flux d’événements"
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
	ms.date="11/23/2015"
	ms.author="jeffstok"/>

# Connexion de données : en savoir plus sur les entrées de flux de données pour Stream Analytics

La connexion de données à Stream Analytics est un flux de données d’événements depuis une source de données. Il s’agit d’une « entrée ». Stream Analytics propose une meilleure intégration aux sources de flux de données Azure Event Hub, IoT Hub et stockages d’objets blob pouvant être issus du même abonnement Azure ou d’un autre abonnement comme votre tâche d’analyse.

## Types d’entrée de données : flux de données et données de référence.
Lorsque les données sont transmises à une source de données, elles sont utilisées par la tâche Stream Analytics et traitées en temps réel. Les entrées sont divisées en deux types distincts : les entrées de flux de données et les entrées de données de référence.

### Entrées de flux de données
Un flux de données est une séquence illimitée d’événements arrivant au fil du temps. Les tâches Stream Analytics doivent contenir au moins une entrée de flux de données qui sera traitée et transformée par la tâche. Le stockage d’objets, Event Hubs et IoT Hubs sont pris en charge en tant que sources d’entrée de flux de données. Les hubs d’événements sont utilisés pour collecter des flux d’événements à partir de plusieurs appareils et services, comme les flux d’activités sur les réseaux sociaux, des informations boursières ou des données de capteurs. Les conteneurs IoT Hub sont optimisés pour collecter des données à partir d’appareils connectés dans les scénarios Internet des objets (IoT). Le stockage d’objets blob peut être utilisé comme source d’entrée pour la réception de données en bloc en tant que flux.

### Données de référence
Stream Analytics prend en charge un second type d'entrée : les données de référence. Il s’agit de données auxiliaires, statiques ou variant lentement au fil du temps, qui sont généralement utilisées pour effectuer des corrélations et des recherches. Le stockage d’objets blob Azure est la seule source d’entrée prise en charge pour les données de référence. Les objets blob de source de données de référence sont limités à une taille de 50 Mo. Pour savoir comment créer des entrées de données de référence, consultez [Utiliser les données de référence](stream-analytics-use-reference-data.md).

## Créer une entrée de flux de données avec Event Hub

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) est un service de réception d’événements de publication/d’abonnement hautement évolutif. Il peut collecter des millions d’événements par seconde afin que vous puissiez traiter et analyser les grandes quantités de données générées par vos appareils connectés et vos applications. Il s’agit des entrées les plus couramment utilisées pour Stream Analytics. Event Hubs et Stream Analytics fournissent au client une solution complète pour des analyses en temps réel. Event Hubs permet aux clients d’alimenter Azure en événements en temps réel et les tâches Stream Analytics peuvent traiter ces événements en temps réel. Par exemple, les clients peuvent envoyer des clics web, des lectures de capteurs ou des journaux des événements en ligne sur Event Hubs et créer des tâches Stream Analytics pour utiliser Event Hubs comme flux de données d’entrée pour les opérations de filtrage, d’agrégation et de corrélation en temps réel.

Il est important de noter que l’horodatage par défaut des événements issus d’Event Hubs dans Stream Analytics est l’horodatage de l’arrivée de l’événement dans Event Hub, EventEnqueuedUtcTime. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### Groupes de consommateurs

Chaque entrée Event Hub Stream Analytics doit être configurée de manière à disposer de son propre groupe de consommateurs. Quand une tâche contient une jointure réflexive ou plusieurs entrées, certaines entrées peuvent être lues par plus d'un lecteur en aval, ce qui impacte le total de lecteurs d'un même groupe de consommateurs. Pour éviter de dépasser la limite d'Event Hub de 5 lecteurs par groupe de consommateurs par partition, il est recommandé de désigner un groupe de consommateurs pour chaque tâche Stream Analytics. Notez qu’il existe également une limite de 20 groupes de consommateurs par hub d’événements. Pour plus d’informations, consultez [Guide de programmation Event Hubs](./event-hubs/event-hubs-programming-guide.md).

## Configurer Event Hub comme un flux de données d’entrée

Le tableau ci-dessous explique chaque propriété de l’onglet des entrées Event Hub et fournit la description correspondante :

| NOM DE LA PROPRIÉTÉ | DESCRIPTION |
|------|------|
| Alias d’entrée | Nom convivial qui servira dans la requête de tâche pour faire référence à cette entrée |
| Espace de noms Service Bus | Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un hub d’événements, vous avez également créé un espace de noms Service Bus. |
| Hub d’événements | Nom de l’entrée de votre hub d’événements |
| Nom de la stratégie du hub d’événements | Stratégie d’accès partagé, qui peut être créée dans l’onglet Configuration du hub d’événements. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie du hub d’événements | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus |
| Groupe de consommateurs du hub d’événements (facultatif) | Groupe de consommateurs qui absorbe les données du hub d’événements. En l’absence de spécification, les travaux Stream Analytics utilisent le groupe de consommateurs par défaut pour recevoir les données à partir du hub d’événements. Il est recommandé d’utiliser un groupe de consommateurs différent pour chaque tâche Stream Analytics. |
| Format de sérialisation de l’événement | Pour s’assurer que vos requêtes fonctionnent comme prévu, Stream Analytics a besoin de connaître le format de sérialisation (JSON, CSV ou Avro) que vous utilisez pour les flux de données d’entrée. |
| Encodage | UTF-8 est le seul format d’encodage actuellement pris en charge. |

Lorsque vos données proviennent d’une source Event Hub, vous pouvez accéder à certains champs de métadonnées dans votre requête Stream Analytics. Le tableau ci-dessous répertorie les champs et fournit leur description.

| PROPRIÉTÉ | DESCRIPTION |
|------|------|
| EventProcessedUtcTime | Date et heure du traitement de l’événement par Stream Analytics |
| EventEnqueuedUtcTime | Date et heure de la réception de l’événement par le hub d’événements. |
| PartitionId | ID de partition de base zéro de l’adaptateur d’entrée. |

Par exemple, vous pouvez écrire une requête comme suit :

````
SELECT
	EventProcessedUtcTime,
	EventEnqueuedUtcTime,
	PartitionId
FROM Input
````

## Créer d’une entrée de flux de données IoT Hub

Azure Iot Hub est un service de réception d’événements de publication/d’abonnement hautement évolutif optimisé pour les scénarios IoT. Il est important de noter que l’horodatage par défaut des événements issus des conteneurs IoT Hub dans Stream Analytics est l’horodatage de l’arrivée de l’événement dans IoT Hub, EventEnqueuedUtcTime. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### Groupes de consommateurs

Chaque entrée IoT Hub Stream Analytics doit être configurée de manière à disposer de son propre groupe de consommateurs. Quand une tâche contient une jointure réflexive ou plusieurs entrées, certaines entrées peuvent être lues par plus d’un lecteur en aval, ce qui impacte le total de lecteurs d’un même groupe de consommateurs. Pour éviter de dépasser la limite d’IoT Hub de 5 lecteurs par groupe de consommateurs par partition, il est recommandé de désigner un groupe de consommateurs pour chaque tâche Stream Analytics.

## Configurer IoT Hub comme flux de données d’entrée

Le tableau ci-dessous explique chaque propriété de l’onglet des entrées IoT Hub et fournit la description correspondante :

| NOM DE LA PROPRIÉTÉ | DESCRIPTION |
|------|------|
| Alias d’entrée | Nom convivial qui servira dans la requête de tâche pour faire référence à cette entrée. |
| IoT Hub | IoT Hub est un conteneur pour un jeu d’entités de messagerie. |
| Point de terminaison | Le nom de votre point de terminaison IoT Hub. |
| Nom de la stratégie d’accès partagé | Stratégie d’accès partagé pour accorder l’accès à IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie d’accès partagé | Clé d’accès partagé utilisée pour authentifier l’accès à IoT Hub. |
| Groupe de consommateurs (facultatif) | Groupe de consommateurs qui absorbe les données d’IoT Hub. En l’absence de spécification, les travaux Stream Analytics utilisent le groupe de consommateurs par défaut pour recevoir les données à partir d’IoT Hub. Il est recommandé d’utiliser un groupe de consommateurs différent pour chaque tâche Stream Analytics. |
| Format de sérialisation de l’événement | Pour s’assurer que vos requêtes fonctionnent comme prévu, Stream Analytics a besoin de connaître le format de sérialisation (JSON, CSV ou Avro) que vous utilisez pour les flux de données d’entrée. |
| Encodage | UTF-8 est le seul format d’encodage actuellement pris en charge. |

Quand vos données proviennent d’une source IoT Hub, vous pouvez accéder à certains champs de métadonnées dans votre requête Stream Analytics. Le tableau ci-dessous répertorie les champs et fournit leur description.

| PROPRIÉTÉ | DESCRIPTION |
|------|------|
| EventProcessedUtcTime | Date et heure du traitement de l'événement. |
| EventEnqueuedUtcTime | Date et heure de la réception de l’événement par IoT Hub |
| PartitionId | ID de partition de base zéro de l'adaptateur d'entrée. |
| IoTHub.MessageId | Permet de corréler une communication bidirectionnelle dans IoT Hub |
| IoTHub.CorrelationId | Utilisé dans les réponses de message et les commentaires dans IoT Hub |
| IoTHub.ConnectionDeviceId | ID authentifié utilisé pour envoyer ce message, marqué sur les messages liés aux services par IoT Hub |
| IoTHub.ConnectionDeviceGenerationId | ID de génération de l’appareil authentifié utilisé pour envoyer ce message, marqué sur les messages liés aux services par IoT Hub |
| IoTHub.EnqueuedTime | Heure à laquelle le message a été reçu par IoT Hub. |
| IoTHub.StreamId | Propriété d’événement personnalisée ajoutée par l’appareil de l’expéditeur. |

## Créer une entrée de flux de données de stockage d’objets blob

Quand il est nécessaire de stocker de grandes quantités de données non structurées dans le cloud, le stockage d’objets blob offre une solution peu coûteuse et évolutive. Les données dans le [stockage d’objets blob](https://azure.microsoft.com/services/storage/blobs/) sont généralement considérées comme « au repos », mais elles peuvent être traitées comme un flux de données par Stream Analytics. Le traitement du journal est un scénario courant pour les entrées de stockage d’objets Blob avec Stream Analytics dans lequel la télémétrie est capturée à partir d’un système et doit être analysée et traitée pour extraire des données pertinentes.

Il est important de noter que l’horodatage par défaut des événements de stockage d’objets blob dans Stream Analytics est l’horodatage de la dernière modification de l’objet blob, *BlobLastModifiedUtcTime*. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Le tableau ci-dessous explique chaque propriété de l’onglet des entrées de stockage d’objets blob et fournit la description correspondante :

<table>
<tbody>
<tr>
<td>NOM DE LA PROPRIÉTÉ</td>
<td>DESCRIPTION</td>
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
<td>Conteneur de stockage
</td>
<td>Les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d’objets blob Microsoft Azure. Lorsque vous téléchargez un objet blob dans le service d'objets Blob, vous devez spécifier un conteneur pour cet objet blob.</td>
</tr>
<tr>
<td>Séquence d’octets préfixe du chemin d’accès [facultatif]</td>
<td>Chemin d’accès de fichier utilisé pour localiser vos objets blob dans le conteneur spécifié. Dans le chemin d’accès, vous pouvez choisir de spécifier une ou plusieurs instances de l’une des 3 variables suivantes&#160;:<BR>{date}, {time},<BR>{partition}<BR>Exemple&#160;1&#160;: cluster1/logs/{date}/{time}/{partition}<BR>Exemple&#160;2&#160;: cluster1/logs/{date}<P>Notez que «&#160;*&#160;» n’est pas une valeur autorisée pour pathprefix. Seuls les <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caractères d’objet blob Azure</a> valides sont autorisés.</td>
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

| PROPRIÉTÉ | DESCRIPTION |
|------|------|
| BlobName | Nom de l’objet blob d’entrée d’où provient l’événement. |
| EventProcessedUtcTime | Date et heure du traitement de l’événement par Stream Analytics |
| BlobLastModifiedUtcTime | Date et heure de la dernière modification apportée à l’objet blob. |
| PartitionId | ID de partition de base zéro de l’adaptateur d’entrée. |

Par exemple, vous pouvez écrire une requête comme suit :

````
SELECT
	BlobName,
	EventProcessedUtcTime,
	BlobLastModifiedUtcTime
FROM Input
````


## Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes
Vous avez appris à connaître les options de connexion de données dans Azure pour vos travaux Stream Analytics. Pour en savoir plus sur Stream Analytics, consultez :

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

<!---HONumber=AcomDC_0128_2016-->