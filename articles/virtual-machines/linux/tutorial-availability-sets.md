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
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 63fe3f165864f06228604cac56d06cc061ab25f5
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

---

# <a name="how-to-use-availability-sets"></a>Utilisation des groupes à haute disponibilité


Ce didacticiel explique comment améliorer la disponibilité et la fiabilité de vos solutions de machine virtuelle sur Azure en utilisant une fonctionnalité appelée Groupes à haute disponibilité. Les groupes à haute disponibilité veillent à ce que les machines virtuelles que vous déployez sur Azure soient distribuées sur plusieurs clusters matériels isolés. Cela garantit que, si une défaillance matérielle ou logicielle se produit dans Azure, seul un sous-ensemble de vos machines virtuelles soit concerné et que votre solution globale reste disponible et opérationnelle dans la perspective des clients qui l’utilisent.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="availability-set-overview"></a>Vue d’ensemble des groupes à haute disponibilité

Un groupe à haute disponibilité est une fonctionnalité de regroupement logique que vous pouvez utiliser dans Azure pour vous assurer que les ressources de machine virtuelle que vous y incluez sont isolées les unes des autres lors de leur déploiement dans un centre de données Azure. Azure veille à ce que les machines virtuelles que vous placez dans un groupe à haute disponibilité s’exécutent sur plusieurs serveurs physiques, racks de calcul, unités de stockage et commutateurs réseau. Cela garantit qu’en cas de panne matérielle ou logicielle d’Azure, seul un sous-ensemble de vos machines virtuelles est affecté, et que votre application globale reste opérationnelle et disponible pour vos clients. L’utilisation de groupes à haute disponibilité est essentielle pour créer des solutions cloud fiables.

Prenons l’exemple d’une solution basée sur une machine virtuelle classique dans laquelle vous disposez de 4 serveurs web frontaux et utilisez 2 machines virtuelles principales hébergeant une base de données. Avec Azure, vous pouvez définir deux groupes à haute disponibilité avant de déployer vos machines virtuelles : l’un pour le niveau « web » et l’autre pour le niveau « base de données ». Lorsque vous créez une machine virtuelle, vous pouvez spécifier le groupe à haute disponibilité en tant que paramètre pour la commande az vm create, de sorte qu’Azure veille automatiquement à ce que les machines virtuelles que vous créez dans le groupe disponible soient isolées sur plusieurs ressources matérielles. Cela signifie que, si le matériel sur lequel s’exécute l’une de vos machines virtuelles serveur web ou serveur de base de données rencontre un problème, vous savez que les autres instances de votre serveur web et de vos machines virtuelles de base de données continueront à s’exécuter correctement parce qu’elles se trouvent sur d’autres éléments matériels.

Vous devez toujours utiliser des groupes à haute disponibilité quand vous souhaitez déployer des solutions fiables basées sur des machines virtuelles dans Azure.


## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Vous pouvez créer un groupe à haute disponibilité à l’aide de la commande [az vm availability-set create](/cli/azure/vm/availability-set#create). Dans cet exemple, nous définissons le nombre de domaines de mise à jour et d’erreur sur *2* pour le groupe à haute disponibilité nommé *myAvailabilitySet* dans le groupe de ressources *myResourceGroupAvailability*.

Créez un groupe de ressources.

```azurecli-interactive 
az group create --name myResourceGroupAvailability --location eastus
```


```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Les groupes à haute disponibilité vous permettent d’isoler des ressources dans des « domaines d’erreur » et des « domaines de mise à jour ». Un **domaine d’erreur** représente une collection isolée comportant un serveur, un réseau et des ressources de stockage. Dans l’exemple précédent, nous indiquons que nous voulons que notre groupe à haute disponibilité soit distribué sur au moins deux domaines d’erreur lors du déploiement de nos machines virtuelles. Nous indiquons également que nous souhaitons que notre groupe à haute disponibilité soit réparti sur deux **domaines de mise à jour**.  Deux domaines de mise à jour garantissent que, quand Azure effectue des mises à jour logicielles, nos ressources de machines virtuelles sont isolées, ce qui évite que tous les logiciels en cours d’exécution sous notre machine virtuelle soient mis à jour au même moment.

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel
Avant de déployer des machines virtuelles et de pouvoir tester votre équilibreur, créez les ressources de réseau virtuel de prise en charge. Pour plus d’informations sur les réseaux virtuels, consultez le didacticiel [Manage Azure Virtual Networks (Gérer les réseaux virtuels Azure)](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Créer des ressources réseau
Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#create). L’exemple suivant crée un réseau virtuel nommé *myVnet* avec un sous-réseau nommé *mySubnet* :

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupAvailability \
    --name myVnet \
    --subnet-name mySubnet
```
Les cartes d’interface réseau virtuelles sont créées avec la commande [az network nic create](/cli/azure/network/nic#create). L’exemple suivant crée trois cartes réseau virtuelles. (Une carte d’interface réseau virtuelle pour chaque machine virtuelle que vous créez pour votre application dans les étapes suivantes). Vous pouvez créer des machines virtuelles et des cartes d’interface réseau virtuelles supplémentaires à tout moment et les ajouter à l’équilibreur de charge :

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupAvailability \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

## <a name="create-vms-inside-an-availability-set"></a>Créer des machines virtuelles dans un groupe à haute disponibilité

Vous devez créer des machines virtuelles au sein du groupe à haute disponibilité pour vous assurer qu’elles sont correctement réparties dans le matériel. Vous ne pouvez pas ajouter une machine virtuelle existante à un groupe à haute disponibilité après sa création. 

Lorsque vous créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm#create), vous spécifiez le nom du groupe à haute disponibilité à l’aide du paramètre `--availability-set`.

```azurecli-interactive 
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --nics myNic$i \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Nous avons à présent deux machines virtuelles à l’intérieur du groupe à haute disponibilité que nous venons de créer. Ces machines virtuelles se trouvant dans le même groupe à haute disponibilité, Azure veille à ce que celles-ci et toutes leurs ressources (dont les disques de données) soient distribuées sur des éléments matériels isolés. Cette distribution contribue à assurer une disponibilité sensiblement plus importante de notre solution globale de machine virtuelle.

Une situation que vous pouvez rencontrer lorsque vous ajoutez des machines virtuelles est qu’une taille de machine virtuelle particulière ne soit plus disponible et que vous ne puissiez pas l’utiliser dans votre groupe à haute disponibilité. Ce problème peut se produire si la capacité n’est plus suffisante pour ajouter la machine virtuelle tout en préservant les règles d’isolation qu’applique le groupe à haute disponibilité. Vous pouvez utiliser le paramètre `--availability-set list-sizes` pour vérifier les tailles de machine virtuelle disponibles utilisables dans un groupe à haute disponibilité existant.

## <a name="check-for-available-vm-sizes"></a>Vérifier les tailles de machines virtuelles disponibles 

Vous pouvez ajouter ultérieurement d’autres machines virtuelles au groupe à haute disponibilité, mais vous devez connaître les tailles des machines virtuelles qui sont disponibles sur le matériel. Utilisez [az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) pour répertorier toutes les tailles disponibles sur le cluster matériel pour le groupe à haute disponibilité.

```azurecli-interactive 
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles

Passez au didacticiel suivant pour en savoir plus sur les groupes de machines virtuelles identiques.

> [!div class="nextstepaction"]
> [Créer un groupe de machines virtuelles identiques](tutorial-create-vmss.md)


