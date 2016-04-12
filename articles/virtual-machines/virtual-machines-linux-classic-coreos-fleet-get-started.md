<properties
	pageTitle="Prise en main de Fleet avec CoreOS | Microsoft Azure"
	description="Fournit des exemples simples d’utilisation de Fleet et de Docker avec un cluster de machine virtuelle CoreOS Linux créé avec le modèle de déploiement classique sur Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/09/2015"
	ms.author="danlep"/>

# Prise en main de fleet avec un cluster de machine virtuelle CoreOS sur Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://github.com/Azure/azure-quickstart-templates/tree/master/coreos-with-fleet-multivm).


Voici deux courts exemples de l’utilisation de [fleet](https://github.com/coreos/fleet) et de [Docker](https://www.docker.com/) pour l’exécution d’applications sur un cluster de machines virtuelles [CoreOS] dans Azure.

Pour ces exemples, commencez par paramétrer un cluster CoreOS exécutant trois nœuds, comme indiqué dans [Utilisation de CoreOS sur Azure]. Cela vous permettra de comprendre les éléments de base des déploiements CoreOS et de disposer d’un ordinateur client et d’un cluster en état de marche. Nous allons utiliser le même nom de cluster dans ces exemples. En outre, les exemples suivants partent du principe que vous utilisez un hôte Linux local pour exécuter les commandes **fleetctl**. Voir [Utilisation du client](https://coreos.com/fleet/docs/latest/using-the-client.html) pour en savoir plus sur le client **fleetctl**.


## Exemple 1 : Hello World avec Docker</a>

Voici une application « Hello World » simple qui s’exécute dans un conteneur Docker unique. Cet exemple utilise l’[image Docker Hub busybox].

Sur votre ordinateur client Linux, créez le fichier d’unité **systemd** suivant à l’aide d’un éditeur de texte, puis nommez-le `helloworld.service`. Pour plus d’informations sur la syntaxe, consultez l’article [Fichiers d’unité] (en anglais).

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

Connectez-vous à présent au cluster CoreOS et démarrez l’unité en exécutant la commande **fleetctl** suivante. La sortie indique que l’unité est démarrée et précise son emplacement.


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE] Pour exécuter vos commandes **fleetctl** sans le paramètre **--tunnel**, vous pouvez définir la variable d’environnement FLEETCTL\_TUNNEL afin de transférer les demandes. Par exemple : `export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`.


Vous pouvez vous connecter au conteneur pour afficher la sortie du service :

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

Pour nettoyer, arrêter et décharger l’unité :

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## Exemple 2 : serveur nginx hautement disponible</a>

L’utilisation de CoreOS, de Docker et de **fleet** facilite l’exécution des services en mode haute disponibilité. Dans cet exemple, vous déployez un service qui se compose de trois conteneurs identiques exécutant le serveur Web nginx. Les conteneurs s’exécutent sur les trois machines virtuelles du cluster. Cet exemple ressemble à l’exemple présenté sur la page [Lancement de conteneurs avec fleet] (en anglais) et utilise l’[image nginx Docker Hub].

>[AZURE.IMPORTANT] Pour exécuter le serveur Web hautement disponible, vous devez configurer un point de terminaison HTTP avec équilibrage de charge sur les machines virtuelles (port public 80, port privé 80). Vous pouvez le faire après la création du cluster CoreOS à l’aide du portail Azure Classic ou de la commande **azure vm endpoint**. Pour découvrir les étapes à suivre, consultez [Configurer un jeu d’équilibrage de la charge].

Sur votre ordinateur client, créez un fichier d’unité modèle **systemd** à l’aide d’un éditeur de texte, puis nommez-le nginx@.service. Utilisez ce modèle simple pour ouvrir trois instances distinctes, nommées nginx@1.service, nginx@2.service et nginx@3.service :

```
[Unit]
Description=High Availability Nginx
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=/usr/bin/docker pull nginx
ExecStart=/usr/bin/docker run --rm --name nginx1 -p 80:80 nginx
ExecStop=/usr/bin/docker stop nginx1

[X-Fleet]
X-Conflicts=nginx@*.service
```

>[AZURE.NOTE] L’attribut `X-Conflicts` indique à CoreOS qu’une seule instance de ce conteneur peut être exécutée sur un hôte CoreOS donné. Pour plus d’informations, consultez l’article [Fichiers d’unité] (en anglais).

Ouvrez à présent les instances de l’unité sur le cluster CoreOS. Normalement, elles doivent s’exécuter sur trois ordinateurs distincts :

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start nginx@{1,2,3}.service

unit nginx@3.service launched on 00c927e4.../100.79.62.16
unit nginx@1.service launched on 62f0f66e.../100.79.86.62
unit nginx@2.service launched on df85f2d1.../100.78.126.15

```
Pour atteindre le serveur Web en cours d’exécution sur l’une des unités, envoyez une demande simple au service cloud qui héberge le cluster CoreOS.

`curl http://coreos-cluster.cloudapp.net`

Le message par défaut renvoyé à partir du serveur nginx s’affiche. Il ressemble au message suivant :

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

Vous pouvez essayer d’arrêter une ou plusieurs machines virtuelles de votre cluster afin de vérifier que le service Web continue à s’exécuter.

Lorsque vous avez terminé, arrêtez et déchargez les unités.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop nginx@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload nginx@{1,2,3}.service

```

## Étapes suivantes

* Vous pouvez essayer d’exploiter davantage votre cluster CoreOS à trois nœuds sur Azure. Apprenez à créer des clusters plus complexes, à utiliser Docker et à créer d’autres applications intéressantes en lisant le [didacticiel CoreOS de Tim Park], le [didacticiel CoreOS de Patrick Chanezon], la documentation [Docker] et la [présentation de CoreOS].

* Pour démarrer avec Fleet et CoreOS dans Azure Resource Manager, essayez cet [exemple de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/coreos-with-fleet-multivm).

* Pour apprendre à utiliser les environnements open-source sur des machines virtuelles Linux dans Azure, consultez [Linux et informatique open-source sur Azure] .

<!--Link references-->
[Azure Command-Line Interface (Azure)]: ../xplat-cli-install.md
[CoreOS]: https://coreos.com/
[présentation de CoreOS]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[didacticiel CoreOS de Tim Park]: https://github.com/timfpark/coreos-azure
[didacticiel CoreOS de Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Utilisation de CoreOS sur Azure]: virtual-machines-linux-classic-coreos-howto.md
[Configurer un jeu d’équilibrage de la charge]: ../load-balancer/load-balancer-get-started-internet-classic-cli.md
[Lancement de conteneurs avec fleet]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[Fichiers d’unité]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[image Docker Hub busybox]: https://registry.hub.docker.com/_/busybox/
[image nginx Docker Hub]: https://hub.docker.com/_/nginx/
[Linux et informatique open-source sur Azure]: virtual-machines-linux-opensource-links.md

<!---HONumber=AcomDC_0323_2016-->