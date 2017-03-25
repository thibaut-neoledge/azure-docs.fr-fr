---
title: "Intégration de Service Map avec System Center Operations Manager | Microsoft Docs"
description: "Carte de service est une solution OMS (Operations Management Suite) qui détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services.  Cet article fournit des informations sur l’utilisation de Service Map pour créer automatiquement des diagrammes d’application distribuée dans SCOM."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 389c01234acff068dc90f3cdfdc4916a9d76d244
ms.lasthandoff: 03/09/2017


---

# <a name="service-map-integration-with-system-center-operations-manager-integration"></a>Intégration de Service Map avec System Center Operations Manager
  > [!NOTE]
  > Cette fonctionnalité est disponible en version préliminaire privée et ne doit donc pas être utilisée sur les systèmes de production.
  > 
  
Operations Management Suite (OMS) Service Map détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Elle vous permet d’afficher les serveurs comme des systèmes interconnectés qui fournissent des services critiques. Carte de service affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée à TCP, sans configuration requise autre que l’installation d’un agent.  Pour plus d’informations, consultez la [documentation de Service Map](operations-management-suite-service-map.md).

Avec cette intégration entre Service Map et System Center Operations Manager (SCOM), vous pouvez créer automatiquement des diagrammes d’application distribuée dans SCOM basés sur des cartes de dépendance dynamique dans Service Map.

## <a name="prerequisites"></a>Composants requis
1.    Un groupe d’administration SCOM gérant un ensemble de serveurs.
2.    Un espace de travail OMS avec la solution Service Map activée.
3.    Un ensemble de serveurs (au moins un) gérés par SCOM et envoyant des données à Service Map.  Les serveurs Windows et Linux sont pris en charge.
4.    Un principal de service disposant d’un accès à l’abonnement Azure associé à l’espace de travail OMS.  [Plus d'informations sur la création d’un principal de service](#creating-a-service-principal).

## <a name="installing-service-map-management-pack"></a>Installation du pack d’administration de Service Map
L’intégration entre SCOM et Service Map est activée en important le groupement de packs d’administration Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb).  Le groupe contient les packs d’administration suivants :
* Microsoft ServiceMap - Vues de l’application
* Microsoft System Center ServiceMap - Interne
* Microsoft System Center ServiceMap - Valeurs de remplacement
* Microsoft System Center ServiceMap

## <a name="configuring-the-service-map-integration"></a>Configuration de l’intégration de Service Map
1. Après avoir installé le pack d’administration ServiceMap, vous trouverez un nouveau nœud, Service Map, dans le volet Administration d’Operations Management Suite.
2. Cliquez sur « Ajouter un espace de travail » dans le volet de vue d’ensemble de Service Map pour ouvrir l’Assistant Configuration.

    ![SCOM - Assistant Configuration](media/oms-service-map/scom-configuration.png)

3. La première étape de l’Assistant est la configuration de la connexion qui vous permet d’entrer les informations pour votre principal de service Azure. Entrez l’ID ou le nom du client, l’ID d’application (ou le nom d’utilisateur ou l’ID client) et le mot de passe du principal de service.  [Plus d'informations sur la création d’un principal de service](#creating-a-service-principal).

    ![SCOM - Configurer le nom du principal de service](media/oms-service-map/scom-config-spn.png)

4. L’étape suivante consiste à sélectionner l’abonnement Azure, le groupe de ressources Azure (celui contenant l’espace de travail OMS) et l’espace de travail OMS.

    ![SCOM - Configurer l’espace de travail](media/oms-service-map/scom-config-workspace.png)

5. L’étape suivante consiste à configurer le groupe de serveurs Service Map avec les serveurs que vous souhaitez synchroniser entre SCOM et Service Map.  Cliquez sur le bouton Ajouter/Supprimer des serveurs… . Pour l’intégration visant à créer un diagramme d’application distribuée pour un serveur, celui-ci doit : 1) être géré par SCOM, 2) être géré par Service Map et 3) être répertorié dans le groupe de serveurs Service Map.

    ![SCOM - Configurer le groupe](media/oms-service-map/scom-config-group.png)

6. Facultatif : sélectionnez le pool de ressources du serveur d’administration pour communiquer avec OMS et cliquez sur « Ajouter un espace de travail ».

    ![SCOM - Configurer le pool de ressources](media/oms-service-map/scom-config-pool.png)

7. La configuration et l’inscription de l’espace de travail OMS prendra une minute. Une fois la configuration effectuée, SCOM lance la première synchronisation de Service Map à partir d’OMS.

    ![SCOM - Configurer le pool de ressources](media/oms-service-map/scom-config-success.png)

**Remarque :** l’intervalle de synchronisation par défaut est défini sur 60 minutes. Les utilisateurs peuvent configurer des valeurs de remplacement afin de modifier l’intervalle de synchronisation. Les utilisateurs peuvent également ajouter des serveurs au groupe de serveurs Service Map manuellement via le volet Création (volet Création --> Groupes, puis rechercher « Groupe de serveurs Service Map »). Les mappages de serveur de ces serveurs seront synchronisés lors de la prochaine synchronisation (selon l’intervalle de synchronisation configuré).

## <a name="monitoring-service-map"></a>Surveillance de Service Map
Une fois l’espace de travail OMS connecté, un nouveau dossier Service Map s’affiche dans le volet Surveillance de la console SCOM.
![SCOM - Surveillance](media/oms-service-map/scom-monitoring.png)

Le dossier Service Map possède trois nœuds :
### <a name="all-alerts"></a>All Alerts (Toutes les alertes) :
Il affiche toutes les alertes sur les communications entre SCOM et la solution Service Map dans OMS.

**Remarque :** il ne s’agit pas d’alertes OMS exposées dans SCOM.
### <a name="servers"></a>Serveurs :
Il indique la liste des serveurs analysés configurés pour la synchronisation à partir de Service Map.

![SCOM - Serveurs de surveillance](media/oms-service-map/scom-monitoring-servers.png)

### <a name="server-dependency-views"></a>Vues de dépendance du serveur :
Cette vue affiche la liste de tous les serveurs synchronisés à partir de Service Map. Les utilisateurs peuvent cliquer sur n’importe quel serveur pour afficher son diagramme d’application distribuée.

![Diagramme d’application distribuée SCOM](media/oms-service-map/scom-dad.png)

## <a name="editdelete-workspace"></a>Modifier/Supprimer un espace de travail :
Les utilisateurs peuvent modifier ou supprimer l’espace de travail configuré via le volet de vue d’ensemble de Service Map (volet Administration--> Operations Management Suite --> Service Map).  Vous ne pouvez configurer qu’un seul espace de travail OMS pour l’instant.

![SCOM - Modifier l’espace de travail](media/oms-service-map/scom-edit-workspace.png)

## <a name="configuring-rules-and-overrides"></a>Configuration des règles et des valeurs de remplacement :
Une règle **_Microsoft.SystemCenter.ServiceMapImport.Rule**_ est créée pour extraire régulièrement des informations de Service Map.  Les utilisateurs peuvent configurer des valeurs de remplacement de cette règle pour modifier l’intervalle de synchronisation.
Volet Création--> Règles --> Microsoft.SystemCenter.ServiceMapImport.Rule

![SCOM - Valeurs de remplacement](media/oms-service-map/scom-overrides.png)
* **Enabled** : activer/désactiver les mises à jour automatiques. 
* **IntervalSeconds** : intervalle entre les mises à jour.  Valeur par défaut : 1 heure. Les utilisateurs peuvent modifier la valeur ici s’ils souhaitent synchroniser les mappages de serveurs plus régulièrement.
* **TimeoutSeconds** : durée avant expiration de la demande. 
* **TimeWindowMinutes** : étendue de la requête pour les données.  Valeur par défaut : fenêtre de 60 minutes. La valeur maximale est 1 heure (valeur maximale autorisée par Service Map).

## <a name="known-issueslimitations"></a>Problèmes connus/Limitations :
Dans la conception actuelle :
1. Bien que les utilisateurs puissent ajouter des serveurs au « Groupe de serveurs Service Map » manuellement via le volet Création, les mappages de ces serveurs seront synchronisés à partir de Service Map uniquement lors du prochain cycle de synchronisation (60 minutes par défaut. Les utilisateurs peuvent modifier l’intervalle de synchronisation). 
2. Les utilisateurs peuvent se connecter à un seul espace de travail OMS.

## <a name="creating-a-service-principal"></a>Création d’un principal de service
Les liens suivants vous permettent d’accéder à la documentation officielle Azure présentant trois manières différentes de créer un principal de service.
* [Créer un principal de service avec PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Créer un principal de service avec l’interface de ligne de commande Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Créer un principal de service avec le portail Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Commentaires
Avez-vous des commentaires à nous transmettre à propos de la carte de service ou de sa documentation ?  Si c’est le cas, sachez que notre page [User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), vous permet de nous suggérer des fonctionnalités ou de voter pour les suggestions en cours.

