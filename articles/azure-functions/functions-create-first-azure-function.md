---
title: "Créer votre première fonction Azure | Microsoft Docs"
description: "Générez votre première fonction Azure, une application sans serveur, en moins de deux minutes."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a797910e286cd2aacf5a8aa6c509e2b0f5f60276
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-azure-function"></a>Créer votre première fonction Azure

Cette rubrique vous montre comment utiliser le démarrage rapide d’Azure Functions dans le portail pour créer une fonction « Hello World » simple, appelée par une requête HTTP. Pour en savoir plus sur Azure Functions, consultez [Vue d’ensemble d’Azure Functions](functions-overview.md).

Avant de commencer, il vous faut un compte Azure. Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/). Vous pouvez également [essayer Azure Functions](https://azure.microsoft.com/try/app-service/functions/) sans vous inscrire auprès d’Azure.

## <a name="create-a-function-from-the-portal-quickstart"></a>Créer une fonction à l’aide du démarrage rapide dans le portail

1. Accédez au [Portail Azure Functions](https://functions.azure.com/signin) et connectez-vous avec votre compte Azure. 
 
2. Dans **Nom**, indiquez le nom unique de votre nouvelle fonction ou acceptez le nom généré automatiquement, sélectionnez votre **Région**, puis cliquez sur **Créer + commencer**. Le nom ne peut contenir que des lettres, des chiffres et des traits d’union. Le trait de soulignement (**_**) n’est pas un caractère autorisé.

3. Dans l’onglet **Démarrage rapide**, cliquez sur **WebHook + API** et choisissez la langue de votre fonction, puis cliquez sur **Créer une fonction**. Une fonction prédéfinie est créée dans la langue que vous avez choisie. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (Facultatif) À ce stade du démarrage rapide, vous pouvez suivre une visite rapide des fonctionnalités Azure Functions dans le portail. Après avoir terminé ou ignoré la visite, vous pouvez tester votre nouvelle fonction en envoyant une requête HTTP.

## <a name="test-the-function"></a>Tester la fonction
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="watch-the-video"></a>Regarder la vidéo
La vidéo suivante montre comment effectuer les étapes de base de ce didacticiel. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 


## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


