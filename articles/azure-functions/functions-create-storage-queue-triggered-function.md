---
title: "Créer une fonction dans Azure déclenchée par des messages de file d’attente | Microsoft Docs"
description: "Utilisez Azure Functions pour créer une fonction sans serveur appelée par un message soumis à une file d’attente de stockage Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b3910ba2f95bb6f3228926a51f71f96ccd1e23
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Créer une fonction déclenchée par une file d’attente de stockage Azure

Apprenez à créer une fonction se déclenchant lorsque des messages sont envoyés à une file d’attente de stockage Azure.  

![Affichez le message dans les journaux.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

Moins de cinq minutes sont nécessaires pour effectuer toutes les étapes de cette rubrique.

## <a name="prerequisites"></a>Composants requis

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Vous devez également télécharger et installer [l’Explorateur de Stockage Microsoft Azure](http://storageexplorer.com/). 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Créer une fonction déclenchée par une file d’attente

Développez votre Function App, cliquez sur le bouton **+** à côté de **Fonctions**, puis cliquez sur le modèle **QueueTrigger** correspondant à la langue de votre choix. Ensuite, utilisez les paramètres comme indiqué dans la table et cliquez sur **Créer**.

![Créez la fonction déclenchée par la file d’attente de stockage.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)
    
| Paramètre      |  Valeur suggérée   | Description                                        |
| ------------ |  ----------------- | -------------------------------------------------- |
| **Nom de la file d’attente**   | myqueue-items    | Le nom de la file d’attente à connecter à votre compte de stockage. |
| **Connexion au compte de stockage** | AzureWebJobStorage | Vous pouvez utiliser la connexion au compte de stockage qui est déjà utilisée par votre Function App ou en créer une.  |
| **Nommer votre fonction** | Unique dans votre Function App | Nom de cette fonction déclenchée par la file d’attente. |  

Ensuite, connectez-vous à votre compte de stockage Azure et créez la file d’attente de stockage **myqueue-items**.

## <a name="create-the-queue"></a>Créer la file d’attente

1. Dans votre fonction, cliquez sur **Intégrer**, développez **Documentation** et copiez le **Nom du compte** et la **Clé du compte**. Vous utilisez ces informations d’identification pour vous connecter au compte de stockage. Si vous avez déjà connecté votre compte de stockage, passez à l’étape 4.
 
    ![Obtenez les informations d’identification de connexion au compte de stockage.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)v

2. Exécutez [l’Explorateur de stockage Microsoft Azure](http://storageexplorer.com/), cliquez sur l’icône de connexion située sur la gauche, choisissez **Utiliser un nom et une clé de compte de stockage**, puis cliquez sur **Suivant**.

    ![Exécutez l’outil Explorateur de compte de stockage.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)
    
3. Saisissez le **Nom du compte** et la **Clé du compte** récupérés à l’étape 1, puis cliquez sur **Suivant** et sur **Connexion**. 
  
    ![Entrez les informations d’identification de stockage et connectez-vous.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

4. Développez le compte de stockage attaché, cliquez avec le bouton droit sur **Files d’attente**, puis sur **Créer une file d’attente**, saisissez `myqueue-items` et appuyez sur Entrée.
 
    ![Créez une file d’attente de stockage.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Maintenant que vous disposez d’une file d’attente de stockage, vous pouvez tester la fonction en ajoutant un message à la file d’attente.  

## <a name="test-the-function"></a>Tester la fonction

1. Dans le portail Azure, accédez à votre fonction, développez les **Journaux** en bas de la page et vérifiez que la diffusion de journaux n’est pas suspendue.

2. Dans l’Explorateur de stockage, développez votre compte de stockage, **Files d’attente**, et **myqueue-items**, puis cliquez sur **Ajouter message**. 

    ![Ajoutez un message dans la file d’attente.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

2. Saisissez le message « Hello World ! » dans **Texte du message** et cliquez sur **OK**.
 
3. Attendez quelques secondes, puis retournez à vos journaux de fonction et vérifiez que le nouveau message a été lu à partir de la file d’attente. 

    ![Affichez le message dans les journaux.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

4. Dans l’Explorateur de stockage, cliquez sur **Actualiser** et vérifiez que le message a été traité et qu’il ne se trouve plus dans la file d’attente.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une fonction qui s’exécute lorsqu’un message est ajouté à une file d’attente de stockage. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Pour en savoir plus sur les déclencheurs de stockage en file d’attente, consultez la page [Liaisons de file d’attente de stockage Azure Functions](functions-bindings-storage-queue.md). 




