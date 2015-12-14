<properties
	pageTitle="Résolution des problèmes d’allocation de machines virtuelles | Microsoft Azure"
	description="Résolution des problèmes d’allocation lors de la création, du redémarrage ou du redimensionnement d’une machine virtuelle dans Azure"
	services="virtual-machines, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="virtual-machines"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="kenazk"/>



# Résolution des problèmes d’allocation lors de la création, du redémarrage ou du redimensionnement d’une machine virtuelle dans Azure

Lorsque vous créez une machine virtuelle, redémarrez des machines virtuelles ayant été arrêtées (désallouées) ou redimensionnez une machine virtuelle, Microsoft Azure alloue des ressources de calcul à votre abonnement. Vous pouvez parfois recevoir des erreurs lorsque vous effectuez ces opérations avant même d’avoir atteint les limites de votre abonnement Azure. Cet article explique les causes de certains échecs d’allocation courants et propose des solutions possibles. Les informations qu’il contient peuvent également vous être utiles dans le cadre de la planification du déploiement de vos services.

Si vous avez besoin d’aide supplémentaire concernant n’importe quel point de cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](http://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir de l’aide**.

La section « Résolution des problèmes courants liés à l'allocation » répertorie les étapes permettant de résoudre les problèmes courants. La section « Résolution de scénarios d'échec d'allocation spécifiques » indique une procédure de résolution pour chaque message d'erreur spécifique. Avant de commencer, voici quelques informations contextuelles qui vous permettront de comprendre le fonctionnement de l'allocation et pourquoi un échec d'allocation se produit.

## Informations contextuelles
### Fonctionnement de l’allocation
Les serveurs des centres de données Azure sont partitionnés en clusters. En règle générale, les tentatives de demande d’allocation sont exécutées dans plusieurs clusters, mais il est possible que certaines contraintes de la demande d’allocation forcent la plateforme Azure à exécuter la tentative dans un seul cluster. Dans cet article, nous parlerons « d’épinglage à un cluster ». La Figure 1 ci-dessous illustre le cas d’une allocation normale dans laquelle la tentative est exécutée dans plusieurs clusters. La Figure 2 illustre le cas d’une allocation épinglée au Cluster 2, là où est hébergé le service cloud CS\_1 existant ou le groupe à haute disponibilité. ![Schéma d’allocation](./media/virtual-machines-allocation-failure/Allocation1.png)

### Raisons de l’échec d’une allocation
Lorsqu’une demande d’allocation est épinglée à un cluster, il y a plus de risque de ne pas trouver les ressources disponibles puisque le pool de ressources disponibles est réduit. En outre, si votre demande d’allocation est épinglée à un cluster alors que le type de ressource que vous avez demandé n’est pas pris en charge par ce cluster, votre demande échouera, même si le cluster comporte des ressources disponibles. La Figure 3 ci-dessous illustre le cas d’une allocation épinglée qui échoue car le seul cluster candidat ne comporte pas de ressources disponibles. La Figure 4 illustre le cas de figure où une allocation épinglée échoue parce que le seul cluster candidat ne prend pas en charge la taille de machine virtuelle demandée, bien qu’il puisse libérer des ressources. ![Échec d’allocation épinglée](./media/virtual-machines-allocation-failure/Allocation2.png)

## Résolution des problèmes courants liés à l'allocation dans le modèle de déploiement classique

Ces étapes peuvent aider à résoudre de nombreux échecs d'allocation survenant au niveau des machines virtuelles.

- Redimensionnez la machine virtuelle à une taille différente.<br> Cliquez sur Parcourir tout > Machines virtuelles (classiques) > votre machine virtuelle > Paramètres > **Taille**. Pour obtenir la procédure détaillée, consultez [Redimensionner la machine virtuelle](https://msdn.microsoft.com/library/dn168976.aspx).

- Supprimez toutes les machines virtuelles du service cloud et recréez les machines virtuelles.<br> Cliquez sur Parcourir tout > Machines virtuelles (classiques) > votre machine virtuelle > Supprimer. Cliquez ensuite sur Nouveau > Calcul > [Image de machine virtuelle].

## Résolution des problèmes courants liés à l'allocation dans le modèle de déploiement Resource Manager

Ces étapes peuvent aider à résoudre de nombreux échecs d'allocation survenant au niveau des machines virtuelles.

- Arrêtez la désallocation de toutes les machines virtuelles dans le même groupe à haute disponibilité, puis redémarrez chacune d’elles.<br> Procédure d’arrêt : cliquez sur Groupes de ressources > votre groupe de ressources > Ressources > Votre groupe à haute disponibilité > Machines virtuelles > votre Machine virtuelle > Arrêter.

	Une fois toutes les machines virtuelles arrêtées, sélectionnez la première machine virtuelle et cliquez sur Démarrer.

## Résolution de scénarios d’échecs d’allocation spécifiques dans le modèle de déploiement classique
Voici les scénarios courants d’allocation qui entraînent l’épinglage d’une demande d’allocation. Nous aborderons chaque scénario ultérieurement dans cet article.

- Redimensionnement d'une machine virtuelle ou ajout de machines virtuelles ou d'instances de rôle supplémentaires à un service cloud existant
- Redémarrage de machines virtuelles arrêtées (désallouées) - désallocation partielle
- Redémarrage de machines virtuelles arrêtées (désallouées) - désallocation complète
- Déploiements en environnement intermédiaire ou de production (PaaS uniquement)
- Groupe d’affinités - proximité entre la machine virtuelle et le service
- Réseau virtuel par groupe d’affinités

Lorsque vous recevez une erreur d’allocation, voyez si le scénario décrit s’applique à votre cas de figure. Utilisez l’erreur d’allocation renvoyée par la plateforme Azure pour identifier le scénario correspondant. Si votre demande est épinglée, essayez de supprimer certaines contraintes d’épinglage pour élargir votre demande à plusieurs clusters et augmenter ainsi les chances de succès de l’allocation.

En règle générale, tant que l’erreur n’indique pas que la taille de machine virtuelle demandée n’est pas prise en charge, vous pouvez toujours réessayer ultérieurement, car il est possible que des ressources suffisantes aient été libérées dans le cluster pour prendre en charge votre demande. Si l’erreur est liée à un problème de prise en charge de la taille de machine virtuelle demandée, essayez une autre taille de machine virtuelle. Sinon, la seule option possible consiste à supprimer la contrainte d’épinglage.

Deux scénarios d’échec courants sont liés au groupe d’affinités. Le groupe d’affinités était auparavant utilisé pour assurer une proximité étroite aux instances de machines virtuelles ou de services, ou encore pour permettre la création d’un réseau virtuel (VNet). Avec l’introduction du réseau virtuel régional, le groupe d’affinités n’est plus nécessaire pour la création d’un réseau virtuel. La réduction du problème de latence du réseau dans l’infrastructure Windows Azure a modifié la recommandation relative à l’utilisation d’un groupe d’affinités pour la proximité entre la machine virtuelle et le service.

La Figure 5 ci-dessous présente la taxonomie des scénarios d’allocation (épinglés). ![Taxonomie d’une allocation épinglée](./media/virtual-machines-allocation-failure/Allocation3.png)

> [AZURE.NOTE]L’erreur répertoriée dans chaque scénario d’allocation est une forme abrégée. Reportez-vous à l’[Annexe](#appendix) pour obtenir les chaînes d’erreur détaillées.

### Scénario d’allocation : redimensionnement d’une machine virtuelle ou ajout de machines virtuelles ou d’instances de rôle supplémentaires à un service cloud existant
**Erreur**

Upgrade\_VMSizeNotSupported* ou GeneralError*

**Cause de l’épinglage au cluster**

La demande de redimensionnement d’une machine virtuelle ou d’ajout d’une machine virtuelle ou d’une instance de rôle à un service cloud existant doit être exécutée sur le cluster d’origine qui héberge le service cloud existant. La création d’un nouveau service cloud permet à la plateforme Azure de trouver un autre cluster disposant de ressources ou prenant en charge la taille de machine virtuelle que vous avez demandée.

**Solution de contournement**

Si l’erreur est Upgrade\_VMSizeNotSupported*, essayez une autre taille de machine virtuelle. Si vous ne pouvez pas utiliser une autre taille de machine virtuelle, mais qu’il est possible d’utiliser une autre adresse IP virtuelle (VIP), créez un service cloud pour héberger la nouvelle machine virtuelle puis ajoutez le nouveau service cloud au réseau virtuel régional où sont exécutées les machines virtuelles existantes. Si votre service cloud existant n’utilise pas de réseau virtuel régional, vous pouvez toujours créer un nouveau réseau virtuel pour le nouveau service cloud, puis connecter votre [réseau virtuel existant au nouveau réseau virtuel](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). En savoir plus sur le [réseau virtuel régional](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Si l’erreur est GeneralError*, il est probable que le type de ressource (par exemple, une taille de machine virtuelle particulière) soit pris en charge par le cluster, mais que ce dernier ne comporte pas de ressources disponibles pour le moment. Comme ci-dessus, essayez d’ajouter de la ressource de calcul en créant un service cloud (notez le nouveau service cloud doit utiliser une adresse IP virtuelle différente) et utilisez le réseau virtuel régional pour connecter vos services cloud.

### Scénario d’allocation : redémarrage de machines virtuelles arrêtées (désallouées) - désallocation partielle

**Erreur**

GeneralError*

**Cause de l’épinglage au cluster**

Une désallocation **partielle** signifie que vous avez arrêté (désalloué) une ou plusieurs machines virtuelles, mais **pas toutes**, dans un service cloud. Lorsque vous arrêtez (désallouez) une machine virtuelle, les ressources associées sont libérées. Le redémarrage de cette machine virtuelle constitue donc une nouvelle demande d’allocation. Le redémarrage de machines virtuelles dans un service cloud partiellement désalloué revient à ajouter des machines virtuelles à un service cloud existant. La demande d’allocation doit être tentée sur le cluster d’origine qui héberge le service cloud existant. La création d’un service cloud différent permet à la plateforme Azure de trouver un autre cluster disposant de ressources ou prenant en charge la taille de machine virtuelle que vous avez demandée.

**Solution de contournement**

S’il est possible d’utiliser une autre adresse IP virtuelle, supprimez les machines virtuelles arrêtées (désallouées) tout en conservant les disques associés, puis ajoutez les machines virtuelles via un autre service cloud. Utilisez le réseau virtuel régional pour connecter vos services cloud : 1. Si votre service cloud existant utilise un réseau virtuel régional, ajoutez simplement le nouveau service cloud au même réseau virtuel. 2. Si votre service cloud existant n’utilise pas de réseau virtuel régional, créez un nouveau réseau virtuel pour le nouveau service cloud, puis connectez votre [réseau virtuel existant au nouveau réseau virtuel](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). En savoir plus sur le [réseau virtuel régional](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

### Scénario d’allocation : redémarrage de machines virtuelles arrêtées (désallouées) - désallocation complète
**Erreur**

GeneralError*

**Cause de l’épinglage au cluster**

Une désallocation **complète** signifie que vous avez arrêté (désalloué) **toutes** les machines virtuelles d’un service cloud. Dès lors, les demandes d’allocation pour le redémarrage de ces machines virtuelles doivent être exécutées sur le cluster d’origine qui héberge le service cloud. La création d’un nouveau service cloud permet à la plateforme Azure de trouver un autre cluster disposant de ressources ou prenant en charge la taille de machine virtuelle que vous avez demandée.

**Solution de contournement**

S’il est possible d’utiliser une autre adresse IP virtuelle, supprimez les machines virtuelles d’origine qui ont été arrêtées (désallouées) tout en conservant les disques associés, puis supprimez le service cloud correspondant (les ressources de calcul associées ont déjà été libérées lorsque vous avez arrêté (désalloué) les machines virtuelles). Créez un nouveau service cloud pour ajouter les machines virtuelles.

### Scénario d’allocation : déploiements en environnement intermédiaire ou de production (PaaS uniquement)
**Erreur**

New\_General* ou New\_VMSizeNotSupported*

**Cause de l’épinglage au cluster**

Les déploiements d’un service cloud en environnement intermédiaire et en environnement de production sont hébergés dans le même cluster. Lorsque vous ajoutez le deuxième déploiement, la demande d’allocation correspondante sera exécutée au niveau du cluster qui héberge le premier déploiement.

**Solution de contournement**

Si possible, supprimez le premier déploiement et le service cloud d’origine, puis redéployez le service cloud. Cette action peut avoir pour effet de réaffecter le premier déploiement dans un cluster qui dispose de suffisamment de ressources pour les deux déploiements ou dans un cluster qui prend en charge les tailles de machine virtuelle demandées.

### Scénario d’allocation : groupe d’affinités - proximité entre la machine virtuelle et le service
**Erreur**

New\_General* ou New\_VMSizeNotSupported*

**Cause de l’épinglage au cluster**

Les ressources de calcul affectées à un groupe d’affinités sont liées à un seul cluster. Les nouvelles demandes de ressources de calcul portant sur ce groupe d’affinités sont exécutées dans le même cluster hébergeant les ressources existantes. Cela est vrai que les nouvelles ressources soient créées via un nouveau service cloud ou via un service cloud existant.

**Solution de contournement**

Si possible, évitez d’utiliser le groupe d’affinités ou essayez de regrouper vos ressources de calcul dans plusieurs groupes d’affinités.

### Scénario d’allocation : réseau virtuel par groupe d’affinités
**Erreur**

New\_General* ou New\_VMSizeNotSupported*

**Cause de l’épinglage au cluster**

Avant l’annonce du réseau virtuel régional, vous avez dû associer un réseau virtuel à un groupe d’affinités. Les ressources de calcul placées dans le groupe d’affinités sont donc liées aux mêmes contraintes que celles décrites dans le « scénario d’allocation : groupe d’affinités - proximité entre la machine virtuelle et le service » ci-dessus. Les ressources de calcul sont liées à un cluster.

**Solution de contournement**

Si vous n’avez pas besoin du groupe d’affinités, créez un nouveau réseau virtuel régional pour les nouvelles ressources que vous souhaitez ajouter, puis [connectez votre réseau virtuel existant au nouveau réseau virtuel](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). En savoir plus sur le [réseau virtuel régional](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Vous pouvez également [migrer votre réseau virtuel par groupe d’affinités vers le réseau virtuel régional](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), puis essayez à nouveau d’ajouter les ressources souhaitées.

## Résolution d’échecs de scénarios d’allocation spécifiques dans le modèle de déploiement Azure Resource Manager
Voici les scénarios courants d’allocation qui entraînent l’épinglage d’une demande d’allocation. Nous aborderons chaque scénario ultérieurement dans cet article.

- Redimensionnement d'une machine virtuelle ou ajout de machines virtuelles ou d'instances de rôle supplémentaires à un service cloud existant
- Redémarrage de machines virtuelles arrêtées (désallouées) - désallocation **partielle**
- Redémarrage de machines virtuelles arrêtées (désallouées) - désallocation **complète**

Lorsque vous recevez une erreur d’allocation, voyez si le scénario décrit s’applique à votre cas de figure. Utilisez l’erreur d’allocation renvoyée par la plateforme Azure pour identifier le scénario correspondant. Si votre demande est épinglée à un cluster existant, essayez de supprimer certaines contraintes d’épinglage pour élargir votre demande à plusieurs clusters et augmenter ainsi les chances de succès de l’allocation.

En règle générale, tant que l’erreur n’indique pas que la taille de machine virtuelle demandée n’est pas prise en charge, vous pouvez toujours réessayer ultérieurement, car il est possible que des ressources suffisantes aient été libérées dans le cluster pour prendre en charge votre demande. Si le problème est lié à la non prise en charge de la taille de machine virtuelle demandée, reportez-vous aux solutions de contournement ci-dessous.

### Scénario d’allocation : redimensionnement d’une machine virtuelle ou ajout de machines virtuelles supplémentaires à un groupe à haute disponibilité existant
**Erreur**

Upgrade\_VMSizeNotSupported* ou GeneralError*

**Cause de l’épinglage au cluster**

La demande de redimensionnement d’une machine virtuelle ou d’ajout d’une machine virtuelle à un groupe à haute disponibilité existant doit être exécutée sur le cluster d’origine qui héberge le groupe à haute disponibilité existant. La création d’un nouveau groupe à haute disponibilité permet à la plateforme Azure de trouver un autre cluster disposant de ressources ou prenant en charge la taille de machine virtuelle que vous avez demandée.

**Solution de contournement**

Si l’erreur est Upgrade\_VMSizeNotSupported*, essayez une autre taille de machine virtuelle. Si vous ne pouvez pas utiliser une autre taille de machine virtuelle, arrêtez toutes les machines virtuelles dans le groupe à haute disponibilité. Vous pourrez ainsi modifier la taille de la machine virtuelle, ce qui vous permettra d’allouer la machine virtuelle à un cluster prenant en charge la taille de machine virtuelle souhaitée.

Si l’erreur est GeneralError*, il est probable que le type de ressource (par exemple, une taille de machine virtuelle particulière) soit pris en charge par le cluster, mais que ce dernier ne puisse pas libérer de ressources pour le moment. Si la machine virtuelle peut faire partie d’un autre groupe à haute disponibilité, créez une nouvelle machine virtuelle dans un autre groupe à haute disponibilité (dans la même région). Cette nouvelle machine virtuelle peut ensuite être ajoutée au même réseau virtuel.

### Scénario d’allocation : redémarrage de machines virtuelles arrêtées (désallouées) - désallocation partielle
**Erreur**

GeneralError*

**Cause de l’épinglage au cluster**

Une désallocation **partielle** signifie que vous avez arrêté (désalloué) une ou plusieurs machines virtuelles, mais **pas toutes**, dans un groupe à haute disponibilité. Lorsque vous arrêtez (désallouez) une machine virtuelle, les ressources associées sont libérées. Le redémarrage de cette machine virtuelle constitue donc une nouvelle demande d’allocation. Le redémarrage de machines virtuelles dans un groupe à haute disponibilité partiellement désalloué revient à ajouter des machines virtuelles à un groupe à haute disponibilité existant. La demande d’allocation doit être tentée sur le cluster d’origine qui héberge le groupe à haute disponibilité existant.

**Solution de contournement**

Essayez d’arrêter toutes les machines virtuelles du groupe à haute disponibilité avant de redémarrer la première machine. Vous pourrez ainsi effectuer une nouvelle tentative d’allocation et sélectionner un nouveau cluster ayant une capacité disponible.

### Scénario d’allocation : redémarrage de machines virtuelles arrêtées (désallouées) - désallocation complète
**Erreur**

GeneralError*

**Cause de l’épinglage au cluster**

Une désallocation **complète** signifie que vous avez arrêté (désalloué) **toutes** les machines virtuelles d’un groupe à haute disponibilité. La demande d’allocation pour le redémarrage de ces machines virtuelles ciblera tous les clusters prenant en charge la taille souhaitée.

**Solution de contournement**

Essayez de sélectionner une nouvelle taille de machine virtuelle à allouer. Sinon, réessayez ultérieurement.

## Annexe
### Recherche de chaîne d’erreur
**New\_VMSizeNotSupported***

La taille de la machine virtuelle (ou la combinaisons de tailles de machines virtuelles) requise par ce déploiement ne peut pas être configurée en raison de contraintes liées à la demande de déploiement. Si possible, essayez d’assouplir les contraintes telles que les liaisons de réseaux virtuels, de déployer vers un service hébergé ne contenant aucun autre déploiement et vers un autre groupe d’affinités ou sans aucun groupe d’affinités, ou encore de déployer vers une autre région.

**New\_General***

Échec de l’allocation ; impossible de satisfaire aux contraintes de la demande. Le déploiement du nouveau service demandé est lié à un groupe d’affinités ou cible un réseau virtuel, ou ce service hébergé comporte déjà un déploiement. Toutes ces conditions réduisent le nouveau déploiement à certaines ressources Azure spécifiques. Veuillez réessayer ultérieurement, ou bien essayez de réduire la taille de la machine virtuelle ou le nombre d’instances de rôle. Vous pouvez également, si possible, supprimer les contraintes mentionnées ci-dessus ou tenter d’effectuer le déploiement vers une autre région.

**Upgrade\_VMSizeNotSupported***

Impossible de mettre à niveau le déploiement. La taille de la machine virtuelle XXX demandée n’est peut-être pas disponible dans les ressources prenant en charge le déploiement existant. Veuillez réessayer ultérieurement, essayez avec une taille de machine virtuelle différente ou un plus petit nombre d’instances de rôle, ou bien créez un déploiement sous un service hébergé vide avec un nouveau groupe d’affinités ou sans liaison à un groupe d’affinités.

**GeneralError***

une erreur interne s'est produite sur le serveur. Relancez la demande. » ou « Impossible de produire une allocation pour le service.

## Ressources supplémentaires
### Contacter le Support technique Azure

Si cet article ne vous a pas aidé à résoudre votre problème avec Azure, parcourez les forums Azure sur [MSDN et Stack Overflow](http://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur Obtenir de l’aide. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).

<!---HONumber=AcomDC_1203_2015-->