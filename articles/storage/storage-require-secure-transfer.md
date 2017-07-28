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
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 516618653064fd4e334197bba767a013a805260a
ms.contentlocale: fr-fr
ms.lasthandoff: 06/21/2017

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

## <a name="next-steps"></a>Étapes suivantes
Azure Storage propose un ensemble complet de fonctionnalités de sécurité qui, une fois réunies, permettent aux développeurs de créer des applications sécurisées. Pour plus d’informations, consultez notre [guide de sécurité sur Storage](storage-security-guide.md).

