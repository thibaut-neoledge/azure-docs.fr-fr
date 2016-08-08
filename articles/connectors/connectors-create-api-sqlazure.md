<properties
    pageTitle="Ajouter le connecteur de base de données SQL Azure à vos applications logiques | Microsoft Azure"
    description="Vue d’ensemble du connecteur de base de données SQL Azure avec les paramètres de l’API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/25/2016"
   ms.author="mandia"/>


# Prise en main du connecteur de base de données SQL Azure
Créez des flux de travail pour votre organisation destinés à gérer les données dans vos tables à l’aide du connecteur de base de données SQL Azure.

Avec Base de données SQL, vous pouvez effectuer les opérations suivantes :

- Créez votre flux de travail en ajoutant un nouveau client à une base de données clients ou en mettant à jour une commande dans une base de données de commandes.
- Utilisez des actions pour obtenir une ligne de données, insérer une nouvelle ligne ou en supprimer. Par exemple, lorsqu’un enregistrement est créé dans Dynamics CRM Online (déclencheur), insérer une ligne dans une base de données SQL Azure (action).

Cette rubrique décrit comment utiliser le connecteur de base de données SQL dans une application logique et répertorie également les actions.

>[AZURE.NOTE] Cette version de l’article s’applique à la disponibilité générale des applications logiques.

Pour plus d’informations sur Logic Apps, voir [Qu’est-ce qu’une application logique ?](../app-service-logic/app-service-logic-what-are-logic-apps.md) et [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connexion à la base de données SQL Azure

Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à Base de données SQL, vous devez commencer par créer une *connexion* Base de données SQL. Pour créer une connexion, entrez les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ensuite, dans la base de données SQL, entrez vos informations d’identification de base de données SQL pour créer la connexion.

#### Créer la connexion

>[AZURE.INCLUDE [Créer la connexion à SQL Azure](../../includes/connectors-create-api-sqlazure.md)]

## Utilisation d’un déclencheur

Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, notamment un déclencheur de périodicité, un déclencheur Webhook HTTP, des déclencheurs disponibles avec d’autres connecteurs, etc. La page [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) vous fournit un exemple.

## Utilisation d’une action
	
Une action est une opération effectuée par le flux de travail défini dans une application logique. [Apprenez-en davantage sur les actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Sélectionnez le signe plus. Vous disposez de plusieurs options : **Ajouter une action**, **Ajouter une condition** ou l’une des options **Plus**.

	![](./media/connectors-create-api-sqlazure/add-action.png)

2. Choisissez **Ajouter une action**.

3. Dans la zone de texte, saisissez « sql » pour obtenir la liste de toutes les actions disponibles.

	![](./media/connectors-create-api-sqlazure/sql-1.png)

4. Dans notre exemple, choisissez **SQL Server - Obtenir une ligne**. Si une connexion existe déjà, sélectionnez le **nom de la table** dans la liste déroulante, puis entrez **l’ID de la ligne** à laquelle vous souhaitez accéder.

	![](./media/connectors-create-api-sqlazure/sample-table.png)

	Si vous êtes invité à saisir les informations de connexion, entrez les informations requises pour créer la connexion. La section [Créer la connexion](connectors-create-api-sqlazure.md#create-the-connection) figurant dans cette rubrique décrit ces propriétés.

	> [AZURE.NOTE] Dans cet exemple, nous obtiendrons une ligne d’une table. Pour consulter les données de cette ligne, ajoutez une autre action qui crée un fichier en utilisant les champs de la table. Par exemple, ajoutez une action OneDrive qui utilise les champs FirstName et LastName pour créer un nouveau fichier dans le compte de stockage cloud.

5. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.


## Détails techniques

## Actions Base de données SQL
Une action est une opération effectuée par le flux de travail défini dans une application logique. Le connecteur de base de données SQL inclut les actions suivantes.

|Action|Description|
|--- | ---|
|[ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure)|Exécute une procédure stockée dans SQL|
|[GetRow](connectors-create-api-sqlazure.md#get-row)|Récupère une seule ligne d’une table SQL|
|[GetRows](connectors-create-api-sqlazure.md#get-rows)|Récupère les lignes d’une table SQL|
|[InsertRow](connectors-create-api-sqlazure.md#insert-row)|Insère une nouvelle ligne dans une table SQL|
|[DeleteRow](connectors-create-api-sqlazure.md#delete-row)|Supprime une ligne d’une table SQL|
|[GetTables](connectors-create-api-sqlazure.md#get-tables)|Extrait des tables à partir d’une base de données SQL|
|[UpdateRow](connectors-create-api-sqlazure.md#update-row)|Met à jour une ligne existante dans une table SQL|

### Détails de l’action

Dans cette section, consultez les détails relatifs à chaque action, y compris toutes les propriétés d’entrée requises ou facultatives et toute sortie correspondante associée au connecteur.


#### Exécuter une procédure stockée
Exécute une procédure stockée dans SQL.

| Nom de la propriété| Display Name |Description|
| ---|---|---|
|procedure * | Nom de la procédure | Nom de la procédure stockée à exécuter |
|parameters * | Paramètres d'entrée | Les paramètres sont dynamiques et dépendent de la procédure stockée que vous choisissez. <br/><br/> Par exemple, si vous utilisez l’exemple de base de données Adventure Works, choisissez la procédure stockée *ufnGetCustomerInformation*. Le paramètre d’entrée **ID du client** est affiché. Entrez « 6 » ou l’un des autres ID de client. |

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
ProcedureResult : transmet le résultat de l’exécution de la procédure stockée

| Nom de la propriété | Type de données | Description |
|---|---|---|
|OutputParameters|objet|Valeurs de paramètres de sortie |
|ReturnCode|integer|Code de retour d’une procédure |
|ResultSets|objet| Jeux de résultats|


#### Obtenir une ligne 
Récupère une seule ligne d’une table SQL.

| Nom de la propriété| Display Name |Description|
| ---|---|---|
|table * | Nom de la table |Nom de la table SQL|
|id * | ID de la ligne |Identificateur unique de la ligne à récupérer|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Item

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|


#### Obtenir des lignes 
Récupère les lignes d’une table SQL.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table SQL|
|$skip|Nombre à ignorer|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|Nombre maximal à récupérer|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|Requête de filtre|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|Trier par|Requête orderBy ODATA pour spécifier l’ordre des entrées|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
|---|---|
|value|array|


#### Insérer une ligne 
Insère une nouvelle ligne dans une table SQL.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table SQL|
|item*|Ligne|Ligne à insérer dans la table SQL spécifiée|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Item

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|


#### Supprimer la ligne 
Supprime une ligne d’une table SQL.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table SQL|
|id*|ID de la ligne|Identificateur unique de la ligne à supprimer|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Aucun.

#### Obtenir des tables 
Extrait des tables à partir d’une base de données SQL.

Il n'existe aucun paramètre pour cet appel.

##### Détails des résultats 
TablesList

| Nom de la propriété | Type de données |
|---|---|
|value|array|

#### Mettre à jour une ligne 
Met à jour une ligne existante dans une table SQL.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table SQL|
|id*|ID de la ligne|Identificateur unique de la ligne à mettre à jour|
|item*|Ligne|Ligne avec valeurs mises à jour|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats  
Item

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|


### Réponses HTTP

Lorsque vous exécutez des appels de diverses actions, vous pouvez obtenir certaines réponses. Le tableau suivant présente les réponses et leurs descriptions :

|Nom|Description|
|---|---|
|200|OK|
|202|Acceptée|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s'est produite|
|default|L’opération a échoué.|


## Étapes suivantes

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Explorez les autres connecteurs disponibles dans Logic Apps en consultant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->