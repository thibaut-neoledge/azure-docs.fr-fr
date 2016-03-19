<properties
   pageTitle="Guide de création d’un service de données pour le Marketplace | Microsoft Azure"
   description="Instructions détaillées pour créer, certifier et déployer un service de données que d’autres peuvent acheter sur Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="01/04/2016"
      ms.author="hascipio; avikova" />

# Présentation du schéma de nœuds pour le mappage d’un service web existant à OData via le langage CSDL
Ce document permet de clarifier la structure de nœuds pour le mappage d’un protocole OData au langage CSDL. Il est important de noter que la structure de nœuds est un code XML bien formé. Par conséquent, le schéma racine, parent et enfant s’applique lors de la conception de votre mappage OData.

## Éléments ignorés
Voici les éléments CSDL (nœuds XML) de haut niveau qui ne vont pas être utilisés par le serveur principal Azure Marketplace pendant l’importation de métadonnées du service web. Ils peuvent être présents, mais seront ignorés.

| Élément | Portée |
|----|----|
| Élément Using | Le nœud, les sous-nœuds et tous les attributs |
| Élément Documentation | Le nœud, les sous-nœuds et tous les attributs |
| ComplexType | Le nœud, les sous-nœuds et tous les attributs |
| Élément Association | Le nœud, les sous-nœuds et tous les attributs |
| Propriété Extended | Le nœud, les sous-nœuds et tous les attributs |
| EntityContainer | Seuls les attributs suivants sont ignorés : *extends* et *AssociationSet* |
| Schéma | Seuls les attributs suivants sont ignorés : *Namespace* |
| FunctionImport | Seuls les attributs suivants sont ignorés : *Mode* (la valeur par défaut de In est supposée) |
| EntityType | Seuls les sous-nœuds suivants sont ignorés : *Key* et *PropertyRef* |

La section suivante décrit en détail les modifications (éléments ajoutés et ignorés) aux différents nœuds CSDL XML.

## Nœud FunctionImport
Un nœud FunctionImport représente une URL (point d’entrée) qui expose un service à l’utilisateur final. Le nœud autorise la description de la manière dont l’URL est résolue, les paramètres disponibles pour l’utilisateur final et la manière dont ces paramètres sont fournis.

De plus amples informations sur ce nœud sont disponibles à l’adresse [http://msdn.microsoft.com/library/cc716710(v=vs.100).aspx][MSDNFunctionImportLink].

[MSDNFunctionImportLink]: 'http://msdn.microsoft.com/library/cc716710(v=vs.100).aspx'

Voici les attributs supplémentaires (ou ajouts aux attributs) qui sont exposés par le nœud FunctionImport.

**d:BaseUri** : le modèle d’URI de la ressource REST exposée sur Marketplace. Marketplace utilise le modèle pour construire des requêtes sur le service web REST. Le modèle d’URI contient des espaces réservés pour les paramètres sous la forme {parameterName}, où parameterName est le nom du paramètre. P. ex., apiVersion={apiVersion}. Les paramètres sont autorisés à apparaître en tant que paramètres d’URI ou en tant que partie du chemin d’accès de l’URI. Dans le cas de l’apparence dans le chemin d’accès, ils sont toujours obligatoires (ils ne peuvent être marqués comme nullable). *Exemple :* `d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Name** : nom de la fonction importée. Il ne peut être identique à d’autres noms définis dans le langage CSDL. P. ex., Name="GetModelUsageFile"

**EntitySet** *(facultatif)* : si la fonction renvoie une collection de types d’entité, la valeur d’**EntitySet** doit être le jeu d’entités auquel la collection appartient. Dans le cas contraire, l’attribut **EntitySet** ne doit pas être utilisé. *Exemple :* `EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(facultatif)* : spécifie le type des éléments renvoyés par l’URI. N’utilisez pas cet attribut si la fonction ne renvoie pas de valeur. Les types suivants sont pris en charge :

 - **Collection (<Entity type name>)** : spécifie une collection de types d’entités définis. Le nom est présent dans l’attribut Name du nœud EntityType. Par exemple, Collection(WXC.HourlyResult).
 - **Raw (<mime type>)** : spécifie un document/blob brut qui est renvoyé à l’utilisateur. Par exemple, Raw(image/jpeg) ; autres exemples :

  - ReturnType="Raw(text/plain)"
  - ReturnType="Collection(sage.DeleteAllUsageFilesEntity)"*

**d:Paging** : spécifie la manière dont la pagination est gérée par la ressource REST. Les valeurs de paramètre sont utilisées dans des accolades, p. ex., page={$page}&itemsperpage={$size} ; les options disponibles sont :

- **None :** aucune pagination n’est disponible
- **Skip :** la pagination est exprimée à l’aide d’une logique « skip » et « take » (supérieure). « skip » passe les éléments M, puis « take » renvoie les éléments N suivants. Valeur du paramètre : $skip
- **Take :** « take » renvoie les éléments N suivants. Valeur du paramètre : $take
- **PageSize :** la pagination est exprimée via une page logique et via la taille (éléments par page). La page représente la page actuelle qui est renvoyée. Valeur du paramètre : $page
- **Size :** la taille représente le nombre d’éléments renvoyés pour chaque page. Valeur du paramètre : $size

**d:AllowedHttpMethods** *(facultatif)* : spécifie le verbe géré par la ressource REST. Restreint également le verbe accepté à la valeur spécifiée. Valeur par défaut = POST. *Exemple :* `d:AllowedHttpMethods="GET"` les options disponibles sont :

- **GET :** généralement utilisé pour renvoyer des données
- **POST :** généralement utilisé pour insérer de nouvelles données
- **PUT :** généralement utilisé pour mettre à jour des données
- **DELETE:** utilisé pour supprimer des données

Les nœuds enfants supplémentaires (non couverts par la documentation du langage CSDL) dans le nœud FunctionImport sont :

**d:RequestBody** *(facultatif)* : le corps de la demande est utilisé pour indiquer que la demande attend un corps à envoyer. Les paramètres peuvent être donnés dans le corps de la demande. Ils sont exprimés dans des accolades, p. ex., {parameterName}. Ces paramètres sont mappés à partir de l’entrée utilisateur dans le corps transféré vers le service du fournisseur de contenu. L’élément requestBody possède un attribut nommé httpMethod. L’attribut autorise deux valeurs :

- **POST :** utilisé si la demande est de type HTTP POST
- **GET:** utilisé si la demande est de type HTTP GET

	Exemple :

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** et **d:Namespace** : ce nœud décrit les espaces de noms qui sont définis dans le code XML renvoyé par l’importation de fonction (point de terminaison de l’URI). Le code XML qui est renvoyé par le service principal peut contenir n’importe quel nombre d’espaces de noms pour différencier le contenu qui est renvoyé. **Tous ces espaces de noms, s’ils sont utilisés dans des requêtes d:Map ou d:Match XPath doivent être répertoriés.** Le nœud d:Namespaces contient un jeu/une liste de nœuds d:Namespace. Chacun d’eux répertorie un espace de noms utilisé dans la réponse du service du serveur principal. Les éléments suivants constituent l’attribut du nœud d:Namespace :

-	**d:Prefix :** le préfixe de l’espace de noms, comme vu dans les résultats XML renvoyés par le service, p. ex., f:FirstName, f:LastName, où f est le préfixe.
- **d:Uri :** l’URI complet de l’espace de noms utilisé dans le document de résultats. Il représente la valeur sur laquelle le préfixe est résolu lors de l’exécution.

**d:ErrorHandling** *(facultatif)* : ce nœud contient des conditions pour la gestion des erreurs. Chacune des conditions est validée par rapport au résultat renvoyé par le service du fournisseur de contenu. Si une condition correspond au code d’erreur HTTP proposé, un message d’erreur est renvoyé à l’utilisateur final.

**d:ErrorHandling** *(facultatif)* et **d:Condition** *(facultatif)* : un nœud de condition contient une condition qui est vérifiée dans le résultat renvoyé par le service du fournisseur de contenu. Les éléments suivants sont les attributs **obligatoires** :

- **d:Match :** une expression XPath qui vérifie si un nœud ou une valeur donnés sont présents dans le code XML de sortie du fournisseur de contenu. L’expression XPath est exécutée sur la sortie et doit renvoyer la valeur true si la condition est une correspondance, et la valeur false dans le cas contraire.
- **d:HttpStatusCode :** le code d’état HTTP qui doit être renvoyé par Marketplace au cas où la condition correspondrait. Marketplace signale les erreurs à l’utilisateur au moyen de codes d’état HTTP. Une liste des codes d’état HTTP est disponible à l’adresse http://en.wikipedia.org/wiki/HTTP_status_code.
- **d:ErrorMessage :** le message d’erreur renvoyé (avec le code d’état HTTP) à l’utilisateur final. Il s’agit d’un message d’erreur amical qui ne contient aucun secret.

**d:Title** *(facultatif)* : décrit le titre de la fonction. Provenance de la valeur pour le titre :

- L’attribut de mappage facultatif (xpath) qui indique où trouver le titre dans la réponse renvoyée par la demande de service.
- - ou - Le titre spécifié en tant que valeur du nœud.

**d:Rights** *(facultatif)* : les droits (p. ex., copyright) associés avec la fonction. Provenance de la valeur pour les droits :

- L’attribut de mappage facultatif (xpath) qui indique où trouver les droits dans la réponse renvoyée par la demande de service.
-	- Ou - Les droits spécifiés en tant que valeur du nœud.

**d:Description** *(facultatif)* : une brève description de la fonction. Provenance de la valeur pour la description :

- L’attribut de mappage facultatif (xpath) qui indique où trouver la description dans la réponse renvoyée par la demande de service.
- - Ou - La description spécifiée en tant que valeur du nœud.

**d:EmitSelfLink** : *consultez l’exemple ci-dessus « FunctionImport pour la pagination » via les données renvoyées*

**d:EncodeParameterValue** : extension facultative à OData

**d:QueryResourceCost** : extension facultative à OData

**d:Map** : extension facultative à OData

**d:Headers** : extension facultative à OData

**d:Headers** : extension facultative à OData

**d:Value** : extension facultative à OData

**d:HttpStatusCode** : extension facultative à OData

**d:ErrorMessage** : extension facultative à OData

## Nœud du paramètre

Ce nœud représente un paramètre qui est exposé en tant que partie du modèle d’URI / du corps de la demande qui a été spécifié dans le nœud FunctionImport.

Une page de documentation détaillée très utile sur le nœud « Élément Parameter » est disponible à l’adresse [http://msdn.microsoft.com/library/ee473431.aspx](http://msdn.microsoft.com/library/ee473431.aspx) (utilisez la liste déroulante **Autre version** pour sélectionner une version différente pour afficher la documentation si nécessaire). *Exemple :* `<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Attribut de paramètre | Est obligatoire | Valeur |
|----|----|----|
| Nom | Oui | Le nom du paramètre. Respecte la casse. Respectez la casse de l’URI de base. **Exemple :** `<Property Name="IsDormant" Type="Byte" />` |
| Type | Oui | Le type du paramètre. La valeur doit être de type **EDMSimpleType** ou de type complexe, dans la portée du modèle. Pour plus d’informations, consultez « 6. Types de paramètres/propriétés pris en charge ». (Respecte la casse. Le premier caractère est en majuscule, les autres sont en minuscules). Consultez également la page [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][MSDNParameterLink]. **Exemple :** `<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mode | Non | **In**, Out ou InOut selon que le paramètre est un paramètre d’entrée, de sortie ou d’entrée/sortie. (Seule la valeur « IN » est disponible dans Azure Marketplace.) **Exemple :** `<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | Non | La longueur maximale autorisée du paramètre. **Exemple :** `<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precision | Non | La précision du paramètre. **Exemple :** `<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Scale | Non | L’échelle du paramètre. **Exemple :** `<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]: 'http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx'

Les éléments suivants sont des attributs qui ont été ajoutés à la spécification du langage CSDL :

| Attribut de paramètre | Description |
|----|----|
| **d:Regex** *(facultatif)* | Une instruction d’expression régulière utilisée pour valider la valeur d’entrée pour le paramètre. Si la valeur d’entrée ne correspond pas à l’instruction, la valeur est rejetée. Cela permet également de spécifier un ensemble de valeurs possibles, p. ex., ^ [0-9] +? $ pour autoriser uniquement les chiffres. **Exemple :** `<Parameter Name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters" d:SampleValues="George|John|Thomas|James"/>` |
| **d:Enum** *(facultatif)* | Une liste de valeurs séparées par des barres verticales, valide pour le paramètre. Le type des valeurs doit correspondre au type défini du paramètre. Exemple : `english|metric|raw`. Enum s’affiche sous forme d’une liste déroulante sélectionnable de paramètres dans l’interface utilisateur (explorateur de service). **Exemple :** `<Parameter Name="Duration" Type="String" Mode="In" Nullable="true" d:Enum="1year|5years|10years"/>` |
| **d:Nullable** *(facultatif)* | Permet de définir si un paramètre peut être null. La valeur par défaut est true. Toutefois, les paramètres qui sont exposés en tant que partie du chemin d’accès dans le modèle d’URI ne peuvent pas être null. Lorsque l’attribut est défini sur false pour ces paramètres, l’entrée utilisateur est remplacée. **Exemple :** `<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(facultatif)* | Un exemple de valeur à afficher en tant que note au client dans l’interface utilisateur. Il est possible d’ajouter plusieurs valeurs à l’aide d’une liste séparée par des barres verticales, c’est-à-dire `a|b|c` **Exemple :** `<Parameter Name="BikeOwner" Type="String" Mode="In" d:SampleValues="George|John|Thomas|James"/>` |

## Nœud EntityType

Ce nœud représente l’un des types renvoyés à partir de Marketplace à l’utilisateur final. Il contient également le mappage de la sortie qui est renvoyée par le service du fournisseur de contenu aux valeurs renvoyées à l’utilisateur final.

Des informations sur ce nœud sont disponibles à l’adresse [http://msdn.microsoft.com/library/bb399206.aspx](http://msdn.microsoft.com/library/bb399206.aspx) (utilisez la liste déroulante **Autre version** pour sélectionner une version différente pour afficher la documentation si nécessaire).

| Nom de l'attribut | Est obligatoire | Valeur |
|----|----|----|
| Nom | Oui | Le nom du type d’entité. **Exemple :** `<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | Non | Le nom d’un autre type d’entité qui est le type de base du type d’entité défini. **Exemple :** `<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Les éléments suivants sont des attributs qui ont été ajoutés à la spécification du langage CSDL :

**d:Map** : expression XPath exécutée sur la sortie du service. Ici, l’hypothèse est que la sortie du service contient un ensemble d’éléments qui se répètent, comme un flux ATOM où il existe un ensemble de nœuds d’entrée qui se répètent. Chacun de ces nœuds qui se répètent contient un enregistrement. L’expression XPath est ensuite spécifiée pour pointer vers le nœud individuel qui se répète dans le résultat du service du fournisseur de contenu qui contient les valeurs pour un enregistrement individuel. Exemple de sortie du service :

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

L’expression XPath serait /foo/bar car chaque nœud de barre est le nœud répété dans la sortie, et contient le contenu réel qui est renvoyé à l’utilisateur final.

**Key** : cet attribut est ignoré par Marketplace. En général, les services web basés sur REST n’exposent pas de clé primaire.


## Nœud de propriété

Ce nœud contient une propriété de l’enregistrement.

Des informations sur ce nœud sont disponibles à l’adresse [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (utilisez la liste déroulante **Autre version** pour sélectionner une version différente pour afficher la documentation si nécessaire). *Exemple :* `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name" 	Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
		...
        </EntityType>`

| Nom de l’attribut | Obligatoire | Valeur |
|----|----|----|
| Nom | Oui | Le nom de la propriété. |
| Type | Oui | Le type de la valeur de propriété. Le type de la valeur de propriété doit être un type **EDMSimpleType** ou un type complexe (indiqué par un nom qualifié complet) qui se trouve dans la portée du modèle. Pour plus d’informations, consultez Types de modèle conceptuel (CSDL). |
| Nullable | Non | **True** (valeur par défaut) ou **False** selon que la propriété peut avoir ou non une valeur null. Remarque : dans la version du langage CSDL indiqué par l’espace de noms [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm), une propriété de type complexe doit avoir Nullable="False". |
| DefaultValue | Non | Valeur par défaut de la propriété. |
|MaxLength | Non | Longueur maximale de la valeur de propriété. |
| FixedLength | Non | **True** ou **False** selon que la valeur de propriété sera stockée ou non comme une chaîne de longueur fixe. |
| Precision | Non | Se rapporte au nombre maximal de chiffres à conserver dans la valeur numérique. |
| Scale | Non | Nombre maximal de décimales à conserver dans la valeur numérique. |
| Unicode | Non | **True** ou **False** selon que la valeur de propriété sera stockée ou non comme une chaîne Unicode. |
| Collation | Non | Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données. |
| ConcurrencyMode | Non | **None** (valeur par défaut) ou **Fixed**. Si la valeur est définie sur **Fixed**, la valeur de propriété sera utilisée dans les contrôles d’accès concurrentiel optimiste. |

Les éléments suivants sont des attributs supplémentaires qui ont été ajoutés à la spécification du langage CSDL.

**d:Map** : expression XPath exécutée sur la sortie de service et qui extrait une propriété de la sortie. L’expression XPath spécifiée est relative au nœud répété sélectionné dans l’expression XPath du nœud EntityType. Il est également possible de spécifier une expression XPath absolue pour autoriser l’intégration d’une ressource statique dans chacun des nœuds de sortie, par exemple une déclaration de copyright qui se trouve uniquement une fois dans la sortie du service d’origine, mais doit être présente dans chaque ligne de la sortie OData. Exemple du service :

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

Ici, l’expression XPath serait ./bar/baz0 pour obtenir le nœud baz0 du service du fournisseur de contenu.

**d:CharMaxLength** : pour le type de chaîne, vous pouvez spécifier la longueur maximale. Consultez l’exemple de service de données CSDL.

**d:IsPrimaryKey** : indique si la colonne est la clé Primaire dans la table/vue. Consultez l’exemple de service de données CSDL.

**d:isExposed** : détermine si le schéma de la table est exposé (généralement, valeur true). Consultez l’exemple de service de données CSDL.

**d:IsView** *(facultatif)* : valeur true si cela est basé sur une vue plutôt que sur une table. Consultez l’exemple de service de données CSDL.

**d:Tableschema** : consultez l’exemple de service de données CSDL.

**d:ColumnName** : nom de la colonne dans la table/vue. Consultez l’exemple de service de données CSDL.

**d:IsReturned** : valeur booléenne qui détermine si le service expose cette valeur au client. Consultez l’exemple de service de données CSDL.

**d:IsQueryable** : valeur booléenne qui détermine si la colonne peut être utilisée dans une requête de base de données. Consultez l’exemple de service de données CSDL.

**d:OrdinalPosition** : position numérique d’apparence de la colonne, x, dans la table ou la vue, où x va de 1 au nombre de colonnes dans la table. Consultez l’exemple de service de données CSDL.

**d:DatabaseDataType** : type de données de la colonne dans la base de données, c’est-à-dire, type de données SQL. Consultez l’exemple de service de données CSDL.

## Types de paramètres/propriétés pris en charge
Les éléments suivants sont les types de paramètres et de propriétés pris en charge. (Respectent la casse)

| Types primitifs | Description |
|----|----|
| Null | Représente l’absence d’une valeur |
| Boolean | Représente le concept mathématique de logique binaire|
| Byte | Valeur entière 8 bits non signée|
|DateTime| Représente la date et l’heure avec des valeurs allant de 12:00:00 (minuit), le 1er janvier 1753 (après Jésus-Christ) à 11:59:59 (soir), décembre 9999 (après Jésus-Christ)|
|Decimal | Représente des valeurs numériques avec précision et échelle fixes. Ce type peut décrire une valeur numérique allant de -10 ^ 255 + 1 à +10 ^ 255 -1|
| Double | Représente un nombre à virgule flottante avec une précision de 15 chiffres pouvant représenter des valeurs avec une plage approximative de ± 2,23e -308 à ± 1,79e +308. **Utilisation de décimales en raison d’un problème d’exportation Excel**|
| Single | Représente un nombre à virgule flottante avec une précision de 7 chiffres pouvant représenter des valeurs avec une plage approximative de ± 1,18e -38 à ± 3,40e +38|
|Guid |Représente une valeur d’identificateur unique de 16 octets (128 bits) |
|Int16|Représente une valeur entière de 16 bits signée |
|Int32|Représente une valeur entière de 32 bits signée |
|Int64|Représente une valeur entière de 64 bits signée |
|String | Représente des données de type caractère à longueur fixe ou variable |


## Voir aussi
- Si vous souhaitez comprendre le processus de mappage OData global et son rôle, lisez l’article [Mappage du service de données OData](marketplace-publishing-data-service-creation-odata-mapping.md) pour passer en revue des définitions, des structures et des instructions.
- Si vous souhaitez passer en revue des exemples, lisez l’article [Exemples de mappage du service de données OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) pour consulter des exemples de code et pour comprendre la syntaxe et le contexte du code.
- Pour retourner au chemin indiqué pour la publication d’un service de données sur Azure Marketplace, lisez l’article [Guide de publication de services de données](marketplace-publishing-data-service-creation.md).

<!---HONumber=AcomDC_0114_2016-->