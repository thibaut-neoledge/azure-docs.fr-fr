<properties
   pageTitle="Création de modèles avec des extensions de machine virtuelle Linux | Microsoft Azure"
   description="En savoir plus sur la création de modèles Azure Resource Manager avec des extensions de machine virtuelle Linux"
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
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Création de modèles Azure Resource Manager avec des extensions de machine virtuelle Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Dans l’interface de ligne de commande Azure, exécutez l’applet de commande suivante :

      Azure VM extension list

Cette commande renvoie le nom de l’éditeur, le nom de l’extension et la version, comme suit :

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Ces trois propriétés sont mappées respectivement à « publisher », « type » et « typeHandlerVersion » dans l’extrait du modèle ci-dessus.

>[AZURE.NOTE]Il est toujours recommandé d’utiliser la dernière version de l’extension pour obtenir les fonctionnalités les plus à jour.

## Identification du schéma pour les paramètres de configuration de l’extension

L’étape suivante de la création du modèle d’extension consiste à identifier le format pour fournir les paramètres de configuration. Chaque extension prend en charge son propre ensemble de paramètres.

Pour voir un exemple de configuration pour les extensions Linux, cliquez sur la documentation [Exemples d’extensions Linux](virtual-machines-linux-extensions-configuration-samples.md).

Reportez-vous à ce qui suit pour obtenir un modèle totalement terminé avec des extensions de machine virtuelle.

[Extension de script personnalisé sur une machine virtuelle Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Une fois le modèle créé, vous pouvez le déployer en utilisant l’interface de ligne de commande Azure.

<!---HONumber=AcomDC_0323_2016-->