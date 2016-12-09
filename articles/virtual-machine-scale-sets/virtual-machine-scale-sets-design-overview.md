---
title: "Conception de jeux de mise à l’échelle de machines virtuelles | Microsoft Docs"
description: "En savoir plus sur la conception de vos jeux de mise à l&quot;échelle de machines virtuelles"
keywords: "machine virtuelle linux, jeux de mise à l’échelle de machine virtuelle"
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
ms.date: 07/28/2016
ms.author: gatneil
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d4821bf5b7e3527e9d130e24c81d90368f395d30


---
# <a name="designing-vm-scale-sets-for-scale"></a>Conception de jeux de mise à l’échelle de machine virtuelle pour la mise à l’échelle
Cette rubrique présente les considérations à prendre en compte pour créer des groupes identique de machines virtuelles. Pour plus d'informations sur les groupe identique de machines virtuelles, reportez-vous à la rubrique [Présentation des groupes de machines virtuelles identiques](virtual-machine-scale-sets-overview.md).

## <a name="storage"></a>Storage
Un jeu de mise à l'échelle utilise des comptes de stockage pour stocker les disques du système d'exploitation des machines virtuelles dans le jeu. Nous recommandons un ratio maximum de 20 machines virtuelles par compte de stockage. Nous vous recommandons également de répartir dans l'alphabet les premiers caractères des noms de comptes de stockage. Cette méthode améliore la répartition sur les différents systèmes internes. Par exemple, dans le modèle suivant, nous utilisons la fonction de modèle Resource Manager uniqueString pour générer des hachages de préfixe qui sont ajoutés aux noms de comptes de stockage : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).

## <a name="overprovisioning"></a>Sur-approvisionnement
À compter de la version d'API « 2016-03-30 », les groupes identiques de machines virtuelles sur-approvisionnent par défaut les machines virtuelles. Avec le sur-approvisionnement activé, le groupe identique crée véritablement plus de machines virtuelles que vous avez demandé, puis supprime les dernières machines virtuelles supplémentaires créées. Le sur-approvisionnement améliore les taux de réussite de l’approvisionnement. Vous n’êtes pas facturé pour ces machines virtuelles supplémentaires et elles ne seront pas comptabilisées dans vos limites de quotas.

Bien que le sur-approvisionnement n'améliore pas les taux de réussite de l’approvisionnement, il peut provoquer un comportement déroutant si une application n'est pas conçue pour gérer les machines virtuelles qui disparaissent sans avertissement. Pour désactiver le sur-approvisionnement, vérifiez que votre modèle contient la chaîne suivante : "overprovision": "false". Vous trouverez plus de détails dans la [documentation de l’API REST de groupes identiques de machines virtuelles](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Si vous désactivez le sur-approvisionnement, vous pouvez obtenir un ratio plus important de machines virtuelles par compte de stockage, mais nous déconseillons d’aller au-delà de 40.

## <a name="limits"></a>Limites
Un groupe identique basé sur une image personnalisée (créée par vous-même) doit créer tous les disques durs virtuels du système d’exploitation dans un même compte de stockage. Par conséquent, le nombre maximal recommandé de machines virtuelles dans un groupe identique basé sur une image personnalisée est de 20. Si vous désactivez le sur-approvisionnement, vous pouvez aller jusqu’à 40.

Un groupe identique basé sur une image de plateforme est actuellement limité à 100 machines virtuelles (nous vous recommandons d’utiliser 5 comptes de stockage pour cette échelle).

Si vous souhaitez avoir plus de machines virtuelles que ne l’autorisent ces limites, vous devez déployer plusieurs groupes identiques, comme indiqué dans [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).




<!--HONumber=Nov16_HO3-->


