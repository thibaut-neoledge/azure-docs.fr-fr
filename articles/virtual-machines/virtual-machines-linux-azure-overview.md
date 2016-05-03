 <properties
   pageTitle="Azure et Linux | Microsoft Azure"
   description="Décrit les services de calcul, de stockage et de mise en réseau Azure à l’aide de machines virtuelles Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="rickstercdn"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/01/2016"
   ms.author="rclaus"/>

# Azure et Linux
Microsoft Azure propose une gamme croissante de services cloud publics intégrés, comprenant des analyses, des machines virtuelles, des bases de données, mobiles, la mise en réseau, le stockage et le web. En d’autres termes, il s’agit de la méthode idéale pour héberger vos solutions. Microsoft Azure fournit une plate-forme évolutive de cloud computing qui vous permet de payer uniquement ce que vous utilisez, quand vous le souhaitez, sans avoir à investir dans du matériel en local. Azure permet de faire face à toutes les exigences en matière de montée en puissance de vos solutions ou d’augmentation de la taille des instances.
 
## Azure Virtual Machines
Les machines virtuelles Azure vous permettent de déployer un large éventail de solutions informatiques et ce, en toute flexibilité. Déployez une machine virtuelle Windows ou Linux à partir de la galerie d’images ou une machine virtuelle personnalisée créée par l’un de nos nombreux partenaires. Vous pouvez pratiquement déployer n’importe quelle charge de travail et n’importe quel langage sur presque tous les systèmes d’exploitation. Vous ne trouvez toujours pas ce que vous cherchez ? Ne vous inquiétez pas, vous pouvez également ajouter vos propres images en local.
 
## Prise en main de Microsoft Azure sur Linux

Combinez les machines virtuelles Microsoft Azure avec le stockage et la mise en réseau pour fournir IaaS à l’échelle d’Internet pour tous vos besoins informatiques sur Linux. La prise en main de Microsoft Azure sur Linux nécessite les éléments suivants :

1. Un compte d’évaluation gratuit. [En obtenir un](https://azure.microsoft.com/pricing/free-trial/).
2. L’interface de ligne de commande Azure pour Linux, Mac et Windows (Azure CLI). [L’installer](../xplat-cli-install.md).
3. Une machine virtuelle Linux [La créer](virtual-machines-linux-quick-create-cli.md).
4. Plus d’informations sur Linux et Azure, notamment comment bénéficier du contrat de niveau de service. **Lisez ce document, même si les documents juridiques ne vous parlent pas**.

## Questions logistiques : régions, distributions, disponibilité, taille des machines virtuelles et quotas
### Régions
Les ressources Microsoft Azure sont réparties sur plusieurs régions géographiques dans le monde. Une « région » représente plusieurs centres de données au sein d’une seule zone géographique. Au 1er janvier 2016, elles étaient au nombre de 8 en Amérique, 2 en Europe, 6 en Asie-Pacifique, 2 en Chine continentale et 3 en Inde. Si vous souhaitez obtenir la liste complète de toutes les régions Azure, nous tenons une liste des régions existantes et récemment annoncées **[ici](https://azure.microsoft.com/regions/)**.

### Distributions
Microsoft Azure prend en charge un certain nombre de distributions Linux populaires fournies et gérées par plusieurs partenaires. Vous trouverez des distributions comme CentOS, Debian, Red Hat Enterprise, Ubuntu, FreeBSD et plus encore dans Azure Marketplace. Nous travaillons activement avec différentes communautés Linux pour enrichir davantage la liste des distributions approuvées. **[Découvrez les distributions actuelles](virtual-machines-linux-endorsed-distros.md)**. Si votre distribution Linux préférée n’est pas présente dans la galerie, vous pouvez « apporter votre propre machine virtuelle Linux » en suivant les instructions **[sur cette page.](virtual-machines-linux-create-upload-generic.md)**

## Disponibilité et contrat SLA Microsoft Azure
Afin que votre déploiement puisse bénéficier de notre contrat de niveau de service de 99,95 % pour les machines virtuelles, vous devez déployer au moins deux machines virtuelles exécutant votre charge de travail à l’intérieur d’un groupe à haute disponibilité. Ainsi, vos machines virtuelles sont réparties sur plusieurs domaines d’erreur dans nos centres de données et déployées sur des hôtes ayant des fenêtres de maintenance distinctes. Pour des informations complètes sur notre contrat SLA, cliquez **[ici](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)**.

## Quotas et taille des machines virtuelles
Lorsque vous déployez une machine virtuelle dans Azure, vous sélectionnez parmi nos tailles disponibles une taille de machine virtuelle qui convient à votre charge de travail. La taille influe également sur les capacités de traitement, de mémoire et de stockage de la machine virtuelle. Vous êtes facturé en fonction de la durée du fonctionnement de la machine virtuelle et de la consommation de ses ressources allouées. Pour une liste plus complète, consultez l’article suivant sur les [tailles des machines virtuelles](virtual-machines-linux-sizes.md).

Voici quelques conseils de base pour la sélection d’une taille de machine virtuelle parmi nos séries (A, D, DS, G et GS).

* Les machines virtuelles de série A sont des machines virtuelles d’entrée de gamme offrant un bon rapport qualité/prix et qui conviennent pour des charges de travail légères et des scénarios de développement/test. Elles sont largement disponibles dans l’ensemble des régions et peuvent se connecter et utiliser toutes les ressources standard disponibles pour les machines virtuelles.
* Les tailles de série A (A8 - A11) sont des configurations spéciales de calcul intensif adaptées aux applications de cluster informatique à hautes performances.
* Les machines virtuelles de la série D sont conçues pour exécuter des applications qui nécessitent une puissance de calcul et des performances de disque temporaire supérieures. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et un disque SSD pour le disque temporaire. 
* La série Dv2 (la version la plus récente de la série D) offre un processeur plus puissant. Le processeur de la série Dv2 est environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération de processeur 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) et comporte la technologie 2.0 Intel Turbo Boost, et peut atteindre 3,2 GHz. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.
* Les machines virtuelles de la série G offrent le plus de mémoire et s’exécutent sur des hôtes équipés de processeurs de la famille Intel Xeon E5 V3.

Remarque : les machines virtuelles des séries DS et GS peuvent utiliser un stockage Premium, qui offre un stockage SSD hautes performances à faible latence pour les charges de travail impliquant des E/S intensives. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article **[Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure (en anglais)](../storage/storage-premium-storage.md)**.

Chaque abonnement Azure comporte des limites de quota qui peuvent avoir un impact négatif sur le déploiement d’un grand nombre de machines virtuelles pour votre projet. La limite est de 20 machines virtuelles par région et par abonnement. Ces limites de quota peuvent être augmentées en soumettant un ticket de support demandant leur hausse. Pour plus d’informations sur les limites de quota, consultez **[les limites du service d’abonnement Azure](../azure-subscription-service-limits.md)**

## Étapes suivantes

Un compte d’évaluation gratuit. **[En obtenir un](https://azure.microsoft.com/pricing/free-trial/)**. Si vous en possédez déjà un, **[installez l’interface de ligne de commande Azure](../xplat-cli-install.md)** pour l’essayer. Si c’est déjà fait, [créez votre machine virtuelle Linux](virtual-machines-linux-quick-create-cli.md).

<!---HONumber=AcomDC_0420_2016-->