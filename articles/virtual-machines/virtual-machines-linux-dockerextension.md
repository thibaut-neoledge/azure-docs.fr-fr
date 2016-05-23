<properties
   pageTitle="En savoir plus sur l’extension de machine virtuelle Docker sur Azure | Microsoft Azure"
   description="Découvrez comment utiliser l’extension de machine virtuelle Docker sur Azure pour déployer un environnement Docker rapidement et en toute sécurité dans Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/04/2016"
   ms.author="iainfou"/>

# Utilisation de l’extension de machine virtuelle Docker pour déployer votre environnement

Docker est une solution courante de gestion de conteneurs et une plateforme de création d’images qui permet de travailler rapidement avec des conteneurs sous Linux (et Windows). Avec Azure, vous avez la possibilité de déployer Docker de différentes manières selon vos besoins :

- Si vous souhaitez développer rapidement le prototype d’une application, ou si vous êtes déjà familiarisé avec Docker Machine et que vous l’utilisez, vous pouvez [utiliser le pilote Docker Machine Azure](./virtual-machines-linux-docker-machine.md) pour déployer des hôtes Docker dans Azure.
- Dans le cas d’un déploiement basé sur un modèle, l’extension de machine virtuelle Docker pour les machines virtuelles Azure peut être utilisée. Cette approche peut s’intégrer à des déploiements de modèles Azure Resource Manager et inclut tous les avantages associés tels que l’accès en fonction du rôle, les diagnostics et la configuration post-déploiement.
- Vous pouvez également [déployer un cluster Docker Swarm complet sur les services de conteneur Azure](../container-service/container-service-deployment.md) pour des déploiements prêts pour la production et évolutifs qui exploitent les outils de planification et de gestion supplémentaires fournis par Swarm.

Cet article se concentre sur l’utilisation de modèles Ressource Manager pour déployer l’extension de machine virtuelle Docker dans un environnement personnalisé et prêt pour la production que vous définissez.

## Extension de machine virtuelle Docker sur Azure pour les déploiements de modèle

L’extension de machine virtuelle Docker Azure installe et configure le démon Docker, le client Docker et Docker Compose dans votre machine virtuelle Linux. Cette extension permet également de définir et déployer des applications de conteneur à l’aide de Docker Compose. L’environnement peut ensuite être redéployé de manière cohérente à l’aide de modèles Resource Manager. L’utilisation de l’extension de machine virtuelle Docker Azure est parfaitement adaptée aux environnements de développement ou de production plus robustes, car vous disposez de contrôles supplémentaires en utilisant simplement Docker Machine ou en créant l’hôte Docker vous-même.

À l’aide de l’Azure Resource Manager, vous pouvez créer et déployer des modèles qui définissent la structure complète de votre environnement, notamment les hôtes Docker, le stockage, diagnostics, des contrôles d’accès en fonction du rôle (RBAC), etc. Vous pouvez [lire des documentations supplémentaires sur Ressource Manager](../resource-group-overview.md) et les modèles afin de mieux en comprendre certains avantages. L’avantage de l’utilisation de modèles Resource Manager par rapport à la simple utilisation de Docker Machine est que vous pouvez définir entre autres des hôtes Docker, du stockage, des contrôles d’accès supplémentaires et être en mesure de reproduire les déploiements en fonction des besoins à l’avenir.

## Déployer un modèle avec l’extension de machine virtuelle Docker :

Nous allons utiliser un modèle existant de démarrage rapide pour montrer comment déployer une machine virtuelle Ubuntu comportant une installation de l’extension de machine virtuelle Docker. Vous pouvez voir le modèle ici : [déploiement Simple d’une machine virtuelle Ubuntu avec Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)

Déployez le modèle à l’aide de l’interface de ligne de commande Azure en spécifiant un nom pour le nouveau groupe de ressources (ici `myDockerResourceGroup`) ainsi que l’URI du modèle :

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Répondez aux invites vous demandant de donner un nom à votre compte de stockage, d’indiquer le nom DNS, le nom d’utilisateur, etc. et attendez quelques minutes pour que le déploiement se termine. Le résultat ressemble à ce qui suit :

```
info:    Executing command group create
+ Getting resource group myDockerResourceGroup
+ Updating resource group myDockerResourceGroup
info:    Updated resource group myDockerResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mydockerstorage
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mydockergroup
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myDockerResourceGroup
data:    Name:                myDockerResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

## Déployer votre premier conteneur nginx

Une fois le déploiement terminé, utilisez SSH pour votre nouvel hôte Docker à l’aide du nome DNS fourni pendant le déploiement. Les outils Docker sont déjà installés ; nous allons donc essayer d’exécuter un conteneur nginx :

```
docker run -d -p 80:80 nginx
```

Le résultat ressemble à ce qui suit :

```
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

Examinez le conteneur en cours d’exécution sur votre ordinateur hôte à l’aide de `docker ps` :

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   nostalgic_murdock
```

Ouvrez un navigateur Web et entrez le nom DNS spécifié lors du déploiement pour voir votre conteneur en action :

![Exécution d’un conteneur ngnix](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Pour plus d’informations sur l’extension de machine virtuelle Docker, notamment sur la configuration du port TCP du démon Docker, la configuration de la sécurité et le déploiement de conteneurs à l’aide de Docker Compose, voir le [projet GitHub Azure Virtual Machine Extension for Docker](https://github.com/Azure/azure-docker-extension/) (Extension de machine virtuelle Azure pour Docker).

## Référence de modèle JSON pour l’extension de machine virtuelle Docker

Cet exemple utilise un modèle de démarrage rapide. Vous pouvez utiliser vos propres modèles Resource Manager existants pour installer l’extension de machine virtuelle Docker pour les machines virtuelles définies dans votre modèle en ajoutant le code suivant à votre fichier de définition JSON :

```
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

Pour découvrir la procédure détaillée d’utilisation de modèles Resource Manager, voir [Présentation d’Azure Resource Manager](../resource-group-overview.md).

## Étapes suivantes

Lire des étapes plus détaillées pour les différentes options de déploiement :

1. [Utiliser Docker Machine avec le pilote Azure](./virtual-machines-linux-docker-machine.md)  
2. [Utilisation de l’extension Docker VM à partir de l’interface de ligne de commande Microsoft Azure](./virtual-machines-linux-classic-cli-use-docker.md)  
3. [Déploiement d’un cluster Azure Container Service](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0511_2016-->