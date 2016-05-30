<properties
	pageTitle="API REST de recherche de journal Log Analytics | Microsoft Azure"
	description="Ce guide fournit un didacticiel de base qui décrit comment utiliser l’API REST de recherche Log Analytics dans Operations Management Suite (OMS). Il propose aussi des exemples qui vous permettent de découvrir comment utiliser les commandes."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# API REST de recherche de journal Log Analytics

Ce guide fournit un didacticiel de base qui décrit comment utiliser l’API REST de recherche Log Analytics dans Operations Management Suite (OMS). Il propose aussi des exemples qui vous permettent de découvrir comment utiliser les commandes. Certains exemples de cet article font référence à Operational Insights, qui est le nom de la version précédente de Log Analytics.

## Vue d’ensemble de l’API REST de recherche de journal

L’API REST de recherche Log Analytics est un service RESTful qui est accessible par le biais de l’API Azure Resource Manager. Vous trouverez, dans ce document, des exemples qui vous indiqueront comment accéder à l'API via l’[ARMClient](https://github.com/projectkudu/ARMClient), un outil de ligne de commande open source qui simplifie l'appel de l'API du Gestionnaire de ressources Azure. L'utilisation d’ARMClient et de PowerShell est une des nombreuses options vous permettant d’accéder à l'API de recherche Log Analytics. Une autre option consiste à utiliser le module Azure PowerShell pour OperationalInsights qui inclut des applets de commande pour accéder à la recherche. Grâce à ces outils, vous pouvez utiliser l'API RESTful Azure Resource Manager pour effectuer des appels vers les espaces de travail OMS et exécuter en leur sein des commandes de recherche. L'API produira pour vous des résultats de recherche au format JSON, qui vous permet d'utiliser ces résultats, par programme, de différentes manières.

Le Gestionnaire de ressources Azure peut être utilisé via une [Bibliothèque pour .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) et une [API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Pour en savoir plus, consultez les pages web connexes.

## Didacticiel de base sur l’API de recherche de journal de Log Analytics

### Pour utiliser le client ARM

1. Installez [Chocolatey](https://chocolatey.org/) (gestionnaire de packages open source pour Windows). Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur, puis exécutez la commande suivante :

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Installez ARMClient en exécutant la commande suivante :

    ```
    choco install armclient
    ```

### Pour effectuer une simple recherche à l'aide d’ARMClient

1. Connectez-vous à votre compte Microsoft ou OrgID :

    ```
    armclient login
    ```

    Une connexion réussie répertorie tous les abonnements associés au compte spécifique :

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Obtenez les espaces de travail Operations Management Suite :

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Un appel Get réussi affiche tous les espaces de travail liés à l’abonnement :

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Créez votre variable de recherche :

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Lancez une recherche à l’aide de votre nouvelle variable de recherche :

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## Exemples de référence de l’API REST de recherche Log Analytics
Les exemples suivants vous expliquent comment utiliser l'API de recherche.

### Recherche - Action/lecture

**Exemple d’URL :**

```
	/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Requête :**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
Le tableau suivant décrit les propriétés qui sont disponibles

|**Propriété**|**Description**|
|---|---|
|top|Le nombre maximal de résultats à renvoyer.|
|highlight|Contient des pré- et post-paramètres, utilisés généralement pour mettre en surbrillance les champs de correspondance|
|pre|Préfixe la chaîne spécifique à vos champs correspondants.|
|post|Ajoute la chaîne spécifique à vos champs correspondants.|
|query|La requête de recherche utilisée pour recueillir et renvoyer des résultats.|
|start|Le début de la plage horaire à partir de laquelle vous souhaitez obtenir des résultats.|
|end|La fin de la plage horaire à partir de laquelle vous souhaitez obtenir des résultats.|


**Réponse :**

```
	{
	  "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	  "__metadata" : {
	    "resultType" : "raw",
	    "total" : 1455,
	    "top" : 150,
	    "StartTime" : "2015-02-11T21:09:07.0345815Z",
	    "Status" : "Successful",
	    "LastUpdated" : "2015-02-11T21:09:07.331463Z",
	    "CoreResponses" : [],
	    "sort" : [{
	      "name" : "TimeGenerated",
	      "order" : "desc"
	    }],
	    "requestTime" : 450
	  },
	  "value": [{
	    "SourceSystem" : "OpsManager",
	    "TimeGenerated" : "2015-02-07T14:07:33Z",
	    "Source" : "SideBySide",
	    "EventLog" : "Application",
	    "Computer" : "BAMBAM",
	    "EventCategory" : 0,
	    "EventLevel" : 1,
	    "EventLevelName" : "Error",
	    "UserName" : "N/A",
	    "EventID" : 78,
	    "MG" : "00000000-0000-0000-0000-000000000001",
	    "TimeCollected" : "2015-02-07T14:10:04.69Z",
	    "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
	    "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	    "Type" : "Event",
	    "__metadata" : {
	      "Type" : "Event",
	      "TimeGenerated" : "2015-02-07T14:07:33Z",
	      "highlighting" : {
	      "EventLevelName" : ["{[hl]}Error{[/hl]}"]
	    }
	  }]
	],
	        "start" : "2015-02-04T21:03:29.231Z",
	        "end" : "2015-02-11T21:03:29.231Z"
	      }
	    }
	  }]
	}
```

### Recherche/{ID} - Action/lecture

**Demander le contenu d'une recherche enregistrée :**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] Si la recherche renvoie un état « En attente », une interrogation des résultats mis à jour peut alors être réalisée via cette API. Après 6 minutes, le résultat de la recherche est supprimé du cache et HTTP Gone est retourné. Si la demande de recherche initiale a retourné immédiatement un état « Succès », le résultat n’est pas ajouté au cache, ce qui signifie que cette API retourne HTTP Gone si elle est interrogée. Le contenu d’un résultat HTTP 200 est présenté dans le même format que la demande de recherche initiale. Toutefois, les valeurs sont mises à jour.

### Recherches enregistrées - REST uniquement

**Demander la liste des recherches enregistrées :**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Méthodes prises en charge : GET PUT DELETE

Méthodes de collection prises en charge : GET

Le tableau suivant décrit les propriétés qui sont disponibles

|Propriété|Description|
|---|---|
|ID|Identificateur unique.|
|Etag|**Requis pour le correctif**. Mis à jour par le serveur à chaque écriture. La valeur doit être égale à la valeur actuelle stockée ou '*' pour mettre à jour. 409 retourné pour les valeurs anciennes ou non valides.|
|properties.query|**Requis**. Requête de la recherche.|
|properties.displayName|**Requis**. Nom d'affichage de la requête défini par l'utilisateur. S'il est modélisé en tant que ressource Azure, il s'agit d'une balise.|
|properties.category|**Requis**. Catégorie de la requête définie par l'utilisateur. Si elle est modélisée en tant que ressource Azure, il s'agit d'une balise.|

>[AZURE.NOTE] Actuellement, l'API de recherche Log Analytics retourne des recherches enregistrées créées par l'utilisateur lorsqu'elle est interrogée concernant les recherches enregistrées dans un espace de travail. Pour le moment, l'API ne retourne pas de recherches enregistrées fournies par les solutions. Cette fonctionnalité sera ajoutée à une date ultérieure.

### Supprimer les recherches enregistrées

**Requête :**

```
	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### Mettre à jour les recherches enregistrées

 **Requête :**

```
	$savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### Métadonnées - JSON uniquement

Voici un moyen d'afficher les champs pour tous les types de journaux pour les données collectées dans votre espace de travail. Par exemple, si vous voulez savoir si le type Événement possède un champ nommé Ordinateur, ceci constitue une méthode de recherche et de confirmation.

**Requête de champs :**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Réponse :**

```
	{
	  "__metadata" : {
	    "schema" : {
	      "name" : "Example Name",
	      "version" : 2
	    },
	    "resultType" : "schema",
	    "requestTime" : 35
	  },
	  "value" : [{
	      "name" : "MG",
	      "displayName" : "MG",
	      "type" : "Guid",
	      "facetable" : true,
	      "display" : false,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }, {
	      "name" : "ManagementGroupName",
	      "displayName" : "ManagementGroupName",
	      "type" : "String",
	      "facetable" : true,
	      "display" : true,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }
	  ]
	}
```

Le tableau suivant décrit les propriétés qui sont disponibles

|**Propriété**|**Description**|
|---|---|
|name|Nom du champ.|
|displayName|Nom d'affichage du champ.|
|type|Type de la valeur du champ.|
|facetable|Combinaison des propriétés « indexed », « stored » et « facet » actuelles.|
|display|Propriété « display » actuelle. True si le champ est visible dans la recherche.|
|ownerType|Réduit uniquement aux types appartenant aux adresses IP intégrées.|


## Paramètres facultatifs
Les informations suivantes décrivent les paramètres facultatifs disponibles.

### Highlighting

Le paramètre « Highlight » est un paramètre facultatif que vous pouvez utiliser pour demander au sous-système de recherche d'inclure un jeu de marqueurs dans sa réponse.

Ces marqueurs indiquent le début et la fin du texte mis en surbrillance qui correspond aux critères fournis dans votre requête de recherche. Vous pouvez spécifier les marqueurs de début et de fin qui seront utilisés par la recherche pour encapsuler le terme en surbrillance.

**Exemple de requête de recherche**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Exemple de résultat :**

```
	{
	    "TimeGenerated":
	    "2015-05-18T23:55:59Z", "Source":
	    "EventLog": "Application",
	    "Computer": "smokedturkey.net",
	    "EventCategory": 0,
	    "EventLevel":1,
	    "EventLevelName":
	    "Error"
	    "Manager ", "__metadata":
	    {
	        "Type": "Event",
	        "TimeGenerated": "2015-05-18T23:55:59Z",
	        "highlighting": {
	            "EventLevelName":
	            ["{[hl]}Error{[/hl]}"]
	        }
	    }
	}
```

Notez que le résultat ci-dessus contient un message d'erreur qui a été préfixé et ajouté.

## Groupes d’ordinateurs

Les groupes d'ordinateurs sont des recherches spéciales enregistrées qui retournent un ensemble d'ordinateurs. Vous pouvez utiliser un groupe d'ordinateurs dans d'autres requêtes pour limiter les résultats aux ordinateurs du groupe. Un groupe d'ordinateurs est implémenté comme une recherche enregistrée avec une balise Group dont la valeur est Computer.

Voici un exemple de réponse pour un groupe d'ordinateurs.

	"etag": "W/"datetime'2016-04-01T13%3A38%3A04.7763203Z'"",
	"properties": {
		"Category": "My Computer Groups",
		"DisplayName": "My Computer Group",
		"Query": "srv* | Distinct Computer",
		"Tags": [{
        	"Name": "Group",
        	"Value": "Computer"
      	}],
    "Version": 1
	}

### Récupération de groupes d'ordinateurs

Utilisez la méthode Get avec l'ID de groupe pour récupérer un groupe d'ordinateurs.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### Création ou mise à jour d'un groupe d'ordinateurs

Utilisez la méthode Put avec un ID de recherche enregistrée unique pour créer un groupe d'ordinateurs. Si vous utilisez un ID de groupe d’ordinateurs existant, celui-ci sera modifié. Lorsque vous créez un groupe d'ordinateurs dans la console OMS, l'ID est créé à partir du groupe et du nom.

La requête utilisée pour la définition du groupe doit retourner un ensemble d'ordinateurs pour que le groupe fonctionne correctement. Il est recommandé de terminer votre requête par *| Distinct Computer* pour s’assurer que les données correctes sont retournées.

La définition de la recherche enregistrée doit inclure une balise nommée Group avec une valeur Computer pour que la recherche soit classée comme un groupe d'ordinateurs.

	$etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
	$groupName="My Computer Group"
	$groupQuery = "Computer=srv* | Distinct Computer"
	$groupCategory="My Computer Groups"
	$groupID = "My Computer Groups | My Computer Group"

	$groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

	armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### Suppression de groupes d'ordinateurs

Utilisez la méthode Delete avec l'ID de groupe pour supprimer un groupe d'ordinateurs.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## Étapes suivantes

- En savoir plus sur les [recherches de journaux](log-analytics-log-searches.md) pour générer des requêtes utilisant des champs personnalisés comme critères.

<!---HONumber=AcomDC_0518_2016-->