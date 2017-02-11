---
title: "Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure | Microsoft Docs"
description: "Découvrez comment déterminer l’éditeur, l’offre et la référence SKU pour des images quand vous créez une machine virtuelle Linux avec le modèle de déploiement de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 732767bcece6d892aee7a8e6976f527fc66bb48d


---
# <a name="select-linux-vm-images-with-the-azure-cli"></a>Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure
Cette rubrique décrit comment rechercher des éditeurs, des offres, des références SKU et des versions pour chaque emplacement vers lequel vous souhaitez effectuer un déploiement. À titre d’exemple, voici certaines images de machines virtuelles Linux couramment utilisées :

**Tableau des images Linux couramment utilisées**

| PublisherName | Offer | Sku |
|:--- |:--- |:--- |:--- |
| Red Hat |RHEL |7,2 |
| credativ |Debian |8 |
| SUSE |openSUSE |13.2 |
| SUSE |SLES |12-SP1 |
| OpenLogic |CentOS |7.1 |
| Canonical |UbuntuServer |14.04.4-LTS |
| CoreOS |CoreOS |Stable |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]




<!--HONumber=Nov16_HO3-->


