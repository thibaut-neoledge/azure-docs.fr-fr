---
title: Docker et Compose sur une machine virtuelle | Microsoft Docs
description: "Brève introduction à l’utilisation de Compose et Docker sur des machines virtuelles Linux dans Azure"
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
ms.date: 09/22/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 6433922d1acfad4e84f57a51d6ebb5b6fdea31b1


---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>Prise en main de Docker et Compose pour définir et exécuter une application à conteneurs multiples sur une machine virtuelle Azure
Découvrez comment prendre en main Docker et [Compose](http://github.com/docker/compose) pour définir et exécuter une application complexe sur une machine virtuelle Linux dans Azure. Avec Compose, un fichier texte simple vous permet de définir une application composée de plusieurs conteneurs Docker. Vous faites ensuite tourner votre application dans une seule commande qui fait tout pour déployer votre environnement défini. 

À titre d’exemple, cet article vous explique comment configurer rapidement un blog WordPress avec une base de données SQL MariaDB de type backend sur une machine virtuelle Ubuntu. Vous pouvez également utiliser Compose pour configurer des applications plus complexes.

## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Étape 1 : Configurer une machine virtuelle Linux en tant qu’hôte Docker
La Place de marché Azure propose différentes procédures Azure et des images ou des modèles Resource Manager disponibles pour la création d’une machine virtuelle Linux et sa configuration en tant qu’hôte Docker. Par exemple, la page [Utilisation de l’extension de machine virtuelle Docker pour déployer votre environnement](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) contient une procédure rapide relative à la création d’une machine virtuelle Ubuntu avec l’extension de machine virtuelle Docker à l’aide d’un [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Si vous utilisez l’extension de machine virtuelle Docker, votre machine virtuelle est automatiquement configurée en tant que hôte Docker et Compose est déjà installé. L’exemple de cet article montre comment utiliser [l’interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli-install.md) en mode Ressource Manager pour créer la machine virtuelle.

La commande de base issue du document précédent crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `West US` et déploie une machine Virtuelle avec l’extension Azure Docker VM installée :

```azurecli
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

## <a name="step-2-verify-that-compose-is-installed"></a>Étape 2: vérifier que Compose est installé
Une fois le déploiement terminé, utilisez SSH pour votre nouvel hôte Docker en utilisant le nom DNS fourni pendant le déploiement. Vous pouvez utiliser `azure vm show -g myDockerResourceGroup -n myDockerVM` pour afficher les détails de votre machine virtuelle, y compris le nom DNS.

Pour vérifier que Compose est installé sur la machine virtuelle, exécutez la commande suivante :

```bash
docker-compose --version
```

La sortie doit être similaire à `docker-compose 1.6.2, build 4d72027`.

> [!TIP]
> Si vous avez utilisé une autre méthode pour créer un hôte Docker et devez installer Compose vous-même, consultez la [Documentation Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).
> 
> 

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
> 
> 

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



<!--HONumber=Nov16_HO3-->


