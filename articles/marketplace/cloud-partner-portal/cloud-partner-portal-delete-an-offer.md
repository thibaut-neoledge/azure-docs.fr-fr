---
title: "Supprimer une offre de la Place de marché Azure | Microsoft Docs"
description: "Cet article donne des détails sur la suppression d’une offre de la Place de marché Azure."
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: f09be39b75bd0f39094a2ec08609c0c7ff03bc8e
ms.lasthandoff: 04/12/2017


---
# <a name="delete-an-offersku-from-azure-marketplace"></a>Supprimer une offre / référence SKU de la Place de marché Azure

Vous pouvez décider de supprimer votre offre de la Place de marché pour diverses raisons.  La suppression de l’offre permet de faire en sorte que les nouveaux clients ne puissent plus acheter ou déployer votre offre, mais elle n’a aucun impact sur les clients existants. L’arrêt de l’offre est le processus d’arrêt de l’accord de service et/ou de licence entre vos clients actuels et vous.  L’aide et les stratégies relatives à l’arrêt et à la suppression des offres sont régies par le [contrat d’éditeur de la Place de marché Microsoft](http://go.microsoft.com/fwlink/?LinkID=699560) (voir la section 7) et les [stratégies de participation](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (voir la section 6.2).   Cet article traite des différents scénarios de suppression pris en charge et des étapes à suivre correspondantes.

## <a name="delete-a-live-sku-from-azure-marketplace"></a>Supprimer une référence SKU en ligne de la Place de marché Azure

Vous pouvez supprimer une référence SKU en ligne de la Place de marché Azure en suivant ces étapes :

1.  Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sélectionnez votre offre dans l’onglet **Toutes les offres**.

3.  Dans le volet à gauche de l’écran, sélectionnez l’onglet **SKU**.

4.  Sélectionnez la référence que vous souhaitez supprimer et cliquez sur le bouton Supprimer de cette référence SKU.

5.  [Republiez](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) l’offre sur la Place de marché Azure.

Une fois que l’offre est en ligne sur la Place de marché Azure, la référence est supprimée de la Place de marché Azure et du Portail Azure.

## <a name="rollback-to-a-previous-sku-version"></a>Restaurer une version précédente de la référence SKU

Vous pouvez supprimer la version actuelle d’une référence SKU en ligne de la Place de marché Azure en suivant les étapes ci-dessous. Une fois le processus terminé, la référence sera restaurée à sa version précédente.

1.  Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sélectionnez votre offre dans l’onglet **Toutes les offres**.

3.  Dans le volet à gauche de l’écran, sélectionnez l’onglet **SKU**.

4.  Supprimez la dernière **Version du disque** de la liste des versions du disque publiées.

5.  [Republiez](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) l’offre sur la Place de marché Azure.

Une fois que l’offre est en ligne sur la Place de marché Azure, la version actuelle de la référence listée est supprimée de la Place de marché Azure et du Portail Azure. La référence sera restaurée à sa version précédente.

## <a name="delete-a-live-offer"></a>Supprimer une offre en direct

Différents aspects doivent être pris en compte en cas de demande de suppression d’une offre en direct. Suivez les étapes ci-dessous pour obtenir de l’aide auprès de l’équipe de support afin de supprimer une offre en ligne de la Place de marché Azure :

1.  Ouvrez un ticket de support en utilisant ce [lien](https://go.microsoft.com/fwlink/?linkid=844975).

2.  Sélectionnez **Gestion des offres** dans la liste **Type de problème**, puis **Modification d’une offre et/ou d’une référence SKU déjà en production** dans la liste **Catégorie**.

3.  Envoyez la demande.

L’équipe de support vous guidera tout au long du processus de suppression de l’offre.

Notez que la suppression d’une référence SKU / offre n’affectera pas les achats de la référence / offre déjà effectués. Ceux continueront à fonctionner comme avant. Ils ne pourront plus être utilisés pour effectuer de nouveaux achats à l’avenir.  
