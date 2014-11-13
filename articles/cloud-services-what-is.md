<properties urlDisplayName="What is a Cloud Service" pageTitle="Qu'est-ce qu'un service cloud&nbsp;? - Gestion des services Azure" metaKeywords="Azure cloud services intro, cloud services overview, cloud services basics" description="Introduction aux services cloud dans Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="Qu'est-ce qu'un service cloud&nbsp;?" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />

# Qu'est-ce qu'un service cloud ?

Lorsque vous créez une application et que vous l'exécutez dans Azure, l'ensemble constitué du code et de la configuration est appelé service cloud Azure (également connu sous le nom de *service hébergé* dans les versions antérieures d'Azure).

En créant un service cloud, vous pouvez déployer une application Web multiniveau dans Azure, définissant ainsi plusieurs rôles pour répartir le traitement et permettre une plus grande flexibilité de votre application. Un service cloud se compose d'un ou plusieurs rôles Web et/ou de travail, chacun comportant ses propres fichiers d'application et sa configuration. Les sites Web Azure et les machines virtuelles permettent également de déployer des applications Web dans Azure. L'avantage principal des services cloud réside dans leur capacité à prendre en charge des architectures multiniveau plus complexes. Pour une comparaison détaillée, consultez la page [Comparaison des sites Web Azure, des services cloud et des machines virtuelles][Comparaison des sites Web Azure, des services cloud et des machines virtuelles].

Dans le cadre d'un service cloud, Azure assure l'entretien de l'infrastructure : maintenance de routine, application des correctifs sur les systèmes d'exploitation et tentatives de récupération après échec matériel ou de service. Si vous définissez au moins deux instances de chaque rôle, la plus grande partie de la maintenance et vos propres mises à jour de service peuvent être effectuées sans interruption de service. Un service cloud doit comporter au moins deux instances de chaque rôle pour répondre aux conditions du contrat de niveau de service Azure, qui garantit la connectivité externe de vos rôles sur Internet au moins 99,95 % du temps.

Chaque service cloud comporte deux environnements dans lesquels vous pouvez déployer vos packages de service et votre configuration. Vous pouvez déployer un service cloud dans l'environnement intermédiaire afin d'effectuer des tests avant de le passer en production. Il est facile de passer un service cloud de l'environnement intermédiaire vers celui de production : il suffit d'échanger les adresses IP virtuelles (VIP) associées à ces deux environnements.

## Concepts

-   **Rôle de service cloud :** celui-ci se compose de fichiers d'application et d'une configuration. Un service cloud peut comporter deux types de rôle :

> -   **Rôle Web :** celui-ci fournit un serveur Web IIS (Internet Information Services) dédié, servant à héberger les applications Web frontales.

> -   **Rôle de travail :** les applications hébergées dans un rôle de travail peuvent exécuter des tâches asynchrone, sur le long terme ou perpétuelles, indépendamment des entrées ou des interactions de l'utilisateur.

-   **Instance de rôle :** il s'agit d'une machine virtuelle sur laquelle sont exécutés le code d'application et la configuration de rôle. Un rôle peut comporter plusieurs instances, définies dans le fichier de configuration de service.

-   **Système d'exploitation invité :** pour un service cloud, il s'agit du système d'exploitation installé sur les instances de rôle (machines virtuelles) sur lesquelles est exécuté le code de votre application.

-   **Composants de service cloud :** trois composants sont nécessaires pour déployer une application en tant que service cloud dans Azure :

> -   **Fichier de définition de service (.csdef) :** celui-ci définit le modèle du service, avec le nombre de rôles.

> -   **Fichier de configuration de service (.cscfg) :** celui-ci contient des paramètres de configuration pour le service cloud et les différents rôles, y compris le nombre d'instances de rôle.

> -   **Package de service (.cspkg) :** celui-ci contient le code de l'application et le fichier de définition de service.

-   **Déploiement de service cloud :** il s'agit d'une instance de service cloud déployée dans l'environnement Azure intermédiaire ou de production. Vous pouvez conserver des déploiements à la fois en environnement intermédiaire et en production.

-   **Environnements de déploiement :** Azure propose deux environnements de déploiement pour les services cloud : un *environnement intermédiaire* dans lequel vous pouvez tester votre déploiement avant de le passer en *environnement de production*. Ces deux environnements se distinguent uniquement par les adresses IP virtuelles (VIP) via lesquelles on accède au service cloud. Dans l'environnement intermédiaire, le GUID du service cloud identifie le service cloud dans les URL (*GUID*.cloudapp.net). Dans l'environnement de production, l'URL est basée sur le préfixe DNS, plus explicite, attribué au service cloud (par exemple, *monservice*.cloudapp.net).

-   **Inversion de déploiements :** pour promouvoir un déploiement dans l'environnement Azure intermédiaire, il est possible d'inverser les déploiements en intervertissant les adresses IP virtuelles permettant d'accéder aux deux déploiements. Une fois le déploiement effectué, le nom DNS du service cloud pointe vers le déploiement qui était dans l'environnement intermédiaire.

-   **Surveillance minimale ou détaillée :** la *surveillance minimale*, configurée par défaut pour un service cloud, utilise des compteurs de performances récupérés sur le système d'exploitation hôte des instances de rôle (machine virtuelle). La *surveillance détaillée* recueille des mesures supplémentaires sur les données de performances dans les instances de rôle, afin de permettre une analyse plus fine des problèmes qui surviennent au cours du traitement de l'application. Pour plus d'informations, consultez la page [Surveillance des services cloud][Surveillance des services cloud].

-   **Azure Diagnostics :** il s'agit de l'API qui vous permet de recueillir des données de diagnostic provenant des applications exécutées dans Azure. Azure Diagnostics doit être activé pour les rôles de service cloud afin de pouvoir utiliser la surveillance détaillée. Pour plus d'informations, consultez la page [Activation des diagnostics dans Azure][Activation des diagnostics dans Azure].

-   **Liaison d'une ressource :** pour voir les dépendances de votre service cloud par rapport à d'autres ressources, par exemple une instance de base de données SQL, vous pouvez établir une liaison entre la ressource et le service cloud. Dans la version préliminaire du portail de gestion, vous pouvez afficher les ressources liées sur la page **Ressources liées**, afficher leur statut sur le tableau de bord et étendre une base de données SQL liée sur la page **Mettre à l'échelle**. Ce type de lien de ressource ne connecte pas la ressource à l'application ; vous devez configurer les connexions du code de l'application.

-   **Extension d'un service cloud :** il est possible d'effectuer une montée en charge d'un service cloud en augmentant le nombre d'instances de rôle (machines virtuelles) déployées pour un rôle. Une diminution des instances de rôle réduit l'étendue du service cloud. Dans la version préliminaire du portail de gestion, vous pouvez également modifier l'étendue d'une instance de base de données SQL liée en modifiant l'édition de base de données SQL et la taille maximale de la base de données lorsque vous modifiez l'échelle de vos rôles de service.

-   **Contrat de niveau de service Azure (SLA) :** le SLA Azure Compute garantit que, en déployant deux instances de rôle ou plus pour chaque rôle, l'accès à votre service cloud sera maintenu au minimum 99,95 % du temps. Des actions de détection et de correction seront également entreprises 99,9 % du temps au cours duquel le processus d'une instance de rôle n'est pas exécuté. Pour plus d'informations, consultez la page [Contrats de niveau de service][Contrats de niveau de service].

  [Comparaison des sites Web Azure, des services cloud et des machines virtuelles]: http://azure.microsoft.com/fr-fr/documentation/articles/choose-web-site-cloud-service-vm/
  [Surveillance des services cloud]: http://azure.microsoft.com/fr-fr/manage/services/cloud-services/how-to-monitor-a-cloud-service/
  [Activation des diagnostics dans Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/cloud-services-dotnet-diagnostics/
  [Contrats de niveau de service]: http://azure.microsoft.com/fr-fr/support/legal/sla/
