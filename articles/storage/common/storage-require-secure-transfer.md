---
title: "Exiger un transfert sécurisé dans le stockage Azure | Microsoft Docs"
description: "Découvrez la fonctionnalité Transfert sécurisé requis pour Stockage Azure et apprenez à l’activer."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 5ec50ca23d9f7c92365492dfab42dc14a38699e2
ms.contentlocale: fr-fr
ms.lasthandoff: 09/28/2017

---
# <a name="require-secure-transfer-in-azure-storage"></a>Exiger un transfert sécurisé dans Stockage Azure

L’option Transfert sécurisé requis améliore la sécurité de votre compte de stockage en autorisant uniquement les requêtes relatives au compte provenant de connexions sécurisées. Par exemple, lorsque vous appelez des API REST pour accéder à votre compte de stockage, vous devez vous connecter à l’aide du protocole HTTPS. La fonctionnalité « Transfert sécurisé requis » rejette les requêtes qui utilisent le protocole HTTP.

Lorsque vous utilisez le service Azure Files, toute connexion sans chiffrement échoue si l’option Transfert sécurisé requis est activée. Cela inclut des scénarios utilisant SMB 2.1, SMB 3.0 sans chiffrement et certaines versions du client SMB Linux. 

Par défaut, l’option Transfert sécurisé requis est désactivée.

> [!NOTE]
> Étant donné que Stockage Azure ne prend pas en charge le protocole HTTPS pour les noms de domaine personnalisés, cette option n’est pas appliquée lorsque vous utilisez un nom de domaine personnalisé.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Activer l’option Transfert sécurisé requis dans le portail Azure

Vous pouvez activer le paramètre Transfert sécurisé requis lorsque vous créez un compte de stockage dans le [portail Azure](https://portal.azure.com). Vous pouvez également l’activer pour les comptes de stockage existants.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Exiger un transfert sécurisé pour un nouveau compte de stockage

1. Ouvrez le volet **Créer un compte de stockage** dans le portail Azure.
1. Sous **Transfert sécurisé requis**, sélectionnez **Activé**.

  ![Panneau Créer un compte de stockage](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Exiger un transfert sécurisé pour un compte de stockage existant

1. Sélectionnez un compte de stockage existant dans le portail Azure.
1. Dans le volet de menu du compte de stockage, sous **PARAMÈTRES**, sélectionnez **Configuration**.
1. Sous **Transfert sécurisé requis**, sélectionnez **Activé**.

  ![Volet de menu du compte de stockage](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Activer le paramètre « Transfert sécurisé requis » par programmation

Pour exiger un transfert sécurisé par programme, utilisez le paramètre _supportsHttpsTrafficOnly_ dans les propriétés de compte de stockage avec l’API REST, des outils ou des bibliothèques :

* [API REST](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts) (version : 01/12/2016)
* [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (version : 4.1.0)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (version : 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (version : 1.1.0)
* [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (version : 6.3.0)
* [SDK Python](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (version : 1.1.0)
* [SDK Ruby](https://rubygems.org/gems/azure_mgmt_storage) (version : 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>Activer le paramètre « Transfert sécurisé requis » avec l’API REST

Pour simplifier les tests avec l’API REST, utilisez [ArmClient](https://github.com/projectkudu/ARMClient) pour appeler à partir de la ligne de commande.

 Utilisez la ligne de commande ci-dessous pour vérifier le paramètre avec l’API REST :

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

Recherchez le paramètre _supportsHttpsTrafficOnly_ dans la réponse. Par exemple :

```Json

{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}",
  "kind": "Storage",
  ...
  "properties": {
    ...
    "supportsHttpsTrafficOnly": false
  },
  "type": "Microsoft.Storage/storageAccounts"
}

```

Utilisez la ligne de commande ci-dessous pour activer le paramètre avec l’API REST :

```

# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json

```

Vous trouverez ci-dessous un exemple de Input.json :
```Json

{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}

```

## <a name="next-steps"></a>Étapes suivantes
Stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui, une fois réunies, permettent aux développeurs de créer des applications sécurisées. Pour plus d’informations, consultez notre [guide de sécurité Stockage Azure](storage-security-guide.md).

