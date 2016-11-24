

Depuis son lancement, l’extension de script personnalisé a été largement utilisée pour configurer des charges travail aussi bien sur des machines virtuelles Windows que Linux. Avec la présentation des modèles Azure Resource manager, les utilisateurs peuvent maintenant créer un modèle unique qui non seulement met en service la machine virtuelle, mais configure les charges de travail.

## <a name="about-azure-resource-manager-templates"></a>À propos des modèles Azure Resource Manager
Le modèle Azure Resource Manager vous permet de spécifier de manière déclarative l’infrastructure IaaS Azure dans le langage JSON en définissant les dépendances entre ressources. Pour obtenir une présentation détaillée des modèles Azure Resource Manager, voir les articles suivants :

* [Présentation du groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md)
* [Déploiement de modèles avec Azure Powershell](../articles/virtual-machines/virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="prerequisites"></a>Conditions préalables
1. Téléchargez les outils en ligne de commande Azure pour votre système d’exploitation [ici](https://azure.microsoft.com/downloads/).
2. Si les scripts sont exécutés sur une machine virtuelle existante, assurez-vous que l’agent de machine virtuelle est activé sur la machine virtuelle, sinon suivez les instructions pour [Linux](../articles/virtual-machines/virtual-machines-linux-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) ou [Windows](../articles/virtual-machines/virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) pour en installer un.
3. Téléchargez les scripts que vous souhaitez exécuter sur la machine virtuelle vers Azure Storage. Les scripts peuvent provenir d'un seul ou de plusieurs conteneurs de stockage.
4. Les scripts peuvent également être téléchargés vers un compte Github.
5. Le script doit être conçu de manière à ce que le script d'entrée lancé par l'extension lance à son tour les autres scripts.

## <a name="using-the-custom-script-extension"></a>Utilisation de l’extension de script personnalisé
Pour le déploiement de modèles, nous utilisons la même version d’extension de script personnalisé que celle de l’API de gestion de Service Azure. L’extension prend en charge les mêmes paramètres et scénarios, par exemple, le téléchargement de fichiers vers le compte de stockage Azure ou un emplacement GitHub. La différence clé qui intervient pendant l’utilisation des modèles est que la version exacte de l’extension doit être spécifiée, contrairement à la spécification de la version au format majorversion.*.



<!--HONumber=Nov16_HO3-->


