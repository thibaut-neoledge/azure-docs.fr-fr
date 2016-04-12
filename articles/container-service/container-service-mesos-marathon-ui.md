<properties
   pageTitle="Gestion de conteneur Azure Container Service via l’interface utilisateur web | Microsoft Azure"
   description="Déployez des conteneurs dans un cluster Azure Container Service à l’aide de l’interface utilisateur web Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, conteneurs, micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>

# Gestion des conteneurs via l’interface utilisateur web

Mesos offre un environnement de déploiement et de mise à l’échelle des charges de travail en cluster tout en faisant abstraction du matériel sous-jacent. Mesos sous-tend une infrastructure qui gère la planification et l’exécution des charges de travail de calcul.

Bien qu’il existe des infrastructures pour de nombreuses charges de travail courantes, ce document décrit la création et la mise à l’échelle des déploiements de conteneurs avec Marathon. Avant d’étudier ces exemples, vous devez disposer d’un cluster Mesos configuré dans Azure Container Service. Vous devez également disposer d’une connectivité à distance à ce cluster. Pour plus d’informations sur ces éléments, consultez les articles suivants :

- [Déploiement d’un cluster Azure Container Service](./container-service-deployment.md)
- [Connexion à un cluster Azure Container Service](./container-service-connect.md)

## Découverte de l’interface utilisateur Mesos

Créez un tunnel SSH (Secure Shell), puis accédez à http://localhost/Mesos. pour charger l’interface utilisateur web Mesos. Cette page regroupe des informations sur le cluster Mesos, notamment sur les agents activés, l’état de la tâche et la disponibilité des ressources.

![Créer un déploiement - Interface utilisateur 1](media/ui1.png)

## Découverte de l’interface utilisateur Marathon

Pour afficher l’interface utilisateur Marathon, accédez à http://localhost/Marathon. À partir de cet écran, vous pouvez démarrer un nouveau conteneur ou une autre application sur le cluster Mesos d’Azure Container Service. Vous pouvez également voir des informations sur les conteneurs et les applications en cours d’exécution.

![Créer un déploiement - Interface utilisateur 2](media/ui2.png)

## Déployer un conteneur au format Docker

Pour démarrer un nouveau conteneur sur le cluster Mesos à l’aide de Marathon, cliquez sur le bouton **Créer une application**. Le formulaire **Nouvelle application** permet de définir les paramètres de l’application ou du conteneur. Dans cet exemple, vous allez déployer un seul conteneur Nginx. Entrez les informations ci-après. Lorsque vous avez terminé, cliquez sur **Créer**.

Champ | Valeur
----------------|-----------
ID | nginx
Image | nginx
Réseau | Relié par un pont
Port du conteneur | 80
Port de l’hôte | 80
Protocole | TCP

![Créer un déploiement - Interface utilisateur 3](media/ui3.png)

Sur la page principale de Marathon, vous pouvez voir l’état du déploiement du conteneur.

![Créer un déploiement - Interface utilisateur 4](media/ui4.png)

Si vous revenez à l’application Mesos (http://localhost/Mesos)), vous voyez maintenant qu’une tâche, dans ce cas de figure un conteneur formaté Docker, est en cours d’exécution sur le cluster Mesos. Vous pouvez également voir le nœud du cluster sur lequel la tâche est exécutée.

![Créer un déploiement - Interface utilisateur 5](media/ui5.png)

## Mettre vos conteneurs à l’échelle

L’IU web de Marathon peut également servir à augmenter ou diminuer le nombre d’instances d’un conteneur. Pour ce faire, accédez à la page de Marathon, sélectionnez le conteneur que vous souhaitez mettre à l’échelle, puis cliquez sur le bouton **Mettre à l’échelle**. Dans la fenêtre **Mettre à l’échelle l’application**, entrez le nombre d’instances de conteneur de votre choix et sélectionnez **Mettre à l’échelle l’application**.

![Créer un déploiement - Interface utilisateur 6](media/ui6.png)

Une fois l’opération de mise à l’échelle terminée, plusieurs instances de la même tâche seront réparties sur les agents Mesos.

![Créer un déploiement - Interface utilisateur 7](media/ui8.png)

<!---HONumber=AcomDC_0406_2016-->