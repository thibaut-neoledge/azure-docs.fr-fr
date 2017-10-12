---
title: "Télécharger des éléments de la Place de marché à partir d’Azure | Microsoft Docs"
description: "Je peux télécharger des éléments de la Place de marché à partir d’Azure dans mon déploiement Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Quand vous décidez du contenu à inclure dans votre Place de marché Azure Stack, il est recommandé de prendre en compte le contenu disponible auprès de la Place de marché Azure. Vous pouvez télécharger à partir d’une liste organisée d’éléments de la Place de marché Azure, qui ont été préalablement testés pour s’exécuter sur Azure Stack. De nouveaux éléments sont fréquemment ajoutés à cette liste : consultez-la donc régulièrement pour voir ce qu’elle contient de nouveau.

Pour télécharger des éléments de la Place de marché, vous devez d’abord [inscrire Azure Stack auprès d’Azure](azure-stack-register.md). 

## <a name="download"></a>Télécharger
1. Connectez-vous au portail d’administration d’Azure Stack (https://portal.local.azurestack.external).
2. Certains éléments de la Place de marché peuvent être très volumineux.  Vérifiez que vous avez suffisamment d’espace sur votre système en cliquant sur **Fournisseurs de ressources** > **Stockage**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. Cliquez sur **Autres services** > **Gestion de la Place de marché**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Cliquez sur **Ajouter à partir d’Azure** pour afficher la liste des éléments disponibles au téléchargement. Vous pouvez cliquer sur chaque élément de la liste pour afficher sa description et la taille du téléchargement.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Sélectionnez l’élément que vous voulez dans la liste, puis cliquez sur **Télécharger**. Ceci démarre le téléchargement de l’image de machine virtuelle pour l’élément sélectionné. Le temps de téléchargement varie.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Une fois le téléchargement terminé, vous pouvez déployer votre nouvel élément de Place de marché en tant qu’opérateur ou utilisateur Azure Stack. Cliquez sur **+ Nouveau**, recherchez le nouvel élément de Place de marché parmi les catégories, puis sélectionnez l’élément.
7. Cliquez sur **Créer** pour ouvrir l’expérience de création de l’élément qui vient d’être téléchargé. Suivez les instructions pas à pas pour déployer votre élément.

## <a name="next-steps"></a>Étapes suivantes

[Créer et publier un article de la Place de marché](azure-stack-create-and-publish-marketplace-item.md)
