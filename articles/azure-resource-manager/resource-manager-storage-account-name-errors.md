---
title: "Erreurs liées au nom du compte de stockage Azure | Microsoft Doc"
description: "Décrit les erreurs que vous pouvez rencontrer lorsque vous spécifiez un nom de compte de stockage."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: dc045827fbd38054a334ff22eb30e0db6a31bac8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-storage-account-names"></a>Résoudre les erreurs liées aux noms de compte de stockage

Cet article décrit les erreurs de dénomination que vous risquez de rencontrer lorsque vous déployez un compte de stockage.

## <a name="symptom"></a>Symptôme

Si le nom de votre compte de stockage inclut des caractères interdits, vous recevez une erreur similaire à ce qui suit :

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Pour les comptes de stockage, vous devez fournir un nom pour la ressource qui est unique dans Azure. Si vous ne fournissez pas un nom unique, une erreur de ce type s’affiche :

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Si vous déployez un compte de stockage portant le même nom qu’un compte de stockage existant dans votre abonnement, mais que vous indiquez un emplacement différent, vous recevez une erreur indiquant que le compte de stockage existe déjà dans un autre emplacement. Supprimez le compte de stockage existant ou indiquez le même emplacement que le compte de stockage existant.

## <a name="cause"></a>Cause :

Ce nom doit comprendre entre 3 et 24 caractères, uniquement des lettres en minuscules et des nombres. Ce nom doit être unique.

## <a name="solution"></a>Solution

### <a name="solution-1"></a>Solution 1

Assurez-vous que le nom du compte de stockage est unique. Vous pouvez créer un nom unique en concaténant votre nommage avec le résultat de la fonction [uniqueString](resource-group-template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

### <a name="solution-2"></a>Solution 2

Vérifiez que le nom de votre compte de stockage ne présente pas plus de 24 caractères. La fonction [uniqueString](resource-group-template-functions-string.md#uniquestring) renvoie 13 caractères. Si vous concaténez un préfixe ou un suffixe au résultat de la fonction **uniqueString**, fournissez une valeur de 11 caractères maximum.

```json
"parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
}
```

### <a name="solution-3"></a>Solution 3

Assurez-vous que le nom de votre compte de stockage n’inclut aucune majuscule, ni aucun caractère spécial.