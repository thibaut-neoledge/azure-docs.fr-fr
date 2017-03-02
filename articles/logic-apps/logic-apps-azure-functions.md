---
title: "Ajout de code personnalisé aux applications logiques avec Azure Functions | Microsoft Docs"
description: "Création de code personnalisé pour les applications logiques Azure avec Azure Functions"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
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
ms.sourcegitcommit: c63dde728eaaf8237970e05cc524c6220b69a074
ms.openlocfilehash: 8b68f017a2c7a17603508438b0d4bd760bec4f78
ms.lasthandoff: 02/15/2017


---
# <a name="add-custom-code-to-azure-logic-apps-with-azure-functions"></a>Ajout de code personnalisé aux applications logiques Azure avec Azure Functions

Vous pouvez exécuter des extraits de code personnalisés de C# ou node.js en utilisant Azure Functions dans les applications logiques. 
[Azure Functions](../azure-functions/functions-overview.md) assure le calcul sans serveur dans Microsoft Azure, et est utile pour effectuer ces tâches :

* Mise en forme avancée ou calcul de champs dans les applications logiques
* Effectuez les calculs dans un workflow.
* Extension des fonctionnalités des applications logiques avec des fonctions prises en charge dans C# ou node.js

## <a name="create-functions-for-logic-apps"></a>Création de fonctions pour les applications logiques

Nous vous recommandons de créer une fonction dans le portail Azure Functions à partir des modèles **Generic Webhook - Node (Webhook générique - Nœud)** ou **Generic Webhook - C# (Webhook générique - C#)**. Ce résultat crée automatiquement un modèle qui accepte `application/json` à partir d’une application logique. Les fonctions que vous créez à partir de ces modèles sont automatiquement détectées et s’affichent dans le concepteur d’applications logiques sous **Azure Functions in my region**

Dans le portail Azure, dans le volet **Intégrer** de votre fonction, votre modèle doit montrer que **Mode** a la valeur **Webhook** et que **Type de webhook** est défini sur **JSON générique**. 

Les fonctions de webhook acceptent une demande et la passent à la méthode par le biais d’une variable `data` . Vous pouvez accéder aux propriétés de votre charge utile à l’aide d’une notation par points telle que `data.foo`. Par exemple, une fonction JavaScript simple qui convertit une valeur DateTime en une chaîne de date se présente comme suit :

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>Appeler Azure Functions à partir d’applications logiques

Pour répertorier les conteneurs dans votre abonnement et sélectionner la fonction que vous souhaitez appeler, dans le concepteur d’applications logiques, cliquez sur le menu **Actions**, puis choisissez dans **Azure Functions in my Region (Fonctions Azure dans ma région)**.

Après avoir sélectionné la fonction, il vous est demandé de spécifier un objet de charge utile d’entrée. Cet objet représente le message qu’envoie l’application logique à la fonction et doit être un objet JSON. Par exemple, pour passer la **date de dernière modification** à partir d’un déclencheur Salesforce, la charge utile de la fonction peut ressembler à cet exemple :

![Date de dernière modification][1]

## <a name="trigger-logic-apps-from-a-function"></a>Déclencher des applications logiques à partir d’une fonction

Vous pouvez déclencher une application logique dans une fonction. Consultez [Applications logiques en tant que points de terminaison pouvant être appelés](logic-apps-http-endpoint.md). Créez une application logique disposant d’un déclencheur manuel, puis, dans votre fonction, générez une requête HTTP POST vers l’URL du déclencheur manuel avec la charge utile que vous souhaitez envoyer à l’application logique.

### <a name="create-a-function-from-logic-app-designer"></a>Création d’une fonction à partir du concepteur d’applications logiques

Vous pouvez également créer une fonction de webhook node.js depuis le concepteur. Tout d’abord, sélectionnez **Azure Functions in my Region** (Azure Functions dans ma région) et choisissez un conteneur pour votre fonction. Si vous n’avez pas encore de conteneur, vous devez en créer un à partir du [portail Azure Functions](https://functions.azure.com/signin). Sélectionnez ensuite **Créer**.  

Pour générer un modèle basé sur les données que vous souhaitez calculer, spécifiez l’objet de contexte que vous envisagez de passer à une fonction. Cet objet doit être un objet JSON. Par exemple, si vous passez le contenu de fichier à partir d’une action FTP, la charge utile de contexte ressemblera à cet exemple :

![Charge utile de contexte][2]

> [!NOTE]
> Cet objet n’ayant pas été transformé en chaîne, le contenu est ajouté directement à la charge utile JSON. Cependant, cette opération provoque une erreur si l’objet n’est pas d’un jeton JSON (par exemple, une chaîne ou un objet/tableau JSON). Pour transformer cet objet en chaîne, ajoutez des guillemets comme indiqué dans la première illustration de cet article.
> 

Le concepteur génère ensuite un modèle de fonction que vous pouvez créer sous forme inline. Les variables sont créées préalablement en fonction du contexte que vous envisagez de passer à la fonction.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png

