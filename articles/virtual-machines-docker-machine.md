<properties
   pageTitle="Comment utiliser docker-machine avec Azure"
   description="Montre comment être opérationnel sur Windows Azure avec Docker Machine sur Ubuntu."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/25/2015"
   ms.author="rasquill"/>

# Comment utiliser docker-machine avec Azure

Cette rubrique décrit l’utilisation de [Docker](https://www.docker.com/) avec [Machine](https://github.com/docker/machine) et l’[interface de ligne de commande Microsoft Azure](https://github.com/Azure/azure-xplat-cli) pour créer une machine virtuelle Microsoft Azure vous permettant de gérer rapidement et en toute simplicité les conteneurs Linux à partir d’un ordinateur exécutant Ubuntu. À fin d’illustration, le didacticiel montre comment déployer l’[image Docker Hub busybox ](https://registry.hub.docker.com/_/busybox/) et l’[image Docker Hub nginx](https://registry.hub.docker.com/_/nginx/) et configurer le conteneur pour acheminer les requêtes Web vers le conteneur nginx. (La documentation Docker **Machine** vous explique comment modifier ces instructions sur d’autres plateformes.)

Il existe certaines conditions préalables pour suivre ce didacticiel. Vous devez installer les éléments suivants :

1. [npm](https://docs.npmjs.com/) et [Node.js](http://nodejs.org/)
2. [L’interface de ligne de commande Microsoft Azure](https://github.com/Azure/azure-xplat-cli)
3. Un [client Docker](https://docs.docker.com/installation/)

Si vous installez ces éléments dans cet ordre, votre ordinateur Ubuntu sera prêt pour le didacticiel. (Ce didacticiel doit être en grande partie identique pour n'importe quelle autre distribution basée sur dpkg telle que Debian. N'hésitez pas à laisser un commentaire si vous découvrez des exigences ou des étapes supplémentaires.)

## Obtenir docker-machine ou le générer

La façon la plus rapide de commencer à utiliser **docker-machine** consiste à télécharger la version appropriée directement à partir du [partage de versions](https://github.com/docker/machine/releases). L’ordinateur client utilisé pour l’élaboration de ce didacticiel était un ordinateur x64 exécutant Ubuntu ; c’est donc l’image **docker-machine_linux-amd64** qui est utilisée.

Vous pouvez également créer votre **docker-machine** vous-même en suivant les étapes pour [contribuer à Machine](https://github.com/docker/machine#contributing). Vous devrez peut-être télécharger 1 Go de matériau, si ce n'est plus, pour effectuer la génération, mais en procédant ainsi, vous pouvez personnaliser votre expérience comme vous le souhaitez.

> [AZURE.NOTE]Vous pouvez également créer un [lien symbolique](http://en.wikipedia.org/wiki/Symbolic_link) à votre version de plateforme correspondante, mais ce didacticiel utilise le fichier binaire directement pour illustrer le comportement très clairement. En conséquence, à la place de commandes telles que `docker-machine env` comme l’illustre la documentation **docker-machine**, ce didacticiel utilise `docker-machine_linux-amd64 env`. Le choix de créer un lien symbolique ou d'utiliser simplement le nom du fichier binaire vous appartient, mais si vous modifiez le nom que vous utilisez, n'oubliez pas de modifier le nom dans les instructions ci-dessous.

<br />

>  Quelle que soit la méthode choisie, vous devez appeler le fichier binaire directement sur la ligne de commande ou l'intégrer au chemin d'accès, par exemple **/usr/local/bin**. Assurez-vous qu’il est marqué comme exécutable en tapant `chmod +x` &lt;*`binaryName`*&gt; où &lt;*`binaryName`*&gt; est le nom de votre exécutable Docker Machine. Ce didacticiel utilise **docker-machine_linux-amd64**.

## Créer les fichiers de certificat et de clé pour docker, machine et Azure

Vous devez maintenant créer les fichiers de certificat et de clé dont Microsoft Azure a besoin pour vérifier vos identité et autorisations, ainsi que ceux dont se sert **docker-machine** pour communiquer avec votre machine virtuelle Microsoft Azure afin de créer et de gérer des conteneurs à distance. Si vous avez déjà ces fichiers dans un répertoire, peut-être pour une utilisation avec docker, vous pouvez les réutiliser. Toutefois, la méthode recommandée pour tester **docker-machine** consiste à les créer dans un répertoire séparé et à faire pointer Docker Machine vers ces fichiers.

> [AZURE.NOTE]Si vous êtes amené à essayer **docker-machine** à maintes reprises, veillez à réutiliser les mêmes fichiers de certificat et clé. **docker-machine** crée un ensemble de certificats clients ; tout ce qu’il crée peut être examiné dans `~/.docker/machine`. Si vous déplacez ces certificats vers un autre ordinateur, vous devez également déplacer les dossiers de certificats **docker-machine**. Cela a son importance si vous prévoyez d’utiliser **docker-machine** sur une autre plateforme, juste pour voir comme tout cela fonctionne.

Si vous connaissez les distributions Linux, ces fichiers sont peut-être déjà disponibles quelque part sur votre ordinateur en vue de leur utilisation ; la documentation [Docker HTTPS documentation explique bien ces étapes](https://docs.docker.com/articles/https/). Toutefois, voici la forme la plus simple de cette étape.

1. Créez un dossier local puis, sur la ligne de commande, accédez à ce dossier et tapez :

		openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
		openssl pkcs12 -export -out mycert.pfx -in mycert.pem -name "My Certificate"

	Tenez-vous prêt à saisir le mot de passe d’exportation pour votre certificat et à le capturer pour une utilisation ultérieure. Tapez ensuite :

		openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

2. Téléchargez le fichier .cer de votre certificat sur Azure. Dans le [portail Azure](https://manage.windowsazure.com), cliquez sur **Paramètres** dans la partie inférieure gauche de la zone de service (voir ci-dessous)

	![][portalsettingsitem]

	et cliquez sur **Certificats de gestion** :

	![][managementcertificatesitem]

	et sur **Télécharger** (en bas de la page) ![][uploaditem] pour télécharger le fichier **mycert.cer** créé à l’étape précédente.

3. Dans le même volet **Paramètres** du portail, cliquez sur **Abonnements** et capturez l’ID d’abonnement à utiliser pendant la création de votre machine virtuelle, car vous vous en servirez à l’étape suivante. (Vous pouvez également rechercher l’ID d’abonnement depuis la ligne de commande à l’aide de la commande de l’interface de ligne de commande Microsoft Azure `azure account list`, qui affiche l’ID d’abonnement de chaque abonnement disponible dans le compte.)

4. Créez une machine virtuelle d’hôte Docker dans Microsoft Azure à l’aide de la commande **docker-machine create**. La commande nécessite l’ID d’abonnement capturé à l’étape précédente et le chemin d’accès du fichier **.pem** créé à l’étape 1. Cette rubrique utilise « machine-name » comme nom de service cloud Azure (et nom de votre machine virtuelle), mais vous devez le remplacer par le nom de votre choix, et pensez à utiliser le nom de votre service cloud dans toute autre étape qui nécessite le nom de la machine virtuelle. (N’oubliez pas que dans cet exemple, nous utilisons le nom complet du fichier binaire et non un lien symbolique **docker-machine**.)

		docker-machine_linux-amd64 create \
	    -d azure \
	    --azure-subscription-id="<subscription ID acquired above>" \
	    --azure-subscription-cert="mycert.pem" \
	    machine-name

	Comme les deux premières étapes nécessitent la création d'une machine virtuelle et le chargement de l'agent Linux Azure, ainsi que la mise à jour de la nouvelle machine virtuelle, vous devez obtenir quelque chose comme ceci.

		INFO[0001] Creating Azure machine...
	    INFO[0049] Waiting for SSH...
	    modprobe: FATAL: Module aufs not found.
	    + sudo -E sh -c sleep 3; apt-get update
	    + sudo -E sh -c sleep 3; apt-get install -y -q linux-image-extra-3.13.0-36-generic
	    E: Unable to correct problems, you have held broken packages.
	    modprobe: FATAL: Module aufs not found.
	    Warning: tried to install linux-image-extra-3.13.0-36-generic (for AUFS)
	     but we still have no AUFS.  Docker may not work. Proceeding anyways!
	    + sleep 10
	    + [ https://get.docker.com/ = https://get.docker.com/ ]
	    + sudo -E sh -c apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	    gpg: requesting key A88D21E9 from hkp server keyserver.ubuntu.com
	    gpg: key A88D21E9: public key "Docker Release Tool (releasedocker) <docker@dotcloud.com>" imported
	    gpg: Total number processed: 1
	    gpg:               imported: 1  (RSA: 1)
	    + sudo -E sh -c echo deb https://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list
	    + sudo -E sh -c sleep 3; apt-get update; apt-get install -y -q lxc-docker
	    + sudo -E sh -c docker version
	    INFO[0368] "machine-name" has been created and is now the active machine.
	    INFO[0368] To point your Docker client at it, run this in your shell: $(docker-machine_linux-amd64 env machine-name)

    > [AZURE.NOTE]La machine virtuelle étant en cours de création, quelques minutes peuvent s'écouler avant qu'elle ne soit prête. En attendant, vous pouvez vérifier l’état de votre nouvel hôte Docker en tapant `azure vm list` à l’aide de l’interface de ligne de commande Microsoft Azure, jusqu’à ce que vos machines virtuelles présentent l’état **ReadyRole**.

5. Définissez les variables d'environnement machine et docker pour la session terminal. La dernière ligne de commentaires vous propose d'exécuter immédiatement la commande **env** afin d'exporter les variables d'environnement nécessaires pour utiliser votre client Docker directement avec une machine spécifique.

		$(docker-machine_linux-amd64 env machine-name)

	Une fois cette opération effectuée, vous n'avez plus besoin de commandes spéciales pour utiliser votre client Docker local afin de vous connecter à la machine virtuelle créée par Docker **Machine**.

	    $ docker info
	    Containers: 0
	    Images: 0
	    Storage Driver: devicemapper
	     Pool Name: docker-8:1-131736-pool
	     Pool Blocksize: 65.54 kB
	     Backing Filesystem: extfs
	     Data file: /dev/loop0
	     Metadata file: /dev/loop1
	     Data Space Used: 305.7 MB
	     Data Space Total: 107.4 GB
	     Metadata Space Used: 729.1 kB
	     Metadata Space Total: 2.147 GB
	     Udev Sync Supported: false
	     Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	     Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	     Library Version: 1.02.82-git (2013-10-04)
	    Execution Driver: native-0.2
	    Kernel Version: 3.13.0-36-generic
	    Operating System: Ubuntu 14.04.1 LTS
	    CPUs: 1
	    Total Memory: 1.639 GiB
	    Name: machine-name
	    ID: W3FZ:BCZW:UX24:GDSV:FR4N:N3JW:XOC2:RI56:IWQX:LRTZ:3G4P:6KJK
	    WARNING: No swap limit support

> [AZURE.NOTE]Ce didacticiel montre comment **docker-machine** crée une machine virtuelle. Toutefois, vous pouvez répéter la procédure pour créer autant de machines que vous le souhaitez. Si vous procédez ainsi, la meilleure façon de basculer entre les machines virtuelles avec Docker consiste à utiliser la commande **env** en ligne pour définir les variables d'environnement **docker** pour chacune des commandes. Par exemple, pour utiliser **docker info** avec une machine virtuelle différente, vous pouvez taper`docker $(docker-machine env <VM name>) info`, et la commande **env** remplit les informations de connexion Docker à utiliser avec cette machine virtuelle.

## Nous avons terminé. Exécutons certaines applications à distance à l'aide de docker et d'images à partir du Docker Hub.

Vous pouvez maintenant utiliser docker normalement pour créer une application dans le conteneur. L'application la plus simple à illustrer est [busybox](https://registry.hub.docker.com/_/busybox/) :

	    $  docker run busybox echo hello world
	    Unable to find image 'busybox:latest' locally
	    511136ea3c5a: Pull complete
	    df7546f9f060: Pull complete
	    ea13149945cb: Pull complete
	    4986bf8c1536: Pull complete
	    busybox:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
	    Status: Downloaded newer image for busybox:latest
	    hello world

Toutefois, vous souhaiterez peut-être créer une application que vous pouvez voir immédiatement sur Internet, telle que [nginx](https://registry.hub.docker.com/_/nginx/) à partir du [Docker Hub](https://registry.hub.docker.com/).

> [AZURE.NOTE]Pensez à utiliser l'option **-P** pour que **docker** affecte des ports aléatoires à l'image, et l'option **-d** pour que le conteneur s'exécute en continu en arrière-plan. (Si vous oubliez, nginx s'arrêtera, dès qu'il aura démarré. Pensez-y !)

	$ docker run --name machinenginx -P -d nginx
    Unable to find image 'nginx:latest' locally
    30d39e59ffe2: Pull complete
    c90d655b99b2: Pull complete
    d9ee0b8eeda7: Pull complete
    3225d58a895a: Pull complete
    224fea58b6cc: Pull complete
    ef9d79968cc6: Pull complete
    f22d05624ebc: Pull complete
    117696d1464e: Pull complete
    2ebe3e67fb76: Pull complete
    ad82b43d6595: Pull complete
    e90c322c3a1c: Pull complete
    4b5657a3d162: Pull complete
    511136ea3c5a: Already exists
    nginx:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for nginx:latest
    5883e2ff55a4ba0aa55c5c9179cebb590ad86539ea1d4d367d83dc98a4976848

Pour voir l'application depuis internet, créez un point de terminaison public sur le port 80 pour la machine virtuelle Azure et mappez ce port au port du conteneur nginx. Tout d'abord, utilisez `docker ps -a` pour localiser le conteneur et rechercher les ports que **docker** a affectés au port 80 du conteneur. (Vous verrez toute une série d'informations, mais nous ne montrons ci-dessous que celles relatives au port.)

	$ docker ps -a
    IMAGE               PORTS
    nginx:latest        0.0.0.0:49153->80/tcp, 0.0.0.0:49154->443/tcp
    busybox:latest

Nous pouvons voir que docker a affecté le port 80 du conteneur au port 49153 de la machine virtuelle. Nous pouvons à présent utiliser l'interface de ligne de commande Microsoft Azure pour mapper le port public 80 du service cloud externe sur le port 49153 de la machine virtuelle. Docker vérifie ensuite que le trafic tcp entrant sur le port 49153 de la machine virtuelle est acheminé vers le conteneur nginx.

	$ azure vm endpoint create machine-name 80 49153
    info:    Executing command vm endpoint create
    + Getting virtual machines
    + Reading network configuration
    + Updating network configuration
    info:    vm endpoint create command OK

Ouvrez votre navigateur préféré et jetez un coup d'œil.

![][nginx]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
Accédez au [guide de l’utilisateur Docker](https://docs.docker.com/userguide/) et créez des applications sur Microsoft Azure. Sinon, expérimentez [**docker** et swarm](https://github.com/docker/swarm) sur Microsoft Azure](virtual-machines-docker-swarm) et découvrez comment swarm peut être utilisé avec docker et Microsoft Azure.

<!--Image references-->
[nginx]: ./media/virtual-machines-docker-machine/nginxondocker.png
[portalsettingsitem]: ./media/virtual-machines-docker-machine/portalsettingsitem.png
[managementcertificatesitem]: ./media/virtual-machines-docker-machine/managementcertificatesitem.png
[uploaditem]: ./media/virtual-machines-docker-machine/uploaditem.png

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=58-->