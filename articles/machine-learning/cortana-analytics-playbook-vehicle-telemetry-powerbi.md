---
title: "Tableau de bord Power BI pour l’état des véhicules et les habitudes de conduite - Azure | Microsoft Docs"
description: "Utilisez les fonctionnalités de Cortana Intelligence pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: b7332aa2b2518bd926ee2221fd0129adcc25415b
ms.lasthandoff: 04/13/2017


---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Instructions de configuration du tableau de bord Power BI pour le modèle de la solution Vehicle Telemetry Analytics
Ce **menu** contient des liens vers les chapitres de ce manuel. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

La solution Vehicle Telemetry Analytics décrit la manière dont les concessions, constructeurs automobiles et compagnies d’assurance peuvent exploiter les fonctionnalités de Cortana Intelligence pour obtenir une visibilité en temps réel et prédictive sur l’état du véhicule et les habitudes de conduite, ce afin d’apporter des améliorations dans les domaines de l’expérience client, de la recherche et du développement, ainsi que des campagnes marketing. Ce document contient des instructions pas à pas sur la manière de configurer les rapports et le tableau de bord Power BI une fois la solution déployée dans votre abonnement. 

## <a name="prerequisites"></a>Conditions préalables
1. Déployez la solution Vehicle Telemetry Analytics en accédant à [https://gallery.cortanaintelligence.com/Solution/Vehicle-Telemetry-Analytics-9](https://gallery.cortanaintelligence.com/Solution/Vehicle-Telemetry-Analytics-9)  
2. [Installez Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3. Un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous n’avez pas d’abonnement Azure, commencez par un abonnement Azure gratuit
4. Compte Microsoft Power BI

## <a name="cortana-intelligence-suite-components"></a>Composants Cortana Intelligence Suite
Les services Cortana Intelligence suivants sont déployés dans votre abonnement parallèlement au modèle de solution Vehicle Telemetry Analytics.

* **Event Hubs** pour l’ingestion dans Azure de millions d’événements de télémétrie de véhicules.
* **STREAM ANALYTICS** , pour une visibilité en temps réel sur l’état des véhicules et une conservation de ces données dans un stockage à long terme afin d’enrichir les analyses par lots.
* **MACHINE LEARNING** , pour la détection d’anomalies en temps réel et le traitement par lots afin d’obtenir des informations prédictives.
* **HDInsight** est utilisé pour transformer les données à grande échelle
* **Data Factory** gère l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots.

**POWER BI** offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives. 

La solution utilise deux sources de données différentes : **jeu de données de diagnostics et de signaux des véhicules simulés** et **catalogue de véhicules**.

Un simulateur de télématique des véhicules est intégré à cette solution. Ce simulateur émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et au schéma de conduite à un moment donné dans le temps. 

Le catalogue de véhicules est un jeu de données de référence contenant un mappage VIN/modèle

## <a name="power-bi-dashboard-preparation"></a>Préparation du tableau de bord Power BI
### <a name="setup-power-bi-real-time-dashboard"></a>Configuration du tableau de bord Power BI en temps réel

**Démarrer l’application de tableau de bord en temps réel** Une fois le déploiement terminé, vous devez suivre les Instructions d’opération manuelle

* Téléchargez l’application de tableau de bord en temps réel RealtimeDashboardApp.zip et décompressez-le.
*  Dans le dossier décompressé, ouvrez le fichier de configuration d’application « RealtimeDashboardApp.exe.config », remplacez les appSettings pour les connexions de service EventHub, Stockage Blob et ML par les valeurs indiquées dans les Instructions d’opération manuelle, puis enregistrez vos modifications.
* Exécutez l’application RealtimeDashboardApp.exe. Une fenêtre de connexion s’affiche. Entrez vos informations d’identification Power BI valides, puis cliquez sur le bouton **Accepter**. Ensuite, l’application commence à s’exécuter.

   ![Connectez-vous à Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
   ![Autorisations du tableau de bord Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

* Connectez-vous au site web de Power BI, puis créez le tableau de bord en temps réel.

Vous êtes maintenant prêt à configurer le tableau de bord Power BI avec des visualisations enrichies pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite. Comptez de 45 minutes à une heure environ pour créer tous les rapports et configurer le tableau de bord. 

### <a name="configure-power-bi-reports"></a>Configurer les rapports Power BI
Les rapports en temps réel et le tableau de bord prennent environ 30 à 45 minutes. Accédez à [http://powerbi.com](http://powerbi.com) et connectez-vous.

![Connectez-vous à Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Un nouveau jeu de données est généré dans Power BI. Cliquez sur le jeu de données **ConnectedCarsRealtime** .

![Jeu de données Connected Cars en temps réel sélectionné](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Enregistrez le rapport vierge à l’aide de la combinaison **Ctrl + s**.

![Enregistrez le rapport vide](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Nommez le rapport *Vehicle Telemetry Analytics Real-time - Reports*.

![Nommez le rapport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Rapports en temps réel
Il existe trois rapports en temps réel dans cette solution :

1. Véhicules opérationnels
2. Véhicules nécessitant une maintenance
3. Statistiques relatives à l’état des véhicules

Vous pouvez choisir de configurer les trois rapports en temps réel, ou arrêter après l’étape de votre choix et passer à la section suivante de la configuration des rapports de traitement par lots. Nous vous recommandons de créer les trois rapports afin de visualiser toutes les informations de la fonction temps réel de la solution.  

### <a name="1-vehicles-in-operation"></a>1. Véhicules opérationnels
Double-cliquez sur la **Page 1** et renommez-la « Véhicules opérationnels ».  
    ![Connected Cars - Véhicules opérationnels](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

Sélectionnez **vin** dans **Champs** et choisissez **« Carte »** comme type de visualisation.  

Une visualisation sous forme de carte est créée, comme illustré ci-dessous.  
    ![Connected Cars - Sélectionner le numéro d’identification du véhicule](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez **Ville** et **vin** dans Champs. Choisissez la visualisation de type **« Carte »**. Faites glisser **vin** dans la zone de valeurs. Faites glisser **city** vers la zone **Legend**.   
    ![Connected Cars - Visualisation sous forme de carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

Sélectionnez la section **Format** dans **Visualisations**, puis cliquez sur **Titre** et modifiez le **texte** en le remplaçant par **« Véhicules opérationnels par ville »**.  
    ![Connected Cars - Véhicules opérationnels par ville](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

La visualisation finale doit être telle qu’illustrée ci-dessous.    
    ![Connected Cars - Visualisation finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez **Ville** et **vin**, puis modifiez le type de visualisation en lui affectant la valeur **Histogramme groupé**. Vérifiez le champ **Ville** dans la zone **Axe** et **vin** dans la zone **Valeur**.  

Triez le graphique par **« Nombre de vin »**.  
    ![Connected Cars - Nombre de numéros d’identification de véhicule](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

Modifiez le **titre** du graphique en le nommant **« Véhicules opérationnels par ville »**.  

Cliquez sur la section **Format**, puis sélectionnez **Couleurs des données**. Cliquez sur **« Activé »** dans **Afficher tout**.  
    ![Connected Cars - Afficher toutes les couleurs de données](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

Modifiez la couleur de chaque ville en cliquant sur l’icône de couleur.  
    ![Connected Cars - Modifier les couleurs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez la visualisation **Histogramme groupé** dans la zone Visualisations, faites glisser le champ **Ville** dans la zone **Axe**, **Modèle** dans la zone **Légende** et **vin** dans la zone **Valeur**.  
    ![Connected Cars - Histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Connected Cars - Rendu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

Réorganisez toutes les visualisations sur cette page comme indiqué dans la figure.  
    ![Connected Cars - Visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Vous avez correctement configuré le rapport en temps réel « Véhicules opérationnels ». Vous pouvez passer à la création du rapport en temps réel suivant, ou bien vous arrêter ici et configurer le tableau de bord. 

### <a name="2-vehicles-requiring-maintenance"></a>2. Véhicules nécessitant une maintenance
Cliquez sur ![Ajouter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pour ajouter un nouveau rapport, puis renommez-le **« Véhicules nécessitant une maintenance »**

![Connected Cars - Véhicules nécessitant une maintenance](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

Sélectionnez le champ **vin** et définissez le type de visualisation sur **Carte**.  
    ![Connected Cars - Visualisation du numéro d’identification du véhicule sous forme de carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

Le jeu de données contient un champ nommé « MaintenanceLabel ». Ce champ peut avoir une valeur de « 0 » ou « 1 ». Il est défini par le modèle Azure Machine Learning configuré dans le cadre de la solution et intégré avec la fonction temps réel. La valeur « 1 » indique un véhicule nécessitant une maintenance. 

Pour ajouter le filtre **Page Level** (Niveau page) afin d’afficher les données relatives aux véhicules ayant besoin d’une intervention de maintenance : 

1. Faites glisser le champ **« MaintenanceLabel »** dans **Filtres de niveau page**.  
   ![Connected Cars - Filtres au niveau de la page](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  
2. Cliquez sur le menu **Filtrage de base** situé en bas du filtre de niveau page MaintenanceLabel.  
   ![Connected Cars - Filtrage de base](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  
3. Affectez-lui la valeur de filtre **« 1 »**    
   ![Connected Cars - Valeur de filtre](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez **Histogramme groupé** dans la section Visualisations.  
![Connected Cars - Visualisation du numéro d’identification du véhicule sous forme de carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Connected Cars - Histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Faites glisser le champ **Modèle** dans la zone **Axe** et le champ **Vin** dans la zone **Valeur**. Triez ensuite la visualisation par **Nombre de vin**.  Modifiez le **titre** du graphique en le renommant **« Véhicules nécessitant une maintenance par modèle »**.  

Faites glisser les champs **vin** dans la zone **Saturation de la couleur** située dans la section **Champs** ![Champs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png)de l’onglet **Visualisation**.  
![Connected Cars - Saturation des couleurs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

Modifiez **Couleurs des données** dans les visualisations à partir de la section **Format**.  
Définissez **F2C812** comme couleur minimale.  
Définissez **FF6300** comme couleur maximale.  
![Connected Cars - Changements de couleur](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Connected Cars - Nouvelles couleurs de visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez **Histogramme groupé** dans la zone Visualisations, faites glisser le champ **vin** dans la zone **Valeur** et le champ **Ville** dans la zone **Axe**. Triez le graphique par **« Nombre de vin »**. Modifiez le **titre** du graphique en le renommant **« Véhicules nécessitant une maintenance par ville »**   
![Connected Cars - Véhicules nécessitant une maintenance par ville](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Dans la section Visualisations, sélectionnez la visualisation **Carte à plusieurs lignes**, puis faites glisser les champs **Modèle** et **vin** dans la zone **Champs**.  
![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

En réorganisant l’ensemble de la visualisation, le rapport final se présente comme suit :   
![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Vous avez correctement configuré le rapport en temps réel « Véhicules nécessitant une maintenance ». Vous pouvez passer à la création du rapport en temps réel suivant, ou bien vous arrêter ici et configurer le tableau de bord. 

### <a name="3-vehicles-health-statistics"></a>3. Statistiques relatives à l’état des véhicules
Cliquez sur ![Ajouter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pour ajouter un rapport, puis renommez-le **« Statistiques relatives à l’état des véhicules »**  

Dans la section Visualisations, sélectionnez la visualisation **Jauge**, puis faites glisser le champ **Vitesse** dans les zones **Valeur, Valeur minimum, Valeur maximum**.  
![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

Modifiez l’agrégation par défaut du champ **Vitesse** dans la zone **Valeur** en lui affectant la valeur **Moyenne**. 

Modifiez l’agrégation par défaut du champ **Vitesse** dans la zone **Minimum** en lui affectant la valeur **Minimum**.

Modifiez l’agrégation par défaut du champ **Vitesse** dans la zone **Maximum** en lui affectant la valeur **Maximum**.

![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

Modifiez le **titre de la jauge** en **« Vitesse moyenne »**. 

![Connected Cars - Jauge](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Vous pouvez également ajouter une **jauge** pour l’**huile moteur moyenne**, le **carburant moyen** et la **température moteur moyenne**.  

Modifiez l’agrégation par défaut des champs de chaque jauge comme indiqué ci-dessus dans la jauge **« Vitesse moyenne »** .

![Connected Cars - Jauges](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualisations, sélectionnez **Graphique en courbes et histogramme groupé**, puis faites glisser le champ **Ville** dans la zone **Axe partagé** et les champs **Vitesse**, **Pression des pneus et Huile moteur** dans la zone **Valeurs de colonne**, puis définissez le type d’agrégation sur **Moyenne**. 

Faites glisser le champ **Température moteur** dans la zone **Valeurs de ligne** et définissez le type d’agrégation sur **Moyenne**. 

![Connected Cars - Champs de visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Modifiez le **titre** du graphique en le renommant **« Vitesse moyenne, pression des pneus, huile moteur et température moteur »**.  

![Connected Cars - Champs de visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualisations, sélectionnez la visualisation **Compartimentage**, puis faites glisser le champ **Modèle** dans la zone **Groupe** et le champ **Probabilité de maintenance** dans la zone **Valeurs**.

Renommez le **titre** du graphique **« Modèles de véhicules nécessitant une maintenance »**.

![Connected Cars - Modifier le titre du graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualisations, sélectionnez **Graphique à barres empilées 100 %**, puis faites glisser le champ **Ville** dans la zone **Axe** et les champs **Probabilité de maintenance** et **Probabilité de rappel** dans la zone **Valeur**.

![Connected Cars - Ajouter une visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Cliquez sur **Format**, sélectionnez **Couleurs des données**, puis affectez la valeur **« F2C80F »** à la couleur **Probabilité de maintenance**.

Renommez le **titre** du graphique **« Probabilité de maintenance et de rappel de véhicules par ville »**.

![Connected Cars - Ajouter une visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualisations, sélectionnez la visualisation **Graphique en aires**, puis faites glisser le champ **Modèle** dans la zone **Axe** et les champs **Huile moteur, Pression des pneus, Vitesse et Probabilité de maintenance** dans la zone **Valeurs**. Définissez le type d’agrégation sur **« Moyenne »**. 

![Connected Cars - Modifier le type d’agrégation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Modifiez le titre du graphique en le renommant **« Moyenne d’huile moteur, de pression des pneus et de vitesse et probabilité de maintenance par modèle »**.

![Connected Cars - Modifier le titre du graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation :

1. Dans la section Visualisations, sélectionnez **Nuage de points** .
2. Faites glisser le champ **Modèle** dans les zones **Détails** et **Légende**.
3. Faites glisser le champ **Carburant** dans la zone **Axe des abscisses** et définissez l’agrégation sur **Moyenne**.
4. Faites glisser le champ **Température moteur** dans la zone **Axe des ordonnées** et définissez l’agrégation sur **Moyenne**.
5. Faites glisser le champ **vin** dans la zone **Taille**.

![Connected Cars - Ajouter une visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Modifiez le **titre** du graphique en le renommant **«  Moyennes de carburant et de température moteur par modèle »**.

![Connected Cars - Modifier le titre du graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

Le rapport final doit être similaire à ce qui suit :

![Connected Cars - Rapport final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Épingler les visualisations des rapports sur le tableau de bord en temps réel
Créez un tableau de bord vide en cliquant sur l’icône « plus » en regard des tableaux de bord. Vous pouvez le nommer « Vehicle Telemetry - Tableau de bord d’analyse ».

![Connected Cars - Tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Épinglez la visualisation des rapports ci-dessus au tableau de bord. 

![Connected Cars - Tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

Le tableau de bord doit se présenter comme illustré ci-après, lorsque les trois rapports sont créés et que les visualisations correspondantes sont épinglées à celui-ci. Si vous n’avez pas créé tous les rapports, l’apparence de votre tableau de bord peut différer. 

![Connected Cars - Tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Félicitations ! Vous avez correctement créé le tableau de bord en temps réel. Tandis que vous continuez pour exécuter CarEventGenerator.exe et RealtimeDashboardApp.exe, vous devez voir les mises à jour en direct sur le tableau de bord. La procédure suivante doit prendre environ 10 à 15 minutes.

## <a name="setup-power-bi-batch-processing-dashboard"></a>Configuration du tableau de bord de traitement par lots de Power BI
> [!NOTE]
> Une fois le déploiement effectué, comptez environ deux heures pour permettre au pipeline de traitement par lots de terminer son exécution et de traiter l’équivalent d’une année de données générées. Attendez donc que le traitement se termine avant de passer aux étapes suivantes. 
> 
> 

**Télécharger le fichier Power BI Designer**

* Un fichier Power BI Designer préconfiguré est inclus dans les Instructions d’opération manuelle du déploiement
* Recherchez 2. Installez le tableau de bord du traitement par lots de Power BI Vous pouvez télécharger le modèle Power BI pour le tableau de bord de traitement par lots appelé **ConnectedCarsPbiReport.pbix**.
* Enregistrez le fichier en local

**Configurer les rapports Power BI**

* Ouvrez le fichier de concepteur « **ConnectedCarsPbiReport.pbix** » à l’aide de Power BI Desktop. Le cas échéant, installez Power BI Desktop depuis [PowerBI Desktop install](http://www.microsoft.com/download/details.aspx?id=45331). 
* Cliquez sur **Modifier des requêtes**.

![Modifier une requête Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

* Double-cliquez sur la **Source**.

![Définir la source Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

* Mettez à jour la chaîne de connexion serveur avec le serveur SQL Azure que vous avez provisionné dans le cadre du déploiement.  Recherchez dans les Instructions d’opération manuelle sous 

    4. Base de données SQL Azure
    
    * Serveur : somethingsrv.database.windows.net
    * Base de données : connectedcar
    * Nom d’utilisateur : nom d’utilisateur
    * Mot de passe : vous pouvez gérer votre mot de passe SQL Server à partir du portail Azure

* Laissez la **base de données** en tant que *connectedcar*.

![Définir la base de données Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

* Cliquez sur **OK**.
* L’onglet **Informations d’identification Windows** est sélectionné par défaut. Basculez sur **Informations d’identification de la base de données** en cliquant sur l’onglet **Base de données** situé à droite.
* Indiquez le **nom d’utilisateur** et le **mot de passe** de votre base de données SQL Azure renseignés pendant la configuration du déploiement.

![Fournir les informations d’identification de la base de données](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

* Cliquez sur **Connexion**
* Répétez les étapes ci-dessus pour chacune des trois requêtes restantes présentes sur le volet de droite, puis mettez à jour les informations de connexion de la source de données.
* Cliquez sur **Fermer et charger**. Les jeux de données de fichier Power BI Desktop sont connectés à des tables de base de données SQL Azure.
* **Fermez** le fichier Power BI Desktop.

![Fermez Power BI Desktop](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

* Cliquez sur **Enregistrer** pour enregistrer les modifications. 

Vous avez maintenant configuré tous les rapports correspondant au traitement par lots dans la solution. 

## <a name="upload-to-powerbicom"></a>Chargez les éléments vers *powerbi.com*
1. Accédez au portail web Power BI à l’adresse http://powerbi.com et connectez-vous.
2. Cliquez sur **Get Data**  
3. Chargez le fichier Power BI Desktop.  
4. Pour lancer le chargement, cliquez sur **Get Data (Obtenir les données) -> Files Get (Obtention de fichiers) -> Fichier local**  
5. Accédez au fichier « **“**ConnectedCarsPbiReport.pbix** »**  
6. Une fois le fichier chargé, vous serez redirigé vers votre espace de travail Power BI.  

Un jeu de données, un rapport et un tableau de bord vide sont alors créés.  

Épinglez les graphiques au nouveau tableau de bord **Vehicle Telemetry - Tableau de bord d’analyse** dans **Power BI**. Cliquez sur le tableau de bord vide que vous avez créé ci-dessus et accédez à la section **Rapports**. Cliquez sur le rapport que vous venez de charger.  

![Vehicle Telemetry Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

**Notez que le rapport contient six pages :**  
Page 1 : densité de véhicules  
Page 2 : intégrité des véhicules en temps réel  
Page 3 : véhicules utilisés de manière intensive   
Page 4 : véhicules rappelés  
Page 5 : véhicules économes en carburant  
Page 6 : logo de Contoso  

![Connected Cars Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

**À partir de la Page 3**, épinglez les éléments suivants :  

1. Nombre de vin  
   ![Connected Cars Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 
2. Véhicules utilisés de manière intensive par modèle – Graphique en cascade   
   ![Vehicle Telemetry - Épingler des graphiques 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**À partir de la Page 5**, épinglez les éléments suivants : 

1. Nombre de vin    
   ![Vehicle Telemetry - Épingler des graphiques 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2. Véhicules économes en carburant par modèle : histogramme groupé   
   ![Vehicle Telemetry - Épingler des graphiques 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**À partir de la Page 4**, épinglez les éléments suivants :  

1. Nombre de vin  
   ![Vehicle Telemetry - Épingler des graphiques 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 
2. Véhicules rappelés par ville, modèle : Compartimentage   
   ![Vehicle Telemetry - Épingler des graphiques 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**À partir de la Page 6**, épinglez les éléments suivants :  

1. Logo Contoso Motors   
   ![Vehicle Telemetry - Épingler des graphiques 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organiser le tableau de bord**  

1. Accédez au tableau de bord.
2. Placez le curseur sur chaque graphique et renommez-les selon les noms indiqués dans l’image complète du tableau de bord ci-dessous. Déplacez également les graphiques de manière à obtenir un tableau de bord similaire à la capture ci-dessous.  
   ![Vehicle Telemetry - Organiser le tableau de bord 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
   ![Vehicle Telemetry Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. Si vous avez créé tous les rapports comme indiqué dans ce document, le tableau de bord terminé final doit ressembler à la figure suivante. 

![Vehicle Telemetry - Organiser le tableau de bord 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Félicitations ! Vous avez correctement créé les rapports et le tableau de bord pour obtenir des informations en temps réel, prédictives et par lots sur l’état des véhicules et les habitudes de conduite.  

