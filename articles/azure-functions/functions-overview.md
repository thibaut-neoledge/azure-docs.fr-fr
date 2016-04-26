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
   ms.date="03/09/2016"
   ms.author="cfowler;mahender"/>
   
   
# Vue d’ensemble d’Azure Functions

## Créez des fonctions plus rapidement !

Écrivez une fonction en moins d’une minute, qu’il s’agisse d’une tâche simple pour nettoyer une base de données ou d’une fonctionnalité traitant des millions de messages d’appareils connectés. Utilisez votre langage de développement préféré (C#, Node.JS, Python, etc.). Payez uniquement pour temps d’exécution de votre code et faites confiance à Azure pour effectuer la mise à l’échelle nécessaire.

Azure Functions est une solution pour exécuter facilement des petits morceaux de code, ou « fonctions », dans le cloud. Vous pouvez simplement écrire le code dont vous avez besoin pour le problème, sans vous soucier d’une application dans sa globalité ou de l’infrastructure pour l’exécuter. Cela peut rendre le développement encore plus productif, et vous pouvez [prendre en main votre première fonction](functions-create-first-azure-function.md) en quelques minutes.

## Que puis-je faire avec Azure Functions ?

Azure Functions est une solution idéale pour le traitement des données, et l’intégration de systèmes, l’utilisation de l’Internet des objets (IoT) et la création de microservices et d’API simples.

Azure Functions inclut une galerie de modèles pour les scénarios courants, y compris :

* Répondre à une demande de webhook GitHub
* Redimensionner une image qui a été téléchargée sur Azure Storage
* Utiliser des files d’attente de traitement des commandes
* Et bien plus encore... 

Pour plus de détails sur le fonctionnement d’Azure Functions et des exemples de configuration, voir la page [Référence du développeur](functions-reference.md).

## Caractéristiques

Azure Functions est une plateforme d’entreprise complète dotée de fonctionnalités permettant de simplifier les tâches de connectivité et d’intégration complexes. Avec cet ensemble principal de fonctionnalités, les développeurs qui utilisent Azure Functions peuvent augmenter leur productivité en se concentrant uniquement sur leur objectif plutôt que sur l’assemblage des éléments d’infrastructure.

Les fonctionnalités suivantes sont incluses dans Azure Functions :
    
* **Choix du langage** : écrivez des fonctions en C#, Node.js, Python, F#, PHP, batch, bash, Java ou tout exécutable.  
* **Modèle de tarification à l’utilisation** : payez uniquement pour le temps passé à l’exécution de votre code. Voir l’option de plan Dynamic App Service dans la [section de tarification](#pricing) ci-dessous.  
* **Apport de vos propres dépendances** : Azure Functions prend en charge NuGet et NPM, vous pouvez donc utiliser vos bibliothèques préférées.  
* **Sécurité intégrée** : protégez les fonctions déclenchées avec HTTP à l’aide de fournisseurs OAuth comme Azure Active Directory, Facebook, Google, Twitter et un compte Microsoft.  
* **Intégration sans code** : tirez facilement parti des services Azure et des offres SaaS (software-as-a-service). Voir la [section relative aux intégrations](#integrations) ci-dessous pour obtenir des exemples.  
* **Développement flexible** : modifiez vos fonctions avec un éditeur dans le portail ou configurez l’intégration continue et déployez votre code via GitHub, Visual Studio Team Services, etc.  
* **Open source** : Azure Functions est open source et [disponible sur GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

### <a name="integrations"></a>Intégrations

Azure Functions prend en charge de nombreuses intégrations avec les services tiers et Azure. Vous pouvez les utiliser pour déclencher votre fonction et démarrer l’exécution ou pour servir d’entrée et de sortie à votre code. Le tableau ci-dessous présente des exemples d’intégrations prises en charge par Azure Functions.

[AZURE.INCLUDE [calcul dynamique](../../includes/functions-bindings.md)]

## <a name="pricing"></a>Combien coûte Azure Functions ?

Azure Functions peut être exécuté de deux façons : à l’aide d’un plan Dynamic App Service et à l’aide d’un plan Classic App Service.

Dans un **plan Dynamic App Service**, vous n’avez pas à vous soucier de la gestion des ressources. Pour chaque exécution de votre fonction, Azure fournit toutes les ressources de calcul nécessaires. Vous payez uniquement le temps d’exécution de votre code. L’intégralité des détails de tarification sont disponibles à la [page Tarification d’Azure Functions](/pricing/details/functions).

Un **plan Classic App Service** vous permet d’exécuter des fonctions comme vos applications API, mobile et web. Il s’agit d’une solution idéale si vous utilisez déjà App Service pour d’autres applications : vos fonctions peuvent être exécutées dans le cadre du même plan, sans coûts supplémentaires. Vous trouverez plus de détails sur la [page Tarification App Service](/pricing/details/app-service/).

## Problèmes liés aux rapports

[AZURE.INCLUDE [Problèmes liés aux rapports](../../includes/functions-reporting-issues.md)]

<!---HONumber=AcomDC_0420_2016-->