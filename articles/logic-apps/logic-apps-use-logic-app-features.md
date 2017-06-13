---
title: "Ajouter une logique conditionnelle pour exécuter les workflows - Azure Logic Apps | Microsoft Docs"
description: "Contrôlez le mode d’exécution des workflows dans Azure Logic Apps en ajoutant une logique conditionnelle, des déclencheurs, des actions et des paramètres."
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9f7d623ec213de6d46f59547aff9d4417ac95ede
ms.openlocfilehash: 41aafe94d24f0e22fe2256ab213c7668b670764c
ms.contentlocale: fr-fr
ms.lasthandoff: 02/15/2017


---
# <a name="use-logic-apps-features"></a>Utiliser les fonctionnalités des applications logiques
Dans la [rubrique précédente](../logic-apps/logic-apps-create-a-logic-app.md), vous avez créé votre première application logique. Vous allez maintenant créer un processus plus complet avec Azure Logic Apps. Cette rubrique présente les nouveaux concepts Azure Logic Apps suivants :

* logique conditionnelle, qui exécute une action uniquement si une certaine condition est remplie ;
* mode code pour modifier une application logique existante ;
* options de démarrage d'un flux de travail.

Avant d’effectuer les étapes de cette rubrique, vous devez effectuer celles de la rubrique [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md). Dans le [portail Azure], accédez à votre application logique, cliquez sur **Déclencheurs et actions** dans le résumé pour modifier la définition de l’application logique.

## <a name="reference-material"></a>Documents de référence
Les documents suivants peuvent vous être utiles :

* [API REST de gestion et d’exécution](https://msdn.microsoft.com/library/azure/mt643787.aspx) - notamment comment appeler directement des applications logiques
* [Référence sur le langage](https://msdn.microsoft.com/library/azure/mt643789.aspx) - liste complète de toutes les fonctions/expressions prises en charge
* [Types de déclencheurs et d'actions](https://msdn.microsoft.com/library/azure/mt643939.aspx) - les différents types d'actions et ce qu’elles prennent en entrée
* [Vue d'ensemble d’App Service](../app-service/app-service-value-prop-what-is.md) - description des composants à choisir pour la création d’une solution

## <a name="add-conditional-logic-to-your-logic-app"></a>Ajouter la logique conditionnelle à votre application logique

Bien que le flux d’origine de votre application logique fonctionne, nous pouvions améliorer certains domaines.

### <a name="conditional"></a>Logique conditionnelle

Votre première application logique pourra causer la réception d’un trop grand nombre de messages électroniques. Les étapes suivantes ajoutent une logique conditionnelle pour que vous receviez un e-mail uniquement lorsque le tweet provient d’une personne avec un certain nombre d’abonnés.

0. Dans le concepteur d’applications logiques, choisissez **Nouvelle étape** (+) > **Ajouter une action**.
0.    Recherchez et ajoutez l’action **Obtenir l’utilisateur** pour Twitter.
0. Transmettez le champ **Tweeted by** à partir du déclencheur pour obtenir les informations relatives à l’utilisateur Twitter.

    ![Get User](media/logic-apps-use-logic-app-features/getuser.png)

0. Choisissez **Nouvelle étape** (+) > **Ajouter une condition**.
0. Pour filtrer le nombre d’abonnés qu’ont les utilisateurs, sous **Nom de l’objet**, choisissez **Ajouter du contenu dynamique**. 
0.    Dans la zone de recherche, recherchez et ajoutez le champ **Nombre d’abonnés**.
0. Sous **Relation**, sélectionnez **est supérieur à**.
0. Dans la zone **Valeur**, saisissez le nombre d’abonnés que les utilisateurs doivent avoir.

    ![Logique conditionnelle](media/logic-apps-use-logic-app-features/conditional.png)

0. Enfin, effectuez un glisser-déplacer de la **Envoi d’e-mail** vers la zone **If Yes** . 

Maintenant, vous obtenez des messages électroniques uniquement lorsque le nombre d’abonnés répond à votre condition.

## <a name="repeat-actions-over-a-list-with-foreach"></a>Répétition des actions sur une liste à l’aide de forEach

La boucle forEach spécifie un tableau sur lequel répéter une action. Le flux échouera s’il ne se présente pas sous la forme d’un tableau. Par exemple, si action1 génère un tableau de messages et que vous souhaitez envoyer chaque message, vous pouvez inclure cette instruction forEach dans les propriétés de votre action : `forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>Modification de la définition du code pour une application logique

Même si vous avez le concepteur d’applications logiques, vous pouvez modifier directement le code qui définit une application logique.

1. Dans la barre de commandes, choisissez **Mode Code**.

    Cela ouvre un éditeur complet qui affiche la définition que vous venez de modifier.

    ![Mode code](media/logic-apps-use-logic-app-features/codeview.png)

    Dans l'éditeur de texte, vous pouvez copier et coller les actions de votre choix dans la même application logique ou d'une application logique vers une autre. 
    Vous pouvez aussi ajouter ou supprimer facilement des sections entières de la définition et partager des définitions avec d'autres utilisateurs.

2. Pour enregistrer vos modifications, cliquez sur **Enregistrer**.

### <a name="parameters"></a>Paramètres

Certaines fonctionnalités de Logic Apps sont disponibles uniquement en mode code, les paramètres par exemple. Les paramètres simplifient la réutilisation des valeurs dans votre application logique. Par exemple, si vous avez une adresse de messagerie que vous souhaitez utiliser dans plusieurs actions, vous devez la définir en tant que paramètre.

Les paramètres constituent un bon moyen d'extraire des valeurs que vous êtes susceptible de modifier souvent. Ils sont particulièrement utiles quand vous devez substituer des paramètres dans différents environnements. Pour plus d’informations sur la façon de substituer des paramètres en fonction de l’environnement, consultez la [Documentation sur l’API REST](https://docs.microsoft.com/rest/api/logic).

L’exemple montre comment mettre à jour votre application logique existante pour utiliser des paramètres pour le terme de requête.

1. En mode code, recherchez l’objet `parameters : {}` et ajoutez un objet de rubrique :

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }

2. Naviguez jusqu’à l’action `twitterconnector`, recherchez la valeur de la requête et remplacez-la par `#@{parameters('topic')}`. 

    Pour joindre deux chaînes ou plus, vous pouvez également utiliser la fonction `concat`. 
    Par exemple, `@concat('#',parameters('topic'))` fonctionne de la même façon que ci-dessus.

3.    Une fois ces opérations effectuées, sélectionnez **Enregistrer**. 

    À présent, toutes les heures, les nouveaux tweets qui ont plus de cinq retweets sont envoyés dans un dossier nommé **tweets** dans votre dossier Dropbox.

Pour en savoir plus sur les définitions d'application logique, consultez la rubrique [Créer des définitions d'application logique](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Démarrage de workflows d’application logique

Vous avez plusieurs options pour démarrer le flux de travail défini dans votre application logique. Vous pouvez toujours démarrer un flux de travail à la demande dans le [portail Azure].

### <a name="recurrence-triggers"></a>Déclencheurs de périodicité

Un déclencheur de périodicité s'exécute selon un intervalle que vous spécifiez. Quand le déclencheur a une logique conditionnelle, il détermine si le flux de travail doit s'exécuter ou non. Un déclencheur indique que le workflow doit s’exécuter en retournant un code d’état `200` . Lorsque le workflow n’a pas besoin de s’exécuter, le déclencheur renvoie un code d’état `202`.

### <a name="callback-using-rest-apis"></a>Rappel à l'aide des API REST

Pour démarrer un workflow, les services peuvent appeler un point de terminaison d'application logique. Pour démarrer ce type d’application logique à la demande, choisissez **Exécuter maintenant** dans la barre de commandes. Consultez la page [Démarrage des flux de travail en appelant les points de terminaison en tant que déclencheurs](../logic-apps/logic-apps-http-endpoint.md). 

<!-- Shared links -->
[portail Azure]: https://portal.azure.com

