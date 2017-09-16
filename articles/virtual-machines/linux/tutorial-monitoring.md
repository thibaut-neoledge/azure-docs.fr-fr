---
title: Surveiller des machines virtuelles Linux dans Azure | Microsoft Docs
description: "Découvrez comment surveiller les diagnostics de démarrage et les métriques de performances sur une machine virtuelle Linux dans Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: c5eab88f1b2311d52e582a0aa1121c8001437376
ms.contentlocale: fr-fr
ms.lasthandoff: 09/09/2017

---
# <a name="how-to-monitor-a-linux-virtual-machine-in-azure"></a>Guide pratique pour surveiller une machine virtuelle Linux dans Azure

Pour vérifier que vos machines virtuelles dans Azure fonctionnent correctement, vous pouvez consulter les diagnostics de démarrage et les métriques de performances. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer les diagnostics de démarrage sur la machine virtuelle
> * Afficher les diagnostics de démarrage
> * Afficher les métriques de l’hôte
> * Activer l’extension Diagnostics sur la machine virtuelle
> * Afficher les métriques de la machine virtuelle
> * Créer des alertes en fonction des métriques des diagnostics
> * Configurer la surveillance avancée


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-vm"></a>Créer une machine virtuelle

Pour voir les diagnostics et les métriques en action, vous avez besoin d’une machine virtuelle. Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupMonitor* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

Créez maintenant une machine virtuelle avec la commande [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVM* :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Activer les diagnostics de démarrage

Au démarrage des machines virtuelles Linux, l’extension Diagnostics de démarrage capture la sortie du démarrage et la stocke dans le stockage Azure. Ces données peuvent être utilisées pour résoudre les problèmes de démarrage des machines virtuelles. Les diagnostics de démarrage ne sont pas activés automatiquement quand vous créez une machine virtuelle Linux à l’aide d’Azure CLI.

Avant d’activer les diagnostics de démarrage, vous devez créer un compte de stockage pour stocker les journaux de démarrage. Les comptes de stockage doivent avoir un nom global unique, comprenant entre 3 et 24 caractères, et contenant seulement des chiffres et des lettres minuscules. Créez un compte de stockage avec la commande [az storage account create](/cli/azure/storage/account#create). Dans cet exemple, une chaîne aléatoire est utilisée pour créer un nom de compte de stockage unique. 

```azurecli-interactive 
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Lors de l’activation des diagnostics de démarrage, l’URI vers le conteneur de stockage d’objets blob est nécessaire. La commande suivante interroge le compte de stockage et retourne cet URI. La valeur de l’URI est stockée dans une variable nommée *bloburi*, qui est utilisé à l’étape suivante.

```azurecli-interactive 
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Activez maintenant les diagnostics de démarrage avec la commande [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). La valeur de `--storage` est l’URI de l’objet blob collecté à l’étape précédente.

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>Afficher les diagnostics de démarrage

Quand les diagnostics de démarrage sont activés, chaque fois que vous arrêtez et que vous démarrez la machine virtuelle, des informations sur le processus de démarrage sont écrites dans un fichier journal. Pour cet exemple, désallouez d’abord la machine virtuelle avec la commande [az vm deallocate](/cli/azure/vm#deallocate) comme suit :

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Démarrez maintenant la machine virtuelle avec la commande [az vm start]( /cli/azure/vm#stop) comme suit :

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Vous pouvez obtenir les données des diagnostics de démarrage pour *myVM* avec la commande [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) comme suit :

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>Afficher les métriques de l’hôte

Une machine virtuelle Linux a un hôte dédié dans Azure qui interagit avec elle. Les métriques sont automatiquement collectées pour l’hôte et peuvent être visualisées dans le portail Azure comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroupMonitor** puis sélectionnez **myVM** dans la liste des ressources.
1. Pour voir comment la machine virtuelle hôte fonctionne, cliquez sur **Métriques** dans le panneau de la machine virtuelle, puis sélectionnez une des métriques de *[hôte]* sous **Métriques disponibles**.

    ![Afficher les métriques de l’hôte](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>Installer l’extension Diagnostics

> [!IMPORTANT]
> Ce document décrit la version 2.3 de l’extension de diagnostic Linux, qui est déconseillée. La version 2.3 sera prise en charge jusqu’au 30 juin 2018.
>
> La version 3.0 de l’extension de diagnostic Linux peut être activée à la place. Pour plus d’informations, consultez [la documentation](./diagnostic-extension.md).

Les métriques de base de l’hôte sont disponibles, mais pour voir des métriques plus précises et spécifiques à la machine virtuelle, vous devez installer l’extension Diagnostics Azure sur la machine virtuelle. L’extension Diagnostics Azure permet de récupérer des données supplémentaires de surveillance et de diagnostic auprès de la machine virtuelle. Vous pouvez voir ces métriques de performances et créer des alertes basées sur le fonctionnement de la machine virtuelle. L’extension Diagnostics est installée via le portail Azure comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
1. Cliquez sur **Paramètres de diagnostic**. La liste montre que les *Diagnostics de démarrage* sont déjà activés depuis la section précédente. Cliquez sur la case à cocher *Métriques de base*.
1. Dans la section *Compte de stockage*, recherchez et sélectionnez le compte *mydiagdata[1234]* créé dans la section précédente.
1. Cliquez sur le bouton **Enregistrer** .

    ![Afficher les métriques de diagnostic](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>Afficher les métriques de la machine virtuelle

Vous pouvez afficher les métriques de la machine virtuelle de la même façon que vous avez affiché le mesures de la machine virtuelle hôte :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
1. Pour voir comment la machine virtuelle fonctionne, cliquez sur **Métriques** dans le panneau de la machine virtuelle puis sélectionnez une des métriques de diagnostic sous **Métriques disponibles**.

    ![Afficher les métriques de la machine virtuelle](./media/tutorial-monitoring/monitor-vm-metrics.png)


## <a name="create-alerts"></a>Créez des alertes

Vous pouvez créer des alertes en fonction de métriques de performances spécifiques. Les alertes peuvent être utilisées par exemple pour notifier que l’utilisation moyenne de l’UC dépasse un certain seuil ou que l’espace disque disponible est inférieur à une certaine quantité. Les alertes sont affichées dans le portail Azure ou peuvent être envoyées par e-mail. Vous pouvez également déclencher des runbooks Azure Automation ou Azure Logic Apps en réponse aux alertes générées.

L’exemple suivant crée une alerte pour l’utilisation moyenne de l’UC.

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
2. Cliquez sur **Règles d’alerte** dans le panneau de la machine virtuelle puis cliquez sur **Ajouter une alerte Métrique** dans la partie supérieure du panneau des alertes.
4. Spécifiez un **Nom** pour votre alerte, comme *myAlertRule*
5. Pour déclencher une alerte quand le pourcentage d’UC dépasse 1,0 pendant cinq minutes, laissez toutes les autres valeurs par défaut sélectionnées.
6. Si vous le souhaitez, cochez la case *Envoyer un e-mail aux propriétaires, aux contributeurs et aux lecteurs* pour envoyer la notification par e-mail. L’action par défaut est de présenter une notification dans le portail.
7. Cliquez sur le bouton **OK**.


## <a name="advanced-monitoring"></a>Surveillance avancée 

Vous pouvez faire une surveillance plus avancée de votre machine virtuelle en utilisant [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Si vous ne l’avez pas déjà fait, vous pouvez vous inscrire pour une [version d’évaluation gratuite](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) d’Operations Management Suite.

Quand vous avez accès au portail OMS, vous pouvez trouver la clé de l’espace de travail et l’identificateur de l’espace de travail dans le panneau Paramètres. Remplacez <workspace-key> et <workspace-id> par les valeurs correspondant à votre espace de travail OMS et utilisez ensuite **az vm extension set** pour ajouter l’extension OMS à la machine virtuelle :

```azurecli-interactive 
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

Dans le panneau Recherche dans les journaux du portail OMS, vous devez normalement voir *myVM*, comme illustré dans l’image suivante :

![Panneau OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré et examiné des machines virtuelles avec Azure Security Center. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Activer les diagnostics de démarrage sur la machine virtuelle
> * Afficher les diagnostics de démarrage
> * Afficher les métriques de l’hôte
> * Activer l’extension Diagnostics sur la machine virtuelle
> * Afficher les métriques de la machine virtuelle
> * Créer des alertes en fonction des métriques des diagnostics
> * Configurer la surveillance avancée

Passez au didacticiel suivant pour découvrir plus d’informations sur Azure Security Center.

> [!div class="nextstepaction"]
> [Gérer la sécurité des machines virtuelles](./tutorial-azure-security.md)
