---
title: Comment cibler des versions du runtime Azure Functions | Microsoft Docs
description: "Azure Functions prend en charge plusieurs versions du runtime. Découvrez comment spécifier la version du runtime d’une application de fonction hébergée sur Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Comment cibler des versions du runtime Azure Functions

Le runtime Azure Functions implémente l’exécution sans serveur de votre code dans Azure. Vous trouverez ce runtime dans différents environnements, autres que ceux qu’héberge Azure. L’outil [Azure Functions Core Tools](functions-run-local.md) implémente ce runtime sur votre ordinateur de développement. Le [Runtime Azure Functions](functions-runtime-overview.md) vous permet d’utiliser Azure Functions dans des environnements locaux. 

Azure Functions prend en charge plusieurs versions majeures du runtime. Une mise à jour de la version majeure peut introduire de nouvelles modifications. Cette rubrique explique comment cibler vos applications de fonction vers une version de runtime spécifique lorsqu’elles sont hébergées dans Azure. 

Azure Functions vous permet de cibler une version majeure du runtime en utilisant le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` dans votre application de fonction. Ceci s’applique aux versions publiques et privées. Votre application de fonction est conservée sur la version majeure du runtime spécifiée jusqu’à ce que vous décidiez de changer de version. L’application de fonction est mise à jour avec de nouvelles versions mineures du runtime lorsque celles-ci sont disponibles. Les mises à jour de versions mineures peuvent introduire de nouvelles modifications.  

Lorsqu’une nouvelle version majeure est disponible publiquement, vous avez la possibilité de passer à cette version lorsque vous consultez votre application de fonction sur le portail. Une fois que vous avez changé de version, vous avez toujours la possibilité d’utiliser le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` pour repasser à la version précédente du runtime.

Votre application de fonction redémarre à chaque changement de version du runtime. Les notes de publication de toutes les versions du runtime (majeures comme mineures) sont publiées dans le [référentiel GitHub](https://github.com/Azure/azure-webjobs-sdk-script/releases).   
## <a name="view-the-current-runtime-version"></a>Afficher la version actuelle du runtime

Utilisez les instructions suivantes pour afficher la version du runtime qu’utilise actuellement votre application de fonction. 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre application de fonction, et sous **Fonctionnalités configurées**, choisissez **Paramètres de l’application de fonction**. 

    ![Sélectionnez Paramètres de l’application de fonction](./media/functions-versions/add-update-app-setting.png)

2. Dans l’onglet **Paramètres de l’application de fonction**, trouvez la **Version du runtime**. Prenez note de la version spécifique du runtime ainsi que de la version majeure souhaitée. Dans l’exemple ci-dessous, la version majeure est définie sur `~1.0`.
 
   ![Sélectionnez Paramètres de l’application de fonction](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>Cibler le runtime d’Azure Functions version 2.0

>[!IMPORTANT]   
> Le runtime d’Azure Functions 2.0 est en préversion ; pour le moment, les fonctionnalités d’Azure Functions ne sont pas toutes prises en charge. Pour plus d’informations, consultez l’article [Azure Functions runtime 2.0 known issues](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues) (Problèmes connus du runtime d’Azure Functions 2.0).  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Vous pouvez passer votre application de fonction à la préversion du runtime 2.0 dans le portail Azure. Dans l’onglet **Paramètres de l’application de fonction**, sélectionnez **Beta** sous **Version du runtime**.  

   ![Sélectionnez Paramètres de l’application de fonction](./media/functions-versions/function-app-view-version.png)

Ce paramètre revient à définir le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` sur `beta`. Choisissez le bouton **~1** pour repasser à la version majeure actuelle prise en charge publiquement. Vous pouvez aussi utiliser Azure CLI pour mettre à jour ce paramètre d’application. 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>Cibler une version spécifique du runtime à partir du portail

Lorsque vous avez besoin de cibler une version majeure (autre que la version majeure actuelle ou la version 2.0), vous devez définir le paramètre d’application `FUNCTIONS_EXTENSION_VERSION`.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre application de fonction, et sous **Fonctionnalités configurées**, choisissez **Paramètres d’application**.

    ![Sélectionnez Paramètres de l’application de fonction](./media/functions-versions/add-update-app-setting1a.png)

2. Dans l’onglet **Paramètres d’application**, trouvez le paramètre `FUNCTIONS_EXTENSION_VERSION` puis remplacez la valeur par une version majeure valide du runtime 1.x ou `beta` pour la version 2.0. 

    ![Définir le paramètre de l’application de fonction](./media/functions-versions/add-update-app-setting2.png)

3. Cliquez sur **Enregistrer** pour enregistrer la mise à jour du paramètre d’application. 

## <a name="target-a-specific-version-using-azure-cli"></a>Cibler une version spécifique à l’aide d’Azure CLI

 Vous pouvez aussi définir l’élément `FUNCTIONS_EXTENSION_VERSION` à partir d’Azure CLI. À l’aide d’Azure CLI, mettez à jour ce paramètre d’application dans l’application de fonction, à l’aide de la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
Dans ce code, remplacez `<function_app>` par le nom de votre application de fonction. Remplacez également `<my_resource_group>` par le nom du groupe de ressources de votre application de fonction. Remplacez `<version>` par une version valide du runtime 1.x ou `beta` pour la version 2.0. 

Vous pouvez exécuter cette commande à partir de [Azure Cloud Shell](../cloud-shell/overview.md) en choisissant **Essayer** dans l’exemple de code qui précède. Vous pouvez également utiliser [Azure CLI en local](/cli/azure/install-azure-cli) pour exécuter cette commande après avoir lancé la commande [az login](/cli/azure#az_login) pour vous connecter.
