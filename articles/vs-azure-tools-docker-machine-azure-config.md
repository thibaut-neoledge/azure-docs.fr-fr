---
title: "Créer des hôtes Docker dans Azure avec Docker Machine | Microsoft Docs"
description: "Décrit l'utilisation de Docker Machine pour créer des hôtes Docker dans Azure."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 766d327a87ed13e04166d71c3d9ae0a1e7a66d19
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Créer des hôtes Docker dans Azure avec Docker Machine
L’exécution de conteneurs [Docker](https://www.docker.com/) nécessite une machine virtuelle hôte exécutant le démon Docker.
Cette rubrique décrit comment utiliser la commande [docker-machine](https://docs.docker.com/machine/) pour créer des machines virtuelles Linux, configurées avec le démon Docker et s’exécutant dans Azure. 

**Remarque :** 

* *Cet article suppose que la version de docker-machine est 0.9.0-rc2 ou une version supérieure*
* *Les conteneurs Windows seront prochainement pris en charge avec docker-machine*

## <a name="create-vms-with-docker-machine"></a>Créer des machines virtuelles avec Docker Machine
Créez des machines virtuelles d’hôte Docker sans Azure avec la commande `docker-machine create` en utilisant le pilote `azure`. 

Le pilote Azure nécessite votre ID d’abonnement. Vous pouvez utiliser l’[interface de ligne de commande Azure](cli-install-nodejs.md) ou le [Portail Azure](https://portal.azure.com) pour récupérer votre abonnement Azure. 

**Utilisation du portail Azure**

* Sélectionnez **Abonnements** dans la page de navigation de gauche, puis copiez l’ID d’abonnement.

**Utilisation de l’interface de ligne de commande Azure (CLI)**

* Saisissez ```azure account list``` et copiez l’ID d’abonnement.

Saisissez `docker-machine create --driver azure` pour afficher les options et leurs valeurs par défaut.
Vous pouvez également consulter la [Documentation Docker Azure pilote](https://docs.docker.com/machine/drivers/azure/) pour plus d’informations. 

L’exemple suivant repose sur les [valeurs par défaut](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), mais il définit de façon facultative les valeurs suivantes : 

* azure-dns pour le nom associé à l’adresse IP publique et aux certificats générés. Il s’agit du nom DNS de votre machine virtuelle. La machine virtuelle peut alors être éteinte en toute sécurité, libérer l’adresse IP dynamique et donner la possibilité de se reconnecter après le redémarrage de la machine virtuelle avec une nouvelle adresse IP. Le préfixe du nom doit être unique pour cette région UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* Ouverture du port 80 sur la machine virtuelle pour l’accès sortant à Internet
* Taille de la machine virtuelle pour utiliser le stockage Premium plus rapide
* Stockage Premium utilisé pour le disque de la machine virtuelle

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Choisissez un hôte Docker avec docker-machine
Une fois que vous avez une entrée dans docker-machine pour votre hôte, vous pouvez définir l’hôte par défaut lorsque vous exécutez des commandes Docker.

## <a name="using-powershell"></a>Utiliser PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Avec Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Vous pouvez maintenant exécuter des commandes Docker sur l’hôte spécifié

```
docker ps
docker info
```

## <a name="run-a-container"></a>Exécuter un conteneur
Avec un hôte configuré, vous pouvez maintenant exécuter un serveur web simple pour vérifier si votre hôte a bien été configuré.
Comme nous utilisons ici une image nginx standard, spécifiez que le serveur doit écouter sur le port 80, et que si la machine virtuelle hôte redémarre, le conteneur redémarre également (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

La sortie doit ressembler à ceci :

```
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

Pour connaître le conteneur en cours d’exécution, saisissez `docker-machine ip <VM name>` pour rechercher l’adresse IP à saisir dans le navigateur :

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Exécution d’un conteneur ngnix](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Résumé
Avec docker-machine, vous pouvez facilement approvisionner des hôtes Docker dans Azure pour chacune de vos validations d’hôte Docker.
Pour l’hébergement de production de conteneurs, consultez le [Service de conteneur Azure](http://aka.ms/AzureContainerService)

Pour développer des applications .NET Core avec Visual Studio, consultez [Outils Docker pour Visual Studio](http://aka.ms/DockerToolsForVS)

