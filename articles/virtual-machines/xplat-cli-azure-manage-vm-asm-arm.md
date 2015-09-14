<properties
	pageTitle="Commandes Azure CLI équivalentes pour les tâches de machine virtuelle | Microsoft Azure"
	description="Commandes équivalentes de l'interface de ligne de commande Azure pour créer et gérer des machines virtuelles Azure en modes Gestionnaire de ressources Azure et Gestion des services Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="08/28/2015"
	ms.author="danlep"/>


# Commandes équivalentes du Gestionnaire de ressources et de la gestion des services pour les tâches de machine virtuelle avec l'interface de ligne de commande Azure pour Mac, Linux et Windows
Cet article illustre les commandes équivalentes de l’interface de ligne de commande Azure (Azure CLI) pour créer et gérer des machines virtuelles Azure dans la gestion des services Azure et le Gestionnaire de ressources Azure. Ces informations vous serviront de guide pratique pour migrer des scripts d'un mode de commande à l'autre.

* Si vous n'avez pas déjà installé l'interface de ligne de commande Azure et si vous n'êtes pas encore connecté à votre abonnement, consultez [Installation de l'interface de ligne de commande Azure](../xplat-cli-install.md) et [Connexion à un abonnement Azure à partir de l'interface de ligne de commande Azure](../xplat-cli-connect.md). Lorsque vous souhaitez utiliser les commandes du mode Gestionnaire de ressources, veillez à vous connecter avec la méthode par connexion.

* Pour prendre en main le mode Gestionnaire de ressources dans les modes Azure CLI et de basculement de commandes, consultez la rubrique [Utilisation de l'interface de ligne de commande Azure avec le Gestionnaire de ressources](xplat-cli-azure-resource-manager.md). Par défaut, l’interface de ligne de commande démarre en mode gestion des services Azure. Pour passer au mode Gestionnaire de ressources, exécutez `azure config mode arm`. Pour revenir au mode gestion des services, exécutez `azure config mode asm`.

* Pour les options et l'aide en ligne de commande, tapez `azure <command> <subcommand> --help` ou `azure help <command> <subcommand>`.

## Tâches de machine virtuelle
Le tableau suivant compare les tâches courantes de machine virtuelle que vous pouvez effectuer avec les commandes CLI Azure dans la gestion des services et le Gestionnaire de ressources. Avec de nombreuses commandes du Gestionnaire de ressources, vous devez passer le nom d'un groupe de ressources existant.

> [AZURE.NOTE]Ces exemples n'incluent pas d'opération basée sur un modèle dans le Gestionnaire de ressources. Pour plus d’informations, consultez la rubrique [Utilisation de l’interface de ligne de commande Azure avec le Gestionnaire de ressources](xplat-cli-azure-resource-manager.md).

Task | Gestion des services | Gestionnaire de ressources
-------------- | ----------- | -------------------------
Créer la machine virtuelle de base | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Obtenir le `image-urn` à partir de la commande `azure vm image list`.)
Créer une machine virtuelle Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Créer une machine virtuelle Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Énumérer les machines virtuelles | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
Obtenir des informations sur une machine virtuelle | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Démarrer une machine virtuelle | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Arrêter une machine virtuelle | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Désallouer une machine virtuelle | Non disponible | `azure vm deallocate [options] <resource-group> <name>`
Redémarrer une machine virtuelle | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Supprimer une machine virtuelle | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Capturer une machine virtuelle | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Créer une machine virtuelle à partir d'une image utilisateur | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Créer une machine virtuelle à partir d'un disque spécialisé | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Ajouter un disque de données à une machine virtuelle | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -OU- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Supprimer un disque de données à partir d'une machine virtuelle | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Ajouter une extension générique à une machine virtuelle | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Ajouter l'extension d'accès aux machines virtuelles à une machine virtuelle | Non disponible | `azure vm reset-access [options] <resource-group> <name>`
Ajouter l'extension Docker à une machine virtuelle | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Ajouter l'extension Chef à une machine virtuelle | `azure  vm extension get-chef [options] <vm-name>` | Non disponible
Désactiver une extension de machine virtuelle | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | Non disponible
Supprimer une extension de machine virtuelle | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Énumérer des extensions de machine virtuelle | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
Énumérer des images de machine virtuelle | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` -OU- <br/> `azure vm image list-publishers [options] <location>` -OU- <br/> `azure vm image list-offers [options] <location>` -OU- <br/> `azure vm image list-skus [options] <location>`
Afficher une image de machine virtuelle | `azure vm image show [options]` | Non disponible
Obtenir l'utilisation des ressources de la machine virtuelle | Non disponible | `azure vm list-usage [options] <location>`
Obtenir toutes les tailles de machines virtuelles disponibles | Non disponible | `azure vm sizes [options]`


## Étapes suivantes

* Pour plus d'informations sur l'utilisation de l'interface de ligne de commande Azure avec des ressources du Gestionnaire de ressources, consultez les rubriques [Utilisation de l'interface de ligne de commande Azure avec le Gestionnaire de ressources](xplat-cli-azure-resource-manager.md) et [Gestion du contrôle d'accès en fonction du rôle à l'aide de l'interface de ligne de commande Azure](../role-based-access-control-xplat-cli.md).
* Pour d'autres exemples de commandes de l'interface de ligne de commande, consultez [Utilisation de l'interface de ligne de commande Azure avec Azure Service Management](../virtual-machines-command-line-tools.md) et [Utilisation de l'interface de ligne de commande Azure avec Azure Resource Manager](azure-cli-arm-commands.md).

<!---HONumber=September15_HO1-->