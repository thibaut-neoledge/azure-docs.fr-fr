<properties
   pageTitle="Comment baliser une machine virtuelle Linux | Microsoft Azure"
   description="Découvrez comment baliser une machine virtuelle Linux créée dans Azure à l’aide du modèle de déploiement de Resource Manager."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# Comment baliser une machine virtuelle Linux dans Azure

Cet article décrit différentes façons d’ajouter des balises à une machine virtuelle Linux dans Azure à l’aide du modèle de déploiement Azure Resource Manager. Les balises sont des paires clé/valeur définies par l’utilisateur, qui peuvent être placées directement sur une ressource ou sur un groupe de ressources. Azure prend actuellement en charge jusqu’à 15 balises par ressource et par groupe de ressources. Les balises peuvent être placées sur une ressource au moment de la création ou bien ajoutées à une ressource existante. Notez que les balises ne sont prises en charge que pour les ressources créées via le modèle de déploiement Azure Resource Manager.

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Balisage avec l’interface de ligne de commande Azure

Pour commencer, [installez et configurez l’interface de ligne de commande Azure](../xplat-cli-azure-resource-manager.md), et assurez-vous que vous êtes en mode Resource Manager (`azure config mode arm`).

Vous pouvez afficher toutes les propriétés d’une machine virtuelle donnée, y compris les balises, à l’aide de cette commande :

        azure vm show -g MyResourceGroup -n MyTestVM

Pour ajouter une nouvelle balise de machine virtuelle via l'interface de ligne de commande Azure, vous pouvez utiliser la commande `azure vm set` avec le paramètre de balise **-t** :

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Pour supprimer toutes les balises, vous pouvez utiliser le paramètre **–T** dans la commande `azure vm set`.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Maintenant que nous avons appliqué des balises à nos ressources via l’interface de ligne de commande et le portail, examinons les détails d’utilisation pour afficher les balises dans le portail de facturation.

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## Étapes suivantes

* Pour en savoir plus sur le balisage de vos ressources Azure, consultez [Présentation d’Azure Resource Manager][] et [Organisation des ressources Azure à l’aide de balises][].
* Pour voir en quoi les balises peuvent vous aider à gérer votre utilisation des ressources Azure, consultez [Comprendre votre facture Azure][] et [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure][].





[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Présentation d’Azure Resource Manager]: ../resource-group-overview.md
[Organisation des ressources Azure à l’aide de balises]: ../resource-group-using-tags.md
[Comprendre votre facture Azure]: ../billing-understand-your-bill.md
[Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0706_2016-->