---
title: "Présentation des groupes de machines virtuelles identiques Azure | Microsoft Docs"
description: En savoir plus sur les groupes de machines virtuelles identiques Azure
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/10/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 2071debe24ef2705b2ee05ce1210a813234cf277
ms.lasthandoff: 03/10/2017


---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>À quoi correspondent les groupes de machines virtuelles identiques dans Azure ?
Les groupes de machines virtuelles identiques sont des ressources Azure Compute que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques. Avec toutes les machines virtuelles configurées de la même façon, les groupes identiques sont conçus pour prendre en charge une véritable mise à l’échelle automatique (aucun préapprovisionnement de machine virtuelle n’est nécessaire) et faciliter ainsi la création de services à grande échelle pour le big compute, le big data et les charges de travail en conteneurs.

Pour les applications nécessitant une mise à l’échelle des ressources de calcul internes et externes, les opérations de mise à l’échelle sont équilibrées de façon implicite sur plusieurs domaines d’erreur et de mise à jour. Pour accéder à une présentation des groupes identiques, reportez-vous à [l’Annonce du blog Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Regardez ces vidéos pour en savoir plus sur les groupes identiques :

* [Mark Russinovich parle des groupes identiques Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Jeux de mise à l’échelle de machine virtuelle, avec Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>Création et gestion des groupes identiques
Vous pouvez créer un groupe identique sur le [Portail Azure](https://portal.azure.com) en sélectionnant *Nouveau* et en tapant « identique » dans la barre de recherche. « Groupe de machines virtuelles identiques » apparaîtra dans les résultats. À partir de là, vous pourrez renseigner les champs obligatoires pour personnaliser et déployer votre groupe identique. Notez que le portail propose également des options pour configurer les règles de mise à l’échelle automatique de base en fonction de l’utilisation du processeur.

Les groupes identiques peuvent également être définis et déployés à l’aide de modèles JSON et [d’API REST](https://msdn.microsoft.com/library/mt589023.aspx), tout comme des machines virtuelles Azure Resource Manager individuelles. Par conséquent, les méthodes de déploiement d’Azure Resource Manager standard peuvent être utilisées. Pour en savoir plus sur les modèles, consultez [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Vous trouverez des modèles d’exemple de groupes de machines virtuelles identiques dans le dépôt GitHub des modèles de démarrage rapide Azure, [ici](https://github.com/Azure/azure-quickstart-templates) (recherchez les modèles dont le titre contient *vmss*).

Dans les pages de détail correspondant à ces modèles, vous pouvez voir un bouton qui est associé à la fonction de déploiement de portail. Pour déployer le groupe identique, cliquez sur le bouton et renseignez ensuite tous les paramètres requis dans le portail. Si vous ne savez pas si une ressource prend en charge les majuscules ou les casses mixtes, il est préférable de toujours utiliser des lettres en minuscules ou des chiffres dans les valeurs de paramètre. Il existe également une pratique de dissection vidéo de groupe identique ici :

[Dissection des modèles de groupe de machines virtuelles identiques](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-scale-set-out-and-in"></a>Mise à l’échelle d’un groupe identique (augmentation et diminution)
Vous pouvez modifier la capacité d’un groupe identique dans le portail Azure en cliquant sur la section _Mise à l’échelle_ sous _Paramètres_. 

Pour modifier la capacité de groupe identique sur la ligne de commande, [l’interface de ligne de commande Azure](https://github.com/Azure/azure-cli) fournit une commande _Mise à l’échelle_. Par exemple, pour définir un groupe identique sur une capacité de 10 machines virtuelles, procédez comme suit :

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Pour définir le nombre de machines virtuelles dans un groupe identique à l’aide de PowerShell, utilisez la commande _Update-AzureRmVmss_ :

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Pour augmenter ou diminuer le nombre de machines virtuelles dans un groupe identique à l’aide d’un modèle Azure Resource Manager, il suffit de changer la propriété *capacité* et de redéployer le modèle. Cette simplicité rend plus facile l’intégration de groupes identiques à la mise à l’échelle automatique Azure ou l’écriture de votre couche de mise à l’échelle personnalisée si vous devez définir des événements d’échelle personnalisée qui ne sont pas pris en charge par la mise à l’échelle automatique Azure. 

Si vous redéployez un modèle Azure Resource Manager pour modifier la capacité, vous pouvez définir un modèle beaucoup plus petit qui inclut uniquement le paquet de propriété SKU et la capacité mise à jour. Vous trouverez un exemple [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).

## <a name="autoscale"></a>Autoscale

Un groupe identique peut éventuellement être configuré avec les paramètres de mise à l’échelle lorsqu’il est créé dans le portail Azure, ce qui permet d’augmenter ou de diminuer le nombre de machines virtuelles en fonction de l’utilisation moyenne de l’UC. La plupart des groupes identiques dans [Modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates) définissent les paramètres de mise à l’échelle automatique. Vous pouvez également ajouter des paramètres de mise à l’échelle automatique à un groupe identique existant. Par exemple, voici un script Azure PowerShell permettant d’ajouter une mise à l’échelle automatique basée sur l’UC au groupe identique :

```PowerShell

$subid = "yoursubscriptionid"
$rgname = "yourresourcegroup"
$vmssname = "yourscalesetname"
$location = "yourlocation" # e.g. southcentralus

$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionValue 1
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator LessThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Decrease -ScaleActionValue 1
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "autoprofile1"
Add-AzureRmAutoscaleSetting -Location $location -Name "autosetting1" -ResourceGroup $rgname -TargetResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -AutoscaleProfiles $profile1
```

 Vous trouverez la liste des mesures valides pour une mise à l’échelle ici : [Mesures prises en charge avec Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md) sous le titre _Microsoft.Compute/virtualMachineScaleSets_. Des options de mise à l’échelle automatique plus avancées sont également disponibles, y compris des mises à l’échelle automatiques basées sur la planification, ainsi que l’utilisation de webhooks pour s’intégrer aux systèmes d’alerte.

## <a name="monitoring-your-scale-set"></a>Surveillance de votre groupe identique
Le [portail Azure](https://portal.azure.com) répertorie les groupes identiques et affiche leurs propriétés. Le portail prend également en charge les opérations de gestion, qui peuvent être exécutées à la fois sur les groupes identiques et sur des machines virtuelles individuelles au sein d’un groupe identique. Le portail fournit également un graphique d’utilisation des ressources personnalisable. Si vous souhaitez afficher ou modifier la définition JSON sous-jacente d’une ressource Azure, vous pouvez également utiliser [Azure Resource Explorer](https://resources.azure.com). Les groupes identiques sont des ressources sous Microsoft.Compute Azure Resource Provider, vous pouvez donc les voir à partir de ce site en développant les liens suivants :

**Abonnements -> votre abonnement -> resourceGroups -> fournisseurs -> Microsoft.Compute -> virtualMachineScaleSets -> votre groupe identique -> etc.**

## <a name="scale-set-scenarios"></a>Scénarios de groupe identique
Cette section répertorie quelques scénarios de groupe identique classiques. Certains services Azure de niveau supérieur (comme Batch, Service Fabric, Service de conteneur Azure) utilisent également ces scénarios.

* **RDP/SSH vers des instances de groupe identique** - Un groupe identique est créé dans un réseau virtuel et les machines virtuelles individuelles du groupe ne se voient pas allouer d’adresses IP publiques. Cette stratégie évite la surcharge de gestion et de coût liée à l’attribution d’adresses IP publiques distinctes sur tous les nœuds dans la grille de calcul. Vous pouvez vous connecter à ces machines virtuelles à partir d’autres ressources dans votre réseau virtuel, par exemple les équilibreurs de charge et les machines virtuelles autonomes, qui peuvent être alloués à des adresses IP publiques.
* **Se connecter à des machines virtuelles à l’aide de règles NAT** - Vous pouvez créer une adresse IP publique, l’affecter à un équilibreur de charge, puis définir un pool de règles NAT entrantes qui mappent les ports de l’adresse IP sur un port de machine virtuelle, dans le groupe identique. Par exemple :
  
  | Source | Port source | Destination | Port de destination |
  | --- | --- | --- | --- |
  |  Adresse IP publique |Port 50000 |vmss\_0 |Port 22 |
  |  Adresse IP publique |Port 50001 |vmss\_1 |Port 22 |
  |  Adresse IP publique |Port 50002 |vmss\_2 |Port 22 |
  
   Dans cet exemple, les règles NAT sont définies pour activer la connexion SSH à chaque machine virtuelle dans un groupe identique à l’aide d’une adresse IP publique unique : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Voici un exemple faisant la même chose avec RDP et Windows : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Connectez-vous à la machine virtuelle à l’aide d’une « jumpbox »** -Si vous créez un groupe identique et une machine virtuelle autonome dans le même réseau virtuel, la machine virtuelle autonome et les machines virtuelles de groupe identique peuvent se connecter entre elles en utilisant leurs adresses IP internes comme défini par le réseau virtuel/sous-réseau. Si vous créez une adresse IP publique et l’affectez à la machine virtuelle autonome, vous pouvez lancer un RDP ou un SSH sur la machine virtuelle autonome, puis vous connecter depuis cette machine à vos instances de groupe identique. À ce stade, vous pouvez remarquer qu’un simple groupe identique est intrinsèquement plus sûr qu’une machine virtuelle autonome simple avec une adresse IP publique dans sa configuration par défaut.
  
   Par exemple, ce modèle déploie un groupe identique simple comportant une machine virtuelle autonome : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **Équilibrage de charge pour les instances de groupe identique** - Si vous souhaitez remettre un travail à un cluster de calcul de machines virtuelles selon une approche de type « tourniquet », vous pouvez configurer un équilibreur de charge Azure avec des règles d’équilibrage de charge de couche 4. Vous pouvez définir des sondes pour vérifier que votre application s’exécute en envoyant des requêtes ping aux ports, en spécifiant un protocole, un intervalle et un chemin d’accès à la demande. Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) prend également en charge les groupes identiques de machines virtuelles, ainsi que des scénarios d’équilibrage de charge de couche 7 et plus.
  
   Cet exemple crée un groupe identique exécutant des serveurs web Apache, et utilise un équilibrage de charge pour équilibrer la charge de chaque machine virtuelle : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (Vérifiez le type de ressource Microsoft.Network/loadBalancers et les éléments networkProfile et extensionProfile dans virtualMachineScaleSet)

   Cet exemple utilise Application Gateway. Linux :  [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway). Windows : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) 

* **Déploiement d’un groupe identique en tant que cluster de calcul dans un gestionnaire de cluster PaaS** - les groupes identiques sont parfois décrits en tant que rôle de travail de la prochaine génération. Bien qu’il s’agisse d’une description valide, il est possible de confondre les fonctionnalités du groupe identique avec les fonctionnalités d’Azure Cloud Services. Dans un sens, les groupes identiques fournissent un véritable « rôle de travail » ou une véritable ressource de travail. En effet, ils représentent une ressource de calcul généralisée, indépendante de la plateforme et du runtime, personnalisable et qui s’intègre à l’environnement IaaS Azure Resource Manager.
  
   Un rôle de travail Cloud Services, limité en termes de prise en charge de plateforme/runtime (images de plateforme Windows) inclut également les services d’échange d’adresse IP virtuelle, de paramètres de mise à niveau configurables, de paramètres spécifiques de runtime/déploiement d’application qui ne sont pas *encore* disponibles dans les groupes identiques ou sont fournis par des services PaaS de plus haut niveau tels que Service Fabric. Vous pouvez envisager les groupes identiques comme une infrastructure prenant en charge PaaS. Les solutions PaaS, telles que [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/), s’appuient sur cette infrastructure.
  
   Pour obtenir un exemple de cette approche, le service [Azure Container Service](https://azure.microsoft.com/services/container-service/) déploie un cluster basé sur les groupes identiques avec un orchestrateur de conteneur : [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).

## <a name="scale-set-performance-and-scale-guidance"></a>Performance de groupe identique et guide de mise à l’échelle
* Les groupes identiques peuvent prendre en charge jusqu’à 1 000 machines virtuelles dans un groupe. Si vous créez et téléchargez vos propres images de machine virtuelle personnalisées, la limite est de 100. Pour connaître les aspects à prendre en compte en matière d’utilisation de grands groupes identiques, voir [Utilisation de grands groupes de machines virtuelles identiques](virtual-machine-scale-sets-placement-groups.md).
* Vous n’êtes pas obligé de créer au préalable des comptes Azure Storage pour utiliser les groupes identiques. Les groupes identiques prennent en charge Azure Managed Disks, qui élimine les problèmes de performances liés au nombre de disques par compte de stockage. Pour plus d’informations, voir [Groupes de machines virtuelles identiques et disques gérés Azure](virtual-machine-scale-sets-managed-disks.md).
* Envisagez d’utiliser Azure Premium Storage plutôt que le stockage standard afin d’optimiser les temps d’approvisionnement des machines virtuelles et les performances d’E/S.
* Le nombre de machines virtuelles que vous pouvez créer est limité par le quota de base dans la région dans laquelle vous déployez. Vous devrez peut-être contacter le support technique pour augmenter votre limite de quota de calcul augmentée, même si vous avez une limite de cœurs haute à utiliser avec vos services cloud Azure dès aujourd’hui. Pour interroger votre quota, vous pouvez exécuter la commande CLI Azure suivante : `azure vm list-usage`, et la commande PowerShell suivante : `Get-AzureRmVMUsage` (si vous utilisez une version de PowerShell inférieure à 1.0, utilisez `Get-AzureVMUsage`).

## <a name="scale-set-frequently-asked-questions"></a>Groupe identique - Forum aux Questions
**Q.** Combien de machines virtuelles peut-il y avoir dans un groupe identique ?

**A.** Un groupe identique peut contenir de 0 à 1 000 machines virtuelles à partir d’images de plateforme, ou de 0 à 100 machines virtuelles à partir d’images personnalisées. 

**Q.** Les disques de données sont-ils pris en charge dans les groupes identiques ?

**A.** Oui. Un groupe identique peut définir une configuration de disques de données attachés qui s’appliquera à toutes les machines virtuelles du groupe. Pour plus d’informations, voir (Groupes identiques Azure et disques de données associés)[virtual-machine-scale-sets-attached-disks.md]. Il existe d’autres options pour stocker les données :

* Fichiers Azure (lecteurs SMB partagés)
* Système d’exploitation de lecteur
* Lecteur temp (local, non sauvegardé par le stockage Azure)
* Service de données Azure (par exemple, tables Azure, objets blob Azure)
* Service de données externe (par exemple, base de données distante)

**Q.** Quelles sont les régions Azure qui prennent en charge les groupes identiques ?

**A.** Toutes les régions prennent en charge les groupes identiques.

**Q.** Comment créer un groupe identique à l’aide d’une image personnalisée ?

**A.** Créez un disque géré en fonction de votre image personnalisée de disque dur virtuel et référencez-le dans votre modèle de groupe identique. Voici un exemple : [https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**Q.** Si je réduis ma capacité de groupe identique de 20 à 15, quelles sont les machines virtuelles qui seront supprimées ?

**A.** Les machines virtuelles sont supprimées du groupe uniformément entre les domaines de mise à niveau et les domaines d’erreur pour optimiser la disponibilité. Les machines virtuelles avec les ID les plus élevés sont supprimées en premier.

**Q.** Que diriez-vous si j’augmente par la suite la capacité de 15 à 18 ?

**A.** Si vous augmentez la capacité à 18, 3 machines virtuelles sont créées. À chaque fois, l’ID d’instance de la machine virtuelle est incrémenté avec la valeur la plus élevée précédente (par exemple, 20, 21, 22). Les machines virtuelles sont réparties entre FD et UD.

**Q.** Lorsque vous utilisez plusieurs extensions dans un groupe identique, puis-je appliquer une séquence d’exécution ?

**A.** Pas directement, mais pour l’extension customScript, votre script peut attendre une autre extension avant de s’exécuter ([par exemple en surveillant le journal d’extension](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Vous trouverez des conseils supplémentaires sur le séquencement d’extensions dans ce billet de blog : [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)(Séquencement d’extensions dans les groupes identiques de machines virtuelles Azure).

**Q.** Les groupes identiques fonctionnent-ils avec des ensembles haute disponibilité Azure ?

**A.** Oui. Un groupe identique est un ensemble de disponibilité comprenant 5 FD et 5 UD. Les groupes identiques de plus de 100 machines virtuelles couvrent plusieurs « groupes de placement » qui équivalent à plusieurs groupes à haute disponibilité. Pour plus d’informations sur les groupes de placement, voir [Working with large virtual machine scale sets](virtual-machine-scale-sets-placement-groups.md) (Utilisation de grands groupes de machines virtuelles identiques). Un groupe de machines virtuelles à haute disponibilité peut figurer dans le même réseau virtuel qu’un groupe identique de machines virtuelles. Une configuration courante consiste à placer les machines virtuelles du nœud de contrôle qui nécessitent souvent une configuration unique dans un groupe à haute disponibilité et les nœuds de données dans le groupe identique.

Pour connaître les questions les plus fréquemment posées sur les groupes identiques, voir [FAQ sur les groupes de machines virtuelles identiques Azure](virtual-machine-scale-sets-faq.md).

