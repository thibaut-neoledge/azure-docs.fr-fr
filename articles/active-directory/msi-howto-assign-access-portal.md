---
title: "Attribuer à une identité du service administré un accès à une ressource Azure à l’aide du portail Azure"
description: "Instructions détaillées pour attribuer une identité du service administré à une ressourc et un accès à une autre ressource, à l’aide du portail Azure."
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
ms.openlocfilehash: 9dd02c8d7580cd9233e192f807686b7857ccf696
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-the-azure-portal"></a>Attribuer à une identité de service administré (MSI) un accès à une ressource à l’aide du portail Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Une fois que vous avez configuré une ressource Azure avec une identité du service administré, vous pouvez accorder à cette dernière un accès à une autre ressource, tout comme n’importe quel principal de sécurité. Cet exemple montre comment accorder à l’identité du service administré d’une machine virtuelle Azure un accès à un compte de stockage Azure, à l’aide du portail Azure.

## <a name="prerequisites"></a>Composants requis

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Utiliser le contrôle d’accès en fonction du rôle (RBAC) pour attribuer à l’identité du service administré un accès à une autre ressource

Après avoir activé l’identité du service administré sur une ressource Azure, [telle qu’une machine virtuelle Azure](msi-qs-configure-portal-windows-vm.md) :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure sur lequel vous avez configuré l’identité du service administré.

2. Accédez à la ressource souhaitée sur laquelle vous voulez modifier le contrôle d’accès. Dans cet exemple, nous offrons à une machine virtuelle Azure l’accès à un compte de stockage que nous parcourons.

3. Cliquez sur la page « Contrôle d’accès (IAM) » de la ressource, puis cliquez sur « + Ajouter ». Spécifiez le rôle, attribuez un accès à une « Machine virtuelle » et spécifiez l’abonnement et le groupe de ressources correspondants où se situe la ressource. La ressource doit s’afficher sous la zone de critères de recherche. Sélectionnez la ressource et appuyez sur « Enregistrer » : 

   ![Capture d’écran du contrôle d’accès (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. Vous être renvoyé à la page principale « Contrôle d’accès (IAM) », où s’affiche une nouvelle entrée pour l’identité du service administré de la ressource. Dans cet exemple, la machine virtuelle « SimpleWinVM » du groupe de ressources de démonstration s’est vue accorder un accès de type « Contributeur » pour le compte de stockage :

   ![Capture d’écran du contrôle d’accès (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>Résolution des problèmes

Si l’identité du service administré pour la ressource n’apparaît pas dans la liste des identités disponibles, vérifiez si elle a été activée correctement. Dans notre cas, nous pouvons retourner sur la machine virtuelle Azure et :

- Examinez la page « Configuration » et s’assurer que le paramètre d’identité du service administré est activé.
- Examinez la page « Extensions » et assurez-vous que l’extension de l’identité du service administré est déployée avec succès.

En cas d’inexactitude, vous devez redéployer l’identité du service administré sur votre ressource, ou résoudre le problème de déploiement.

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).
- Pour activer l’identité du service administré sur une machine virtuelle Azure, consultez [Configurer l’identité du service administré (MSI) d’une machine virtuelle Azure via le portail Azure](msi-qs-configure-portal-windows-vm.md).



