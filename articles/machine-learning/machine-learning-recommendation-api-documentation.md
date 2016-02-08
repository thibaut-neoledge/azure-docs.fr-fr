<properties 
	pageTitle="Documentation sur les API Machine Learning Recommendations | Microsoft Azure" 
	description="Documentation relative aux API Azure Machine Learning Recommendations pour un moteur de recommandations disponible dans Microsoft Azure Marketplace." 
	services="machine-learning" 
	documentationCenter="" 
	authors="AharonGumnik" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/10/2015" 
	ms.author="LuisCa"/>

#Documentation sur les API Azure Machine Learning Recommendations

Ce document décrit les API Microsoft Azure Machine Learning Recommendations.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1\. Présentation générale
Ce document fait référence aux API. Nous vous recommandons de commencer par consulter le document « Azure Machine Learning Recommendation – Démarrage rapide ».

Les API Azure Machine Learning Recommendations peuvent être divisées en groupes logiques comme ci-après :

- <ins>Limitations</ins> : limitations concernant les API Recommandations.
- <ins>Informations générales</ins> : informations sur l'authentification, l’URI de service et le contrôle de version.
- <ins>Modèle – De base</ins> : API permettant d'effectuer des opérations de base sur un modèle (par exemple, création, mise à jour et suppression d'un modèle).
- <ins>Modèle – Avancé</ins> : API permettant d'obtenir des analyses de données avancées sur le modèle.
- <ins>Règles métiers de modèle</ins> : API permettant de gérer des règles métiers sur les résultats des recommandations de modèle.
- <ins>Catalogue</ins> : API permettant d'effectuer des opérations de base sur un catalogue de modèles. Un catalogue contient des informations de métadonnées sur les éléments des données d'utilisation.
- <ins>Fonctionnalité</ins> : API permettant d’obtenir des analyses sur des éléments du catalogue, ainsi qu’une méthode d’utilisation de ces informations pour créer de meilleures recommandations.
- <ins>Données d'utilisation</ins> : API permettant d'effectuer des opérations de base sur les données d'utilisation de modèle. Les données d'utilisation dans leur forme élémentaire consistent en des lignes composées de paires de &#60;userId&#62;,&#60;itemId&#62;.
- <ins>Build</ins> : API permettant de déclencher une build de modèle et d'effectuer des opérations de base en rapport avec cette build. Vous pouvez déclencher une build de modèle dès que vous avez des données d'utilisation utiles.
- <ins>Recommendation</ins> : API permettant d'utiliser des recommandations au terme de l'exécution de la build d'un modèle.
- <ins>Données utilisateur</ins> : API permettant d’extraire des informations relatives aux données d’utilisation de l’utilisateur.
- <ins>Notifications</ins> : API permettant de recevoir des notifications sur les problèmes liés à vos opérations d'API. (Par exemple, si vous signalez les données d'utilisation via acquisition de données et que la plupart des événements traités échouent, une notification d'erreur est déclenchée.)

##2\. Limites

- Le nombre maximal de modèles par abonnement est de 10.
- Le nombre maximal de builds par modèle est de 20.
- Le nombre maximal d'éléments pouvant être contenus dans un catalogue est de 100 000.
- La quantité maximale de points d'utilisation conservée est d'environ 5 000 000. Le plus ancien est supprimé quand des nouveaux sont téléchargés ou signalés.
- La taille maximale des données pouvant être envoyées dans POST (par exemple, importation des données de catalogue ou des données d'utilisation) est de 200 Mo.
- Le nombre de transactions par seconde pour une build de modèle de recommandation inactive est d'environ 2 TPS. Une build de modèle de recommandation active peut prendre en charge jusqu'à 20 TPS.
- Le nombre maximal d'éléments qui peuvent être demandés lors de l'obtention de recommandations est 150.

##3\. API – Informations générales

###3\.1. Authentification
Respectez les instructions de Microsoft Azure Marketplace concernant l'authentification. Le Marketplace prend en charge les méthodes d'authentification De base et OAuth.

###3\.2. URI de service
Les URI racines de service des API Azure Machine Learning Recommendations se trouvent [ici](https://api.datamarket.azure.com/amla/recommendations/v3/).

L'URI de service complet est exprimée à l'aide des éléments de la spécification OData.

###3\.3. Version de l'API
À la fin de chaque appel d'API doit se trouver un paramètre de requête appelé apiVersion qui doit avoir la valeur 1.0.

###3\.4. Respect de la casse des ID
Les ID, quelle que soit l'API qui les retourne, respectent la casse. Ils doivent donc être utilisés comme tels quand ils sont passés en tant que paramètres dans les appels d'API ultérieurs. Par exemple, les ID de modèle et de catalogue respectent la casse.

##4\. Qualité des recommandations et éléments froids

###4\.1. Qualité de la recommandation

La création d'un modèle de recommandation est généralement suffisante pour permettre au système de fournir des recommandations. Toutefois, la qualité de la recommandation varie en fonction de l'utilisation traitée et de la couverture du catalogue. Par exemple, si vous avez beaucoup d'éléments froids (éléments n'étant pas beaucoup utilisés), le système aura des difficultés à émettre une recommandation pour de tels éléments ou à utiliser l'un de ces éléments en tant qu'élément recommandé. Pour remédier au problème des éléments froids, le système autorise l'utilisation des métadonnées des éléments pour améliorer les recommandations. Ces métadonnées sont appelées « caractéristiques ». L'auteur d'un livre et l'acteur d'un film sont des exemples de caractéristiques. Les caractéristiques sont fournies via le catalogue sous la forme de chaînes clé/valeur. Pour obtenir le format complet du fichier catalogue, consultez la [section Importer des données de catalogue](#81-import-catalog-data).

###4\.2. Build de classement

L'amélioration du modèle de recommandation exige le recours à des caractéristiques significatives. Dans cette optique, une nouvelle build a été introduite : la build de classement. Cette build permet de classer l'utilité des caractéristiques. Une caractéristique est significative si elle reçoit un score d'au moins 2 de la build de classement. Après avoir déterminé les caractéristiques significatives, déclenchez une build de recommandation avec la liste (ou sous-liste) des caractéristiques significatives. Il est possible d'utiliser ces caractéristiques pour améliorer à la fois les éléments chauds et les éléments froids. Pour utiliser des caractéristiques pour des éléments chauds, vous devez configurer le paramètre de build `UseFeatureInModel`. Pour utiliser des caractéristiques pour des éléments froids, vous devez activer le paramètre de build `AllowColdItemPlacement`. Remarque : il est impossible d'activer `AllowColdItemPlacement` sans activer `UseFeatureInModel`.

###4\.3. Raisonnement de la recommandation

Le raisonnement de la recommandation est un autre aspect de l'utilisation des caractéristiques. En effet, le moteur Azure Machine Learning Recommendations peut utiliser des caractéristiques pour fournir des explications sur la recommandation (ou « raisonnement »), renforçant ainsi la confiance de l'utilisateur de la recommandation envers l'élément recommandé. Pour activer le raisonnement, les paramètres `AllowFeatureCorrelation` et `ReasoningFeatureList` doivent être configurés avant la demande d'une build de recommandation.


##5\. Modèle – De base

###5\.1. Création de modèle
Crée une demande de création de modèle.

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

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###5\.2. Obtention de modèle
Crée une demande d'obtention de modèle.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Exemple :<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	id |	Identificateur unique du modèle (respecte la casse) |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

Les données du modèle se trouvent sous les éléments suivants :

- `feed/entry/content/properties/Id` : ID unique du modèle.
- `feed/entry/content/properties/Name` : nom du modèle.
- `feed/entry/content/properties/Date` : date de création du modèle.
- `feed/entry/content/properties/Status` : état du modèle. Celui-ci peut avoir l'une des valeurs suivantes :
    - Created : le modèle est créé, mais il ne contient pas de données de catalogue et d'utilisation.
	- ReadyForBuild : le modèle est créé et il contient des données de catalogue et d'utilisation.
- `feed/entry/content/properties/HasActiveBuild` : indique si le modèle a été correctement généré.
- `feed/entry/content/properties/BuildId` : ID de build active du modèle.
- `feed/entry/content/properties/Mpr` : classement centile moyen du modèle (MPR, Mean Percentile Ranking). Pour plus d'informations, consultez ModelInsight.
- `feed/entry/content/properties/UserName` : nom d'utilisateur interne du modèle.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Get A List Of All Models</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T14:35:51Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
		<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
	  </entry>
	</feed>

###5\.3. Obtention de tous les modèles
Récupère tous les modèles de l'utilisateur actuel.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>Exemple :<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

- `feed/entry/content/properties/Id` : ID unique du modèle.
- `feed/entry/content/properties/Name` : nom du modèle.
- `feed/entry/content/properties/Date` : date de création du modèle.
- `feed/entry/content/properties/Status` : état du modèle. Celui-ci peut avoir l'une des valeurs suivantes :
  - Created : le modèle est créé, mais il ne contient pas de données de catalogue et d'utilisation.
  - ReadyForBuild : le modèle est créé et il contient des données de catalogue et d'utilisation.
- `feed/entry/content/properties/HasActiveBuild` : indique si le modèle a été correctement généré.
- `feed/entry/content/properties/BuildId` : ID de build active du modèle.
- `feed/entry/content/properties/Mpr` : MPR du modèle (pour plus d'informations, consultez ModelInsight).
- `feed/entry/content/properties/UserName` : nom d'utilisateur interne du modèle.
- `feed/entry/content/properties/UsageFileNames` : liste de fichiers d'utilisation du modèle séparés par des virgules.
- `feed/entry/content/properties/CatalogId` : ID de catalogue du modèle.
- `feed/entry/content/properties/Description` : description du modèle.
- `feed/entry/content/properties/CatalogFileName` : nom de fichier de catalogue du modèle.

OData XML


    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get A List Of All Models</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-28T14:35:51Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
					<d:Name m:type="Edm.String">vah-11111</d:Name>
					<d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
					<d:Status m:type="Edm.String">ReadyForBuild</d:Status>
					<d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
					<d:BuildId m:type="Edm.String">-1</d:BuildId>
					<d:Mpr m:type="Edm.String">0</d:Mpr>
					<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
					<d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
					<d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
					<d:Description m:type="Edm.String">short description</d:Description>
					<d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
				</m:properties>
			</content>
		</entry>
	</feed>

###5\.4. Mise à jour du modèle

Vous pouvez mettre à jour la description du modèle ou l'ID de build active.<br> <ins>ID de build active</ins> : chaque build de chaque modèle possède un ID de build. L'ID de build active correspond à la première build réussie de chaque nouveau modèle. Une fois que vous avez un ID de build active et que vous effectuez d'autres builds pour le même modèle, vous pouvez le définir explicitement comme ID de build par défaut. Quand vous utilisez des recommandations, si vous ne spécifiez pas l'ID de build à utiliser, l'ID par défaut est automatiquement sélectionné.<br> Ce mécanisme vous permet, une fois que vous disposez d'un modèle de recommandation en production, de générer de nouveaux modèles et de les tester avant de les passer en production.


| Méthode HTTP | URI |
|:--------|:--------|
|PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>Exemple :<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	id | Identificateur unique du modèle (respecte la casse) |
|	apiVersion | 1\.0 |
|
| Corps de la requête | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>Notez que les balises XML Description et ActiveBuildId sont facultatives. Si vous ne souhaitez pas définir Description ou ActiveBuildId, supprimez la balise entière.|

**Réponse** :

Code d'état HTTP : 200

###5\.5. Suppression de modèle
Supprime un modèle existant par ID.

| Méthode HTTP | URI |
|:--------|:--------|
|SUPPRIMER |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Exemple :<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	id |	Identificateur unique du modèle (respecte la casse) |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Delete Model by Id</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T10:39:33Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
		<d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
	  </entry>
	</feed>

##6\. Modèle – Avancé

###6\.1. Analyse des données de modèle
Retourne des données statistiques sur les données d'utilisation qui ont servi à générer ce modèle.

Disponible uniquement pour la build de recommandation.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Exemple :<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

Les données sont retournées en tant que collection de propriétés.

- `feed/entry/id/content/properties/key` : contient le nom de la propriété.
- `feed/entry/id/content/properties/value` : contient la valeur de la propriété.

Le tableau ci-dessous décrit la valeur de chaque clé.

|Clé|Description|
|:-----|:----|
| AvgItemLength | Nombre moyen d'utilisateurs distincts par élément. |
| AvgUserLength | Nombre moyen d'éléments distincts par utilisateur. |
| DensificationNumberOfItems | Nombre d'éléments après nettoyage des éléments ne pouvant pas être modélisés. |
| DensificationNumberOfUsers | Nombre de points d'utilisation après nettoyage des utilisateurs et des éléments ne pouvant pas être modélisés. |
| DensificationNumberOfRecords | Nombre de points d'utilisation après nettoyage des utilisateurs et des éléments ne pouvant pas être modélisés. |
| MaxItemLength | Nombre d'utilisateurs distincts pour l'élément le plus populaire. |
| MaxUserLength | Nombre maximal d'éléments distincts pour un utilisateur. |
| MinItemLength | Nombre maximal d'utilisateurs distincts pour un élément. |
| MinUserLength | Nombre minimal d'éléments distincts pour un utilisateur. |
| RawNumberOfItems | Nombre d’éléments dans les fichiers d’utilisation. |
| RawNumberOfUsers | Nombre de points d'utilisation avant nettoyage. |
| RawNumberOfRecords | Nombre de points d'utilisation avant nettoyage. |
| SamplingNumberOfItems | N/A |
| SamplingNumberOfRecords | N/A |
| SamplingNumberOfUsers | N/A |

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get data insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
	</entry>
	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
	<content type="application/xml">
		<m:properties>
			<d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
			<d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxItemLength</d:Key>
				<d:Value m:type="Edm.String">4,704</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    	<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxUserLength</d:Key>
				<d:Value m:type="Edm.String">190</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinItemLength</d:Key>
				<d:Value m:type="Edm.String">8</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinUserLength</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    </feed>

###6\.2. Informations de modèle
Retourne une analyse du modèle sur la build active ou sur une build spécifique (si une telle build est fournie).

Disponible uniquement pour la build de recommandation.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |Avec une ID de build active :<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>Avec une ID de build spécifique :<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
|	buildId |	Facultatif. Numéro qui identifie une build réussie. |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

Les données sont retournées en tant que collection de propriétés.

- `feed/entry/id/content/properties/key`
- `feed/entry/id/content/properties/value`


Le tableau ci-dessous décrit la valeur de chaque clé.

| Clé | Description |
|:---- |:----|
| CatalogCoverage | Partie du catalogue pouvant être modélisée avec des modèles d'utilisation. Le reste des éléments nécessite des caractéristiques basées sur le contenu. |
| Mpr | Classement centile moyen du modèle. Plus la valeur est faible, mieux c'est. |
| NumberOfDimensions | Nombre de dimensions utilisées par l'algorithme de factorisation de matrice. |


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get model insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">CatalogCoverage</d:Key>
				<d:Value m:type="Edm.String">1.000</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">Mpr</d:Key>
				<d:Value m:type="Edm.String">0.367</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###6\.3. Obtention d'exemple de modèle
Obtient un exemple du modèle de recommandation.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Exemple :<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>Avec une ID de build spécifique :<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>Exemple :<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

OData XML

La réponse est retournée au format texte brut :

<pre>
Level 1
---------------
655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel
	655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel Rating: 0.5215
	3f471802-f84f-44a0-99c8-6d2e7418eec1, Black Hawk Down: A Story of Modern War Rating: 0.5151
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Rating: 0.5148
	6afc18e4-8c2a-43d1-9021-57543d6b11d8, Imajica Rating: 0.5146
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Rating: 0.514
56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai
	56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai Rating: 0.5218
	53156702-cc0c-443d-b718-6fb74b2491d3, Son of \ Rating: 0.5212
	fb8cf7a6-8719-46ee-97d4-92f931d77a3a, Smoke and Mirrors: Short Fictions and Illusions Rating: 0.5188
	8f5fe006-79e4-4679-816b-950989d1db4b, A Place I've Never Been (Contemporary American Fiction) Rating: 0.5156
	d8db4583-cc0f-49ce-bc95-b7fa3491623f, Happiness: A Novel Rating: 0.5156
50471eec-9aeb-4900-84d7-21567ab18546, If the Buddha Dated: A Handbook for Finding Love on a Spiritual Path
	cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Divine Secrets of the Ya-Ya Sisterhood: A Novel Rating: 0.5266
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5252
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5244
	e2cbf7ad-0636-4117-8b30-298da6df7077, Animal Dreams Rating: 0.5227
	6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Confessions of an Ugly Stepsister: A Novel Rating: 0.5222
5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club)
	5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club) Rating: 0.537
	5dcbac37-2946-4f2a-a0b3-bbe710f9409a, Up Island: A Novel Rating: 0.5277
	bc5b69db-733b-4346-adde-3927544258f7, Downtown Rating: 0.5275
	31fe5c63-3e5a-48d0-802b-d3b0f989a634, Have a Nice Day: A Tale of Blood and Sweatsocks Rating: 0.5252
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Rating: 0.5238
68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived
	68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived Rating: 0.5379
	6724862e-e4e7-4022-9614-1468d8b902ff, Little House on the Prairie Rating: 0.5345
	cdedb837-1620-496d-94c4-6ccfed888320, Little House in the Big Woods Rating: 0.5325
	382164ba-406b-4187-b726-d7a54b9d790d, The Tao of Pooh Rating: 0.5309
	6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5, On the Banks of Plum Creek Rating: 0.5285
37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships
	37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars, Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships Rating: 0.5397
	f2be16d4-5faf-4d32-ab83-7ba74d29261e, Politically Correct Bedtime Stories: Modern Tales for Our Life and Times Rating: 0.5207
	ef732c5c-334b-4d6b-ab82-7255eb7286d0, Honor Among Thieves Rating: 0.5195
	0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, The Giving Tree Rating: 0.5194
	883b360f-8b42-407f-b977-2f44ad840877, Scary Stories to Tell in the Dark: Collected from American Folklore (Scary Stories) Rating: 0.5184
ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball
	d008dae9-c73a-40a1-9a9b-96d5cf546f36, The Gulag Archipelago 1918-1956: An Experiment in Literary Investigation I-II Rating: 0.5416
	ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball Rating: 0.5403
	49dec30e-0adb-411a-b186-48eaabf6f8bc, Fatherland Rating: 0.5394
	cc7964fd-d30f-478e-a425-93ddbdf094ed, Magic the Gathering: Arena Vol. 1 Rating: 0.5379
	8a1e9f36-97af-4614-bed9-24e3940a05f3, More Sniglets: Any Word That Doesn't Appear in the Dictionary but Should Rating: 0.5377
12a6d988-be21-4a09-8143-9d5f4261ba16, A Dream of Eagles
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Rating: 0.5417
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Rating: 0.5416
	1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, The Family Rating: 0.5371
	56daeffe-7d48-43cd-8ef8-7dffd0c103d3, Kilo Class Rating: 0.5366
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Rating: 0.5366
df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish
	56d33036-dfda-46b9-8e2a-76cb03921bb0, The X-Files: Ground Zero Rating: 0.5417
	0780cde8-6529-4e1d-b6c6-082c1b80e596, Twelve Red Herrings Rating: 0.5416
	df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish Rating: 0.5408
	400fe331-2c35-490c-adbc-b28b4b73d56c, Shall We Tell the President? Rating: 0.5383
	f86ad7d0-5c03-42b3-aebf-13d44aec8b30, Shades of Grace Rating: 0.5358
de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology
	de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology Rating: 0.5422
	b303538f-e2c6-4a2c-b425-8d21e684fc3e, My Uncle Oswald Rating: 0.5385
	34b84627-48af-4a4c-96c4-b26fb3863f56, Midnight In the Garden of Good and Evil Rating: 0.5379
	306cbaa7-b1a8-4142-9d55-e11b5018a7a8, The Street Lawyer Rating: 0.5376
	e53b4baa-8c09-45c4-95c0-b6a26b98770b, Miss Smillas Feeling for Snow Rating: 0.5367

Level 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony)
	352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony) Rating: 0.5425
	74c49398-bc10-4af5-a658-a996a1201254, Children of the Storm (Peters Elizabeth) Rating: 0.5387
	9ba80080-196e-43fd-8025-391d963f77e7, The Floating Girl Rating: 0.5372
	e68f81d5-7745-4cc7-b943-fedb8fcc2ced, Killer Smile (Scottoline Lisa) Rating: 0.5353
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Rating: 0.5332
c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Rating: 0.5433
	c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days Rating: 0.543
	a00ae6ad-4a7f-4211-9836-75ce8834eb11, Sniglets (Snig'lit: Any Word That Doesn't Appear in the Dictionary But Should) Rating: 0.5327
	6f6e192e-0d64-49ca-9b63-f09413ea1ee6, Politically Correct Holiday Stories: For an Enlightened Yuletide Season Rating: 0.5307
	798051a8-147d-4d46-b0dc-e836325029e6, AGE OF INNOCENCE (MOVIE TIE-IN) Rating: 0.5301
73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics)
	cba8163f-6536-436b-8130-47b4a43c827f, Trust No One (The Official Guide to the X-Files Vol. 2) Rating: 0.5434
	5708e4cb-2492-49c0-94a8-cc413eec5d89, Small Gods (Discworld Novels (Paperback)) Rating: 0.5406
	73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics) Rating: 0.5403
	d885b0bd-ae4b-452d-bdf2-faa90197dbc9, The Color of Magic Rating: 0.539
	b133a9c4-4784-4db3-b100-d0d6dffb94d2, The Truth Is Out There (The Official Guide to the X-Files Vol. 1) Rating: 0.5367
271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings
	271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings Rating: 0.5445
	2de1c354-90ff-47c5-a0db-1bad7d88ef94, The Salaryman's Wife (Children of Violence Series) Rating: 0.5329
	d279416e-19c0-43f8-9ec9-a585947879ca, Zen Attitude Rating: 0.5316
	c8f854d7-3de3-4b23-8217-f4f851670fd4, Revenge of the Cootie Girls: A Robin Hudson Mystery (Robin Hudson Mysteries (Paperback)) Rating: 0.5305
	8ef4751c-7074-409e-a3ac-d49b222fc864, Where the Wild Things Are Rating: 0.5289
9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God
	9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God Rating: 0.5446
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Rating: 0.5389
	65ecbdd1-131c-40c3-a3d6-d86ca281377a, The God of Small Things Rating: 0.5387
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Rating: 0.5355
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5344
5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback))
	5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback)) Rating: 0.5446
	57169b2b-9a8a-486b-9aac-1ed98ce57168, Final Appeal Rating: 0.5332
	efcb1bc4-7278-4a8f-b491-befde02070d6, Moment of Truth Rating: 0.5329
	1efa91a2-993b-4c43-9f5c-3454fc12612d, Burn Factor Rating: 0.5309
	24c59962-458a-4ec8-b95d-d694e861919c, At Home in Mitford (The Mitford Years) Rating: 0.5303
4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl
	4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl Rating: 0.5449
	cd5f2c03-20cb-43be-a1fb-3b4233e63222, Pigs in Heaven Rating: 0.5329
	19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Love in the Time of Cholera (Penguin Great Books of the 20th Century) Rating: 0.5267
	15689d09-c711-4844-84d8-130a90237b26, Bel Canto Rating: 0.5245
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5235
98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories
	f874b5a3-5d40-4436-94ff-0fa1c090ddf5, The Sun Also Rises (A Scribner classic) Rating: 0.5451
	98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories Rating: 0.5442
	0ce0014a-9a48-4013-a08a-7f2c11877930, H.M.S. Unseen Rating: 0.5421
	15316ca6-1e38-425f-893d-691944a47000, More Scary Stories To Tell In The Dark Rating: 0.5409
	329d5682-3dc3-4206-8aa2-eef4b1032258, Letters from the Earth Rating: 0.54
5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover))
	5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)) Rating: 0.5462
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5372
	604eb3bd-6026-4f51-bffd-9fb54f180400, Family Pictures: A Novel Rating: 0.5341
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Rating: 0.5334
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Rating: 0.5319
d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven
	d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven Rating: 0.5491
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5401
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Rating: 0.5393
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Rating: 0.5382
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5367

</pre>


##7\. Règles métiers de modèle

Voici les types de règles prises en charge : <strong>Liste de blocage</strong> : permet de fournir une liste d’éléments à ne pas retourner dans les résultats de la recommandation.

- <strong>Liste de blocage de fonctionnalités</strong> : permet de bloquer les éléments en fonction des valeurs de ses fonctionnalités.

*N’envoyez pas plus de 1000 éléments dans une même règle de liste de blocage, car votre appel pourrait dépasser le délai d’attente. Si vous avez besoin de bloquer plus de 1000 éléments, vous pouvez effectuer plusieurs appels de liste de blocage.*

- <strong>Upsale</strong> : permet de forcer le retour d'éléments dans les résultats de la recommandation.

- <strong>Liste blanche</strong> : permet de suggérer seulement des recommandations dans une liste d’éléments.

- <strong>Liste blanche de fonctionnalités</strong> : permet de recommander seulement des éléments qui ont des valeurs de fonctionnalité spécifiques.

- <strong>PerSeedBlockList</strong> : permet de fournir, par élément, une liste d'éléments ne pouvant pas être retournés dans les résultats de la recommandation.




###7\.1. Obtention de règles de modèle

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Exemple :<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

- `feed/entry/content/properties/Id` : identificateur unique de cette règle.
- `feed/entry/content/properties/Type` : type de la règle.
- `feed/entry/content/properties/Parameter` : paramètre de la règle.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get a list of rules for a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T12:58:57Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000043</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000044</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.2. Ajout de règle

| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/AddRule?apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
|
| Corps de la requête | 
<ins>Chaque fois que vous fournissez des ID d’élément pour des règles métier, veillez à utiliser l’ID externe de l’élément (le même ID que vous avez utilisé dans le fichier de catalogue)</ins><br> <ins>Pour ajouter une règle BlockList :</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins> <ins>Pour ajouter une règle FeatureBlockList :</ins><br> <br> `<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureBlockList</Type><Value>{"Name":"Movie_category","Values":["Adult","Drama"]}</Value></ApiFilter>`<br><br><ins> Pour ajouter une règle Upsale :</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"]}</Value></ApiFilter>`<br><br> <ins>Pour ajouter une règle WhiteList :</ins><br> `<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins> <ins>Pour ajouter une règle FeatureWhiteList :</ins><br> <br> `<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureWhiteList</Type><Value>{"Name":"Movie_rating","Values":["PG13"]}</Value></ApiFilter>`<br><br><ins> Pour ajouter une règle PerSeedBlockList :</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|


**Réponse** :

Code d'état HTTP : 200

L'API retourne la règle qui vient d'être créée avec ses détails. Les chemins d'accès suivants permettent de récupérer les propriétés de la règle :

- `feed/entry/content/properties/Id` : identificateur unique de cette règle.
- `feed/entry/content/properties/Type` : type de règle (BlockList ou Upsale).
- `feed/entry/content/properties/Parameter` : paramètre de la règle.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Add A Rule</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T11:13:28Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AddARuleEntity</title>
		<updated>2014-11-05T11:13:28Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000041</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.3. Suppression de règle

| Méthode HTTP | URI |
|:--------|:--------|
|SUPPRIMER |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
|	filterId |	Identificateur unique du filtre |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

###7\.4. Suppression de toutes les règles

| Méthode HTTP | URI |
|:--------|:--------|
|SUPPRIMER |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

##8\. Catalogue

###8\.1. Importation de données de catalogue

Si vous téléchargez plusieurs fichiers de catalogue dans le même modèle avec plusieurs appels, seuls les nouveaux éléments de catalogue sont insérés. Les éléments existants conservent leurs valeurs d'origine. Vous ne pouvez pas mettre à jour des données de catalogue à l'aide de cette méthode.

Les données de catalogue doivent être au format suivant :

- Sans caractéristiques : `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Avec caractéristiques : `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

Remarque : la taille de fichier maximale est de 200 Mo.

** Détails du format **

| Nom | Obligatoire | Type | Description |
|:---|:---|:---|:---|
| Item Id |Oui | [A-z], [a-z] [0-9], [\_] &#40;trait de soulignement&#41;, [-] &#40;tiret&#41;<br> Longueur maximale : 50 | Identificateur unique d’un élément. | | Nom de l’élément | Oui | Caractères alphanumériques<br> Longueur maximale : 255 | Nom de l’élément. | | Catégorie d’élément | Oui | Caractères alphanumériques <br> Longueur maximale : 255 | Catégorie à laquelle cet élément appartient (par exemple, livres de cuisine, théâtre...) ; peut être vide. | | Description | Non, sauf si les fonctionnalités sont présentes (mais peuvent être vides) | Caractères alphanumériques <br> Longueur maximale : 4000 | Description de cet élément. | | Liste des fonctionnalités | Non | Caractères alphanumériques <br> Longueur maximale : 4000 ; nombre maximal de fonctionnalités : 20 | | Liste de noms de fonctionnalité séparés par une virgule = valeur de fonctionnalité qui peut être utilisée pour améliorer la recommandation de modèle ; consultez la section [Rubriques avancées](#2-advanced-topics). |


| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
| filename | Identificateur textuel du catalogue.<br>Seuls les lettres (A-Z, a-z), nombres (0-9), tirets (-) et traits de soulignement (\_) sont autorisés.<br>Longueur maximale : 50 | | apiVersion | 1.0 | ||| | Corps de la requête | Exemple (avec fonctionnalités) :<br/>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book,the book description,author=Richard Wright,publisher=Harper Flamingo Canada,year=2001<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book,,author=Nick Bantock,publisher=Harpercollins,year=1997<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book,,author=Timothy Findley, publisher=HarperFlamingo Canada, year=2001<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book,the book description,author=Magnus Mills, publisher=Arcade Publishing, year=1998</pre> |


**Réponse** :

Code d'état HTTP : 200

L'API retourne un rapport de l'importation. `feed\entry\content\properties\LineCount` : nombre de lignes acceptées. `feed\entry\content\properties\ErrorCount` : nombre de lignes non insérées en raison d'une erreur.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Import catalog file</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-05T06:58:04Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
	<entry>
   	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ImportCatalogFileEntity2</title>
		<updated>2014-10-05T06:58:04Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:LineCount m:type="Edm.String">4</d:LineCount>
				<d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.2. Obtention de catalogue
Récupère tous les éléments de catalogue.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

La réponse inclut une entrée par élément de catalogue. Chaque entrée comprend les données suivantes :

- `feed/entry/content/properties/ExternalId` : ID externe de l'élément de catalogue, celui fourni par le client.
- `feed/entry/content/properties/InternalId` : ID interne de l'élément de catalogue, celui généré par Azure Machine Learning Recommendations.
- `feed/entry/content/properties/Name` : nom de l'élément de catalogue.
- `feed/entry/content/properties/Category` : catégorie de l'élément de catalogue.
- `feed/entry/content/properties/Description` : description de l'élément de catalogue.
- `feed/entry/content/properties/Metadata` : métadonnées de l'élément de catalogue.


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get All Catalog Items</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
		<entry>
        	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
				<d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
				<d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
				<d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
				<d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
				<d:Name m:type="Edm.String">Spadework</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
				<d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
				<d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.3. Obtention d'éléments de catalogue par jeton

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
|	token |	Jeton du nom de l'élément de catalogue. Doit contenir au moins 3 caractères. |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

La réponse inclut une entrée par élément de catalogue. Chaque entrée comprend les données suivantes :

- `feed/entry/content/properties/ExternalId` : ID externe de l'élément de catalogue, celui fourni par le client.
- `feed/entry/content/properties/InternalId` : ID interne de l'élément de catalogue, celui généré par Azure Machine Learning Recommendations.
- `feed/entry/content/properties/Name` : nom de l'élément de catalogue.
- `feed/entry/content/properties/Category` : catégorie de l'élément de catalogue.
- `feed/entry/content/properties/Description` : description de l'élément de catalogue.
- `feed/entry/content/properties/Metadata` : métadonnées de l'élément de catalogue.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get Catalog items that contain a token</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:48:19Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">CatalogItemsThatContainATokenEntity</title>
			<updated>2014-10-29T11:48:19Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    		<content type="application/xml">
      			<m:properties>
					<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
        			<d:Name m:type="Edm.String">Clara Callan</d:Name>
        			<d:Rating m:type="Edm.Double">0</d:Rating>
        			<d:Reasoning m:type="Edm.String"></d:Reasoning>
        			<d:Metadata m:type="Edm.String"></d:Metadata>
        			<d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
      			</m:properties>
			</content>
		</entry>
	</feed>

##9\. Données d'utilisation
###9\.1. Importation de données d'utilisation
####9\.1.1. Téléchargement d'un fichier
Cette section indique comment télécharger des données d'utilisation à l'aide d'un fichier. Vous pouvez appeler cette API plusieurs fois avec les données d'utilisation. Toutes les données d'utilisation sont enregistrées pour tous les appels.

| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
| filename | Identificateur textuel du catalogue.<br>Seuls les lettres (A-Z, a-z), les nombres (0-9), les tirets (-) et les traits de soulignement (_) sont autorisés.<br>Longueur maximale : 50 | | apiVersion | 1.0 | ||| | Corps de la demande | Données d’utilisation. Format :<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Nom</th><th>Obligatoire</th><th>Type</th><th>Description</th></tr><tr><td>ID utilisateur</td><td>Oui</td><td>[A-z], [a-z], [0-9], [_] &#40;Trait de soulignement&#41;, [-] &#40;Tiret&#41;<br> Longueur maximale : 255 </td><td>Identificateur unique d’un utilisateur.</td></tr><tr><td>ID d’élément</td><td>Oui</td><td>[A-z], [a-z], [0-9], [&#95;] &#40;Trait de soulignement&#41;, [-] &#40;Tiret&#41;<br> Longueur maximale : 50</td><td>Identificateur unique d’un élément.</td></tr><tr><td>Heure</td><td>Non</td><td>Date au format : AAAA/MM/JJTHH:MM:SS (par exemple, 2013/06/20T10:00:00)</td><td>Heure des données.</td></tr><tr><td>Événement</td><td>Non, mais s’il est indiqué, la date doit l’être également</td><td>Une des valeurs suivantes :<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>Taille de fichier maximale : 200 Mo<br><br>Exemple :<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Réponse** :

Code d'état HTTP : 200

- `Feed\entry\content\properties\LineCount` : nombre de lignes acceptées.
- `Feed\entry\content\properties\ErrorCount` : nombre de lignes non insérées en raison d'une erreur.
- `Feed\entry\content\properties\FileId` : identificateur de fichier.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####9\.1.2. Utilisation de l'acquisition de données
Cette section explique comment envoyer des événements en temps réel à Azure Machine Learning Recommendations, généralement à partir de votre site web.

| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
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
					<PurchaseItem>
						<ItemId>ABBF8081-C5C0-4F09-9701-E1C7AC78304A</ItemId>
						<Count>1</Count>
					</PurchaseItem>
					<PurchaseItem>
						<ItemId>21BF8088-B6C0-4509-870C-11C0AC7F304B</ItemId>
						<Count>3</Count>
					</PurchaseItem>
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

###9\.2. Liste des fichiers d'utilisation de modèle
Récupère les métadonnées de tous les fichiers d'utilisation du modèle.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	forModelId |	Identificateur unique du modèle |
|	apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

La réponse inclut une entrée par fichier d'utilisation. Chaque entrée comprend les données suivantes :

- `feed\entry\content\properties\Id` : ID du fichier utilisation.
- `feed\entry\content\properties\Length` : longueur du fichier utilisation en Mo.
- `feed\entry\content\properties\DateModified` : date de création du fichier d'utilisation.
- `feed\entry\content\properties\UseInModel` : indique si le fichier d'utilisation est utilisé dans le modèle.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of model's usage files info</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
			<updated>2014-10-30T09:40:25Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
					<d:Length m:type="Edm.Double">0</d:Length>
					<d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
					<d:UseInModel m:type="Edm.String">true</d:UseInModel>
				</m:properties>
			</content>
		</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
				<d:Length m:type="Edm.Double">0.001</d:Length>
				<d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
				<d:UseInModel m:type="Edm.String">true</d:UseInModel>
          	</m:properties>
		</content>
	</entry>
</feed>

###9\.3. Obtention de statistiques d'utilisation
Obtient les statistiques d'utilisation.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId |	Identificateur unique du modèle |
| startDate |	Date de début. Format : aaaa/MM/jjTHH:mm:ss |
| endDate |	Date de fin. Format : aaaa/MM/jjTHH:mm:ss |
| eventTypes |	Chaîne de types d'événements séparés par des virgules ou valeur null pour obtenir tous les événements |
| apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

Collection d'éléments clé/valeur. Chaque élément contient la somme des événements d'un type spécifique regroupés par heure.

- `feed\entry[i]\content\properties\Key` : contient l'heure (regroupement par heure) et le type d'événement.
- `feed\entry[i]\content\properties\Value` : nombre total d'événements.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-18T11:39:16Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
				<d:Value m:type="Edm.String">5</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
          	</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
            	<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
        </content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###9\.4. Obtention d'exemple de fichier d'utilisation
Récupère les 2 premiers Ko de contenu du fichier d'utilisation.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId |	Identificateur unique du modèle |
| fileId |	Identificateur unique du fichier d'utilisation du modèle |
| apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

La réponse est retournée au format texte brut : <pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 </pre>


###9\.5. Obtention de fichier d'utilisation de modèle
Récupère le contenu intégral du fichier d'utilisation.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| mid |	Identificateur unique du modèle |
| fid |	Identificateur unique du fichier d'utilisation du modèle |
| télécharger | 1 |
| apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

La réponse est retournée au format texte brut : <pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260965,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 102758,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 112602,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 163925,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 262998,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 144717,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 </pre>

###9\.6. Suppression de fichier d'utilisation
Supprime le fichier d'utilisation du modèle spécifié.

| Méthode HTTP | URI |
|:--------|:--------|
|SUPPRIMER |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId |	Identificateur unique du modèle |
| fileId | Identificateur unique du fichier à supprimer |
| apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200


###9\.7. Suppression de tous les fichiers d'utilisation
Supprime tous les fichiers d'utilisation du modèle.

| Méthode HTTP | URI |
|:--------|:--------|
|SUPPRIMER |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId |	Identificateur unique du modèle |
| apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

##10\. Caractéristiques
Cette section montre comment récupérer des informations sur les caractéristiques, notamment les caractéristiques importées et leurs valeurs, leur classement et la date à laquelle ce classement a été alloué. Les caractéristiques sont importées dans le cadre des données de catalogue, puis leur classement est associé au terme de l'exécution d'une build de classement. Le classement des caractéristiques peut varier selon le modèle des données d'utilisation et le type d'éléments. Toutefois, pour des données d'utilisation ou des éléments cohérents, les fluctuations de classement sont généralement minimes. Le classement des caractéristiques est représenté par une valeur non négative. La valeur 0 signifie que la caractéristique n'a pas été classée (ce qui se produit si vous appelez cette API avant la fin de la première build de classement). On parle d'actualisation du score pour désigner la date à laquelle le classement a été attribué.

###10\.1. Obtention d'informations sur les caractéristiques (pour la dernière build de classement)
Récupère des informations sur les caractéristiques, y compris le classement, pour la dernière build de classement réussie.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27`

| Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId |	Identificateur unique du modèle |
|samplingSize| Nombre de valeurs à inclure pour chaque caractéristique en fonction des données présentes dans le catalogue. <br/>Les valeurs possibles sont les suivantes :<br> -1 : tous les échantillons. <br>0 : aucun échantillonnage. <br>N : retourne N échantillons pour chaque nom de caractéristique.|
| apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |


**Réponse** :

Code d'état HTTP : 200

La réponse contient une liste d'entrées d'informations sur les caractéristiques. Chaque entrée contient :

- `feed/entry/content/m:properties/d:Name` : nom de la caractéristique.
- `feed/entry/content/m:properties/d:RankUpdateDate` : date à laquelle le classement a été alloué à cette caractéristique (ou « actualisation du score »). Une date historique (« 0001-01-01T00:00:00 ») signifie qu'aucune build de classement n'a été exécutée.
- `feed/entry/content/m:properties/d:Rank` : classement de la caractéristique (float). Un classement de 2.0 ou plus désigne une bonne caractéristique.
- `feed/entry/content/m:properties/d:SampleValues` : liste de valeurs séparées par des virgules, jusqu'à la taille d'échantillonnage demandée.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get the features of a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-01-08T13:15:02Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Author</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Publisher</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
		<contenttype="application/xml">
		<m:properties>
			<d:Name m:type="Edm.String">Year</d:Name>
			<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
			<d:Rank m:type="Edm.String">0</d:Rank>
			<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
		</m:properties>
		</content>
	</entry>
</feed>


###10\.2. Obtention d'informations sur les caractéristiques (pour une build de classement spécifique)

Récupère des informations sur les caractéristiques, y compris le classement, pour une build de classement spécifique.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=<rankBuildId>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=1000551&apiVersion=%271.0%27`

| Nom du paramètre |	Valeurs valides |
|:--------			|:--------			|
| modelId |	Identificateur unique du modèle |
|samplingSize| Nombre de valeurs à inclure pour chaque caractéristique en fonction des données présentes dans le catalogue.<br/> Les valeurs possibles sont les suivantes :<br> -1 : tous les échantillons. <br>0 : aucun échantillonnage. <br>N : retourne N échantillons pour chaque nom de caractéristique.|
|rankBuildId| Identificateur unique de la build de classement ou -1 pour la dernière build de classement|
| apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |


**Réponse** :

Code d'état HTTP : 200

La réponse contient une liste d'entrées d'informations sur les caractéristiques. Chaque entrée contient :

- `feed/entry/content/m:properties/d:Name` : nom de la caractéristique.
- `feed/entry/content/m:properties/d:RankUpdateDate` : date à laquelle le classement a été alloué à cette caractéristique (ou « actualisation du score »). Une date historique (« 0001-01-01T00:00:00 ») signifie qu'aucune build de classement n'a été exécutée.
- `feed/entry/content/m:properties/d:Rank` : classement de la caractéristique (float). Un classement de 2.0 ou plus désigne une bonne caractéristique.
- `feed/entry/content/m:properties/d:SampleValues` : liste de valeurs séparées par des virgules, jusqu'à la taille d'échantillonnage demandée.

OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get the features of a model</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:54:22Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Author</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">3.38867426</d:Rank>
					<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Publisher</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.67839336</d:Rank>
					<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Year</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.12352145</d:Rank>
					<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
				</m:properties>
			</content>
		</entry>
	</feed>


##11\. Créer

  Cette section explique les différentes API liées aux builds. Il existe 3 types de builds : une build de recommandation, une build de classement et une build FBT (fréquemment achetés ensemble).

L’objectif de la build de recommandation est de générer un modèle de recommandation utilisé pour les prévisions. Les prédictions (pour ce type de build) sont de deux types : * I2I - alias. Recommandations d’éléments selon des éléments donnés : sur la base d’un élément ou d’une liste d’éléments, cette option prédit une liste d’éléments susceptibles d’être très intéressants. * U2I - alias Recommandations d’éléments selon un utilisateur : sur la base d’un ID utilisateur (et éventuellement d’une liste d’éléments), cette option prédit une liste d’éléments susceptibles de présenter un intérêt élevé pour l’utilisateur donné (et son choix d’éléments supplémentaires). Les recommandations U2I se basent sur l’historique des éléments ayant présenté un intérêt pour l’utilisateur jusqu’au moment où le modèle a été généré.

Une build de classement est une build technique qui vous permet d'en savoir plus sur l'utilité de vos caractéristiques. En général, pour tirer le meilleur parti d'un modèle de recommandation impliquant des caractéristiques, vous devez procéder comme suit : - Déclencher une build de classement (sauf si le score de vos caractéristiques est stable) et attendre l'obtention du score des caractéristiques. - Récupérer le classement de vos caractéristiques en appelant l'API [Obtention d'informations sur les caractéristiques](#101-get-features-info-for-last-rank-build). - Configurer une build de recommandation avec les paramètres suivants :- `useFeatureInModel` : valeur True. - `ModelingFeatureList` : liste séparée par des virgules des caractéristiques avec un score de 2.0 ou plus (selon les classements récupérés lors de l'étape précédente). - `AllowColdItemPlacement` : valeur True. - Affecter éventuellement à `EnableFeatureCorrelation` la valeur True et à `ReasoningFeatureList` la liste des caractéristiques à utiliser pour fournir des explications (en général la même liste de caractéristiques que celle utilisée lors de la modélisation ou une sous-liste). -Déclencher la build de recommandation avec les paramètres configurés.

Remarque : si vous ne configurez aucun paramètre (par exemple, en appelant la build de recommandation sans paramètres) ou que vous ne désactivez pas explicitement l'utilisation de caractéristiques (par exemple, en affectant à `UseFeatureInModel` la valeur False), le système affecte aux paramètres liés aux caractéristiques les valeurs ci-dessus dans le cas d'une build de classement existante.

Aucune restriction n'empêche l'exécution simultanée d'une build de classement et d'une build de recommandation pour le même modèle. Toutefois, vous ne pouvez pas exécuter deux builds du même type sur le même modèle en parallèle.

Une build FBT (Frequently Bought Together) est un autre algorithme de génération de recommandations. Parfois qualifié de « conservateur », ce système de recommandation convient bien aux catalogues qui ne sont pas homogènes par nature (homogènes : livres, films, certains produits alimentaires, articles de mode ; non homogènes : ordinateur et périphériques, produits interdomaines, très diversifiés).

Remarque : si les fichiers d’utilisation que vous avez téléchargés contiennent le champ facultatif « type d’événement », seuls les événements « Purchase » sont utilisés pour la modélisation FBT. Si aucun type d’événement n’est fourni, tous les événements sont considérés comme achat (purchase).


####11\.1 Paramètres de build

Chaque type de build peut être configuré via un ensemble de paramètres (décrits ci-dessous). Si vous ne configurez pas les paramètres, le système leur attribue automatiquement des valeurs en fonction des informations présentes au moment du déclenchement de la build.

#####11\.1.1. Condenseur d'utilisation
Les utilisateurs ou éléments avec peu de points d'utilisation peuvent contenir plus de bruit que d'informations. Le système tente de prédire le nombre minimal de points d'utilisation par utilisateur/élément à utiliser dans un modèle. Ce nombre est compris dans la plage définie par les paramètres ItemCutoffLowerBound et ItemCutoffUpperBound pour les éléments, et dans la plage définie par les paramètres UserCutOffLowerBound et UserCutoffUpperBound pour les utilisateurs. Vous pouvez réduire l'effet du condenseur sur les éléments ou utilisateurs en affectant au moins à l'une des limites correspondantes la valeur zéro.

#####11\.1.2. Paramètres de build de classement

Le tableau ci-dessous décrit les paramètres de build pour une build de classement.

|Clé|Description|Type|Valeur valide|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Le nombre d'itérations effectuées par le modèle est déterminé par le temps de calcul total et la précision du modèle. Un nombre élevé signifie une plus grande précision, mais aussi des temps de calcul plus longs.| Integer | 10-50 |
| NumberOfModelDimensions | Le nombre de dimensions correspond au nombre de « caractéristiques » que le modèle tente de trouver dans vos données. Le fait d'augmenter le nombre de dimensions permet de mieux ajuster les résultats en clusters plus petits. Toutefois, si le nombre de dimensions est trop important, le modèle ne pourra pas trouver de corrélations entre les éléments. | Integer | 10-40 |
|ItemCutOffLowerBound| Définit la limite inférieure des éléments pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
|ItemCutOffUpperBound| Définit la limite supérieure des éléments pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
|UserCutOffLowerBound| Définit la limite inférieure des utilisateurs pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
|UserCutOffUpperBound| Définit la limite supérieure des utilisateurs pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |

#####11\.1.3. Paramètres de build de recommandation
Le tableau ci-dessous décrit les paramètres de build pour une build de recommandation.

|Clé|Description|Type|Valeur valide|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Le nombre d'itérations effectuées par le modèle est déterminé par le temps de calcul total et la précision du modèle. Un nombre élevé signifie une plus grande précision, mais aussi des temps de calcul plus longs.| Integer | 10-50 |
| NumberOfModelDimensions | Le nombre de dimensions correspond au nombre de « caractéristiques » que le modèle tente de trouver dans vos données. Le fait d'augmenter le nombre de dimensions permet de mieux ajuster les résultats en clusters plus petits. Toutefois, si le nombre de dimensions est trop important, le modèle ne pourra pas trouver de corrélations entre les éléments. | Integer | 10-40 |
|ItemCutOffLowerBound| Définit la limite inférieure des éléments pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
|ItemCutOffUpperBound| Définit la limite supérieure des éléments pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
|UserCutOffLowerBound| Définit la limite inférieure des utilisateurs pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
|UserCutOffUpperBound| Définit la limite supérieure des utilisateurs pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
| Description | Description de la build. | String | Tout texte, 512 caractères maximum |
| EnableModelingInsights | Permet de calculer des mesures sur le modèle de recommandation. | Boolean | True/False |
| UseFeaturesInModel | Indique si des caractéristiques peuvent être utilisées pour améliorer le modèle de recommandation. | Boolean | True/False |
| ModelingFeatureList | Liste de noms de caractéristiques séparés par des virgules à utiliser dans la build de recommandation pour améliorer les recommandations. | String | Noms de caractéristiques, 512 caractères maximum |
| AllowColdItemPlacement | Indique si la recommandation doit également placer les éléments froids selon la similarité des caractéristiques. | Boolean | True/False |
| EnableFeatureCorrelation | Indique si des caractéristiques peuvent être utilisées dans le raisonnement. | Boolean | True/False |
| ReasoningFeatureList | Liste de noms de caractéristiques séparés par des virgules à utiliser pour générer des phrases de raisonnement (par exemple, pour expliquer les recommandations). | String | Noms de caractéristiques, 512 caractères maximum |
| EnableU2I | Autoriser la recommandation personnalisée alias U2I (recommandations d’éléments selon un utilisateur). | Boolean | Vrai/faux (Vrai par défaut) |

#####11\.1.4. Paramètres de build FBT
Le tableau ci-dessous décrit les paramètres de build pour une build de recommandation.

|Clé|Description|Type|Valeur valide (par défaut)|
|:-----|:----|:----|:---|
|FbtSupportThreshold | Niveau de conservatisme du modèle. Nombre de co-occurrences d'éléments à prendre en compte pour la modélisation.| Integer | 3-50 (6) |
|FbtMaxItemSetSize | Limite le nombre d'éléments dans un ensemble fréquent.| Integer | 2-3 (2) |
|FbtMinimalScore | Score minimal d'un jeu fréquent pour que celui-ci soit inclus dans les résultats retournés. Plus le score est élevé, mieux c'est.| Double | 0 et plus (0) |
|FbtSimilarityFunction | Définit la fonction de similarité à utiliser par le build. Élévation favorise la sérendipité, cooccurence favorise la prévisibilité, et Jaccard est un bon compromis entre les deux. | Chaîne | cooccurrence, élévation, jaccard (élévation) |


###11\.2. Déclencher une build de recommandation

  Par défaut, cette API déclenche une build de modèle de recommandation. Pour déclencher une build de classement (afin d'affecter un score aux caractéristiques), utilisez la variante de l'API de build qui possède un paramètre de type de build.


| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"` (en cas d’envoi du corps de la requête)|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId |	Identificateur unique du modèle |
| userDescription | Identificateur textuel du catalogue. Notez que si vous utilisez des espaces, vous devez plutôt l'encoder avec %20. Consultez l'exemple ci-dessus.<br>Longueur maximale : 50 |
| apiVersion | 1\.0 |
|
| Corps de la requête | S'il est laissé vide, la build s'exécute avec les paramètres de build par défaut.<br><br>Si vous souhaitez définir les paramètres de build, envoyez les paramètres au format XML dans le corps, comme dans l'exemple suivant. (Consultez la section « Paramètres de build » pour obtenir une explication des paramètres).`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**Réponse** :

Code d'état HTTP : 200

Il s'agit d'une API asynchrone. La réponse obtenue est un ID de build. Pour savoir à quel moment la build s'est terminée, vous devez appeler l'API « Get Builds Status of a Model » (obtention de l'état de build d'un modèle) et rechercher cet ID de build dans la réponse. Notez que l'exécution d'une build peut nécessiter de quelques minutes à plusieurs heures selon la taille des données.

Vous ne pouvez pas utiliser de recommandations tant que la build n'est pas terminée.

État de build valide :

- Create : la demande de build a été créée.
- Queued : la demande de build a été envoyée et mise en attente.
- Building : la build est en cours.
- Success : la build a été correctement exécutée.
- Error : la build s'est terminée par un échec.
- Cancelled : la build a été annulée.
- Cancelling : une demande d’annulation de la build a été envoyée.


Notez que l'ID de build se trouve sous le chemin suivant : `Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###11\.3. Déclenchement d'une build (recommandation, classement ou FBT)

| Méthode HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"` (en cas d’envoi du corps de la requête)|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId |	Identificateur unique du modèle |
| userDescription | Identificateur textuel du catalogue. Notez que si vous utilisez des espaces, vous devez plutôt l'encoder avec %20. Consultez l'exemple ci-dessus.<br>Longueur maximale : 50 |
| buildType | Type de la build à appeler : <br/> : « Recommendation » pour une build de recommandation <br> : « Ranking » pour une build de classement <br/> : « Fbt » pour une build FBT
| apiVersion | 1\.0 |
|
| Corps de la requête | S’il est laissé vide, le build s’exécute avec les paramètres de build par défaut.<br><br>Si vous souhaitez définir les paramètres de build, envoyez-les au format XML dans le corps, comme dans l’exemple suivant. (Consultez la section « Paramètres de build » pour obtenir une explication, ainsi que la liste complète des paramètres).`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

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
- Cancelling : la build est en cours d'annulation.

Notez que l'ID de build se trouve sous le chemin suivant : `Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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




###11\.4. Obtention de l'état de build d'un modèle
Récupère les builds et leur état pour un modèle spécifié.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|


|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	modelId |	Identificateur unique du modèle |
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
- `feed/entry/content/properties/Status` : état de la build. Celui-ci peut avoir l'une des valeurs suivantes : Error, Building, Queued, Cancelling, Cancelled, Success.
- `feed/entry/content/properties/StatusMessage` : message d'état détaillé (s'applique uniquement à des états spécifiques).
- `feed/entry/content/properties/Progress` : progression de l'exécution de la build (%).
- `feed/entry/content/properties/StartTime` : heure de début de l'exécution de la build.
- `feed/entry/content/properties/EndTime` : heure de fin de l'exécution de la build.
- `feed/entry/content/properties/ExecutionTime` : durée de la build.
- `feed/entry/content/properties/ProgressStep` : détails sur l'étape actuelle d'une build en cours d'exécution.

États de build valides : - Created : l'entrée de demande de build a été créée. - Queued : la demande de build a été déclenchée et mise en file d'attente. - Building : la build est en cours d'exécution. - Success : la build a été correctement exécutée - Error : la build s'est terminée par un échec. - Cancelled : la build a été annulée. - Cancelling : la build est en cours d'annulation.

Valeurs valides pour le type de build : - Rank : build de classement. - Recommendation : build de recommandation.


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a model</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T17:51:10Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
		<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T17:51:10Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###11\.5. Obtention de l'état de build
Récupère les états de build de tous les modèles d'un utilisateur.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`|


|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
|	onlyLastBuild |	Indique s'il faut retourner l'historique de build du modèle en totalité ou uniquement l'état de la build la plus récente. |
|	apiVersion |	1\.0 |


**Réponse** :

Code d'état HTTP : 200

La réponse inclut une entrée par build. Chaque entrée comprend les données suivantes :

- `feed/entry/content/properties/UserName` : nom de l'utilisateur.
- `feed/entry/content/properties/ModelName` : nom du modèle.
- `feed/entry/content/properties/ModelId` : identificateur unique du modèle.
- `feed/entry/content/properties/IsDeployed` : indique si la build est déployée.
- `feed/entry/content/properties/BuildId` : identificateur unique de la build.
- `feed/entry/content/properties/BuildType` : type de build.
- `feed/entry/content/properties/Status` : état de la build. Celui-ci peut avoir l'une des valeurs suivantes : Error, Building, Queued, Cancelled, Cancelling, Success.
- `feed/entry/content/properties/StatusMessage` : message d'état détaillé (s'applique uniquement à des états spécifiques).
- `feed/entry/content/properties/Progress` : progression de l'exécution de la build (%).
- `feed/entry/content/properties/StartTime` : heure de début de l'exécution de la build.
- `feed/entry/content/properties/EndTime` : heure de fin de l'exécution de la build.
- `feed/entry/content/properties/ExecutionTime` : durée de la build.
- `feed/entry/content/properties/ProgressStep` : détails sur l'étape actuelle d'une build en cours d'exécution.

États de build valides : - Created : l'entrée de demande de build a été créée. - Queued : la demande de build a été déclenchée et mise en file d'attente. - Building : la build est en cours d'exécution. - Success : la build a été correctement exécutée - Error : la build s'est terminée par un échec. - Cancelled : la build a été annulée. - Cancelling : la build est en cours d'annulation.


Valeurs valides pour le type de build : - Rank : build de classement. - Recommendation : build de recommandation.


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T18:41:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T18:41:21Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###11\.6. Suppression de build
Supprime une build.

REMARQUE : <br>Vous ne pouvez pas supprimer une build active. Le modèle doit être mis à jour vers une autre build active avant sa suppression.<br>Vous ne pouvez pas supprimer une build en cours d'exécution. Vous devez d'abord annuler la build en appelant <strong>Cancel Build</strong>.

| Méthode HTTP | URI |
|:--------|:--------|
|SUPPRIMER |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| buildId | Identificateur unique de la build. |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200

###11\.7. Annulation de build
Annule une build avec l'état Building.

| Méthode HTTP | URI |
|:--------|:--------|
|PUT |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| buildId | Identificateur unique de la build. |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200

###11\.8. Obtention des paramètres de build
Récupère les paramètres de build.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| buildId | Identificateur unique de la build. |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200

Cette API retourne une collection d'éléments clé/valeur. Chaque élément représente un paramètre et sa valeur : - `feed/entry/content/properties/Key` : nom du paramètre de build. - `feed/entry/content/properties/Value` : valeur du paramètre de build.

Le tableau ci-dessous décrit la valeur de chaque clé.

|Clé|Description|Type|Valeur valide|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Le nombre d'itérations effectuées par le modèle est déterminé par le temps de calcul total et la précision du modèle. Un nombre élevé signifie une plus grande précision, mais aussi des temps de calcul plus longs.| Integer | 10-50 |
| NumberOfModelDimensions | Le nombre de dimensions correspond au nombre de « caractéristiques » que le modèle tente de trouver dans vos données. Le fait d'augmenter le nombre de dimensions permet de mieux ajuster les résultats en clusters plus petits. Toutefois, si le nombre de dimensions est trop important, le modèle ne pourra pas trouver de corrélations entre les éléments. | Integer | 10-40 |
|ItemCutOffLowerBound| Définit la limite inférieure des éléments pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
|ItemCutOffUpperBound| Définit la limite supérieure des éléments pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
|UserCutOffLowerBound| Définit la limite inférieure des utilisateurs pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
|UserCutOffUpperBound| Définit la limite supérieure des utilisateurs pour le condenseur. Consultez Condenseur d'utilisation ci-dessus. | Integer | 2 ou plus (0 désactive le condenseur) |
| Description | Description de la build. | String | Tout texte, 512 caractères maximum |
| EnableModelingInsights | Permet de calculer des mesures sur le modèle de recommandation. | Boolean | True/False |
| UseFeaturesInModel | Indique si des caractéristiques peuvent être utilisées pour améliorer le modèle de recommandation. | Boolean | True/False |
| ModelingFeatureList | Liste de noms de caractéristiques séparés par des virgules à utiliser dans la build de recommandation pour améliorer les recommandations. | String | Noms de caractéristiques, 512 caractères maximum |
| AllowColdItemPlacement | Indique si la recommandation doit également placer les éléments froids selon la similarité des caractéristiques. | Boolean | True/False |
| EnableFeatureCorrelation | Indique si des caractéristiques peuvent être utilisées dans le raisonnement. | Boolean | True/False |
| ReasoningFeatureList | Liste de noms de caractéristiques séparés par des virgules à utiliser pour générer des phrases de raisonnement (par exemple, pour expliquer les recommandations). | String | Noms de caractéristiques, 512 caractères maximum |


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get build parameters</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:50:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
			<titletype="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">Description</d:Key>
					<d:Value m:type="Edm.String">rankBuild</d:Value>
				</m:properties>
			</content>
		</entry>
	</feed>

##12\. Recommandation
###12\.1. Obtention de recommandations d’éléments (pour la build active)

Obtient des recommandations de la build active de type « Recommendation » ou « Fbt » en fonction d’une liste d’éléments (saisis) initiaux.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle |
| itemIds | Liste des éléments séparés par des virgules faisant l'objet d'une demande de recommandation. <br>Si la build active est de type FBT, vous ne pouvez envoyer qu'un seul élément. <br>Longueur maximale : 1 024 |
| numberOfResults | Nombre de résultats requis <br> Max : 150 |
| includeMetatadata | Utilisation ultérieure, toujours false |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200


La réponse inclut une entrée par élément recommandé. Chaque entrée comprend les données suivantes : - `Feed\entry\content\properties\Id` : ID de l'élément recommandé. - `Feed\entry\content\properties\Name` : nom de l'élément. - `Feed\entry\content\properties\Rating` : évaluation de la recommandation ; plus le nombre est élevé, plus le niveau de confiance est élevé. - `Feed\entry\content\properties\Reasoning` : raisonnement de la recommandation (par exemple, explications de la recommandation).

L'exemple de réponse ci-dessous comprend 10 éléments recommandés.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
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

###12\.2. Obtention de recommandations d’éléments (d’une build spécifique)

Obtient des recommandations d'une build spécifique de type « Recommendation » ou « Fbt ».

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle |
| itemIds | Liste des éléments séparés par des virgules faisant l'objet d'une demande de recommandation. <br>Si la build active est de type FBT, vous ne pouvez envoyer qu'un seul élément. <br>Longueur maximale : 1 024 |
| numberOfResults | Nombre de résultats requis <br> Max : 150 |
| includeMetatadata | Utilisation ultérieure, toujours false
| buildId | ID de build à utiliser pour cette demande de recommandation |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200


La réponse inclut une entrée par élément recommandé. Chaque entrée comprend les données suivantes : - `Feed\entry\content\properties\Id` : ID de l'élément recommandé. - `Feed\entry\content\properties\Name` : nom de l'élément. - `Feed\entry\content\properties\Rating` : évaluation de la recommandation ; plus le nombre est élevé, plus le niveau de confiance est élevé. - `Feed\entry\content\properties\Reasoning` : raisonnement de la recommandation (par exemple, explications de la recommandation).

Pour obtenir un exemple de réponse, consultez la section 12.1.

###12\.3. Obtention de recommandations FBT (pour la build active)

Obtient des recommandations de la build active de type « Fbt ». en fonction d’un élément (saisi) initial.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle |
| itemId | Élément à recommander. <br>Longueur maximale : 1024 |
| numberOfResults | Nombre de résultats requis <br> Max : 150 |
| minimalScore | Score minimal qu’un jeu fréquent doit obtenir pour être inclus dans les résultats retournés. |
| includeMetatadata | Utilisation ultérieure, toujours false |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200


La réponse inclut une entrée par ensemble d’éléments recommandé (il s’agit d’un ensemble d’éléments souvent acheté avec l’élément initial/saisi). Chaque entrée comprend les données suivantes : - `Feed\entry\content\properties\Id1` : ID de l’élément recommandé. - `Feed\entry\content\properties\Name1` : nom de l’élément. - `Feed\entry\content\properties\Id2` : ID du 2e élément recommandé (facultatif). - `Feed\entry\content\properties\Name2` : nom du 2e élément (facultatif). - `Feed\entry\content\properties\Rating` : évaluation de la recommandation ; plus le nombre est élevé, plus le niveau de confiance est élevé. - `Feed\entry\content\properties\Reasoning` : raisonnement de la recommandation (par exemple, explications de la recommandation).

L’exemple de réponse ci-dessous comprend 3 ensembles d’éléments recommandés.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
		<d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###12\.4. Obtention de recommandations FBT (d’une build spécifique)

Obtient des recommandations d’une build spécifique de type « Fbt ».

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle |
| itemId | Élément à recommander. <br>Longueur maximale : 1024 |
| numberOfResults | Nombre de résultats requis <br> Max : 150 |
| minimalScore | Score minimal qu’un jeu fréquent doit obtenir pour être inclus dans les résultats retournés. |
| includeMetatadata | Utilisation ultérieure, toujours false |
| buildId | ID de build à utiliser pour cette demande de recommandation |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200


La réponse inclut une entrée par ensemble d’éléments recommandé (il s’agit d’un ensemble d’éléments souvent acheté avec l’élément initial/saisi). Chaque entrée comprend les données suivantes : - `Feed\entry\content\properties\Id1` : ID de l’élément recommandé. - `Feed\entry\content\properties\Name1` : nom de l’élément. - `Feed\entry\content\properties\Id2` : ID du 2e élément recommandé (facultatif). - `Feed\entry\content\properties\Name2` : nom du 2e élément (facultatif). - `Feed\entry\content\properties\Rating` : évaluation de la recommandation ; plus le nombre est élevé, plus le niveau de confiance est élevé. - `Feed\entry\content\properties\Reasoning` : raisonnement de la recommandation (par exemple, explications de la recommandation).

Pour obtenir un exemple de réponse, consultez la section 12.3.

###12\.5. Obtention de recommandations de l’utilisateur (pour la build active)

Obtient des recommandations de l’utilisateur auprès de la build de type « Recommendation » marquée comme build active.

L’API retourne une liste d’éléments prédits en fonction de l’historique d’utilisation de l’utilisateur.

Remarques : 1. Il n’existe aucune recommandation de l’utilisateur pour une build de type FBT. 2. Si la build active est de type FBT, cette méthode renvoie une erreur.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle |
| userId | Identificateur unique de l’utilisateur |
| numberOfResults | Nombre de résultats requis |
| includeMetatadata | Utilisation ultérieure, toujours false |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200


La réponse inclut une entrée par élément recommandé. Chaque entrée comprend les données suivantes : - `Feed\entry\content\properties\Id` : ID de l'élément recommandé. - `Feed\entry\content\properties\Name` : nom de l'élément. - `Feed\entry\content\properties\Rating` : évaluation de la recommandation ; plus le nombre est élevé, plus le niveau de confiance est élevé. - `Feed\entry\content\properties\Reasoning` : raisonnement de la recommandation (par exemple, explications de la recommandation).

Pour obtenir un exemple de réponse, consultez la section 12.1.

###12\.6. Obtention de recommandations de l’utilisateur avec une liste d’éléments (pour la build active)

Obtient des recommandations de l’utilisateur auprès de la build de type « Recommendation » marquée comme build active, à l’aide d’une liste supplémentaire d’éléments.

L’API retourne une liste d’éléments prédits en fonction de l’historique d’utilisation de l’utilisateur et des éléments supplémentaires fournis.

Remarques : 1. Il n’existe aucune recommandation de l’utilisateur pour une build de type FBT. 2. Si la build active est de type FBT, cette méthode renvoie une erreur.


| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%2C1000%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle |
| userId | Identificateur unique de l’utilisateur |
| itemsIds | Liste des éléments séparés par des virgules faisant l'objet d'une demande de recommandation. Longueur maximale : 1 024 |
| numberOfResults | Nombre de résultats requis |
| includeMetatadata | Utilisation ultérieure, toujours false |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200


La réponse inclut une entrée par élément recommandé. Chaque entrée comprend les données suivantes : - `Feed\entry\content\properties\Id` : ID de l'élément recommandé. - `Feed\entry\content\properties\Name` : nom de l'élément. - `Feed\entry\content\properties\Rating` : évaluation de la recommandation ; plus le nombre est élevé, plus le niveau de confiance est élevé. - `Feed\entry\content\properties\Reasoning` : raisonnement de la recommandation (par exemple, explications de la recommandation).

Pour obtenir un exemple de réponse, consultez la section 12.1.

###12\.7. Obtention de recommandations de l’utilisateur (d’une build spécifique)

Obtient des recommandations de l’utilisateur auprès d’une build spécifique de type « Recommendation ».

L’API retourne une liste d’éléments prédits en fonction de l’historique d’utilisation de l’utilisateur (utilisé dans la build spécifique).

Remarque : il n’existe aucune recommandation de l’utilisateur pour une build de type FBT.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|


|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle |
| userId | Identificateur unique de l’utilisateur |
| numberOfResults | Nombre de résultats requis |
| includeMetatadata | Utilisation ultérieure, toujours false |
| buildId | ID de build à utiliser pour cette demande de recommandation |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200


La réponse inclut une entrée par élément recommandé. Chaque entrée comprend les données suivantes : - `Feed\entry\content\properties\Id` : ID de l'élément recommandé. - `Feed\entry\content\properties\Name` : nom de l'élément. - `Feed\entry\content\properties\Rating` : évaluation de la recommandation ; plus le nombre est élevé, plus le niveau de confiance est élevé. - `Feed\entry\content\properties\Reasoning` : raisonnement de la recommandation (par exemple, explications de la recommandation).

Pour obtenir un exemple de réponse, consultez la section 12.1.


###12\.8. Obtention de recommandations de l’utilisateur avec une liste d’éléments (pour une build spécifique)

Obtient des recommandations de l’utilisateur auprès d’une build spécifique de type « Recommendation » et d’une liste d’éléments supplémentaires.

L’API retourne une liste d’éléments prédits en fonction de l’historique d’utilisation de l’utilisateur et des éléments de la liste.

Remarque : il n’existe aucune recommandation de l’utilisateur pour une build de type FBT.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|



|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle |
| userId | Identificateur unique de l’utilisateur |
| itemIds | Liste des éléments séparés par des virgules faisant l'objet d'une demande de recommandation. Longueur maximale : 1 024 |
| numberOfResults | Nombre de résultats requis |
| includeMetatadata | Utilisation ultérieure, toujours false |
| buildId | ID de build à utiliser pour cette demande de recommandation |
| apiVersion | 1\.0 |

**Réponse :**

Code d'état HTTP : 200


La réponse inclut une entrée par élément recommandé. Chaque entrée comprend les données suivantes : - `Feed\entry\content\properties\Id` : ID de l'élément recommandé. - `Feed\entry\content\properties\Name` : nom de l'élément. - `Feed\entry\content\properties\Rating` : évaluation de la recommandation ; plus le nombre est élevé, plus le niveau de confiance est élevé. - `Feed\entry\content\properties\Reasoning` : raisonnement de la recommandation (par exemple, explications de la recommandation).

Pour obtenir un exemple de réponse, consultez la section 12.1.

##13\. Historique d’utilisation de l’utilisateur
Une fois qu’un modèle de recommandation a été créé, le système autorise la récupération de l’historique de l’utilisateur (éléments associés à un utilisateur spécifique) utilisé pour la build. Cette API permet de récupérer l’historique de l’utilisateur.

Remarque : l’historique de l’utilisateur est actuellement disponible uniquement pour les builds de recommandation.

###13\.1 Récupération de l’historique de l’utilisateur
Récupère la liste des éléments utilisés dans la build active ou dans la build spécifiée pour l’ID d’utilisateur donné.

| Méthode HTTP | URI |
|:--------|:--------|
|GET | Obtient l’historique de l’utilisateur pour le build actif.<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>Obtient l’historique de l’utilisateur pour le build donné. `<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27`<br/><br/>Exemple :`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277`|


|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle.|
| userId | Identificateur unique de l’utilisateur.
| buildId | Paramètre facultatif permettant d’indiquer de quelle build l’historique de l’utilisateur doit être extrait.
| version\_api | 1\.0 |


**Réponse :**

Code d'état HTTP : 200

La réponse inclut une entrée par élément recommandé. Chaque entrée comporte les données suivantes :- `Feed\entry\content\properties\Id` : ID de l’élément recommandé. - `Feed\entry\content\properties\Name` : nom de l’élément. - `Feed\entry\content\properties\Rating` : n/a. - `Feed\entry\content\properties\Reasoning` : n/a.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get User History</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-05-26T15:32:47Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">CatalogItemsThatContainATokenEntity</title>
		<updated>2015-05-26T15:32:47Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Rating m:type="Edm.Double">0</d:Rating>
				<d:Reasoning m:type="Edm.String"/>
				<d:Metadata m:type="Edm.String"/>
				<d:FormattedRating m:type="Edm.Double" m:null="true"/>
			</m:properties>
		</content>
	</entry>
</feed>

##14\. Notifications
Azure Machine Learning Recommendations crée des notifications dès lors que des erreurs persistantes se produisent dans le système. Il existe 3 types de notifications : 1. Échec de build : cette notification est déclenchée à chaque échec de build. 2. Échec de traitement d'acquisition de données : cette notification est déclenchée quand plus de 100 erreurs se produisent au cours des 5 dernières minutes du traitement des événements d'utilisation par modèle. 3. Échec d'utilisation de recommandation : cette notification est déclenchée quand plus de 100 erreurs se produisent au cours des 5 dernières minutes du traitement des demandes de recommandation par modèle.


###14\.1. Obtention de notifications
Récupère toutes les notifications pour tous les modèles ou pour un seul modèle.

| Méthode HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Obtention de toutes les notifications pour tous les modèles :<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>Exemple d'obtention des notifications pour un modèle spécifique :<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Paramètre facultatif. Si vous l'omettez, vous obtenez toutes les notifications pour tous les modèles. <br>Valeur valide : identificateur unique du modèle.|
| apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse :**

Code d'état HTTP : 200

OData XML

    The response includes one entry per notification. Each entry has the following data:
		* feed\entry\content\properties\UserName – Internal user name identification.
		* feed\entry\content\properties\ModelId – Model ID.
		* feed\entry\content\properties\Message – Notification message.
		* feed\entry\content\properties\DateCreated – Date that this notification was created in UTC format.
		* feed\entry\content\properties\NotificationType – Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of Notifications for a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-04T13:24:23Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
		<entry>
				<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfNotificationsForAUserEntity</title>
			<updated>2014-11-04T13:24:23Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
					<d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
					<d:Message m:type="Edm.String">Build failed for user</d:Message>
					<d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
					<d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
				</m:properties>
			</content>
		</entry>
	</feed>

###14\.2. Suppression de notifications de modèle
Supprime toutes les notifications lues pour un modèle.

| Méthode HTTP | URI |
|:--------|:--------|
|SUPPRIMER |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| modelId | Identificateur unique du modèle |
| apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200

###14\.3. Suppression de notifications utilisateur
Supprime toutes les notifications pour tous les modèles.

| Méthode HTTP | URI |
|:--------|:--------|
|SUPPRIMER |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|	Nom du paramètre |	Valeurs valides |
|:--------			|:--------								|
| apiVersion | 1\.0 |
|
| Corps de la requête | AUCUN |

**Réponse** :

Code d'état HTTP : 200




##15\. Informations juridiques
Ce document est fourni « en l'état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis.<br><br> Certains exemples sont fournis à titre indicatif uniquement et sont fictifs. Toute association ou lien est purement involontaire ou fortuit.<br><br> Ce document ne vous accorde aucun droit légal à la propriété intellectuelle pour un produit Microsoft. Vous pouvez copier et utiliser ce document pour un usage interne, à titre de référence.<br><br> © 2015 Microsoft. Tous droits réservés.
 

<!---HONumber=AcomDC_0128_2016-->