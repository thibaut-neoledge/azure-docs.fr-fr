<properties
   pageTitle="Parcourir et sélectionner des images de machines virtuelles Linux | Microsoft Azure"
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
   ms.date="03/14/2016"
   ms.author="rasquill"/>

# Parcourir et sélectionner des images de machines virtuelles Linux dans Azure avec l’interface CLI ou PowerShell

Cette rubrique décrit comment rechercher des éditeurs, des offres, des références SKU et des versions pour chaque emplacement vers lequel vous souhaitez effectuer un déploiement. À titre d’exemple, voici certaines images de machines virtuelles Linux couramment utilisées :

**Tableau des images Linux couramment utilisées**


| PublisherName | Offer | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7\.1 |
| Canonical | UbuntuServer | 12\.04.5-LTS |
| Canonical | UbuntuServer | 14\.04.2-LTS |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0330_2016-->