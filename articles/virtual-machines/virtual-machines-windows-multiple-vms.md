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

Il existe de nombreux scénarios dans lesquels vous devez créer un grand nombre de machines virtuelles similaires, comme par exemple le calcul hautes performances (HPC), l’analyse des données à grande échelle, les serveurs intermédiaires ou principaux évolutifs et souvent sans état (par exemple, des serveurs Web) et les bases de données distribuées.

Cet article décrit les options disponibles pour créer plusieurs machines virtuelles dans Azure qui dépassent la simple création manuelle d’une série de machines virtuelles, ce qui limite l’évolutivité si vous avez besoin créer un certain nombre de machines virtuelles.

Pour optimiser la création de machines virtuelles similaires, vous pouvez utiliser la construction Azure Resource Manager de _boucles de ressources_.

## Boucles de ressources

Les boucles de ressources sont un raccourci syntaxique dans Azure Resource Manager. Elles vous permettent de créer un ensemble de ressources configurées de manière similaire dans une boucle. Vous pouvez utiliser des boucles de ressources pour créer par exemple plusieurs comptes de stockage, des interfaces réseau et des machines virtuelles. Pour plus d’informations sur les boucles de ressources, référez-vous à l’article [Création de machines virtuelles dans des groupes à haute disponibilité à l’aide de boucles de ressources](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## Les défis de la mise à l’échelle

Bien que les boucles de ressources facilitent la création d’une infrastructure de cloud à l’échelle et autorisent des modèles plus concis, elles posent certains défis. Par exemple, si vous créez 100 machines virtuelles à l’aide d’une boucle de ressources, vous devez mettre en corrélation les cartes réseau, les machines virtuelles et les comptes de stockage lorsque vous définissez les machines virtuelles. Le nombre de machines virtuelles et de comptes de stockage sont susceptibles d’être différents, ce qui a pour conséquence des tailles de boucle différentes. Ces problèmes peuvent être résolus, mais la complexité augmente considérablement avec la mise à l’échelle.

Vous pouvez cependant rencontrer des problèmes si vous avez besoin d’une infrastructure pouvant être facilement mise à l’échelle, notamment avec la mise à l’échelle automatique (l’augmentation ou la diminution du nombre de machines virtuelles en réponse fonction d’une charge de travail). Les machines virtuelles ne disposent pas d’un mécanisme intégré permettant de modifier leur nombre (augmentation ou diminution). Si vous mettez à l’échelle en diminuant le nombre de machines virtuelles, il est également difficile de garantir la haute disponibilité en répartissant les machines virtuelles sur différents domaines de mise à jour et d’erreur.

Enfin, même si les boucles de ressources constituent un raccourci syntaxique, plusieurs appels sont envoyés à l’infrastructure sous-jacente pour créer des ressources lorsque vous en utilisez une. Lorsque plusieurs appels créent des ressources similaires, Azure dispose d’une opportunité implicite d’améliorer cette conception pour optimiser la fiabilité du déploiement et les performances. C’est ici qu’interviennent les _jeux de mise à l’échelle de machine virtuelle_.

## Jeux de mise à l’échelle de machine virtuelle

Les jeux de mise à l’échelle de machine virtuelle sont des ressources de calcul Azure permettant de déployer et de gérer un jeu de machines virtuelles identiques. Avec toutes les machines virtuelles configurées de la même manière, les jeux de mise à l’échelle de machine virtuelle sont faciles à mettre à l’échelle par une simple modification du nombre de machines virtuelles dans le jeu. Et ils peuvent être configurés pour la mise à l’échelle automatique en fonction des exigences liées à la charge de travail.

Pour les applications nécessitant une mise à l’échelle des ressources de calcul internes et externes, les opérations de mise à l’échelle sont équilibrées de façon implicite sur plusieurs domaines d’erreur et de mise à jour.

Au lieu d’effectuer une mise en corrélation de plusieurs ressources telles que les cartes réseau et les machines virtuelles, un jeu de mise à l’échelle comprend un réseau, un stockage, une machine virtuelle et des propriétés d’extension que vous pouvez configurer de manière centralisée.

Vous trouverez une introduction sur les jeux de mise à l’échelle de machine virtuelle à la [page du produit](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Pour plus d’informations, consultez la [documentation](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0504_2016-->