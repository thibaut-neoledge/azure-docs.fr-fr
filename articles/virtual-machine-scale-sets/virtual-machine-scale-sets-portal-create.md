<properties
	pageTitle="Création de portail d’interface de ligne de commande de jeux de mise à l’échelle | Microsoft Azure"
	description="Déployez des jeux de mise à l’échelle à l’aide du portail Azure."
	keywords="Jeux de mise à l’échelle de machine virtuelle" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="gatneil"/>

# Créer un jeu de mise à l’échelle à l’aide du portail Azure

Ce didacticiel vous montre à quel point il est facile de créer jeu de mise à l’échelle de machines virtuelles en quelques minutes à l’aide du portail Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## Choisir l’image de machine virtuelle à partir de Marketplace

À partir du portail, vous pouvez facilement déployer un jeu de mise à l’échelle avec des images CentOS, CoreOS, Debian, Open Suse, Red Hat Enterprise Linux, SUSE Linux Enterprise Server ou les Ubuntu Server.

Tout d’abord, accédez au [portail Azure](https://portal.azure.com) dans un navigateur web. Cliquez sur `New`, recherchez `scale set`, puis sélectionnez l’entrée `Virtual machine scale set` :

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## Créer la machine virtuelle Windows

Vous pouvez à présent utiliser les paramètres par défaut pour créer rapidement la machine virtuelle.

* Sur le panneau `Basics`, entrez un nom pour le jeu de mise à l’échelle. Ce nom constituant la base du nom de domaine complet (FQDN) de l’équilibreur de charge placé devant le jeu de mise à l’échelle, assurez-vous qu’il est unique dans Azure.

* Sélectionnez le type de système d’exploitation souhaité, entrez le nom d’utilisateur de votre choix, puis sélectionnez le type d’authentification que vous préférez. Si vous choisissez de définir un mot de passe, celui-ci doit compter au moins 12 caractères et remplir trois des quatre conditions suivantes : une lettre minuscule, une lettre majuscule, un chiffre et un caractère spécial. En savoir plus sur les [conditions requises pour les noms d’utilisateur et les mots de passe](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Si vous choisissez `SSH public key`, assurez-vous de coller uniquement votre clé publique, PAS votre clé privée :

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Entrez le nom de groupe de ressources et l’emplacement de votre choix, puis cliquez sur `OK`.

* Sur le panneau `Virtual machine scale set service settings` : entrez l’étiquette de nom de domaine de votre choix (servant de base au nom de domaine complet pour l’équilibreur de charge placé devant le jeu de mise à l’échelle). Cette étiquette doit être unique dans Azure.

* Choisissez l’image de disque du système d’exploitation, le nombre d’instances et la taille de machine que vous souhaitez.

* Activez ou désactivez la mise à l’échelle automatique et configurez-la si elle est activée :

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* Sur le panneau `Summary`, une fois la validation terminée, cliquez sur `OK`.

* Enfin, sur le panneau `Purchase`, cliquez sur `Purchase` pour démarrer le déploiement du jeu de mise à l’échelle.

## Se connecter à une machine virtuelle dans le jeu de mise à l’échelle

Une fois votre jeu de mise à l’échelle déployé, accédez à la l’onglet `Inbound NAT Rules` de l’équilibreur de charge pour le jeu de mise à l’échelle :

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

Vous pouvez vous connecter à chaque machine virtuelle dans le jeu de mise à l’échelle en utilisant des règles NAT. Par exemple, pour un jeu de mise à l’échelle Windows, s’il existe une règle NAT sur le port entrant 50000, vous pourriez vous connecter à cette machine via protocole RDP sur `<load-balancer-ip-address>:50000`. Pour un jeu de mise à l’échelle Linux, vous vous connecteriez en utilisant la commande `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## Étapes suivantes

Pour obtenir de la documentation sur le déploiement de jeux de mise à l’échelle à partir de l’interface de ligne de commande, voir [cette documentation](./virtual-machine-scale-sets-cli-quick-create.md).

Pour obtenir de la documentation sur le déploiement de jeux de mise à l’échelle à partir de Powershell, voir [cette documentation](./virtual-machine-scale-sets-windows-create.md).

Pour obtenir de la documentation sur le déploiement de jeux de mise à l’échelle à partir de Visual Studio, voir [cette documentation](./virtual-machine-scale-sets-vs-create.md).

Pour obtenir une documentation générale, voir la [page de présentation de la documentation relative aux jeux de mise à l’échelle](./virtual-machine-scale-sets-overview.md).

Pour des informations générales, voir la [page d’accueil principale pour les jeux de mise à l’échelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0928_2016-->