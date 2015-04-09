<properties 
	pageTitle="Interrogation avec le langage SQL de DocumentDB | Azure" 
	description="DocumentDB, service de base de données NoSQL orienté documents, prend en charge l'interrogation de documents JSON hiérarchiques à l'aide du langage SQL sans nécessiter un schéma explicite ou la création d'index secondaires." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mimig"/>

#Interrogation de DocumentDB
Microsoft Azure DocumentDB prend en charge l'interrogation de documents à l'aide du langage SQL sur les documents JSON hiérarchiques. DocumentDB n'utilise pas de schéma. En raison de son engagement dans le modèle de données JSON directement au sein du moteur de base de données, il fournit l'indexation automatique des documents JSON sans nécessiter un schéma explicite ou la création d'index secondaires. 

Lors de la conception du langage de requête pour DocumentDB, nous avions deux objectifs à l'esprit :

-	<strong>Adopter SQL</strong> - Au lieu d'inventer un langage de requête, nous voulions exploiter le langage SQL existant. Après tout, le SQL est l'un des langages de requête les plus conviviaux et populaires. Le langage de requête SQL de DocumentDB fournit un modèle de programmation formel pour créer des requêtes élaborées sur les documents JSON.
-	<strong>Étendre SQL</strong> - Comme une base de données de document JSON peut exécuter JavaScript directement dans le moteur de base de données, nous avons voulu utiliser le modèle de programmation de JavaScript comme base pour notre langage de requête SQL. Le langage de requête SQL de DocumentDB est inclus dans le système de type, l'évaluation d'expression et l'appel de fonction de JavaScript. En retour, cela fournit un modèle de programmation naturel pour les projections relationnelles, la navigation hiérarchique entre les documents JSON, les jointures réflexives et l'appel de fonctions définies par l'utilisateur écrites entièrement en JavaScript, entre autres fonctionnalités. 

Nous pensons que ces capacités sont la clé pour réduire la friction entre l'application et la base de données et sont cruciales pour la productivité des développeurs.

Nous vous recommandons de commencer par visionner la vidéo suivante, dans laquelle Aravind Ramachandran présente les fonctionnalités d'interrogation de DocumentDB, puis d'utiliser notre [Query Playground](http://www.documentdb.com/sql/demo) pour essayer DocumentDB et exécuter des requêtes SQL sur notre dataset.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

Revenez ensuite à cet article qui vous montrera d'abord comment exécuter des requêtes simples sur quelques documents JSON simples.

## Prise en main
Pour voir comment le langage SQL de DocumentDB fonctionne, nous allons commencer par quelques documents JSON simples sur lesquels nous allons appliquer certaines requêtes simples. Prenez ces deux documents JSON relatifs à deux familles. Notez qu'avec DocumentDB, nous n'avons pas besoin de créer de schéma ou d'index secondaire de façon explicite. Nous devons simplement insérer les documents JSON dans une collection DocumentDB et ensuite les interroger. 
Nous avons ici un document JSON simple pour la famille Andersen, les parents, les enfants (et leurs animaux), l'adresse et les informations d'enregistrement. Le document se compose de chaînes, de nombres, d'opérateurs booléens, de tableaux et de propriétés imbriquées. 

**Document**  

	{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}


Voici un second document comportant une différence subtile : - `givenName` et `familyName` sont utilisés au lieu de `firstName` et `lastName`.

**Document**  

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	             "givenName": "Jesse", 
	            "gender": "female", "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	            "familyName": "Miller", 
	             "givenName": "Lisa", 
	             "gender": "female", 
	             "grade": 8 }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	}



﻿À présent, appliquons quelques requêtes sur ces données pour comprendre certains aspects clés du langage SQL de DocumentDB. Par exemple, la requête suivante va retourner les documents où le champ ID correspond à `AndersenFamily`. Comme il s'agit d'une requête `SELECT *`, le résultat est le document JSON complet :

**Requête**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Résultats**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}]


﻿À présent, imaginez que nous ayons besoin de remettre en forme le résultat JSON. Cette requête projette un nouvel objet JSON avec 2 champs sélectionnés, Name et City, où le nom de la ville de l'adresse est identique à celui de l'État. Dans ce cas, " NY, NY " correspond.

**Requête**	

	SELECT {"Name":f.id, "City":f.address.city} AS Family 
	FROM Families f 
	WHERE f.address.city = f.address.state

**Résultats**

	[{
	    "Family": {
	        "Name": "WakefieldFamily", 
	        "City": "NY"
	    }
	}]


La requête suivante retourne tous les noms donnés des enfants de la famille dont l'ID correspond à `WakefieldFamily`.

**Requête**

	SELECT c.givenName 
	FROM Families f 
	JOIN c IN f.children 
	WHERE f.id = 'WakefieldFamily'

**Résultats**

	[
	  { "givenName": "Jesse" }, 
	  { "givenName": "Lisa"}
	]


Nous aimerions attirer votre attention sur quelques aspects importants du langage de requête DocumentDB à travers les exemples que nous venons de voir :  
 
-	Comme le langage SQL de DocumentDB fonctionne avec les valeurs JSON, il traite des entités d'arborescence au lieu des lignes et des colonnes. C'est pourquoi ce langage vous permet de faire référence aux nœuds de l'arborescence à n'importe quel niveau arbitraire, comme `Node1.Node2.Node3.....Nodem`, tout comme le SQL relationnel se rattachant à la référence en deux parties de `<table>.<column>`.   
-	Le langage fonctionne avec des données sans schéma. C'est pourquoi le système de type doit être lié de façon dynamique. La même expression peut engendrer différents types sur différents documents. Le résultat d'une requête est une valeur JSON valide, mais n'est pas forcément un schéma fixe.  
-	DocumentDB prend uniquement en charge les documents JSON stricts. Cela signifie que le système de type et les expressions peuvent uniquement traiter des types JSON. Reportez-vous à la [spécification JSON](http://www.json.org/) pour plus de détails.  
-	Une collection DocumentDB est un conteneur sans schéma pour vos documents JSON. Les relations des entités de données dans et entre les documents d'une collection sont capturées de façon implicite par le contenant et non par les relations de clé primaire et de clé étrangère. Cet aspect est important dans le cadre des liaisons entre documents (ce sujet est abordé plus loin dans cet article).

##Indexation DocumentDB

Avant d'aborder le langage SQL de DocumentDB, nous allons présenter la conception de l'indexation de DocumentDB. 

L'objectif des index de base de données est de servir les requêtes dans leurs différents formulaires et formes tout en consommant un minimum de ressources (comme le temps processeur ou les E/S) et en fournissant un bon débit et une faible latence. Souvent, le choix des index adéquats pour l'interrogation d'une base de données requiert une planification et une expérimentation importantes. Cette approche constitue un défi pour les bases de données sans schéma, où les données ne sont pas conformes à un schéma strict et évoluent rapidement. 

C'est pourquoi, lorsque nous avons conçu le sous-système d'indexation de DocumentDB, nous avons fixé les objectifs suivants :

-	Indexer les documents sans requérir un schéma : le sous-système d'indexation ne requiert pas d'informations de schéma ou n'établit pas d'hypothèse a priori sur les schémas des documents. 

-	Prendre en charge des requêtes hiérarchiques et relationnelles enrichies et efficaces : l'index prend en charge le langage de requête DocumentDB de manière efficace, notamment la prise en charge des projections hiérarchiques et relationnelles.

-	Prendre en charge des requêtes cohérentes en dépit de volumes soutenus d'écritures : dans le cas des charges de travail à débits d'écriture élevés avec des requêtes cohérentes, l'index est mis à jour de manière incrémentielle, efficacement et en ligne, en dépit de volumes soutenus d'écritures. La mise à jour d'index cohérente est cruciale pour servir les requêtes en respectant le niveau de cohérence défini par l'utilisateur pour le service du document.

-	Prise en charge de l'infrastructure mutualisée : étant donné le modèle basé sur la réservation pour la gouvernance des ressources sur les locataires, les mises à jour d'index sont effectuées dans le budget des ressources système (processeur, mémoire, opérations d'E/S par seconde) allouées par réplica. 

-	Efficacité du stockage : pour des raisons économiques, la surcharge de stockage sur disque de l'index est limitée et prévisible. Ceci est très important, car DocumentDB permet au développeur de trouver des compromis en fonction des coûts entre la surcharge d'index et les performances des requêtes.  

Reportez-vous aux [exemples DocumentDB](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content) sur MSDN pour obtenir des exemples montrant comment configurer la stratégie d'indexation d'une collection. Nous allons à présent détailler davantage le langage SQL de DocumentDB.


##Notions de base sur les requêtes DocumentDB
Chaque requête se compose d'une clause SELECT et de clauses FROM et WHERE facultatives conformes aux normes ANSI-SQL. Généralement, pour chaque requête, la source de la clause FROM est énumérée. Puis le filtre de la clause WHERE est appliqué sur la source pour extraire un sous-ensemble de documents JSON. Finalement, la clause SELECT est utilisée pour projeter les valeurs JSON demandées dans la liste sélectionnée.
    
    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]    


##Clause FROM
La clause `FROM <from_specification>` est facultative, sauf si la source est filtrée ou projetée plus loin dans la requête. L'objectif de cette clause est de spécifier la source des données à partir de laquelle la requête doit fonctionner. Généralement, l'intégralité de la collection est la source, mais parfois, il peut s'agir plutôt d'un sous-ensemble de la collection. 

Une requête telle que `SELECT * FROM Families` indique que l'intégralité de la collection Families est la source de l'énumération. Un identificateur ROOT spécial peut être utilisé pour représenter la collection au lieu d'utiliser le nom de la collection. 
La liste suivante contient les règles appliquées par requête :

- La collection peut être un alias, tel que `SELECT f.id FROM Families AS f` ou simplement `SELECT f.id FROM Families f`.  `f` is the equivalent of `Families`. `AS` est un mot clé facultatif pour convertir l'identificateur en alias.

-	Notez qu'une fois l'alias appliqué, vous ne pouvez plus lier la source d'origine. Par exemple, `SELECT Familes.id FROM Families f` est syntaxiquement incorrect dans la mesure où l'identificateur Families ne peut plus être résolu.

-	Toutes les propriétés qui doivent être référencées doivent être entièrement qualifiées. Si le schéma strict n'est pas respecté, ceci est renforcé pour éviter toute liaison ambiguë.  `SELECT id FROM Families f` est donc syntaxiquement incorrect, car la propriété `id` n'est pas liée.
	
###Sous-documents
Vous pouvez également réduire la source à un sous-ensemble. Par exemple, en cas d'énumération de la seule sous-arborescence de chaque document, le sous-dossier racine peut alors devenir la source, comme indiqué dans l'exemple suivant.

**Requête**

	SELECT * 
	FROM Families.children

**Résultats**  

	[
	  [
	    {
	        "firstName": "Henriette Thaulow",
	        "gender": "female",
	        "grade": 5,
	        "pets": [
	          {
	              "givenName": "Fluffy"
	          }
	        ]
	    }
	  ],
	  [
	    {
	        "familyName": "Merriam",
	        "givenName": "Jesse",
	        "gender": "female",
	        "grade": 1
	    },
	    {
	        "familyName": "Miller",
	        "givenName": "Lisa",
	        "gender": "female",
	        "grade": 8
	    }
	  ]
	]

Même si la source est un tableau dans l'exemple précédent, il est possible d'utiliser un objet en tant que source, comme indiqué dans l'exemple suivant. Toute valeur JSON valide (définie) pouvant être trouvée dans la source est incluse dans le résultat de la requête. Si certaines familles n'ont pas de valeur `address.state`, elles sont exclues du résultat de la requête.

**Requête**

	SELECT * 
	FROM Families.address.state

**Résultats**

	[
	  "WA", 
	  "NY"
	]


##Clause WHERE
La clause WHERE (**`WHERE < condition_filtre>`**) est facultative. Elle indique les conditions que doivent respecter les documents JSON fournis par la source pour être inclus dans le résultat. Chaque document JSON doit évaluer les conditions indiquées sur " true " pour être inclus dans le résultat. La clause WHERE est utilisée par la couche d'index pour déterminer le sous-ensemble le plus petit absolu de documents sources pouvant appartenir au résultat. 

La requête suivante demande des documents qui contiennent une propriété de nom dont la valeur est `AndersenFamily`. Tous les documents n'ayant pas la propriété name ou ceux dont la valeur de la propriété name ne correspond pas à `AndersenFamily` sont exclus. 

**Requête**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Résultats**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


L'exemple précédent illustrait une simple requête d'égalité. Le langage SQL de DocumentDB prend également en charge plusieurs expressions scalaires. Les plus répandues sont les expressions binaires et unaires. Les références de propriété de l'objet JSON source sont également des expressions valides. 

Les opérateurs binaires suivants sont actuellement pris en charge et peuvent être utilisés dans les requêtes, comme indiqué dans les exemples suivants :  
<table>
<tr>
<td>Opérateurs arithmétiques</td>	
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Opérateurs au niveau du bit</td>	
<td>|, &, ^, <<, >>, >>> (décalage vers la droite avec remplissage de zéros) </td>
</tr>
<tr>
<td>Opérateurs logiques</td>
<td>AND, OR</td>
</tr>
<tr>
<td>Opérateurs de comparaison</td>	
<td>=, !=, >, >=, <, <=, <></td>
</tr>
<tr>
<td>String</td>	
<td>|| (concaténer)</td>
</tr>
</table>  

Examinons certaines requêtes utilisant des opérateurs binaires.

	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade % 2 = 1     -- matching grades == 5, 1
	
	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade ^ 4 = 1    -- matching grades == 5
	
	SELECT *
	FROM Families.children[0] c
	WHERE c.grade >= 5     -- matching grades == 5


Les opérateurs unaires +,-, ~ et NOT sont également pris en charge et peuvent être utilisés dans des requêtes comme illustré dans l'exemple suivant :

	SELECT *
	FROM Families.children[0] c
	WHERE NOT(c.grade = 5)  -- matching grades == 1
	
	SELECT *
	FROM Families.children[0] c
	WHERE (-c.grade = -5)  -- matching grades == 5



En plus des opérateurs binaires et unaires, les références de propriétés sont également autorisées. Par exemple, `SELECT * FROM Families f WHERE f.isRegistered` retourne le document JSON qui contient la propriété `isRegistered` dont la valeur est égale à la valeur JSON `true`. Toutes les autres valeurs (false, null, Undefined, `<number>`, `<string>`, `<object>`, `<array>`, etc.) entraînent l'exclusion du document source du résultat. 

###Opérateurs d'égalité et de comparaison
Le tableau suivant répertorie les résultats des comparaisons d'égalité dans le langage SQL de DocumentDB entre deux types JSON.
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Undefined</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Boolean</strong>
         </td>
         <td valign="top">
            <strong>Number</strong>
         </td>
         <td valign="top">
            <strong>String</strong>
         </td>
         <td valign="top">
            <strong>Objet</strong>
         </td>
         <td valign="top">
            <strong>Array</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Undefined<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Boolean<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Number<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>String<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objet<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Array<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

Pour les autres opérateurs de comparaison tels que >, >=,! =, < et <=, les règles suivantes s'appliquent :   

-	Une comparaison entre des types entraîne le résultat Undefined.
-	Une comparaison entre deux objets ou deux tableaux entraîne le résultat Undefined.   

Si le résultat de l'expression scalaire dans le filtre est Undefined, le document correspondant ne doit pas être inclus dans le résultat, car Undefined n'équivaut pas logiquement à " true ".

###Mot clé BETWEEN
Vous pouvez également utiliser le mot clé BETWEEN pour exprimer des requêtes sur des plages de valeurs, comme dans SQL ANSI. Vous pouvez utiliser BETWEEN sur n'importe quel type de primitive JSON (nombres, chaînes, valeurs booléennes et valeurs nulles). 

Par exemple, cette requête retourne tous les documents de la famille dans lesquels la note du premier enfant est comprise entre 1 et 5 (tous deux inclus). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Contrairement à SQL ANSI, vous pouvez également utiliser la clause BETWEEN dans la clause FROM, comme dans l'exemple suivant.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Pour accélérer le temps d'exécution de requête, pensez à créer une stratégie d'indexation qui utilise un type d'index de plage sur tous les chemins d'accès/propriétés numériques qui sont filtrés dans la clause BETWEEN. 

La principale différence entre l'utilisation de BETWEEN dans DocumentDB et ANSI SQL est que vous pouvez exprimer des requêtes de plage sur des propriétés de types mixtes. Vous pourriez par exemple faire en sorte que " grade " soit un nombre (5) dans certains documents et des chaînes dans d'autres (" grade4 "). Dans ces cas-là, comme dans JavaScript, une comparaison entre deux types différents a comme résultat " undefined " et le document est ignoré.


###Opérateurs logiques (AND, OR et NOT)
Les opérateurs logiques interviennent sur des valeurs booléennes. Les tables de vérité logiques de ces opérateurs sont présentées dans les tableaux suivants.

<table style = "width:300px">
    <tbody>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>OR</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    True
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    Undefined
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

<table style = "width:300px">
    <tbody>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>AND</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    False
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    Undefined
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

<table style = "width:300px">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>NOT</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong></strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    False
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    True
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

###Opérateurs Ternary (?) et Coalesce (??) :
Vous pouvez utiliser les opérateurs Ternary et Coalesce pour créer des expressions conditionnelles, un peu comme dans des langages de programmation courants tels que C# et JavaScript. 

L'opérateur Ternary (?) peut être très pratique lors de la construction de nouvelles propriétés JSON à la volée. Par exemple, vous pouvez maintenant écrire des requêtes pour classer les niveaux de classe dans un format lisible tel que Débutant/Intermédiaire/Avancé, comme illustré ci-dessous.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Vous pouvez également imbriquer les appels à l'opérateur, comme dans la requête ci-dessous.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Comme avec d'autres opérateurs de requête, si les propriétés référencées dans l'expression conditionnelle sont manquantes dans un document, ou si les types comparés sont différents, ces documents sont exclus dans les résultats de requête.

Vous pouvez utiliser l'opérateur Coalesce (?) pour vérifier la présence d'une propriété dans un document (c'est-à-dire vérifier si elle est définie). Cela est utile lors de l'interrogation de données semi-structurées ou de types différents. Par exemple, cette requête retourne " lastName " s'il est présent ou " surname " dans le cas contraire.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

De même, vous pouvez interroger l'absence d'une propriété (" undefined ") comme dans l'exemple suivant.

    SELECT *
    FROM classes c
    WHERE c.lastName ?? true

##Clause SELECT
La clause SELECT (**`SELECT <liste_sélection>`**) est obligatoire. Elle indique les valeurs qui seront récupérées dans la requête, tout comme dans ANSI-SQL. Le sous-ensemble filtré au début des documents source est transmis à la phase de projection, où les valeurs JSON spécifiées sont récupérées et un nouvel objet JSON est construit, pour chaque entrée qui lui est transmise. 

L'exemple ci-dessous illustre une requête SELECT classique. 

**Requête**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Résultats**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


###Propriétés imbriquées
Dans l'exemple suivant, nous allons projeter les deux propriétés imbriquées `f.address.state` et `f.address.city`.

**Requête**

	SELECT f.address.state, f.address.city
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Résultats**

	[{
	  "state": "WA", 
	  "city": "seattle"
	}]


La projection prend également en charge les expressions JSON, comme le montre l'exemple suivant.

**Requête**

	SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Résultats**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle", 
	    "name": "AndersenFamily"
	  }
	}]


Observons le rôle de `$1` ici. La clause `SELECT` doit créer un objet JSON et, comme aucune clé n'est fournie, nous utilisons des noms de variable d'argument implicites commençant par `$1`. Par exemple, cette requête retourne deux variables d'arguments implicites, étiquetées `$1` et `$2`.

**Requête**

	SELECT { "state": f.address.state, "city": f.address.city }, 
	       { "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Résultats**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "$2": {
	    "name": "AndersenFamily"
	  }
	}]


###Alias
﻿À présent, nous allons développer l'exemple précédent en appliquant des alias de valeurs explicites. AS est le mot clé utilisé pour l'application d'alias. Notez que cela est facultatif, comme indiqué lors de la projection de la seconde valeur en tant que `NameInfo`. 

Si une requête a deux propriétés portant le même nom, l'alias doit être utilisé pour renommer l'une ou l'autre des propriétés, pour éviter toute ambiguïté dans le résultat projeté.

**Requête**

	SELECT 
	       { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
	       { "name": f.id } NameInfo
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Résultats**

	[{
	  "AddressInfo": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "NameInfo": {
	    "name": "AndersenFamily"
	  }
	}]


###Expressions scalaires
En plus des références de propriété, la clause SELECT prend également en charge des expressions scalaires telles que les constantes, les expressions arithmétiques, les expressions logiques, etc. Par exemple, voici une requête " Hello World " simple.

**Requête**

	SELECT "Hello World"

**Résultats**

	[{
	  "$1": "Hello World"
	}]


Voici un exemple plus complexe utilisant une expression scalaire.

**Requête**

	SELECT ((2 + 11 % 7)-2)/3	

**Résultats**

	[{
	  "$1": 1.33333
	}]


Dans l'exemple suivant, le résultat de l'expression scalaire est un booléen.

**Requête**

	SELECT f.address.city = f.address.state AS AreFromSameCityState
	FROM Families f	

**Résultats**

	[
	  {
	    "AreFromSameCityState": false
	  }, 
	  {
	    "AreFromSameCityState": true
	  }
	]


###Création d'objet et de tableau
Une autre fonctionnalité clé du langage SQL de DocumentDB est la possibilité de créer un tableau ou un objet. Dans l'exemple précédent, notez que nous avons créé un objet JSON. De même, on peut également construire des tableaux comme indiqué dans les exemples suivants.

**Requête**

	SELECT [f.address.city, f.address.state] AS CityState 
	FROM Families f	

**Résultats**  

	[
	  {
	    "CityState": [
	      "seattle", 
	      "WA"
	    ]
	  }, 
	  {
	    "CityState": [
	      "NY", 
	      "NY"
	    ]
	  }
	]

###Mot clé VALUE
Le mot clé **VALUE** fournit une méthode pour renvoyer une valeur JSON. Par exemple, la requête ci-dessous retourne le scalaire `"Hello World"` au lieu de `{$1: "Hello World"}`.

**Requête**

	SELECT VALUE "Hello World"

**Résultats**

	[
	  "Hello World"
	]


La requête suivante retourne la valeur JSON sans l'étiquette `"address"` dans les résultats.

**Requête**

	SELECT VALUE f.address
	FROM Families f	

**Résultats**  

	[
	  {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }, 
	  {
	    "state": "NY", 
	    "county": "Manhattan", 
	    "city": "NY"
	  }
	]

L'exemple suivant développe ceci pour expliquer comment renvoyer des valeurs JSON primitives, comme le niveau feuille de l'arborescence JSON. 

**Requête**

	SELECT VALUE f.address.state
	FROM Families f	

**Résultats**

	[
	  "WA",
	  "NY"
	]


###Opérateur *
L'opérateur spécial (*) est pris en charge pour projeter le document tel quel. Une fois utilisé, il doit être le seul champ projeté. La requête `SELECT * FROM Families f` est correcte, contrairement aux requêtes `SELECT VALUE * FROM Families f ` et  `SELECT *, f.id FROM Families f `.

**Requête**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Résultats**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}]

##Concepts avancés
###Itération
Une nouvelle construction a été ajoutée via le mot clé **IN** de SQL DocumentDB pour prendre en charge l'itération sur les tableaux JSON. La source FROM fournit une prise en charge pour l'itération. Commençons par l'exemple suivant :

**Requête**

	SELECT * 
	FROM Families.children

**Résultats**  

	[
	  [
	    {
	      "firstName": "Henriette Thaulow", 
	      "gender": "female", 
	      "grade": 5, 
	      "pets": [{ "givenName": "Fluffy"}]
	    }
	  ], 
	  [
	    {
	        "familyName": "Merriam", 
	        "givenName": "Jesse", 
	        "gender": "female", 
	        "grade": 1
	    }, 
	    {
	        "familyName": "Miller", 
	        "givenName": "Lisa", 
	        "gender": "female", 
	        "grade": 8
	    }
	  ]
	]

﻿À présent, intéressons-nous à une autre requête, qui effectue une itération sur les enfants de la collection. Notez la différence dans le tableau de sortie. Cet exemple fractionne `children` et regroupe les résultats en un seul tableau.  

**Requête**

	SELECT * 
	FROM c IN Families.children

**Résultats**  

	[
	  {
	      "firstName": "Henriette Thaulow",
	      "gender": "female",
	      "grade": 5,
	      "pets": [{ "givenName": "Fluffy" }]
	  },
	  {
	      "familyName": "Merriam",
	      "givenName": "Jesse",
	      "gender": "female",
	      "grade": 1
	  },
	  {
	      "familyName": "Miller",
	      "givenName": "Lisa",
	      "gender": "female",
	      "grade": 8
	  }
	]

Cette utilisation peut être généralisée pour filtrer chaque entrée du tableau, comme dans l'exemple suivant.

**Requête**

	SELECT c.givenName
	FROM c IN Families.children
	WHERE c.grade = 8

**Résultats**  

	[{
	  "givenName": "Lisa"
	}]

###Jointures
Dans une base de données relationnelle, il est très important de joindre les tables. Ceci est la conséquence logique de la conception de schémas normalisés. Au contraire, DocumentDB traite les modèles de données dénormalisés de documents sans schéma. Il s'agit de l'équivalent logique d'une " jointure réflexive ".

La syntaxe que le langage prend en charge est <from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>. D'une façon générale, ceci renvoie un ensemble de **N**-tuples (un tuple avec **N** valeurs). Les valeurs de chaque tuple sont produites par l'itération de tous les alias de la collection sur leurs ensembles respectifs. En d'autres termes, il s'agit d'un produit croisé complet des ensembles participants à la jointure.

Les exemples suivants illustrent le fonctionnement de la clause JOIN. Dans l'exemple suivant, le résultat est vide, car le produit croisé de chaque document de la source et d'un ensemble vide est vide.

**Requête**

	SELECT f.id
	FROM Families f
	JOIN f.NonExistent

**Résultats**  

	[{
	}]


Dans l'exemple suivant, la jointure concerne la racine du document et la sous-racine `children`. Il s'agit d'un produit croisé entre deux objets JSON. Le fait que les enfants soient compris dans un tableau n'est pas valide dans le JOIN, car nous traitons une seule racine qui est le tableau des enfants. Nous n'obtenons donc que deux résultats, car le produit croisé de chaque document avec le tableau renvoie exactement un seul document.

**Requête**

	SELECT f.id
	FROM Families f
	JOIN f.children
 
**Résultats**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]


L'exemple suivant illustre une jointure plus conventionnelle :

**Requête**

	SELECT f.id
	FROM Families f
	JOIN c IN f.children 

**Résultats**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]



La première chose à noter est que l'élément `from_source` de la clause **JOIN** est un itérateur. Dans ce cas, le flux est le suivant :  

-	Développez chaque élément enfant **c** du tableau.
-	Appliquez le produit croisé de la racine du document **f** avec chaque élément enfant **c** aplati à la première étape.
-	Enfin, projetez seule la propriété du nom **f** de l'objet racine. 

Le premier document (`AndersenFamily`) contient uniquement un élément enfant. L'ensemble des résultats contient donc un seul objet correspondant à ce document. Le second document (`WakefieldFamily`) contient deux enfants. Le produit croisé produit donc un objet distinct pour chaque enfant, résultant en deux objets, un pour chaque enfant correspondant à ce document. Notez que les champs racine de ces deux documents seront identiques, tout comme on peut l'attendre d'un produit croisé.

La véritable utilité de la syntaxe JOIN est de former des tuples à partir du produit croisé dans une forme qui serait autrement difficile à projeter. En outre, comme nous allons le voir dans l'exemple ci-dessous, vous pouvez filtrer la combinaison d'un tuple permettant à l'utilisateur de choisir une condition respectée par l'ensemble des tuples.

**Requête**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
 
**Résultats**

	[
	  {
	    "familyName": "AndersenFamily", 
	    "childFirstName": "Henriette Thaulow", 
	    "petName": "Fluffy"
	  }, 
	  {
	    "familyName": "WakefieldFamily", 
	    "childGivenName": "Jesse", 
	    "petName": "Goofy"
	  }, 
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]



Cet exemple est une extension naturelle du précédent, et effectue une double jointure. Le produit croisé peut donc être affiché comme le pseudo-code ci-dessous.

	for-each(Family f in Families)
	{	
		for-each(Child c in f.children)
		{
			for-each(Pet p in c.pets)
			{
				return (Tuple(f.id AS familyName, 
	              c.givenName AS childGivenName, 
	              c.firstName AS childFirstName,
	              p.givenName AS petName));
			}
		}
	}

`AndersenFamily` a un enfant qui a un animal. Par conséquent, le produit croisé renvoie une ligne (1*1*1) à partir de cette famille. Cependant, WakefieldFamily a deux enfants, mais seul l'un d'eux, " Jesse ", a des animaux. Jesse a 2 animaux. Le produit croisé renvoie donc 1*1*2 = 2 lignes à partir de cette famille.

L'exemple suivant ajoute un filtre supplémentaire sur `pet`. Ceci exclut tous les tuples où le nom de l'animal n'est pas " Shadow ". Notez que nous pouvons développer des tuples à partir de tableaux, filtrer n'importe quel élément du tuple et projeter n'importe quelle combinaison d'éléments. 

**Requête**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
	WHERE p.givenName = "Shadow"

**Résultats**

	[
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]


##Intégration JavaScript
DocumentDB fournit un modèle de programmation pour l'exécution de la logique d'application JavaScript directement sur les collections en termes de procédures stockées et de déclencheurs. Ceci permet pour les deux :

-	La possibilité d'effectuer des CRUD transactionnels à hautes performances et d'interroger les documents d'une collection grâce à l'intégration approfondie de l'exécution JavaScript directement dans le moteur de base de données. 
-	Une modélisation naturelle du flux de contrôle, de l'étendue des variables, de l'attribution et de l'intégration des primitives de gestion d'exception avec des transactions de base de données. Pour plus de détails sur la prise en charge DocumentDB pour l'intégration JavaScript, veuillez consulter la documentation sur la programmation côté serveur de JavaScript.

###Fonctions définies par l'utilisateur
En plus des types déjà définis dans cet article, SQL de DocumentDB prend en charge les fonctions définies par l'utilisateur. En particulier, les fonctions définies par l'utilisateur scalaires sont prises en charge pour que les développeurs puissent transmettre de nombreux arguments ou aucun, puis renvoyer un seul argument en retour. La légalité des valeurs JSON de chacun de ces arguments est vérifiée.  

La syntaxe du langage SQL de DocumentDB est étendue pour prendre en charge la logique d'application personnalisée à l'aide de ces fonctions définies par l'utilisateur. Ces dernières peuvent être enregistrées avec DocumentDB, puis référencées dans le cadre d'une requête SQL. En fait, les fonctions définies par l'utilisateur sont conçues avec soin pour pouvoir être appelées par des requêtes. En conséquence, les fonctions définies par l'utilisateur ne peuvent pas accéder à l'objet de contexte que possèdent les autres types JavaScript (procédures stockées, déclencheurs). Comme les requêtes s'exécutent en lecture seule, elles peuvent démarrer sur des réplicas principaux ou secondaires. Par conséquent, les fonctions définies par l'utilisateur sont conçues pour être exécutées sur des réplicas secondaires, contrairement à d'autres types JavaScript.

Voici un exemple de méthode d'enregistrement de fonction définie par l'utilisateur sur la base de données DocumentDB, plus précisément sous une collection de documents.

   
	   UserDefinedFunction sqrtUdf = new UserDefinedFunction
	   {
	       Id = "SQRT",
	       Body = @"function(number) { 
	                   return Math.sqrt(number);
	               };",
	   };
	   UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
	       collectionSelfLink/* link of the parent collection*/, 
	       sqrtUdf).Result;  
                                                                             
L'exemple ci-dessus crée une fonction définie par l'utilisateur dont le nom est `SQRT`. La fonction accepte une seule valeur JSON `number` et calcule la racine carrée du nombre à l'aide de la bibliothèque Math.


Nous pouvons maintenant utiliser cette fonction définie par l'utilisateur dans une requête, dans une projection. Les fonctions définies par l'utilisateur doivent être qualifiées par le préfixe respectant la casse " udf." quand elles sont appelées à partir de requêtes. 

>[AZURE.NOTE] Jusqu'au 17 mars 2015, DocumentDB prenait en charge les appels de fonctions définies par l'utilisateur sans préfixe " udf. ", comme SELECT SQRT(5). Ce modèle d'appel est maintenant déconseillé.  

**Requête**

	SELECT udf.SQRT(c.grade)
	FROM c IN Families.children

**Résultats**

	[
	  {
	    "$1": 2.23606797749979
	  }, 
	  {
	    "$1": 1
	  }, 
	  {
	    "$1": 2.8284271247461903
	  }
	]

Vous pouvez également utiliser les fonctions définies par l'utilisateur (et qualifiées par le préfixe " udf. ") dans un filtre, comme indiqué dans l'exemple suivant :

**Requête**

	SELECT c.grade
	FROM c IN Familes.children
	WHERE udf.SQRT(c.grade) = 1

**Résultats**

	[{
	    "grade": 1
	}]


Fondamentalement, les fonctions définies par l'utilisateur sont des expressions scalaires valides et peuvent être utilisées dans des projections et des filtres. 

Pour développer la puissance des fonctions définies par l'utilisateur, étudions un autre exemple de logique conditionnelle :

	   UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
	   {
	       Id = "SEALEVEL",
	       Body = @"function(city) {
	       		switch (city) {
	       		    case 'seattle':
	       		        return 520;
	       		    case 'NY':
	       		        return 410;
	       		    case 'Chicago':
	       		        return 673;
	       		    default:
	       		        return -1;
	                }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(collection.SelfLink, seaLevelUdf);
	
	
L'exemple ci-dessous utilise les fonctions définies par l'utilisateur.

**Requête**

	SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
	FROM Families f	

**Résultats**

	 [
	  {
	    "city": "seattle", 
	    "seaLevel": 520
	  }, 
	  {
	    "city": "NY", 
	    "seaLevel": 410
	  }
	]


Les exemples suivants illustrent l'aptitude des fonctions définies par l'utilisateur à intégrer la puissance du langage JavaScript dans le SQL de DocumentDB afin de fournir une interface programmable enrichie pour mener à bien des procédures complexes et employer une logique conditionnelle en exploitant les capacités intégrées de l'exécution JavaScript.

Le langage SQL de DocumentDB fournit les arguments aux fonctions définies par l'utilisateur pour chaque document de la source lors de l'étape actuelle (clause WHERE ou SELECT) du traitement des fonctions définies par l'utilisateur. Le résultat est intégré au pipeline d'exécution générale en toute transparence. Si les propriétés de référence des paramètres des fonctions définies par l'utilisateur ne sont pas disponibles dans la valeur JSON, les paramètres sont considérés comme non définis et l'appel des fonctions définies par l'utilisateur est entièrement ignoré. De même, si le résultat des fonctions définies par l'utilisateur est indéfini, il n'est pas inclus dans le résultat. 

En résumé, les fonctions définies par l'utilisateur sont des outils efficaces pour mener à bien des logiques métier complexes dans le cadre d'une requête.

###Évaluation d'opérateur
DocumentDB, en étant une base de données JSON, peut établir des correspondances entre les opérateurs JavaScript et ses sémantiques d'évaluation. Même si DocumentDB tente de préserver les sémantiques JavaScript dans le cadre de la prise en charge JSON, l'opération d'évaluation dévie dans certains cas.

Dans le langage de requête SQL de DocumentDB, contrairement au SQL classique, les types de valeurs sont souvent inconnus jusqu'à ce que les valeurs soient réellement extraites de la base de données. Afin d'exécuter les requêtes de manière efficace, la plupart des opérateurs ont des exigences de type strictes. 

Le langage SQL de DocumentDB n'effectue pas de conversions implicites, contrairement à JavaScript. Par exemple, une requête comme `SELECT * FROM Person p WHERE p.Age = 21` correspond à des documents qui contiennent une propriété Age dont la valeur est 21. Tout autre document dont la propriété Age correspond à la chaîne " 21 ", ou
éventuellement à d'autres variantes infinies comme " 021 ", " 21.0 ", " 0021 ", " 00021 ", etc. ne sera pas mis en correspondance. 
Ce comportement contraste avec celui de JavaScript où les valeurs de chaîne sont implicitement converties en nombres (à partir de l'opérateur, par exemple : ==). Ce choix est crucial pour une correspondance d'index efficace dans le langage SQL de DocumentDB. 

##SQL paramétré
DocumentDB prend en charge les requêtes avec des paramètres exprimées avec la notation @ classique. SQL paramétré fournit une gestion et un échappement robustes de l'entrée utilisateur et empêche l'exposition accidentelle des données par l'intermédiaire de l'injection SQL. 

Par exemple, vous pouvez écrire une requête qui prend le nom et l'état de l'adresse comme paramètres, puis l'exécuter pour différentes valeurs de nom et d'état d'adresse en fonction de l'entrée utilisateur.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Cette demande peut ensuite être envoyée à DocumentDB comme requête JSON paramétrable, comme indiqué ci-dessous.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

Les valeurs de paramètres peuvent être n'importe quel format JSON valide (chaînes, nombres, valeurs booléennes, null, même des tableaux ou des valeurs JSON imbriquées). De plus, comme DocumentDB est sans schéma, les paramètres ne sont pas validés par rapport à un type.

##Fonctions intégrées
DocumentDB prend également en charge plusieurs fonctions intégrées pour des opérations courantes. Ces fonctions s'utilisent dans les requêtes comme les fonctions définies par l'utilisateur.

<table>
<tr>
<td>Fonctions mathématiques</td>	
<td>ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE et TRUNC</td>
</tr>
<tr>
<td>Fonctions de vérification du type</td>	
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT et IS_STRING</td>
</tr>
</table>  

Si vous utilisez actuellement une fonction définie par l'utilisateur pour laquelle une fonction intégrée est désormais disponible, remplacez-la par la fonction intégrée correspondante, car celle-ci s'exécutera plus rapidement et sera plus performante. 

###Fonctions mathématiques
Chaque fonction mathématique effectue un calcul, généralement basé sur les valeurs d'entrée fournies comme arguments, et retourne une valeur numérique. Ce tableau répertorie les fonctions mathématiques intégrées qui sont prises en charge.

<table>
<tr>
<td><strong>Utilisation</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td>	
<td>Retourne la valeur (positive) absolue de l'expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">CEILING (num_expr)</a></td>	
<td>Retourne le plus petit nombre entier qui est supérieur ou égal à l'expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td>	
<td>Retourne le plus grand nombre entier qui est inférieur ou égal à l'expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td>	
<td>Retourne l'exposant de l'expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [,base])</a></td>	
<td>Retourne le logarithme népérien de l'expression numérique spécifiée, ou le logarithme dans la base spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td>	
<td>Retourne le logarithme en base 10 de l'expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ROUND (num_expr)</a></td>	
<td>Retourne une valeur numérique, arrondie au nombre entier le plus proche.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">TRUNC (num_expr)</a></td>	
<td>Retourne une valeur numérique, tronquée au nombre entier le plus proche.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">SQRT (num_expr)</a></td>	
<td>Retourne la racine carrée de l'expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">SQUARE (num_expr)</a></td>	
<td>Retourne le carré de l'expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POWER (num_expr, num_expr)</a></td>	
<td>Retourne un nombre spécifié élevé à la puissance spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SIGN (num_expr)</a></td>	
<td>Retourne la valeur du signe (-1, 0, 1) de l'expression numérique spécifiée.</td>
</tr>
</table> 

Par exemple, vous pouvez désormais exécuter des requêtes similaires à celle-ci :

**Requête**

    SELECT VALUE ABS(-4)

**Résultats**

    [4]

La principale différence entre les fonctions de DocumentDB et le langage SQL ANSI est que les fonctions sont conçues pour s'exécuter avec des données sans schéma et des données avec un schéma mixte. Par exemple, si vous avez un document pour lequel la propriété Size est manquante ou a une valeur non numérique (de type " inconnu "), le document est ignoré, et aucune erreur n'est retournée.

###Fonctions de vérification du type
Les fonctions de vérification du type vous permettent de vérifier le type d'une expression donnée dans les requêtes SQL. Elles peuvent être utilisées pour déterminer instantanément le type variable ou inconnu des propriétés dans les documents. Le tableau répertorie les fonctions de vérification du type intégrées qui sont prises en charge.

<table>
<tr>
  <td><strong>Utilisation</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type tableau.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type booléen.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type numérique.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type objet JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type chaîne.</td>
</tr>
</table>

Avec ces fonctions, vous pouvez désormais exécuter des requêtes similaires à celle-ci :

**Requête**

    SELECT VALUE IS_NUMBER(-4)

**Résultats**

    [true]


##LINQ vers le langage SQL de DocumentDB
LINQ est un modèle de programmation .NET qui exprime un calcul en tant que requête sur des flux d'objets. DocumentDB fournit une bibliothèque côté client pour interagir avec LINQ en facilitant la conversion entre les objets JSON et .NET et un mappage à partir d'un sous-ensemble de requêtes LINQ vers des requêtes DocumentDB. 

L'image suivante illustre l'architecture de prise en charge des requêtes LINQ à l'aide de DocumentDB.  En utilisant le client DocumentDB, les développeurs peuvent créer un objet **IQueryable** dirigeant les requêtes vers le fournisseur de requête de DocumentDB, qui traduit alors les requêtes LINQ en requêtes DocumentDB. Ces requêtes sont ensuite transmises au serveur DocumentDB pour récupérer un ensemble de résultats au format JSON. Les résultats renvoyés sont désérialisés en un flux d'objets .NET, côté client.

![][1]
 


###Mappage .NET et JSON
Le mappage entre les objets .NET et les documents JSON est naturel : chaque champ de membre de données est mappé vers un objet JSON, où le nom du champ est mappé vers la partie " clé " de l'objet tandis que la partie " valeur " est mappée de façon récursive vers la partie de valeur de l'objet. Considérez l'exemple suivant. L'objet Family créé est mappé vers le document JSON, comme indiqué ci-dessous. ﻿À l'inverse, le document JSON est mappé vers un objet .NET.

**Classe C#**

	public class Family
	{
	    [JsonProperty(PropertyName="id")]
	    public string Id;
	    public Parent[] parents;
	    public Child[] children;
	    public bool isRegistered;
	};
	
	public struct Parent
	{
	    public string familyName;
	    public string givenName;
	};
	
	public class Child
	{
	    public string familyName;
	    public string givenName;
	    public string gender;
	    public int grade;
	    public List<Pet> pets;
	};
	
	public class Pet
	{
	    public string givenName;
	};
	
	public class Address
	{
	    public string state;
	    public string county;
	    public string city;
	};
	
	// Create a Family object.
	Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
	Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
	Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
	Pet pet = new Pet { givenName = "Fluffy" };
	Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
	Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	            "givenName": "Jesse", 
	            "gender": "female", 
	            "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	          "familyName": "Miller", 
	          "givenName": "Lisa", 
	          "gender": "female", 
	          "grade": 8 
	        }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	};



###Conversion LINQ en SQL
Le fournisseur de requêtes de DocumentDB effectue le meilleur mappage possible entre une requête LINQ et une requête SQL DocumentDB. Dans la description suivante, nous partons du principe que le lecteur connaît les principes de base de LINQ.

D'abord, pour le système de type, nous prenons en charge tous les types JSON primitifs : numérique, booléen, chaîne et Null. Seuls ces types JSON sont pris en charge. Les expressions scalaires suivantes sont prises en charge.

-	Valeurs constantes : elles incluent des valeurs constantes des types de données primitifs au moment de l'évaluation de la requête.

-	Expressions d'index de propriété/tableau : ces expressions font référence à la propriété d'un objet ou d'un élément de tableau.

		family.Id;
		family.children[0].familyName;
		family.children[0].grade;
		family.children[n].grade; //n is an int variable

-	Expressions arithmétiques : elles incluent les expressions arithmétiques communes sur les valeurs numériques et booléennes. Pour obtenir une liste complète, reportez-vous à la spécification SQL.

		2 * family.children[0].grade;
		x + y;

-	Expressions de comparaison de chaîne : elles incluent les comparaisons de valeurs de chaînes à certaines valeurs de chaînes constantes.  
 
		mother.familyName == "Smith";
		child.givenName == s; //s is a string variable

-	Expressions de création d'objet/tableau : ces expressions renvoient un objet de type de valeur composée ou de type anonyme ou un tableau de tels objets. Ces valeurs peuvent être imbriquées.

		new Parent { familyName = "Smith", givenName = "Joe" };
		new { first = 1, second = 2 }; //an anonymous type with 2 fields              
		new int[] { 3, child.grade, 5 };

###Opérateurs de requête
Voici certains exemples illustrant comment certains des opérateurs de requête LINQ standard sont traduits en requêtes DocumentDB.

####Opérateur Select
La syntaxe est `input.Select(x => f(x))`, où `f` est une expression scalaire.

**Expression Lambda LINQ**

	input.Select(family => family.parents[0].familyName);

**SQL** 

	SELECT VALUE f.parents[0].familyName
	FROM Families f



**Expression Lambda LINQ**

	input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

	SELECT VALUE f.children[0].grade + c
	FROM Families f 



**Expression Lambda LINQ**

	input.Select(family => new
	{
	    name = family.children[0].familyName,
	    grade = family.children[0].grade + 3
	});


**SQL** 

	SELECT VALUE {"name":f.children[0].familyName, 
	              "grade": f.children[0].grade + 3 }
	FROM Families f



####Opérateur SelectMany
La syntaxe est `input.SelectMany(x => f(x))`, où `f` est une expression scalaire qui retourne un type de collection.

**Expression Lambda LINQ**

	input.SelectMany(family => family.children);

**SQL** 

	SELECT VALUE child
	FROM child IN Families.children



####Opérateur Where
La syntaxe est `input.Where(x => f(x))`, où `f` est une expression scalaire qui retourne une valeur booléenne.

**Expression Lambda LINQ**

	input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith" 



**Expression Lambda LINQ**

	input.Where(
	    family => family.parents[0].familyName == "Smith" && 
	    family.children[0].grade < 3);

**SQL** 

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"
	AND f.children[0].grade < 3


###Requêtes composites
Les opérateurs suivants peuvent être composés pour former des requêtes plus puissantes. Comme DocumentDB prend en charge les collections imbriquées, la composition peut être concaténée ou imbriquée.

####Concaténation 

La syntaxe est une requête `input(.|.SelectMany())(.Select()|.Where())*`. A concatenated query can start with an optional `SelectMany` suivie de plusieurs opérateurs `Select` ou `Where`.


**Expression Lambda LINQ**

	input.Select(family=>family.parents[0])
	    .Where(familyName == "Smith");

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"



**Expression Lambda LINQ**

	input.Where(family => family.children[0].grade > 3)
	    .Select(family => family.parents[0].familyName);

**SQL** 

	SELECT VALUE f.parents[0].familyName
	FROM Families f
	WHERE f.children[0].grade > 3



**Expression Lambda LINQ**

	input.Select(family => new { grade=family.children[0].grade}).
	    Where(anon=> anon.grade < 3);
            
**SQL** 

	SELECT *
	FROM Families f
	WHERE ({grade: f.children[0].grade}.grade > 3)



**Expression Lambda LINQ**

	input.SelectMany(family => family.parents)
	    .Where(parent => parents.familyName == "Smith");

**SQL** 

	SELECT *
	FROM p IN Families.parents
	WHERE p.familyName = "Smith"



####Imbrication

La syntaxe est `input.SelectMany(x=>x.Q())`, où Q est un opérateur `Select`, `SelectMany` ou `Where`.

Dans une requête imbriquée, la requête interne est appliquée à chaque élément de la collection externe. La requête interne peut faire référence aux champs des éléments dans la collection externe, comme des jointures réflexives : cette fonctionnalité est importante.

**Expression Lambda LINQ**

	input.SelectMany(family=> 
	    family.parents.Select(p => p.familyName));

**SQL** 

	SELECT VALUE p.familyName
	FROM Families f
	JOIN p IN f.parents


**Expression Lambda LINQ**

	input.SelectMany(family => 
	    family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = "Jeff"



**Expression Lambda LINQ**
            
	input.SelectMany(family => family.children.Where(
	    child => child.familyName == family.parents[0].familyName));

**SQL** 

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = f.parents[0].familyName


##Exécution de requêtes
DocumentDB expose les ressources via une API REST qui peut être appelée par n'importe quel langage capable de créer des requêtes HTTP/HTTPS. Par ailleurs, DocumentDB offre des bibliothèques de programmation pour plusieurs langages populaires comme .NET, Node.js, JavaScript et Python. L'API REST et les différentes bibliothèques prennent toutes en charge l'interrogation via SQL. Le Kit de développement logiciel (SDK) .NET prend en charge l'interrogation LINQ en plus du SQL.

Les exemples suivants montrent comment créer une requête et la soumettre par rapport à un compte de base de données DocumentDB.
###API REST
DocumentDB fournit un modèle de programmation RESTful ouvert sur HTTP. Vous pouvez approvisionner vos comptes de bases de données en utilisant un abonnement Azure. Le modèle de ressource de DocumentDB se compose d'ensembles de ressources sous un compte de base de données, toutes adressables via un URI stable et logique. Dans ce document, de tels ensembles de ressources sont désignés sous le nom de " flux ". Un compte de base de données se compose d'un ensemble de bases de données. Chacune d'elles contient plusieurs collections et chaque collection contient des documents, des fonctions définies par l'utilisateur et d'autres types de ressources.

Le modèle d'interaction de base avec ces ressources consiste à utiliser des verbes HTTP, tels que GET, PUT, POST et DELETE avec leur interprétation standard. Le verbe POST permet de créer une ressource, d'exécuter une procédure stockée ou d'émettre une requête DocumentDB. Les requêtes sont toujours des opérations en lecture seule sans effets secondaires.

Les exemples suivants illustrent l'utilisation d'une action POST pour une requête DocumentDB appliquée sur une collection contenant les deux exemples de documents utilisés jusqu'à présent. La requête contient un simple filtre sur la propriété de nom JSON. Notez l'utilisation des en-têtes `x-ms-documentdb-isquery` et Content-Type: `application/query+json` pour indiquer que l'opération est une requête.


**Demande**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
	

**Résultats**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
	x-ms-item-count: 1
	x-ms-request-charge: 0.32
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "id":"AndersenFamily",
	         "lastName":"Andersen",
	         "parents":[  
	            {  
	               "firstName":"Thomas"
	            },
	            {  
	               "firstName":"Mary Kay"
	            }
	         ],
	         "children":[  
	            {  
	               "firstName":"Henriette Thaulow",
	               "gender":"female",
	               "grade":5,
	               "pets":[  
	                  {  
	                     "givenName":"Fluffy"
	                  }
	               ]
	            }
	         ],
	         "address":{  
	            "state":"WA",
	            "county":"King",
	            "city":"seattle"
	         },
	         "_rid":"u1NXANcKogEcAAAAAAAAAA==",
	         "_ts":1407691744,
	         "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
	         "_etag":"00002b00-0000-0000-0000-53e7abe00000",
	         "_attachments":"_attachments\/"
	      }
	   ],
	   "count":1
	}


Le deuxième exemple illustre une requête plus complexe qui renvoie plusieurs résultats à partir de la jointure.

**Demande**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json
	
    {      
        "query": "SELECT 
				     f.id AS familyName, 
				     c.givenName AS childGivenName, 
				     c.firstName AS childFirstName, 
				     p.givenName AS petName 
				  FROM Families f 
				  JOIN c IN f.children 
				  JOIN p in c.pets",     
        "parameters": [] 
    }


**Résultats**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
	x-ms-item-count: 1
	x-ms-request-charge: 7.84
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "familyName":"AndersenFamily",
	         "childFirstName":"Henriette Thaulow",
	         "petName":"Fluffy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Goofy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Shadow"
	      }
	   ],
	   "count":3
	}


Si les résultats d'une requête ne tiennent pas sur une seule page, l'API REST retourne un jeton de liaison via l'en-tête de réponse `x-ms-continuation-token`. Les clients peuvent paginer les résultats en incluant l'en-tête dans les résultats suivants. Vous pouvez aussi contrôler le nombre de résultats par page via l'en-tête de nombre `x-ms-max-item-count`.

Pour gérer la stratégie de cohérence des données des requêtes, utilisez l'en-tête `x-ms-consistency-level` comme pour toutes les requêtes d'API REST. Pour maintenir la cohérence par session, vous devez aussi appliquer l'écho sur le dernier en-tête de cookie `x-ms-session-token` dans la demande de requête. Notez que la stratégie d'indexation de la collection interrogée peut également influencer la cohérence des résultats de la requête. Avec les paramètres de stratégie d'indexation par défaut, les collections de l'index sont toujours actualisées par rapport aux contenus du document et les résultats de la requête correspondront à la cohérence choisie pour les données. Si la stratégie d'indexation est passée en différé, les requêtes peuvent renvoyer des résultats obsolètes. Pour plus d'informations, consultez l'article [Niveaux de cohérence dans DocumentDB][consistency-levels].

Si la stratégie d'indexation configurée pour la collection ne peut pas prendre en charge la requête spécifiée, le serveur DocumentDB renvoie le code d'état 400 " Demande incorrecte ". Ce code est renvoyé pour les requêtes de plage par rapport aux chemins d'accès configurés pour les recherches (d'égalité) de hachage et pour les chemins d'accès explicitement exclus de l'indexation. L'en-tête `x-ms-documentdb-query-enable-scan` peut être spécifié pour permettre à la requête d'effectuer une analyse quand un index n'est pas disponible.

###Kit de développement logiciel (SDK) C# (.NET)
Le Kit de développement logiciel (SDK) .NET prend en charge l'interrogation LINQ et SQL. L'exemple suivant illustre l'exécution d'une simple requête de filtre présentée précédemment dans ce document.


	foreach (var family in client.CreateDocumentQuery(collectionLink, 
	    "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

	foreach (var family in (
	    from f in client.CreateDocumentQuery(collectionLink)
	    where f.Id == "AndersenFamily"
	    select f))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in client.CreateDocumentQuery(collectionLink)
	    .Where(f => f.Id == "AndersenFamily")
	    .Select(f => f))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


Cet exemple compare l'égalité de deux propriétés dans chaque document et utilise des projections anonymes. 


	foreach (var family in client.CreateDocumentQuery(collectionLink,
	    @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
	    FROM Families f 
	    WHERE f.address.city = f.address.state"))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
	foreach (var family in (
	    from f in client.CreateDocumentQuery<Family>(collectionLink)
	    where f.address.city == f.address.state
	    select new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .Where(f => f.address.city == f.address.state)
	    .Select(f => new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


Le prochain exemple illustre des jointures, exprimées via l'opérateur LINQ SelectMany.


	foreach (var pet in client.CreateDocumentQuery(collectionLink,
	      @"SELECT p
	        FROM Families f 
	             JOIN c IN f.children 
	             JOIN p in c.pets 
	        WHERE p.givenName = ""Shadow"""))
	{
	    Console.WriteLine("\tRead {0} from SQL", pet);
	}
	
	// Equivalent in Lambda expressions
	foreach (var pet in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .SelectMany(f => f.children)
	    .SelectMany(c => c.pets)
	    .Where(p => p.givenName == "Shadow"))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", pet);
	}



Le client .NET effectue une itération automatique à travers l'ensemble des pages des résultats de la requête dans les blocs foreach, comme indiqué précédemment. Les options de requête présentées dans la section sur l'API REST sont également disponibles dans le Kit de développement logiciel (SDK) .NET à l'aide des classes `FeedOptions` et `FeedResponse` dans la méthode CreateDocumentQuery. Vous pouvez aussi contrôler le nombre de pages en utilisant le paramètre `MaxItemCount`. 

Les développeurs peuvent aussi contrôler explicitement la pagination en créant `IDocumentQueryable` à l'aide de l'objet `IQueryable`, puis en lisant les valeurs` ResponseContinuationToken` et en repassant ces dernières en tant que `RequestContinuationToken` dans `FeedOptions`. `EnableScanInQuery` peut être configuré pour activer les analyses quand la requête n'est pas prise en charge par la stratégie d'indexation configurée.

Reportez-vous aux [Exemples .NET DocumentDB](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content) pour obtenir plus d'exemples de requêtes. 

###API JavaScript côté serveur 
DocumentDB fournit un modèle de programmation pour l'exécution de la logique d'application JavaScript directement sur les collections utilisant des procédures stockées et des déclencheurs. La logique JavaScript enregistrée au niveau d'une collection peut alors émettre des opérations de base de données sur les opérations des documents d'une collection donnée. Ces opérations sont encapsulées dans les transactions ACID ambiantes.

L'exemple suivant illustre l'utilisation de queryDocuments dans l'API JavaScript côté serveur pour créer des requêtes depuis l'intérieur des procédures stockées et des déclencheurs.


	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();
	    var collectionLink = collectionManager.getSelfLink()
	
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        { name: name, author: author },
	        function (err, documentCreated) {
	            if (err) throw new Error(err.message);
	
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function (err, matchingDocuments) {
	                    if (err) throw new Error(err.message);
	context.getResponse().setBody(matchingDocuments.length);
	
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don't need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);
	                    }
	                })
	        });
	}


##Références
1.	[Présentation d'Azure DocumentDB][introduction]
2.	[Spécification du langage SQL de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.	[Exemples .NET DocumentDB](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content)
4.	[Niveaux de cohérence dans DocumentDB][consistency-levels]
5.	ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.	JSON [http://json.org/](http://json.org/)
7.	Specification Javascript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.	LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.	Techniques d'évaluation des requêtes pour les bases de données volumineuses [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10.	Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11.	Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12.	Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins : Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13.     G. Graefe. The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md

<!--HONumber=49-->