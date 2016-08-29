<properties
pageTitle="Indexation d’Azure Blob Storage avec Azure Search"
description="Découvrez comment indexer Azure Blob Storage et extraire le texte de documents avec Azure Search"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="08/16/2016"
ms.author="eugenesh" />

# Indexation de documents dans Azure Blob Storage avec Azure Search

Cet article explique comment utiliser Azure Search pour indexer des documents (tels que des fichiers PDF, des documents Microsoft Office et plusieurs autres formats courants) stockés dans le stockage d’objets blob Azure. Le nouvel indexeur d’objets blob Azure Search rend ce processus rapide et transparent.

> [AZURE.IMPORTANT] Pour l’instant, cette fonctionnalité n’existe qu’en version préliminaire. Elle est uniquement disponible dans l’API REST utilisant la version **2015-02-28-Preview**. N’oubliez pas que les API d’évaluation sont destinées à être utilisées à des fins de test et d’évaluation, et non dans les environnements de production.

## Formats de document pris en charge

L’indexeur d’objets blob peut extraire du texte à partir des formats de document suivants :

- PDF
- Formats Microsoft Office : DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG (e-mails Outlook)
- HTML
- XML
- ZIP
- EML
- Fichiers de texte brut
- JSON (consultez [Indexation d’objets blob JSON](search-howto-index-json-blobs.md) pour plus d’informations)
- CSV (consultez [Indexation d’objets blob CSV](search-howto-index-csv-blobs.md) pour plus d’informations)

## Configuration de l’indexation d’objets blob

Pour installer et configurer un indexeur Azure Blob Storage, vous pouvez appeler l’API REST Azure Search afin de créer et de gérer des **indexeurs** et des **sources de données** en suivant les procédures décrites dans [cet article](https://msdn.microsoft.com/library/azure/dn946891.aspx). Vous pouvez également utiliser la [version 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) du SDK .NET. À l’avenir, la prise en charge de l’indexation d’objets blob sera ajoutée au Portail Azure.

Pour configurer un indexeur, effectuez les opérations suivantes : créez une source de données, créez un index, configurez l’indexeur.

### Étape 1 : Création d’une source de données

Une source de données spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données et les stratégies qui permettent à Azure Search d’identifier efficacement les changements dans les données (tels que des lignes ajoutées, modifiées ou supprimées). Une source de données peut être utilisée par plusieurs indexeurs dans le même abonnement.

Pour l’indexation des objets blob, la source de données doit avoir les propriétés requises suivantes :

- **name** est le nom unique de la source de données au sein de votre service de recherche.

- **type** doit être `azureblob`.

- **credentials** fournit la chaîne de connexion du compte de stockage en tant que paramètre `credentials.connectionString`. Vous pouvez obtenir la chaîne de connexion à partir du portail Azure en accédant au panneau du compte de stockage souhaité > **Paramètres** > **Clés** et utiliser la valeur « Chaîne de connexion principale » ou « Chaîne de connexion secondaire ». Étant donné que la chaîne de connexion est liée à un compte de stockage, le fait de spécifier la chaîne de connexion identifie implicitement le compte de stockage qui fournit les données.

- **container** spécifie un conteneur dans votre compte de stockage. Par défaut, tous les objets blob du conteneur sont récupérables. Si vous souhaitez indexer uniquement les objets blob dans un répertoire virtuel particulier, vous pouvez spécifier ce répertoire à l’aide du paramètre facultatif **query**.

L’exemple suivant illustre une définition de source de données :

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
	}   

Pour plus d’informations sur l’API Créer une source de données, consultez [Créer une source de données](search-api-indexers-2015-02-28-preview.md#create-data-source).

### Étape 2 : Création d’un index 

L’index spécifie les champs d’un document, les attributs et d’autres constructions qui façonnent l’expérience de recherche.

Pour l’indexation des objets blob, assurez-vous que l’index contient un champ `content` cherchable pour stocker l’objet blob.

	POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/json
	api-key: [admin key]

	{
  		"name" : "my-target-index",
  		"fields": [
    		{ "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    		{ "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
  		]
	}

Pour plus d’informations sur l’API Créer un index, consultez [Créer un index](https://msdn.microsoft.com/library/dn798941.aspx)

### Étape 3 : Création d’un indexeur 

Un indexeur connecte des sources de données à des index de recherche cibles et fournit des informations de planification pour vous permettre d’automatiser l’actualisation des données. Une fois la source de données et l’index créés, il est relativement simple de créer un indexeur qui référence la source de données et un index cible. Par exemple :

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "blob-indexer",
	  "dataSourceName" : "blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}

Cet indexeur s’exécutera toutes les deux heures (intervalle de planification défini sur « PT2H »). Pour exécuter un indexeur toutes les 30 minutes, définissez l’intervalle sur « PT30M ». Le plus court intervalle pris en charge est de 5 minutes. La planification est facultative : en cas d’omission, un indexeur ne s’exécute qu’une seule fois lorsqu’il est créé. Toutefois, vous pouvez à tout moment exécuter un indexeur à la demande.

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](search-api-indexers-2015-02-28-preview.md#create-indexer).


## Processus d’extraction de document

Azure Search indexe chaque document (objet blob) comme suit :

- L’ensemble du contenu de texte du document est extrait dans un champ de chaîne nommé `content`. Notez que pour l’instant, nous ne prenons pas en charge l’extraction de plusieurs documents à partir d’un seul et même objet blob :
	- Par exemple, un fichier CSV est indexé en tant que document unique. Si vous avez besoin de traiter chaque ligne d’un fichier CSV comme un document séparé, veuillez voter pour [cette suggestion UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/13865325-please-treat-each-line-in-a-csv-file-as-a-separate).
	- Un document composé ou incorporé (tel qu’une archive ZIP ou un document Word avec e-mail Outlook incorporé intégrant une pièce jointe au format PDF) est également indexé en tant que document unique.

- Les propriétés de métadonnées spécifiées par l’utilisateur qui sont éventuellement présentes dans l’objet blob sont extraites textuellement. Il est également possible d’utiliser les propriétés de métadonnées pour contrôler certains aspects du processus d’extraction de document. Pour plus d’informations, voir la section [Utilisation de métadonnées personnalisées pour contrôler l’extraction de document](#CustomMetadataControl).

- Les propriétés de métadonnées d’objet blob standard sont extraites dans les champs suivants :

	- **metadata\_storage\_name** (Edm.String) : nom de fichier de l’objet blob. Par exemple, si vous disposez de l’objet blob /my-container/my-folder/subfolder/resume.pdf, ce champ présente la valeur `resume.pdf`.

	- **metadata\_storage\_path** (Edm.String) : URI complet de l’objet blob, incluant le compte de stockage. Par exemple, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

	- **metadata\_storage\_content\_type** (Edm.String) : type de contenu tel que spécifié par le code que vous avez utilisé pour charger l’objet blob. Par exemple : `application/octet-stream`.

	- **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) : horodateur de la dernière modification de l’objet blob. Azure Search utilise cet horodateur pour identifier les objets blob modifiés afin d’éviter une réindexation complète après l’indexation initiale.

	- **metadata\_storage\_size** (Edm.Int64) : taille de l’objet blob en octets.

	- **metadata\_storage\_content\_md5** (Edm.String) : code de hachage MD5 du contenu de l’objet blob s’il est disponible.

- Les propriétés de métadonnées propres à chaque format de document sont extraites dans les champs répertoriés [ici](#ContentSpecificMetadata).

Vous n’avez pas besoin de définir les champs relatifs à chacune des propriétés ci-dessus dans votre index de recherche. Il vous suffit de capturer les propriétés dont vous devez disposer pour votre application.

> [AZURE.NOTE] Les noms de champ figurant dans votre index existant diffèrent généralement des noms de champ générés lors de l’extraction de document. Dans ce cas, vous pouvez utiliser les **mappages de champs** pour mapper les noms de propriété fournis par Azure Search sur les noms de champ de votre index de recherche. Découvrez ci-dessous une exemple d’utilisation de mappage de champ.

## Sélection du champ de clé de document et gestion des différences de nom de champ

Dans Azure Search, la clé de document identifie un document de manière unique. Chaque index de recherche doit comporter exactement un champ de clé de type Edm.String. Ce champ de clé est nécessaire pour chaque document ajouté à l’index (il constitue en fait le seul champ obligatoire).
   
Vous devez déterminer avec soin le champ extrait que vous souhaitez mapper sur le champ de clé de votre index. Les candidats sont les suivants :

- **metadata\_storage\_name** : ce champ pourrait se révéler un choix commode, mais notez que (1) les noms ne sont pas forcément uniques, car vous pouvez disposer d’objets blob portant le même nom dans différents dossiers, et (2) le nom peut contenir des caractères qui ne sont pas valides dans les clés de document, comme des tirets. Vous pouvez gérer les caractères non valides en activant l’option `base64EncodeKeys` dans les propriétés de l’indexeur. Dans ce cas, pensez à encoder les clés de document lorsque vous les transmettez dans des appels d’API, comme l’API Lookup. (Par exemple, dans .NET, vous pouvez utiliser la [méthode UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) à cet effet).

- **metadata\_storage\_path** : l’utilisation du chemin d’accès complet garantit l’unicité, mais le chemin d’accès contient invariablement des caractères `/` qui ne sont [pas valides dans une clé de document](https://msdn.microsoft.com/library/azure/dn857353.aspx). Comme ci-dessus, vous avez la possibilité d’encoder les clés à l’aide de l’option `base64EncodeKeys`.

- Si aucune des solutions ci-dessus n’est adaptée à votre cas, vous pouvez en dernier recours ajouter une propriété de métadonnées personnalisée aux objets blob. Toutefois, cette approche contraint votre processus de chargement d’objets blob à ajouter cette propriété de métadonnées à tous les objets blob. Étant donné que la clé est une propriété obligatoire, tous les objets blob dépourvus de cette propriété ne seront pas indexés.

> [AZURE.IMPORTANT] En l’absence de mappage explicite pour le champ de clé dans l’index, Azure Search utilisera automatiquement `metadata_storage_path` (la deuxième option ci-dessus) en guise de clé et activera le codage base 64 des clés.

Pour cet exemple, sélectionnons le champ `metadata_storage_name` en tant que clé de document. Supposons également que votre index comporte un champ de clé nommé `key` et un champ `fileSize` pour le stockage de la taille du document. Pour obtenir le résultat souhaité, spécifiez les mappages de champs ci-après lors de la création ou de la mise à jour de votre indexeur :

	"fieldMappings" : [
	  { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	  { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	]

Pour regrouper tous ces éléments, utilisez le code ci-après pour ajouter des mappages de champs et activer le codage base 64 des clés pour un indexeur existant :

	PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "dataSourceName" : " blob-datasource ",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
	  "fieldMappings" : [
	    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	  ],
	  "parameters" : { "base64EncodeKeys": true }
	}

> [AZURE.NOTE] Pour en savoir plus sur les mappages de champs, consultez [cet article](search-indexer-field-mappings.md).

## Indexation incrémentielle et détection des suppressions

Lorsque vous configurez un indexeur d’objets blob à exécuter selon une planification, ce dernier réindexe uniquement les objets blob modifiés, comme déterminé par l’horodateur `LastModified` des objets blob.

> [AZURE.NOTE] Vous n’êtes pas contraint de spécifier une stratégie de détection des modifications ; l’indexation incrémentielle est activée automatiquement à votre intention.

Pour indiquer que certains documents doivent être supprimés de l’index, vous devez utiliser une stratégie de suppression réversible : plutôt que de supprimer les objets blob correspondants, ajoutez une propriété de métadonnées personnalisée pour signaler leur suppression, puis configurez une stratégie de détection des suppressions réversibles sur la source de données.

> [AZURE.WARNING] Si vous supprimez simplement les objets blob au lieu d’utiliser une stratégie de détection des suppressions, les documents correspondants ne seront pas supprimés de l’index de recherche.

Par exemple, la stratégie ci-après considère qu’un objet blob est supprimé s’il présente une propriété de métadonnées `IsDeleted` avec la valeur `true` :

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
		"container" : { "name" : "my-container", "query" : "my-folder" },
		"dataDeletionDetectionPolicy" : {
			"@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", 	
			"softDeleteColumnName" : "IsDeleted",
			"softDeleteMarkerValue" : "true"
		}
	}   

<a name="ContentSpecificMetadata"></a>
## Propriétés de métadonnées propres au type de contenu

Le tableau ci-après récapitule le traitement appliqué pour chaque format de document et décrit les propriétés de métadonnées extraites par Azure Search.

Format de document/type de contenu | Propriétés de métadonnées propres au type de contenu | Détails du traitement
-------------------------------|-------------------------------------------|-------------------
HTML (`text/html`) | `metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` | Suppression du balisage HTML et extraction du texte
PDF (`application/pdf`) | `metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`| Extraction du texte, y compris les documents incorporés (à l’exclusion des images)
DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Extraction du texte, y compris les documents incorporés
DOC (application/msword) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Extraction du texte, y compris les documents incorporés
XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Extraction du texte, y compris les documents incorporés
XLS (application/vnd.ms-excel) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Extraction du texte, y compris les documents incorporés
PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Extraction du texte, y compris les documents incorporés
PPT (application/vnd.ms-powerpoint) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Extraction du texte, y compris les documents incorporés
MSG (application/vnd.ms-outlook) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` | Extraction du texte, y compris les pièces jointes
ZIP (application/zip) | `metadata_content_type` | Extraction du texte de tous les documents figurant dans l’archive
XML (application/xml) | `metadata_content_type`</br>`metadata_content_encoding`</br> | Suppression du balisage XML et extraction du texte
JSON (application/json) | `metadata_content_type`</br>`metadata_content_encoding` | Extraction du texte<br/>REMARQUE : si vous devez extraire plusieurs champs de document à partir d’un objet blob JSON, consultez [Indexation d’objets blob JSON](search-howto-index-json-blobs.md) pour plus de détails
EML (message/rfc822) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` | Extraction du texte, y compris les pièces jointes
Texte brut (text/plain) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 

<a name="CustomMetadataControl"></a>
## Utilisation de métadonnées personnalisées pour contrôler l’extraction de document

Vous pouvez ajouter des propriétés de métadonnées à un objet blob pour contrôler certains aspects du processus d’indexation d’objets blob et d’extraction de document. Les propriétés actuellement prises en charge sont les suivantes :

Nom de la propriété | Valeur de la propriété | Explication
--------------|----------------|------------
AzureSearch\_Skip | "true" | Demande à l’indexeur d’objets blob d’ignorer complètement l’objet blob ; aucune extraction de métadonnées ou de contenu ne sera tentée. Cela se révèle utile lorsque vous souhaitez ignorer certains types de contenu, ou quand un objet blob spécifique échoue à plusieurs reprises et interrompt le processus d’indexation.
AzureSearch\_SkipContent | "true" | Indique à l’indexeur d’objets blob de seulement indexer les métadonnées et d’ignorer l’extraction du contenu de l’objet blob. Cela est utile si le contenu de l’objet blob n’est pas intéressant, mais que vous souhaitez néanmoins indexer les métadonnées attachées à cet objet blob.

<a name="IndexerParametersConfigurationControl"></a>
## Utilisation de paramètres d’indexeur pour contrôler l’extraction des documents

Plusieurs paramètres de configuration de l’indexeur sont disponibles pour contrôler les objets blob et les parties du contenu d'un objet blob et des métadonnées qui seront indexés.

### Indexer uniquement les objets blob avec des extensions de fichier spécifiques

Vous pouvez indexer uniquement les objets blob avec des extensions de nom de fichier que vous spécifiez à l’aide du paramètre de configuration d’indexeur `indexedFileNameExtensions`. La valeur est une chaîne contenant une liste d'extensions de fichier séparées par des virgules (précédées d'un point). Par exemple, pour indexer uniquement les objets blob .PDF et .DOCX, procédez comme suit :

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
	}

### Exclure de l’indexation les objets blob avec des extensions de fichier spécifiques

Vous pouvez exclure de l’indexation des objets blob avec des extensions de nom de fichier spécifiques à l’aide du paramètre de configuration `excludedFileNameExtensions`. La valeur est une chaîne contenant une liste d'extensions de fichier séparées par des virgules (précédées d'un point). Par exemple, pour indexer tous les objets blob, sauf ceux qui ont les extensions .PNG et .JPEG, procédez comme suit :

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
	}

Si les paramètres `indexedFileNameExtensions` et `excludedFileNameExtensions` sont tous deux présents, Azure Search regarde d’abord `indexedFileNameExtensions`, puis `excludedFileNameExtensions`. Cela signifie que, si la même extension de fichier est présente dans les deux listes, elle sera exclue de l'indexation.

### Indexer uniquement les métadonnées de stockage

Vous pouvez indexer uniquement les métadonnées de stockage et ignorer complètement le processus d’extraction de documents à l’aide de la propriété de configuration `indexStorageMetadataOnly`. Cela est utile lorsque vous n'avez pas besoin du contenu du document, ou des propriétés des métadonnées propres au type de contenu. Pour ce faire, définissez la propriété `indexStorageMetadataOnly` sur `true` :

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "indexStorageMetadataOnly" : true } }
	}

### Indexer à la fois les métadonnées de type de contenu et de stockage, mais ignorer l’extraction du contenu

Si vous devez extraire toutes les métadonnées, mais ignorer l’extraction de contenu pour tous les objets blob, vous pouvez demander ce comportement à l’aide de la configuration d’indexeur, au lieu de devoir ajouter individuellement des métadonnées `AzureSearch_SkipContent` à chaque objet blob. Pour ce faire, définissez la propriété de configuration de l’indexeur `skipContent` sur `true` :

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "skipContent" : true } }
	}

## Aidez-nous à améliorer Azure Search

Si vous souhaitez nous soumettre des demandes d’ajout de fonctionnalités ou des idées d’amélioration, n’hésitez pas à nous contacter sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0817_2016-->