<properties
   pageTitle="Vue d’ensemble d’Azure Functions | Microsoft Azure"
   description="Comprendre comment Azure Functions permet d’optimiser les charges de travail asynchrones en créant des fonctions simples qui peuvent être écrites en quelques minutes."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="04/21/2016"
   ms.author="cfowler;mahender"/>
   
   
# Vue d’ensemble d’Azure Functions

Cette rubrique fournit une vue d’ensemble d’Azure Functions. Si vous souhaitez vous familiariser avec Azure Functions, commencez par [Créer votre première fonction Azure](functions-create-first-azure-function.md). Si vous recherchez des informations plus techniques sur les fonctions, consultez la [référence du développeur](functions-reference.md).

## Créez des fonctions plus rapidement !

Écrivez une fonction en moins d’une minute, qu’il s’agisse d’une tâche simple pour nettoyer une base de données ou d’une fonctionnalité traitant des millions de messages d’appareils connectés. Utilisez votre langage de développement préféré (C#, Node.JS, Python, etc.). Payez uniquement pour temps d’exécution de votre code et faites confiance à Azure pour effectuer la mise à l’échelle nécessaire.

Azure Functions est une solution pour exécuter facilement des petits morceaux de code, ou « fonctions », dans le cloud. Vous pouvez simplement écrire le code dont vous avez besoin pour le problème, sans vous soucier d’une application dans sa globalité ou de l’infrastructure pour l’exécuter. Le développement gagne en productivité. Quelques minutes suffisent pour [créer une première fonction](functions-create-first-azure-function.md).

## Que puis-je faire avec Azure Functions ?

Azure Functions est une solution idéale pour le traitement des données, et l’intégration de systèmes, l’utilisation de l’Internet des objets (IoT) et la création de microservices et d’API simples.

Azure Functions inclut une galerie de modèles pour les scénarios courants, y compris :

* Répondre à une demande de webhook GitHub
* Redimensionner une image qui a été téléchargée sur Azure Storage
* Utiliser des files d’attente de traitement des commandes
* Et bien plus encore... 

Pour des informations plus détaillées sur le fonctionnement d’Azure Functions et quelques exemples de configuration, consultez la [Référence du développeur](functions-reference.md).

## Caractéristiques

Azure Functions est une plateforme d’entreprise complète dotée de fonctionnalités permettant de simplifier les tâches de connectivité et d’intégration complexes. Avec cet ensemble principal de fonctionnalités, les développeurs qui utilisent Azure Functions peuvent augmenter leur productivité en se concentrant uniquement sur leur objectif plutôt que sur l’assemblage des éléments d’infrastructure.

Les fonctionnalités suivantes sont incluses dans Azure Functions :
    
* **Choix du langage** : écrivez des fonctions avec C#, Node.js, Python, F#, PHP, batch, bash, Java ou tout autre fichier exécutable.  
* **Modèle de tarification à l’utilisation** : ne payez que le temps passé à exécuter votre code. Consultez le plan App Service dynamique dans la [section de tarification](#pricing) ci-dessous.  
* **Intégration de vos propres dépendances** : Azure Functions prenant en charge NuGet et NPM, vous pouvez utiliser vos bibliothèques préférées.  
* **Sécurité intégrée** : protégez les fonctions déclenchées par HTTP à l’aide de fournisseurs OAuth comme Azure Active Directory, Facebook, Google, Twitter et Microsoft Account.  
* **Intégration sans code** : tirez facilement parti des services Azure et des offres SaaS (software-as-a-service). Pour obtenir des exemples, consultez la [section relative aux intégrations](#integrations).  
* **Développement flexible** : modifiez vos fonctions avec un éditeur intégré au portail ou configurez une intégration continue et déployez votre code via GitHub, Visual Studio Team Services, etc.  
* **Open Source** : Azure Functions est open source et [disponible sur GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

### <a name="integrations"></a>Intégrations

Azure Functions prend en charge de nombreuses intégrations avec les services tiers et Azure. Vous pouvez les utiliser pour déclencher votre fonction et démarrer l’exécution ou pour servir d’entrée et de sortie à votre code. Le tableau ci-dessous présente des exemples d’intégrations prises en charge par Azure Functions.

[AZURE.INCLUDE [calcul dynamique](../../includes/functions-bindings.md)]

## <a name="pricing"></a>Combien coûte Azure Functions ?

Azure Functions peut être exécuté de deux façons : à l’aide d’un plan Dynamic App Service et à l’aide d’un plan Classic App Service.

Dans un **plan App Service dynamique**, la gestion des ressources n’est plus une préoccupation. Pour chaque exécution de votre fonction, Azure fournit toutes les ressources de calcul nécessaires. Vous payez uniquement le temps d’exécution de votre code. Toutes les informations sur la tarification sont disponibles sur la [page Tarification de Functions](/pricing/details/functions).

Un **plan App Service classique** vous permet d’exécuter des fonctions comme vos applications API, mobile et web. Il s’agit d’une solution idéale si vous utilisez déjà App Service pour d’autres applications : vos fonctions peuvent être exécutées dans le cadre du même plan, sans coûts supplémentaires. Toutes les informations sont disponibles dans la [page Tarification de App Service](/pricing/details/app-service/).

## Problèmes liés aux rapports

[AZURE.INCLUDE [Problèmes liés aux rapports](../../includes/functions-reporting-issues.md)]

##Étapes suivantes

+ La rubrique [Créer votre première fonction Azure](functions-create-first-azure-function.md) vous permet de créer votre première fonction à l’aide du démarrage rapide d’Azure Functions. 
+ La rubrique [Référence du développeur Azure Functions](functions-reference.md) fournit des informations techniques supplémentaires sur l’exécution d’Azure Functions ainsi qu’une référence pour le codage de fonctions et la définition des déclencheurs et des liaisons.
+ Le didacticiel [Test d’Azure Functions](functions-test-a-function.md) décrit plusieurs outils et techniques permettant de tester vos fonctions.
+ La rubrique [Comment mettre à l’échelle Azure Functions](functions-scale.md) présente les plans de services disponibles dans Azure Functions, dont le plan de service dynamique, et explique comment choisir le plan adapté à vos besoins. 
+ [Qu'est-ce qu'Azure App Service ?](../app-service/app-service-value-prop-what-is.md) Azure Functions s’appuie sur la plateforme Azure App Service pour les fonctionnalités essentielles comme les déploiements, les variables d’environnement et les diagnostics. 

<!---HONumber=AcomDC_0427_2016-->