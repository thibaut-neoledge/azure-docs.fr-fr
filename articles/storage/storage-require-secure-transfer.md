---
title: "Exiger un transfert sécurisé dans le stockage Azure | Microsoft Docs"
description: "Découvrez la fonctionnalité Transfert sécurisé requis pour le stockage Azure et apprenez à l’activer."
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
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: 78737c681a91f24f73502a9cc25a301efc9304a4
ms.contentlocale: fr-fr
ms.lasthandoff: 08/07/2017

---
# <a name="require-secure-transfer"></a>Exiger un transfert sécurisé

L’option Transfert sécurisé requis améliore la sécurité de votre compte de stockage en autorisant uniquement les requêtes pour le compte de stockage à partir de connexions sécurisées. Par exemple, lors de l’appel d’API REST pour l’accès à votre compte de stockage, vous devez vous connecter à l’aide de HTTPS. Toutes les requêtes utilisant HTTP sont rejetées lorsque l’option Transfert sécurisé requis est activée.

Lorsque vous utilisez le service Azure Files, toute connexion sans chiffrement échoue si l’option Transfert sécurisé requis est activée. Cela inclut des scénarios utilisant SMB 2.1, SMB 3.0 sans chiffrement, et certaines versions du client SMB Linux. 

Par défaut, l’option Transfert sécurisé requis est désactivée.

> [!NOTE]
> Étant donné que le stockage Azure ne prend pas en charge HTTPS pour les noms de domaine personnalisés, cette option n’est pas appliquée lorsque vous utilisez un nom de domaine personnalisé.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Activer l’option Transfert sécurisé requis dans le portail Azure

Vous pouvez activer le paramètre Transfert sécurisé requis à la fois lorsque vous créez un compte de stockage dans le [portail Azure](https://portal.azure.com) et pour les comptes de stockage existants.

### <a name="require-secure-transfer-when-you-create-a-storage-account"></a>Exiger un transfert sécurisé lorsque vous créez un compte de stockage

1. Ouvrez le panneau **Créer un compte de stockage** dans le portail Azure.
1. Sous **Transfert sécurisé requis**, sélectionnez **Activé**.

  ![capture d’écran](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Exiger un transfert sécurisé pour un compte de stockage existant

1. Sélectionnez un compte de stockage existant dans le portail Azure.
1. Sélectionnez **Configuration** sous **PARAMÈTRES** dans le panneau de menu du compte de stockage.
1. Sous **Transfert sécurisé requis**, sélectionnez **Activé**.

  ![capture d’écran](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Activer le paramètre « Transfert sécurisé requis » par programmation

Le nom du paramètre est _supportsHttpsTrafficOnly_ dans les propriétés du compte de stockage. Vous pouvez activer « Transfert sécurisé requis » avec l’API REST, des outils ou des bibliothèques :

* **API REST** (Version : 2016-12-01) : [package de version](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts)
* **PowerShell** (Version : 4.1.0) : [package de version](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0)
* **CLI** (Version : 2.0.11) : [package de version](https://pypi.python.org/pypi/azure-cli-storage/2.0.11)
* **NodeJS** (Version : 1.1.0) : [package de version](https://www.npmjs.com/package/azure-arm-storage/)
* **SDK .NET** (Version : 6.3.0) : [package de version](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview)
* **SDK Python** (Version : 1.1.0) : [package de version](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0)
* **SDK Ruby** (Version : 0.11.0) : [package de version](https://rubygems.org/gems/azure_mgmt_storage)

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>Activer le paramètre « Transfert sécurisé requis » avec l’API REST

Pour simplifier les tests avec l’API REST, vous pouvez utiliser [ArmClient](https://github.com/projectkudu/ARMClient) pour appeler à partir de la ligne de commande.

 Vous pouvez utiliser la ligne de commande ci-dessous pour vérifier le paramètre avec l’API REST :

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

Dans la réponse figure le paramètre _supportsHttpsTrafficOnly_. Exemple :

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

Vous pouvez utiliser la ligne de commande ci-dessous pour activer le paramètre avec l’API REST :

```
# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json
```
Exemple de code Input.json :
```Json
{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}
```

## <a name="next-steps"></a>Étapes suivantes
Stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui, une fois réunies, permettent aux développeurs de créer des applications sécurisées. Pour plus d’informations, consultez notre [guide de sécurité sur Stockage](storage-security-guide.md).

