<properties
   pageTitle="Création de modèles avec des extensions de machine virtuelle Azure | Microsoft Azure"
   description="En savoir plus sur la création de modèles avec des extensions"
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
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Création de modèles Azure Resource Manager avec des extensions de machine virtuelle

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de l’utilisation du modèle de déploiement de Resource Manager.

## Vue d’ensemble des modèles Azure Resource Manager

Le modèle Azure Resource Manager vous permet de spécifier de manière déclarative l’infrastructure IaaS Azure dans le langage JSON en définissant les dépendances entre ressources. Pour obtenir une présentation détaillée des modèles Azure Resource Manager, consultez les articles suivants :

[Présentation du groupe de ressources](../resource-group-overview.md)

## Extrait de l'exemple de modèle pour les extensions de machine virtuelle.
Pour déployer une extension de machine virtuelle dans le cadre du modèle Azure Resource Manager, vous devez spécifier la configuration de l’extension de façon déclarative dans le modèle. Voici le format qui permet de spécifier la configuration de l’extension.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Comme vous pouvez le voir ci-dessus, le modèle d’extension contient deux parties principales :

1. Nom de l’extension, éditeur et version.
2. Configuration de l’extension.

## Identification de l’éditeur, du type et de typeHandlerVersion pour les extensions.

Les extensions de machine virtuelle Azure sont publiées par Microsoft et approuvées par des éditeurs tiers, et chaque extension est identifiée de façon univoque par son éditeur, son type et la typeHandlerVersion. Ils peuvent être déterminés comme suit :

Dans Azure PowerShell, exécutez l’applet de commande Azure PowerShell suivante :

      Get-AzureVMAvailableExtension

Dans l’interface de ligne de commande Azure, exécutez l’applet de commande Azure PowerShell suivante :

      Azure VM Extension list

Cette applet de commande renvoie le nom de l’éditeur, le nom de l’extension et la version, comme suit :

       Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Ces trois propriétés sont mappées respectivement à « publisher », « type » et « typeHandlerVersion » dans l’extrait du modèle ci-dessus. Remarque : il est toujours recommandé d’utiliser la dernière version de l’extension pour obtenir les fonctionnalités les plus à jour.

## Identification du schéma pour les paramètres de configuration de l’extension

L’étape suivante de la création du modèle d’extension consiste à identifier le format pour fournir les paramètres de configuration. Chaque extension prend en charge son propre ensemble de paramètres.

Pour voir un exemple de configuration pour les extensions Windows, cliquez sur la documentation [Exemples d’extensions Windows](virtual-machines-extensions-configuration-samples-windows.md).

Pour voir un exemple de configuration pour les extensions Linux, cliquez sur la documentation [Exemples d’extensions Linux](virtual-machines-extensions-configuration-samples-linux.md).

Reportez-vous à ce qui suit pour les modèles de machine virtuelle pour obtenir un modèle totalement terminé avec des extensions de machine virtuelle.

[Extension de script personnalisé sur une machine virtuelle Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

[Extension de script personnalisé sur une machine virtuelle Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Une fois le modèle créé, vous pouvez le déployer en utilisant l’interface de ligne de commande Azure ou Azure Powershell.

<!---HONumber=Sept15_HO4-->