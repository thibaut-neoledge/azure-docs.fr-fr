<properties
   pageTitle="Balisage d’une machine virtuelle | Microsoft Azure"
   description="Découvrez comment baliser une machine virtuelle Windows créée dans Azure à l’aide du modèle de déploiement de Resource Manager."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="04/04/2016"
   ms.author="iainfou;memccror"/>

# Balisage d’une machine virtuelle Windows dans Azure


Cet article décrit différentes façons d’ajouter des balises à une machine virtuelle Windows dans Azure à l’aide d’Azure Resource Manager. Les balises sont des paires clé/valeur définies par l’utilisateur, qui peuvent être placées directement sur une ressource ou sur un groupe de ressources. Azure prend actuellement en charge jusqu’à 15 balises par ressource et par groupe de ressources. Les balises peuvent être placées sur une ressource au moment de la création ou bien ajoutées à une ressource existante. Notez que les balises ne sont prises en charge que pour les ressources créées via Azure Resource Manager. Si vous souhaitez baliser une machine virtuelle Linux, consultez l’article [How to tag a Linux virtual machine in Azure](virtual-machines-linux-tag.md) (Balisage d’une machine virtuelle Linux dans Azure).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Balisage avec PowerShell

Pour créer, ajouter et supprimer des balises via PowerShell, vous devez d’abord configurer votre [environnement PowerShell avec Azure Resource Manager][]. Une fois que vous avez terminé la configuration, vous pouvez placer des balises sur les ressources Calcul, Réseau et Stockage au moment de la création ou après la création de la ressource via PowerShell. Cet article se concentre sur l’affichage et la modification des balises placées sur des machines virtuelles.

Accédez d’abord à une machine virtuelle via l’applet de commande `Get-AzureVM`.

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

Si votre machine virtuelle contient déjà des balises, vous verrez toutes les balises sur votre ressource :

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Si vous voulez ajouter des balises via PowerShell, vous pouvez utiliser la commande `Set-AzureResource`. Notez que lors de la mise à jour des balises via PowerShell, les balises sont mises à jour comme un tout. Ainsi, si vous ajoutez une balise à une ressource qui a déjà des balises, vous devez inclure toutes les balises que vous voulez placer sur la ressource. Voici un exemple montrant comment ajouter des balises supplémentaires à une ressource via des applets de commande PowerShell.

Cette première applet de commande définit toutes les balises placées sur *MyWindowsVM* sur la variable *tags*, à l’aide de la fonction `Get-AzureResource` et `Tags`. Notez que le paramètre `ApiVersion` est facultatif ; s’il n’est pas spécifié, la dernière version de l'API du fournisseur de ressources est utilisée.

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

La deuxième commande affiche les balises pour la variable donnée.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

La troisième commande ajoute une balise supplémentaire à la variable *tags*. Notez l'utilisation de **+=** pour ajouter la nouvelle paire clé/valeur à la liste *tags*.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

La quatrième commande définit toutes les balises définies dans la variable *tags* sur la ressource donnée. Dans ce cas, il s’agit de MyWindowsVM.

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

La cinquième commande affiche toutes les balises sur la ressource. Comme vous pouvez le voir, *Emplacement* est maintenant défini en tant que balise avec la valeur *MyLocation*.

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

Pour en savoir plus sur le balisage dans PowerShell, consultez les [applets de commande des ressources Azure][].

## Étapes suivantes

* Pour en savoir plus sur le balisage de vos ressources Azure, consultez les articles [Présentation d’Azure Resource Manager][] et [Utilisation de balises pour organiser vos ressources Azure][].
* Pour voir en quoi les balises peuvent vous aider à gérer l’utilisation des ressources Azure, consultez les articles [Comprendre votre facture Azure][] et [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure][].

[environnement PowerShell avec Azure Resource Manager]: ../powershell-azure-resource-manager.md
[applets de commande des ressources Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Présentation d’Azure Resource Manager]: ../resource-group-overview.md
[Utilisation de balises pour organiser vos ressources Azure]: ../resource-group-using-tags.md
[Comprendre votre facture Azure]: ../billing-understand-your-bill.md
[Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0406_2016-->