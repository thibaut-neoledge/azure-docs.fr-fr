---
title: "Exemple de script Azure CLI - Créer un compte Batch | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer un compte Batch"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: fd2f4682a04c557b69bbfce115f41c54a96d462c
ms.contentlocale: fr-fr
ms.lasthandoff: 09/09/2017

---

# <a name="create-a-batch-account-with-the-azure-cli"></a>Créer un compte Batch avec Azure CLI

Ce script crée un compte Azure Batch et indique de quelle manière les différentes propriétés du compte peuvent être interrogées et mises à jour.

## <a name="prerequisites"></a>Composants requis

Installez Azure CLI en suivant les instructions fournies dans le [Guide d’installation d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), si ce n’est déjà fait.

## <a name="batch-account-sample-script"></a>Exemple de script de compte Batch

Lorsque vous créez un compte Batch, ses nœuds de calcul sont par défaut attribués en interne par le service Batch. Les nœuds de calcul alloués seront soumis à un quota principal distinct, et le compte pourra être authentifié via les informations d’identification de la clé partagée ou par un jeton Azure Active Directory.

[!code-azurecli[principal](../../../cli_scripts/batch/create-account/create-account.sh "Créer un compte")]

## <a name="batch-account-using-user-subscription-sample-script"></a>Compte Batch utilisant un exemple de script d’abonnement

Vous pouvez également demande à Azure Batch de créer ses propres de calcul dans votre abonnement Azure.
Les comptes qui allouent des nœuds de calcul à votre abonnement doivent être authentifiés via un jeton Azure Active Directory ; les nœuds de calcul alloués seront comptabilités dans votre quota d’abonnement. Pour créer un compte dans ce mode, vous devez indiquer une référence Key Vault lors de cette opération.

[!code-azurecli[principal](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Créer un compte à l’aide d’un abonnement utilisateur")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois que vous avez exécuté les exemples de scripts ci-dessus, lancez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées (y compris les comptes Batch et de stockage Azure, ainsi que les coffres de clés Azure).

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un compte Batch et toutes les ressources associées. Chaque commande de la table renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_create) | Crée le compte Batch.  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_set) | Met à jour les propriétés du compte Batch.  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_show) | Récupère les détails relatifs au compte Batch spécifié.  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#az_batch_account_keys_list) | Récupère les clés d’accès du compte Batch spécifié.  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Crée un compte de stockage. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Crée un coffre de clés. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | Met à jour la stratégie de sécurité du coffre de clés spécifié. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Batch dans la [documentation relative à la CLI Azure Batch](../batch-cli-samples.md).

