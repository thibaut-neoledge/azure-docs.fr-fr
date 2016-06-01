<properties
	pageTitle="Création de plusieurs machines virtuelles | Microsoft Azure"
	description="Options de création de plusieurs machines virtuelles sous Windows"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="guybo"/>

# Création de plusieurs machines virtuelles Azure

Il existe de nombreux cas de figure où vous devez créer un grand nombre de machines virtuelles similaires. C’est le cas notamment pour le calcul hautes performances (HPC), l’analyse des données à grande échelle, les serveurs intermédiaires ou principaux évolutifs et souvent sans état (comme les serveurs Web) ainsi que les bases de données distribuées.

Cet article présente les options permettant de créer plusieurs machines virtuelles dans Azure. Ces options vont bien au-delà des cas simples où vous créez manuellement une série de machines virtuelles. Si vous devez créer beaucoup de machines virtuelles, les processus que vous utilisez le plus souvent ne sont pas adaptés.

Pour créer de nombreuses machines virtuelles similaires, une solution consiste à utiliser la construction Azure Resource Manager des _boucles de ressources_.

## Boucles de ressources

Les boucles de ressources sont un raccourci syntaxique dans les modèles Azure Resource Manager. Elles peuvent créer un ensemble de ressources à la configuration similaire dans une boucle. Vous pouvez utiliser des boucles de ressources pour créer plusieurs comptes de stockage, interfaces réseau ou machines virtuelles. Pour plus d’informations sur les boucles de ressources, consultez [Création de machines virtuelles dans des groupes à haute disponibilité à l’aide de boucles de ressources](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## Les problèmes de la mise à l’échelle

Bien que les boucles de ressources facilitent la création d’une infrastructure cloud à l’échelle et produisent des modèles plus concis, elles posent certains problèmes. Par exemple, si vous utilisez une boucle de ressources pour créer 100 machines virtuelles, vous devez mettre en corrélation des cartes réseau (NIC) avec les machines virtuelles et les comptes de stockage correspondants. Comme le nombre de machines virtuelles peut ne pas être identique au nombre de comptes de stockage, vous devez également gérer des boucles de ressources de taille différente. Ces problèmes peuvent être résolus, mais la complexité augmente considérablement avec la mise à l’échelle.

La mise en place d’une infrastructure évolutive et souple est un autre problème. Par exemple, vous souhaitez une infrastructure qui augmente ou diminue automatiquement le nombre de machines virtuelles en fonction de la charge de travail. Les machines virtuelles ne disposent pas d’un mécanisme intégré permettant de modifier leur nombre (augmentation ou diminution). Si vous effectuez une mise à l’échelle par suppression de machines virtuelles, il est difficile de garantir une haute disponibilité et la répartition des machines virtuelles entre différents domaines de mise à jour et d’erreur.

Enfin, lorsque vous utilisez une boucle de ressources, plusieurs appels de création de ressources sont envoyés à l’infrastructure sous-jacente. Lorsque plusieurs appels créent des ressources similaires, Azure peut améliorer implicitement cette conception et optimiser la fiabilité et les performances du déploiement. C’est ici qu’interviennent les _jeux de mise à l’échelle de machine virtuelle_.

## Jeux de mise à l’échelle de machine virtuelle

Les jeux de mise à l’échelle de machine virtuelle sont une ressource Azure Cloud Services permettant de déployer et gérer un jeu de machines virtuelles identiques. Avec toutes les machines virtuelles configurées à l’identique, les jeux de mise à l’échelle de machine virtuelle sont faciles à ajuster. Il vous suffit de modifier le nombre de machines virtuelles dans le jeu. Vous pouvez également configurer la mise à l’échelle automatique des ensembles de mise à l’échelle de machine virtuelle en fonction des exigences de la charge de travail.

Pour les applications nécessitant une mise à l’échelle des ressources de calcul internes et externes, les opérations de mise à l’échelle sont équilibrées de façon implicite sur plusieurs domaines d’erreur et de mise à jour.

Au lieu d’effectuer la mise en corrélation de plusieurs ressources telles que des cartes réseau et des machines virtuelles, un jeu de mise à l’échelle comprend un réseau, un stockage, une machine virtuelle et des propriétés d’extension que vous pouvez configurer de manière centralisée.

Pour une présentation des jeux de mise à l’échelle de machine virtuelle, consultez la page [Jeux de mise à l’échelle de machine virtuelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Pour plus d’informations, consultez la [documentation Jeux de mise à l’échelle de machine virtuelle](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0518_2016-->