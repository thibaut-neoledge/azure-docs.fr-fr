<properties 
   pageTitle="Définition d’une adresse IP privée statique en mode classique à l’aide de l’interface de ligne de commande | Microsoft Azure"
   description="Présentation des adresses IP privées statiques (adresses IP dynamiques) et de leur gestion en mode classique à l’aide de l’interface de ligne de commande"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Définition d’une adresse IP privée statique (classique) dans l’interface de ligne de commande Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article traite du modèle de déploiement classique. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement de Resource Manager](virtual-networks-static-private-ip-arm-cli.md).

Les exemples de commandes d’interface de ligne de commande Azure supposent qu’un environnement simple a déjà été créé. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test décrit dans [Créer un réseau virtuel](virtual-networks-create-vnet-classic-cli.md).

## Spécification d’une adresse IP privée statique lors de la création d’une machine virtuelle
Pour créer une machine virtuelle nommée *DNS01* dans un nouveau service cloud nommé *TestService* selon le scénario ci-dessus, procédez comme suit :

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, voir [Installation et configuration de l’interface de ligne de commande Azure](xplat-cli-install.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
1. Exécutez la commande **azure service create** pour créer le service cloud.

		azure service create TestService --location uscentral

	Sortie attendue :

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name TestService
		info:    service create command OK
	
2. Exécutez la commande **azure create vm** commande pour créer la machine virtuelle. Notez la valeur d’une adresse IP privée statique. La liste affichée après le résultat présente les différents paramètres utilisés.

		azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

	Sortie attendue :

		info:    Executing command vm create
		warn:    --vm-size has not been specified. Defaulting to "Small".
		info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
		info:    Looking up virtual network
		info:    Looking up cloud service
		warn:    --location option will be ignored
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Retrieving storage accounts
		info:    Creating VM
		info:    OK
		info:    vm create command OK

	- **-l (ou --location)**. Région Azure où la machine virtuelle sera créée. Pour notre scénario, *centralus*.
	- **-n (ou--vm-name)**. Nom de la machine virtuelle à créer.
	- **-w (ou --virtual-network-name)**. Nom du réseau virtuel où la machine virtuelle sera créée. 
	- **-S (ou --static-ip)**. Adresse IP privée statique de la machine virtuelle.
	- **TestService**. Nom du service cloud dans lequel la machine virtuelle sera créée.
	- **bd507d3a70934695bc2128e3e5a255ba\_\_RightImage-Windows-2012R2-x64-v14.2**. Image utilisée pour créer la machine virtuelle.
	- **adminuser**. Administrateur local pour la machine virtuelle Windows.
	- ****AdminP@ssw0rd**. Mot de passe administrateur local pour la machine virtuelle Windows.

## Récupération d’informations d’adresse IP privée statique pour une machine virtuelle
Pour visualiser les informations d’adresse IP privée statique concernant la machine virtuelle créée avec le script ci-dessus, exécutez la commande d’interface de ligne de commande Azure ci-après, et examinez la valeur *Network StaticIP* :

	azure vm static-ip show DNS01

Sortie attendue :

	info:    Executing command vm static-ip show
	info:    Getting virtual machines
	data:    Network StaticIP "192.168.1.101"
	info:    vm static-ip show command OK

## Suppression d’une adresse IP privée statique d’une machine virtuelle
Pour supprimer l’adresse IP privée statique ajoutée à la machine virtuelle dans le script ci-dessus, exécutez la commande d’interface de ligne de commande Azure suivante :
	
	azure vm static-ip remove DNS01

Sortie attendue :

	info:    Executing command vm static-ip remove
	info:    Getting virtual machines
	info:    Reading network configuration
	info:    Updating network configuration
	info:    vm static-ip remove command OK

## Ajout d’une adresse IP privée statique à une machine virtuelle existante
Pour ajouter une adresse IP privée statique à la machine virtuelle créée à l’aide du script ci-dessus, exécutez la commande suivante :

	azure vm static-ip set DNS01 192.168.1.101

Sortie attendue :

	info:    Executing command vm static-ip set
	info:    Getting virtual machines
	info:    Looking up virtual network
	info:    Reading network configuration
	info:    Updating network configuration
	info:    vm static-ip set command OK

## Étapes suivantes

- En savoir plus sur les [adresses IP publiques réservées](../virtual-networks-reserved-public-ip).
- En savoir plus sur les [adresses IP publiques de niveau d’instance](../virtual-networks-instance-level-public-ip).
- Consulter les [API REST d’adresse IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_1217_2015-->