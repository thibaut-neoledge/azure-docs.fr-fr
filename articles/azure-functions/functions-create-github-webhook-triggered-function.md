---
title: "Création d’une fonction dans Azure déclenchée par un webhook GitHub | Microsoft Docs"
description: "Utilisez Azure Functions pour créer une fonction sans serveur appelée par un webhook GitHub."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: d79ce0e047e71d9f6af7ca55f55bea405c280b1d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Créer une fonction déclenchée par un webhook GitHub

Apprenez à créer une fonction qui est déclenchée par une demande webhook HTTP avec une charge utile GitHub.

![Fonction webhook GitHub déclenchée dans le Portail Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Composants requis

Avant d’exécuter cet exemple, vous devez disposer des éléments suivants :

- Un compte GitHub avec au moins un projet.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App créée avec succès.](./media/functions-create-first-azure-function/function-app-create-success.png)

Créez ensuite une fonction dans la nouvelle Function App.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>Créer une fonction de déclenchement de webhook GitHub

1. Développez votre Function App, cliquez sur le bouton **+** à côté de **Fonctions**, puis cliquez sur le modèle **GitHubWebHook** pour la langue de votre choix. **Nommez votre fonction**, puis cliquez sur **Créer**.

1. Dans votre nouvelle fonction, cliquez sur **</> Obtenir l’URL de fonction**, puis copiez et enregistrez les valeurs. Faite la même chose pour **</> Obtenir le secret GitHub**. Vous avez besoin de ces valeurs pour configurer le webhook dans GitHub.

    ![Examiner le code de fonction](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

Ensuite, vous créez le webhook dans votre référentiel GitHub.

## <a name="configure-the-webhook"></a>Configurer le webhook

1. Dans GitHub, accédez à un référentiel que vous possédez. Vous pouvez également utiliser l’un des référentiels que vous avez dupliqués. Si vous avez besoin de dupliquer un référentiel, utilisez <https://github.com/Azure-Samples/functions-quickstart>.

1. Cliquez sur **Paramètres**, puis sur **Webhooks** et **Ajouter un webhook**.

    ![Ajouter un webhook GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Utilisez les paramètres spécifiés dans la table, puis cliquez sur **Ajouter un webhook**.

    ![Définir l’URL et le secret du webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Paramètre | Valeur suggérée | Description |
|---|---|---|
| **URL de charge utile** | Valeur copiée | Utilisez la valeur retournée par **<>/ Obtenir une fonction URL**. |
| **Secret**   | Valeur copiée | Utilisez la valeur retournée par **<>/ Obtenir un secret GitHub**. |
| **Type de contenu** | application/json | La fonction attend une charge utile JSON. |
| Déclencheurs d’événement | Je vais sélectionner les événements individuels | Nous voulons seulement déclencher des événements de problème sous forme de commentaire.  |
| | Problème sous forme de commentaire |  |

À ce stade, le webhook est configuré pour déclencher la fonction au moment de l’ajout d’un nouveau problème sous forme de commentaire.

## <a name="test-the-function"></a>Tester la fonction

1. Dans votre référentiel GitHub, ouvrez l’onglet **Problèmes** dans une nouvelle fenêtre de navigateur.

1. Dans la nouvelle fenêtre, cliquez sur **Nouveau problème**, saisissez un titre, puis cliquez sur **Envoyer nouveau problème**.

1. Dans le problème, entrez un commentaire et cliquez sur **Commentaire**.

    ![Ajoutez un problème GitHub sous forme de commentaire.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. Revenez au portail et afficher les journaux. Vous devez voir une entrée de suivi avec le nouveau commentaire textuel.

     ![Afficher le commentaire textuel dans les journaux.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une fonction qui s’exécute lorsqu’une requête est reçue à partir d’un webhook GitHub. 
[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]Pour en savoir plus sur les déclencheurs webhook, consultez la page [Liaisons HTTP et webhook d’Azure Functions](functions-bindings-http-webhook.md).
