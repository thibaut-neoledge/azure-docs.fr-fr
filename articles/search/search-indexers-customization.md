<properties 
	pageTitle="Personnalisation des indexeurs Azure Search | Microsoft Azure | Service de recherche cloud hébergé" 
	description="Apprenez à personnaliser les paramètres et les stratégies des indexeurs dans Azure Search, un service de recherche cloud hébergé sur Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="11/04/2015" 
	ms.author="eugenesh"/>

#Personnalisation des indexeurs Azure Search

La configuration d'un indexeur dans Azure Search vous permet de renommer des champs entre une source de données et un index cible, transformer des chaînes à partir d'une table de base de données en collections de chaînes, basculer la stratégie de détection des modifications sur une source de données, encoder par URL les clés de document qui contiennent des caractères non sécurisés pour les URL, et tolérer des échecs d'indexation de certains documents.

Si vous n'êtes pas familiarisé avec les indexeurs Azure Search, vous pouvez au préalable consulter les articles suivants :

- [Connexion d'Azure SQL Database à Azure Search à l'aide d'indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Connexion de DocumentDB à Azure Search à l'aide d'indexeurs](../documentdb/documentdb-search-indexer.md)
- [Kit de développement logiciel (SDK) .NET avec prise en charge des indexeurs](https://msdn.microsoft.com/library/dn951165.aspx) ou 
- [Informations de référence sur l'API REST des indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx)

##Renommer des champs entre une source de données et un index cible##

Les **mappages de champs** sont des propriétés qui réconcilient les différences entre des définitions de champ. Les exemples les plus courants figurent dans les noms de champs qui violent les règles d'affectation de noms Azure Search. Considérez une table source où un ou plusieurs noms de champ commencent par un trait de soulignement (tel que `_id`). Comme Azure Search n'autorise pas de noms de champ commençant par un trait de soulignement, le champ doit être renommé.

L'exemple suivant illustre la mise à jour d'un indexeur pour qu'il inclue un mappage de champ qui « renomme » le champ `_id` de la source de données en champ `id` dans l'index cible :

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
    } 

**REMARQUE :** vous devez utiliser une API préliminaire version 2015-02-28-Preview pour recourir aux mappages de champs.

Vous pouvez spécifier plusieurs mappages de champs.

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

Les noms de champs sources et cibles sont sensibles à la casse.

##Transformer des chaînes à partir d'une table de base de données en collections de chaînes##

Les mappages de champs peuvent également être utilisés pour transformer des valeurs de champs source à l'aide de *fonctions de mappage*.

Une de ces fonctions, `jsonArrayToStringCollection`, analyse un champ qui contient une chaîne formatée sous forme de tableau JSON dans un champ Collection(Edm.String) dans l'index cible. Elle est conçue pour une utilisation avec l'indexeur SQL Azure en particulier, car SQL ne dispose pas d'un type de données de collection natif. Elle peut être utilisée comme suit :

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Par exemple, si le champ source contient la chaîne de caractères `["red", "white", "blue"]`, le champ cible de type `Collection(Edm.String)` sera rempli avec les valeurs `"red"`, `"white"` et `"blue"`.

Notez que la propriété `targetFieldName` est facultative. Si elle n'est pas définie, la valeur `sourceFieldName` est utilisée.

##Basculer la stratégie de détection des modifications sur une source de données##
  
Dans Azure Search, les éléments pris en charge par vos sources de données et schéma de données déterminent en grande partie la stratégie de détection des modifications à utiliser. Au fil du temps, surtout si vous mettez à niveau ou migrez des bases de données, vous pouvez être amené à basculer une stratégie de détection des modifications sur un autre type. Par exemple, si vous venez de mettre à jour votre base de données SQL Azure vers une version plus récente qui prend en charge le suivi des modifications intégré, vous pouvez basculer de la stratégie de limite supérieure à la stratégie de suivi des modifications intégré. Ou peut-être avez-vous décidé d'utiliser une autre colonne en guise de limite supérieure.

Si vous appelez simplement l'API REST de source de données PUT pour mettre à jour votre source de données, vous risquez d'obtenir une réponse 400 contenant un message d'erreur semblable au suivant :


	"Change detection policy cannot be changed for data source '…' because indexer '…' references this data source and has a non-empty change tracking state. You can use Reset API to reset the indexer's change tracking state, and retry this call."

 Vous vous demandez sans doute ce que cela signifie et comment y remédier. Cette erreur se produit car Azure Search gère l'état interne associé à votre stratégie de détection des modifications. Quand la stratégie est modifiée, l'état existant est invalidé dans la mesure où il ne s'applique pas à la nouvelle stratégie. Cela signifie que l'indexeur doit démarrer l'indexation de votre source de données à l'aide de la nouvelle stratégie à partir de zéro, ce qui peut avoir des conséquences de votre côté (telles que des charges supplémentaires sur la base de données ou des coûts de bande passante réseau supérieurs). C'est la raison pour laquelle Azure Search vous demande d'appeler l'[API de réinitialisation d'indexeur](https://msdn.microsoft.com/library/azure/dn946897.aspx) pour réinitialiser l'état associé à la stratégie de détection des modifications actuelle ; la stratégie pourra ensuite être modifiée avec un appel de source de données PUT normal. Bien sûr, Azure Search pourrait effectuer la réinitialisation automatiquement, mais nous avons pensé qu'il était important que vous compreniez bien les implications de l'appel de l'API de réinitialisation.

##Encoder par URL les clés de document qui contiennent des caractères non sécurisés pour les URL##

Azure Search limite les caractères à l'intérieur d'un champ de clé de document aux caractères sécurisés pour les URL, car les utilisateurs doivent être en mesure de rechercher des documents en fonction de leurs clés. Que se passe-t-il quand les documents que vous devez indexer contiennent des caractères de ce type dans le champ de clé ? Si vous indexez des documents vous-même à l'aide d'une API REST ou d'un Kit de développement logiciel (SDK) client, vous pouvez encoder les clés par URL. Dans le cas d'un indexeur, vous pouvez indiquer à Azure Search d'encoder vos clés par URL en définissant le paramètre **base64EncodeKeys** sur `true` au moment de la création ou de la mise à jour de l'indexeur :

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "base64EncodeKeys": true }
    }

Pour plus d'informations sur l'encodage, consultez cet [article MSDN](http://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx).

REMARQUE : si vous devez effectuer des recherches ou des filtrages qui portent sur des valeurs de clé, vous devrez de la même manière encoder les clés utilisées dans votre demande, afin que celle-ci puisse trouver la valeur encodée stockée dans l'index de recherche.


##Tolérer des échecs d'indexation de certains documents##

Par défaut, un indexeur Azure Search arrête l'indexation dès que l'opération échoue avec le moindre document. Selon votre scénario, vous pouvez choisir de tolérer certains échecs (par exemple, si vous réindexez à plusieurs reprises la totalité de votre source de données). Azure Search fournit deux paramètres d'indexeur qui permettent d'affiner ce comportement :

- **maxFailedItems** : nombre d'éléments dont l'indexation peut échouer avant que l'exécution de l'indexeur soit considérée comme un échec. La valeur par défaut est 0.
- **maxFailedItemsPerBatch** : nombre d'éléments dont l'indexation peut échouer dans un même lot avant que l'exécution de l'indexeur soit considérée comme un échec. La valeur par défaut est 0.

Vous pouvez modifier ces valeurs à tout moment en spécifiant un de ces paramètres, ou les deux, au moment de la création ou de la mise à jour de votre indexeur :

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
	Content-Type: application/json
	api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }
    }

Même si vous choisissez de tolérer certains échecs, les informations indiquant les documents affectés par ceux-ci sont retournées par l'[API d'obtention d'état d'indexeur](https://msdn.microsoft.com/library/azure/dn946884.aspx).

C'est tout pour le moment. Si vous avez des idées ou des suggestions de contenu, vous pouvez nous envoyer un tweet en utilisant le hashtag #AzureSearch ou les soumettre via notre [page UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
 

<!---HONumber=AcomDC_0128_2016-->