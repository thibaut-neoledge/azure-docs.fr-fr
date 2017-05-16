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
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Créer une fonction déclenchée par un webhook GitHub

Apprenez à créer une fonction déclenchée par un webhook GitHub. 

![Créer une Function App dans le Portail Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

Cette rubrique requiert les ressources créées dans la rubrique [Créer votre première fonction à partir du portail Azure](functions-create-first-azure-function.md).

Vous avez également besoin d’un compte GitHub. Si vous ne possédez pas encore de compte GitHub, vous pouvez [vous inscrire pour en obtenir un gratuitement](https://github.com/join). 

Moins de cinq minutes sont nécessaires pour effectuer toutes les étapes de cette rubrique.

## <a name="find-your-function-app"></a>Rechercher votre Function App    

1. Connectez-vous au [portail Azure](https://portal.azure.com/). 

2. Dans la barre de recherche en haut du portail, tapez le nom de votre Function App et sélectionnez-la dans la liste.

## <a name="create-function"></a>Créer une fonction déclenchée par un webhook GitHub

1. Dans votre Function App, cliquez sur le bouton  **+**  à côté de **Fonctions**, puis cliquez sur le modèle **GitHubWebHook** pour la langue de votre choix et sur **Créer**.
   
    ![Créez une fonction déclenchée par un webhook GitHub dans le portail Azure.](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Cliquez sur **</> Obtenir l’URL de fonction**, puis copiez et enregistrez les valeurs. Faite la même chose pour **</> Obtenir le secret GitHub**. Vous avez besoin de ces valeurs pour configurer le webhook dans GitHub. 

    ![Examiner le code de fonction](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
Ensuite, vous créez le webhook dans votre référentiel GitHub. 

## <a name="configure-the-webhook"></a>Configurer le webhook
1. Dans GitHub, accédez à un référentiel que vous possédez. Vous pouvez également utiliser l’un des référentiels que vous avez dupliqués.
 
2. Cliquez sur **Paramètres**, puis sur **Webhooks** et **Ajouter un webhook**.
   
    ![Ajouter un webhook GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. Collez l’URL de votre fonction et le secret dans **Payload URL** (URL de la charge utile) et **Secret** puis sélectionnez **application/json** pour **Type de contenu**.

4. Cliquez sur **Let me select individual events** (Me laisser choisir des événements individuels), sélectionnez **Commentaire sur le problème**, puis cliquez sur **Ajouter webhook**.
   
    ![Définir l’URL et le secret du webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

À ce stade, le webhook est configuré pour déclencher la fonction au moment de l’ajout d’un nouveau problème sous forme de commentaire. 

## <a name="test-the-function"></a>Tester la fonction
1. Dans votre référentiel GitHub, ouvrez l’onglet **Problèmes** dans une nouvelle fenêtre de navigateur.

2. Dans la nouvelle fenêtre, cliquez sur **Nouveau problème**, saisissez un titre, puis cliquez sur **Submit new issue** (Envoyer nouveau problème). 

2. Dans le problème, entrez un commentaire et cliquez sur **Commentaire**. 

3. Dans l’autre fenêtre de GitHub, cliquez sur **Modifier** à côté de votre nouveau webhook, faites défiler jusqu’à **Recent Deliveries** (Dernières livraisons), puis vérifiez qu’une demande de webhook a été traitée par votre fonction. 
 
    ![Définir l’URL et le secret du webhook](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   La réponse à partir de votre fonction doit contenir `New GitHub comment: <Your issue comment text>`.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


