Pour utiliser l’interface de ligne de commande Azure avec les commandes et modèles de Resource Manager afin de déployer des ressources et charges de travail Azure à l’aide de groupes de ressources, vous devez disposer d’un compte Azure. Si vous ne disposez pas d’un compte, vous pouvez obtenir une [version d’essai gratuite d’Azure ici](https://azure.microsoft.com/pricing/free-trial/).

Si vous n’avez pas encore installé l’interface de ligne de commande Azure et connecté cette dernière à votre abonnement, consultez l’article [Installer l’interface de ligne de commande Microsoft Azure](../articles/cli-install-nodejs.md), définissez le mode sur `arm` avec `azure config mode arm`, puis connectez-vous à Azure avec la commande `azure login`.

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- Azure CLI 10 : notre interface de ligne de commande pour les modèles de déploiement Classic et Resource Manager (cet article)
- [Azure CLI 2.0](../articles/virtual-machines/linux/cli-manage.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Commandes de base Basic Azure Resource Manager de l’interface de ligne de commande Azure
Cet article traite des commandes de base que vous utiliserez avec Azure CLI pour gérer vos ressources (principalement des machines virtuelles) et interagir avec elles dans votre abonnement Azure.  Pour plus d’informations sur les commutateurs et options de ligne de commande spécifiques, vous pouvez utiliser les options et l’aide en ligne des commandes en tapant `azure <command> <subcommand> --help` ou `azure help <command> <subcommand>`.

> [!NOTE]
> Ces exemples n’incluent pas les opérations basées sur des modèles qui sont recommandées pour les déploiements de machines virtuelles dans le Gestionnaire de ressources. Pour plus d’informations, consultez les articles [Utilisation de l’interface de ligne de commande Azure (CLI) avec Azure Resource Manager](../articles/xplat-cli-azure-resource-manager.md) et [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

| Tâche | Gestionnaire de ressources |
| --- | --- | --- |
| Créer la machine virtuelle de base |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Obtenez l’élément `image-urn` à partir de la commande `azure vm image list`. Consultez [cet article](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour découvrir des exemples.) |
| Créer une machine virtuelle Linux |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Créer une machine virtuelle Windows |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| Énumérer les machines virtuelles |`azure  vm list [options]` |
| Obtenir des informations sur une machine virtuelle |`azure  vm show [options] <resource_group> <name>` |
| Démarrer une machine virtuelle |`azure vm start [options] <resource_group> <name>` |
| Arrêter une machine virtuelle |`azure vm stop [options] <resource_group> <name>` |
| Désallouer une machine virtuelle |`azure vm deallocate [options] <resource-group> <name>` |
| Redémarrer une machine virtuelle |`azure vm restart [options] <resource_group> <name>` |
| Supprimer une machine virtuelle |`azure vm delete [options] <resource_group> <name>` |
| Capturer une machine virtuelle |`azure vm capture [options] <resource_group> <name>` |
| Créer une machine virtuelle à partir d'une image utilisateur |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| Créer une machine virtuelle à partir d'un disque spécialisé |`azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| Ajouter un disque de données à une machine virtuelle |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| Supprimer un disque de données à partir d'une machine virtuelle |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| Ajouter une extension générique à une machine virtuelle |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Ajouter l'extension d'accès aux machines virtuelles à une machine virtuelle |`azure vm reset-access [options] <resource-group> <name>` |
| Ajouter l'extension Docker à une machine virtuelle |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| Supprimer une extension de machine virtuelle |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Obtenir l'utilisation des ressources de la machine virtuelle |`azure vm list-usage [options] <location>` |
| Obtenir toutes les tailles de machines virtuelles disponibles |`azure vm sizes [options]` |

## <a name="next-steps"></a>Étapes suivantes
* Pour d'autres exemples de commandes de l'interface CLI dépassant la gestion de base des machines virtuelles, consultez la page [Utilisation de l'interface CLI Azure avec Azure Resource Manager](../articles/virtual-machines/azure-cli-arm-commands.md).
