<properties
   pageTitle="Comment baliser une machine virtuelle Linux | Microsoft Azure"
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
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# Comment baliser une machine virtuelle Linux dans Azure

Cet article décrit différentes façons d’ajouter des balises à une machine virtuelle Linux dans Azure à l’aide d’Azure Resource Manager. Les balises sont des paires clé/valeur définies par l’utilisateur, qui peuvent être placées directement sur une ressource ou sur un groupe de ressources. Azure prend actuellement en charge jusqu’à 15 balises par ressource et par groupe de ressources. Les balises peuvent être placées sur une ressource au moment de la création ou bien ajoutées à une ressource existante. Notez que les balises ne sont prises en charge que pour les ressources créées via Azure Resource Manager. Si vous souhaitez baliser une machine virtuelle Windows, consultez l’article [Balisage d’une machine virtuelle Windows dans Azure](virtual-machines-windows-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Balisage avec l’interface de ligne de commande Azure

Le balisage est également pris en charge pour les ressources qui sont déjà créés via l’interface de ligne de commande Azure. Pour commencer, configurez votre [environnement CLI Azure][]. Connectez-vous à votre abonnement via l’interface de ligne de commande Azure et passez en mode ARM. Vous pouvez afficher toutes les propriétés d’une machine virtuelle donnée, y compris les balises, à l’aide de cette commande :

        azure vm show -g MyResourceGroup -n MyVM

Contrairement à PowerShell, si vous ajoutez des balises à une ressource qui contient déjà des balises, il est inutile de spécifier toutes les balises (anciennes et nouvelles) avant d'utiliser la commande `azure vm set`. Au lieu de cela, cette commande vous permet d’ajouter une balise à votre ressource. Pour ajouter une nouvelle balise de machine virtuelle via l'interface de ligne de commande Azure, vous pouvez utiliser la commande `azure vm set` avec le paramètre de balise **-t** :

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Pour supprimer toutes les balises, vous pouvez utiliser le paramètre **–T** dans la commande `azure vm set`.

        azure vm set – g MyResourceGroup –n MyVM -T


Maintenant que nous avons appliqué des balises à nos ressources via PowerShell, l’interface de ligne de commande et le portail, examinons les détails d’utilisation pour afficher les balises dans le portail de facturation.




## Étapes suivantes

* Pour en savoir plus sur le balisage de vos ressources Azure, consultez les articles [Présentation d’Azure Resource Manager][] et [Utilisation de balises pour organiser vos ressources Azure][].
* Pour voir en quoi les balises peuvent vous aider à gérer l’utilisation des ressources Azure, consultez les articles [Comprendre votre facture Azure][] et [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure][].





[environnement CLI Azure]: ./xplat-cli-azure-resource-manager.md
[Présentation d’Azure Resource Manager]: ../resource-group-overview.md
[Utilisation de balises pour organiser vos ressources Azure]: ../resource-group-using-tags.md
[Comprendre votre facture Azure]: ../billing-understand-your-bill.md
[Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0330_2016-->