---
title: IT Service Management Connector dans OMS | Microsoft Docs
description: "Utilisez IT Service Management Connector pour surveiller et gérer de manière centralisée les éléments de travail ITSM dans OMS, et résoudre rapidement les problèmes éventuels."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: v-jysur
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0aa41bbc0e0135737d352553607f48a39757bcc3
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>Gérer de manière centralisée les éléments de travail ITSM à l’aide d’IT Service Management Connector (version préliminaire)

Vous pouvez utiliser IT Service Management Connector (ITSMC) dans OMS Log Analytics pour surveiller et gérer de manière centralisée les éléments de travail de vos produits/services ITSM.

IT Service Management Connector intègre vos produits et services de gestion des services informatiques (ITSM) existants avec OMS Log Analytics.  La solution offre une intégration bidirectionnelle avec les produits/services ITSM afin de fournir aux utilisateurs OMS une option pour créer des incidents, des alertes ou des événements dans une solution ITSM. Le connecteur importe également des données telles que les incidents et les demandes de modification à partir de la solution ITSM vers OMS Log Analytics.

IT Service Management Connector vous offre de nombreuses possibilités :

  - Surveiller et gérer de manière centralisée des éléments de travail pour les produits/services ITSM utilisés au sein de votre organisation.
  - Créer des éléments de travail ITSM (tels que des alertes, des événements et des incidents) dans ITSM à partir des alertes OMS et via la fonctionnalité de recherche dans les journaux.
  - Lire les incidents et les demandes de modification de votre solution ITSM et les mettre en corrélation avec les données de journal pertinentes dans l’espace de travail Log Analytics.
  - Identifier les événements inattendus et inhabituels et les résoudre avant même que les utilisateurs ne les signalent au support technique.
  - Importer des données d’éléments de travail dans Log Analytics et créer des rapports d’indicateurs de performance clés.  Grâce à ces rapports, vous pouvez identifier, analyser et agir sur plusieurs éléments importants tels que l’évaluation des programmes malveillants.
  - Afficher des tableaux de bord spécialement conçus pour fournir des informations plus détaillées sur les incidents, les demandes de modification et les systèmes concernés.
  - Résoudre plus rapidement les problèmes en mettant les données en corrélation avec d’autres solutions de gestion de l’espace de travail Log Analytics.


## <a name="prerequisites"></a>Composants requis

Pour importer les éléments de travail ITSM dans OMS Log Analytics, la solution requiert une connexion entre le connecteur IT Service Management Connector dans OMS et le produit/service ITSM à partir duquel vous importez les éléments de travail.


## <a name="configuration"></a>Configuration

Ajoutez la solution IT Service Management Connector dans votre espace de travail OMS en suivant la procédure décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).

Vignette IT Service Management Connector apparaissant dans la galerie de solutions :

![vignette du connecteur](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

Une fois ajoutée, la solution IT Service Management Connector s’affiche sous **OMS** > **Paramètres** > **Sources connectées**.

![ITSMC connecté](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

## <a name="management-packs"></a>Packs d’administration
Cette solution ne requiert aucun pack d’administration.

## <a name="connected-sources"></a>Sources connectées

Les produits/services ITSM suivants sont pris en charge par le connecteur IT Service Management Connector :

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>Utilisation de la solution

Une fois que vous avez connecté la solution OMS IT Service Management Connector à votre service ITSM, le service Log Analytics commence à collecter des données à partir des services/produits ITSM connectés.

> [!NOTE]
> - Les données importées par la solution IT Service Management Connector s’affichent dans Log Analytics en tant qu’événements nommés **ServiceDesk_CL**.
- Chaque événement contient un champ nommé **ServiceDeskWorkItemType_s** qui est défini sur Incident ou Demande de modification selon les données de l’élément de travail contenues dans l’événement **ServiceDesk_CL**.

Les informations suivantes présentent des exemples de données collectées par IT Service Management Connector :

> [!NOTE]
> Selon le type d’élément de travail importé dans Log Analytics, l’événement **ServiceDesk_CL** contient les champs suivants :

**Élément de travail :** **Incidents**  
ServiceDeskWorkItemType_s="Incident"

**Champs**

- ServiceDeskConnectionName
- ID du service d’assistance
- State
- Urgence
- Impact
- Priorité
- Escalade
- Créé par
- Résolu par
- Fermé par
- Source
- Affecté à
- Catégorie
- Intitulé
- Description
- Date de création
- Date de fermeture
- Date de résolution
- Date de dernière modification
- Ordinateur


**Élément de travail :** **Demandes de modification**

ServiceDeskWorkItemType_s="ChangeRequest"

**Champs**
- ServiceDeskConnectionName
- ID du service d’assistance
- Créé par
- Fermé par
- Source
- Affecté à
- Intitulé
- Type
- Catégorie
- State
- Escalade
- État conflictuel
- Urgence
- Priorité
- Risque
- Impact
- Affecté à
- Date de création
- Date de fermeture
- Date de dernière modification
- Date de la demande
- Date de début prévue
- Date de fin prévue
- Date de début du travail
- Date de fin du travail
- Description
- Ordinateur

Exemple d’écran Log Analytics pour les données ITSM :

![Écran Log Analytics](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT Service Management Connector – intégration avec d’autres solutions OMS

IT Service Management Connector prend actuellement en charge l’intégration avec la solution Carte de service.

La solution Carte de service détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Elle vous permet d’afficher les serveurs comme des systèmes interconnectés qui fournissent des services critiques. Carte de service affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée à TCP, sans configuration requise autre que l’installation d’un agent. Pour en savoir plus : [Carte de service](../operations-management-suite/operations-management-suite-service-map.md).

Grâce à cette intégration, vous pouvez afficher les éléments de service d’assistance créés dans les solutions ITSM comme indiqué dans l’exemple suivant :

![Solution intégrée ](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>Créer des éléments de travail ITSM pour des alertes OMS

Pour les alertes OMS, vous pouvez créer des éléments de travail associés dans les sources ITSM connectées.  Pour ce faire, procédez comme suit :

1. Dans la fenêtre **Recherche dans les journaux**, exécutez une requête de recherche dans les journaux pour afficher les données. Les résultats de la requête correspondent aux sources des éléments de travail.
2. Dans **Recherche dans les journaux**, cliquez sur **Alerte** pour ouvrir la page **Ajouter une règle d’alerte**.

    ![Écran Log Analytics](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. Dans la fenêtre **Ajouter une règle d’alerte**, spécifiez les champs **Nom**, **Gravité**, **Requête de recherche** et **Critères d’alerte** (fenêtre de temps/mesures métriques).
4. Sélectionnez **Oui** pour **Actions ITSM**.
5. Sélectionnez votre connexion ITSM dans la liste **Sélectionner une connexion**.
6. Spécifiez les informations requises.
7. Pour créer un élément de travail distinct pour chaque entrée de journal de cette alerte, cochez la case **Créer des éléments de travail distincts pour chaque entrée de journal**

    Ou

    laissez cette case décochée pour créer un seul élément de travail pour l’ensemble des entrées de journal associées à cette alerte.

7. Cliquez sur **Save**.

L’alerte OMS est créée sous **Alertes**. Les éléments de travail de la connexion ITSM correspondante sont créés lorsque les critères de l’alerte spécifiée sont remplis.

## <a name="create-itsm-work-items-from-oms-logs"></a>Créer des éléments de travail ITSM à partir de journaux OMS

Vous pouvez créer des éléments de travail dans les sources ITSM connectées à l’aide de la fonctionnalité de recherche dans les journaux d’OMS. Pour ce faire, procédez comme suit :

1. Sous **Recherche dans les journaux**, recherchez les données requises, sélectionnez les informations détaillées, puis cliquez sur **Créer un élément de travail**.

    La fenêtre **Créer un élément de travail ITSM** s’affiche :

    ![Écran Log Analytics](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   Ajoutez les informations suivantes :

  - **Titre de l’élément de travail** : titre de l’élément de travail.
  - **Description de l’élément de travail** : description du nouvel élément de travail.
  - **Ordinateur concerné** : nom de l’ordinateur sur lequel les données de journal ont été trouvées.
  - **Sélectionner une connexion** : connexion ITSM dans laquelle vous souhaitez créer cet élément de travail.
  - **Élément de travail** : type d’élément de travail.

3. Pour utiliser un modèle d’élément de travail existant pour un incident, cliquez sur **Oui** sous **Générer l’élément de travail en fonction du modèle**, puis cliquez sur **créer**.

    Ou,

    Cliquez sur **Non** si vous souhaitez fournir des valeurs personnalisées.

4. Indiquez les valeurs appropriées dans les zones de texte **Type de contact**, **Impact**, **Urgence**, **Catégorie** et **Sous-catégorie**, puis cliquez sur **Créer**.

L’élément de travail sera créé dans ITSM, que vous pouvez également afficher dans OMS.

## <a name="contact-us"></a>Nous contacter

Pour toute question ou tout commentaire à propos de la solution IT Service Management Connector, contactez-nous à l’adresse [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes
[Ajouter des produits/services ITSM à IT Service Management Connector](log-analytics-itsmc-connections.md).

