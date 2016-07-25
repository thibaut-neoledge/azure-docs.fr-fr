<properties
	pageTitle="Présentation des groupes de machines virtuelles identiques | Microsoft Azure"
	description="En savoir plus sur les groupes de machines virtuelles identiques"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="guybo"/>

# Présentation des groupes de machines virtuelles identiques

Les groupes de machines virtuelles identiques sont des ressources Azure Compute que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques. Avec toutes les machines virtuelles configurées de la même façon, les groupes de machines virtuelles identiques sont conçus pour prendre en charge une véritable mise à l’échelle automatique (aucun préapprovisionnement de machine virtuelle n’est nécessaire) et faciliter ainsi la création de services à grande échelle pour le big compute, le big data et les charges de travail en conteneurs.

Pour les applications nécessitant une mise à l’échelle des ressources de calcul internes et externes, les opérations de mise à l’échelle sont équilibrées de façon implicite sur plusieurs domaines d’erreur et de mise à jour. Pour accéder à une présentation des groupes de machines virtuelles identiques, reportez-vous aux récentes [Annonces de blog Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Regardez ces vidéos pour en savoir plus sur les groupes de machines virtuelles identiques :

 - [Mark Russinovich parle des groupes de machines virtuelles identiques Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)

 - [Groupes de machines virtuelles identiques, avec Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## Création et gestion des groupes de machines virtuelles identiques

Les groupes de machines virtuelles identiques peuvent être définis et déployés à l’aide de modèles JSON et d’[API REST](https://msdn.microsoft.com/library/mt589023.aspx), tout comme des machines virtuelles Azure Resource Manager individuelles. Par conséquent, les méthodes de déploiement d’Azure Resource Manager standard peuvent être utilisées. Pour en savoir plus sur les modèles, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Vous trouverez des modèles d’exemple de groupes de machines virtuelles identiques dans le dépôt GitHub des modèles de démarrage rapide Azure, [ici](https://github.com/Azure/azure-quickstart-templates) (recherchez les modèles dont le titre contient _vmss_)

Dans les pages de détail correspondant à ces modèles, vous pouvez voir un bouton qui est associé à la fonction de déploiement de portail. Pour déployer le groupe de machines virtuelles identiques, cliquez sur le bouton et renseignez ensuite tous les paramètres nécessaires dans le portail. Si vous ne savez pas si une ressource prend en charge les majuscules ou les casses mixtes, il est préférable d’utiliser toujours les valeurs de paramètre en minuscules. Il existe également une pratique de dissection vidéo d’un modèle de groupe de machines virtuelles identiques ici :

[Dissection des modèles de groupes de machines virtuelles identiques](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## Augmentation ou diminution du nombre de machines virtuelles identiques dans un groupe

Pour augmenter ou diminuer le nombre de machines virtuelles dans un groupe de machines virtuelles identiques, il suffit de changer la propriété _capacité_ et redéployez le modèle. Cette simplicité rend plus facile l’écriture de votre couche de mise à l’échelle personnalisée si vous souhaitez définir des événements d’échelle personnalisée qui ne sont pas pris en charge par l’échelle automatique Azure.

Si vous redéployez un modèle pour modifier la capacité, vous pouvez définir un modèle beaucoup plus petit incluant uniquement la référence SKU et la capacité mise à jour. Un exemple est présenté [ici.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)

Pour être guidé tout au long des étapes de création d’un groupe automatiquement mis à l’échelle, consultez [Mettre automatiquement à l’échelle les machines d’un groupe de machines virtuelles identiques](virtual-machine-scale-sets-windows-autoscale.md)

## Surveillance de votre groupe de machines virtuelles identiques

Le [portail Azure](https://portal.azure.com) répertorie les groupes de machines virtuelles identiques et affiche les propriétés de base, ainsi que la liste des machines virtuelles du groupe. Pour obtenir plus de détails, vous pouvez utiliser [Azure Resource Explorer](https://resources.azure.com) pour afficher les groupes de machines virtuelles identiques. Les groupes de machines virtuelles identiques sont des ressources sous Microsoft.Compute, vous pouvez donc les voir à partir de ce site en développant les liens suivants :

	subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## Scénarios de groupe de machines virtuelles identiques

Cette section répertorie quelques scénarios typiques de groupe de machines virtuelles identiques. Certains services Azure plus généraux (comme Batch, Service Fabric, Azure Container Service) utilisent également ces scénarios.

 - **RDP/SSH vers des instances de groupe de machines virtuelles identiques** - Un groupe de machines virtuelles identiques est créé dans un réseau virtuel et les machines virtuelles individuelles du groupe ne se voient pas allouer d’adresses IP publiques. C’est préférable car vous ne souhaiterez pas être soumis à des frais supplémentaires liés à l’affectation d’adresses IP publiques séparées à toutes les ressources sans état dans la grille de calcul. De plus, vous pouvez facilement vous connecter à ces machines virtuelles à partir d’autres ressources de votre réseau virtuel, notamment celles qui ont des adresses IP publiques telles que les équilibreurs de charge ou les machines virtuelles autonomes.

 - **Se connecter à des machines virtuelles à l’aide de règles NAT** - Vous pouvez créer une adresse IP publique, l’affecter à un équilibreur de charge et définir des règles NAT entrantes qui mappent un port de l’adresse IP sur un port de machine virtuelle dans le groupe de machines virtuelles identiques. Par exemple :
 
	Source | Port source | Destination | Port de destination
	--- | --- | --- | ---
	Adresse IP publique | Port 50000 | vmss\_0 | Port 22
	Adresse IP publique | Port 50001 | vmss\_1 | Port 22
	Adresse IP publique | Port 50002 | vmss\_2 | Port 22

	Voici un exemple de création d’un groupe de machines virtuelles identiques qui utilise les règles NAT pour activer la connexion SSH à chaque machine virtuelle dans une échelle à l’aide d’une adresse IP publique unique : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

	Voici un exemple faisant la même chose avec RDP et Windows : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **Connectez-vous à la machine virtuelle à l’aide d’une « jumpbox »** -Si vous créez un groupe de machines virtuelles identiques et une machine virtuelle autonome dans le même réseau virtuel, la machine virtuelle autonome et les machines virtuelles du groupe de machines virtuelles identiques peuvent se connecter entre elles en utilisant leurs adresses IP internes, comme défini par le réseau virtuel/sous-réseau. Si vous créez une adresse IP publique et l’affectez à la machine virtuelle autonome, vous pouvez lancer un RDP ou un SSH sur la machine virtuelle autonome, puis vous connecter depuis cette machine à vos instances de groupe de machines virtuelles identiques. À ce stade, vous pouvez remarquer qu’un simple groupe de machines virtuelles identiques est intrinsèquement plus sûr qu’une machine virtuelle autonome simple avec une adresse IP publique dans sa configuration par défaut.

	[Pour obtenir un exemple de cette approche, ce modèle crée un cluster Mesos simple composé d’une machine virtuelle Master qui gère un cluster de machines virtuelles de jeu de mise à l’échelle de machine virtuelle.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **Équilibrage de charge pour les instances de groupe de machines virtuelles identiques** - Si vous souhaitez remettre un travail à un cluster de calcul de machines virtuelles à l’aide d’une approche de type « tourniquet », vous pouvez configurer un équilibreur de charge Azure avec des règles d’équilibrage de charge appropriées. Vous pouvez définir des sondes pour vérifier que votre application s’exécute en envoyant des requêtes ping aux ports, en spécifiant un protocole, un intervalle et un chemin d'accès à la demande. Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) prend également en charge les groupes de machines virtuelles identiques, ainsi que des scénarios d’équilibrage de charge plus sophistiqués.

	[Voici un exemple qui montre comment créer un groupe de machines virtuelles identiques exécutant le serveur web IIS, puis utiliser un équilibreur de charge pour équilibrer la charge reçue par chaque machine virtuelle. Il utilise également le protocole HTTP pour lancer une commande ping sur une URL spécifique sur chaque machine virtuelle. ](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) (examiner le type de ressource Microsoft.Network/loadBalancers et networkProfile et extensionProfile dans virtualMachineScaleSet)

 - **Déploiement d’un groupe de machines virtuelles identiques comme cluster de calcul dans un gestionnaire de cluster PaaS** - les groupes de machines virtuelles identiques sont parfois décrits comme les rôles de travail nouvelle génération. Cette description est juste, mais le risque est de confondre les fonctionnalités de groupe de machines virtuelles identiques avec les fonctionnalités du rôle de travail PaaS v1. Dans un sens, les groupes de machines virtuelles identiques fournissent un véritable « rôle » ou une ressource de travail, car ils fournissent une ressource de calcul générale indépendante de la plateforme/runtime, personnalisable et s’intègrent au Gestionnaire de ressources IaaS Azure.

	Un rôle de travail PaaS v1, limité en termes de prise en charge de plate-forme/runtime (images de plateforme Windows) inclut également les services d’échange d’adresse IP virtuelle, de paramètres de mise à niveau configurables, de paramètres spécifiques de runtime/déploiement d’application spécifiques qui ne sont pas _encore_ disponibles dans les groupes de machines virtuelles identiques ou sera fournie par des services PaaS plus généraux tels que Service Fabric. Avec cela à l’esprit, vous pouvez considérer les groupes de machines virtuelles identiques comme une infrastructure qui prend en charge PaaS. Par exemple, des solutions PaaS telles que Service Fabric ou gestionnaires de cluster comme Mesos peuvent se construire sur des groupes de machines virtuelles identiques en tant que couche de calcul évolutive.

	[Pour obtenir un exemple de cette approche, ce modèle crée un cluster Mesos simple composé d’une machine virtuelle Master qui gère un cluster de machines virtuelles de groupe de machines virtuelles identiques.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json) Les versions futures [d’Azure Container Service](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) déploieront des versions plus complexes / renforcées de ce scénario basées sur des groupes de machines virtuelles identiques.

## Performances des groupes de machines virtuelles identiques et conseils de mise à l’échelle

- Ne créez pas plus de 500 machines virtuelles dans plusieurs groupes de machines virtuelles identiques à la fois.
- Ne prévoyez pas plus de 20 machines virtuelles par compte de stockage (sauf si vous définissez la propriété _overprovision_ sur « false », auquel cas vous pouvez en prévoir jusqu’à 40).
- Fractionnez le plus possible les premières lettres des noms de compte de stockage. Les exemples de modèles VMSS dans les [modèles Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/) montrent comment effectuer cette opération.
- Si vous utilisez des machines virtuelles personnalisées, ne prévoyez pas plus de 40 machines virtuelles par groupe de machines virtuelles identiques, dans un seul compte de stockage. Vous aurez besoin de l’image pré copiée dans le compte de stockage avant de pouvoir commencer le déploiement du groupe de machines virtuelles identiques. Visitez le FAQ pour plus d’informations.
- Ne prévoyez pas plus de 4096 machines virtuelles par réseau virtuel.
- Le nombre de machines virtuelles que vous pouvez créer est limité par le quota de base dans la région dans laquelle vous déployez. Vous devrez peut-être contacter le support technique pour augmenter votre limite de quota de calcul augmentée, même si vous avez une limite de cœurs haute à utiliser avec vos services cloud ou IaaS v1 dès aujourd’hui. Pour interroger votre quota, vous pouvez exécuter la commande CLI Azure suivante : `azure vm list-usage`, et la commande PowerShell suivante : `Get-AzureRmVMUsage` (si vous utilisez une version de PowerShell inférieure à 1.0, utilisez `Get-AzureVMUsage`).

## Forum aux questions sur les groupes de machines virtuelles identiques

**Q.** Combien de machines virtuelles peut-il y avoir dans un groupe de machines virtuelles identiques ?

**A.** 100 si vous utilisez des images de plateforme qui peuvent être distribuées sur plusieurs comptes de stockage. Si vous utilisez des images personnalisées, jusqu’à 40 (si la propriété _overprovision_ est définie sur « false », sinon 20 par défaut), étant donné que les images personnalisées sont actuellement limitées à un seul compte de stockage.

**Q** Quelles sont les autres limites de ressource qui existent pour les groupes de machines virtuelles identiques ?

**A.** Vous êtes limité à la création de seulement 500 machines virtuelles dans plusieurs groupes identiques par région pendant une période de 10 minutes. Les [limites du Service d’abonnement Azure /](../azure-subscription-service-limits.md) s’appliquent.

**Q.** Les disques de données sont-ils pris en charge dans les groupes de machines virtuelles identiques ?

**A.** Pas dans la version initiale. Vos options de stockage des données sont :

- Fichiers Azure (lecteurs SMB partagés)

- Système d’exploitation de lecteur

- Lecteur temp (local, non sauvegardé par le stockage Azure)

- Service de données Azure (tables Azure, objets blob Azure)

- Service de données externe (base de données distante)

**Q.** Quelles sont les régions Azure qui prennent en charge les groupes de machines virtuelles identiques ?

**A.** N’importe quelle région qui prend en charge Azure Resource Manager prend en charge les groupes de machines virtuelles identiques.

**Q.** Comment créer un groupe de machines virtuelles identiques à l’aide d’une image personnalisée ?

**A.** Laissez la propriété vhdContainers vide, par exemple :

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


**Q.** Si je réduis ma capacité de groupe de machines virtuelles identiques de 20 à 15, quelles sont les machines virtuelles qui seront supprimées ?

**A.** Les machines virtuelles sont supprimées du groupe uniformément entre les domaines de mise à niveau et les domaines d’erreur pour optimiser la disponibilité.

**Q.** Que diriez-vous si j’augmente par la suite la capacité de 15 à 18 ?

**A.** Si vous augmentez jusqu’à 18, des machines virtuelles avec index 15, 16, 17 seront créées. Dans les deux cas, les machines virtuelles sont réparties entre FD et UD.

**Q.** Lorsque vous utilisez plusieurs extensions dans un groupe de machines virtuelles identiques, puis-je appliquer une séquence d’exécution ?

**A.** Pas directement, mais pour l’extension customScript, votre script peut attendre une autre extension avant de s’exécuter ([par exemple en surveillant le journal d’extension](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Vous trouverez des conseils supplémentaires sur le séquencement d’extensions dans ce billet de blog : [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Séquencement d’extensions dans les groupes de machines virtuelles identiques Azure).

**Q.** Les groupes de machines virtuelles identiques fonctionnent-ils avec des groupes à haute disponibilité Azure ?

**A.** Oui. Un groupe de machines virtuelles identiques est un groupe à haute disponibilité implicite avec 5 FD et 5 UD. Vous n’avez pas besoin de configurer quoi que ce soit sous virtualMachineProfile. Dans les futures versions, les groupes de machines virtuelles identiques peuvent s’étendre sur plusieurs clients, mais dans l’immédiat, un groupe identique est un groupe à haute disponibilité unique.

<!---HONumber=AcomDC_0713_2016-->