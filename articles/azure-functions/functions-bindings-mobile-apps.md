<properties
	pageTitle="Liaisons Mobile Apps Azure Functions| Microsoft Azure"
	description="Découvrez comment utiliser des liaisons Azure Mobile Apps dans Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Liaisons Azure Mobile Apps Azure Functions

Cet article explique comment configurer et coder des liaisons Azure Mobile Apps dans Azure Functions.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure App Service Mobile Apps vous permet d’exposer les données de point de terminaison de table aux clients mobiles. Ces mêmes données tabulaires sont utilisables avec les liaisons d’entrée et de sortie dans Azure Functions. Une application mobile de serveur principal Node.js est idéale pour exposer les données tabulaires à utiliser avec vos fonctions, grâce à sa prise en charge des schémas dynamiques. Le schéma dynamique est activé par défaut et doit être désactivé dans une application mobile de production. Pour plus d’informations sur les points de terminaison de table dans un serveur principal Node.js, consultez [Vue d’ensemble : opérations de table](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations). Dans Mobile Apps, le serveur principal Node.js prend en charge la navigation sur portail et la modification des tables. Pour plus d’informations, consultez [Modification sur le portail](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing) dans la rubrique du Kit de développement logiciel Node.js. Lorsque vous utilisez une application mobile de serveur principal .NET avec Azure Functions, vous devez manuellement mettre à jour votre modèle de données, comme requis par votre fonction. Pour plus d’informations sur les points de terminaison de table dans une application mobile de serveur principal .NET, consultez [Définir un contrôleur de table](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) dans la rubrique du Kit de développement logiciel de .NET.

## <a id="mobiletablesapikey"></a> Utiliser une clé API pour sécuriser l’accès à vos points de terminaison de table Mobile Apps

Dans Azure Functions, les liaisons de tables vous permettent de spécifier une clé API, qui est une clé secrète partagée pouvant être utilisée pour empêcher les accès indésirables à partir d’applications autres que vos fonctions. Mobile Apps n’intègre aucune prise en charge de l’authentification par clé API. Toutefois, vous pouvez implémenter une clé API dans votre application mobile principale Node.js en suivant les exemples présentés dans [Azure App Service Mobile Apps backend implementing an API key](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) (Serveur principal Azure App Service Mobile Apps implémentant une clé API). Vous pouvez implémenter une clé API dans une [application mobile de serveur principal .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key) de la même façon.

>[AZURE.IMPORTANT] Cette clé API ne doit pas être distribuée avec vos clients d’application mobile ; elle doit uniquement être distribuée de façon sécurisée aux clients côté service, comme Azure Functions.

## <a id="mobiletablesinput"></a> Liaisons d’entrée Azure Mobile Apps

Les liaisons d’entrée peuvent charger un enregistrement à partir d’un système d'extrémité de table mobile et le transmettre directement à votre liaison. L’ID d’enregistrement est déterminé en fonction du déclencheur qui a appelé la fonction. Dans une fonction C#, toutes les modifications apportées à l’enregistrement sont automatiquement renvoyées à la table une fois que la fonction s’est correctement terminée.

#### function.json pour les liaisons d’entrée Mobile Apps

Le fichier *function.json* prend en charge les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour le nouvel enregistrement.
- `type` : type de liaison devant être défini sur *mobileTable*.
- `tableName` : table dans laquelle sera créé le nouvel enregistrement.
- `id` : ID de l’enregistrement à récupérer. Cette propriété prend en charge les liaisons semblables à `{queueTrigger}`, qui utiliseront la valeur de chaîne du message de file d’attente en tant qu’ID de l’enregistrement.
- `apiKey` : chaîne correspondant au paramètre d’application qui spécifie la clé API facultative pour l’application mobile. Cette propriété est requise lorsque votre application mobile utilise une clé API pour restreindre l’accès client.
- `connection` : chaîne correspondant au paramètre d’application qui spécifie l’URI de votre application mobile.
- `direction` : sens de la liaison, qui doit être défini sur *in*.

Exemple de fichier *function.json* :

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Exemple de code Azure Mobile Apps pour un déclencheur de file d’attente C#

En fonction de l’exemple de fichier function.json ci-dessus, la liaison d’entrée récupère l’enregistrement dont l’ID correspond à la chaîne de message de file d’attente depuis un point de terminaison de table Mobile Apps et le transmet au paramètre *record*. Si l’enregistrement est introuvable, le paramètre présente la valeur Null. L’enregistrement est ensuite mis à jour avec la nouvelle valeur *Text* une fois la fonction terminée.

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Exemple de code Azure Mobile Apps pour un déclencheur de file d’attente Node.js

En fonction de l’exemple de fichier function.json ci-dessus, la liaison d’entrée récupère l’enregistrement dont l’ID correspond à la chaîne de message de file d’attente depuis un point de terminaison de table Mobile Apps et le transmet au paramètre *record*. Dans les fonctions Node.js, les enregistrements mis à jour ne sont pas renvoyés à la table. Cet exemple de code écrit l’enregistrement récupéré dans le journal.

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


## <a id="mobiletablesoutput"></a> Liaisons de sortie Azure Mobile Apps

Votre fonction peut écrire un enregistrement dans un système d'extrémité de table Mobile Apps à l’aide d’une liaison de sortie.

#### function.json pour les liaisons de sortie Mobile Apps

Le fichier function.json prend en charge les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour le nouvel enregistrement.
- `type` : type de liaison devant être défini sur *mobileTable*.
- `tableName` : table dans laquelle est créé le nouvel enregistrement.
- `apiKey` : chaîne correspondant au paramètre d’application qui spécifie la clé API facultative pour l’application mobile. Cette propriété est requise lorsque votre application mobile utilise une clé API pour restreindre l’accès client.
- `connection` : chaîne correspondant au paramètre d’application qui spécifie l’URI de votre application mobile.
- `direction` : sens de la liaison, qui doit être défini sur *out*.

Exemple de fichier function.json :

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### Exemple de code Azure Mobile Apps pour un déclencheur de file d’attente C#

Cet exemple de code C# insère un nouvel enregistrement sur un point de terminaison de table Mobile Apps avec une propriété *Text* dans la table spécifiée dans la liaison ci-dessus.

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Exemple de code Azure Mobile Apps pour un déclencheur de file d’attente Node.js

Cet exemple de code Node.js insère un nouvel enregistrement sur un point de terminaison de table Mobile Apps avec une propriété *Text* dans la table spécifiée dans la liaison ci-dessus.

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Étapes suivantes

[AZURE.INCLUDE [Étapes suivantes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0525_2016-->