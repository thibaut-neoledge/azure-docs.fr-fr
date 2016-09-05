<properties
   pageTitle="Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure | Microsoft Azure"
   description="Découvrez comment déterminer l’éditeur, l’offre et la référence SKU pour des images quand vous créez une machine virtuelle Linux avec le modèle de déploiement de Resource Manager."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure

Cette rubrique décrit comment rechercher des éditeurs, des offres, des références SKU et des versions pour chaque emplacement vers lequel vous souhaitez effectuer un déploiement. À titre d’exemple, voici certaines images de machines virtuelles Linux couramment utilisées :

**Tableau des images Linux couramment utilisées**


| PublisherName | Offer | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| Red Hat | RHEL | 7,2 |
| credativ | Debian | 8 | 
| SUSE | openSUSE | 13\.2 |
| SUSE | SLES | 12-SP1 |
| OpenLogic | CentOS | 7\.1 |
| Canonical | UbuntuServer | 14\.04.4-LTS |
| CoreOS | CoreOS | Stable |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0824_2016-->