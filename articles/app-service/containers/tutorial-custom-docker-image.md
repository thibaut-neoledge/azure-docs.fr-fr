---
title: "Utiliser une image Docker personnalisée pour Azure Web App for Containers | Microsoft Docs"
description: "Comment utiliser une image Docker personnalisée pour Azure Web App for Containers."
keywords: azure app service, application web, linux, docker, conteneur
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 760772d1d1c79dd4a1114c36971de0b3693ab74f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-custom-docker-image-for-azure-web-app-for-containers"></a>Utiliser une image Docker personnalisée pour Azure Web App for Containers

[Web App for Containers](app-service-linux-intro.md) fournit des images Docker intégrées sur Linux avec prise en charge de versions spécifiques, comme PHP 7.0 et Node.js 4.5. Web App for Containers tire parti de la technologie de conteneur Docker pour héberger à la fois des images intégrées et des images personnalisées en tant que système PaaS (Platform as a Service). Dans ce didacticiel, vous apprendrez à créer une image Docker personnalisée afin de l’utiliser dans Web App for Containers, qui constitue un modèle commun s’il n’existe aucune image intégrée pour votre langue, ou si votre application nécessite une configuration spécifique qui n’est pas fournie dans les images intégrées.

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* [Git](https://git-scm.com/downloads)
* Un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/) actif
* [Docker](https://docs.docker.com/get-started/#setup)
* Un [compte Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Téléchargez l’exemple

Dans une fenêtre de terminal, exécutez la commande ci-après pour cloner le référentiel de l’exemple d’application sur votre ordinateur local, puis accédez au répertoire contenant l’exemple de code.

```bash
git clone https://github.com/Azure-Samples/use-custom-docker-image.git
cd use-custom-docker-image
```

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="build-the-image-from-the-docker-file"></a>Créer l’image à partir du fichier Docker

Le fichier Docker suivant décrit l’environnement Python requis pour exécuter notre application. En outre, l’image définit un serveur [SSH](https://www.ssh.com/ssh/protocol/) pour sécuriser la communication entre le conteneur et l’hôte.

```docker
# Use an official Python runtime as a parent image
FROM python

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Run python's package manager and install the flask package
RUN pip install flask

# Configure ports
EXPOSE 2222 80

# Run apt-get, to install the SSH server
RUN apt-get update \ 
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "root:Docker!" | chpasswd

#Copy the sshd_config file to its new location
COPY sshd_config /etc/ssh/

# Start the SSH service
RUN service ssh start

# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/
    
# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh 

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Pour créer l’image Docker, exécutez la commande `docker build`, puis indiquez un nom, `mydockerimage`, et une balise, `v1.0.0`. Remplacez `<docker-id>` par votre ID de compte Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

La commande génère des informations qui ressemblent à ce qui suit :

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  23.04kB
Step 1/13 : FROM python
 ---> 968120d8cbe8
Step 2/13 : WORKDIR /app
 ---> Using cache
 ---> dd6fdca5aa65
Step 3/13 : ADD . /app
 ---> e05c8f4beeae
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
Get:3 http://deb.debian.org jessie Release.gpg [2373 B]
Fetched 9988 kB in 7s (1266 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
The following extra packages will be installed:
  init-system-helpers libwrap0 openssh-sftp-server
Suggested packages:
  ssh-askpass rssh molly-guard ufw monkeysphere
Recommended packages:
  tcpd xauth ncurses-term
The following NEW packages will be installed:
  init-system-helpers libwrap0 openssh-server openssh-sftp-server
0 upgraded, 4 newly installed, 0 to remove and 3 not upgraded.
Need to get 442 kB of archives.
After this operation, 1138 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian/ jessie/main libwrap0 amd64 7.6.q-25 [58.5 kB]
Creating SSH2 RSA key; this may take some time ...
2048 f0:e9:fb:69:de:62:a4:5c:a3:7c:b3:41:e9:2e:96:a3 /etc/ssh/ssh_host_rsa_key.pub (RSA)
Creating SSH2 DSA key; this may take some time ...
1024 4a:5e:89:bd:aa:2d:71:bb:0e:3a:32:94:fb:c0:b1:4d /etc/ssh/ssh_host_dsa_key.pub (DSA)
Processing triggers for systemd (215-17+deb8u7) ...
 ---> 5b416a7dcdca
Removing intermediate container 283b3b4623d7
Step 13/13 : CMD python app.py
 ---> Running in 1c776e5e0772
 ---> 1bfc1bbc968d
Removing intermediate container 1c776e5e0772
Successfully built 1bfc1bbc968d
Successfully tagged <docker-id>/myDockerImage:v1.0.0
```

Vérifiez que la build fonctionne en exécutant le conteneur Docker. Exécutez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) puis passez le nom et la balise de l’image. Vous devez également spécifier le port à l’aide de l’argument `-p`.

```bash
docker run -p 80:2222 <docker-ID>/mydockerimage:v1.0.0
```

Vérifiez que l’application web et le conteneur fonctionnent correctement en parcourant l’application web localement.

![Tester l’application web localement](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-a-docker-image-to-docker-hub"></a>Transférer une image Docker vers Docker Hub

Un registre est une application qui héberge des images et fournit des services d’image et de conteneur. Pour partager votre image, vous devez la transférer vers un registre. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Transfert vers un registre Docker privé Consultez les instructions facultatives pour [transférer une image Docker vers un registre privé](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub est un registre d’images Docker qui vous permet d’héberger vos propres référentiels publics ou privés. Pour transférer une image Docker personnalisée vers le registre Docker Hub public, utilisez la commande [docker push](https://docs.docker.com/engine/reference/commandline/push/) et indiquez le nom complet et la balise de l’image. L’exemple suivant montre un nom complet et une balise d’image :

```bash
<docker-id>/image-name:tag
```

Si vous ne vous êtes pas encore connecté au registre Docket Hub, faites-le en utilisant la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/) avant de transférer une image.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

Un message « login succeeded » (connexion réussie) confirme que vous êtes connecté. Une fois connecté, vous pouvez transférer l’image vers le registre Docker Hub à l’aide de la commande [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Vérifiez que le transfert a réussi en consultant les résultats de la commande.

```bash
The push refers to a repository [docker.io/<docker-id>/python-flask]
e9aa2c6d0f34: Pushed
0fdcb490aeec: Pushed
08ae61c7869c: Pushed
2548e7db2a94: Mounted from library/python
325b9d6f2920: Pushed
815acdffadff: Mounted from library/python
97108d083e01: Mounted from library/python
5616a6292c16: Mounted from library/python
f3ed6cb59ab0: Mounted from library/python
654f45ecb7e3: Mounted from library/python
2c40c66f7667: Mounted from library/python
v1: digest: sha256:a910d5b77e6960c01745a87c35f3d1a13ba73231ac9a4664c5011b1422d59b60 size: 2632
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

## <a name="create-web-app-for-containers"></a>Créer une application Web App for Containers

Vous pouvez héberger des applications Linux natives dans le cloud en utilisant Azure Web Apps. Pour créer une application Web App for Containers, vous devez exécuter les commandes CLI d’Azure qui créent un groupe, un plan de service puis l’application web elle-même. Tout d’abord, exécutez la commande [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create), puis transmettez un emplacement et un nom unique.

```azurecli-interactive
az group create --location "West Europe" --name myResourceGroup
```

Le résultat est similaire à l’exemple suivant :

```json
{
  "id": "/subscriptions/432849d3e4-4f90-a782-87c11e-5e59d6dd/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Utilisez le nom du groupe pour créer un plan de service d’application à l’aide de la commande [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create). Vous devez également attribuer un nom unique et définir l’indicateur `--is-linux`.

```azurecli-interactive
az appservice plan create --name myServicePlan --resource-group myResourceGroup --is-linux
```

La création d’un plan de service génère des résultats semblables à l’exemple suivant :

```json
  {- Starting...
  "adminSiteName": null,
  "appServicePlanName": "myServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/resourceGroups/myResourceGroup/provide
rs/Microsoft.Web/serverfarms/myServicePlan",
  "kind": "linux",
  "location": "West Europe", 
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "tier": "Basic"
  },
  "status": "Ready",
  "subscription": "",
  "tags": null,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

Maintenant que le groupe de ressources et le plan de service ont été créés, vous pouvez exécuter la commande [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) pour créer l’application web. Notez que la pile d’exécution est Python 3.4 et que l’application web utilise la configuration de groupe de ressources et de plan de service des étapes précédentes.

```azurecli-interactive
az webapp create -g myResourceGroup -p myServicePlan -n <web-app-name> --runtime "python|3.4" 
```

La commande pour créer une application web génère les résultats suivants :

```json
{- Starting ..
  "availabilityState": "Normal",
   "enabled": true,
  "enabledHostNames": [
    "<web-app-name>.azurewebsites.net",
    "<web-app-name>.scm.azurewebsites.net"
  ],
  "ftpPublishingUrl": "ftp://waws-prod-am2-085.ftp.azurewebsites.windows.net/site/wwwroot",
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<web-app-name>.azurewebsites.net",
    },
  ],
  "hostNames": [
    "<web-app-name>.azurewebsites.net"
  ],
  "hostNamesDisabled": false,
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/5e59d6dd-d3e4-4f90-a782-43284987c11e/resourceGroups/myResourceGroup/providers/Microsoft.
Web/sites/<web-app-name>",
  "lastModifiedTimeUtc": "2017-08-08T21:09:33.693333",
  "location": "West Europe",
  "name": "<web-app-name>",
  "outboundIpAddresses": "13.81.108.99,52.232.76.83,52.166.73.203,52.233.173.39,52.233.159.48",
  "resourceGroup": "myResourceGroup"
}

```

La plupart des applications web ont des paramètres d’application qui doivent être configurés. Si vous utilisez une image Docker existante créée par une autre personne, cette image peut nécessiter un port autre que le port 80 pour l’application. Pour définir `WEBSITES_PORT`, exécutez la commande [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config/appsettings), comme indiqué dans l’exemple de code suivant :

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <web-app-name> --settings WEBSITES_PORT=2222
```

> [!NOTE]
> Les paramètres d’application respectent la casse.
>

Vérifiez que l’application web fonctionne en la parcourant. N’oubliez pas le numéro de port.

![Test de la configuration du port de l’application web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="configure-web-app-to-use-docker-container-from-docker-hub"></a>Configurer l’application web pour utiliser le conteneur Docker à partir de Docker Hub

La commande [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config) vous permet d’utiliser une image Docker personnalisée.

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Vous déployez à partir d’un registre Docker privé ? Consultez les instructions facultatives afin de [configurer une application web pour utiliser un conteneur Docker à partir d’un registre privé](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

Pour configurer l’application web afin d’utiliser un registre Docker public, transmettez le nom de l’application, le groupe de ressources, le nom de l’image et l’URL à la commande [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set).

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage
--docker-registry-server-url <docker-id>/myContainerRegistry
```

Une modification de configuration réussie retourne des informations générales sur le conteneur.

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "{docker-id}/mydockerimage:v1.0.0"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{docker-id}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

## <a name="test-the-application-in-azure"></a>Test de l’application dans Azure

Avant de tester, vous devez redémarrer l’application web à l’aide de la commande [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart) pour que les modifications de configuration prennent effet.

```azurecli-interactive
az webapp restart --name <web-app-name> --resource-group myResourceGroup
```

La commande restart redémarre l’application web en mode silencieux, et aucune information n’apparaît dans le terminal. Une fois l’application web en cours d’exécution, testez-la en parcourant ses URL à l’adresse `http://<username>.azurewebsites.net`. Vérifiez que l’application affiche le nouveau message d’accueil.

![Test de l’application web dans Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Modification de l’application web et redéploiement

Un fichier Python nommé `app.py` existe dans le répertoire `using-custom-docker-image`. Ce fichier contient une ligne de code qui affiche un message `Hello World!`. Remplacez la ligne afin qu’elle affiche le message `Hello World of Web Apps running in Docker Containers!`.

```python
return "Hello World of Web Apps running in Docker Containers!"
```

Après avoir modifié et enregistré le fichier Python, vous devez reconstruire et transmettre la nouvelle image Docker. Redémarrez ensuite l’application web pour que les modifications prennent effet. Utilisez les mêmes commandes que vous avez utilisées précédemment dans ce didacticiel. Vous pouvez faire référence aux sections permettant de [créer l’image à partir du fichier Docker](#build-the-image-from-the-docker-file) et de [transmettre l’image Docker](#push-docker-image). Tester l’application web en suivant les instructions de la section [Tester l’application dans Azure](#tTest-the-application-in-azure)

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Se connecter à Web App for Containers à l’aide de SSH

SSH permet d’établir une communication sécurisée entre un conteneur et un client. Pour qu’une image Docker personnalisée prenne en charge SSH, vous devez l’intégrer à un fichier Docker. Vous activez SSH dans le fichier Docker lui-même. Les instructions SSH ayant déjà été ajoutées à l’exemple de fichier docker, vous pouvez appliquer ces instructions à votre propre image personnalisée :

* Une instruction [RUN](https://docs.docker.com/engine/reference/builder/#run) qui appelle `apt-get`, puis définit le mot de passe du compte racine sur `"Docker!"`.

    ```docker
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

    > [!NOTE]
    > Cette configuration n’autorise pas les connexions externes avec le conteneur. SSH est disponible uniquement via le site Kudu/SCM. Le site Kudu/SCM est authentifié avec des informations d’identification de publication.

* Une instruction [COPY](https://docs.docker.com/engine/reference/builder/#copy) qui indique au moteur Docker de copier le fichier [sshd_config](http://man.openbsd.org/sshd_config) dans le répertoire */etc/ssh/*. Votre fichier de configuration doit être basé sur notre fichier [sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config) dans le référentiel GitHub Azure-App-Service.

    > [!NOTE]
    > Le fichier *sshd_config* doit inclure les éléments suivants : 
    > * `Ciphers` doit inclure au moins un élément dans cette liste : `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` doit inclure au moins un élément dans cette liste : `hmac-sha1,hmac-sha1-96`.

    ```docker
    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/
    ```

* Une instruction [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) qui expose le port 2222 dans le conteneur. Bien que le mot de passe racine soit connu, le port 2222 n’est pas accessible à partir d’Internet. Il s’agit d’un port interne accessible uniquement par les conteneurs au sein du réseau de pont d’un réseau privé virtuel. Après cela, les commandes copient les détails de la configuration SSH et démarrent le service `ssh`.

    ```docker
    # Configure ports
    EXPOSE 2222 80

    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/

    # Start the SSH service
    RUN service ssh start
    ```

Le fichier `init_container.sh` dans l’exemple de code contient des instructions sur la façon d’initialiser le conteneur lorsqu’il s’exécute. Le fichier Docker utilise les instructions [COPY](https://docs.docker.com/engine/reference/builder/#copy), [RUN](https://docs.docker.com/engine/reference/builder/#run) et [CMD](https://docs.docker.com/engine/reference/builder/#cmd) pour lancer correctement le script `init_container.sh`.

```docker
# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/

# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Vous pouvez vérifier que certaines applications sont en cours d’exécution dans le conteneur. Pour inspecter le conteneur et vérifier les processus en cours d’exécution, démarrez en ouvrant un navigateur puis accédez à `https://<app name>.scm.azurewebsites.net/webssh/host`. Vous êtes ensuite redirigé vers une page affichant une console interactive. Exécutez la commande `top` à l’invite.

```bash
top
```

La commande `top` expose tous les processus en cours d’exécution dans un conteneur.

```bash
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Félicitations ! Vous avez configuré une image Docker personnalisée destinée à Azure Web App for Containers.

## <a name="push-a-docker-image-to-private-registry-optional"></a>Transmettre une image Docker au registre privé (facultatif)

Azure Container Registry est un service Docker géré à partir d’Azure pour l’hébergement d’images privées. Les déploiements peuvent être de tout type, y compris [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) et Azure App Service. Azure Container Registry s’utilise comme n’importe quel registre privé. Par conséquent, si vous devez utiliser votre propre registre privé, les étapes pour effectuer cette tâche sont identiques.

Utilisez la commande [az acr create](https://docs.microsoft.com/cli/azure/acr#az_acr_create) pour créer un Azure Container Registry. Transmettez le nom, le groupe de ressources et `Basic` pour la référence SKU. Les références disponibles sont `Classic`, `Basic`, `Standard`, et `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

La création d’un conteneur génère les résultats suivants :

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/{azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Pour transférer une image vers le registre, vous devez fournir les informations d’identification afin que le registre accepte le transfert. Vous pouvez récupérer ces informations d’identification à l’aide de la commande [az acr show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show). 

```azurecli-interactive
az acr credential show --name {azure-container-registry-name}
```

La commande fournit deux mots de passe qui peuvent être utilisés avec le nom d’utilisateur.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<azure-container-registry-name>"
}
```

Maintenant que vous disposez des informations d’identification nécessaires, connectez-vous à Azure Container Registry à l’aide de la commande `docker login`. Le nom du serveur est obligatoire pour se connecter. Utilisez le format `{azure-container-registry-name>.azurecr.io`.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <azure-container-registry-name> --password <password> 
```

Vérifiez que la connexion a réussi. Transférez l’image en utilisant la commande `docker push` et le balisage de l’image avec le nom du registre, suivi du nom et de la balise de l’image.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Vérifiez que le transfert a correctement ajouté un conteneur au registre en répertoriant les référentiels ACR. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

L’affichage des images dans le registre confirme que `mydockerimage` figure dans le registre.

```json
[
  "mydockerimage"
]
```

## <a name="configure-web-app-to-use-docker-container-from-a-private-registry-optional"></a>Configurer une application web pour utiliser un conteneur Docker à partir d’un registre privé (facultatif)

Vous pouvez configurer des applications web sur Linux afin qu’elles exécutent un conteneur stocké dans Azure Container Registry. Azure Container Registry s’utilise comme n’importe quel registre privé. Par conséquent, si vous devez utiliser votre propre registre privé, les étapes pour effectuer cette tâche sont identiques.

La commande [az acr credential show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) affiche les mots de passe pour le registre de conteneurs. Copiez le nom d’utilisateur et l’un des mots de passe afin de les utiliser pour configurer l’application web à l’étape suivante.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<azure-container-registry-name>"
}
```

Exécutez la commande [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set). Cette commande affecte l’image Docker personnalisée à l’application web. Notez que vous avez besoin d’une URL au format `https://{your-registry-username}.azurecr.io`. En outre, l’application web a besoin du nom d’utilisateur et du mot de passe obtenus à l’étape précédente pour accéder au registre de conteneurs.

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

> [!NOTE]
> L’élément `https` dans les URL du registre est requis.
>

La commande fournit un résultat similaire à la chaîne JSON suivante, indiquant que la modification de configuration a réussi :

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "mycontainerregistry.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{azure-container-registry-name}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

[Forum aux questions sur Azure App Service Web App for Containers](app-service-linux-faq.md)
