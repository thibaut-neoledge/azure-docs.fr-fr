<properties 
	pageTitle="Qu'est-ce qu'un service cloud ? - Gestion des services Azure" 
	description="Introduction aux services cloud dans Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="adegeo"/>




#Qu'est-ce qu'un service cloud ?
Lorsque vous créez une application et que vous l'exécutez dans Azure, l'ensemble constitué du code et de la configuration est appelé service cloud Azure (également connu sous le nom de  *hosted service* dans les versions antérieures d'Azure).

En créant un service cloud, vous pouvez déployer une application Web multiniveau dans Azure, définissant ainsi plusieurs rôles pour répartir le traitement et permettre une plus grande flexibilité de votre application. Un service cloud se compose d'un ou plusieurs rôles Web et/ou de travail, chacun comportant ses propres fichiers d'application et sa configuration. Les sites Web Azure et les machines virtuelles permettent également de déployer des applications Web dans Azure.  L'avantage principal des services cloud réside dans leur capacité à prendre en charge des architectures multiniveau plus complexes. Pour une comparaison détaillée, consultez la page [Comparaison des sites Web Azure, des services cloud et des machines virtuelles][Comparison].

Dans le cadre d'un service cloud, Azure assure l'entretien de l'infrastructure : maintenance de routine, application des correctifs sur les systèmes d'exploitation et tentatives de récupération après échec matériel ou de service. Si vous définissez au moins deux instances de chaque rôle, la plus grande partie de la maintenance et vos propres mises à jour de service peuvent être effectuées sans interruption de service. Un service cloud doit comporter au moins deux instances de chaque rôle pour répondre aux conditions du contrat de niveau de service Azure, qui garantit la connectivité externe de vos rôles sur Internet au moins 99,95 % du temps. 

Chaque service cloud comporte deux environnements dans lesquels vous pouvez déployer vos packages de service et votre configuration. Vous pouvez déployer un service cloud dans l'environnement intermédiaire afin d'effectuer des tests avant de le passer en production. Il est facile de passer un service cloud de l'environnement intermédiaire vers celui de production : il suffit d'échanger les adresses IP virtuelles (VIP) associées à ces deux environnements. 


## Concepts ##


- **Rôle du service cloud :** celui-ci se compose de fichiers d'application et d'une configuration. Un service cloud peut comporter deux types de rôle :
 
>- **Rôle Web :**celui-ci fournit un serveur Web IIS (Internet Information Services) dédié, servant à héberger les applications Web frontales.

>- **Rôle de travail :** les applications hébergées dans un rôle de travail peuvent exécuter des tâches asynchrones, sur le long terme ou perpétuelles, indépendamment des entrées ou des interactions de l'utilisateur.

- **Instance de rôle :** il s'agit d'une machine virtuelle sur laquelle sont exécutés le code d'application et la configuration de rôle. Un rôle peut comporter plusieurs instances, définies dans le fichier de configuration de service.

- **Système d'exploitation invité :** pour un service cloud, il s'agit du système d'exploitation installé sur les instances de rôle (machines virtuelles) sur lesquelles est exécuté le code de votre application.

- **Composants du service cloud :** trois composants sont nécessaires pour déployer une application en tant que service cloud dans Azure :

>- **Fichier de définition du service :** le fichier de définition du service (.csdef) définit le modèle du service, y compris le nombre de rôles.

>- **Fichier de configuration du service :** le fichier de configuration de service (.cscfg)contient des paramètres de configuration pour le service cloud et les différents rôles, y compris le nombre d'instances de rôle.

>- **Package de service :** le package de service (.cspkg) contient le code de l'application et le fichier de définition de service.

- **Déploiement du service cloud :** il s'agit d'une instance de service cloud déployée dans l'environnement Azure intermédiaire ou de production. Vous pouvez conserver des déploiements à la fois en environnement intermédiaire et en production.

- **Environnements de déploiement :** Azure propose deux environnements de déploiement pour les services cloud : un  *staging environment* dans lequel vous pouvez tester votre déploiement avant de le promouvoir en  *production environment*. Ces deux environnements se distinguent uniquement par les adresses IP virtuelles (VIP) via lesquelles on accède au service cloud. Dans l'environnement intermédiaire, l'identificateur global unique (GUID) du service cloud identifie le service cloud dans les URL (*GUID*.cloudapp.net). Dans l'environnement de production, l'URL est basée sur le préfixe DNS, plus explicite, attribué au service cloud (par exemple,  *myservice*.cloudapp.net).

- **Inversion de déploiements :** pour promouvoir un déploiement de l'environnement intermédiaire à l'environnement de production Azure, il est possible d'inverser les déploiements en intervertissant les adresses IP virtuelles permettant d'accéder aux deux déploiements. Une fois le déploiement effectué, le nom DNS du service cloud indique le déploiement qui était dans l'environnement intermédiaire. 

- **Surveillance minimale ou détaillée :la ** *Minimal monitoring*, configurée par défaut pour un service cloud, utilise des compteurs de performances récupérés sur le système d'exploitation hôte des instances de rôle (machine virtuelle). La  *Verbose monitoring* recueille des mesures supplémentaires sur les données de performances dans les instances de rôle afin de permettre une analyse plus fine des problèmes qui surviennent au cours du traitement de l'application. Pour plus d'informations, consultez la page [Surveillance des services cloud][HTMonitorCloudServices].

- **Azure Diagnostics :** il s'agit de l'API qui vous permet de recueillir des données de diagnostic provenant des applications exécutées dans Azure. Azure Diagnostics doit être activé pour les rôles de service cloud afin de pouvoir utiliser la surveillance détaillée. Pour plus d'informations, consultez la page [Activation des diagnostics dans Azure][CloudServicesDiagnostics].

- **Liaison d'une ressource :** pour voir les dépendances de votre service cloud par rapport à d'autres ressources, par exemple une instance de base de données SQL Azure, vous pouvez établir une liaison entre la ressource et le service cloud. Dans l'aperçu du portail de gestion, vous pouvez afficher les ressources liées sur la page **Ressources liées**, afficher leur statut sur le tableau de bord et mettre à l'échelle une base de données SQL liée sur la page **Mettre à l'échelle**. Ce type de lien de ressource ne connecte pas la ressource à l'application. Vous devez configurer les connexions dans le code de l'application.

- **Mise à l'échelle un service cloud :** un service cloud est monté en charge en augmentant le nombre d'instances de rôle (machines virtuelles) déployées pour un rôle. Une diminution des instances de rôle réduit l'étendue du service cloud. Dans l'aperçu du portail de gestion, vous pouvez également modifier l'échelle d'une instance de base de données SQL liée en modifiant l'édition de base de données SQL et la taille maximale de la base de données lorsque vous modifiez l'échelle de vos rôles de service.

- **Contrat de niveau de service Azure (SLA) :** le SLA Azure Compute garantit que, en déployant deux instances de rôle ou plus pour chaque rôle, l'accès à votre service cloud sera maintenu au minimum 99,95 % du temps. Des actions de détection et de correction seront également entreprises 99,9 % du temps au cours duquel le processus d'une instance de rôle n'est pas exécuté. Pour plus d'informations, consultez la page [Contrats de niveau de service][SLA].

[HTMonitorCloudServices]:http://azure.microsoft.com/ manage/services/cloud-services/how-to-monitor-a-cloud-service/
[SLA]: http://azure.microsoft.com/ support/legal/sla/
[CloudServicesDiagnostics]: http://azure.microsoft.com/ documentation/articles/cloud-services-dotnet-diagnostics/
[Comparison]: http://azure.microsoft.com/ documentation/articles/choose-web-site-cloud-service-vm/

<!--HONumber=45--> 
