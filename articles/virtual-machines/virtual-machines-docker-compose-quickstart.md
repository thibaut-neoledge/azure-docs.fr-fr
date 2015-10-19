<properties
   pageTitle="Docker et Compose sur une machine virtuelle | Microsoft Azure"
   description="Brève introduction de l’utilisation de Compose et Docker sur des machines virtuelles Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/07/2015"
   ms.author="danlep"/>

# Prise en main de Docker et Compose sur une machine virtuelle Microsoft Azure

Cet article vous montre comment commencer à utiliser Docker et [Compose](http://github.com/docker/compose) pour définir et exécuter une application complexe sur une machine virtuelle Linux dans Azure. Avec Compose (le successeur de *Fig*), un fichier texte simple vous permet de définir une application composée de plusieurs conteneurs Docker. Faites ensuite tourner votre application dans une seule commande qui fait tout pour l’exécuter sur la machine virtuelle. À titre d’exemple, cet article vous explique comment configurer rapidement un blog WordPress avec une base de données SQL MariaDB principale, mais vous pouvez également utiliser Compose pour configurer des applications plus complexes.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article s’applique à la création de machines virtuelles à l’aide de Resource Manager et des modèles de déploiement classique.

Si vous découvrez Docker et les conteneurs, visionnez la vidéo [Docker high level whiteboard](http://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/) (en anglais).

## Étape 1 : Configurer une machine virtuelle Linux en tant qu’hôte Docker

Votre magasin en ligne Microsoft Azure Marketplace comporte différentes procédures Microsoft Azure et des images décrivant la création d’une machine virtuelle Linux et sa configuration en tant qu’hôte Docker. Par exemple, la page [Utilisation de l’extension de machine virtuelle Docker à partir de l’interface de ligne de commande Microsoft Azure](virtual-machines-docker-with-xplat-cli-install.md) contient une procédure rapide relative à la création d’une machine virtuelle Ubuntu avec l’extension de machine virtuelle Docker. Si vous utilisez l’extension de machine virtuelle Docker, votre machine virtuelle est automatiquement configurée en tant que hôte Docker. L’exemple de cet article vous montre comment utiliser l’[interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli-install.md) (CLI Azure) en mode de gestion des services pour créer la machine virtuelle.

## Étape 2 : Installer Compose

Une fois que la machine virtuelle Linux s’exécute avec Docker, connectez-vous à celle-ci à partir de votre ordinateur client à l’aide de SSH. Si vous en avez besoin, installez [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) en exécutant les deux commandes suivantes.

>[AZURE.TIP]Si vous avez utilisé l’extension de machine virtuelle Docker pour créer votre machine virtuelle, Compose est déjà installé. Ignorez ces commandes et passez à l’étape 3. Vous ne devez installer Compose que si vous avez installé vous-même Docker sur la machine virtuelle.

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Si une erreur d’autorisation refusée est générée, cela signifie que le répertoire /usr/local/bin de la machine virtuelle n’est pas modifiable et que vous devez installer Compose en tant que superutilisateur. Exécutez `sudo -i`, puis les deux commandes ci-dessus, et enfin `exit`.

Pour tester votre installation de Compose, exécutez la commande ci-dessous.

```
$ docker-compose --version
```

Vous verrez une sortie semblable à
```
docker-compose 1.3.2
```.


## Étape 3 : Créer un fichier de configuration docker-compose.yml

À présent, vous allez créer un fichier `docker-compose.yml`, qui est simplement un fichier texte de configuration, pour définir les conteneurs Docker s’exécutant sur la machine virtuelle. Le fichier indique l’image à exécuter sur chaque conteneur (il peut également s’agir d’un build d’un fichier Dockerfile), les variables d’environnement et les dépendances nécessaires, les ports, les liens entre les conteneurs, etc. Pour plus d’informations sur la syntaxe du fichier yml, consultez la page de [référence docker-compose.yml](http://docs.docker.com/compose/yml/).

Créez un répertoire de travail sur votre machine virtuelle et utilisez votre éditeur de texte préféré pour créer le fichier `docker-compose.yml`. Pour tester un exemple simple, copiez le texte suivant dans le fichier. Cette configuration utilise des images du [registre DockerHub](https://registry.hub.docker.com/_/wordpress/) pour installer WordPress (le système open source de gestion de blogs et de contenu) et une base de données principale associée SQL MariaDB.

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 8080:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## Step 4: Start the containers with Compose

In the working directory on your VM, simply run the following command.

```
$ docker-compose up -d

```

This starts the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE]Veillez à utiliser l’option **-d** au démarrage, de manière à ce que les conteneurs s’exécutent en continu en arrière-plan.

Pour vérifier que les contrôleurs sont en cours d’exécution, tapez `docker-compose ps`. Le résultat suivant devrait s’afficher :

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

Vous pouvez maintenant vous connecter directement à WordPress sur la machine virtuelle, en accédant à `http://localhost:8080`. Si vous souhaitez vous connecter à la machine virtuelle par le biais d’Internet, configurez sur la machine virtuelle un point de terminaison HTTP qui mappe le port public 80 sur le port privé 8080. Par exemple, dans un déploiement de gestion des services Azure, exécutez la commande CLI Azure :

```
$ azure vm endpoint create <machine-name> 80 8080

```

L’écran de démarrage de WordPress, vous permettant de terminer l’installation et de prendre en main l’application, doit maintenant s’afficher.

![Écran d’accueil WordPress][wordpress_start]


## Étapes suivantes

* Consultez la page [Compose CLI reference](http://docs.docker.com/compose/cli/) et le [guide d’utilisation](http://docs.docker.com/compose/) pour plus d’exemples sur le développement et le déploiement d’applications à conteneurs multiples.
* Utilisez un modèle Microsoft Azure Manager, le vôtre ou un élément de la [communauté](http://azure.microsoft.com/documentation/templates/), pour déployer une machine virtuelle Microsoft Azure avec Docker et une application configurée avec Compose. Par exemple, le modèle [Deploy a WordPress blog with Docker](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/) (en anglais) utilise Docker et Compose pour déployer rapidement WordPress avec un serveur principal MySQL sur une machine virtuelle Ubuntu.
* Essayez d’intégrer Docker Compose à un cluster [Docker Swarm](virtual-machines-docker-swarm.md). Pour plus de scénarios, consultez la page
[Intégration Docker Compose/Swarm](https://github.com/docker/compose/blob/master/SWARM.md).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=Oct15_HO2-->