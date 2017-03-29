---
title: "Créer votre première fonction à l’aide du Portail Azure | Microsoft Docs"
description: "Bienvenue dans Azure. Créez votre première fonction Azure à l’aide du Portail Azure."
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
ms.date: 03/15/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 6bb15dfd0e2c40b99d06ef2d695e3ba7ca78e91b
ms.lasthandoff: 03/22/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Créer votre première fonction à l’aide du Portail Azure

Cette rubrique vous explique comment créer une fonction Azure « Hello World » simple, appelée par une requête HTTP. Avant de pouvoir créer une fonction dans le Portail Azure, vous devez créer une Function App dans Azure App Service pour héberger l’exécution de votre fonction.

Pour suivre cette procédure de démarrage rapide, vous avez besoin d’un compte Azure. Vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/). Vous pouvez également [essayer Azure Functions](https://azure.microsoft.com/try/app-service/functions/) sans vous inscrire auprès d’Azure.

## <a name="create-a-function-app"></a>Créer une Function App

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Pour plus d’informations, consultez [Créer une Function App à l’aide du Portail Azure](functions-create-function-app-portal.md).

## <a name="create-a-function"></a>Créer une fonction
Ces étapes permettent de créer une fonction dans la nouvelle Function App à l’aide du démarrage rapide Azure Functions.

1. Dans l’onglet **Démarrage rapide**, cliquez sur **WebHook + API**, choisissez le langage de votre fonction, puis cliquez sur **Créer une fonction**. Une fonction prédéfinie est créée dans le langage que vous avez choisi.  
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

4. (Facultatif) À ce stade du démarrage rapide, vous pouvez suivre une visite rapide des fonctionnalités Azure Functions dans le portail. Après avoir terminé ou ignoré la visite, vous pouvez tester votre nouvelle fonction en envoyant une requête HTTP.


## <a name="test-the-function"></a>Tester la fonction
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


