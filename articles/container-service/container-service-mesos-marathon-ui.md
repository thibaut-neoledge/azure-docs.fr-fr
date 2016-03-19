<properties
   pageTitle="Gestion des conteneurs ACS via l’interface utilisateur web"
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
   
Mesos offre un environnement de déploiement et de mise à l’échelle de la charge de travail en cluster tout en faisant abstraction du matériel sous-jacent. Mesos repose sur une infrastructure qui gère la planification et l’exécution de charges de calcul. Bien qu’il existe des infrastructures pour de nombreuses charges de travail courantes, ce document décrit en détail la création et la mise à l’échelle des déploiements de conteneurs avec Marathon. Avant de pouvoir suivre ces exemples, vous devez disposer d’un cluster Mesos configuré dans ACS et connecté à distance. Pour plus d’informations, consultez les articles suivants :

- [Déploiement d’un cluster Azure Container Service](./container-service-deployment.md) 
- [Connexion à un cluster ACS](./container-service-connect.md)

## Découverte de l’interface utilisateur Mesos

Créez un tunnel SSH, puis accédez à http://localhost/Mesos pour charger l’interface utilisateur web Mesos. Cette page regroupe des informations sur le cluster Mesos, notamment sur les agents activés, l’état de la tâche et la disponibilité des ressources.

![Créer un déploiement](media/ui1.png)

## Découverte de l’interface utilisateur Marathon

Pour afficher l’interface utilisateur Marathon, accédez à http://localhost/Marathon. Sur cet écran, vous pouvez démarrer un nouveau conteneur ou une autre application sur le cluster ACS Mesos, tout en visualisant des informations sur les conteneurs et les applications en cours d’exécution.

![Créer un déploiement](media/ui2.png)

## Déployer un conteneur Docker

Pour démarrer un nouveau conteneur sur le cluster Mesos à l’aide de Marathon, cliquez sur le bouton `Create Application`. Le formulaire Nouvelle application permet de définir les paramètres de l’application ou du conteneur. Dans cet exemple, nous déployons un simple conteneur Nginx. Entrez les informations ci-après. Cliquez sur Créer lorsque vous avez terminé.
 
Champ | Valeur
----------------|-----------
ID | nginx
Image | nginx
Réseau | Relié par un pont
Port du conteneur | 80
Port de l’hôte | 80
Protocole | TCP

![Créer un déploiement](media/ui3.png)

Sur la page principale de Marathon, vous pouvez voir l’état du déploiement du conteneur.

![Créer un déploiement](media/ui4.png)

Si vous revenez à l’application Mesos (http://localhost/Mesos)), vous voyez maintenant qu’une tâche (dans ce cas de figure, un conteneur Docker) est en cours d’exécution sur le cluster Mesos. Vous pouvez également voir le nœud du cluster sur lequel la tâche est exécutée.

![Créer un déploiement](media/ui5.png)

## Mise à l’échelle d’un conteneur Docker

L’interface utilisateur web de Marathon peut également servir à augmenter ou diminuer le nombre d’instances d’un conteneur. Pour ce faire, accédez à la page de Marathon, sélectionnez le conteneur que vous souhaitez mettre à l’échelle, puis cliquez sur le bouton `scale`. Dans la fenêtre Mettre à l’échelle l’application, entrez le nombre d’instances de conteneur de votre choix et sélectionnez `Scale Application`.

![Créer un déploiement](media/ui6.png)

Une fois l’opération de mise à l’échelle terminée, plusieurs instances de la même tâche seront réparties sur les agents Mesos.

![Créer un déploiement](media/ui8.png)

<!---HONumber=AcomDC_0218_2016-->