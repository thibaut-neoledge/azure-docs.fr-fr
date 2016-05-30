* Si vous n'avez pas déjà installé l'interface de ligne de commande Azure et si vous n'êtes pas encore connecté à votre abonnement, consultez [Installation de l'interface de ligne de commande Azure](../articles/xplat-cli-install.md) et [Connexion à un abonnement Azure à partir de l'interface de ligne de commande Azure](../articles/xplat-cli-connect.md). Lorsque vous souhaitez utiliser les commandes du mode Gestionnaire de ressources, veillez à vous connecter avec la commande `azure login`.

* Pour vous familiariser avec le mode Gestionnaire de ressources dans l’interface CLI Azure, vous devrez peut-être changer de mode de commande. Par défaut, l’interface de ligne de commande démarre en mode gestion des services Azure. Pour passer au mode Gestionnaire de ressources, exécutez `azure config mode arm`. Pour revenir au mode Gestion des services, exécutez `azure config mode asm`.

* Pour les options et l'aide en ligne de commande, tapez `azure <command> <subcommand> --help` ou `azure help <command> <subcommand>`.

## Tâches de machine virtuelle
Le tableau suivant compare les tâches courantes de machine virtuelle que vous pouvez effectuer avec les commandes CLI Azure dans le mode Gestion des services et le mode Gestionnaire de ressources. Avec de nombreuses commandes du Gestionnaire de ressources, vous devez passer le nom d'un groupe de ressources existant.

> [AZURE.NOTE] Ces exemples n’incluent pas les opérations basées sur des modèles qui sont recommandées pour les déploiements de machines virtuelles dans le Gestionnaire de ressources. Pour plus d’informations, consultez la rubrique [Utilisation de l’interface CLI Azure avec Azure Resource Manager](../articles/xplat-cli-azure-resource-manager.md) et [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface CLI Azure](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md).

Task | Mode Gestion des services | Mode Resource Manager
-------------- | ----------- | -------------------------
Créer la machine virtuelle de base | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Obtenir le `image-urn` pour les images Marketplace prisées en exécutant `azure help vm quick-create`. Pour des exemples d’URN au format *publisherName:offer:skus:version*, consultez [cet article](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md).)
Créer une machine virtuelle Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Créer une machine virtuelle Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Énumérer les machines virtuelles | `azure  vm list [options]` | `azure  vm list [options]`
Obtenir des informations sur une machine virtuelle | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Démarrer une machine virtuelle | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Arrêter une machine virtuelle | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Désallouer une machine virtuelle | Non disponible | `azure vm deallocate [options] <resource-group> <name>`
Redémarrer une machine virtuelle | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Supprimer une machine virtuelle | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Capturer une machine virtuelle | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Créer une machine virtuelle à partir d'une image utilisateur | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Créer une machine virtuelle à partir d'un disque spécialisé | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Ajouter un disque de données à une machine virtuelle | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -OU- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Supprimer un disque de données à partir d'une machine virtuelle | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Ajouter une extension générique à une machine virtuelle | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Ajouter l'extension d'accès aux machines virtuelles à une machine virtuelle | Non disponible | `azure vm reset-access [options] <resource-group> <name>`
Ajouter l'extension Docker à une machine virtuelle | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Ajouter l'extension Chef à une machine virtuelle | `azure  vm extension get-chef [options] <vm-name>` | Non disponible
Désactiver une extension de machine virtuelle | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | Non disponible
Supprimer une extension de machine virtuelle | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Énumérer des extensions de machine virtuelle | `azure vm extension list [options]` | Non disponible
Afficher une image de machine virtuelle | `azure vm image show [options]` | Non disponible
Obtenir l'utilisation des ressources de la machine virtuelle | Non disponible | `azure vm list-usage [options] <location>`
Obtenir toutes les tailles de machines virtuelles disponibles | Non disponible | `azure vm sizes [options]`


## Étapes suivantes

* Pour des exemples supplémentaires de commandes de l’interface CLI, consultez la rubrique [Commandes de l’interface de ligne de commande Azure en mode Azure Service Management (ASM)](../articles/virtual-machines-command-line-tools.md) et [Commandes de l’interface de ligne de commande Azure en mode Azure Resource Manager (ARM)](../articles/virtual-machines/azure-cli-arm-commands.md).

