---
title: Surveiller un cluster DC/OS Azure - pile ELK | Microsoft Docs
description: Surveillez un cluster DC/OS dans un cluster Azure Container Service avec ELK (Elasticsearch, Logstash et Kibana).
services: container-service
documentationcenter: 
author: sauryadas
manager: dan.lepow
editor: 
tags: acs, azure-container-service
keywords: Conteneurs, DC/OS, Azure, surveillance, elk
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 9433eb9f734dbe1b815074f86b9a691b4d16b5c8
ms.openlocfilehash: c372c4a04de61f1eace81418ec6dea054007ec1f


---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>Surveillez un cluster Azure Container Service avec ELK
Dans cet article, nous montrons comment déployer la pile ELK (Elasticsearch, Logstash, Kibana) sur un cluster DC/OS dans Azure Container Service. 

## <a name="prerequisites"></a>Composants requis
[Déployez](container-service-deployment.md) et [connectez](container-service-connect.md) un cluster DC/OS configuré par Azure Container Service. Explorez le tableau de bord DC/OS et les services Marathon [ici](container-service-mesos-marathon-ui.md). Installez également [l’équilibrage de charge Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
La pile ELK est une combinaison d’Elasticsearch, de Logstash et de Kibana qui fournit une pile de bout en bout pouvant être utilisée pour surveiller et analyser les journaux de votre cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configurer la pile ELK sur un cluster DC/OS
Accéder à votre interface utilisateur DC/OS via [http://localhost:80/](http://localhost:80/), puis rendez-vous dans **Univers**. Recherchez et installez Elasticsearch, Logstash et Kibana dans l’univers du contrôleur de domaine/système d’exploitationDC/OS et dans cet ordre spécifique. Vous pouvez en savoir plus sur la configuration en cliquant sur le lien **Advanced Installation (Installation avancée)**.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Une fois les conteneurs opérationnels, vous devez activer Kibana pour qu’ils soient accessibles via Marathon-LB. Accédez à **Services** > **kibana**, puis cliquez sur **Modifier** comme indiqué ci-dessous.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Basculez en **mode JSON** et défilez jusqu’à la section des étiquettes.
Vous devez ajouter une entrée `"HAPROXY_GROUP": "external"` ici comme indiqué ci-dessous.
Une fois que vous cliquez sur **Deploy changes** (Déployer les changements), votre conteneur redémarre.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Si vous souhaitez vérifier que Kibana est enregistré en tant que service dans le tableau de bord HAPROXY, vous devez ouvrir le port 9090 sur le cluster de l’agent, car HAPROXY s’exécute sur le port 9090.
Par défaut, nous ouvrons les ports 80, 8080 et 443 dans le cluster de l’agent DC/OS.
Vous trouverez les instructions pour ouvrir un port et fournir une évaluation publique [ici](container-service-enable-public-access.md).

Pour accéder au tableau de bord HAPROXY, ouvrez l’interface d’administration Marathon-LB à l’adresse : `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Une fois que vous accédez à l’URL, le tableau de bord HAPROXY s’affiche comme indiqué ci-dessous, et vous devriez voir une entrée de service pour Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Pour accéder au tableau de bord Kibana, qui est déployé sur le port 5601, vous devez ouvrir le port 5601. Suivez les instructions [ici](container-service-enable-public-access.md). Ouvrez ensuite le tableau de bord Kibana à l’adresse : `http://localhost:5601`.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Elasticsearch, Logstash et Kibarna, consultez la [documentation sur la pile élastique](https://www.elastic.co/guide/index.html).
 




<!--HONumber=Feb17_HO1-->


