<properties
	pageTitle="Configuration d’un réseau virtuel pour un Dev/Test Lab | Microsoft Azure"
	description="Apprenez à configurer un réseau virtuel et un sous-réseau existants et à les utiliser dans une machine virtuelle"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="tarcher"/>

# Configuration d’un réseau virtuel pour un Dev/Test Lab

## Vue d'ensemble

Comme expliqué dans l'article [Ajout d’une machine virtuelle avec des artefacts à un Dev/Test Lab Azure](devtest-lab-add-vm-with-artifacts.md), lorsque vous créez une machine virtuelle dans un labo, vous pouvez spécifier un réseau virtuel configuré (et un sous-réseau) pour cette machine virtuelle. Voici l’un des scénarios de cette opération : vous souhaitez être en mesure d'accéder aux ressources de votre réseau d'entreprise à partir de vos machines virtuelles à l'aide du réseau virtuel configuré avec ExpressRoute ou un VPN de site à site. Les sections suivantes montrent comment ajouter votre réseau virtuel existant aux paramètres de réseau virtuel du labo afin qu'il puisse être sélectionné lors de la création de vos machines virtuelles.

## Configuration d’un réseau virtuel pour un Dev/Test Lab à l’aide du portail Azure
Les étapes suivantes vous montrent comment ajouter un réseau virtuel (et sous-réseau) existant à un Dev/Test Lab afin qu'il puisse être utilisé lors de la création d'une machine virtuelle dans le même labo.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des labos, sélectionnez le labo souhaité.

1. Le panneau **Paramètres** du labo sélectionné s’affiche.

1. Appuyez sur **Réseaux virtuels**.

	![Les réseaux virtuels peuvent être configurés à partir du panneau Paramètres du labo](./media/devtest-lab-configure-vnet/lab-settings-vnet.png)
	
1. Sur le panneau **Réseaux virtuels**, vous verrez une liste de réseaux virtuels configurés pour le labo actuel ainsi que le réseau virtuel par défaut créé pour votre labo.

1. Appuyez sur **+ Ajouter**.

	![Ajout d’un réseau virtuel existant à votre labo](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
	
1. Sur le panneau **Réseau virtuel**, cliquez sur **[Sélectionner un réseau virtuel]**.

	![Sélection d’un réseau virtuel existant](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
	
1. Sur le panneau **Choisissez un réseau virtuel**, sélectionnez le réseau virtuel souhaité. Le panneau affiche tous les réseaux virtuels sous la même région de l'abonnement que le labo.

1. Une fois que vous aurez sélectionné un réseau virtuel et que vous serez revenu au panneau **Réseau virtuel**, vous constaterez que plusieurs champs ont été activés.

	![Sélection d’un réseau virtuel existant](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. Spécifiez une description de votre combinaison réseau virtuel / labo.

1. Pour permettre l’utilisation d’un sous-réseau dans le labo de création de machines virtuelles, cochez l’option **UTILISER DANS LA CRÉATION DE MACHINES VIRTUELLES**.

1. Pour autoriser les adresses IP publiques dans un sous-réseau, cochez l’option **AUTORISER LES ADRESSES IP PUBLIQUES**.

1. Dans le champ **NOMBRE MAXIMAL DE MACHINES VIRTUELLES PAR UTILISATEUR**, spécifiez le nombre maximal de machines virtuelles par utilisateur pour chaque sous-réseau. Si vous souhaitez un nombre illimité de machines virtuelles, laissez ce champ vide.

1. Appuyez sur **Enregistrer**.

1. Maintenant que le réseau virtuel est configuré, il peut être sélectionné lors de la création d'une nouvelle machine virtuelle. Ceci est expliqué dans l'article [Ajout d’une machine virtuelle avec des artefacts à un Dev/Test Lab Azure](devtest-lab-add-vm-with-artifacts.md).

## Étapes suivantes

Une fois que vous avez ajouté le ou les réseaux virtuels souhaités à votre labo, l'étape suivante consiste à [ajouter une machine virtuelle à votre Dev/Test Lab](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0323_2016-->