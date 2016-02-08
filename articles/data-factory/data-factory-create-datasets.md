<properties 
	pageTitle="Création de jeux de données" 
	description="Comprendre les jeux de données Azure Data Factory et apprendre à les créer." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Jeux de données

## Description
Un jeu de données est une description logique des données. Les données décrites peuvent aller de données à octets simples, semi-structurées, comme des fichiers CSV, à des tables relationnelles ou même des modèles. Le mécanisme (adresse, protocole, schéma d'authentification) pour accéder aux données est défini dans le Service lié et référencé dans la définition du jeu de données.

## Syntaxe

	{
	    "name": "<name of dataset>",
	    "properties":
	    {
	       "structure": [ ],
	       "type": "<type of dataset>",
			"external": <boolean flag to indicate external data>,
	        "typeProperties":
	        {
	        },
	        "availability":
	        {
	
	        },
	       "policy": 
	        {      
	
	        }
	    }
	}

**Détails de la syntaxe**

| Propriété | Description | Requis | Default |
| -------- | ----------- | -------- | ------- |
| name | Nom du jeu de données | Oui | N/D |
| structure | <p>Schéma du jeu de données</p><p>Consultez la section [Structure d'un jeu de données](#Structure) pour plus de détails</p> | Non. | N/D |
| type | Type du jeu de données | Oui | N/D |
| typeProperties | <p>Propriétés correspondant au type sélectionné</p><p>Consultez la section [Type du jeu de données](#Type) pour plus d'informations sur les types pris en charge et leurs propriétés.</p> | Oui | N/D |
| external | Indicateur booléen pour indiquer si un jeu de données est explicitement généré par un pipeline de fabrique de données ou non | Non | false | 
| availability | <p>Définit la fenêtre de traitement ou le modèle de découpage pour la production du jeu de données. </p><p>Consultez la rubrique [Disponibilité du jeu de données](#Availability) pour plus de détails</p><p>Consultez l'article [Planification et exécution](data-factory-scheduling-and-execution.md) pour plus d'informations sur le modèle de découpage du jeu de données</p> | Oui | N/D
| policy | Définit les critères ou la condition que les segments du jeu de données doivent remplir. <p>Consultez la rubrique [Stratégie du jeu de données](#Policy) pour plus de détails</p> | Non | N/D |

### Exemple

Voici un exemple de jeu de données qui représente une table nommée **MyTable** dans une **base de données SQL Azure**. Les chaînes de connexion de la base de données SQL Azure sont définies dans le **AzureSqlLinkedService** référencé dans ce jeu de données. Ce jeu de données est découpé quotidiennement.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

## <a name="Structure"></a>Structure d'un jeu de données

La section **Structure** indique le schéma du jeu de données. Elle contient la collection de colonnes et le type de ces colonnes. Chaque colonne contient les propriétés suivantes :

Propriété | Description | Requis | Default  
-------- | ----------- | -------- | -------
name | Nom de la colonne | Non | N/D 
type | Type de données de la colonne | Non | N/D 

### Exemple

Dans l'exemple suivant, le jeu de données contient trois colonnes : slicetimestamp, projectname et pageviews.

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Type"></a> Type du jeu de données

Les sources de données prises en charge et les types de jeux de données sont alignés. Consultez les rubriques sur le connecteur référencées dans l'article [Activités de déplacement des données](data-factory-data-movement-activities.md) pour obtenir plus d'informations sur les types et la configuration des jeux de données.

## <a name="Availability"></a> Disponibilité du jeu de données

La section Disponibilité dans un jeu de données définit la fenêtre de traitement ou le modèle de découpage pour la production du jeu de données. Consultez l’article [Planification et exécution](data-factory-scheduling-and-execution.md) pour plus d'informations sur le découpage du jeu de données et le modèle de dépendance.

| Propriété | Description | Requis | Default |
| -------- | ----------- | -------- | ------- |
| frequency | Spécifie l'unité de temps pour la production du segment du jeu de données.<p>**Fréquence prise en charge** : minute, heure, jour, semaine, mois</p> | Oui | N/D |
| interval | Spécifie un multiplicateur de fréquence<p>« Fréquence x intervalle » détermine la fréquence à laquelle le segment est généré.</p><p>Si vous voulez que le jeu de données soit segmenté toutes les heures, vous définissez **Fréquence** sur **Heure** et **Intervalle** sur **1**.</p><p>**Remarque :** Si vous définissez la fréquence en minutes, nous vous recommandons de définir l'intervalle au minimum sur 15</p> | Oui | N/D |
| style | Spécifie si le segment doit être généré au début / à la fin de l'intervalle.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><p>Si la fréquence est définie sur Mois et le style défini sur EndOfInterval, le segment est généré le dernier jour du mois. Si le style est défini sur StartOfInterval, le segment est généré le premier jour du mois.</p><p>Si la fréquence est définie sur Jour et style défini sur EndOfInterval, le segment est généré durant la dernière heure du jour.</p>Si la fréquence est définie sur Heure et le style défini sur EndOfInterval, le segment est généré à la fin de l’heure. Par exemple, pour un segment de la période 13h-14h, le segment est généré à 14h.</p> | Non | EndOfInterval |
| anchorDateTime | Définit la position absolue dans le temps utilisée par le planificateur pour calculer les limites du segment du jeu de données.<p>**Remarque :** si AnchorDateTime a des parties de date qui sont plus granulaires que la fréquence, les parties les plus granulaires sont ignorées. Par exemple, si l’**intervalle** est défini sur **toutes les heures** (fréquence : heure et intervalle : 1) et si **AnchorDateTime** contient **minutes et secondes**, les parties **minutes et secondes** de la valeur AnchorDateTime sont ignorées.</p>| Non | 01/01/0001 |
| Offset | Intervalle selon lequel le début et la fin de tous les segments du jeu de données sont déplacés.<p>**Remarque :** si les valeurs anchorDateTime et offset sont spécifiées, le résultat correspond au décalage combiné.</p> | Non | N/D |

### exemples anchorDateTime

**Exemple :** segments du jeu de données de 23 heures qui démarrent le 2007-04-19T08:00:00

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": "23",	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}


### exemple offset

Segments quotidiens qui démarrent à 6h au lieu de minuit, la valeur par défaut.

	"availability":
	{
		"frequency": "Daily",
		"interval": "1",
		"offset": "06:00:00"
	}

Dans ce cas, la valeur SliceStart est décalée de 6 heures et sera égale à 6h.

Pour un planning de 12 mois (fréquence = mois ; intervalle = 12), la valeur offset : 60.00:00:00 signifie chaque année le 1er ou le 2 mars (60 jours à partir du début de l’année si style = StartOfInterval), selon si l’année en cours est une année bissextile ou non.



## <a name="Policy"></a>Stratégie du jeu de données

La section Stratégie du jeu de données définit les critères ou la condition que les segments du jeu de données doivent remplir.

### Stratégies de validation

| Nom de la stratégie | Description | Appliqué(e) à | Requis | Default |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Valide le fait que les données dans un objet blob Azure répondent aux exigences de taille minimale (en mégaoctets). | Objets blob Azure | Non | N/D |
|minimumRows | Valide le fait que les données dans une base de données SQL Azure ou une table Azure contiennent le nombre minimal de lignes. | <ul><li>Base de données SQL Azure</li><li>Table Azure</li></ul> | Non | N/D

#### Exemples

**minimumSizeMB :**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### Stratégies ExternalData

Les jeux de données externes sont ceux qui ne sont pas générés par un pipeline en cours d'exécution dans la fabrique de données. Si le jeu de données est marqué comme étant External, la stratégie ExternalData peut être définie pour influencer le comportement de la disponibilité du segment du jeu de données.

| Nom | Description | Requis | Valeur par défaut |
| ---- | ----------- | -------- | -------------- |
| dataDelay | <p>Durée du délai de la vérification de la disponibilité des données externes pour le segment donné. Par exemple, si les données sont supposées être disponibles toutes les heures, la vérification pour déterminer si les données externes sont réellement disponibles et si le segment correspondant dispose de l’état Ready peut être différée selon la valeur de dataDelay.</p><p>S’applique uniquement à l’heure actuelle ; par exemple, s’il est 13h et que cette valeur est de 10 minutes, la validation commence à 13h10.</p><p>Ce paramètre n’affecte pas les segments dans le passé (segments avec Slice End Time + dataDelay < maintenant) qui sont traités sans délai.</p> <p>Toute heure supérieure à 23h59 doit être spécifiée à l’aide du format day.hours:minutes:seconds. Par exemple, pour spécifier 24 heures, n'utilisez pas 24:00:00 ; utilisez plutôt 1.00:00:00. Si vous utilisez 24:00:00, cette valeur sera traitée comme 24 jours (24.00:00:00). Pour 1 jour et 4 heures, spécifiez 1:04:00:00. </p>| Non | 0 |
| retryInterval | Délai d'attente entre un échec et la nouvelle tentative. S'applique à l'heure actuelle ; si la tentative précédente a échoué, le système laisse ce délai s'écouler après la dernière tentative. <p>S’il est 13h actuellement, la première tentative commence. Si la durée de la première vérification de validation est de 1 minute et si l'opération a échoué, la tentative suivante aura lieu à 13h + 1 min (durée) + 1 minute (intervalle avant nouvelle tentative) = 13h02. </p><p>Pour les segments dans le passé, il n’y a aucun délai. La nouvelle tentative a lieu immédiatement.</p> | Non | 00:01:00 (1 minute) | 
| retryTimeout | Le délai d’attente pour chaque nouvelle tentative.<p>S’il est défini sur 10 minutes, la validation doit être effectuée en 10 minutes maximum. S’il faut plus de 10 minutes pour effectuer la validation, la nouvelle tentative expire.</p><p>Si toutes les tentatives de validation expirent, le segment est marqué TimedOut.</p> | Non | 00:10:00 (10 minutes) |
| maximumRetry | Nombre de fois où la disponibilité des données externes est vérifiée. La valeur maximale autorisée est de 10. | Non | 3 | 

#### Exemples supplémentaires

Si vous avez besoin d’exécuter un pipeline tous les mois à une date et une heure spécifiques (par exemple, le 3e jour de chaque mois à 8h), vous pouvez utiliser la balise **offset** pour définir la date et l’heure d’exécution.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}

<!---HONumber=AcomDC_0128_2016-->