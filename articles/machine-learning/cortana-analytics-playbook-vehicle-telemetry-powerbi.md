<properties 
	pageTitle="Instructions de configuration du tableau de bord Power BI pour le modèle de solution Vehicle Telemetry Analytics | Microsoft Azure" 
	description="Utilisez les fonctionnalités de Cortana Intelligence pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="bradsev" />


# Instructions de configuration du tableau de bord Power BI pour le modèle de la solution Vehicle Telemetry Analytics

Ce **menu** contient des liens vers les chapitres de ce manuel.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


La solution Vehicle Telemetry Analytics décrit la manière dont les concessions, constructeurs automobiles et compagnies d’assurance peuvent exploiter les fonctionnalités de Cortana Intelligence pour obtenir une visibilité en temps réel et prédictive sur l’état du véhicule et les habitudes de conduite, ce afin d’apporter des améliorations dans les domaines de l’expérience client, de la recherche et du développement, ainsi que des campagnes marketing. Ce document contient des instructions pas à pas sur la manière de configurer les rapports et le tableau de bord Power BI une fois la solution déployée dans votre abonnement.


## Composants requis
1.	Déployez la solution Vehicle Telemetry Analytics via [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3)  
2.	[Installez Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3.	Un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous n’avez pas d’abonnement Azure, commencez par un abonnement Azure gratuit
4.	Compte Microsoft Power BI
	

## Composants Cortana Intelligence Suite
Les services Cortana Intelligence suivants sont déployés dans votre abonnement parallèlement au modèle de solution Vehicle Telemetry Analytics :

- **Event Hubs**, pour la réception dans Azure de millions d’événements de télémétrie associés aux véhicules.
- **Stream Analytics**, pour une visibilité en temps réel sur l’état des véhicules et une conservation de ces données dans un stockage à long terme afin d’enrichir les analyses par lots.
- **Machine Learning**, pour la détection d’anomalies en temps réel et le traitement par lots afin d’obtenir des informations prédictives.
- **HDInsight** est utilisé pour transformer les données à grande échelle
- **Data Factory** gère l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots.

**Power BI** offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives.

La solution utilise deux sources de données différentes : **jeu de données de diagnostics et de signaux des véhicules simulés** et **catalogue de véhicules**.

Un simulateur de télématique des véhicules est intégré à cette solution. Ce simulateur émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et au schéma de conduite à un moment donné dans le temps.

Le catalogue de véhicules est un jeu de données de référence contenant un mappage VIN/modèle


## Préparation du tableau de bord Power BI

### Déploiement

Une fois le déploiement terminé, tous ces composants devraient apparaître en vert dans le schéma ci-dessous.

- Cliquez sur la flèche en haut à droite des nœuds verts pour accéder aux services correspondants et confirmer s’ils ont ou non été déployés avec succès.
- Cliquez sur la flèche située en haut à droite du nœud Vehicle Telematics Simulator pour télécharger le package du simulateur de données. Enregistrez les fichiers en local et extrayez-les sur votre ordinateur. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

Vous êtes maintenant prêt à configurer le tableau de bord Power BI avec des visualisations enrichies pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite. Comptez de 45 minutes à une heure environ pour créer tous les rapports et configurer le tableau de bord.


### Configuration du tableau de bord Power BI en temps réel

**Générer les données simulées**

1. Sur votre ordinateur local, accédez au dossier où vous avez extrait le package Vehicle Telematics Simulator.
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)
2.	Exécutez l’application ***CarEventGenerator.exe***.
3.	Ce simulateur émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et au schéma de conduite à un moment donné dans le temps. Elle est publiée dans une instance Azure Event Hub qui est configurée dans le cadre de votre déploiement.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
	 
**Démarrer l’application de tableau de bord en temps réel**

Une application intégrée dans la solution permet de générer le tableau de bord en temps réel dans Power BI. Cette application est active sur une instance Event Hub dans laquelle Stream Analytics publie les événements en continu. Pour chaque événement reçu par cette application, les données sont traitées à l’aide du point de terminaison de notation Request-Response de Machine Learning, et le jeu de données généré est publié sur l’API push de Power BI pour être visualisé.

Pour télécharger l’application :

1.	Cliquez sur le nœud Power BI sur la vue schématique, puis cliquez sur le lien **Télécharger l’application de tableau de bord en temps réel** dans le volet des propriétés.![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard-new1.png)
2.	Extrayez l’application et enregistrez-la en local.
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.	Exécutez l’application **RealtimeDashboardApp.exe**.
4.	Entrez des informations d’identification Power BI valides, connectez-vous, puis cliquez sur **Accepter**.
	
	![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
	
	![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)


### Configurer les rapports Power BI
Les rapports en temps réel et le tableau de bord prennent environ 30 à 45 minutes. Accédez à [http://powerbi.com](http://powerbi.com) et connectez-vous.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Un nouveau jeu de données est généré dans Power BI. Cliquez sur le jeu de données **ConnectedCarsRealtime**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Enregistrez le rapport vierge à l’aide de la combinaison **Ctrl + s**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Nommez le rapport *Vehicle Telemetry Analytics Real time - Reports*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## Rapports en temps réel
Il existe 3 rapports en temps réel dans cette solution :

1.	Véhicules opérationnels
2.	Véhicules nécessitant une maintenance
3.	Statistiques relatives à l’état des véhicules

Vous pouvez choisir de configurer les 3 rapports en temps réel, ou arrêter après l’étape de votre choix et passer à la section suivante de la configuration des rapports de traitement par lots. Nous vous recommandons de créer les 3 rapports afin de visualiser toutes les informations de la fonction temps réel de la solution.

### 1\. Véhicules opérationnels
  
Double-cliquez sur la **Page 1** et renommez-la « Véhicules opérationnels ».
![Connected Cars - Véhicules opérationnels](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)

Sélectionnez **vin** dans **Champs** et choisissez **« Carte »** comme type de visualisation.

Une visualisation sous forme de carte est créée, comme illustré ci-dessous.
![Connected Cars - Sélectionner le numéro d’identification du véhicule](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez **Ville** et **vin** dans Champs. Choisissez la visualisation de type **« Carte »**. Faites glisser **vin** dans la zone de valeurs. Faites glisser **city** vers la zone **Legend**.
![Connected Cars - Visualisation sous forme de carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
Sélectionnez la section **Format** dans **Visualisations**. Cliquez sur **Titre** et modifiez le texte en le remplaçant par **« Véhicules opérationnels par ville »**.
![Connected Cars - Véhicules opérationnels par ville](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)

La visualisation finale doit être telle qu’illustrée ci-dessous.
![Connected Cars - Visualisation finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez **Ville** et **vin**, puis modifiez le type de visualisation en le définissant sur **Histogramme groupé**. Vérifiez le champ **Ville** dans la zone **Axe** et **vin** dans la zone **Valeur**.

Triez le graphique par **« Nombre de vin »**.
![Connected Cars - Nombre de numéros d’identification de véhicule](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)

Modifiez le **titre** du graphique en le nommant **« Véhicules opérationnels par ville »**.

Cliquez sur la section **Format**, puis sélectionnez **Couleurs des données**. Cliquez sur **«Activé »** dans **Afficher tout**.
![Connected Cars - Afficher toutes les couleurs de données](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)

Modifiez la couleur de chaque ville en cliquant sur l’icône de couleur.
![Connected Cars - Modifier les couleurs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez la visualisation **Histogramme groupé** dans la zone Visualisations, faites glisser le champ **Ville** dans la zone **Axe**, **Modèle** dans la zone **Légende** et **vin** dans la zone **Valeur**.
![Connected Cars - Histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png) ![Connected Cars - Rendu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
Réorganisez toutes les visualisations sur cette page comme indiqué dans la figure.
![Connected Cars - Visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Vous avez correctement configuré le rapport en temps réel « Véhicules opérationnels ». Vous pouvez passer à la création du rapport en temps réel suivant, ou bien vous arrêter ici et configurer le tableau de bord.

### 2\. Véhicules nécessitant une maintenance
  
Cliquez sur ![Ajouter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pour ajouter un nouveau rapport, puis renommez-le **« Véhicules nécessitant une maintenance »**.

![Connected Cars - Véhicules nécessitant une maintenance](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)

Sélectionnez le champ **vin** et définissez le type de visualisation sur **Carte**.
![Connected Cars - Visualisation du numéro d’identification du véhicule sous forme de carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)

Le jeu de données contient un champ nommé « MaintenanceLabel ». Ce champ peut avoir une valeur de « 0 » ou « 1 ». Il est défini par le modèle Azure Machine Learning configuré dans le cadre de la solution et intégré avec la fonction temps réel. La valeur « 1 » indique un véhicule nécessitant une maintenance.

Nous allons ajouter le filtre **Niveau page** pour afficher les données relatives aux véhicules ayant besoin d’une intervention de maintenance.

1. Faites glisser le champ **« MaintenanceLabel »** dans **Filtres de niveau page**.
![Connected Cars - Filtres au niveau de la page](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. Cliquez sur le menu **Filtrage de base** situé en bas du filtre niveau page MaintenanceLabel.
![Connected Cars - Filtrage de base](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)

3.  Affectez-lui la valeur de filtre **« 1 »**.
![Connected Cars - Valeur de filtre](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)


Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez **Histogramme groupé** dans la section Visualisations.
![Connected Cars - Visualisation du numéro d’identification du véhicule sous forme de carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png) ![Connected Cars - Histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Faites glisser le champ **Modèle** dans la zone **Axe** et le champ **Vin** dans la zone **Valeur**. Triez ensuite la visualisation par **Nombre de vin**. Modifiez le **titre** du graphique en le renommant **« Véhicules nécessitant une maintenance par modèle »**.

Faites glisser les champs **vin** dans la zone **Saturation de la couleur** située dans la section **Champs** ![Champs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) de l’onglet **Visualisation**.
![Connected Cars - Saturation des couleurs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)

Dans les visualisations, modifiez le champ **Couleurs des données** à partir de la section **Format**. Définissez la couleur minimale sur : **F2C812**. Définissez la couleur maximale sur : **FF6300**.
![Connected Cars - Changements de couleur](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png) ![Connected Cars - Nouvelles couleurs de visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez **Histogramme groupé** dans la zone Visualisations, faites glisser le champ **vin** dans la zone **Valeur** et le champ **Ville** dans la zone **Axe**. Triez le graphique par **« Nombre de vin »**. Modifiez le **titre** du graphique en le renommant **« Véhicules nécessitant une maintenance par ville »**.
![Connected Cars - Véhicules nécessitant une maintenance par ville](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualisations, sélectionnez la visualisation **Carte à plusieurs lignes**, puis faites glisser les champs **Modèle** et **vin** dans la zone **Champs**.
![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)

Réorganisez toutes les visualisations. Le rapport final doit être similaire à ce qui suit :
![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)

Vous avez correctement configuré le rapport en temps réel « Véhicules nécessitant une maintenance ». Vous pouvez passer à la création du rapport en temps réel suivant, ou bien vous arrêter ici et configurer le tableau de bord.

### 3\. Statistiques relatives à l’état des véhicules
  
Cliquez sur ![Ajouter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pour ajouter un rapport, puis renommez-le **« Statistiques relatives à l’état des véhicules »**.

Dans la section Visualisations, sélectionnez la visualisation **Jauge**, puis faites glisser le champ **Vitesse** dans les zones **Valeur, Valeur minimum, Valeur maximum**.
![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)

Modifiez l’agrégation par défaut du champ **Vitesse** dans la zone **Valeur** en la définissant sur **Moyenne**.

Modifiez l’agrégation par défaut du champ **Vitesse** dans la zone **Minimum** en la définissant sur **Minimum**.

Modifiez l’agrégation par défaut du champ **Vitesse** dans la zone **Maximum** en la définissant sur **Maximum**.

![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)

Modifiez le **titre de la jauge** en **« Vitesse moyenne »**.
 
![Connected Cars - Jauge](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Vous pouvez également ajouter une **jauge** pour l’**huile moteur moyenne**, le **carburant moyen** et la **température moteur moyenne**.

Modifiez l’agrégation par défaut des champs de chaque jauge comme indiqué ci-dessus dans la jauge **« Vitesse moyenne »**.

![Connected Cars - Jauges](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualisations, sélectionnez **Graphique en courbes et histogramme groupé**, puis faites glisser le champ **Ville** dans la zone **Axe partagé** et les champs **Vitesse**, **Pression des pneus et Huile moteur** dans la zone **Valeurs de colonne**, puis définissez le type d’agrégation sur **Moyenne**.

Faites glisser le champ **Température moteur** dans la zone **Valeurs de ligne** et définissez le type d’agrégation sur **Moyenne**.

![Connected Cars - Champs de visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Modifiez le **titre** du graphique en le renommant **« Vitesse moyenne, pression des pneus, huile moteur et température moteur »**.

![Connected Cars - Champs de visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualisations, sélectionnez la visualisation **Compartimentage**, puis faites glisser le champ **Modèle** dans la zone **Groupe** et le champ **Probabilité de maintenance** dans la zone **Valeurs**.

Renommez le **titre** du graphique **« Modèles de véhicules nécessitant une maintenance »**

![Connected Cars - Modifier le titre du graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualisations, sélectionnez la visualisation **Graphique à barres empilées 100 %**, puis faites glisser le champ **Ville** dans la zone **Axe** et les champs **Probabilité de maintenance** et **Probabilité de rappel** dans la zone **Valeur**.

![Connected Cars - Ajouter une visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Cliquez sur **Format**, sélectionnez **Couleurs des données**, puis définissez la couleur **Probabilité de maintenance** sur la valeur **« F2C80F »**.

Renommez le **titre** du graphique **« Probabilité de maintenance et de rappel de véhicules par ville »**

![Connected Cars - Ajouter une visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualisations, sélectionnez la visualisation **Graphique en aires**, puis faites glisser le champ **Modèle** dans la zone **Axe** et les champs **Huile moteur, Pression des pneus, Vitesse et Probabilité de maintenance** dans la zone **Valeurs**. Définissez le type d’agrégation sur **« Moyenne »**.

![Connected Cars - Modifier le type d’agrégation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Modifiez le titre du graphique en le renommant **« Moyenne d’huile moteur, de pression des pneus et de vitesse et probabilité de maintenance par modèle »**.

![Connected Cars - Modifier le titre du graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

1. Dans la section Visualisations, sélectionnez **Nuage de points**.
2. Faites glisser le champ **Modèle** dans les zones **Détails** et **Légende**. 
3. Faites glisser le champ **Carburant** dans la zone **Axe des abscisses** et définissez l’agrégation sur « Moyenne ».
4. Faites glisser le champ **engineTemperature** dans la zone **Y Axis** et définissez l’agrégation sur **Average**.
5. Faites glisser le champ **vin** dans la zone **Taille**.

![Connected Cars - Ajouter une visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Modifiez le **titre** du graphique en le renommant **« Moyennes de carburant et de température moteur par modèle »**.

![Connected Cars - Modifier le titre du graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

Le rapport final doit être similaire à ce qui suit :

![Connected Cars - Rapport final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### Épingler les visualisations des rapports sur le tableau de bord en temps réel
  
Créez un tableau de bord vide en cliquant sur l’icône « plus » en regard des tableaux de bord. Vous pouvez le nommer « Vehicle Telemetry - Tableau de bord d’analyse ».

![Connected Cars - Tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Épinglez la visualisation des rapports ci-dessus au tableau de bord.
 
![Connected Cars - Tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

Le tableau de bord doit se présenter comme illustré ci-dessous, lorsque les 3 rapports sont créés et que les visualisations correspondantes sont épinglées à celui-ci. L’apparence de votre tableau de bord peut différer si vous n’avez pas créé tous les rapports.

![Connected Cars - Tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Félicitations ! Vous avez correctement créé le tableau de bord en temps réel. Tandis que vous continuez pour exécuter CarEventGenerator.exe et RealtimeDashboardApp.exe, vous devez voir les mises à jour en direct sur le tableau de bord. La procédure ci-dessous doit prendre environ 10 à 15 minutes.
 
##  Configuration du tableau de bord de traitement par lots de Power BI

Remarque : une fois le déploiement effectué, comptez environ 2 heures pour permettre au pipeline de traitement par lots de terminer son exécution et de traiter l’équivalent d’une année de données générées. Veuillez patienter avant de procéder aux étapes suivantes.

**Téléchargez le fichier de concepteur Power BI** • Un fichier de concepteur Power BI préconfiguré est inclus dans le cadre du déploiement • Cliquez sur le nœud Power BI sur la vue schématique et cliquez sur « Télécharger le fichier de concepteur Power BI » dans le volet des propriétés ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9.5-download-powerbi-designer.png)

• Enregistrez le fichier en local

**Configurez les rapports Power BI** • Ouvrez le fichier de concepteur « VehicleTelemetryAnalytics - Desktop Report.pbix » à partir de Power BI Desktop. Le cas échéant, installez Power BI Desktop depuis [PowerBI Desktop install](http://www.microsoft.com/download/details.aspx?id=45331).

• Cliquez sur **Modifier des requêtes**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

- Cliquez deux fois sur la **source**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- Mettez à jour la chaîne de connexion serveur avec le serveur SQL Azure que vous avez provisionné dans le cadre du déploiement. Cliquez sur le nœud SQL Azure sur le schéma et affichez le nom du serveur du volet de propriétés.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11.5-view-server-name.png)

- Laissez la **base de données** en tant que *connectedcar*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- Cliquez sur **OK**.
- L’onglet **Informations d’identification Windows** est sélectionné par défaut, basculez sur **Informations d’identification de la base de données** en cliquant sur l’onglet **Base de données** situé à droite.
- Indiquez le **nom d’utilisateur** et le **mot de passe** de votre base de données SQL Azure renseignés pendant la configuration du déploiement.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- Cliquez sur **Connecter**.
- Répétez les étapes ci-dessus pour chacune des 3 requêtes restantes présentes sur le volet de droite, puis mettez à jour les informations de connexion de la source de données.
- Cliquez sur **Fermer et charger**. Les jeux de données de fichier Power BI Desktop sont connectés à des tables de base de données SQL Azure.
- **Fermez** le fichier Power BI Desktop.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- Cliquez sur **Enregistrer** pour enregistrer les modifications. 
 
Vous avez maintenant configuré tous les rapports correspondant au traitement par lots dans la solution.


## Chargez les éléments vers *powerbi.com*.
 
1.	Accédez au portail web Power BI à l’adresse http://powerbi.com et connectez-vous.
2.	Cliquez sur Get Data.  
3.	Chargez le fichier Power BI Desktop.  
4.	Cliquez sur **Obtenir des données -> Obtention de fichiers -> Fichiers locaux file** pour lancer le chargement.  
5.	Accédez à **« VehicleTelemetryAnalytics – Desktop Report.pbix »**.  
6.	Une fois le fichier chargé, vous serez redirigé vers votre espace de travail Power BI.  

Un jeu de données, un rapport et un tableau de bord vide sont alors créés.
 

Épinglez des graphiques au tableau de bord existant **Vehicle Telemetry - Tableau de bord d’analyse** dans **Power BI**. Cliquez sur le tableau de bord vide que vous avez créé ci-dessus et accédez à la section **Rapports**. Cliquez sur le rapport que vous venez de charger.

![Vehicle Telemetry PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png)


**Notez que le rapport contient six pages :** Page 1 : Densité des véhicules Page 2 : État des véhicules en temps réel Page 3 : Véhicules utilisés de manière intensive Page 4 : Véhicules rappelés Page 5 : Véhicules économes en carburant Page 6 : Logo Contoso

![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**À partir de la Page 3**, épinglez les éléments suivants :
1.	Nombre de VIN ![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 2. 

2.	Véhicules utilisés de manière intensive par modèle – Graphique en cascade ![Vehicle Telemetry - Épingler des graphiques 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**À partir de la Page 5**, épinglez les éléments suivants :
1.	Count of vin ![Vehicle Telemetry - Épingler des graphiques 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2.	Véhicules économes en carburant par modèle : histogramme groupé ![Vehicle Telemetry - Épingler des graphiques 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**À partir de la Page 4**, épinglez les éléments suivants :

1.	Count of vin ![Vehicle Telemetry - Épingler des graphiques 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.	Véhicules rappelés par ville, modèle : Compartimentage ![Vehicle Telemetry - Épingler des graphiques 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)

**À partir de la Page 6**, épinglez les éléments suivants :

1.	Logo Contoso Motors ![Vehicle Telemetry - Épingler des graphiques 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organiser le tableau de bord**

1.	Accédez au tableau de bord.
2.	Placez le curseur sur chaque graphique et renommez-les selon les noms indiqués dans l’image complète du tableau de bord ci-dessous. Déplacez également les graphiques de manière à obtenir un tableau de bord similaire à la capture ci-dessous.
![Vehicle Telemetry - Organiser le tableau de bord 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) ![Vehicle Telemetry PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. Le tableau de bord terminé final doit ressembler à la version ci-dessous si vous avez créé tous les rapports comme indiqué dans ce document. 

![Vehicle Telemetry - Organiser le tableau de bord 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Félicitations ! Vous avez correctement créé les rapports et le tableau de bord pour obtenir des informations en temps réel, prédictives et par lots sur l’état des véhicules et les habitudes de conduite.

<!------HONumber=AcomDC_0406_2016-->