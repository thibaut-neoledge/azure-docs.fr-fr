---
title: "Utiliser l’extension de machine virtuelle Azure Docker avec Azure CLI 1.0 | Microsoft Docs"
description: "Découvrez comment utiliser l’extension Docker VM sur Azure pour déployer un environnement Docker rapidement et en toute sécurité dans Azure en utilisant des modèle Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a6230ae2e9b22655988cd25e5f3660bf1bc214d7
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Création d’un environnement Docker dans Azure à l’aide de l’extension de machine virtuelle Docker avec Azure CLI 1.0
Docker est une solution courante de gestion de conteneurs et une plateforme de création d’images qui permet de travailler rapidement avec des conteneurs sous Linux (et Windows). Dans Azure, il existe différentes méthodes pour déployer Docker selon vos besoins. Cet article se concentre sur l’utilisation de l’extension Docker VM et des modèles Azure Resource Manager. 

Pour plus d’informations sur les différentes méthodes de déploiement, y compris l’utilisation des services Docker Machine et Azure Container, consultez les articles suivants :

* Pour créer rapidement un prototype d’application, vous pouvez créer un hôte Docker unique en utilisant [Docker Machine](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pour les environnements plus vastes et plus stables, vous pouvez utiliser l’extension Azure Docker VM, qui prend également en charge [Docker Compose](https://docs.docker.com/compose/overview/) pour générer des déploiements de conteneur cohérents. Cet article détaille l’utilisation de l’extension Azure Docker VM.
* Pour créer des environnements prêts pour la production et évolutifs qui exploitent d’autres outils de planification et de gestion, vous pouvez déployer [un cluster Docker Swarm sur les services Azure Container](../container-service/container-service-deployment.md).

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#azure-docker-vm-extension-overview) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager 

## <a name="azure-docker-vm-extension-overview"></a>Présentation de l’extension Azure Docket VM
L’extension de machine virtuelle Docker Azure installe et configure le démon Docker, le client Docker et Docker Compose dans votre machine virtuelle (VM) Linux. L’extension Azure Docker VM vous offre plus de contrôle et de fonctionnalités que la simple utilisation de Docker Machine ou la création de votre propre hôte Docker. Ces fonctionnalités supplémentaires, telles que [Docker Compose](https://docs.docker.com/compose/overview/), permettent d’adapter l’extension Azure Docker VM à des environnements de développement ou de production plus robustes.

Les modèles Azure Resource Manager définissent la structure complète de votre environnement. Les modèles vous permettent de créer et de configurer des ressources comme les machines virtuelles hôtes Docker, le stockage, les contrôles d’accès en fonction du rôle (RBAC) et les diagnostics. Vous pouvez réutiliser ces modèles pour créer des déploiements supplémentaires de manière cohérente. Pour plus d’informations sur Azure Resource Manager et les modèles, consultez la page [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Déployer un modèle avec l’extension de machine virtuelle Azure Docker
Nous allons utiliser un modèle de démarrage rapide existant pour créer une machine virtuelle Ubuntu qui utilise l’extension Azure Docker VM pour installer et configurer l’hôte Docker. Vous pouvez voir le modèle ici : [Simple deployment of an Ubuntu VM with Docker (Déploiement simple d’une machine virtuelle Ubuntu avec Docker)](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

La [dernière version de l’interface de ligne de commande Azure (CLI)](../cli-install-nodejs.md) doit être installée et connectée à l’aide du mode Resource Manager comme suit :

```azurecli
azure config mode arm
```

Déployez le modèle à l’aide de l’interface de ligne de commande Azure, en spécifiant le modèle URI. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `WestUS`. Utilisez vos propres nom de groupe de ressources et emplacement comme suit :

```azurecli
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Répondez aux invites pour nommer votre compte de stockage, indiquez le nom d’utilisateur et le mot de passe, puis spécifiez un nom DNS. Le résultat ressemble à l’exemple suivant :

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

L’interface CLI Azure vous renvoie à l’invite après quelques secondes, mais l’hôte Docker est toujours en cours de création et de configuration par l’extension Azure Docker VM. Le déploiement prend quelques minutes. Vous pouvez afficher des détails sur l’état de l’hôte Docker à l’aide de la commande `azure vm show`.

L’exemple suivant vérifie l’état de la machine virtuelle nommée `myDockerVM` (nom par défaut extrait du modèle - ne pas modifier ce nom) dans le groupe de ressources nommé `myResourceGroup`. Entrez le nom du groupe de ressources que vous avez créé à l’étape précédente :

```azurecli
azure vm show -g myResourceGroup -n myDockerVM
```

Le résultat de la commande `azure vm show` ressemble à l’exemple suivant :

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Vers le haut de la sortie, vous voyez le `ProvisioningState` de la machine virtuelle. Lorsque `Succeeded`s’affiche, le déploiement est terminé et vous pouvez vous connecter par SSH à la machine virtuelle.

Vers la fin du résultat, `FQDN` affiche le nom de domaine complet de l’hôte Docker. Ce nom de domaine complet est ce que vous utilisez pour vous connecter par SSH à votre hôte Docker dans les étapes restantes.

## <a name="deploy-your-first-nginx-container"></a>Déployer votre premier conteneur nginx
Une fois le déploiement terminé, connectez-vous par SSH à votre nouvel hôte Docker à partir de votre ordinateur local. Entrez vos propre nom d’utilisateur et nom de domaine complet comme suit :

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Une fois connecté à l’hôte Docker, nous allons exécuter un conteneur nginx :

```bash
sudo docker run -d -p 80:80 nginx
```

La sortie est similaire à l’exemple suivant lorsque l’image nginx est téléchargée et qu’un conteneur est démarré :

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Vérifiez l’état des conteneurs s’exécutant sur l’hôte Docker comme suit :

```bash
sudo docker ps
```

Le résultat est similaire à l’exemple suivant, indiquant que le conteneur nginx est en cours d’exécution et que les ports TCP 80 et 443 sont transférés :

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Pour voir votre conteneur en action, ouvrez un navigateur Web et entrez le nom complet de votre hôte Docker :

![Exécution d’un conteneur ngnix](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Référence de modèle pour l’extension Azure Docker VM
L’exemple précédent utilise un modèle de démarrage rapide existant. Vous pouvez également déployer l’extension Azure Docker VM avec vos propres modèles Resource Manager. Pour cela, ajoutez ce qui suit à vos modèles Resource Manager, en définissant la valeur `vmName` de votre machine virtuelle en conséquence :

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Pour découvrir la procédure pas à pas d’utilisation de modèles Resource Manager, voir la [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez [configurer le port TCP du démon Docker](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), examiner la [sécurité Docker](https://docs.docker.com/engine/security/security/), ou déployer des conteneurs à l’aide de [Docker Compose](https://docs.docker.com/compose/overview/). Pour plus d’informations sur l’extension Azure Docker VM elle-même, consultez le [projet GitHub](https://github.com/Azure/azure-docker-extension/).

En savoir plus sur les options de déploiement supplémentaires Docker dans Azure :

* [Utiliser Docker Machine avec le pilote Azure](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  
* [Prise en main de Docker et Compose pour définir et exécuter une application à conteneurs multiples sur une machine virtuelle Azure](virtual-machines-linux-docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Déploiement d’un cluster Azure Container Service](../container-service/container-service-deployment.md)


