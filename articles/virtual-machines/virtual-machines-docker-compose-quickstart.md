<properties
   pageTitle="Prise en main de Docker et Compose sur Microsoft Azure Virtual Machines"
   description="Brève introduction de l’utilisation de Compose et Docker sur Microsoft Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/07/2015"
   ms.author="danlep"/>

# Prise en main de Docker et Compose sur une machine virtuelle Microsoft Azure


Vous pouvez vous familiariser rapidement avec [Compose](http://github.com/docker/compose) (le successeur de *Fig*) afin de définir et exécuter des applications complexes avec Docker sur une machine virtuelle Linux dans Microsoft Azure. Grâce à Compose, vous définissez une application à conteneurs multiples dans un fichier unique, puis lancez votre application avec une seule commande permettant l’exécution sur la machine virtuelle.




## Créer une machine virtuelle Microsoft Azure avec l’extension de machine virtuelle Docker et installer Compose

Votre magasin en ligne Microsoft Azure Marketplace comporte différentes procédures Microsoft Azure et des images décrivant la création d’une machine virtuelle Microsoft Azure et l’installation de Docker et Compose sur cette dernière. Par exemple, la page [Utilisation de l’extension de machine virtuelle Docker à partir de l’interface de ligne de commande Microsoft Azure](virtual-machines-docker-with-xplat-cli) contient une procédure rapide relative à la création d’une machine virtuelle Ubuntu avec l’extension de machine virtuelle Ubuntu à l’aide de l’interface de ligne de commande Microsoft Azure pour Mac, Linux et Windows. L’exemple de cet article utilise l’interface de ligne de commande en mode de gestion des services (Azure Service Management, **asm**).


Une fois que la machine virtuelle Ubuntu est exécutée avec Docker, connectez-vous à l’aide de SSH, puis installez [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) en lançant deux commandes :

```
curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Si une erreur d’autorisation refusée est générée, cela signifie que votre répertoire /usr/local/bin n’est pas modifiable et que vous devez installer Compose en tant que superutilisateur. Exécutez `sudo -i`, puis les deux commandes ci-dessus, et enfin `exit`.

Pour tester votre installation de Compose, exécutez la commande

```
docker-compose --version
```

Vous verrez une sortie semblable à ```
docker-compose 1.2.0
```.


## Créer un fichier de configuration docker-compose.ym

Compose utilise un fichier texte de configuration appelé `docker-compose.yml` pour définir les conteneurs qui s’exécuteront sur la machine virtuelle. Le fichier indique l’image à exécuter sur chaque conteneur (il peut également s’agir d’un build d’un fichier Dockerfile), les variables d’environnement et les dépendances nécessaires, les ports, les liens entre les conteneurs, etc. Pour plus d’informations sur la syntaxe du fichier yml, consultez la page de [référence docker-compose.yml](http://docs.docker.com/compose/yml/).

Créez un répertoire de travail sur votre machine virtuelle et utilisez votre éditeur de texte préféré pour créer le fichier `docker-compose.yml`. Pour tester un exemple simple, copiez le texte suivant dans le fichier. Cette configuration installe WordPress (le système open source de gestion de blogs et de contenu) et une base de données principale associée SQL MariaDB utilisant des images du [ registre DockerHub](https://registry.hub.docker.com/_/wordpress/).

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL_ROOT_PASSWORD: <your password>

```

## Start the containers with Compose

In your working directory simply run

```
docker-compose up -d

```

to start the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1... Creating wordpress_wordpress_1... ```

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

Vous pouvez maintenant vous connecter directement à WordPress sur la machine virtuelle, en accédant à `http://localhost:8080`. Si vous souhaitez vous connecter à la machine virtuelle par le biais d’Internet, configurez sur la machine virtuelle un point de terminaison HTTP qui mappe le port public 80 sur le port privé 8080. Par exemple, exécutez la commande d’interface de ligne de commande Microsoft Azure suivante :

```
azure vm endpoint create <machine-name> 80 8080

```

L’écran de démarrage de WordPress, vous permettant de terminer l’installation, doit maintenant s’afficher.

![Écran d’accueil WordPress][wordpress_start]




## Étapes suivantes

* Consultez la [référence sur les commandes Compose](http://docs.docker.com/compose/cli/) et le [guide d’utilisation](http://docs.docker.com/compose/) pour plus d’exemples sur le développement et le déploiement d’applications à conteneurs multiples.
* Essayez d’intégrer Docker Compose à un cluster [Docker Swarm](virtual-machines-docker-swarm.md). Pour plus de scénarios, consultez la page [Intégration Docker Compose/Swarm](https://github.com/docker/compose/blob/master/SWARM.md).
* Utilisez un modèle Microsoft Azure Manager, le vôtre ou un élément de la [communauté](http://azure.microsoft.com/documentation/templates/), pour déployer une machine virtuelle Microsoft Azure avec Docker et une application configurée avec Compose. Par exemple, le [modèle de machine virtuelle Ubuntu avec Docker et trois conteneurs](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/) vous permet de déployer rapidement trois services à partir d’images du [registre DockerHub](https://registry.hub.docker.com/).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png
 

<!---HONumber=July15_HO2-->