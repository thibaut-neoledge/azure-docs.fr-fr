<properties 
	pageTitle="Utiliser les données de référence | Microsoft Azure" 
	description="Utiliser les données de référence en tant que flux d’entrée" 
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
	ms.date="10/05/2015" 
	ms.author="jeffstok"/>

# Utilisation des données de référence en tant qu’entrée

Les données de référence sont un jeu de données finies, statiques ou variant lentement au fil du temps par nature, utilisé pour effectuer des recherches ou pour se mettre en corrélation avec votre flux de données. Pour utiliser des données de référence dans votre tâche Azure Stream Analytics, vous utiliserez généralement une [jointure de données de référence](https://msdn.microsoft.com/library/azure/dn949258.aspx) dans votre requête. Stream Analytics utilise le stockage d’objets blob Azure comme couche de stockage pour les données de référence et, avec la référence Azure Data Factory, les données peuvent être transformées et/ou copiées en stockage d’objets blob Azure, pour être utilisées comme données de référence pour [un nombre illimité de magasins de données cloud et en local](./articles/data-factory-data-movement-activities.md).

## Configuration des données de référence

Pour configurer vos données de référence, vous devez d’abord créer une entrée de type Données de référence. Le tableau ci-dessous explique chaque propriété que vous devez fournir lors de la création de l’entrée des données de référence avec sa description :

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
<td>Nom du compte de stockage dans lequel se trouvent vos fichiers blob. S’il se trouve dans le même abonnement que votre tâche Stream Analytics, vous pouvez le sélectionner dans la liste déroulante.</td>
</tr>
<tr>
<td>Clé du compte de stockage</td>
<td>Clé secrète associée au compte de stockage. Elle est remplie automatiquement si le compte de stockage se trouve dans le même abonnement que votre tâche Stream Analytics.</td>
</tr>
<tr>
<td>Conteneur de stockage</td>
<td>Les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d’objets blob Microsoft Azure. Lorsque vous téléchargez un objet blob dans le service d'objets Blob, vous devez spécifier un conteneur pour cet objet blob.</td>
</tr>
<tr>
<td>Séquence d’octets préfixe du chemin d’accès [facultatif]</td>
<td>Chemin d’accès de fichier utilisé pour localiser vos objets blob dans le conteneur spécifié. Dans le chemin d’accès, vous pouvez choisir de spécifier une ou plusieurs instances de l’une des 2&#160;variables suivantes&#160;:<BR>{date}, {time}<BR>Exemple&#160;1&#160;: products/{date}/{time}/product-list.csv<BR>Exemple&#160;2&#160;: products/{date}/product-list.csv
</tr>
<tr>
<td>Format de la date [facultatif]</td>
<td>Si vous avez utilisé {date} dans le modèle de chemin d’accès que vous avez spécifié, vous pouvez sélectionner le format de date selon lequel vos fichiers sont organisés dans la liste déroulante des formats pris en charge. Exemple&#160;: JJ/MM/AAAA</td>
</tr>
<tr>
<td>Format de l’heure [facultatif]</td>
<td>Si vous avez utilisé {time} dans le modèle de chemin d’accès que vous avez spécifié, vous pouvez sélectionner le format d’heure selon lequel vos fichiers sont organisés dans la liste déroulante des formats pris en charge. Exemple&#160;: HH</td>
</tr>
<tr>
<td>Format de sérialisation de l’événement</td>
<td>Pour s’assurer que vos requêtes fonctionnent comme prévu, Stream Analytics a besoin de connaître le format de sérialisation que vous utilisez pour les flux de données d’entrée. Pour les données de référence, les formats pris en charge sont CSV et JSON.</td>
</tr>
<tr>
<td>Encodage</td>
<td>UTF-8 est le seul format d’encodage actuellement pris en charge</td>
</tr>
</tbody>
</table>

## Génération de données de référence sur une planification

Si vos données de référence sont un jeu de données variant lentement, la prise en charge de l’actualisation des données de référence peut être activée en spécifiant un modèle de chemin d’accès dans la configuration d’entrée à l’aide des jetons {date} et {time}. Stream Analytics collectera les définitions de données de référence mises à jour en fonction de ce modèle de chemin d’accès. Par exemple, un modèle ````"/sample/{date}/{time}/products.csv"```` avec un format de date « JJ-MM-AAAA » et un format d’heure « HH:mm » indique à Stream Analytics de récupérer l’objet blob mis à jour ````"/sample/2015-04-16/17:30/products.csv"```` à 17:30 le 16 avril 2015 (UTC).

> [AZURE.NOTE]Actuellement, les tâches Stream Analytics recherchent l’actualisation des objets blob uniquement lorsque l’heure machine coïncide avec l’heure encodée dans le nom de l’objet blob. Par exemple, la tâche recherchera /sample/2015-04-16/17:30/products.csv entre 17:30 et 17:30:59.9 le 16 avril 2015 (UTC). Lorsque l’horloge de la machine marque 17:31, la tâche cesse de rechercher /sample/2015-04-16/17:30/products.csv et commence à rechercher /sample/2015-04-16/17:31/products.csv. Le seul cas où les objets blob de données de référence précédents sont pris en compte est lorsque la tâche démarre pour la première fois. À ce moment-là, la tâche recherche l’objet blob le plus récent produit avant l’heure spécifiée de début de la tâche. Cela permet de garantir la présence d’un jeu de données de référence non vide au démarrage de la tâche. En l’absence d’un tel jeu de données, la tâche échouera et affichera la notification de diagnostic suivante à l’utilisateur :

[Azure Data Factory](http://azure.microsoft.com/documentation/services/data-factory/) peut être utilisé pour orchestrer la tâche de création d’objets blob mis à jour requise par Stream Analytics pour mettre à jour les définitions de données de référence. Data Factory est un service d’intégration de données dans le cloud qui gère et automatise le déplacement et la transformation des données. Data Factory prend en charge [la connexion à un grand nombre de magasins de données cloud et en local](./articles/data-factory-data-movement-activities.md) et le déplacement facile de données à intervalles réguliers que vous spécifiez. Pour plus d’informations et des instructions étape par étape sur la façon de configurer un pipeline Data Factory pour générer des données de référence pour Stream Analytics qui est actualisé selon une planification prédéfinie, consultez cet [exemple GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

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

<!---HONumber=Oct15_HO3-->