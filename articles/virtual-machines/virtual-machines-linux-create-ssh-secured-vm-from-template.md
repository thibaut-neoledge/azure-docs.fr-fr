<properties
	pageTitle="Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure | Microsoft Azure"
	description="Créez une machine virtuelle Linux sécurisée sur Azure à l’aide d’un modèle Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/08/2016"
	ms.author="vlivech"/>

# Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure

Les modèles vous permettent de créer des machines virtuelles dans Azure avec des paramètres que vous pouvez personnaliser lors du lancement, tels que les noms d’utilisateur et les noms d’hôte. Cet article présente en détail le lancement d’une machine virtuelle Ubuntu à l’aide d’un modèle Azure, qui crée un groupe de sécurité réseau (NSG) avec un seul port ouvert (le port 22 pour SSH) et qui requiert des clés SSH pour la connexion.

Les modèles Azure Resource Manager sont des fichiers JSON qui peuvent être utilisés pour des tâches simples et uniques, comme le lancement d’une machine virtuelle Ubuntu tel qu’indiqué dans cet article, ou pour construire des configurations Azure complexes d’environnements complets. Ceux-ci incluent les tests, le développement ou le déploiement de production, depuis la mise en réseau à la pile de l’application, en passant par le système d’exploitation.

## Objectif

- Créer une machine virtuelle Linux sécurisée SSH dans Azure à l’aide d’un modèle Azure

## Composants requis

- Un compte Azure ([obtenir une version d’évaluation gratuite !](https://azure.microsoft.com/pricing/free-trial/)), un [fichier de clé publique SSH](virtual-machines-linux-mac-create-ssh-keys.md), l’[interface de ligne de commande Azure](../xplat-cli-install.md), puis basculez cette dernière en mode de ressources en tapant `azure config mode arm`. Connectez-vous ensuite à Azure avec l’interface de ligne de commande en tapant `azure login`.

## Résumé des commandes rapides

Ce déploiement ne requiert qu’une seule commande, ainsi que la sélection d’une option qui indique où se trouve le modèle Azure Resource Manager. Cette rubrique utilise, à titre d’exemple, un modèle directement à partir du référentiel de modèles de démarrage rapide Azure de Github. Les différentes options ci-dessous indiquent simplement l’emplacement du modèle et des fichiers de paramètres, le cas échéant.

1. `azure group create -n <exampleRGname> -l <exampleAzureRegion> [--template-uri <URL> | --template-file <path> | <template-file> -e <parameters.json file>]`

## Créer la machine virtuelle Linux

L’exemple de code suivant montre comment appeler `azure group create` pour créer un groupe de ressources et déployer une machine virtuelle Linux sécurisée SSH simultanément en utilisant [ce modèle Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). N’oubliez pas que dans votre exemple, vous devez utiliser des noms propres à votre environnement. Cet exemple utilise `quicksecuretemplate` comme nom du groupe de ressources, `securelinux` comme nom de machine virtuelle et `quicksecurelinux` comme nom de sous-domaine.

```bash
azure group create -n quicksecuretemplate -l eastus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
adminUserName: ops
sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRZ/XB8p8uXMqgI8EoN3dWQw... user@contoso.com
dnsLabelPrefix: quicksecurelinux
vmName: securelinux
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<guid>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## Procédure pas à pas détaillée

Vous pouvez créer un groupe de ressources et déployer une machine virtuelle à l’aide du paramètre `--template-uri`. Vous pouvez également télécharger ou créer un modèle localement et le transmettre à l’aide du paramètre `--template-file` en indiquant le chemin d’accès au fichier de modèle en tant qu’argument. L’interface de ligne de commande Azure vous invite à entrer les paramètres requis par le modèle.

## Étapes suivantes

Lorsque vous créerez des machines virtuelles Linux avec des modèles, vous souhaiterez probablement savoir quelles autres infrastructures d’application peuvent être utilisées avec des modèles. Consultez la [galerie de modèles](https://azure.microsoft.com/documentation/templates/) pour découvrir les infrastructures d’application à déployer ensuite.

<!---HONumber=AcomDC_0323_2016-->