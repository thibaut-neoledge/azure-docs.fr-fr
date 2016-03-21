<properties
   pageTitle="Scripts personnalisés sur des machines virtuelles utilisant des modèles | Microsoft Azure"
   description="Automatisation des tâches de configuration de machine virtuelle Windows et Linux Azure à l’aide de l’extension de script personnalisé avec des modèles du Gestionnaire des ressources"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="11/01/2015"
   ms.author="kundanap"/>

# Utilisation de l’extension de script personnalisé avec les modèles Azure Resource Manager

Cet article offre une vue d’ensemble de l’écriture de modèles Azure Resource Manager avec l’extension de script personnalisé pour l’amorçage de charges de travail sur une machine virtuelle Linux ou Windows.

Pour une vue d’ensemble de l’extension de script personnalisé, voir l’article [ici](virtual-machines-extensions-customscript.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-extensions-customscript.md).

Depuis son lancement, l’extension de Script personnalisé a été largement utilisée pour configurer des charges travail aussi bien sur des machines virtuelles Windows et que Linux. Avec la présentation des modèles Azure Resource manager, les utilisateurs peuvent maintenant créer un modèle unique qui non seulement met en service la machine virtuelle, mais configure les charges de travail.

## À propos des modèles Azure Resource Manager

Le modèle Azure Resource Manager vous permet de spécifier de manière déclarative l’infrastructure IaaS Azure dans le langage Json en définissant les dépendances entre ressources. Pour obtenir une présentation détaillée des modèles Azure Resource Manager, voir les articles suivants :

- [Présentation du groupe de ressources](../resource-group-overview)
- [Déploiement de modèles avec l'interface de ligne de commande Azure](virtual-machines-deploy-rmtemplates-azure-cli)
- [Déploiement de modèles avec Azure Powershell](virtual-machines-deploy-rmtemplates-powershell)

### Conditions préalables

1. Installez les dernières applets de commande d'Azure PowerShell ou de l'interface de ligne de commande Azure à partir d'[ici](https://azure.microsoft.com/downloads/).
2. Si les scripts sont exécutés sur une machine virtuelle existante, assurez-vous que l'agent de machine virtuelle est activé sur la machine virtuelle, sinon suivez [ces](virtual-machines-extensions-install) instructions pour en installer un.
3. Téléchargez les scripts que vous souhaitez exécuter sur la machine virtuelle vers Azure Storage. Les scripts peuvent provenir d'un seul ou de plusieurs conteneurs de stockage.
4. Les scripts peuvent également être téléchargés vers un compte Github.
5. Le script doit être conçu de manière à ce que le script d'entrée lancé par l'extension lance à son tour les autres scripts.

## Utilisation de l’extension de script personnalisé

Pour le déploiement de modèles, utilisez la même version d’extension de script personnalisé que celle de l’API de gestion de Service Azure. L’extension prend en charge les mêmes paramètres et scénarios, par exemple, le téléchargement de fichiers vers le compte de stockage Azure ou un emplacement Github. La différence clé qui intervient pendant l’utilisation des modèles est que la version exacte de l’extension doit être spécifiée, contrairement à la spécification de la version au format majorversion.*.

 ## Exemple de modèle pour une machine virtuelle sous Linux

Définir la ressource d’extension suivante dans la section Ressource du modèle

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

## Exemple de modèle pour une machine virtuelle sous Windows

Définir la ressource suivante dans la section Ressource du modèle

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

Dans les exemples ci-dessus, remplacez l’URL du fichier et le nom de fichier par vos propres paramètres.

Une fois le modèle créé, vous pouvez le déployer en utilisant l’interface de ligne de commande Azure ou Azure PowerShell.

Reportez-vous aux exemples ci-dessous pour obtenir des exemples complets de configuration d’applications sur une machine virtuelle avec des extensions de script personnalisé.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Extension de script personnalisé sur une machine virtuelle Linux</a>. </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Extension de script personnalisé sur une machine virtuelle Windows</a>.

<!---HONumber=AcomDC_0309_2016-->