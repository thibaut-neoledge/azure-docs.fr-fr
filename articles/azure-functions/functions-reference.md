<properties
	pageTitle="Information de référence pour les développeurs sur Azure Functions | Microsoft Azure"
	description="Découvrez les concepts et les composants Azure Functions communs à tous les langages et liaisons."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Informations de référence pour les développeurs sur Azure Functions

Les fonctions Azure Functions partagent quelques concepts techniques et composants de base, quels que soient le langage et la liaison que vous utilisez. Avant de passer à l'apprentissage des détails propres à un langage ou une liaison donnés, veillez à lire cette présentation qui s'applique à l’ensemble d’entre eux.

Cet article suppose que vous avez déjà lu la [Vue d'ensemble d'Azure Functions](functions-overview.md) et que vous connaissez les [concepts du Kit de développement logiciel (SDK) WebJobs, notamment les déclencheurs, les liaisons et le runtime JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Azure Functions est fondé sur le SDK WebJobs.

## function

La *fonction* est le principal concept d'Azure Functions. Vous écrivez du code pour une fonction dans le langage de votre choix et enregistrez le(s) fichier(s) de code et un fichier de configuration dans le même dossier. La configuration est au format JSON, et le fichier est nommé `function.json`. Plusieurs langages sont pris en charge, et chacun d’entre eux offre une expérience légèrement différente, optimisée pour fonctionner idéalement pour ce langage. Exemple de structure des dossiers :

```
mynodefunction
| - function.json
| - index.js
| - node_modules
| | - ... packages ...
| - package.json
mycsharpfunction
| - function.json
| - run.csx
```

## function.json et liaisons

Le fichier `function.json` contient la configuration propre à une fonction, y compris ses liaisons. Le runtime lit ce fichier pour déterminer les événements à déclencher, les données à inclure lors de l'appel de la fonction et l'emplacement où envoyer les données transmises à partir de la fonction elle-même.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Vous pouvez empêcher le runtime d'exécuter la fonction en définissant la propriété `disabled` sur `true`.

La propriété `bindings` vous permet de configurer les liaisons et les déclencheurs. Chaque liaison partage quelques paramètres communs et des paramètres propres à un type de liaison donné. Chaque liaison requiert les paramètres suivants :

|Propriété|Valeurs/types|Commentaires|
|---|-----|------|
|`type`|string|Type de liaison. Par exemple, `queueTrigger`.
|`direction`|'in', 'out'| Indique si la liaison sert à recevoir des données dans la fonction ou à envoyer des données à partir de la fonction.
| `name` | string | Le nom qui sera utilisé pour les données liées dans la fonction. Pour C#, ce sera un nom d'argument ; pour JavaScript, ce sera la clé dans une liste de clés/valeurs.

## Runtime (hôte de script et hôte web)

Le runtime, également appelé hôte de script, est l'hôte du Kit de développement logiciel (SDK) WebJobs sous-jacent qui écoute les événements, collecte et envoie les données et, enfin, exécute votre code.

Pour faciliter les déclencheurs HTTP, il existe également un hôte web conçu pour précéder l'hôte de script dans les scénarios de production. Cela permet d'isoler l'hôte de script du trafic frontal géré par l'hôte web.

## Structure des dossiers

Un hôte de script pointe vers un dossier qui contient un fichier de configuration et une ou plusieurs fonctions.

```
parentFolder (for example, wwwroot)
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

Le fichier *host.json* contient une configuration propre à l’hôte de script et se trouve dans le dossier parent.

Chaque fonction a un dossier qui contient un ou des fichier(s) de code, *function.json* et d'autres dépendances.

Lorsque vous configurez un projet pour déployer des fonctions dans une application de fonction dans Azure App Service, vous pouvez traiter cette structure de dossiers comme le code de votre site. Vous pouvez utiliser des outils existants, comme le déploiement et l'intégration continus, ou des scripts de déploiement personnalisés pour effectuer l'installation du package ou la transpilation de code au moment du déploiement.

## Exécution en parallèle

Lorsque plusieurs événements de déclenchement se produisent plus rapidement qu'un runtime de fonction monothread ne peut les traiter, le runtime peut appeler la fonction plusieurs fois en parallèle. Si une application de fonction utilise le [Plan de service dynamique](functions-scale.md#dynamic-service-plan), l'application de fonction peut monter en charge automatiquement pour atteindre jusqu'à 10 instances simultanées. Que l’application s’exécute sur le plan de service dynamique ou sur un [Plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) standard, chaque instance de l'application de fonction peut traiter en parallèle des appels de fonction simultanés en utilisant plusieurs threads. Le nombre maximal d'appels de fonction simultanés dans chaque instance d'application de fonction varie en fonction de la taille de la mémoire de l'application de fonction.

## Azure Functions Pulse  

Pulse est un flux d'événements en direct qui indique la fréquence d'exécution de votre fonction, ainsi que les réussites et les échecs. Vous pouvez également surveiller le temps moyen d'exécution. Nous allons y ajouter plus de fonctionnalités et d’éléments de personnalisation au fil du temps. Vous pouvez accéder à la page **Pulse** à partir de l’onglet **Analyse**.

## Liaisons

Voici un tableau de toutes les liaisons prises en charge.

[AZURE.INCLUDE [calcul dynamique](../../includes/functions-bindings.md)]

## Problèmes liés aux rapports

[AZURE.INCLUDE [Problèmes liés aux rapports](../../includes/functions-reporting-issues.md)]

## Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Informations de référence pour les développeurs C# sur Azure Functions](functions-reference-csharp.md)
* [Azure Functions NodeJS developer reference (Référence pour les développeurs NodeJS Azure Functions)](functions-reference-node.md)
* [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0427_2016-->