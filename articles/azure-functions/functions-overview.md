---
title: "Vue d’ensemble d’Azure Functions | Microsoft Docs"
description: "Découvrez comment utiliser les Azure Functions pour optimiser les charges de travail asynchrones en quelques minutes."
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/17/2017
ms.author: cfowler;mahender;glenga
translationtype: Human Translation
ms.sourcegitcommit: 30cc3b8749d5b36b89b242e2691003cc6f67f7d2
ms.openlocfilehash: 00359057d702c556cd8beb91cf17ccf41c96f601


---
# <a name="azure-functions-overview"></a>Vue d’ensemble d’Azure Functions
Azure Functions est une solution conçue pour exécuter facilement des petits morceaux de code, ou « fonctions », dans le cloud. Vous pouvez simplement écrire le code dont vous avez besoin pour le problème, sans vous soucier d’une application dans sa globalité ou de l’infrastructure pour l’exécuter. Grâce aux fonctions, le développement gagne en productivité et vous pouvez utiliser votre langage de développement préféré, tel que C#, F#, Node.js, Python ou PHP. Payez uniquement pour temps d’exécution de votre code et faites confiance à Azure pour effectuer la mise à l’échelle nécessaire.

Cette rubrique fournit une vue d’ensemble d’Azure Functions. Si vous souhaitez vous familiariser avec Azure Functions, commencez par [créer votre première fonction Azure](functions-create-first-azure-function.md). Si vous recherchez des informations plus techniques sur Azure Functions, consultez les [informations de référence pour les développeurs](functions-reference.md).

## <a name="features"></a>Caractéristiques
Voici les principales fonctionnalités d’Azure Functions :

* **Choix du langage** : écrivez des fonctions avec C#, F#, Node.js, Python, PHP, Batch, Bash ou tout autre fichier exécutable.
* **Modèle de tarification à l’utilisation** : seul le temps passé à exécuter votre code vous est facturé. Reportez-vous à l’option de plan d’hébergement de consommation dans la [section relative à la tarification](#pricing).  
* **Intégration de vos propres dépendances** : Azure Functions prenant en charge NuGet et NPM, vous pouvez utiliser vos bibliothèques préférées.  
* **Sécurité intégrée** : protégez les fonctions déclenchées par HTTP à l’aide de fournisseurs OAuth comme Azure Active Directory, Facebook, Google, Twitter et Microsoft Account.  
* **Intégration simplifiée** : tirez facilement parti des services Azure et des offres SaaS (software as a service). Pour obtenir des exemples, consultez la [section relative aux intégrations](#integrations).  
* **Développement flexible** : codez vos fonctions directement dans le portail ou configurez une intégration continue et déployez votre code via GitHub, Visual Studio Team Services et les autres [outils de développement pris en charge](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Open Source** : le runtime Azure Functions est open source et [disponible sur GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Que puis-je faire avec Azure Functions ?
Azure Functions est une solution idéale pour le traitement des données, l’intégration de systèmes, l’utilisation de l’Internet des objets (IoT) et la création de microservices et d’API simples. La solution Azure Functions peut être utilisée pour des tâches telles que le traitement d’images ou de commandes, la maintenance de fichiers, les tâches longues que vous voulez exécuter dans un thread en arrière-plan ou toute tâche que vous voulez exécuter selon une planification. 

Azure Functions fournit des modèles pour vous aider à mettre en œuvre les principaux scénarios, notamment :

* **BlobTrigger** : traitez les objets blob d’Azure Storage lors de leur ajout à des conteneurs. Vous pouvez utiliser cette fonction pour le redimensionnement d’images.
* **EventHubTrigger** : répondre aux événements remis à un Azure Event Hub. Particulièrement utile pour l’instrumentation de l’application, le traitement du workflow ou de l’expérience utilisateur, et les scénarios de l’Internet des Objets (IoT).
* **Webhook générique** : traitez les requêtes HTTP de webhook à partir de n’importe quel service prenant en charge les webhooks.
* **Webhook GitHub** : répondez aux événements qui se produisent dans vos référentiels GitHub. Pour obtenir un exemple, consultez l’article [Créer une fonction Azure d’API ou de webhook](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** : déclenchez l’exécution de votre code à l’aide d’une requête HTTP.
* **QueueTrigger** : répondez aux messages dès leur arrivée dans une file d’attente de stockage Azure. Pour obtenir un exemple, consultez l’article [Créer une fonction Azure liée à un service Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** : connectez votre code à d’autres services Azure ou à des services locaux en écoutant les files d’attente de messages. 
* **ServiceBusTopicTrigger** : connectez votre code à d’autres services Azure ou à des services locaux en vous abonnant à des rubriques. 
* **TimerTrigger** : exécutez des tâches de nettoyage ou d’autres tâches de traitement par lots selon une planification prédéfinie. Pour obtenir un exemple, consultez l’article [Créer une fonction de traitement d’événement](functions-create-an-event-processing-function.md).

Azure Functions prend en charge les *déclencheurs*, qui permettent de démarrer l’exécution de votre code, et les *liaisons*, qui permettent de simplifier le codage des données d’entrée et de sortie. Pour obtenir une description détaillée des déclencheurs et des liaisons offerts par Azure Functions, consultez l’article [Informations de référence pour les développeurs sur les déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

## <a name="a-nameintegrationsaintegrations"></a><a name="integrations"></a>Intégrations
Azure Functions s’intègre avec différents services Azure et services tiers. Ces services peuvent déclencher votre fonction et démarrer l’exécution, ou servir d’entrée et de sortie à votre code. Azure Functions prend en charge les intégrations de service suivantes. 

* Document DB Azure
* Hubs d'événements Azure 
* Azure Mobile Apps (tables)
* Azure Notification Hubs
* Azure Service Bus (files d’attente et rubriques)
* Azure Storage (objets blob, files d’attente et tables) 
* GitHub (webhooks)
* Services locaux (à l’aide de Service Bus)

## <a name="a-namepricingahow-much-does-functions-cost"></a><a name="pricing"></a>Combien coûte Azure Functions ?
Deux types de plans de tarification sont proposés pour Azure Functions. Choisissez celui qui correspond le mieux à vos besoins : 

* **Plan de consommation** : lorsque votre fonction est exécutée, Azure fournit toutes les ressources de calcul nécessaires. Vous n’avez pas à vous préoccuper de la gestion des ressources et seul le temps d’exécution vous est facturé. 
* **Plan App Service** : exécutez vos fonctions comme vos applications API, mobiles et web. Si vous utilisez déjà App Service pour d’autres applications, vous pouvez exécuter vos fonctions dans le cadre du même plan, sans coûts supplémentaires. 

Vous trouverez toutes les informations sur la tarification sur la [page Tarification de Functions](https://azure.microsoft.com/pricing/details/functions/). Pour plus d’informations sur la mise à l’échelle de vos fonctions, consultez l’article [Mise à l’échelle d’Azure Functions](functions-scale.md).

## <a name="next-steps"></a>Étapes suivantes
* [créer votre première fonction Azure](functions-create-first-azure-function.md)  
   vous permet de créer votre première fonction à l’aide du démarrage rapide d’Azure Functions. 
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)  
   fournit des informations techniques supplémentaires sur l’exécution d’Azure Functions, ainsi qu’une référence pour le codage de fonctions et la définition des déclencheurs et des liaisons.
* [Test d’Azure Functions](functions-test-a-function.md)  
   décrit plusieurs outils et techniques permettant de tester vos fonctions.
* [Comment mettre à l’échelle Azure Functions](functions-scale.md)  
  Présente les plans de service disponibles pour Azure Functions, dont le plan d’hébergement de consommation, et explique comment choisir le plan adapté à vos besoins. 
* [En savoir plus sur Azure App Service](../app-service/app-service-value-prop-what-is.md)  
   Azure Functions s’appuie sur la plateforme Azure App Service pour les fonctionnalités essentielles comme les déploiements, les variables d’environnement et les diagnostics. 




<!--HONumber=Dec16_HO1-->


