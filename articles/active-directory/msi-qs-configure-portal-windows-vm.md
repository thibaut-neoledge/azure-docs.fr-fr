---
title: "Configurer l’identité du service administré sur une machine virtuelle Azure à l’aide du portail Azure"
description: "Instructions détaillées sur la configuration de l’identité du service administré (MSI) sur une machine virtuelle Azure, à l’aide du portail Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 9406ba2bbbea41f4677cd0d5aaddf16b0f4f26c8
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Configurer l’identité du service administré (MSI) d’une machine virtuelle à l’aide du portail Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à activer et supprimer l’identité du service administré d’une machine virtuelle Windows Azure, à l’aide du portail Azure.

## <a name="prerequisites"></a>Composants requis

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Activer l’identité du service administré lors de la création d’une machine virtuelle Azure

Au moment de rediger cet article, l’activation de l’identité du service administré lors de la création d’une machine virtuelle dans le portail Azure n’est pas prise en charge. Au lieu de cela, reportez-vous au guide de démarrage rapide [Créer une machine virtuelle Windows avec le portail Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) pour plus d’informations sur la création d’une machine virtuelle. Passez ensuite à la section suivante pour plus d’informations sur l’activation de l’identité du service administré.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Activer l’identité du service administré sur une machine virtuelle Azure existante

Si vous avez une machine virtuelle qui a été initialement approvisionnée sans identité du service administré :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure sous lequel vous souhaitez déployer la machine virtuelle.

2. Accédez à la machine virtuelle souhaitée.

2. Cliquez sur la page « Configuration », activez l’identité du service administré sur la machine virtuelle en sélectionnant « Oui » sous « Identité du service administré », puis cliquez sur **Enregistrer**. Cette opération peut durer 60 secondes ou plus :

   ![Capture d’écran de la page Configuration](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Supprimer l’identité du service administré d’une machine virtuelle Azure

Si vous disposez d’une machine virtuelle qui ne nécessite plus d’identité du service administré :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure sous lequel vous souhaitez déployer la machine virtuelle.

2. Accédez à la machine virtuelle souhaitée.

3. Cliquez sur la page « Configuration », supprimez l’identité du service administré à partir de la machine virtuelle en sélectionnant « Non » sous « Identité du service administré », puis cliquez sur **Enregistrer**. Cette opération peut durer 60 secondes ou plus :

   ![Capture d’écran de la page Configuration](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).
- Cet article se base sur le guide de démarrage rapide [Créer une machine virtuelle Windows avec le portail Azure](../virtual-machines/windows/quick-create-portal.md) ,et a été modifié pour inclure des instructions spécifiques à l’identité du service administré. 

## <a name="next-steps"></a>Étapes suivantes

- À l’aide du portail Azure, accordez à l’identité du service administré d’une machine virtuelle Azure [un accès à une autre ressource Azure](msi-howto-assign-access-portal.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

