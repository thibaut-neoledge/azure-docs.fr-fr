---
title: "Créer un groupe à haute disponibilité pour les machines virtuelles dans Azure | Microsoft Docs"
description: "Apprenez à créer un groupe à haute disponibilité géré ou non géré pour vos machines virtuelles à l’aide d’Azure PowerShell ou du portail dans le modèle de déploiement Resource Manager."
keywords: "groupe à haute disponibilité"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a3db8659-ace8-4e78-8b8c-1e75c04c042c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: e1933c5bf65f3e28d952213317413d0f65309c5b
ms.lasthandoff: 03/31/2017

---
# <a name="increase-vm-availability-by-creating-an-azure-availability-set"></a>Augmenter la disponibilité des machines virtuelles en créant un groupe à haute disponibilité Azure 
Les groupes à haute disponibilité assurent la redondance pour votre application. Nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Cette configuration assure qu'au moins une des machines virtuelles sera disponible pendant un événement de maintenance planifié ou non et répondra au niveau de 99,95 % inscrit dans les contrats de niveau de service Azure. Pour plus d’informations, consultez le [SLA pour Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Les machines virtuelles doivent être créées dans le même groupe de ressources que le groupe à haute disponibilité.
> 

Si vous souhaitez que votre machine virtuelle fasse partie d’un groupe à haute disponibilité, vous devez le créer avant ou pendant la création de la première machine virtuelle du groupe. Si votre machine virtuelle utilise des disques gérés, le groupe à haute disponibilité doit être créé en tant que groupe à haute disponibilité géré.

Pour plus d’informations sur la création et l’utilisation des groupes à haute disponibilité, consultez la page [Gestion de la disponibilité des machines virtuelles](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Utilisation du portail pour créer un groupe à haute disponibilité avant la création des machines virtuelles
1. Dans le menu Hub, cliquez sur **Parcourir** et sélectionnez **Groupes à haute disponibilité**.
2. Dans le **panneau Groupes à haute disponibilité**, cliquez sur **Ajouter**.
   
    ![Capture d’écran illustrant le bouton Ajouter pour la création d’un groupe à haute disponibilité.](./media/create-availability-set/add-availability-set.png)
3. Dans le panneau **Créer un groupe à haute disponibilité** , renseignez les informations du groupe.
   
    ![Capture d’écran illustrant les informations que vous devez entrer pour créer le groupe à haute disponibilité.](./media/create-availability-set/create-availability-set.png)
   
   * **Nom** : le nom doit compter entre 1 et 80 caractères composés de chiffres, de lettres, de points, de traits de soulignement et de tirets. Le premier caractère doit être une lettre ou un chiffre. Le dernier caractère doit être une lettre, un chiffre ou un trait de soulignement.
   * **Domaines d’erreur** : les domaines d’erreur définissent le groupe de machines virtuelles partageant une source d’alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles sont réparties entre trois domaines d’erreur maximum et peuvent être permutées entre le 1er et le 3e.
   * **Domaines de mise à jour** : cinq domaines de mise à jour sont assignés par défaut et ils peuvent être définis entre 1 et 20. Les domaines de mise à jour indiquent les groupes de machines virtuelles et les équipements physiques sous-jacents pouvant être redémarrés en même temps. Par exemple, si nous spécifions cinq domaines de mise à jour, dans le cas où un seul groupe à haute disponibilité comprend plus de cinq machines virtuelles, la sixième machine est placée dans le même domaine de mise à jour que la première, la septième dans le même que la deuxième, etc. L’ordre du redémarrage peut ne pas être séquentiel, mais un seul domaine de mise à jour est redémarré à la fois.
   * **Abonnement** : sélectionnez l’abonnement que vous souhaitez utiliser, si vous en avez plusieurs.
   * **Groupe de ressources** : sélectionnez un groupe de ressources existant en cliquant sur la flèche et en sélectionnant un groupe de ressources dans la liste déroulante. Vous pouvez également créer un nouveau groupe de ressources en entrant un nom. Le nom peut contenir les caractères suivants : lettres, chiffres, points, tirets, traits de soulignement et parenthèses ouvrantes et fermantes. Le nom ne peut pas se terminer par un point. Toutes les machines virtuelles dans le groupe à haute disponibilité doivent être créées dans le même groupe de ressources que le groupe à haute disponibilité.
   * **Emplacement** : sélectionnez un emplacement dans la liste déroulante.
   * **Géré** : sélectionnez *Oui* pour créer un groupe à haute disponibilité à utiliser avec des machines virtuelles qui utilisent des disques gérés pour le stockage. Sélectionnez **Non** si les machines virtuelles qui seront dans le groupe utilisent des disques non gérés dans un compte de stockage.
   
4. Après avoir entré les informations, cliquez sur **Créer**. 

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Utilisation du portail pour créer simultanément une machine virtuelle et un groupe à haute disponibilité
Si vous créez une machine virtuelle à l’aide du portail, vous pouvez également créer un groupe à haute disponibilité pour la machine virtuelle lorsque vous créez la première machine virtuelle dans le groupe. Si vous choisissez d’utiliser des disques gérés pour votre machine virtuelle, un groupe à haute disponibilité géré est créé.

![Capture d’écran illustrant le processus de création d’un groupe à haute disponibilité lorsque vous créez la machine virtuelle.](./media/create-availability-set/new-vm-avail-set.png)

## <a name="add-a-new-vm-to-an-existing-availability-set-in-the-portal"></a>Ajouter une nouvelle machine virtuelle à un groupe à haute disponibilité existant dans le portail
Pour chaque machine virtuelle supplémentaire que vous créez devant appartenir au groupe, assurez-vous de la créer dans le même **groupe de ressources** , puis sélectionnez le groupe à haute disponibilité existant, défini à l’étape 3. 

![Capture d’écran illustrant la sélection d’un groupe à haute disponibilité existant à utiliser pour votre machine virtuelle.](./media/create-availability-set/add-vm-to-set.png)

## <a name="use-powershell-to-create-an-availability-set"></a>Utilisation de PowerShell pour créer un groupe à haute disponibilité
Cet exemple crée un groupe à haute disponibilité nommé **myAvailabilitySet** dans le groupe de ressources **myResourceGroup** dans l’emplacement **États-Unis de l’Ouest**. Cette opération doit être effectuée avant la création de la première machine virtuelle qui appartiendra au groupe.

Avant de commencer, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).


Si vous utilisez des disques gérés pour vos machines virtuelles, tapez :

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" -managed
```

Si vous utilisez vos propres comptes de stockage pour vos machines virtuelles, tapez :

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" 
```

Pour plus d’informations, consultez [New-AzureRmAvailabilitySet](/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermavailabilityset).

## <a name="troubleshooting"></a>Résolution des problèmes
* Lorsque vous créez une machine virtuelle, si le groupe à haute disponibilité que vous voulez utiliser n’est pas dans la liste déroulante du portail, elle a peut-être été créée dans un autre groupe de ressources. Si vous ne connaissez pas le groupe de ressources pour votre groupe à haute disponibilité, cliquez sur Parcourir > Groupes à haute disponibilité dans le menu Hub pour afficher la liste de vos groupes à haute disponibilité et les groupes de ressources auxquels ils appartiennent.

## <a name="next-steps"></a>Étapes suivantes
Ajout de stockage supplémentaire à votre machine virtuelle en ajoutant un [disque de données](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)supplémentaire.


