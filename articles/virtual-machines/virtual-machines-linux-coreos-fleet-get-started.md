<properties
	pageTitle="Prise en main de fleet avec CoreOS sur Azure"
	description="Fournit des exemples simples d’utilisation de fleet et de Docker avec une machine virtuelle CoreOS Linux sur Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>


<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="08/03/2015"
	ms.author="danlep"/>


# Prise en main de fleet avec CoreOS sur Azure

Cet article fournit deux courts exemples de l’utilisation de [fleet](https://github.com/coreos/fleet) et de [Docker](https://www.docker.com/) l’exécution d’applications sur un cluster de machines virtuelles [CoreOS].

Pour ces exemples, commencez par paramétrer un cluster CoreOS exécutant trois nœuds, comme indiqué dans [Utilisation de CoreOS sur Azure]. Cela vous permettra de comprendre les éléments de base des déploiements CoreOS et de disposer d’un ordinateur client et d’un cluster en état de marche. Nous allons utiliser le même nom de cluster dans ces exemples. En outre, les exemples suivants partent du principe que vous utilisez votre hôte Linux local pour exécuter les commandes **fleetctl**.




## <a id='simple'>Exemple 1 : Hello World avec Docker</a>

Voici une application « Hello World » simple qui s’exécute dans un conteneur Docker unique. Cet exemple utilise l’[image Docker Hub busybox].

Sur votre ordinateur client Linux, créez le fichier d’unité **systemd** suivant à l’aide d’un éditeur de texte, puis nommez-le `helloworld.service`. Pour plus d’informations sur la syntaxe, consultez l’article [Fichiers d’unité] (en anglais).

```
[Unit]
Description=HelloWorld
After=docker.service
Requires=docker.service

[Service]

TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

```

Connectez-vous à présent au cluster CoreOS et démarrez l’unité en exécutant la commande **fleetctl** suivante. La sortie indique que l’unité est démarrée et précise son emplacement.


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE]Pour exécuter vos commandes **fleetctl** sans le paramètre **--tunnel**, vous pouvez définir la variable d’environnement FLEETCTL\_TUNNEL afin de transférer les demandes. Par exemple : `export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`.


Vous pouvez vous connecter au conteneur pour afficher la sortie du service :

```
fleetctl --tunnel coreos-cloudapp.cluster.net:22 journal helloworld.service

Mar 04 21:29:26 node-1 docker[57876]: Hello World
Mar 04 21:29:27 node-1 docker[57876]: Hello World
Mar 04 21:29:28 node-1 docker[57876]: Hello World
Mar 04 21:29:29 node-1 docker[57876]: Hello World
Mar 04 21:29:30 node-1 docker[57876]: Hello World
Mar 04 21:29:31 node-1 docker[57876]: Hello World
Mar 04 21:29:32 node-1 docker[57876]: Hello World
Mar 04 21:29:33 node-1 docker[57876]: Hello World
Mar 04 21:29:34 node-1 docker[57876]: Hello World
Mar 04 21:29:35 node-1 docker[57876]: Hello World
```

Pour nettoyer, arrêter et décharger l’unité :

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## <a id='highavail'>Exemple 2 : serveur Apache hautement disponible</a>

L’utilisation de CoreOS, de Docker et de **fleet** facilite l’exécution des services en mode haute disponibilité. Dans cet exemple, vous déployez un service qui se compose de trois conteneurs identiques exécutant le serveur Web Apache. Les conteneurs s’exécutent sur les trois machines virtuelles du cluster. Cet exemple ressemble à l’exemple présenté sur la page [Lancement de conteneurs avec fleet] (en anglais) et utilise l’[image CoreOS Apache Docker Hub].

>[AZURE.IMPORTANT]Pour exécuter le serveur Apache hautement disponible, vous devez configurer un point de terminaison HTTP avec équilibrage de charge sur les machines virtuelles (port public 80, port privé 80). Vous pouvez le faire après la création du cluster CoreOS à l’aide du portail Azure ou de la commande **azure vm endpoint**. Consultez [Configuration d’un équilibrage de charge] pour plus d’informations.

Sur votre ordinateur client, créez un fichier d’unité modèle **systemd** à l’aide d’un éditeur de texte, puis nommez-le apache@.service. Vous utiliserez ce modèle pour ouvrir trois instances distinctes, nommés apache@1.service, apache@2.service et apache@3.service :

```
[Unit]
Description=High Availability Apache
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache1
ExecStartPre=-/usr/bin/docker rm apache1
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run -rm --name apache1 -p 80:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStop=/usr/bin/docker stop apache1

[X-Fleet]
X-Conflicts=apache@*.service
```

>[AZURE.NOTE]L’attribut `X-Conflicts` indique à CoreOS qu’une seule instance de ce conteneur peut être exécutée sur un hôte CoreOS donné. Pour plus d’informations, consultez l’article [Fichiers d’unité] (en anglais).

Ouvrez à présent les instances de l’unité sur le cluster CoreOS. Normalement, elles doivent s’exécuter sur trois ordinateurs distincts :

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start apache@{1,2,3}.service

unit apache@3.service launched on 00c927e4.../100.79.62.16
unit apache@1.\service launched on 62f0f66e.../100.79.86.62
unit apache@2.service launched on df85f2d1.../100.78.126.15

```
Pour atteindre le serveur Apache en cours d’exécution sur l’une des unités, envoyez une demande simple au service cloud qui héberge le cluster CoreOS.

`curl http://coreos-cluster.cloudapp.net`

Le message par défaut renvoyé à partir du serveur Apache, s’affiche. Il ressemble au message suivant :

```
<html><body><h1>It works!</h1>
<p>This is the default web page for this server.</p>
<p>The web server software is running but no content has been added, yet.</p>
</body></html>
```

Vous pouvez essayer d’arrêter une ou plusieurs machines virtuelles de votre cluster afin de vérifier que le service Apache continue à s’exécuter.

Lorsque vous avez terminé, arrêtez et déchargez les unités.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop apache@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload apache@{1,2,3}.service

```

## Étapes suivantes

* Vous pouvez essayer d’exploiter davantage votre cluster CoreOS à trois nœuds sur Azure. Apprenez à créer des clusters plus complexes, à utiliser Docker et à créer d’autres applications intéressantes en lisant le [didacticiel CoreOS de Tim Park], le [didacticiel CoreOS de Patrick Chanezon], la documentation [Docker] et la [présentation de CoreOS].

* Pour démarrer avec Fleet et CoreOS dans Azure Resource Manager, essayez ce [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/).

* Pour apprendre à utiliser les environnements open-source sur les machines virtuelles Linux dans Azure, consultez [Linux et informatique open-source sur Azure] .

<!--Link references-->
[Azure Command-Line Interface (Azure)]: ../xplat-cli.md
[CoreOS]: https://coreos.com/
[présentation de CoreOS]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[didacticiel CoreOS de Tim Park]: https://github.com/timfpark/coreos-azure
[didacticiel CoreOS de Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Utilisation de CoreOS sur Azure]: virtual-machines-linux-coreos-how-to.md
[Configuration d’un équilibrage de charge]: ../load-balancer/load-balancer-internet-getstarted.md
[Lancement de conteneurs avec fleet]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[Fichiers d’unité]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[image Docker Hub busybox]: https://registry.hub.docker.com/_/busybox/
[image CoreOS Apache Docker Hub]: https://registry.hub.docker.com/u/coreos/apache/
[Linux et informatique open-source sur Azure]: virtual-machines-linux-opensource.md

<!---HONumber=August15_HO6-->