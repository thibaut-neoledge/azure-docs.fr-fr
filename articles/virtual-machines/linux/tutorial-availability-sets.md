---
title: "Didacticiel sur les groupes à haute disponibilité pour les machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Découvrez les groupes à haute disponibilité pour les machines virtuelles Linux dans Azure."
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 65a7872e0880cc74a00ca1c05baae2b34b407a61
ms.lasthandoff: 04/26/2017


---

# <a name="how-to-use-availability-sets"></a>Utilisation des groupes à haute disponibilité

Dans ce didacticiel, vous allez découvrir comment augmenter la disponibilité de vos machines virtuelles (VM) en les plaçant dans un regroupement logique appelé groupe à haute disponibilité. Lorsque vous créez des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure répartit les machines virtuelles dans l’infrastructure sous-jacente. En cas de panne matérielle ou de tâches de maintenance planifiées sur la plateforme, l’utilisation des groupes à haute disponibilité garantit qu’au moins une machine virtuelle continue à s’exécuter.

Les étapes de ce didacticiel peuvent être effectuées à l’aide de la dernière version [d’Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="availability-set-overview"></a>Vue d’ensemble des groupes à haute disponibilité

Des machines virtuelles peuvent être créées dans des regroupements logiques de matériel dans le centre de données Azure sous-jacent. Si vous créez au moins deux machines virtuelles, vos ressources de calcul et de stockage sont réparties dans le matériel, par exemple sur les serveurs, les commutateurs réseau et l’espace de stockage. Cette distribution assure la disponibilité de votre application si un composant matériel devait subir une maintenance. Les groupes à haute disponibilité vous permettent de définir ce regroupement logique.

Les groupes à haute disponibilité garantissent une haute disponibilité pour les machines virtuelles. Vous devez également vous assurer que vos applications sont aussi conçues pour tolérer des événements de maintenance ou des interruptions.

## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Vous pouvez créer un groupe à haute disponibilité à l’aide de la commande [az vm availability-set create](/cli/azure/availability-set#create). Dans cet exemple, nous définissons le nombre de domaines de mise à jour et d’erreur sur **2** pour le groupe à haute disponibilité nommé **myAvailabilitySet** dans le groupe de ressources **myResourceGroupAvailability**.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

## <a name="create-vms-inside-an-availability-set"></a>Créer des machines virtuelles dans un groupe à haute disponibilité

Vous devez créer des machines virtuelles au sein du groupe à haute disponibilité pour vous assurer qu’elles seront correctement réparties dans le matériel. Vous ne pouvez pas ajouter une machine virtuelle existante à un groupe à haute disponibilité après sa création. 

Le matériel situé à un emplacement est divisé en plusieurs domaines de mise à jour et d’erreur. Un **domaine de mise à jour** est un groupe de machines virtuelles et d’équipements physiques sous-jacents pouvant être redémarrés en même temps. Les machines virtuelles d’un même **domaine d’erreur** partagent un espace de stockage commun ainsi qu’une source d’alimentation et un commutateur réseau. 

Lorsque vous créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm#create), vous spécifiez le nom du groupe à haute disponibilité à l’aide du paramètre `--availability-set`.

```azurecli
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Nous disposons désormais de 2 machines virtuelles réparties sur le matériel sous-jacent. 

## <a name="check-for-available-vm-sizes"></a>Vérifier les tailles de machines virtuelles disponibles 

Vous pouvez ajouter ultérieurement d’autres machines virtuelles au groupe à haute disponibilité, mais vous devez connaître les tailles des machines virtuelles qui sont disponibles sur le matériel. Utilisez `az vm availability-set list-sizes` pour répertorier toutes les tailles disponibles sur le cluster matériel pour le groupe à haute disponibilité.

```azurecli
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez développé vos connaissances sur les groupes à haute disponibilité. Passez au didacticiel suivant pour en savoir plus sur les groupes de machines virtuelles identiques.

[Créer un groupe de machines virtuelles identiques](tutorial-create-vmss.md)


