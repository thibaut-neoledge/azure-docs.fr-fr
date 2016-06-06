<properties
   pageTitle="Utilisation d’Azure Functions avec les applications logiques | Microsoft Azure"
   description="Découvrez comment utiliser Azure Functions avec les applications logiques"
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# Utilisation d’Azure Functions avec les applications logiques

Vous pouvez exécuter des extraits de code personnalisés de C# ou Node.js en tirant parti d’Azure Functions depuis une application logique. [Azure Functions](../azure-functions/functions-overview.md) est une offre qui permet d’effectuer des calculs sans serveur dans Microsoft Azure. Cette solution est utile dans les applications logiques dans la plupart des scénarios suivants :

* Mettre en forme une valeur d’une action (par exemple, convertir une valeur DateTime en chaîne de date)
* Effectuer des calculs dans un flux de travail
* Étendre des fonctionnalités des applications logiques avec des fonctions prises en charge dans C# ou Node.js

## Création d’une fonction Azure pour les applications logiques

Nous vous recommandons de créer une fonction Azure dans le portail de fonctions en utilisant les modèles « Generic Node Webhook » (Webhook de nœud générique) ou « Generic C# Webhook » (Webhook C# générique). Cette opération permet de remplir automatiquement un modèle qui accepte `application/json` depuis une application logique, et les fonctions qui utilisent ces modèles sont automatiquement détectées et répertoriées dans le concepteur d’applications logiques sous « Azure Functions in my region » (Fonctions Azure dans ma région).

Les fonctions de webhook acceptent une demande et la passent à la méthode par le biais d’une variable `data`. Vous pouvez accéder aux propriétés de votre charge utile à l’aide d’une notation par points telle que `data.foo`. Par exemple, une fonction javascript simple qui convertit une valeur DateTime en une chaîne de date se présente comme suit :

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## Appel de fonctions Azure depuis une application logique

Dans le concepteur, si vous cliquez sur le menu déroulant des actions, vous pouvez sélectionner « Azure Functions in my region » (Fonctions Azure dans ma région). Vous obtenez ainsi la liste des conteneurs dans votre abonnement et pouvez choisir la fonction à appeler. Une fois votre choix effectué, vous êtes invité à spécifier un objet de charge utile d’entrée. Celui-ci représente le message qu’envoie l’application logique à la fonction et doit être un objet JSON. Par exemple, pour passer la date de dernière modification à partir d’un déclencheur Salesforce, la charge utile de la fonction peut ressembler à ceci :

![][1]

## Déclenchement des applications logiques à partir d’une fonction d’Azure

Vous pouvez également déclencher une application logique à partir d’une fonction. Pour ce faire, créez simplement une application logique avec un déclencheur manuel (cliquez [ici](app-service-logic-http-endpoint.md) pour plus de détails). Ensuite, dans votre fonction Azure, générez une requête HTTP POST vers l’URL du déclencheur manuel avec la charge utile que vous souhaitez envoyer à l’application logique.

### Création d’une fonction à partir du concepteur

Vous pouvez également créer une fonction de webhook node.js à partir du concepteur. Tout d’abord, sélectionnez « Azure Functions in my Region » (Fonctions Azure dans ma région) et choisissez un conteneur pour votre fonction. Si vous n’avez pas encore de conteneur, vous devez en créer un à partir du [portail Azure Functions](https://functions.azure.com/signin). Vous pouvez ensuite sélectionner « Créer ». Pour générer un modèle basé sur les données que vous souhaitez calculer, spécifiez l’objet de contexte que vous envisagez de passer à une fonction. Ce doit être un objet JSON. Par exemple, si je passe le contenu de fichier à partir d’une action FTP, ma charge utile de contexte ressemblerait à ceci :

![][2]

>[AZURE.NOTE] Cet objet n’ayant pas été transformé en chaîne au moyen de guillemets, le contenu est ajouté directement à la charge utile JSON. Cette opération provoque une erreur s’il ne s’agit pas d’un jeton JSON (par exemple, une chaîne ou un objet/tableau JSON). Pour effectuer le transtypage en chaîne, ajoutez simplement des guillemets comme dans l’exemple Salesforce ci-dessus.

Le concepteur génère ensuite un modèle de fonction que vous pouvez créer sous forme inline. Les variables sont créées préalablement en fonction du contexte que vous envisagez de passer à la fonction.

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

<!---HONumber=AcomDC_0525_2016-->