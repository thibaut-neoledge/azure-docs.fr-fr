<properties
	pageTitle="Créer une machine virtuelle Linux à l’aide d’un modèle Azure | Microsoft Azure"
	description="Créez une machine virtuelle Linux sur Azure à l’aide d’un modèle Azure Resource Manager."
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
	ms.topic="hero-article"
	ms.date="04/29/2016"
	ms.author="v-livech"/>

# Créer une machine virtuelle Linux à l’aide d’un modèle Azure

Cet article explique comment déployer rapidement une machine virtuelle Linux sur Azure à l’aide d’un modèle Azure. L’article nécessite un compte Azure ([obtenir un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)) et [l’interface de ligne de commande Azure](../xplat-cli-install.md) connectée (`azure login`) en mode Resource Manager (`azure config mode arm`). Vous pouvez également déployer rapidement une machine virtuelle Linux à l’aide du [Portail Azure](virtual-machines-linux-quick-create-portal.md) ou de l’[interface de ligne de commande Azure](virtual-machines-linux-quick-create-cli.md).


## Commande rapide

Dans les exemples de commandes suivants, remplacez les valeurs entre &lt; et &gt; par les valeurs de votre propre environnement.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## Procédure pas à pas

Les modèles vous permettent de créer des machines virtuelles dans Azure avec des paramètres que vous pouvez personnaliser lors du lancement, tels que les noms d’utilisateur et les noms d’hôte. Cet article présente en détail le lancement d’une machine virtuelle Ubuntu à l’aide d’un modèle Azure, qui crée un groupe de sécurité réseau (NSG) avec un seul port ouvert (le port 22 pour SSH) et qui requiert des clés SSH pour la connexion.

Les modèles Azure Resource Manager sont des fichiers JSON qui peuvent être utilisés pour des tâches simples et uniques, comme le lancement d’une machine virtuelle Ubuntu tel qu’indiqué dans cet article, ou pour construire des configurations Azure complexes d’environnements complets. Ceux-ci incluent les tests, le développement ou le déploiement de production, depuis la mise en réseau à la pile de l’application, en passant par le système d’exploitation.

## Créer la machine virtuelle Linux

L’exemple de code suivant montre comment appeler `azure group create` pour créer un groupe de ressources et déployer une machine virtuelle Linux sécurisée SSH simultanément en utilisant [ce modèle Azure Resource Manager](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). N’oubliez pas que dans votre exemple, vous devez utiliser des noms propres à votre environnement. Cet exemple utilise `quicksecuretemplate` comme nom du groupe de ressources, `securelinux` comme nom de machine virtuelle et `quicksecurelinux` comme nom de sous-domaine.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Sortie

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Vous pouvez créer un groupe de ressources et déployer une machine virtuelle à l’aide du paramètre `--template-uri`. Vous pouvez également télécharger ou créer un modèle localement et le transmettre à l’aide du paramètre `--template-file` en indiquant le chemin d’accès au fichier de modèle en tant qu’argument. L’interface de ligne de commande Azure vous invite à entrer les paramètres requis par le modèle.

## Étapes suivantes

Lorsque vous créerez des machines virtuelles Linux avec des modèles, vous souhaiterez probablement savoir quelles autres infrastructures d’application peuvent être déployées avec des modèles. Consultez la [galerie de modèles](https://azure.microsoft.com/documentation/templates/) pour découvrir les infrastructures d’application à déployer ensuite.

<!---HONumber=AcomDC_0504_2016-->