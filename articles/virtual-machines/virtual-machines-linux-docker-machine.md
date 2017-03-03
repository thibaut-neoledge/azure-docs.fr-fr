---
title: "Utiliser Docker Machine pour créer des hôtes Linux dans Azure | Microsoft Docs"
description: "Décrit l&quot;utilisation de Docker Machine pour créer des hôtes Docker dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: b64bafe1a8572369aa7a7e6f0d8b924fc182cd53
ms.openlocfilehash: 9440474db7eb7e3ae082505f8c95cbcf8c285cdf
ms.lasthandoff: 02/15/2017


---
# <a name="use-docker-machine-with-the-azure-driver"></a>Utiliser Docker Machine avec le pilote Azure
[Docker](https://www.docker.com/) propose une virtualisation basée sur les conteneurs Linux plutôt que sur les machines virtuelles afin d’isoler les données d’application et les calculs sur une ressource partagée. Cette rubrique décrit comment et dans quels cas utiliser [Docker Machine](https://docs.docker.com/machine/). La commande `docker-machine` crée une nouvelle machine virtuelle Linux dans Azure activée en tant qu’hôte Docker pour les conteneurs Linux.

## <a name="create-vms-with-docker-machine"></a>Créer des machines virtuelles avec Docker Machine
Créez des machines virtuelles hôtes Docker dans Azure avec la commande `docker-machine create` en utilisant l’argument de pilote `azure` pour l'option de pilote (`-d`) et tous les autres arguments. 

L'exemple suivant repose sur les valeurs par défaut, mais il ouvre le port 80 sur la machine virtuelle pour accéder à Internet et effectuer un test avec un conteneur nginx, utilise `ops` comme utilisateur d'ouverture de session SSH, puis appelle la nouvelle machine virtuelle `machine`. 

Tapez `docker-machine create --driver azure` pour voir les options et leurs valeurs par défaut ; vous pouvez également lire la [documentation Docker Azure Driver](https://docs.docker.com/machine/drivers/azure/). (Notez que si l'authentification à deux facteurs est activée, vous devrez vous identifier à l’aide du second facteur).

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

La sortie doit ressembler à ceci, si l'authentification à deux facteurs a été configurée dans votre compte.

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>Configurer votre interpréteur de commandes Docker
À présent, tapez `docker-machine env <VM name>` pour voir ce que vous devez faire pour configurer l'interpréteur de commandes. 

```bash
docker-machine env machine
```

Imprime les informations d'environnement, qui ressemblent à ceci. Notez l'adresse IP qui a été attribuée et dont vous aurez besoin pour tester la machine virtuelle.

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

Vous pouvez exécuter la commande de configuration suggérée, ou définir les variables d'environnement vous-même. 

## <a name="run-a-container"></a>Exécuter un conteneur
Vous pouvez maintenant exécuter un serveur web simple pour vérifier si tout fonctionne correctement. Comme nous utilisons ici une image nginx standard, spécifiez que le serveur doit écouter sur le port 80, et que si la machine virtuelle redémarre, le conteneur doit également redémarrer (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

La sortie doit ressembler à ceci :

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Tester le conteneur
Examinez les conteneurs en cours d'exécution en utilisant `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Pour vérifier le conteneur en cours d'exécution, tapez `docker-machine ip <VM name>` pour rechercher l'adresse IP (si vous avez oublié la commande `env`) :

![Exécution d’un conteneur ngnix](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>Étapes suivantes
Si vous le souhaitez, vous pouvez utiliser Azure [Docker VM Extension](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour effectuer la même opération à l'aide de l'interface de ligne de commande (CLI) Azure ou des modèles Azure Resource Manager. 

Pour plus d’exemples sur l’utilisation de Docker, consultez [Utilisation de Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) à partir de la [démonstration](https://github.com/Microsoft/HealthClinic.biz) de 2015 Connect pour [HealthClinic.biz](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Pour d’autres démarrages rapides à partir de la démonstration pour HealthClinic.biz, consultez [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)(Démarrages rapides avec les outils de développement Azure).


