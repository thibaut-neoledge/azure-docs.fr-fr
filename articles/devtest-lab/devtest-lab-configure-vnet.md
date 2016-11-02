<properties
	pageTitle="Configuration d’un réseau virtuel dans Azure DevTest Labs | Microsoft Azure"
	description="Découvrez comment configurer un réseau virtuel et un sous-réseau existants, puis les utiliser dans une machine virtuelle avec Azure DevTest Labs"
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
	ms.date="09/06/2016"
	ms.author="tarcher"/>

# Configuration d’un réseau virtuel dans Azure DevTest Labs

Comme expliqué dans l’article [Ajouter une machine virtuelle avec des artefacts à un laboratoire](devtest-lab-add-vm-with-artifacts.md), quand vous créez une machine virtuelle dans un laboratoire, vous pouvez spécifier un réseau virtuel configuré. Voici l’un des scénarios de cette opération : vous devez être en mesure d'accéder aux ressources de votre réseau d'entreprise à partir de vos machines virtuelles à l'aide du réseau virtuel configuré avec ExpressRoute ou un VPN de site à site. Les sections suivantes montrent comment ajouter votre réseau virtuel existant aux paramètres de réseau virtuel d’un labo afin qu'il puisse être sélectionné lors de la création de machines virtuelles.

## Configurer un réseau virtuel pour un laboratoire en utilisant le portail Azure
Les étapes suivantes vous montrent comment ajouter un réseau virtuel (et sous-réseau) existant à un laboratoire pour qu’il puisse être utilisé lors de la création d’une machine virtuelle dans le même laboratoire.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.

1. Dans le panneau du laboratoire, sélectionnez **Configuration**.

1. Dans le panneau **Configuration** du laboratoire, sélectionnez **Réseaux virtuels**.

1. Sur le panneau **Réseaux virtuels**, vous verrez une liste de réseaux virtuels configurés pour le labo actuel ainsi que le réseau virtuel par défaut créé pour votre labo.

1. Sélectionnez **Ajouter**.

	![Ajout d’un réseau virtuel existant à votre labo](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
	
1. Sur le panneau **Réseau virtuel**, sélectionnez **[Sélectionner un réseau virtuel]**.

	![Sélection d’un réseau virtuel existant](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
	
1. Sur le panneau **Choisissez un réseau virtuel**, sélectionnez le réseau virtuel souhaité. Le panneau affiche tous les réseaux virtuels sous la même région de l'abonnement que le labo.

1. Après avoir sélectionné un réseau virtuel, vous revenez au panneau **réseau virtuel**, et plusieurs champs sont activés.

	![Sélection d’un réseau virtuel existant](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. Spécifiez une description de votre combinaison réseau virtuel / labo.

1. Pour permettre l’utilisation d’un sous-réseau dans le labo de création de machines virtuelles, sélectionnez **UTILISER DANS LA CRÉATION DE MACHINES VIRTUELLES**.

1. Pour autoriser les adresses IP publiques dans un sous-réseau, sélectionnez **AUTORISER LES ADRESSES IP PUBLIQUES**.

1. Dans le champ **NOMBRE MAXIMAL DE MACHINES VIRTUELLES PAR UTILISATEUR**, spécifiez le nombre maximal de machines virtuelles par utilisateur pour chaque sous-réseau. Si vous souhaitez un nombre illimité de machines virtuelles, laissez ce champ vide.

1. Sélectionnez **Enregistrer**.

1. Maintenant que le réseau virtuel est configuré, il peut être sélectionné lors de la création d'une machine virtuelle. Pour voir comment créer une machine virtuelle et spécifier un réseau virtuel, consultez l’article [Ajouter une machine virtuelle avec des artefacts à un laboratoire](devtest-lab-add-vm-with-artifacts.md).

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Étapes suivantes

Une fois que vous avez ajouté le réseau virtuel souhaité à votre laboratoire, l’étape suivante consiste à [ajouter une machine virtuelle à votre laboratoire](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0907_2016-->