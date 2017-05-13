---
title: "Intégration de Service Map avec System Center Operations Manager | Microsoft Docs"
description: "Carte de service est une solution OMS (Operations Management Suite) qui détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Cet article décrit l’utilisation de Service Map pour créer automatiquement des diagrammes d’application distribuée dans Operations Manager."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0b710c338be3a2c2fde6bba43173f7c5f480e357
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---

# <a name="service-map-integration-with-system-center-operations-manager"></a>Intégration de Service Map avec System Center Operations Manager
  > [!NOTE]
  > Cette fonctionnalité est disponible en version préliminaire privée et ne doit donc pas être utilisée sur les systèmes de production.
  > 
  
Operations Management Suite Service Map détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Service Map vous permet d’afficher les serveurs comme des systèmes interconnectés qui fournissent des services critiques. Carte de service affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée à TCP, sans configuration requise autre que l’installation d’un agent. Pour plus d’informations, consultez la [documentation de Service Map](operations-management-suite-service-map.md).

Avec cette intégration entre Service Map et System Center Operations Manager, vous pouvez créer automatiquement des diagrammes d’application distribuée dans Operations Manager basés sur des cartes de dépendance dynamique dans Service Map.

## <a name="prerequisites"></a>Composants requis
* Un groupe d’administration d’Operations Manager qui gère un ensemble de serveurs.
* Un espace de travail Operations Management Suite avec la solution Service Map activée.
* Un ensemble de serveurs (au moins un) gérés par Operations Manager et envoyant des données à Service Map. Les serveurs Windows et Linux sont pris en charge.
* Un principal de service disposant d’un accès à l’abonnement Azure associé à l’espace de travail Operations Management Suite. Pour plus d’informations, consultez l’article [Créer un principal du service](#creating-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installation du pack d’administration de Service Map
Vous activez l’intégration entre Operations Manager et Service Map en important le groupement de packs d’administration Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Le groupe contient les packs d’administration suivants :
* Microsoft Service Map - Vues de l’application
* Microsoft System Center Service Map - Interne
* Microsoft System Center Service Map - Valeurs de remplacement
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>Configuration de l’intégration de Service Map
Après avoir installé le pack d’administration Service Map, vous trouverez un nouveau nœud, **Service Map**, dans le volet **Administration** **d’Operations Management Suite**. 

Pour configurer l’intégration de Service Map, procédez comme suit :

1. Cliquez sur **Ajouter un espace de travail** dans le volet de **vue d’ensemble de Service Map** pour ouvrir l’Assistant Configuration.  

    ![Vue d’ensemble de Service Map](media/oms-service-map/scom-configuration.png)

2. Dans la fenêtre **Configuration des connexions**, entrez l’ID ou le nom du client, l’ID d’application (aussi appelé nom d’utilisateur ou ID client) et le mot de passe du principal de service, puis cliquez sur **Suivant**. Pour plus d’informations, consultez l’article [Créer un principal du service](#creating-a-service-principal).

    ![Fenêtre Configuration des connexions](media/oms-service-map/scom-config-spn.png)

3. Dans la fenêtre **Sélection d’un abonnement**, sélectionnez l’abonnement Azure, le groupe de ressources Azure (celui qui contient l’espace de travail Operations Management Suite) et l’espace de travail Operations Management Suite, puis cliquez sur **Suivant**.

    ![Espace de travail de configuration d’Operations Manager](media/oms-service-map/scom-config-workspace.png)

4. Dans la fenêtre **Sélection du serveur**, vous configurez le groupe de serveurs Service Map avec les serveurs que vous voulez synchroniser entre Operations Manager et Service Map. Cliquez sur **Ajouter/Supprimer des serveurs**.   
    
    Pour que l’intégration crée un diagramme d’application distribuée pour un serveur, le serveur doit être :

    * Géré par Operations Manager.
    * Géré par Service Map.
    * Répertorié dans le groupe de serveurs Service Map.

    ![Groupe de configuration d’Operations Manager](media/oms-service-map/scom-config-group.png)

5. Facultatif : sélectionnez le pool de ressources du serveur d’administration pour communiquer avec Operations Management Suite et cliquez sur **Ajouter un espace de travail**.

    ![Pool de ressources de configuration d’Operations Manager](media/oms-service-map/scom-config-pool.png)

    La configuration et l’inscription de l’espace de travail Operations Management Suite peuvent prendre plusieurs minutes. Une fois qu’il est configuré, Operations Manager lance la première synchronisation de Service Map à partir d’Operations Management Suite.

    ![Pool de ressources de configuration d’Operations Manager](media/oms-service-map/scom-config-success.png)

    >[!NOTE]
    >L’intervalle de synchronisation par défaut est défini sur 60 minutes. Vous pouvez configurer des valeurs de remplacement afin de modifier l’intervalle de synchronisation. Vous pouvez également ajouter manuellement des serveurs au groupe de serveurs Service Map, via le panneau **Création**. Pour ce faire, sélectionnez **Groupes**, puis recherchez **Groupe de serveurs Service Map**. Les mappages de serveur de ces serveurs sont synchronisés lors de la prochaine synchronisation, selon l’intervalle de synchronisation configuré.

## <a name="monitor-service-map"></a>Surveillance de Service Map
Une fois l’espace de travail Operations Management Suite connecté, un nouveau dossier, Service Map, s’affiche dans le panneau **Analyse** de la console Operations Manager.

![Panneau Analyse d’Operations Manager](media/oms-service-map/scom-monitoring.png)

Le dossier Service Map possède trois nœuds :
* **Alertes actives** : répertorie toutes les alertes actives sur la communication entre Operations Manager et la solution Service Map dans Operations Management Suite.

    >[!NOTE]
    >Ces alertes ne correspondent pas aux alertes Operations Management Suite qui sont signalées dans Operations Manager.

* **Serveurs** : répertorie les serveurs analysés configurés pour la synchronisation à partir de Service Map.

    ![Panneau Analyse des serveurs d’Operations Manager](media/oms-service-map/scom-monitoring-servers.png)

* **Vues de dépendance au serveur** : répertorie tous les serveurs synchronisés à partir de Service Map. Vous pouvez cliquer sur n’importe quel serveur pour afficher son diagramme d’application distribuée.

    ![Diagramme d’application distribuée Operations Manager](media/oms-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Modification ou suppression de l’espace de travail
Vous pouvez modifier ou supprimer l’espace de travail configuré via le volet de **vue d’ensemble de Service Map** (volet **Administration** > **Operations Management Suite** > **Service Map**). Vous ne pouvez configurer qu’un seul espace de travail Operations Management Suite pour l’instant.

![Volet de modification de l’espace de travail d’Operations Manager](media/oms-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configuration des règles et des valeurs de remplacement
Une règle _Microsoft.SystemCenter.ServiceMapImport.Rule_ est créée pour extraire régulièrement des informations de Service Map. Pour modifier les intervalles de synchronisation, vous pouvez configurer des valeurs de remplacement pour la règle (volet **Création** > **Règles** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![Fenêtre des propriétés des valeurs de remplacement Operations Manager](media/oms-service-map/scom-overrides.png)

* **Enabled** : activer ou désactiver les mises à jour automatiques. 
* **IntervalMinutes** : réinitialiser la durée entre les mises à jour. L’intervalle par défaut est égal à une heure. Si vous voulez synchroniser les mappages de serveurs plus régulièrement, vous pouvez modifier la valeur.
* **TimeoutSeconds** : réinitialiser la durée avant l’expiration de la demande. 
* **TimeWindowMinutes** : réinitialiser la fenêtre de temps pour l’interrogation des données. Valeur par défaut : fenêtre de 60 minutes. La valeur maximale autorisée par Service Map est de 60 minutes.

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

La conception actuelle présente les problèmes et les limitations suivants :
* Bien que vous puissiez ajouter des serveurs au Groupe de serveurs Service Map manuellement via le volet **Création**, les mappages de ces serveurs sont synchronisés à partir de Service Map uniquement lors du prochain cycle de synchronisation. Le paramètre par défaut est de 60 minutes, mais vous pouvez le modifier. 
* Vous ne pouvez vous connecter qu’à un seul espace de travail Operations Management Suite.

## <a name="create-a-service-principal"></a>Créer un principal du service
Pour obtenir une documentation Azure officielle sur la création d’un principal de service, consultez :
* [Création d’un principal de service à l’aide de PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Création d’un principal de service à l’aide d’Azure CLI](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Création d’un principal de service à l’aide du portail Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Commentaires
Avez-vous des commentaires à nous transmettre à propos de la carte de service ou de sa documentation ? Consultez notre page [User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map) qui vous permet de nous suggérer des fonctionnalités ou de voter pour les suggestions en cours.

