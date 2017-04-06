---
title: "Exemple de script Azure CLI - Ajout d’une application dans Batch | Microsoft Docs"
description: "Exemple de script Azure CLI - Ajout d’une application dans Batch"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 342cc2c93304f7a8f651769139e28ebf2e30d412
ms.lasthandoff: 03/24/2017

---

# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Ajout d’applications dans Azure Batch avec Azure CLI

Ce script explique comment configurer une application à utiliser avec une tâche ou un pool Azure Batch. Pour configurer cette application, placez votre fichier exécutable et toutes ses éventuelles dépendances dans un fichier .zip. Dans cet exemple, le fichier .zip est appelé « my-application-exe.zip ».
L’exécution de ce script suppose qu’un compte Batch a déjà été configuré. Pour en savoir plus, consultez [l’exemple de script de création d’un compte Batch](./batch-cli-sample-create-account.md).

Si nécessaire, installez l’interface Azure CLI en suivant les instructions du [Guide d’installation Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), puis exécutez `az login` pour vous connecter à Azure.

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[principal](../../../cli_scripts/batch/add-application/add-application.sh "Ajouter une application")]

## <a name="clean-up-application"></a>Supprimer l’application

Une fois l’exemple de script ci-dessus exécuté, lancez les commandes suivantes pour supprimer l’application et tous les packages associés qui ont été chargés.

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer une application et charger un package d’application.
Chaque commande de la table renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#create) | Crée une application.  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#set) | Met à jour les propriétés d’une application.  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#create) | Ajoute un package d’application à l’application spécifiée.  |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Batch dans la [documentation relative à la CLI Azure Batch](../batch-cli-samples.md).

