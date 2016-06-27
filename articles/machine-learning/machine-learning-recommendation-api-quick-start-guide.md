<properties 
	pageTitle="Guide de démarrage rapide : API Machine Learning Recommendations | Microsoft Azure" 
	description="Azure Machine Learning Recommendations - Guide de démarrage rapide" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/09/2016" 
	ms.author="luisca"/>

# Guide de démarrage rapide pour l'API de Machine Learning Recommendations

Ce document décrit comment intégrer votre service ou application pour utiliser Microsoft Azure Machine Learning Recommendations. Vous trouverez plus d'informations sur l'API Recommendations dans la [galerie](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Présentation générale

Pour utiliser Azure Machine Learning Recommendations, procédez comme suit :

* Créez un modèle : le modèle est le conteneur de vos données d’utilisation, des données de catalogue et du modèle de recommandation.
* Importer les données du catalogue : un catalogue contient des informations de métadonnées sur les éléments. 
* Importez des données d'utilisation : les données d'utilisation peuvent être téléchargées de deux manières :
	* En téléchargeant un fichier qui contient les données d'utilisation.
	* En envoyant des événements d’acquisition de données. Généralement, vous téléchargez un fichier d’utilisation pour pouvoir créer un modèle de recommandation initial (démarrage) et l’utiliser jusqu’à ce que le système rassemble suffisamment de données en utilisant le format d’acquisition de données.
* Générez un modèle de recommandation : il s’agit d’une opération asynchrone dans laquelle le système de recommandation prend toutes les données d’utilisation et crée un modèle de recommandation. Cette opération peut prendre plusieurs minutes, voire plusieurs heures, selon la taille des données et les paramètres de configuration de génération. Lors du déclenchement de la build, vous obtenez un ID de build. Utilisez-le pour vérifier à quel moment s’est terminé le processus de génération et ce, avant de commencer à utiliser les recommandations.
* Utilisez les recommandations : obtenez des recommandations pour un élément spécifique ou pour une liste d’éléments.

Toutes les étapes ci-dessus sont effectuées via l’API Azure Machine Learning Recommendations. Vous pouvez télécharger un exemple d'application qui implémente chacune de ces étapes également à partir de la [galerie.](http://1drv.ms/1xeO2F3)

##Limites

* Le nombre maximal de modèles par abonnement est de 10.
* Le nombre maximal d'éléments pouvant être contenus dans un catalogue est de 100 000.
* La quantité maximale de points d'utilisation conservée est d'environ 5 000 000. Le plus ancien est supprimé quand des nouveaux sont téléchargés ou signalés.
* La taille maximale des données pouvant être envoyées dans POST (par exemple, importation des données de catalogue ou des données d'utilisation) est de 200 Mo.
* Le nombre de transactions par seconde pour une build de modèle de recommandation inactive est d'environ 2 TPS. Une build de modèle de recommandation active peut prendre en charge jusqu'à 20 TPS.

##Intégration

###Authentification
Microsoft Azure Marketplace prend en charge les méthodes d’authentification de base ou OAuth. Vous pouvez facilement trouver les clés de compte en accédant aux clés sur le Marketplace sous [vos paramètres de compte](https://datamarket.azure.com/account/keys).
####Authentification de base
Ajoutez l’en-tête d’autorisation :

	Authorization: Basic <creds>
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
	
Convertir en Base64 (C#)

	var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
	var creds = Convert.ToBase64String(bytes);
	
Convertir en Base64 (JavaScript)

	var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
	



###URI de service
Les URI racines de service des API Azure Machine Learning Recommendations se trouvent [ici](https://api.datamarket.azure.com/amla/recommendations/v2/).

L'URI de service complet est exprimée à l'aide des éléments de la spécification OData.

###Version de l'API
À la fin de chaque appel d’API doit se trouver un paramètre de requête appelé apiVersion qui doit avoir la valeur « 1.0 ».

###Respect de la casse des ID
Les ID, quelle que soit l'API qui les retourne, respectent la casse. Ils doivent donc être utilisés comme tels quand ils sont passés en tant que paramètres dans les appels d'API ultérieurs. Par exemple, les ID de modèle et de catalogue respectent la casse.

###Création d’un modèle
Création d'une requête « Créer un modèle » :

| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelName |	Seuls les lettres (A-Z, a-z), les chiffres (0-9), les tirets (-) et les traits de soulignement (\_) sont autorisés.<br>Longueur maximale : 20 | | apiVersion | 1.0 | ||| | Corps de la demande | AUCUN |


**Réponse** :

Code d'état HTTP : 200

- `feed/entry/content/properties/id` : contient l'ID du modèle. **Remarque** : l'ID du modèle respecte la casse.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>


###Importation de données de catalogue

Si vous téléchargez plusieurs fichiers de catalogue dans le même modèle avec plusieurs appels, seuls les nouveaux éléments de catalogue sont insérés. Les éléments existants resteront avec les valeurs d'origine.

| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle (respecte la casse) |
| filename | Identificateur textuel du catalogue.<br>Seuls les lettres (A-Z, a-z), les nombres (0-9), les tirets (-) et les traits de soulignement (\_) sont autorisés.<br>Longueur maximale : 50 | | apiVersion | 1.0 | ||| | Corps de la demande | Données du catalogue. Format :<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>Nom</th><th>Obligatoire</th><th>Type</th><th>Description</th></tr><tr><td>ID de l'élément</td><td>Oui</td><td>Alphanumérique, longueur maximale 50</td><td>Identificateur unique d'un élément</td></tr><tr><td>Nom de l'élément</td><td>Oui</td><td>Alphanumérique, longueur maximale 255</td><td>Nom de l'élément</td></tr><tr><td>Catégorie de l'élément</td><td>Oui</td><td>Alphanumérique, longueur maximale 255</td><td>Catégorie à laquelle cet élément appartient (par exemple, livres de cuisine, arts dramatiques...)</td></tr><tr><td>Description</td><td>Non</td><td>Alphanumérique, longueur maximale 4000</td><td>Description de cet élément</td></tr></table><br>La taille de fichier maximale est de 200 Mo.<br><br>Exemple :<br><pre>2406e770-c 769-4189-89de-1c9283f93a96, Clara Callan, livre<br>21bf8088-b6c0-4509-870c-e1c7ac78304a, The Forgetting Room: A Fiction (Byzantium Book), livre<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23, Spadework, livre<br>552a1940-21e4-4399-82bb-594b46d7ed54, Restraint of Beasts, livre</pre>|


**Réponse** :

Code d'état HTTP : 200

- `Feed\entry\content\properties\LineCount` : nombre de lignes acceptées.
- `Feed\entry\content\properties\ErrorCount` : nombre de lignes non insérées en raison d'une erreur.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
  		<subtitle type="text">Import catalog file</subtitle>
  		<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T06:58:04Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
 	 </entry>
	</feed>


###Importation de données d'utilisation

####Téléchargement d’un fichier
Cette section indique comment télécharger des données d'utilisation à l'aide d'un fichier. Vous pouvez appeler cette API plusieurs fois avec les données d'utilisation. Toutes les données d'utilisation sont enregistrées pour tous les appels.

| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle (respecte la casse) |
| filename | Identificateur textuel du catalogue.<br>Seuls les lettres (A-Z, a-z), les nombres (0-9), les tirets (-) et les traits de soulignement (\_) sont autorisés.<br>Longueur maximale : 50 | | apiVersion | 1.0 | ||| | Corps de la demande | Données d’utilisation. Format :<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Nom</th><th>Obligatoire</th><th>Type</th><th>Description</th></tr><tr><td>ID utilisateur</td><td>Oui</td><td>Alphanumérique</td><td>Identificateur unique d’un utilisateur</td></tr><tr><td>ID de l’élément</td><td>Oui</td><td>Alphanumérique, longueur maximale 50</td><td>Identificateur unique d’un élément</td></tr><tr><td>Heure</td><td>Non</td><td>Date au format : AAAA/MM/JJTHH:MM:SS (par exemple, 2013/06/20T10:00:00)</td><td>Heure des données</td></tr><tr><td>Événement</td><td>Non, mais s’il est indiqué, la date doit l’être également</td><td>Une des valeurs suivantes :<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>Taille maximale de fichier de 200 Mo.<br><br>Exemple :<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Réponse** :

Code d'état HTTP : 200

- `Feed\entry\content\properties\LineCount` : nombre de lignes acceptées.
- `Feed\entry\content\properties\ErrorCount` : nombre de lignes non insérées en raison d'une erreur.
- `Feed\entry\content\properties\FileId` : identificateur de fichier.


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####Utilisation de l'acquisition de données
Cette section explique comment envoyer des événements en temps réel à Azure Machine Learning Recommendations, généralement à partir de votre site web.

| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
|||
|Corps de la demande| Entrée de données d'événement pour chaque événement à envoyer. Pour une même session d'utilisateur ou de navigateur, vous devez envoyer le même ID dans le champ SessionId. (Consultez l'exemple du corps d'événement ci-dessous.)|


- Exemple pour l'événement « Click » :

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		<SessionId>11112222</SessionId>
		<EventData>
		<EventData>
		<Name>Click</Name>
		<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
		</EventData>
		</EventData>
		</Event>

- Exemple pour l'événement « RecommendationClick » :

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RecommendationClick</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Exemple pour l'événement « AddShopCart » :

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Exemple pour l'événement « RemoveShopCart » :

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RemoveShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Exemple pour l’événement « Purchase » :

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		<SessionId>11112222</SessionId>
		<EventData>
		<EventData>
			<Name>Purchase</Name> 
			<PurchaseItems>
			<PurchaseItems>
				<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
				<Count>3</Count>
			</PurchaseItems>
		</PurchaseItems>
		</EventData>
		</EventData>
		</Event>

- Exemple d'envoi de 2 événements « Click » et « AddShopCart » :

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>Click</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      	<ItemName>itemName</ItemName>
      	<ItemDescription>item description</ItemDescription>
      	<ItemCategory>category</ItemCategory>
    	</EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    	</EventData>
  		</EventData>
		</Event>

**Réponse** : Code d'état HTTP : 200

###Génération d'un modèle de recommandation

| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId |	Identificateur unique du modèle (respecte la casse) |
| userDescription | Identificateur textuel du catalogue. Notez que si vous utilisez des espaces, vous devez plutôt l'encoder avec %20. Consultez l'exemple ci-dessus.<br>Longueur maximale : 50 |
| apiVersion | 1\.0 |
|||
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

Il s'agit d'une API asynchrone. La réponse obtenue est un ID de build. Pour savoir à quel moment la build s'est terminée, vous devez appeler l'API « Get Builds Status of a Model » (obtention de l'état de build d'un modèle) et rechercher cet ID de build dans la réponse. Notez que l'exécution d'une build peut nécessiter de quelques minutes à plusieurs heures selon la taille des données.

Vous ne pouvez pas utiliser de recommandations tant que la build n'est pas terminée.

État de build valide :

- Create : le modèle a été créé.
- Queued : la build de modèle a été déclenchée et mise en file d'attente.
- Building : le modèle est en cours de génération.
- Success : la build a été correctement exécutée.
- Error : la build s'est terminée par un échec.
- Cancelled : la build a été annulée.
- Cancelling : la build est en cours d’annulation.


Notez que l'ID de build se trouve sous le chemin suivant : `Feed\entry\content\properties\Id`

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###Obtention de l’état de build d’un modèle

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|



|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle (respecte la casse) |
|	onlyLastBuild |	Indique s'il faut retourner l'historique de build du modèle en totalité ou uniquement l'état de la build la plus récente. |
|	apiVersion |	1\.0 |


**Réponse** :

Code d'état HTTP : 200

La réponse inclut une entrée par build. Chaque entrée comprend les données suivantes :

- `feed/entry/content/properties/UserName` : nom de l'utilisateur.
- `feed/entry/content/properties/ModelName` : nom du modèle.
- `feed/entry/content/properties/ModelId` : identificateur unique du modèle.
- `feed/entry/content/properties/IsDeployed` : indique si la build est déployée (« build active »).
- `feed/entry/content/properties/BuildId` : identificateur unique de la build.
- `feed/entry/content/properties/BuildType` : type de build.
- `feed/entry/content/properties/Status` : état de la build. Celui-ci peut avoir l’une des valeurs suivantes : Error, Building, Queued, Cancelling, Cancelled, Success.
- `feed/entry/content/properties/StatusMessage` : message d'état détaillé (s'applique uniquement à des états spécifiques).
- `feed/entry/content/properties/Progress` : progression de l'exécution de la build (%).
- `feed/entry/content/properties/StartTime` : heure de début de l'exécution de la build.
- `feed/entry/content/properties/EndTime` : heure de fin de l'exécution de la build.
- `feed/entry/content/properties/ExecutionTime` : durée de la build.
- `feed/entry/content/properties/ProgressStep` : détails sur l’étape actuelle d’une build.

État de build valide :
- Created : l’entrée de demande de build a été créée.
- Queued : la demande de build a été déclenchée et mise en attente.
- Building : la build est en cours.
- Success : la build a été correctement exécutée.
- Error : la build s'est terminée par un échec.
- Cancelled : la build a été annulée.
- Cancelling : la build est en cours d’annulation.

Valeurs valides pour le type de build :
- Rank - Build de classement. (Pour plus d’informations sur les builds de classement, consultez le document « Documentation sur les API Machine Learning Recommandation »).
- Recommendation - Build de recommandation.
- Fbt - Build fréquemment achetés ensemble.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get builds status of a model</subtitle>
	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T17:51:10Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###Obtention de recommandations

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|



|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle (respecte la casse) |
| itemIds | Liste des éléments séparés par des virgules faisant l’objet d’une recommandation.<br>Longueur maximale : 1024 |
| numberOfResults | Nombre de résultats requis |
| includeMetatadata | Utilisation ultérieure, toujours false |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200

La réponse inclut une entrée par élément recommandé. Chaque entrée comprend les données suivantes :

- `Feed\entry\content\properties\Id` : ID d’élément recommandé
- `Feed\entry\content\properties\Name` : nom de l’élément
- `Feed\entry\content\properties\Rating` : évaluation de la recommandation ; plus le nombre est élevé, plus le niveau de confiance est élevé.
- `Feed\entry\content\properties\Reasoning` : raisonnement de la recommandation (par exemple, pour expliquer les recommandations)

OData XML

L’exemple de réponse ci-dessous comprend 10 éléments recommandés :

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###Mise à jour du modèle
Vous pouvez mettre à jour la description du modèle ou l’identifiant de la build active. *Identifiant de build active* : pour chaque modèle, chaque build possède un identifiant de build. L'ID de build active correspond à la première build réussie de chaque nouveau modèle. Une fois que vous avez un ID de build active et que vous effectuez d'autres builds pour le même modèle, vous pouvez le définir explicitement comme ID de build par défaut. Quand vous utilisez des recommandations, si vous ne spécifiez pas l’identifiant de build que vous souhaitez utiliser, un identifiant par défaut est automatiquement utilisé.

Ce mécanisme vous permet, une fois que vous disposez d'un modèle de recommandation en production, de générer de nouveaux modèles et de les tester avant de les passer en production.

| Méthode HTTP | URI |
|:--------|:--------|
|PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|


|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| id | Identificateur unique du modèle (respecte la casse) |
| apiVersion | 1\.0 |
|||
| Corps de la requête | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>Notez que les balises XML Description et ActiveBuildId sont facultatives. Si vous ne souhaitez pas définir Description ou ActiveBuildId, supprimez la balise entière. |

**Réponse** :

Code d'état HTTP : 200

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Update an Existing Model</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
 	 <updated>2014-10-05T10:27:17Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
	</feed>

##Informations juridiques
Ce document est fourni « en l'état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Certains exemples sont fournis à titre indicatif uniquement et sont fictifs. Toute association ou lien est purement involontaire ou fortuit. Ce document ne vous accorde aucun droit légal à la propriété intellectuelle pour un produit Microsoft. Vous pouvez copier et utiliser ce document pour un usage interne, à titre de référence. © 2014 Microsoft. Tous droits réservés.
 

<!---HONumber=AcomDC_0615_2016-->