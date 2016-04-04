
<properties
   pageTitle="Prise en main de docker avec swarm sur Azure"
   description="Explique comment créer un groupe de machines virtuelles avec l'extension de machine virtuelle Docker et comment créer un cluster Docker avec swarm."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# Comment utiliser docker avec swarm

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Cette rubrique montre un moyen très simple d’utiliser [docker](https://www.docker.com/) avec [swarm](https://github.com/docker/swarm) pour créer un cluster géré par swarm dans Microsoft Azure. Quatre machines virtuelles sont créées dans Azure, une en tant que gestionnaire d'essaim et trois dans le cadre du cluster d'hôtes docker. Quand vous avez terminé, vous pouvez utiliser swarm pour voir le cluster, puis commencer à utiliser docker sur celui-ci. Par ailleurs, les appels de l’interface de ligne de commande Microsoft Azure de cette rubrique utilisent le mode de gestion de services Azure (Azure Service Management, asm).

> [AZURE.NOTE] Cette rubrique utilise docker avec swarm et l'interface de ligne de commande Microsoft Azure *sans* utiliser **docker-machine** pour montrer comment les différents outils fonctionnent ensemble tout en demeurant indépendants. **docker-machine** possède des commutateurs **--swarm** qui vous permettent d'utiliser **docker-machine** pour ajouter directement des nœuds à un essaim. Pour obtenir un exemple, consultez la documentation [docker-machine](https://github.com/docker/machine). Si l’exécution de **docker-machine** sur des machines virtuelles Microsoft Azure ne vous est pas familière, consultez la page [Utilisation de docker-machine avec Azure](virtual-machines-linux-classic-docker-machine.md).

## Créer des hôtes docker avec des machines virtuelles Azure

Cette rubrique crée quatre machines virtuelles, mais vous pouvez utiliser le nombre de votre choix. Exécutez la commande suivante en remplaçant *&lt;password&gt;* par le mot de passe que vous avez choisi.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Quand vous avez terminé, vous devez être en mesure d’utiliser **azure vm list** pour voir vos machines virtuelles Microsoft Azure :

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## Installation de swarm dans la machine virtuelle maître swarm

Cette rubrique utilise le [modèle de conteneur d’installation de la documentation docker swarm](https://github.com/docker/swarm#1---docker-image), mais vous pouvez aussi utiliser SSH pour établir une connexion à la machine **swarm-master**. Dans ce modèle, **swarm** est téléchargé en tant que conteneur docker exécutant swarm. Ci-dessous, nous effectuons cette étape *à distance à partir de notre ordinateur portable à l’aide de docker* pour nous connecter à la machine virtuelle **swarm-master** et lui indiquer d’utiliser la commande de création d’ID de cluster, **swarm create**. L'ID de cluster détermine la façon dont **swarm** détecte les membres du groupe swarm. (Vous pouvez également cloner le référentiel et le générer vous-même, ce qui vous donne un contrôle total et vous permet d'effectuer des opérations de débogage.)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

La dernière ligne est l'id de cluster ; copiez-le quelque part, car vous le réutiliserez quand vous joindrez les machines virtuelles de nœud à la machine maître swarm pour créer « l'essaim ». Dans cet exemple, l’ID de cluster est **36731c17189fd8f450c395db8437befd**.

> [AZURE.NOTE] Pour dissiper tout doute, nous utilisons notre installation docker locale pour nous connecter à la machine virtuelle **swarm-master** dans Microsoft Azure et l'instruction **swarm-master** pour télécharger, installer et exécuter la commande **create**, qui retourne l'ID de cluster que nous utilisons ultérieurement à des fins de découverte.
<!-- -->
> Pour vérifier cela, exécutez `docker -H tcp://`*&lt;hostname&gt;*` images` pour répertorier les processus conteneur sur la machine **swarm-master** et sur un autre nœud à des fins de comparaison (comme nous avons exécuté la commande précédente swarm avec le commutateur **--rm**, le conteneur a été supprimé à la fin de la commande et l’utilisation de la commande **docker ps -a** ne renvoie rien) :


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
> Si vous êtes familiarisé avec **docker**, vous savez que les autres nœuds ne possèdent aucune entrée, car aucune image n'a été téléchargée et exécutée pour le moment.

## Joindre les machines virtuelles de nœud à notre cluster docker

Pour chaque nœud, répertoriez les informations sur les points de terminaison à l'aide de l'interface de ligne de commande Microsoft Azure. Ci-dessous, nous effectuons cela pour l’hôte docker **swarm-node-1** afin d’obtenir le port docker du nœud.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


À l’aide de **docker** et de l’option `-H` pour faire pointer le client docker sur votre machine virtuelle de nœud, joignez ce nœud à l’essaim en cours de création en passant l’ID de cluster et le port docker du nœud (ce dernier en utilisant **--addr**) :

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Cela semble correct. Pour vérifier que **swarm** s’exécute sur **swarm-node-1**, nous tapons :

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Répétez la procédure pour tous les autres nœuds du cluster. Dans notre cas, nous l’effectuons pour **swarm-node-2** et **swarm-node-3**.

## Commencez à gérer le cluster swarm

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

puis répertoriez les nœuds du cluster :

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Expérimentez votre essaim. Si vous êtes en manque d’inspiration, consultez la page [https://github.com/docker/swarm/](https://github.com/docker/swarm/) ou, si besoin, une [vidéo](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-linux-classic-docker-machine.md
 

<!---HONumber=AcomDC_0323_2016-->