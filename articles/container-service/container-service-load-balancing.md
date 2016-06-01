<properties
   pageTitle="Équilibrage de charge d’un cluster Azure Container Service | Microsoft Azure"
   description="Équilibrage de charge d’un cluster Azure Container Service."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Conteneurs, micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Équilibrage de charge d’un cluster Azure Container Service

Dans cet article, nous allons configurer un serveur web frontal pouvant être monté en puissance afin de fournir des services derrière l’équilibrage de charge Azure.


## Composants requis

[Déployez une instance d’Azure Container Service](container-service-deployment.md) avec un Orchestrator de type DCOS, [vérifiez que votre client peut se connecter à votre cluster](container-service-connect.md), et[AZURE.INCLUDE [installez l’interface de ligne de commande DC/OS.](../../includes/container-service-install-dcos-cli-include.md)].


## Équilibrage de la charge

Deux couches d’équilibrage de charge sont présentes dans un cluster Azure Container Service : l’équilibrage de charge Azure pour les points d’entrée publics (ceux qu’atteignent les utilisateurs finaux) et l’équilibrage de charge marathon-lb sous-jacent, qui achemine les demandes entrantes pour exécuter des requêtes de traitement d’instances du conteneur. L’équilibrage de charge s’adapte dynamiquement au fur et à mesure que les conteneurs fournissant le service sont mis à l’échelle.

## Marathon LB 

La solution Marathon LB se reconfigure dynamiquement en fonction des conteneurs que vous avez déployés. Elle est également résistante à la perte d’un conteneur ou d’un agent ; le cas échéant, Mesos redémarre simplement le conteneur ailleurs et reconfigure l’équilibrage de charge Marathon LB.

Pour installer Marathon LB, exécutez la commande suivante à partir de votre ordinateur client :

```bash
dcos package install marathon-lb 
``` 

À présent que nous disposons du package marathon-lb, nous pouvons déployer un serveur web simple à l’aide de la configuration suivante :


```json
{ 
  "id": "web", 
  "container": { 
    "type": "DOCKER", 
    "docker": { 
      "image": "tutum/hello-world", 
      "network": "BRIDGE", 
      "portMappings": [ 
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 } 
      ], 
      "forcePullImage":true 
    } 
  }, 
  "instances": 3, 
  "cpus": 0.1, 
  "mem": 65, 
  "healthChecks": [{ 
      "protocol": "HTTP", 
      "path": "/", 
      "portIndex": 0, 
      "timeoutSeconds": 10, 
      "gracePeriodSeconds": 10, 
      "intervalSeconds": 2, 
      "maxConsecutiveFailures": 10 
  }], 
  "labels":{ 
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http" 
  } 
}

```

Les éléments clés sont les suivants :
  * Configurez la valeur de HAProxy\_0\_VHOST sur le nom de domaine complet de l’équilibrage de charge pour vos agents. Il s’agit de la forme `<acsName>agents.<region>.cloudapp.azure.com`. Par exemple, si j’ai créé un cluster Container Service avec le nom `myacs` dans la région `West US`, le nom de domaine complet serait : `myacsagents.westus.cloudapp.azure.com`. Vous pouvez également le trouver en recherchant l’équilibrage de charge avec le terme « agent » dans le nom lorsque vous parcourez les ressources du groupe de ressources créé pour votre service de conteneur dans le [portail Azure](https://portal.azure.com).
  * Définissez servicePort sur un port supérieur ou égal à 10 000. Cela identifie le service en cours d’exécution dans ce conteneur ; marathon-lb s’en sert pour identifier les services sur lesquels il doit équilibrer les charges.
  * Définissez le label HAPROXY\_GROUP sur « external ».
  * Définissez hostPort sur 0. Marathon attribue ainsi un port disponible de manière aléatoire.

Copiez ce JSON dans un fichier appelé `hello-web.json` et utilisez-le pour déployer un conteneur :

```bash
dcos marathon app add hello-web.json 
``` 

## Équilibrage de charge Azure 

Par défaut, l’équilibrage de charge Azure expose les ports 80, 8080 et 443. Si vous utilisez l’un de ces trois ports (comme nous le faisons dans l’exemple ci-dessus), vous n’avez rien à faire : vous devez pouvoir atteindre le nom de domaine complet de l’équilibrage de charge de votre agent. À chaque actualisation, vous atteindrez l’un de vos trois serveurs web par tourniquet (round robin). Toutefois, si vous utilisez un port différent, vous devez ajouter une règle de type tourniquet (round-robin) ainsi qu’une sonde sur l’équilibrage de charge Azure pour le port que vous avez utilisé. Cela peut être fait à partir de l’[interface de ligne de commande Azure XPLAT](../xplat-cli-azure-resource-manager.md) avec les commandes `azure lb rule create` et `azure lb probe create`.


## Autres cas de figure

Un scénario possible serait l’utilisation de différents domaines pour exposer des services différents. Par exemple :

mondomaine1.com -> Azure LB:80 -> marathon-lb:10001 -> monconteneur1:33292 mondomaine2.com -> Azure LB:80 -> marathon-lb:10002 -> monconteneur2:22321

Pour ce faire, consultez la section [Virtual Hosts](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) (Hôtes virtuels), qui fournit un moyen d’associer des domaines à des chemins d’accès marathon-lb spécifiques.

Vous pouvez également exposer des ports différents et les remapper vers le service correct derrière marathon-lb. Par exemple :

Azure lb:80 -> marathon-lb:10001 -> monconteneur:233423 Azure lb:8080 -> marathon-lb:1002 -> monconteneur2:33432
 

## Étapes suivantes

Découvrez [ce billet de blog](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) pour plus d’informations sur l’équilibrage de charge Marathon LB.

<!---HONumber=AcomDC_0525_2016-->