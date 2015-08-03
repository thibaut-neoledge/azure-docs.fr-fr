<properties
	pageTitle="Les différentes façons de créer une machine virtuelle Windows"
	description="Répertorie les différentes façons de créer une machine virtuelle Windows et fournit des liens vers des instructions."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="07/15/2015"
	ms.author="kathydav"/>

# Les différentes façons de créer une machine virtuelle Windows

Microsoft Azure propose différents moyens de créer des machines virtuelles, adaptées à tous les utilisateurs et toutes les applications. En conséquence, vous devez effectuer certains choix relatifs à la machine virtuelle et à sa création. Cet article vous propose un résumé de ces options et des liens pour obtenir des instructions.

Les modèles Microsoft Azure Resource Manager ont été récemment introduits comme un moyen de créer et de gérer une machine virtuelle et ses différentes ressources en tant qu’unité logique unique de déploiement. Des instructions relatives à cette approche sont fournies ci-dessous, le cas échéant. Pour plus d’informations sur Microsoft Azure Resource Manager et sur la gestion des ressources en tant qu’unité unique, consultez cette [vue d’ensemble][].

## Choix des outils

### Interface utilisateur graphique : le portail ou le portail en version préliminaire Microsoft Azure

L’interface utilisateur graphique du portail Microsoft Azure constitue un moyen simple de tester une machine virtuelle, en particulier si vous venez de démarrer avec Microsoft Azure. Pour créer la machine virtuelle, utilisez le portail Azure ou le portail Azure en version préliminaire :

[Créer une machine virtuelle exécutant Windows][]

### Interface de commande : interface de ligne de commande Microsoft Azure ou Microsoft Azure PowerShell

Si vous préférez travailler dans une interface de commande, choisissez l’interface de ligne de commande Microsoft Azure ou Microsoft Azure PowerShell, qui comporte des applets de commande Windows PowerShell pour Microsoft Azure et une console personnalisée.

Pour l’interface de ligne de commande Microsoft Azure, consultez [Commandes équivalentes du Gestionnaire de ressources et de la gestion des services pour les opérations de machine virtuelle avec l’interface de ligne de commande Azure pour Mac, Linux et Windows][]. Pour utiliser un modèle de gestionnaire des ressources, consultez [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure][].

Pour Azure PowerShell, consultez [Créer et préconfigurer une machine virtuelle Windows avec Resource Manager et Azure PowerShell][]. Pour utiliser un modèle, consultez [Déployer et gérer des machines virtuelles à l’aide des modèles Azure Resource Manager et de PowerShell][]. Pour créer des machines virtuelles dans la pile de gestion des services, consultez [Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows][].

### Environnement de développement : Visual Studio

[Création d’une machine virtuelle pour un site Web avec Visual Studio][]

[Déployer les ressources Microsoft Azure à l’aide des bibliothèques de traitement, réseau et de stockage .NET][]

## Système d’exploitation et choix d’images

Choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils. Sinon, utilisez l’une de vos propres images.

### Images Microsoft Azure

Ces instructions vous expliquent comment utiliser une image Microsoft Azure pour créer une machine virtuelle personnalisée avec des options pour la mise en réseau, l’équilibrage de charge, et bien davantage. Consultez [Comment créer une machine virtuelle personnalisée exécutant Windows][].

### Utiliser votre propre image

Utilisez une image basée sur une machine virtuelle Microsoft Azure existante, en *capturant* cette machine virtuelle ou en téléchargeant l’une de vos propres images stockée sur un disque dur virtuel :

- [Capture d’une machine virtuelle Windows][]
- [Créer et charger un disque dur virtuel Windows Server dans Azure][]

## Étapes suivantes

[Connexion à la machine virtuelle][]

[Association d’un disque de données][]

## Ressources supplémentaires
[À propos des paramètres de configuration de machine virtuelle Azure][]

[Environnement de test de la configuration de base][]

[Environnements de test de cloud hybride Azure][]

<!-- LINKS -->
[vue d’ensemble]: ../resource-group-overview.md

[Créer une machine virtuelle exécutant Windows]: virtual-machines-windows-tutorial.md

[Commandes équivalentes du Gestionnaire de ressources et de la gestion des services pour les opérations de machine virtuelle avec l’interface de ligne de commande Azure pour Mac, Linux et Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Créer et préconfigurer une machine virtuelle Windows avec Resource Manager et Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Déployer et gérer des machines virtuelles à l’aide des modèles Azure Resource Manager et de PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows]: virtual-machines-ps-create-preconfigure-windows-vms.md
[Comment créer une machine virtuelle personnalisée exécutant Windows]: virtual-machines-windows-create-custom.md

[Capture d’une machine virtuelle Windows]: virtual-machines-capture-image-windows-server.md

[Créer et charger un disque dur virtuel Windows Server dans Azure]: virtual-machines-create-upload-vhd-windows-server.md


[Création d’une machine virtuelle pour un site Web avec Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Déployer les ressources Microsoft Azure à l’aide des bibliothèques de traitement, réseau et de stockage .NET]: virtual-machines-arm-deployment.md

[Connexion à la machine virtuelle]: virtual-machines-log-on-windows-server.md

[Association d’un disque de données]: storage-windows-attach-disk.md

[À propos des paramètres de configuration de machine virtuelle Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx

[Environnement de test de la configuration de base]: virtual-machines-base-configuration-test-environment.md

[Environnements de test de cloud hybride Azure]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=July15_HO4-->