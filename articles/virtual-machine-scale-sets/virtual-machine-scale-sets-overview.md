---
title: "Présentation des jeux de mise à l’échelle de machines virtuelles | Microsoft Docs"
description: "En savoir plus sur les jeux de mise à l’échelle de machine virtuelle"
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
ms.date: 11/15/2016
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 6fb71859d0ba2e0f2b39d71edd6d518b7a03bfe9
ms.openlocfilehash: 4a2cd02f6f9b6ac51c32314ce892e572e569eb7c


---
# <a name="virtual-machine-scale-sets-overview"></a>Présentation des jeux de machines virtuelles mise à l’échelle
Les groupes de machines virtuelles identiques sont des ressources Azure Compute que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques. Avec toutes les machines virtuelles configurées de la même façon, les groupes de machines virtuelles identiques sont conçus pour prendre en charge une véritable mise à l’échelle automatique (aucun préapprovisionnement de machine virtuelle n’est nécessaire) et faciliter ainsi la création de services à grande échelle pour le big compute, le big data et les charges de travail en conteneurs.

Pour les applications nécessitant une mise à l’échelle des ressources de calcul internes et externes, les opérations de mise à l’échelle sont équilibrées de façon implicite sur plusieurs domaines d’erreur et de mise à jour. Pour accéder à une présentation des groupes identiques de machines virtuelles, reportez-vous à [l’Annonce du blog Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Regardez ces vidéos pour en savoir plus sur les groupes de machines virtuelles identiques :

* [Mark Russinovich parle des jeux de mise à l’échelle Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Jeux de mise à l’échelle de machine virtuelle, avec Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Création et gestion des groupes de machines virtuelles identiques
Vous pouvez créer un groupe identique de machines virtuelles sur le [Portail Azure](https://portal.azure.com) en sélectionnant *Nouveau* et en tapant « identique » dans la barre de recherche. « groupe identique de machines virtuelles » apparaîtra dans les résultats. À partir de là, vous pourrez renseigner les champs obligatoires pour personnaliser et déployer votre groupe identique. 

Les groupes identiques de machines virtuelles peuvent également être définis et déployés à l’aide de modèles JSON et [d’API REST](https://msdn.microsoft.com/library/mt589023.aspx) , tout comme des machines virtuelles Azure Resource Manager individuelles. Par conséquent, les méthodes de déploiement d’Azure Resource Manager standard peuvent être utilisées. Pour en savoir plus sur les modèles, consultez [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Vous trouverez des modèles d’exemple de groupes identiques de machines virtuelles dans le dépôt GitHub des modèles de démarrage rapide Azure, [ici](https://github.com/Azure/azure-quickstart-templates) (recherchez les modèles dont le titre contient *vmss*)

Dans les pages de détail correspondant à ces modèles, vous pouvez voir un bouton qui est associé à la fonction de déploiement de portail. Pour déployer le jeu de mise à l’échelle de machine virtuelle, cliquez sur le bouton et renseignez ensuite tous les paramètres requis dans le portail. Si vous ne savez pas si une ressource prend en charge les majuscules ou les casses mixtes, il est préférable d’utiliser toujours les valeurs de paramètre en minuscules. Il existe également une pratique de dissection vidéo d’un modèle d’ensemble de mise à l’échelle de machine virtuelle ici :

[Dissection des modèles de jeu de mise à l’échelle de machine virtuelle](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Mise à l’échelle d’un jeu de mise à l’échelle de machine virtuelle
Pour augmenter ou diminuer le nombre de machines virtuelles dans un jeu de mise à l’échelle de machine virtuelle, il suffit de changer la propriété *capacité* et de redéployer le modèle. Cette simplicité rend plus facile l’écriture de votre couche de mise à l’échelle personnalisée si vous souhaitez définir des événements d’échelle personnalisée qui ne sont pas pris en charge par l’échelle automatique Azure.

Si vous redéployez un modèle pour modifier la capacité, vous pouvez définir un modèle beaucoup plus petit incluant uniquement la référence SKU et la capacité mise à jour. Un exemple est présenté [ici.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)

Pour être guidé parmi les étapes qui crée un jeu de mise à l’échelle automatique, consultez les [machines de mise à l’échelle automatique dans un jeu de mise à l’échelle de machine virtuelle](virtual-machine-scale-sets-windows-autoscale.md)

## <a name="monitoring-your-vm-scale-set"></a>Surveillance de votre groupe de machines virtuelles identiques
Le [portail Azure](https://portal.azure.com) répertorie les groupes identiques et affiche les propriétés et opérations de base, dont la liste des machines virtuelles contenues dans le groupe et un graphique d’utilisation des ressources. Pour obtenir plus de détails, vous pouvez utiliser [Azure Resource Explorer](https://resources.azure.com) pour afficher les groupes identiques de machines virtuelles. Les groupes de machines virtuelles identiques sont des ressources sous Microsoft.Compute, vous pouvez donc les voir à partir de ce site en développant les liens suivants :

**Abonnements -> votre abonnement -> resourceGroups -> fournisseurs -> Microsoft.Compute -> virtualMachineScaleSets -> votre groupe de machines virtuelles identiques -> etc.**

## <a name="vm-scale-set-scenarios"></a>Scénarios de jeu de mise à l’échelle de machine virtuelle
Cette section répertorie quelques scénarios de jeu de mise à l’échelle de machine virtuelle classique. Certains services Azure de niveau supérieur (comme Batch, Service Fabric, Service de conteneur Azure) utilisent également ces scénarios.

* **RDP/SSH vers des instances de groupes identiques de machines virtuelles** - Un groupe identique de machines virtuelles est créé dans un réseau virtuel et les machines virtuelles individuelles du groupe ne se voient pas allouer d’adresses IP publiques. C’est préférable car vous ne souhaiterez pas être soumis à des frais supplémentaires liés à l’affectation d’adresses IP publiques séparées à toutes les ressources sans état dans la grille de calcul. De plus, vous pouvez facilement vous connecter à ces machines virtuelles à partir d’autres ressources de votre réseau virtuel, notamment celles qui ont des adresses IP publiques telles que les équilibreurs de charge ou les machines virtuelles autonomes.
* **Se connecter à des machines virtuelles à l’aide de règles NAT** - Vous pouvez créer une adresse IP publique, l’affecter à un équilibreur de charge et définir des règles NAT entrantes qui mappent un port de l’adresse IP sur un port de machine virtuelle dans le jeu de mise à l’échelle de machine virtuelle. Par exemple :
  
  | Source | Port source | Destination | Port de destination |
  | --- | --- | --- | --- |
  |  Adresse IP publique |Port 50000 |vmss\_0 |Port 22 |
  |  Adresse IP publique |Port 50001 |vmss\_1 |Port 22 |
  |  Adresse IP publique |Port 50002 |vmss\_2 |Port 22 |
  
   Voici un exemple de création d’un jeu de mise à l’échelle de machine virtuelle qui utilise les règles NAT pour activer la connexion SSH à chaque machine virtuelle dans une échelle à l’aide d’une adresse IP publique unique : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Voici un exemple faisant la même chose avec RDP et Windows : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Connectez-vous à la machine virtuelle à l’aide d’une « jumpbox »** -Si vous créez un jeu de mise à l’échelle de machine virtuelle et un ordinateur virtuel autonome dans le même réseau virtuel, la machine virtuelle autonome et les machines virtuelles de jeu de mise à l’échelle de machine virtuelle peuvent se connecter entre elles en utilisant leurs adresses IP internes comme défini par le réseau virtuel/sous-réseau. Si vous créez une adresse IP publique et l’affectez à la machine virtuelle autonome, vous pouvez lancer un RDP ou un SSH sur la machine virtuelle autonome, puis vous connecter depuis cette machine à vos instances de jeu de mise à l’échelle de machine virtuelle. À ce stade, vous pouvez remarquer qu’un simple groupe de machines virtuelles identiques est intrinsèquement plus sûr qu’une machine virtuelle autonome simple avec une adresse IP publique dans sa configuration par défaut.
  
   Par exemple, ce modèle déploie un groupe identique simple comportant une machine virtuelle autonome : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **Équilibrage de charge pour les instances de groupes identiques de machines virtuelles** - Si vous souhaitez remettre un travail à un cluster de calcul de machines virtuelles selon une approche de type « tourniquet », vous pouvez configurer un équilibreur de charge Azure avec des règles d’équilibrage de charge appropriées. Vous pouvez définir des sondes pour vérifier que votre application s’exécute en envoyant des requêtes ping aux ports, en spécifiant un protocole, un intervalle et un chemin d'accès à la demande. Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) prend également en charge les groupes identiques de machines virtuelles, ainsi que des scénarios d’équilibrage de charge plus sophistiqués.
  
   Voici un exemple qui crée un groupe de machines virtuelles identiques exécutant des serveurs web Apache, et utilise un équilibrage de charge pour équilibrer la charge de chaque machine virtuelle : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (Vérifiez le type de ressource Microsoft.Network/loadBalancers et les éléments networkProfile et extensionProfile dans virtualMachineScaleSet).
* **Déploiement d’un jeu de mise à l’échelle en tant que cluster de calcul dans un gestionnaire de cluster PaaS** - les jeux de mise à l’échelle de machine virtuelle sont parfois décrits en tant que rôle de travail de la prochaine génération. Il s’agit d’une description valide, mais elle peut être confondue avec les fonctionnalités du rôle de travail PaaS v1. Dans un sens, les jeux de mise à l’échelle de machine virtuelle fournissent un véritable « rôle » ou une ressource de travail, car ils fournissent une ressource de calcul générale indépendante de la plateforme/runtime, personnalisable et s’intègrent au Gestionnaire de ressources IaaS Azure.
  
   Un rôle de travail PaaS v1, limité en termes de prise en charge de plateforme/runtime (images de plateforme Windows) inclut également les services d’échange d’adresse IP virtuelle, de paramètres de mise à niveau configurables, de paramètres spécifiques de runtime/déploiement d’application qui ne sont pas *encore* disponibles dans les jeux de mise à l’échelle de machine virtuelle ou seront fournis par des services PaaS de plus haut niveau tels que Service Fabric. Avec cela à l’esprit, vous pouvez considérer les jeux de mise à l’échelle de machine virtuelle comme une infrastructure qui prend en charge de PaaS. Par exemple, des solutions PaaS telles que Service Fabric ou gestionnaires de cluster comme Mesos peuvent se construire sur des groupes de machines virtuelles identiques en tant que couche de calcul évolutive.
  
   Pour obtenir un exemple de cette approche, le service Azure Container Service déploie un cluster basé sur les groupes identiques avec un orchestrateur de conteneur : [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).

## <a name="vm-scale-set-performance-and-scale-guidance"></a>La performance de mise à l’échelle de machine virtuelle et guide de mise à l’échelle
* Ne créez pas plus de 500 machines virtuelles dans plusieurs groupes de machines virtuelles identiques à la fois.
* Ne prévoyez pas plus de 20 machines virtuelles par compte de stockage (sauf si vous définissez la propriété *overprovision* sur « false », auquel cas vous pouvez en prévoir jusqu’à 40).
* Fractionnez le plus possible les premières lettres des noms de compte de stockage.  Les modèles VMSS exemple [modèles Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/) fournissent des exemples montrant comment effectuer cette opération.
* Si vous utilisez des machines virtuelles personnalisées, ne prévoyez pas plus de 40 machines virtuelles par jeu de mise à l’échelle de machine virtuelle, dans un seul compte de stockage.  Vous aurez besoin de l’image pré copiée dans le compte de stockage avant de pouvoir commencer le déploiement du jeu de mise à l’échelle de machine virtuelle. Visitez le FAQ pour plus d’informations.
* Ne prévoyez pas plus de 4096 machines virtuelles par réseau virtuel.
* Le nombre de machines virtuelles que vous pouvez créer est limité par le quota de base dans la région dans laquelle vous déployez. Vous devrez peut-être contacter le support technique pour augmenter votre limite de quota de calcul augmentée, même si vous avez une limite de cœurs haute à utiliser avec vos services cloud ou IaaS v1 dès aujourd’hui. Pour interroger votre quota, vous pouvez exécuter la commande CLI Azure suivante : `azure vm list-usage`, et la commande PowerShell suivante : `Get-AzureRmVMUsage` (si vous utilisez une version de PowerShell inférieure à 1.0, utilisez `Get-AzureVMUsage`).

## <a name="vm-scale-set-frequently-asked-questions"></a>jeu de mise à l’échelle de machine virtuelle - Forum aux Questions.
**Q.**  Combien de machines virtuelles peut-il y avoir dans un jeu de mise à l’échelle de machine virtuelle ?

**A.**  100 si vous utilisez des images de plateforme qui peuvent être distribuées sur plusieurs comptes de stockage. Si vous utilisez des images personnalisées, jusqu’à 40 (si la propriété *overprovision* est définie sur « false », sinon 20 par défaut), étant donné que les images personnalisées sont actuellement limitées à un seul compte de stockage.

**Q** Quelles sont les autres limites de ressource qui existent pour les jeux de mise à l’échelle de machine virtuelle ?

**A.**  Vous êtes limité à la création de seulement 500 machines virtuelles dans plusieurs groupes identiques par région pendant une période de 10 minutes. Les [limites du Service d’abonnement Azure /](../azure-subscription-service-limits.md) s’appliquent.

**Q.**  Les disques de données sont-ils pris en charge dans les jeux de mise à l’échelle de machine virtuelle ?

**A.** Pas dans la version initiale (même si les disques de données sont actuellement disponibles en version préliminaire). Vos options de stockage des données sont :

* Fichiers Azure (lecteurs SMB partagés)
* Système d’exploitation de lecteur
* Lecteur temp (local, non sauvegardé par le stockage Azure)
* Service de données Azure (tables Azure, objets blob Azure)
* Service de données externe (base de données distante)

**Q.**  Quelles sont les régions Azure qui prennent en charge les groupes de machines virtuelles identiques ?

**A.**  N’importe quelle région qui prend en charge Azure Resource Manager prend en charge les jeux de mise à l’échelle de machine virtuelle.

**Q.**  Comment créer un groupe de machines virtuelles identiques à l’aide d’une image personnalisée ?

**A.**  Laissez la propriété vhdContainers vide, par exemple :

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": "https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd"
            },
            "osType": "Windows"
        }
    },


**Q.**  Si je réduis ma capacité de jeu de mise à l’échelle de machine virtuelle de 20 à 15, quelles sont les machines virtuelles qui seront supprimées ?

**A.**  Les machines virtuelles sont supprimées du groupe uniformément entre les domaines de mise à niveau et les domaines d’erreur pour optimiser la disponibilité. Les machines virtuelles avec les ID les plus élevés sont supprimées en premier.

**Q.**  Que diriez-vous si j’augmente par la suite la capacité de 15 à 18 ?

**A.**  Si vous augmentez la capacité à 18, 3 machines virtuelles seront créées. À chaque fois, l’ID d’instance de la machine virtuelle sera incrémentée avec la valeur la plus élevée précédente (par exemple, 20, 21, 22). Les machines virtuelles sont réparties entre FD et UD.

**Q.**  Lorsque vous utilisez plusieurs extensions dans un jeu de mise à l’échelle de machine virtuelle, puis-je appliquer une séquence d’exécution ?

**A.** Pas directement, mais pour l’extension customScript, votre script peut attendre une autre extension avant de s’exécuter ([par exemple en surveillant le journal d’extension](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Vous trouverez des conseils supplémentaires sur le séquencement d’extensions dans ce billet de blog : [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)(Séquencement d’extensions dans les groupes identiques de machines virtuelles Azure).

**Q.**  Les jeux de mise à l’échelle de machine virtuelle fonctionnent-ils avec des ensembles haute disponibilité Azure ?

**A.**  Oui. Un groupe de machines virtuelles identiques est un groupe à haute disponibilité implicite avec 5 FD et 5 UD. Vous n’avez pas besoin de configurer quoi que ce soit sous virtualMachineProfile. Dans les futures versions, les jeux de mise à l’échelle de machine virtuelle peuvent s’étendre sur plusieurs clients, mais dans l’immédiat, un jeu de mise à l’échelle est un ensemble de disponibilité unique.




<!--HONumber=Dec16_HO4-->


