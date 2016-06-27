<properties
   pageTitle="Docker et Compose sur une machine virtuelle | Microsoft Azure"
   description="Brève introduction à l’utilisation de Compose et Docker sur des machines virtuelles Linux dans Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/10/2016"
   ms.author="danlep"/>

# Prise en main de Docker et Compose pour définir et exécuter une application à conteneurs multiples sur une machine virtuelle Azure

Découvrez comment prendre en main Docker et [Compose](http://github.com/docker/compose) pour définir et exécuter une application complexe sur une machine virtuelle Linux dans Azure. Avec Compose (le successeur de *Fig*), un fichier texte simple vous permet de définir une application composée de plusieurs conteneurs Docker. Faites ensuite tourner votre application dans une seule commande qui fait tout pour l’exécuter sur la machine virtuelle.

À titre d’exemple, cet article vous explique comment configurer rapidement un blog WordPress avec une base de données SQL MariaDB backend sur une machine virtuelle Ubuntu, mais vous pouvez également utiliser Compose pour configurer des applications plus complexes.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Si vous découvrez Docker et les conteneurs, visionnez la vidéo [Docker high level whiteboard](https://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/) (en anglais).

## Étape 1 : Configurer une machine virtuelle Linux en tant qu’hôte Docker

Azure Marketplace comporte différentes procédures Azure et des images ou des modèles Resource Manager disponibles pour la création d’une machine virtuelle Linux et sa configuration en tant qu’hôte Docker. Par exemple, la page [Utilisation de l’extension de machine virtuelle Docker pour déployer votre environnement](virtual-machines-linux-dockerextension.md) contient une procédure rapide relative à la création d’une machine virtuelle Ubuntu avec l’extension de machine virtuelle Docker à l’aide d’un [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Si vous utilisez l’extension de machine virtuelle Docker, votre machine virtuelle est automatiquement configurée en tant que hôte Docker et Compose est déjà installé. L’exemple de cet article vous montre comment utiliser l’[interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli-install.md) (CLI Azure) en mode Ressource Manager pour créer la machine virtuelle.

## Étape 2: vérifier que Compose est installé

Une fois que la machine virtuelle Linux s’exécute avec Docker, connectez-vous à celle-ci à partir de votre ordinateur client à l’aide de SSH.

Pour tester votre installation de Compose sur une machine virtuelle, exécutez la commande ci-dessous.

```
$ docker-compose --version
```

Vous verrez une sortie semblable à `docker-compose 1.6.2, build 4d72027`.

>[AZURE.TIP] Si vous avez utilisé une autre méthode pour créer un hôte Docker et devez installer Compose vous-même, consultez la [documentation Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## Étape 3 : Créer un fichier de configuration docker-compose.yml

À présent, vous allez créer un fichier `docker-compose.yml`, qui est simplement un fichier texte de configuration, pour définir les conteneurs Docker s’exécutant sur la machine virtuelle. Le fichier indique l’image à exécuter sur chaque conteneur (il peut également s’agir d’un build d’un fichier Dockerfile), les variables d’environnement et les dépendances nécessaires, les ports, les liens entre les conteneurs, etc. Pour plus d’informations sur la syntaxe du fichier yml, consultez la page de [référence du fichier Compose](http://docs.docker.com/compose/yml/).

Créez un répertoire de travail sur votre machine virtuelle et utilisez votre éditeur de texte préféré pour créer le fichier `docker-compose.yml`. Pour tester un exemple simple à des fins de preuve de concept, copiez le texte suivant dans le fichier. Cette configuration utilise des images du [registre DockerHub](https://registry.hub.docker.com/_/wordpress/) pour installer WordPress (le système open source de gestion de blogs et de contenu) et une base de données principale associée SQL MariaDB.

 ```
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

## Étape 4 : Démarrer les conteneurs avec Compose

Dans le répertoire de tavail de votre machine virtuelle, exécutez simplement la commande suivante. Selon votre environnement, vous devrez peut-être exécuter `docker-compose` à l’aide de `sudo`.

```
$ docker-compose up -d

```

Cette action démarre les conteneurs Docker spécifiés dans `docker-compose.yml`. Le résultat doit être similaire à ceci :

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE] Veillez à utiliser l’option **-d** au démarrage, de manière à ce que les conteneurs s’exécutent en continu en arrière-plan.

Pour vérifier que les contrôleurs sont en cours d’exécution, tapez `docker-compose ps`. Le résultat suivant devrait s’afficher :

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Vous pouvez maintenant vous connecter directement à WordPress sur la machine virtuelle sur le port 80. Si vous avez utilisé un modèle Resource Manager pour créer la machine virtuelle, essayez de vous connecter à `http://<dnsname>.<region>.cloudapp.azure.com` ou, si vous avez créé la machine virtuelle à l’aide du modèle de déploiement Classic, essayez de vous connecter à `http://<cloudservicename>.cloudapp.net`. L’écran de démarrage de WordPress, vous permettant de terminer l’installation et de prendre en main l’application, doit maintenant s’afficher.

![Écran d’accueil WordPress][wordpress_start]


## Étapes suivantes

* Consultez le [guide de l’utilisateur sur l’extension de machine virtuelle Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) pour accéder à d’autres options de configuration de Docker et Compose dans votre machine virtuelle Docker. Par exemple, une option consiste à placer un fichier yml Compose (converti au format JSON) directement dans la configuration de l’extension de machine virtuelle Docker.
* Consultez la [référence de la ligne de commande Compose](http://docs.docker.com/compose/reference/) et le [guide d’utilisation](http://docs.docker.com/compose/) pour plus d’exemples sur le développement et le déploiement d’applications à plusieurs conteneurs.
* Utilisez un modèle Microsoft Azure Manager, le vôtre ou un élément de la [communauté](https://azure.microsoft.com/documentation/templates/), pour déployer une machine virtuelle Microsoft Azure avec Docker et une application configurée avec Compose. Par exemple, le modèle [Deploy a WordPress blog with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) (en anglais) utilise Docker et Compose pour déployer rapidement WordPress avec un serveur principal MySQL sur une machine virtuelle Ubuntu.
* Essayez d’intégrer Docker Compose à un cluster [Docker Swarm](virtual-machines-linux-docker-swarm.md). Pour examiner des scénarios, consultez [Using Compose with Swarm (Utilisation de Compose avec Swarm)](https://docs.docker.com/compose/swarm/).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_0615_2016-->