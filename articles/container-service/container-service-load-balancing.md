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
   ms.date="07/11/2016"
   ms.author="rogardle"/>

# Équilibrage de charge d’un cluster Azure Container Service

Dans cet article, nous allons configurer un site web frontal sur un service Azure Container Service géré par DC/OS. Nous allons également configurer un outil Marathon-LB pour activer la montée en puissance de l’application.

## Configuration requise

[Déployez une instance d’Azure Container Service](container-service-deployment.md) avec un orchestrator de type DC/OS, et [vérifiez que votre client peut se connecter à votre cluster](container-service-connect.md).

## Équilibrage de la charge

Il existe deux couches d’équilibrage de charge dans le cluster Container Service que nous allons créer :

  1. Azure Load Balancer fournit des points d’entrée publics (ceux auxquels les utilisateurs finaux ont recours). Il est automatiquement fourni par Azure Container Service. Par défaut, il est configuré pour ouvrir les ports 80, 443 et 8080.
  2. L’outil Marathon Load Balancer (marathon-lb) achemine les demandes entrantes vers les instances de conteneur qui traitent ces demandes. L’outil marathon-lb s’adapte dynamiquement au fur et à mesure que les conteneurs fournissant notre service web sont mis à l’échelle. Par défaut, cet équilibreur de charge n’est pas fourni dans votre service Container Service, mais il est très facile à installer.

## Équilibreur de charge Marathon

L’équilibreur de charge Marathon se reconfigure dynamiquement en fonction des conteneurs que vous avez déployés. Il est également résistant à la perte d’un conteneur ou d’un agent. Le cas échéant, Apache Mesos redémarre simplement le conteneur ailleurs, et l’outil marathon-lb s’adapte.

Pour installer l’outil Marathon Load Balancer, vous pouvez utiliser l’IU du site web DC/OS ou la ligne de commande DC/OS.

### Installer l’outil Marathon-LB à l’aide de l’IU du site web DC/OS

  1. Cliquez sur Universe (Univers).
  2. Recherchez Marathon-LB.
  3. Cliquez sur Install (Installer).

![Installation de l’outil marathon-lb via l’interface web DC/OS](./media/dcos/marathon-lb-install.png)

### Installer l’outil Marathon-LB à l’aide de l’interface CLI DC/OS

Après avoir installé l’interface CLI DC/OS et vous être assuré que vous pouvez vous connecter à votre cluster, exécutez la commande suivante à partir de votre ordinateur client :

```bash
dcos package install marathon-lb
```

## Déployer une application web à charge équilibrée

À présent que nous disposons du package marathon-lb, nous pouvons déployer un serveur web simple en utilisant la configuration suivante :

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
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

  * Définissez la valeur de `HAProxy_0_VHOST` sur le nom de domaine complet de l’équilibreur de charge de vos agents. Il respecte la forme `<acsName>agents.<region>.cloudapp.azure.com`. Par exemple, si vous créez un cluster Container Service avec le nom `myacs` dans la région `West US`, le nom de domaine complet est `myacsagents.westus.cloudapp.azure.com`. Vous pouvez également le trouver en recherchant l’équilibreur de charge dont le nom contient le terme « agent » lorsque vous parcourez les ressources du groupe de ressources que vous avez créé pour votre service Container Service dans le [portail Azure](https://portal.azure.com).
  * Définissez servicePort sur un port supérieur ou égal à 10 000. Cela identifie le service en cours d’exécution dans ce conteneur ; marathon-lb s’en sert pour identifier les services sur lesquels il doit équilibrer les charges.
  * Définissez l’étiquette `HAPROXY_GROUP` sur external (externe).
  * Définissez `hostPort` sur 0. Cela signifie que Marathon allouera un port disponible de manière aléatoire.
  * Définissez `instances` sur le nombre d’instances à créer. Vous pourrez toujours augmenter ou réduire ce nombre ultérieurement.

### Déployer à l’aide de l’IU du site web DC/OS

  1. Visitez la page Marathon à l’adresse http://localhost/marathon (après avoir configuré votre [tunnel SSH](container-service-connect.md) et cliquez sur `Create Appliction`.
  2. Dans la boîte de dialogue `New Application`, cliquez sur `JSON Mode` dans le coin supérieur droit.
  3. Collez le code JSON ci-dessus dans l’éditeur.
  4. Cliquez sur `Create Appliction`.

### Déployer à l’aide de l’interface CLI DC/OS

Pour déployer cette application avec l’interface CLI DC/OS, copiez simplement le code JSON ci-dessus dans un fichier appelé `hello-web.json`, puis exécutez :

```bash
dcos marathon app add hello-web.json
```

## Équilibrage de charge Azure

Par défaut, Azure Load Balancer expose les ports 80, 8080 et 443. Si vous utilisez l’un de ces trois ports (comme nous le faisons dans l’exemple ci-dessus), vous n’avez rien à faire. Vous devez pouvoir atteindre le nom de domaine complet de l’équilibreur de charge de votre agent. À chaque actualisation, vous atteindrez l’un de vos trois serveurs web par tourniquet (round robin). Toutefois, si vous utilisez un port différent, vous devez ajouter une règle de type tourniquet (round-robin) ainsi qu’une sonde sur l’équilibreur de charge pour le port que vous avez utilisé. Vous pouvez le faire depuis [l’interface de ligne de commande (CLI) Azure](../xplat-cli-azure-resource-manager.md), avec les commandes `azure lb rule create` et `azure lb probe create`. Vous pouvez également procéder à l’aide du portail Azure.


## Autres cas de figure

Un scénario possible serait l’utilisation de différents domaines pour exposer des services différents. Par exemple :

mondomaine1.com -> Azure LB:80 -> marathon-lb:10001 -> monconteneur1:33292 mondomaine2.com -> Azure LB:80 -> marathon-lb:10002 -> monconteneur2:22321

Pour ce faire, consultez la section consacrée aux [hôtes virtuels](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) , qui fournit un moyen d’associer des domaines à des chemins d’accès à l’outil marathon-lb spécifiques.

Vous pouvez également exposer des ports différents et les remapper vers le service correct derrière marathon-lb. Par exemple :

Azure lb:80 -> marathon-lb:10001 -> monconteneur:233423 Azure lb:8080 -> marathon-lb:1002 -> monconteneur2:33432


## Étapes suivantes

Pour en savoir plus sur [marathon-lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/), consultez la documentation DC/OS.

<!---HONumber=AcomDC_0713_2016-->