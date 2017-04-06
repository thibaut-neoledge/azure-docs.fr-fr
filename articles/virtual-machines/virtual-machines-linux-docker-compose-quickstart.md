---
title: Utilisation de Docker Compose sur une machine virtuelle Linux dans Azure | Microsoft Docs
description: "Procédure d’utilisation de Docker et Compose sur des machines virtuelles Linux avec l’interface CLI Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 404b1784717d7a2e56e4154dd8995093b37a3bc1
ms.lasthandoff: 03/29/2017


---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Prise en main de Docker et Compose pour définir et exécuter une application à conteneurs multiples dans Azure
Avec [Compose](http://github.com/docker/compose), un fichier texte simple vous permet de définir une application composée de plusieurs conteneurs Docker. Vous faites ensuite tourner votre application dans une seule commande qui fait tout pour déployer votre environnement défini. À titre d’exemple, cet article vous explique comment configurer rapidement un blog WordPress avec une base de données SQL MariaDB de type backend sur une machine virtuelle Ubuntu. Vous pouvez également utiliser Compose pour configurer des applications plus complexes.

## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Étape 1 : Configurer une machine virtuelle Linux en tant qu’hôte Docker
La Place de marché Azure propose différentes procédures Azure et des images ou des modèles Resource Manager disponibles pour la création d’une machine virtuelle Linux et sa configuration en tant qu’hôte Docker. Par exemple, la page [Utilisation de l’extension de machine virtuelle Docker pour déployer votre environnement](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) contient une procédure rapide relative à la création d’une machine virtuelle Ubuntu avec l’extension de machine virtuelle Docker à l’aide d’un [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Si vous utilisez l’extension de machine virtuelle Docker, votre machine virtuelle est automatiquement configurée en tant que hôte Docker et Compose est déjà installé. Vous pouvez créer une machine virtuelle et utiliser l’extension de machine virtuelle Docker à l’aide d’une des versions suivantes de l’interface CLI :

- [Azure CLI 2.0](#azure-cli-20) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager
- [Azure CLI 1.0](#azure-cli-10) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager

### <a name="azure-cli-20"></a>Azure CLI 2.0
Installez la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure avec la commande [az login](/cli/azure/#login).

Tout d’abord, créez un groupe de ressources pour votre environnement Docker avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `West US` :

```azurecli
az group create --name myResourceGroup --location westus
```

Ensuite, déployez une machine virtuelle avec la commande [az group deployment create](/cli/azure/group/deployment#create) qui inclut l’extension de machine virtuelle Azure Docker de [ce modèle Azure Resource Manager sur GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Indiquez vos propres valeurs pour `newStorageAccountName`, `adminUsername`, `adminPassword` et `dnsNameForPublicIP` :

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Le déploiement prend quelques minutes. Une fois le déploiement terminé, [passez à l’étape suivante](#step-2-verify-that-compose-is-installed) pour procéder à la connexion SSH à votre machine virtuelle. 

Sinon, si vous souhaitez retourner le contrôle à l’invite et permettre au déploiement de continuer en arrière-plan, ajoutez l’indicateur `--no-wait` à la commande précédente. Ce processus vous permet d’effectuer d’autres tâches dans l’interface CLI pendant que le déploiement se poursuit pendant quelques minutes. Vous pouvez ensuite afficher des détails sur l’état de l’hôte Docker avec la commande [az vm show](/cli/azure/vm#show). L’exemple suivant vérifie l’état de la machine virtuelle nommée `myDockerVM` (nom par défaut extrait du modèle - ne pas modifier ce nom) dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az vm show --resource-group myResourceGroup --name myDockerVM \
  --query [provisioningState] --output tsv
```

Lorsque cette commande renvoie `Succeeded`, le déploiement est terminé et vous pouvez procéder à la connexion SSH à la machine virtuelle à l’étape suivante.

### <a name="azure-cli-10"></a>Azure CLI 1.0
Installez la dernière version [d’Azure CLI 1.0](../cli-install-nodejs.md) et connectez-vous à un compte Azure. Assurez-vous que vous utilisez le mode Resource Manager pour créer la machine virtuelle (`azure config mode arm`).

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `West US` et déploie une machine virtuelle avec l’extension de machine virtuelle Azure Docker. Un [modèle Azure Resource Manager issu de GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) est utilisé pour déployer l’environnement :

```azurecli
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

L’interface de ligne de commande Azure vous renvoie à l’invite après quelques secondes, mais l’hôte Docker est toujours en cours de création et de configuration. Le déploiement prend quelques minutes. Vous pouvez afficher des détails sur l’état de l’hôte Docker à l’aide de la commande `azure vm show`. L’exemple suivant vérifie l’état de la machine virtuelle nommée `myDockerVM` (nom par défaut extrait du modèle - ne pas modifier ce nom) dans le groupe de ressources nommé `myResourceGroup`. Entrez le nom du groupe de ressources que vous avez créé à l’étape précédente :

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

## <a name="step-2-verify-that-compose-is-installed"></a>Étape 2: vérifier que Compose est installé
Une fois le déploiement terminé, utilisez SSH pour votre nouvel hôte Docker en utilisant le nom DNS fourni pendant le déploiement. Vous pouvez utiliser `azure vm show -g myResourceGroup -n myDockerVM` (Azure CLI 1.0) ou `az vm show -g myResourceGroup -n myDockerVM -d --query [fqdns] -o tsv` (Azure CLI 2.0) pour afficher les détails de votre machine virtuelle, y compris le nom DNS.

Pour vérifier que Compose est installé sur la machine virtuelle, exécutez la commande suivante :

```bash
docker-compose --version
```

La sortie doit être similaire à `docker-compose 1.6.2, build 4d72027`.

> [!TIP]
> Si vous avez utilisé une autre méthode pour créer un hôte Docker et devez installer Compose vous-même, consultez la [Documentation Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>Étape 3 : Créer un fichier de configuration docker-compose.yml
À présent, créez un fichier `docker-compose.yml` , qui est simplement un fichier texte de configuration, pour définir les conteneurs Docker s’exécutant sur la machine virtuelle. Le fichier indique l’image à exécuter sur chaque conteneur (il peut également s’agir d’un build d’un fichier Dockerfile), les variables d’environnement et les dépendances nécessaires, les ports et les liens entre les conteneurs. Pour plus d’informations sur la syntaxe du fichier yml, consultez [Référence du fichier Compose](http://docs.docker.com/compose/yml/).

Créez le fichier `docker-compose.yml` comme suit :

```bash
touch docker-compose.yml
```

Utilisez votre éditeur de texte favori pour ajouter d’autres données au fichier. L’exemple suivant utilise l’éditeur `vi` :

```bash
vi docker-compose.yml
```

Collez l’exemple suivant dans votre fichier texte. Cette configuration utilise des images du [registre DockerHub](https://registry.hub.docker.com/_/wordpress/) pour installer WordPress (le système open source de gestion de blogs et de contenu) et une base de données principale associée SQL MariaDB. Entrez votre propre `MYSQL_ROOT_PASSWORD` comme suit :

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>Étape 4 : Démarrer les conteneurs avec Compose
Dans le même répertoire que votre fichier `docker-compose.yml`, exécutez la commande suivante (selon votre environnement, vous devrez peut-être exécuter `docker-compose` à l’aide de `sudo`.) :

```bash
docker-compose up -d
```

Cette action démarre les conteneurs Docker spécifiés dans `docker-compose.yml`. L’exécution de cette étape peut prendre une minute ou deux. Vous voyez une sortie similaire à l’exemple suivant :

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Veillez à utiliser l’option **-d** au démarrage, de manière à ce que les conteneurs s’exécutent en continu en arrière-plan.


Pour vérifier que les contrôleurs sont en cours d’exécution, tapez `docker-compose ps`. Le résultat suivant doit s’afficher :

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Vous pouvez maintenant vous connecter directement à WordPress sur la machine virtuelle sur le port 80. Ouvrez un navigateur Web et entrez le nom DNS de votre machine virtuelle (par exemple `http://myresourcegroup.westus.cloudapp.azure.com`). L’écran de démarrage de WordPress, vous permettant de terminer l’installation et de prendre en main l’application, doit maintenant s’afficher.

![Écran d’accueil WordPress][wordpress_start]

## <a name="next-steps"></a>Étapes suivantes
* Pour accéder à d’autres options de configuration de Docker et Compose dans votre machine virtuelle Docker, consultez le [Guide de l’utilisateur de l’extension de machine virtuelle Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) . Par exemple, une option consiste à placer un fichier yml Compose (converti au format JSON) directement dans la configuration de l’extension de machine virtuelle Docker.
* Pour plus d’exemples de développement et de déploiement d’applications à plusieurs conteneurs, consultez la [Référence de la ligne de commande Compose](http://docs.docker.com/compose/reference/) et le [Guide d’utilisation](http://docs.docker.com/compose/).
* Utilisez un modèle Microsoft Azure Manager, le vôtre ou un élément de la [communauté](https://azure.microsoft.com/documentation/templates/), pour déployer une machine virtuelle Microsoft Azure avec Docker et une application configurée avec Compose. Par exemple, le modèle [Deploy a WordPress blog with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) (en anglais) utilise Docker et Compose pour déployer rapidement WordPress avec un serveur principal MySQL sur une machine virtuelle Ubuntu.
* Essayez d’intégrer Docker Compose à un cluster [Docker Swarm](virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . Pour examiner des scénarios, consultez [Utilisation de Compose avec Swarm](https://docs.docker.com/compose/swarm/) .

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

