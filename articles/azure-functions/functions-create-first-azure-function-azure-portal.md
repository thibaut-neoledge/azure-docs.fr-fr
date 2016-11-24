---
title: "Créer une fonction à l’aide du Portail Azure | Microsoft Docs"
description: "Générez votre première fonction Azure, une application sans serveur, en moins de deux minutes."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 638054fb49ace081a7d67817d9dcecb8f5ca1a94
ms.openlocfilehash: b2ad194b62b61e3b16d660718aee155ff2dbf197


---
# <a name="create-a-function-from-the-azure-portal"></a>Créer une fonction à l’aide du Portail Azure
## <a name="overview"></a>Vue d’ensemble
Azure Functions est une expérience de calcul à la demande pilotée par les événements, qui étend la plateforme d’application Azure existante avec des capacités d’implémenter du code déclenché par des événements se produisant dans d’autres services Azure, dans les produits SaaS et dans les systèmes locaux. Avec Azure Functions, vos applications sont mises à l’échelle à la demande, et vous payez uniquement pour les ressources que vous utilisez. Azure Functions vous permet de créer des unités de code planifiées ou déclenchées, et de les implémenter dans divers langages de programmation. Pour en savoir plus sur Azure Functions, consultez [Vue d’ensemble d’Azure Functions](functions-overview.md).

Cette rubrique indique comment utiliser le Portail Azure pour créer une fonction Azure Node.js « Hello World » simple, appelée par un déclencheur HTTP. Avant de pouvoir créer une fonction dans le Portail Azure, vous devez créer explicitement une Function App dans Azure App Service. Pour que la Function App soit créée automatiquement pour vous, consultez [le didacticiel de démarrage rapide des autres Azure Functions](functions-create-first-azure-function.md), une expérience de démarrage rapide plus simple, incluant une vidéo.

## <a name="create-a-function-app"></a>Créer une Function App
Une Function App héberge l’exécution de vos fonctions dans Azure. Suivez ces étapes pour créer une Function App dans le portail Azure.

Pour créer votre première fonction, vous devez avoir un compte Azure actif. Si tel n’est pas le cas, des [comptes gratuits sont disponibles](https://azure.microsoft.com/free/).

1. Accédez au [Portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Cliquez sur **+Nouveau** > **Calculer** > **Function App**, sélectionnez votre **Abonnement**, tapez dans le champ **Nom de l’application** un nom identifiant de manière unique votre Function App, puis spécifiez les paramètres suivants :
   
   * **[Groupe de ressources](../azure-resource-manager/resource-group-overview.md)** : sélectionnez **Créer** et entrez un nom pour votre nouveau groupe de ressources. Vous pouvez également choisir un groupe de ressources existant, mais vous ne serez peut-être pas en mesure de créer un plan App Service dynamique pour votre Function App.
   * **[Plan d’hébergement](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)** : vous pouvez définir ce paramètre sur l’une des valeurs suivantes : 
     * **Dynamic plan (Plan dynamique)** : type de plan par défaut pour Azure Functions. Lorsque vous choisissez un plan dynamique, vous devez également renseigner le champ **Emplacement** et définir la valeur du champ **Allocation de mémoire** (en Mo). Pour plus d’informations sur les répercussions de l’allocation de mémoire sur les coûts, consultez [tarification Azure Functions](https://azure.microsoft.com/pricing/details/functions/). 
     * **Plan App Service** : un plan App Service exige de créer un **Plan App Service/Emplacement** ou d’en sélectionner un existant. Ces paramètres déterminent [l’emplacement, les fonctionnalités, les coûts et les ressources de calcul](https://azure.microsoft.com/pricing/details/app-service/) associés à votre application.  
   * **Compte de stockage**: chaque Function App nécessite un compte de stockage. Vous pouvez choisir un compte de stockage existant ou en créer un. 
     
     ![Créer une Function App dans le Portail Azure](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)

3. Cliquez sur **Créer** pour configurer et déployer la nouvelle Function App.  

Maintenant que la Function App est configurée, vous pouvez créer votre première fonction.

## <a name="create-a-function"></a>Créer une fonction
Les étapes suivantes permettent de créer une fonction à partir du démarrage rapide Azure Functions.

1. Dans l’onglet **Démarrage rapide**, cliquez sur **WebHook + API** et **JavaScript**, puis cliquez sur **Créer une fonction**. Une fonction Node.js prédéfinie est créée. 
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

2. (Facultatif) À ce stade du démarrage rapide, vous pouvez suivre une visite rapide des fonctionnalités Azure Functions dans le portail.    Après avoir terminé ou ignoré la visite, vous pouvez tester votre nouvelle fonction à l’aide du déclencheur HTTP.

## <a name="test-the-function"></a>Tester la fonction
Comme les démarrages rapides Azure Functions contiennent du code fonctionnel, vous pouvez tester votre nouvelle fonction immédiatement.

1. Dans l’onglet **Développer**, examinez la fenêtre **Code** et notez que ce code Node.js attend une requête HTTP avec un *nom* transmis dans le corps du message ou dans une chaîne de requête. Lorsque la fonction s’exécute, cette valeur est renvoyée dans le message de réponse.
   
2. Cliquez sur **Tester** pour afficher le volet de requête HTTP de test intégré pour la fonction.
 
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-develop-tab-testing.png)

2. Dans la zone de texte **Corps de la demande**, remplacez la valeur de la propriété *name* par votre nom, puis cliquez sur **Exécuter**. Vous voyez que l’exécution est déclenchée par une requête HTTP de test, que des informations sont écrites dans les journaux de diffusion en continu, et que la réponse « Hello » s’affiche dans la **Sortie**. 

3. Pour déclencher l’exécution de la même fonction à partir d’une autre fenêtre ou d’un autre onglet de navigateur, copiez la valeur **URL de la fonction** de l’onglet **Développer** et collez-la dans la barre d’adresse d’un navigateur. Puis ajoutez la valeur de chaîne de requête `&name=yourname` à l’URL et appuyez sur Entrée. Les mêmes informations sont écrites dans les journaux et le navigateur affiche la réponse « Hello » comme avant.

## <a name="next-steps"></a>Étapes suivantes
Ce démarrage rapide présente une exécution simple d’une fonction de base déclenchée par HTTP. Pour plus d’informations sur l’utilisation d’Azure Functions dans vos applications, consultez les rubriques suivantes :

* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)  
   Référence du programmeur pour le codage de fonctions et la définition de déclencheurs et de liaisons.
* [Test d’Azure Functions](functions-test-a-function.md)  
   décrit plusieurs outils et techniques permettant de tester vos fonctions.
* [Comment mettre à l’échelle Azure Functions](functions-scale.md)  
  Présente les plans de service disponibles dans Azure Functions, dont le plan de service dynamique, et explique comment choisir le plan adapté à vos besoins. 
* [Qu'est-ce qu'Azure App Service ?](../app-service/app-service-value-prop-what-is.md)  
   Azure Functions utilise la plateforme Azure App Service pour les fonctionnalités essentielles comme les déploiements, les variables d’environnement et les diagnostics. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO3-->


