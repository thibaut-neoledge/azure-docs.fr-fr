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
ms.date: 02/09/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 7743bfb98552f7fa2334d8daa6a6f6935969f393
ms.openlocfilehash: d76630e0be4a021720d88e6d7e64cf2258f84266


---
# <a name="create-your-first-azure-function"></a>Créer votre première fonction Azure
## <a name="overview"></a>Vue d'ensemble
Azure Functions est une expérience de calcul à la demande pilotée par les événements, qui étend la plateforme d’application Azure existante avec des capacités d’implémenter du code déclenché par des événements se produisant dans d’autres services Azure, dans les produits SaaS et dans les systèmes locaux. Avec Azure Functions, vos applications sont mises à l’échelle à la demande, et vous payez uniquement pour les ressources que vous utilisez. Azure Functions vous permet de créer des unités de code planifiées ou déclenchées, et de les implémenter dans divers langages de programmation. Pour en savoir plus sur Azure Functions, consultez [Vue d’ensemble d’Azure Functions](functions-overview.md).

Cette rubrique vous montre comment utiliser le démarrage rapide d’Azure Functions dans le portail pour créer une fonction JavaScript « Hello World » simple, appelée par un déclencheur HTTP. Vous pouvez également regarder une courte vidéo décrivant comment effectuer ces étapes dans le portail.

## <a name="watch-the-video"></a>Regarder la vidéo
La vidéo suivante montre comment effectuer les étapes de base de ce didacticiel. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Créer une fonction à l’aide du démarrage rapide
Une application de fonction héberge l’exécution de vos fonctions dans Azure. Pour créer une application de fonction avec la nouvelle fonction, procédez comme suit. L’application de fonction est créée avec une configuration par défaut. Pour obtenir un exemple explicite de la manière de créer votre application de fonction, consultez [l’autre didacticiel de démarrage rapide Azure Functions](functions-create-first-azure-function-azure-portal.md).

Pour créer votre première fonction, vous devez avoir un compte Azure actif. Si tel n’est pas le cas, des [comptes gratuits sont disponibles](https://azure.microsoft.com/free/).

1. Accédez au [Portail Azure Functions](https://functions.azure.com/signin) et connectez-vous avec votre compte Azure.
2. Dans **Nom**, indiquez le nom unique de votre nouvelle fonction ou acceptez le nom généré automatiquement, sélectionnez votre **Région**, puis cliquez sur **Créer + commencer**. Notez que vous devez entrer un nom valide, qui peut seulement contenir des lettres, des chiffres et des traits d’union. Le trait de soulignement (**_**) n’est pas un caractère autorisé.
3. Dans l’onglet **Démarrage rapide**, cliquez sur **WebHook + API** et **JavaScript**, puis cliquez sur **Créer une fonction**. Une fonction JavaScript prédéfinie est créée. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Facultatif) À ce stade du démarrage rapide, vous pouvez suivre une visite rapide des fonctionnalités Azure Functions dans le portail. Après avoir terminé ou ignoré la visite, vous pouvez tester votre nouvelle fonction à l’aide du déclencheur HTTP.

## <a name="test-the-function"></a>Tester la fonction
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO2-->


