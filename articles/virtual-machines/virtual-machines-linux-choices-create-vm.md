<properties 
	pageTitle="Différentes façons de créer une machine virtuelle Linux" 
	description="Répertorie les différentes façons de créer une machine virtuelle Linux et fournit des liens vers des instructions." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services"
	ms.date="05/14/2015" 
	ms.author="kathydav"/>

# Différentes façons de créer une machine virtuelle Linux

Microsoft Azure propose différents moyens de créer des machines virtuelles, adaptées à tous les utilisateurs et toutes les applications. En conséquence, vous devez effectuer certains choix relatifs à la machine virtuelle et à sa création. Cet article vous propose un résumé de ces options et des liens pour obtenir des instructions.

Les modèles Microsoft Azure Manager ont été récemment introduits comme un moyen de créer et de gérer une machine virtuelle et ses différentes ressources en tant qu’unité logique unique de déploiement. Des instructions relatives à cette approche sont fournies ci-dessous, le cas échéant. Pour plus d’informations sur Microsoft Azure Resource Manager et sur la gestion des ressources en tant qu’unité unique, consultez la [vue d’ensemble][].

## Choix des outils

### Interface utilisateur graphique : le portail ou le portail en version préliminaire Microsoft Azure 

L’interface utilisateur graphique du portail Microsoft Azure constitue un moyen simple de tester une machine virtuelle, en particulier si vous venez de démarrer avec Microsoft Azure. Pour créer la machine virtuelle, utilisez le portail Microsoft Azure ou le portail Microsoft Azure en version préliminaire. Pour obtenir des instructions générales, consultez la page [Création d’une machine virtuelle exécutant Linux][].

### Interface de commande : interface de ligne de commande Microsoft Azure ou Microsoft Azure PowerShell

Si vous préférez travailler dans une interface de commande, choisissez l’interface de ligne de commande Microsoft Azure ou Microsoft Azure PowerShell, qui comporte des applets de commande Windows PowerShell pour Microsoft Azure et une console personnalisée.

Pour l’interface de ligne de commande Microsoft Azure, consultez [Commandes équivalentes du Gestionnaire de ressources et de la gestion des services pour les opérations de machine virtuelle avec l’interface de ligne de commande Azure pour Mac, Linux et Windows][]. Pour utiliser un modèle, consultez Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure.

Pour Microsoft Azure PowerShell, consultez la page [Utilisation de Microsoft Azure PowerShell pour créer et préconfigurer les machines virtuelles Linux][]. Pour utiliser un modèle, consultez la page [Déploiement et gestion de machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell][].

### Environnement de développement : Visual Studio

[Création d’une machine virtuelle pour un site Web avec Visual Studio][]

[Déployer les ressources Microsoft Azure à l’aide des bibliothèques de traitement, réseau et de stockage .NET][]

## Système d’exploitation et choix d’images

Choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils. Sinon, utilisez l’une de vos propres images.

### Images Microsoft Azure

Ces instructions vous expliquent comment utiliser une image Microsoft Azure pour créer une machine virtuelle personnalisée avec des options pour la mise en réseau, l’équilibrage de charge, et bien davantage. Consultez la section [Création d’une machine virtuelle personnalisée exécutant Linux dans Azure][].

### Utiliser votre propre image

Utilisez une image basée sur une machine virtuelle Microsoft Azure existante, en *capturant* cette machine virtuelle ou en téléchargeant l’une de vos propres images stockée sur un disque dur virtuel :

- [Capture d’une machine virtuelle Linux à utiliser comme modèle à l’aide de l’interface de ligne de commande][]
- [Création et téléchargement d’un disque dur virtuel contenant le système d’exploitation Linux][]

## Étapes suivantes

[Connexion à la machine virtuelle][]

[Association d’un disque de données][]

## Ressources supplémentaires
[À propos des paramètres de configuration de machine virtuelle Azure][]

[Environnement de test de la configuration de base][]

[Environnements de test de cloud hybride Azure][]

<!-- LINKS -->
[vue d’ensemble]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Création d’une machine virtuelle exécutant Linux]: virtual-machines-linux-tutorial.md

[Commandes équivalentes du Gestionnaire de ressources et de la gestion des services pour les opérations de machine virtuelle avec l’interface de ligne de commande Azure pour Mac, Linux et Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Déploiement et gestion de machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Utilisation de Microsoft Azure PowerShell pour créer et préconfigurer les machines virtuelles Linux]: virtual-machines-ps-create-preconfigure-linux-vms.md

[Création d’une machine virtuelle personnalisée exécutant Linux dans Azure]: virtual-machines-linux-create-custom.md
[Capture d’une machine virtuelle Linux à utiliser comme modèle à l’aide de l’interface de ligne de commande]: virtual-machines-vm-capture-image-cli.md

[Création et téléchargement d’un disque dur virtuel contenant le système d’exploitation Linux]: virtual-machines-linux-create-upload-vhd.md

[Création d’une machine virtuelle pour un site Web avec Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Déployer les ressources Microsoft Azure à l’aide des bibliothèques de traitement, réseau et de stockage .NET]: virtual-machines-arm-deployment.md

[Connexion à la machine virtuelle]: virtual-machines-linux-how-to-log-on.md

[Association d’un disque de données]: virtual-machines-linux-how-to-attach-disk.md

[À propos des paramètres de configuration de machine virtuelle Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
[Environnement de test de la configuration de base]: virtual-machines-base-configuration-test-environment.md
[Environnements de test de cloud hybride Azure]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=58--> 