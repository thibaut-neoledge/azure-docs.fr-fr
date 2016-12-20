---
title: "Utilisation d’Azure Functions avec les applications logiques | Microsoft Docs"
description: "Découvrez comment utiliser Azure Functions avec les applications logiques"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 37e246ddb362c3b19db988224a5e6df4e41bd1e7


---
# <a name="using-azure-functions-with-logic-apps"></a>Utilisation d’Azure Functions avec les applications logiques
Vous pouvez exécuter des extraits de code personnalisés de C# ou node.js en utilisant Azure Functions à partir d’une application logique.  [Azure Functions](../azure-functions/functions-overview.md) assure le calcul sans serveur dans Microsoft Azure. Cela s’avère particulièrement utile pour effectuer les tâches suivantes :

* Mise en forme avancée ou calcul de champs dans une application logique
* Effectuer des calculs dans un flux de travail
* Étendre les fonctionnalités de Logic Apps avec des fonctions prises en charge dans C# ou node.js

## <a name="create-a-function-for-logic-apps"></a>Créer une fonction pour Logic Apps
Nous vous recommandons de créer une fonction dans le portail Azure Functions en utilisant les modèles **Generic Webhook - Node (Webhook générique - Nœud)** ou **Generic Webhook - C# (Webhook générique - C#)**. Cela remplit automatiquement un modèle qui accepte `application/json` à partir d’une application logique.  Si vous sélectionnez l’onglet **Intégration** dans Azure Functions, la valeur **Mode** doit être définie sur **Webhook** et le **type Webhook** doit être **JSON générique**.  Les fonctions qui utilisent ces modèles sont automatiquement détectées et répertoriées dans le concepteur Logic Apps sous **Azure Functions in my region**

Les fonctions de webhook acceptent une demande et la passent à la méthode par le biais d’une variable `data` . Vous pouvez accéder aux propriétés de votre charge utile à l’aide d’une notation par points telle que `data.foo`.  Par exemple, une fonction JavaScript simple qui convertit une valeur DateTime en une chaîne de date se présente comme suit :

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>Appeler Azure Functions à partir d’une application logique
Dans le concepteur, si vous cliquez sur le menu **Actions**, vous pouvez sélectionner **Azure Functions in my region (Azure Functions dans ma région)**.  Vous obtenez ainsi la liste des conteneurs dans votre abonnement et pouvez choisir la fonction à appeler.  

Après avoir sélectionné la fonction, vous êtes invité à spécifier un objet de charge utile d’entrée. Celui-ci représente le message qu’envoie l’application logique à la fonction et doit être un objet JSON. Par exemple, pour passer la **date de dernière modification** à partir d’un déclencheur Salesforce, la charge utile de la fonction peut ressembler à ceci :

![Date de dernière modification][1]

## <a name="trigger-logic-apps-from-a-function"></a>Déclencher des applications logiques à partir d’une fonction
Vous pouvez également déclencher une application logique à partir d’une fonction.  Pour ce faire, créez simplement une application logique avec un déclencheur manuel. Pour plus d’informations, consultez la page [Applications logiques en tant que points de terminaison pouvant être appelés](app-service-logic-http-endpoint.md).  Ensuite, dans votre fonction, générez une requête HTTP POST vers l’URL du déclencheur manuel avec la charge utile que vous souhaitez envoyer à l’application logique.

### <a name="create-a-function-from-the-designer"></a>Créer une fonction à partir du concepteur
Vous pouvez également créer une fonction de webhook node.js à partir du concepteur. Tout d’abord, sélectionnez **Azure Functions in my Region** (Azure Functions dans ma région) et choisissez un conteneur pour votre fonction.  Si vous n’avez pas encore de conteneur, vous devez en créer un à partir du [portail Azure Functions](https://functions.azure.com/signin). Sélectionnez ensuite **Créer**.  

Pour générer un modèle basé sur les données que vous souhaitez calculer, spécifiez l’objet de contexte que vous envisagez de passer à une fonction. Ce doit être un objet JSON. Par exemple, si vous passez le contenu de fichier à partir d’une action FTP, la charge utile de contexte ressemblera à ceci :

![Charge utile de contexte][2]

> [!NOTE]
> Cet objet n’ayant pas été transformé en chaîne, le contenu est ajouté directement à la charge utile JSON. Cependant, cette opération provoque une erreur s’il ne s’agit pas d’un jeton JSON (par exemple, une chaîne ou un objet/tableau JSON). Pour le transformer en chaîne, ajoutez simplement des guillemets comme indiqué dans la première illustration de cet article.
> 
> 

Le concepteur génère ensuite un modèle de fonction que vous pouvez créer sous forme inline. Les variables sont créées préalablement en fonction du contexte que vous envisagez de passer à la fonction.

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png



<!--HONumber=Nov16_HO3-->


