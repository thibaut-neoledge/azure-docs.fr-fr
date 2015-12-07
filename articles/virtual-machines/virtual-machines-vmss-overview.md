<properties
	pageTitle="Présentation des jeux de mise à l’échelle de machines virtuelles | Microsoft Azure"
	description="En savoir plus sur les jeux de mise à l’échelle de machine virtuelle"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# Présentation des jeux de machines virtuelles mise à l’échelle

Les jeux de mise à l’échelle de machine virtuelle sont des ressources de calcul Azure que vous pouvez utiliser pour déployer et gérer un jeu de machines virtuelles identiques. Avec toutes les machines virtuelles configurées de la même façon, les jeux de mise à l’échelle de machine virtuelle sont conçus pour prendre en charge la mise à niveau automatique réelle (aucun réapprovisionnement de machine virtuelle n’est requis) et en tant que tel, facilite la création de services à grande échelle ciblant les grands calculs, big data et les prises en charge en conteneurs.

[AZURE.INCLUDE [Virtual-machines-vmss-preview](../../includes/virtual-machines-vmss-preview-include.md)]

Pour les applications nécessitant une mise à l’échelle des ressources de calcul internes et externes, les opérations de mise à l’échelle sont équilibrées de façon implicite sur plusieurs domaines d’erreur et de mise à jour. Pour accéder à une présentation des jeux de mise à l’échelle de machines virtuelles, reportez-vous aux récentes [Annonces de blog Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview).

Regardez ces vidéos pour en savoir plus sur les jeux de mise à l’échelle de machine virtuelle :

 - [Mark Russinovich parle des jeux de mise à l’échelle Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [Jeux de mise à l’échelle de machine virtuelle, avec Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## Création et gestion des jeux de mise à l’échelle de machine virtuelle

Les jeux de mise à l’échelle de machine virtuelle peuvent être définis et déployés à l’aide de modèles JSON et d’[API REST](https://msdn.microsoft.com/library/mt589023.aspx), tout comme des machines virtuelles Azure Resource Manager individuelles. Par conséquent, les méthodes de déploiement d’Azure Resource Manager standard peuvent être utilisées. Pour en savoir plus sur les modèles, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Vous trouverez des modèles d’exemple de jeux de mise à l’échelle de machine virtuelle parmi les référentiels de démarrage rapide GitHub, ici :

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) -rechercher les modèles avec _vmss_ dans le titre.

Dans les pages de détail correspondant à ces modèles, vous pouvez voir un bouton qui est associé à la fonction de déploiement de portail. Pour déployer le jeu de mise à l’échelle de machine virtuelle, cliquez sur le bouton et renseignez ensuite tous les paramètres requis dans le portail. Si vous ne savez pas si une ressource prend en charge les majuscules ou les casses mixtes, il est préférable d’utiliser toujours les valeurs de paramètre en minuscules. Il existe également une pratique de dissection vidéo d’un modèle d’ensemble de mise à l’échelle de machine virtuelle ici :

[Dissection des modèles de jeu de mise à l’échelle de machine virtuelle](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## Mise à l’échelle d’un jeu de mise à l’échelle de machine virtuelle

Pour augmenter ou diminuer le nombre de machines virtuelles dans un jeu de mise à l’échelle de machine virtuelle, il suffit de changer la propriété _capacité_ et redéployez le modèle. Cette simplicité rend plus facile l’écriture de votre couche de mise à l’échelle personnalisée si vous souhaitez définir des événements d’échelle personnalisée qui ne sont pas pris en charge par l’échelle automatique Azure.

Si vous redéployez un modèle pour modifier la capacité, vous pouvez définir un modèle beaucoup plus petit incluant uniquement la référence SKU et la capacité mise à jour. Un exemple de cela est indiqué ici : [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Pour être guidé parmi les étapes qui crée un jeu de mise à l’échelle automatique, consultez les [machines de mise à l’échelle automatique dans un jeu de mise à l’échelle de machine virtuelle](virtual-machines-vmss-walkthrough.md)

## Surveillance de votre jeu de mise à l’échelle de machine virtuelle

Il est actuellement recommandé d’utiliser [Azure Resource Explorer](https://resources.azure.com) pour afficher les jeux de mise à l’échelle de machine virtuelle. Les jeux de mise à l’échelle de machine virtuelle sont des ressources sous Microsoft.Compute, alors, à partir de ce site, vous pouvez les voir en étendant les liens suivants :

	subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## Scénarios de jeu de mise à l’échelle de machine virtuelle

Cette section répertorie quelques scénarios de jeu de mise à l’échelle de machine virtuelle classique. Certains services Azure de niveau supérieur (comme Batch, Service Fabric, Service de conteneur Azure) utilisent également ces scénarios.

 - **RDP / SSH to VM scale set instances** - Un jeu de mise à l’échelle de machine virtuelle est créé à l’intérieur d’un réseau virtuel, et les machines individuelles ne se voient pas affecter des adresses IP publiques. ce qui est une bonne chose, car vous ne souhaitez généralement pas être soumis à des frais supplémentaires liés à l’affectation d’adresse IP séparées à toutes les ressources sans états dans la grille de calcul, et vous pouvez facilement vous connecter à ces ordinateurs virtuels à partir d’autres ressources de votre réseau virtuel, notamment celles qui possèdent des adresses IP publiques telles que les équilibreurs de charge ou les machines virtuelles autonomes.

 - **Se connecter à des machines virtuelles à l’aide de règles NAT** - Vous pouvez créer une adresse IP publique, l’affecter à un équilibreur de charge et définir des règles NAT entrantes qui mappent un port de l’adresse IP sur un port de machine virtuelle dans le jeu de mise à l’échelle de machine virtuelle. Par exemple,

	Adresse IP publique -> Port 50000 -> vmss\_0 -> Adresse IP publique Port 22 -> Port 50001 -> vmss\_1 -> Adresse IP publique Port 22 ->Port 50002-> vmss\_2->Port 22

	Voici un exemple de création d’un jeu de mise à l’échelle de machine virtuelle qui utilise les règles NAT pour activer la connexion SSH à chaque machine virtuelle dans une échelle à l’aide d’une adresse IP publique unique : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

	Voici un exemple faisant la même chose avec RDP et Windows : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **Connectez-vous à la machine virtuelle à l’aide d’une « jumpbox »** -Si vous créez un jeu de mise à l’échelle de machine virtuelle et un ordinateur virtuel autonome dans le même réseau virtuel, la machine virtuelle autonome et les machines virtuelles de jeu de mise à l’échelle de machine virtuelle peuvent se connecter entre elles en utilisant leurs adresses IP internes comme défini par le réseau virtuel/sous-réseau. Si vous créez une adresse IP publique et l’affectez à la machine virtuelle autonome, vous pouvez lancer un RDP ou un SSH sur la machine virtuelle autonome, puis vous connecter depuis cette machine à vos instances de jeu de mise à l’échelle de machine virtuelle. À ce stade, vous pouvez remarquer qu’un simple jeu de mise à l’échelle de machine virtuelle est intrinsèquement plus sûr qu’une machine virtuelle autonome simple avec une adresse IP publique dans sa configuration par défaut.

	Pour obtenir un exemple de cette approche, ce modèle crée un cluster Mesos simple composé d’une machine virtuelle Master qui gère un cluster de machines virtuelles de jeu de mise à l’échelle de machine virtuelle : [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **Équilibreur de charge avec tourniquet pour les instances de mise à l’échelle de machine virtuelle** - Si vous souhaitez remettre un travail à un cluster de calcul de machines virtuelles à l’aide d’une approche de type « tourniquet », vous pouvez configurer un équilibreur de charge Azure avec règle d’équilibrage de charge en conséquence. Vous pouvez également définir des sondes pour vérifier que votre application s’exécute par le lancement d’une commande ping sur les ports avec un protocole spécifié, un intervalle et des chemins de requêtes.

	Voici un exemple qui crée le jeu de mise à l’échelle de machines virtuelles exécutant le serveur web IIS, et utilise un équilibreur de charge pour équilibrer la charge que chaque machine virtuelle reçoit. Il utilise également le protocole HTTP pour lancer une commande ping sur une URL spécifique sur chaque machine virtuelle : [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) - examiner le type de ressource Microsoft.Network/loadBalancers et networkProfile et extensionProfile dans virtualMachineScaleSet.

 - **Déploiement d’un jeu de mise à l’échelle en tant que cluster de calcul dans un gestionnaire de cluster PaaS** - les jeux de mise à l’échelle de machine virtuelle sont parfois décrits en tant que rôle de travail de la prochaine génération. Il s’agit d’une description valide, mais elle peut être confondue avec les fonctionnalités du rôle de travail PaaS v1. Dans un sens, les jeux de mise à l’échelle de machine virtuelle fournissent un véritable « rôle » ou une ressource de travail, car ils fournissent une ressource de calcul générale indépendante de la plateforme/runtime, personnalisable et s’intègrent au Gestionnaire de ressources IaaS Azure.

	Un rôle de travail PaaS v1, limité en termes de prise en charge de plate-forme/runtime (images de plateforme Windows) inclut également les services d’échange d’adresse IP virtuelle, de paramètres de mise à niveau configurables, de paramètres spécifiques de runtime/déploiement d’application spécifiques qui ne sont pas _encore_ disponibles dans les jeux de mise à l’échelle de machine virtuelle ou sera fournie par des services PaaS de plus haut niveau tels que Service Fabric. Avec cela à l’esprit, vous pouvez considérer les jeux de mise à l’échelle de machine virtuelle comme une infrastructure qui prend en charge de PaaS. par exemple, des solutions PaaS telles que Service Fabric ou gestionnaires de cluster comme Mesos peuvent se construire sur des jeux de mise à l’échelle de machine virtuelle en tant que couche de calcul évolutive.

	Voici un exemple de cluster Mesos qui déploie un jeu de mise à l’échelle de machine virtuelle définie dans le même réseau virtuel qu’une machine virtuelle autonome. La machine virtuelle autonome est un maître Mesos et le jeu de mise à l’échelle de machine virtuelle représente un ensemble de nœuds esclaves : [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Les versions futures de [Service de conteneur Azure](https://azure.microsoft.com/fr-FR/blog/azure-container-service-now-and-the-future/) déploieront des versions plus complexes/renforcées de ce scénario basées sur des jeux de mise à l’échelle de machine virtuelle.

## La performance de mise à l’échelle de machine virtuelle et guide de mise à l’échelle

- Au cours de l’évaluation publique, ne créez pas plus de 500 machines virtuelles dans plusieurs jeux de mise à l’échelle de machine virtuelle à la fois.
- Ne prévoyez pas plus de 40 machines virtuelles par compte de stockage.
- Fractionnez le plus possible les premières lettres des noms de compte de stockage. Les modèles VMSS exemple [modèles Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/) fournissent des exemples montrant comment effectuer cette opération.
- Si vous utilisez des machines virtuelles personnalisées, ne prévoyez pas plus de 40 machines virtuelles par jeu de mise à l’échelle de machine virtuelle, dans un seul compte de stockage. Vous aurez besoin de l’image pré copiée dans le compte de stockage avant de pouvoir commencer le déploiement du jeu de mise à l’échelle de machine virtuelle. Visitez le FAQ pour plus d’informations.
- Ne prévoyez pas plus de 2 048 machines virtuelles par machine virtuelle. Cette limite peut être repoussée par la suite.
- Le nombre de machines virtuelles que vous pouvez créer est limité par votre quota de cœurs de calcul dans n’importe quelle région. Vous devrez peut-être contacter le support technique pour augmenter votre limite de quota de calcul augmentée, même si vous avez une limite de cœurs haute à utiliser avec vos services cloud ou IaaS v1 dès aujourd’hui. Pour interroger votre quota, vous pouvez exécuter la commande CLI Azure suivante : _azure vm list-usage_, et la commande PowerShell suivante : _Get-AzureRmVMUsage_ (si vous utilisez une version de PowerShell 1.0, utilisez _Get-AzureVMUsage_).

## jeu de mise à l’échelle de machine virtuelle - Forum aux Questions.

**Q.** Combien de machines virtuelles peut-il y avoir dans un jeu de mise à l’échelle de machine virtuelle ?

**A.** 100 si vous utilisez des images de plateforme qui peuvent être distribuées sur plusieurs comptes de stockage. Si vous utilisez des images personnalisées, jusqu’à 40, étant donné que les images personnalisées sont limitées à un seul compte de stockage lors de la visualisation.

**Q** Quelles sont les autres limites de ressource qui existent pour les jeux de mise à l’échelle de machine virtuelle ?

**A.** Vous êtes limité à la création de seulement de 500 machines virtuelles dans plusieurs jeux de mise à l’échelle par région pendant la période d’évaluation. Les [limites du Service d’abonnement Azure /](../azure-subscription-service-limits.md) s’appliquent.

**Q.** Les disques de données sont-ils pris en charge dans les jeux de mise à l’échelle de machine virtuelle ?

**A.** Pas dans la version initiale. Vos options de stockage des données sont :

- Fichiers Azure (lecteurs SMB partagés)

- Système d’exploitation de lecteur

- Lecteur temp (local, non sauvegardé par le stockage Azure)

- Service de données externes (base de données distante, tables Azure, objets blobs Azure)

**Q.** Quelles sont les régions Azure qui prennent en charge les jeux de mise à niveau de machine virtuelle ?

**A.** N’importe quelle région qui prend en charge Azure Resource Manager prend en charge les jeux de mise à l’échelle de machine virtuelle.

**Q.** Comment créer un jeu de mise à l’échelle de machine virtuelle à l’aide d’une image personnalisée ?

**A.** Laissez la propriété vhdContainers vide, par exemple :

	"storageProfile": {
		"osDisk": {
			"name": "vmssosdisk",
			"caching": "ReadOnly",
			"createOption": "FromImage",
			"image": {
				"uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
			},
			"osType": "Windows"
		}
	},


**Q.** Si je réduis ma capacité de jeu de mise à l’échelle de machine virtuelle de 20 à 15, quelles sont les machines virtuelles qui seront supprimées ?

**A.** Les ordinateurs virtuels sont supprimés du jeu de mise à l’échelle uniformément entre les domaines de mise à niveau et les domaines d’erreur pour optimiser la disponibilité.

**Q.** Que diriez-vous si j’augmente par la suite la capacité de 15 à 18 ?

**A.** Si vous augmentez jusqu’à 18, des machines virtuelles avec index 15, 16, 17 seront créées. Dans les deux cas, les machines virtuelles sont réparties entre FD et UD.

**Q.** Lorsque vous utilisez plusieurs extensions dans un jeu de mise à l’échelle de machine virtuelle, puis-je appliquer une séquence d’exécution ?

**A.** Indirectement, pour l’extension customScript, votre script peut attendre qu’une autre extension soit terminée (par exemple, en surveillant le journal d’extension- voir [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**Q.** Les jeux de mise à l’échelle de machine virtuelle fonctionnent-ils avec des ensembles haute disponibilité Azure ?

**A.** Oui. Un ensemble de mise à l’échelle est un ensemble haute disponibilité implicite avec 3 FD et 5 UD. Vous n’avez pas besoin de configurer quoi que ce soit sous virtualMachineProfile. Dans les futures versions, les jeux de mise à l’échelle de machine virtuelle peuvent s’étendre sur plusieurs clients, mais dans l’immédiat, un jeu de mise à l’échelle est un ensemble de disponibilité unique.

<!---HONumber=AcomDC_1125_2015-->