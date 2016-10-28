<properties
   pageTitle="Pools d’agents publics et privés DC/OS ACS | Microsoft Azure"
   description="Fonctionnement des pools d’agents publics et privés avec un cluster Azure Container Service."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, conteneurs, micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="adegeo"/>

# Pools d’agents DC/OS pour Azure Container Service

Azure Container Service DC/OS répartit les agents dans des pools publics ou privés. Un déploiement peut être effectué dans un pool, affectant ainsi l’accessibilité entre les machines de votre service de conteneur. Les machines peuvent être exposées à internet (publiques) ou conservées en interne (privées). Cet article explique brièvement pourquoi il existe des pools publics et privés.

### Agents privés

Les nœuds d’un agent privé sont exécutés via un réseau non routable. Ce réseau n’est accessible qu’à partir de la zone administrateur ou par le biais du routeur Edge de la zone publique. Par défaut, DC/OS lance les applications sur les nœuds de l’agent privé. Pour en savoir plus sur la sécurité réseau, consultez la [documentation DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

### Agents publics

Les nœuds d’un agent public exécutent les services et les applications DC/OS sur un réseau accessible publiquement. Pour en savoir plus sur la sécurité réseau, consultez la [documentation DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

## Utiliser les pools d’agents

Par défaut, **Marathon** déploie toute nouvelle application sur les nœuds de l’agent *privé*. Vous devez explicitement déployer l’application sur le nœud *public* pendant la création de l’application. Sélectionnez l’onglet **Facultatif** et saisissez **slave\_public** pour la valeur **Rôles de ressources acceptés**. Ce processus est décrit [ici](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) et dans la documentation [DC\\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## Étapes suivantes

En savoir plus sur la [gestion de vos conteneurs DC/OS](container-service-mesos-marathon-ui.md).

Découvrez comment [ouvrir le pare-feu](container-service-enable-public-access.md) fourni par Azure pour autoriser l’accès public à votre conteneur de contrôleur DC/OS.

<!---HONumber=AcomDC_0907_2016-->