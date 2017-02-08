---
title: "Gérer un cluster Azure Container Service avec l’interface utilisateur Marathon | Microsoft Docs"
description: "Déployez des conteneurs dans un cluster Azure Container Service à l’aide de l’interface utilisateur web Marathon."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Mesos, Azure
ms.assetid: d148ed1e-b582-4d51-944f-1ac7ae3c4fd6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: c472963048e842e795e17df39567a8380d8543a9
ms.openlocfilehash: b076fabd964cfb583bb4049d1373268d0d2762fb


---
# <a name="manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>Gérer un cluster de contrôleur de domaine/système d’exploitation Azure Container Service via l’interface utilisateur web Marathon
DC/OS offre un environnement de déploiement et de mise à l’échelle des charges de travail en cluster tout en faisant abstraction du matériel sous-jacent. DC/OS sous-tend une infrastructure qui gère la planification et l’exécution des charges de travail de calcul.

Bien qu’il existe des infrastructures pour de nombreuses charges de travail courantes, ce document décrit la création et la mise à l’échelle des déploiements de conteneurs avec Marathon. 


## <a name="prerequisites"></a>Composants requis
Avant d’étudier ces exemples, vous devez avoir un cluster DC/OS configuré dans Azure Container Service. Vous devez également disposer d’une connectivité à distance à ce cluster. Pour plus d’informations sur ces éléments, voir les articles suivants :

* [Déploiement d’un cluster Azure Container Service](container-service-deployment.md)
* [Connexion à un cluster Azure Container Service](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>Exploration de l’interface utilisateur de DC/OS
[Créez](container-service-connect.md) un tunnel SSH (Secure Shell), puis accédez à http://localhost/. Cela charge l’interface utilisateur web de DC/OS et affiche des informations sur le cluster, notamment les ressources utilisées, les agents actifs, et les services en cours d’exécution.

![IU DC/OS](./media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Découverte de l’interface utilisateur Marathon
Pour afficher l’interface utilisateur Marathon, accédez à http://localhost/Marathon. À partir de cet écran, vous pouvez démarrer un nouveau conteneur ou une autre application sur le cluster DC/OS d’Azure Container Service. Vous pouvez également voir des informations sur les conteneurs et les applications en cours d’exécution.  

![IU Marathon](./media/dcos/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Déployer un conteneur au format Docker
Pour déployer un nouveau conteneur à l’aide de Marathon, cliquez sur **Créer une application** et entrez les informations suivantes dans les onglets du formulaire :

| Champ | Valeur |
| --- | --- |
| ID |nginx |
| Mémoire | 32 |
| Image |nginx |
| Réseau |Relié par un pont |
| Port de l’hôte |80 |
| Protocole |TCP |

![Nouvelle interface utilisateur d’application : général](./media/dcos/dcos4.png)

![Nouvelle interface utilisateur d’application : conteneur Docker](./media/dcos/dcos5.png)

![Nouvelle interface utilisateur d’application : détection de service et de ports](./media/dcos/dcos6.png)

Si vous souhaitez mapper le port du conteneur de manière statique à un port de l’agent, cela doit être effectué à l’aide du mode JSON. Pour ce faire, basculez de l’Assistant Nouvelle Application en **Mode JSON** . Ensuite, entrez le paramètre suivant sous la section `portMappings` de la définition d’application. Cet exemple montre comment lier le port 80 du conteneur au port 80 de l’agent DC/OS. Vous pouvez basculer l’Assistant hors du mode JSON après avoir apporté cette modification.

```none
"hostPort": 80,
```

![Nouvelle interface utilisateur d’application : exemple de port 80](./media/dcos/dcos13.png)

Si vous souhaitez activer les contrôles d’intégrité, définissez un chemin d’accès dans l’onglet **Contrôles d'intégrité**.

![Interface utilisateur de nouvelle application -- contrôles d’intégrité](./media/dcos/dcos_healthcheck.png)

Le cluster DC/OS est déployé avec un ensemble d’agents privés et publics. Pour que le cluster puisse accéder à des applications à partir d’Internet, vous devez déployer les applications vers un agent public. Pour ce faire, sélectionnez l’onglet **Facultatif** de l’Assistant Nouvelle Application et saisissez **slave_public** pour les **Rôles de ressources acceptés**.

Cliquez ensuite sur **Créer une application**.

![Nouvelle interface utilisateur d’application : paramètre de l’agent public](./media/dcos/dcos14.png)

Sur la page principale de Marathon, vous pouvez voir l’état du déploiement du conteneur. Initialement, vous voyez l’état **Déploiement**. Après un déploiement réussi, l’état passe à **En cours d’exécution**.

![Interface utilisateur de la page principale de Marathon : état du déploiement du conteneur](./media/dcos/dcos7.png)

Lorsque vous revenez à l’interface utilisateur web de DC/OS (http://localhost/), vous verrez qu’une tâche (en l’occurrence un conteneur formaté par Docker) est en cours d’exécution sur le cluster DC/OS.

![IU web DC/OS : tâche en cours d’exécution sur le cluster](./media/dcos/dcos8.png)

Pour voir le nœud du cluster sur lequel la tâche est exécutée, cliquez sur l’onglet **Nœuds**.

![IU web DC/OS : nœud du cluster de tâche](./media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>Mettre vos conteneurs à l’échelle
L’IU de Marathon peut servir à augmenter ou diminuer le nombre d’instances d’un conteneur. Pour ce faire, accédez à la page de **Marathon**, sélectionnez le conteneur que vous souhaitez mettre à l’échelle, puis cliquez sur **Mettre à l’échelle l’application**. Dans la fenêtre **Mettre à l’échelle l’application**, entrez le nombre d’instances de conteneur de votre choix et cliquez sur **Mettre à l’échelle l’application**.

![IU de Marathon : boîte de dialogue Mettre à l’échelle l’application](./media/dcos/dcos10.png)

Une fois l’opération de mise à l’échelle terminée, plusieurs instances de la même tâche seront réparties sur les agents DC/OS.

![Tableau de bord de l’IU web DC/OS : propagation de tâche sur plusieurs agents](./media/dcos/dcos11.png)

![IU web DC/OS : nœuds](./media/dcos/dcos12.png)

## <a name="next-steps"></a>Étapes suivantes
* [Utilisation de DC/OS et de l’API Marathon](container-service-mesos-marathon-rest.md)

* Deep dive on the Azure Container Service with Mesos (Présentation détaillée d’Azure Container Service avec Mesos)

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 



<!--HONumber=Dec16_HO1-->


