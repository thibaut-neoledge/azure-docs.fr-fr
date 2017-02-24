---
title: "Conception de groupes de machines virtuelles identiques Azure pour la mise à l’échelle | Microsoft Docs"
description: "En savoir plus sur la conception de vos groupes de machines virtuelles identiques Azure pour la mise à l’échelle"
keywords: machine virtuelle linux, groupes de machines virtuelles identiques
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: e869b06935736fae72bd3b5407ebab7c3830098d
ms.openlocfilehash: de3687a1bf36bf49db400a5660ac631f20b629d0


---
# <a name="designing-vm-scale-sets-for-scale"></a>Conception de jeux de mise à l’échelle de machine virtuelle pour la mise à l’échelle
Cette rubrique présente les considérations à prendre en compte pour créer des groupes identique de machines virtuelles. Pour plus d'informations sur les groupe identique de machines virtuelles, reportez-vous à la rubrique [Présentation des groupes de machines virtuelles identiques](virtual-machine-scale-sets-overview.md).

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Groupes identiques avec Azure Managed Disks
Les groupes identiques peuvent désormais être créées avec [Azure Managed Disks](../storage/storage-managed-disks-overview.md). Managed Disks permet de bénéficier des avantages suivants :
- Vous n’êtes pas obligé de créer au préalable un ensemble de comptes de stockage Azure pour les machines virtuelles du groupe identique.
- Vous pouvez définir des [disques de données associés](virtual-machine-scale-sets-attached-disks.md) pour les machines virtuelles de votre groupe identique.
- Les groupes identiques peuvent être configurés pour [prendre en charge jusqu’à 1 000 machines virtuelles par groupe](virtual-machine-scale-sets-placement-groups.md). 

Vous pouvez créer des groupes identiques avec Managed Disks à partir de la version « 2016-04-30-preview » de l’API Azure Compute. Pour plus d’informations sur la conversion d’un modèle de groupe identique vers Managed Disks, voir la page [Convert a scale set template to a managed disk scale set template](virtual-machine-scale-sets-convert-template-to-md.md) (Convertir un modèle de groupe identique en modèle de groupe identique Managed Disks).

### <a name="user-managed-storage"></a>Stockage géré par l’utilisateur
Un groupe identique qui n’est pas défini avec Azure Managed Disks repose sur les comptes de stockage créés par l’utilisateur pour stocker les disques du système d’exploitation des machines virtuelles dans le groupe. Un rapport de 20 machines virtuelles maximum par compte de stockage est recommandé pour atteindre la valeur d’E/S maximale, mais aussi pour tirer parti du _sur-approvisionnement_ (voir ci-dessous). Il est également recommandé de répartir dans l’alphabet les premiers caractères des noms de comptes de stockage. Cette méthode améliore la répartition sur les différents systèmes internes. 

>[!NOTE]
>La version d’API de VM Scale Sets `2016-04-30-preview` prend en charge l’utilisation d’Azure Managed Disks pour le disque du système d’exploitation et tous les disques de données supplémentaires. Pour plus d’informations, consultez les sections relatives à la [vue d’ensemble de Managed Disks](../storage/storage-managed-disks-overview.md) et à [l’utilisation de disques de données associés](virtual-machine-scale-sets-attached-disks.md). 

## <a name="overprovisioning"></a>Sur-approvisionnement
À compter de la version d’API « 2016-03-30 », les groupes identiques de machines virtuelles sur-approvisionnent par défaut les machines virtuelles. Avec le sur-approvisionnement activé, le groupe identique crée en réalité plus de machines virtuelles par rapport au nombre demandé, puis supprime les machines virtuelles supplémentaires une fois ce nombre de machines virtuelles approvisionné. Le sur-approvisionnement améliore les taux de réussite de l’approvisionnement et réduit la durée du déploiement. Vous n’êtes pas facturé pour les machines virtuelles supplémentaires et elles ne seront pas comptabilisées dans vos limites de quotas.

Bien que le sur-approvisionnement améliore les taux de réussite de l’approvisionnement, il peut provoquer un comportement déroutant si une application n’est pas conçue pour gérer les machines virtuelles qui disparaissent, puis réapparaissent. Pour désactiver le sur-approvisionnement, vérifiez que votre modèle contient la chaîne suivante : "overprovision": "false". Vous trouverez plus de détails dans la [documentation de l’API REST de groupes identiques de machines virtuelles](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Si votre groupe identique utilise le stockage géré par l’utilisateur et que vous désactivez le sur-approvisionnement, vous pouvez avoir plus de 20 machines virtuelles par compte de stockage, mais il n’est pas recommandé de dépasser 40 pour des raisons de performances d’E/S. 

## <a name="limits"></a>limites
Un groupe identique basé sur une image Place de marché (également appelée image de plateforme) et configuré pour utiliser Azure Managed Disks peut prendre en charge jusqu’à 1 000 machines virtuelles. Si vous configurez votre groupe identique pour prendre en charge plus de 100 machines virtuelles, tous les scénarios ne fonctionnent pas de la même manière (par exemple l’équilibrage de charge). Pour plus d’informations, voir [Working with large virtual machine scale sets](virtual-machine-scale-sets-placement-groups.md) (Utilisation de grands groupes de machines virtuelles identiques). 

Un groupe identique configuré avec des comptes de stockage gérés par l’utilisateur est actuellement limité à 100 machines virtuelles (et 5 comptes de stockage sont recommandés pour cette échelle).

Un groupe identique basé sur une image personnalisée (créée par vous-même) peut prendre en charge jusqu’à 100 machines virtuelles s’il est configuré avec Azure Managed Disks. Si le groupe identique est configuré avec des comptes de stockage gérés par l’utilisateur, il doit créer tous les disques durs virtuels du disque du système d’exploitation dans un même compte de stockage. Par conséquent, le nombre maximal recommandé de machines virtuelles dans un groupe identique basé sur une image personnalisée et dont le stockage est géré par l’utilisateur est de 20. Si vous désactivez le sur-approvisionnement, vous pouvez aller jusqu’à 40.

Si vous souhaitez avoir plus de machines virtuelles que ne l’autorisent ces limites, vous devez déployer plusieurs groupes identiques, comme indiqué dans [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).




<!--HONumber=Feb17_HO2-->


