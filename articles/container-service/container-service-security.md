---
title: "Sécurité des conteneurs dans Azure Container Service | Microsoft Docs"
description: "Considérations relatives à la sécurisation des conteneurs Docker déployés dans Azure Container Service et les services Azure connexes."
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Mesos, Azure, Kubernetes
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: saudas
ms.custom: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: cabcc35098fa2063812f5aef8593dba6dce48cc2
ms.lasthandoff: 03/31/2017


---
# <a name="securing-docker-containers-in-azure-container-service"></a>Sécurisation des conteneurs Docker dans Azure Container Service

Cet article présente les considérations et recommandations relatives à la sécurisation des conteneurs Docker déployés dans Azure Container Service. Un grand nombre de ces considérations s’appliquent généralement aux conteneurs Docker déployés dans Azure ou dans d’autres environnements. 

## <a name="image-security"></a>Sécurité des images

Les conteneurs sont créés à partir d’images stockées dans un ou plusieurs référentiels. Ces référentiels peuvent appartenir à des registres de conteneurs publics ou privés. [Docker Hub](https://hub.docker.com/) est par exemple un registre public. À l’inverse, [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/) est un registre privé pouvant être installé en local ou dans un cloud privé virtuel. Il existe également des services de registres de conteneurs privés basés sur le cloud, notamment [Azure Container Registry](../container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Images publiques et privées
En général, comme avec n’importe quel package logiciel publié publiquement, une image conteneur disponible publiquement ne garantit en aucun cas la sécurité. Les images conteneur comprennent plusieurs couches logicielles, chacune pouvant contenir des vulnérabilités. Il est essentiel d’identifier l’origine de l’image conteneur, y compris le propriétaire de l’image (pour déterminer s’il s’agit d’une source fiable ou non), les couches logicielles qui la composent et les versions logicielles. 

Par exemple, si vous accédez au [référentiel nginx](https://hub.docker.com/_/nginx/) officiel dans Docker Hub, puis que vous vous rendez dans l’onglet **Balises**, vous voyez les vulnérabilités codées par couleur associées à chaque image. Chaque couleur représente la vulnérabilité d’une couche logicielle de l’image. Pour plus d’informations sur l’analyse des vulnérabilités dans Docker Hub, consultez [Understanding official repos on Docker Hub](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/) (Présentation des référentiels officiels sur Docker Hub).

![Images Nginx dans Docker Hub](./media/container-service-security/docker-hub-nginx.png)



La sécurité est une préoccupation majeure des entreprises, et pour se protéger de toute attaque pouvant la compromettre, elles doivent stocker et récupérer des images dans un registre privé tel que Azure Container Registry ou Docker Trusted Registry. En plus de fournir un registre privé géré, Azure Container Registry prend en charge [l’authentification basée sur le principal de service](../container-registry/container-registry-authentication.md) via Azure Active Directory pour les flux d’authentification de base, y compris l’accès en fonction du rôle pour les autorisations de propriétaire, en lecture seule et en écriture.


### <a name="image-security-scanning"></a>Analyse de la sécurité des images

Même si vous utilisez un registre privé, il est judicieux d’utiliser des solutions d’analyse des images pour une validation supplémentaire de la sécurité. Chaque couche logicielle d’une image conteneur est potentiellement sujette aux vulnérabilités indépendamment des autres couches de l’image conteneur. Comme de plus en plus d’entreprises commencent à déployer leurs charges de travail de production basées sur des technologies de conteneur, l’analyse des images devient importante pour prévenir les menaces de sécurité pouvant toucher leurs organisations. 

Les solutions d’analyse et de surveillance de la sécurité telles que [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) et [Aqua Security](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) (entre autres), peuvent être utilisées pour analyser les images conteneur dans un registre privé et identifier les vulnérabilités éventuelles. Il est important de comprendre la profondeur de l’analyse proposée par les différentes solutions. Par exemple, certaines solutions peuvent uniquement vérifier par recoupement les couches de l’image en se basant sur des vulnérabilités connues. Ces solutions peuvent ne pas être en mesure de vérifier les couches logicielles de l’image créées via certains logiciels de gestionnaire de package. D’autres solutions proposent une analyse plus en profondeur et peuvent détecter des vulnérabilités dans n’importe quel logiciel faisant partie d’un package.

### <a name="production-deployment-rules-and-audit"></a>Audit et règles de déploiement en production
Une fois qu’une application est déployée en production, il est essentiel de définir certaines règles pour garantir que les images utilisées dans les environnements de production sont sûres et ne contiennent aucune vulnérabilité.

* En règle générale, l’exécution d’images avec des vulnérabilités, même mineures, doit être interdite dans un environnement de production. En outre, toutes les images déployées en production doivent idéalement être enregistrées dans un registre privé uniquement accessible à quelques personnes. Il est également important de minimiser le nombre d’images de production pour vous assurer qu’elles peuvent être gérées facilement.

* Dans la mesure où il est difficile d’identifier l’origine des logiciels à partir d’une image conteneur disponible publiquement, nous vous recommandons de créer des images à partir de la source pour être certain de connaître l’origine de la couche. En cas de vulnérabilités dans une image conteneur auto-créée, les clients peuvent plus rapidement trouver une solution. Avec une image publique, les clients devraient trouver la racine de cette image pour résoudre le problème ou obtenir une autre image sécurisée de l’éditeur.

* Une image soigneusement analysée et déployée en production n’est pas nécessairement à jour pour toute la durée de vie de l’application. Des vulnérabilités de sécurité peuvent être signalées pour des couches de l’image qui n’étaient pas connues précédemment ou qui ont été introduites après le déploiement en production. L’audit périodique des images déployées en production est nécessaire pour identifier les images qui sont obsolètes ou qui n’ont pas été mises à jour depuis un certain temps. Vous pouvez utiliser des méthodologies de déploiement bleu-vert et des mécanismes de mise à niveau propagée pour mettre à jour les images conteneur sans interruption de service. L’analyse des images peut être effectuée avec les outils décrits dans la section précédente. 

* Vous pouvez utiliser une solution de pipeline d’intégration continue (IC) permettant de créer des images et d’analyser la sécurité pour vous aider à maintenir la sécurité des registres privés avec des images conteneur sûres. L’analyse des vulnérabilités intégrée à la solution CI garantit que les images réussissant tous les tests sont transmises au registre privé à partir duquel les charges de travail de production sont déployées. En cas d’échec aux tests, la solution de pipeline CI garantit que les images vulnérables ne sont pas transmises au registre privé utilisé pour les déploiements de charge de travail en production. Elle automatise aussi l’analyse de la sécurité des images si celles-ci sont nombreuses. Il s’agit d’une bonne alternative aux audits manuels des images visant à contrôler la présence de vulnérabilités de sécurité, qui sont des processus longs, fastidieux et sujets aux erreurs.

## <a name="host-level-container-isolation"></a>Isolation du conteneur au niveau de l’hôte
Lorsqu’un client déploie des applications de conteneur sur des ressources Azure, elles sont déployées au niveau d’un abonnement dans des groupes de ressources et ne sont pas mutualisées. Cela signifie que si un client partage un abonnement avec d’autres personnes, aucune séparation ne peut être créée entre les deux déploiements du même abonnement. Par conséquent, la sécurité au niveau du conteneur n’est pas garantie. 

Il est également essentiel de comprendre que les conteneurs partagent le noyau et les ressources de l’hôte (dans Azure Container Service, il s’agit d’une machine virtuelle Azure dans un cluster). Par conséquent, les conteneurs s’exécutant en production doivent être exécutés en mode d’utilisateur sans privilège. L’exécution d’un conteneur avec des privilèges racine peut compromettre l’ensemble de l’environnement. En effet, avec un accès de niveau racine dans un conteneur, un pirate peut accéder à tous les privilèges racine de l’hôte. De plus, il est important d’exécuter des conteneurs avec des systèmes de fichiers en lecture seule. Cela empêche une personne qui a accès au conteneur compromis d’écrire des scripts malveillants dans le système de fichiers et d’accéder à d’autres fichiers. De même, il est important de limiter les ressources (telles que la bande passante réseau, l’UC et la mémoire) allouées à un conteneur. Cela permet d’empêcher les pirates d’accaparer des ressources et d’exercer des activités illégales telles que l’utilisation frauduleuse de cartes de crédit ou le minage de bitcoin, pouvant bloquer l’exécution d’autres conteneurs sur l’hôte ou le cluster.

## <a name="orchestrator-considerations"></a>Considérations relatives aux orchestrateurs

Chaque orchestrateur disponible dans Azure Container Service possède ses propres considérations relatives à la sécurité. Par exemple, vous devez limiter l’accès SSH direct aux nœuds des orchestrateurs dans Container Service. À la place, vous pouvez utiliser les outils de ligne de commande ou l’interface utilisateur de chaque orchestrateur (tel que `kubectl` pour Kubernetes) pour gérer l’environnement du conteneur sans accéder aux hôtes. Pour plus d’informations, consultez [Connexion à distance à un cluster Kubernetes, DC/OS ou Docker Swarm](container-service-connect.md).

Pour obtenir des informations de sécurité supplémentaires sur des orchestrateurs en particulier, consultez les ressources suivantes :

* **Kubernetes** : [Security Best Practices for Kubernetes Deployment](http://blog.kubernetes.io/2016/08/security-best-practices-kubernetes-deployment.html) (Meilleures pratiques en matière de sécurité pour les déploiements Kubernetes)

* **DC/OS** : [Securing Your Cluster](https://dcos.io/docs/1.8/administration/securing-your-cluster/) (Sécurisation de votre cluster)

* **Docker Swarm** : [Docker Security](https://www.docker.com/docker-security) (Sécurité Docker)



## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la sécurité du conteneur et de l’architecture Docker, consultez [Introduction to Container Security](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf) (Introduction à la sécurité de conteneur).

* Pour plus d’informations sur la sécurité de la plateforme Azure, visitez [Azure Security Center](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure).
