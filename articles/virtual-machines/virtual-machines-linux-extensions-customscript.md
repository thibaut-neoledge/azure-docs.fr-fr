<properties
   pageTitle="Scripts personnalisés sur des machines virtuelles Linux utilisant des modèles | Microsoft Azure"
   description="Automatisation des tâches de configuration de machine virtuelle Linux à l’aide de l’extension de script personnalisé avec des modèles Resource Manager"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Utilisation de l’extension de script personnalisé pour les machines virtuelles Linux avec les modèles Azure Resource Manager

Cet article offre une vue d’ensemble de l’écriture de modèles Azure Resource Manager avec l’extension de script personnalisé pour l’amorçage de charges de travail sur une machine virtuelle Linux.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-extensions-customscript.md).

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

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

Dans l’exemple ci-dessus, remplacez l’URL du fichier et le nom de fichier par vos propres paramètres.

Une fois le modèle créé, vous pouvez le déployer en utilisant l’interface de ligne de commande Azure.

Reportez-vous à l’exemple ci-dessous pour obtenir des exemples complets de configuration d’applications sur une machine virtuelle avec des extensions de script personnalisé.

* [Extension de script personnalisé sur une machine virtuelle Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!---HONumber=AcomDC_0420_2016-->