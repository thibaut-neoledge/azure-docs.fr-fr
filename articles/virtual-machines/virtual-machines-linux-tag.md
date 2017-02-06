---
title: Comment baliser une machine virtuelle Linux | Microsoft Docs
description: "Découvrez comment baliser une machine virtuelle Linux créée dans Azure à l’aide du modèle de déploiement de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: mmccrory
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 56777605e84326fdde1821b42279bd7fc752c553


---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Comment baliser une machine virtuelle Linux dans Azure
Cet article décrit différentes façons d’ajouter des balises à une machine virtuelle Linux dans Azure à l’aide du modèle de déploiement Azure Resource Manager. Les balises sont des paires clé/valeur définies par l’utilisateur, qui peuvent être placées directement sur une ressource ou sur un groupe de ressources. Azure prend actuellement en charge jusqu’à 15 balises par ressource et par groupe de ressources. Les balises peuvent être placées sur une ressource au moment de la création ou bien ajoutées à une ressource existante. Notez que les balises ne sont prises en charge que pour les ressources créées via le modèle de déploiement Azure Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Balisage avec l’interface de ligne de commande Azure
Pour commencer, [installez et configurez l’interface de ligne de commande Azure](../xplat-cli-azure-resource-manager.md), et assurez-vous que vous êtes en mode Resource Manager (`azure config mode arm`).

Vous pouvez afficher toutes les propriétés d’une machine virtuelle donnée, y compris les balises, à l’aide de cette commande :

        azure vm show -g MyResourceGroup -n MyTestVM

Pour ajouter une nouvelle balise de machine virtuelle via l'interface de ligne de commande Azure, vous pouvez utiliser la commande `azure vm set` avec le paramètre de balise **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Pour supprimer toutes les balises, vous pouvez utiliser le paramètre **–T** dans la commande `azure vm set`.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Maintenant que nous avons appliqué des balises à nos ressources via l’interface de ligne de commande et le portail, examinons les détails d’utilisation pour afficher les balises dans le portail de facturation.

[!INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur le balisage de vos ressources Azure, consultez la rubrique [Présentation d’Azure Resource Manager][Azure Resource Manager Overview] et [Organisation des ressources Azure à l’aide de balises][Using Tags to organize your Azure Resources].
* Pour voir en quoi les balises peuvent vous aider à gérer votre utilisation des ressources Azure, consultez [Comprendre votre facture Azure][Understanding your Azure Bill] et [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../billing-usage-rate-card-overview.md



<!--HONumber=Dec16_HO2-->


