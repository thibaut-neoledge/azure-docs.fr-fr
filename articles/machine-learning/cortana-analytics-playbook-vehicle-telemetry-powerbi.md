<properties 
	pageTitle="Instructions de configuration du tableau de bord Power BI pour le modèle de solution Vehicle Telemetry Analytics | Microsoft Azure" 
	description="Utilisez les fonctions de Cortana Analytics pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite." 
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
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# Instructions de configuration du tableau de bord Power BI pour le modèle de solution Vehicle Telemetry Analytics

Ce **menu** contient des liens vers les chapitres de ce manuel.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


La solution Vehicle Telemetry Analytics décrit la manière dont les concessions automobiles, constructeurs automobiles et compagnies d’assurances peuvent exploiter les fonctionnalités de Cortana Analytics pour obtenir une visibilité en temps réel et prédictive sur l’état du véhicule et sur les habitudes de conduite, ce afin d’apporter des améliorations dans les domaines de l’expérience client, de la recherche et du développement, ainsi que des campagnes marketing. Ce document contient des instructions pas à pas sur la manière de configurer les rapports et le tableau de bord Power BI une fois la solution déployée dans votre abonnement.


## Composants requis
1.	Déployez la solution Vehicle Telemetry Analytics via [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3)  
2.	[Installez Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3.	Un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous ne possédez pas d’abonnement Azure, commencez par un abonnement Azure gratuit
4.	Compte Microsoft Power BI
	

## Composants de Cortana Analytics Suite
Les services Cortana Analytics suivants sont déployés dans votre abonnement parallèlement au modèle de solution Vehicle Telemetry Analytics :

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
	 
Démarrer l’application de tableau de bord en temps réel Une application intégrée dans la solution permet de générer le tableau de bord en temps réel dans Power BI. Cette application est active sur une instance Event Hub dans laquelle Stream Analytics publie les événements en continu. Pour chaque événement reçu par cette application, les données sont traitées à l’aide du point de terminaison de notation Request-Response de Machine Learning, et le jeu de données généré est publié sur l’API push de Power BI pour être visualisé. Pour télécharger l’application :

1.	Cliquez sur le nœud Power BI sur la vue schématique, puis cliquez sur le lien **Download Real-time Dashboard Application** dans le volet des propriétés.

2.	Extrayez les fichiers et enregistrez-les en local.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.	Exécutez l’application « RealtimeDashboardApp.exe ».
4.	Entrez des informations d’identification Power BI valides, connectez-vous, puis cliquez sur **Accept**.
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)
 
## Générer des rapports Power BI en temps réel 

### Configurer les rapports Power BI

Un nouveau jeu de données est généré dans Power BI. Cliquez sur le jeu de données **ConnectedCarsRealtime**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Enregistrez le rapport vierge à l’aide des touches de raccourci **Ctrl + s**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Nommez le rapport *Vehicle Telemetry Analytics Real time - Reports*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)


### Générer un rapport en temps réel : *Véhicules opérationnels*
  
Double-cliquez sur la **Page 1** et renommez-la « Véhicules opérationnels » ![Connected Cars - Véhicules opérationnels](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)

Sélectionnez **vin** dans **Fields** et choisissez **« Card »** comme type de visualisation.

Une visualisation sous forme de carte est créée, comme illustré ci-dessous. ![Connected Cars - Sélectionner le numéro d’identification du véhicule](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez **City** et **vin** dans Fields. Choisissez la visualisation de type **« Map »**. Faites glisser **vin** dans la zone de valeurs. Faites glisser **city** vers la zone **Legend**. ![Connected Cars - Visualisation sous forme de carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
Sélectionnez la section **format** dans **Visualizations**, cliquez sur **Title** et modifiez le texte en le remplaçant par **« Véhicules opérationnels par ville »**. ![Connected Cars - Véhicules opérationnels par ville](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)

La visualisation finale doit être telle qu’illustrée ci-dessous. ![Connected Cars - Visualisation finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez **City** et **vin**, puis modifiez le type de visualisation en le définissant sur **Clustered Column Chart**. Vérifiez le champ **City** dans la zone **Axis** et **vin** dans la zone **Value**.

Triez le graphique par **« Count of vin »** ![Connected Cars - Nombre de numéros d’identification de véhicule](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)

Modifiez le **titre** du graphique en le nommant **« Véhicules opérationnels par ville »**

Cliquez sur la section **Format**, puis sélectionnez **Data Colors**. Cliquez sur **« On »** dans **Show All** ![Connected Cars - Afficher toutes les couleurs de données](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)

Modifiez la couleur de chaque ville en cliquant sur l’icône de couleur. ![Connected Cars - Modifier les couleurs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez la visualisation **Clustered Column Chart** dans la zone Visualizations, faites glisser le champ **city** dans la zone **Axis**, **Model** dans la zone **Legend** et **vin** dans la zone **Value**. ![Connected Cars - Histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png) ![Connected Cars - Rendu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
Réorganisez toutes les visualisations sur cette page comme indiqué dans la figure. ![Connected Cars - Visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

### Ajouter un nouveau rapport : *Véhicules nécessitant une maintenance*
  
Cliquez sur ![Ajouter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pour ajouter un rapport, puis renommez-le **« Véhicules nécessitant une maintenance »**

![Connected Cars - Véhicules nécessitant une maintenance](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)

Sélectionnez le champ **vin** et définissez le type de visualisation sur **Card**. ![Connected Cars - Visualisation du numéro d’identification du véhicule sous forme de carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)

Le jeu de données contient un champ nommé **« MaintenanceLabel »**. Ce champ a une valeur de **« 0 »** et de **« 1 »**. La valeur « 1 » indique les véhicules ayant besoin d’une intervention de maintenance

Nous allons ajouter le filtre **Page Level** pour afficher les données relatives aux véhicules ayant besoin d’une intervention de maintenance.

1. Faites glisser le champ **« MaintenanceLabel »** dans **Page Level Filters**. ![Connected Cars - Filtres au niveau de la page](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. Cliquez sur le menu **Basic Filtering** situé en bas du filtre de MaintenanceLabel Page Level Filter. ![Connected Cars - Filtrage de base](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)

3.  Affectez-lui la valeur de filtre **« 1 »** ![Connected Cars - Valeur de filtre](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)
  
>[AZURE.NOTE]Nous pouvons définir un filtre au niveau du graphique, que nous appellerons **Visual Level Filters**. Ces filtres s’appliquent uniquement à une visualisation spécifique. Les filtres de niveau de page s’appliquent en revanche à toutes les visualisations d’un rapport.

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez **Clustered Column Chart** dans la section Visualizations ![Connected Cars - Visualisation du numéro d’identification du véhicule sous forme de carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png) ![Connected Cars - Histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Faites glissez le champ **Model** dans la zone **Axis** et le champ **Vin** dans la zone **Value**. Triez ensuite la visualisation par « **Count of vin** ». Modifiez le **titre** du graphique en le renommant **« Véhicules nécessitant une maintenance par modèle »**

Faites glisser les champs **vin** dans la zone **Color Saturation** situé dans la section **Fields** ![Champs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) de l’onglet **Visualization** ![Connected Cars - Saturation des couleurs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)

Dans les visualisations, modifiez le champ **Data Colors** à partir de la section **Format** Définissez la couleur minimale sur : **F2C812** Définissez la couleur maximale sur : **FF6300** ![Connected Cars - Changements de couleur](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png) ![Connected Cars - Nouvelles couleurs de visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez **Clustered Column Chart** dans la zone Visualizations, faites glisser le champ **vin** dans la zone **Value** et le champ **City** dans la zone **Axis**. Triez le graphique par **« Count of vin »**. Modifiez le **titre** du graphique en le renommant **« Véhicules nécessitant une maintenance par ville »** ![Connected Cars - Véhicules nécessitant une maintenance par ville](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Vizualisations, sélectionnez la visualisation **Multi Row Card**, puis faites glisser les champs **Model** et **vin** dans la zone **Fields**. ![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)

Réorganisez toutes les visualisations. Le rapport final doit être similaire à ce qui suit : ![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)

### Ajouter un nouveau rapport : *Statistiques relatives à l’état des véhicules*
  
Cliquez sur ![Ajouter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pour ajouter un rapport, puis renommez-le **« Statiques relatives à l’état des véhicules »**

Dans la section Vizualisations, sélectionnez la visualisation **Gauge**, puis faites glisser le champ **Speed** dans les zones **Value, Minimum Value, Maximum Value**. ![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)

Modifiez l’agrégation par défaut du champ **Speed** dans la zone **Value** en la définissant sur **Average**

Modifiez l’agrégation par défaut du champ **Speed** dans la zone **Minimum** en la définissant sur **Minimum**

Modifiez l’agrégation par défaut du champ **Speed** dans la zone **Maximum** en la définissant sur **Maximum**

![Connected Cars - Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)

Modifiez le **titre de la jauge** en **« Vitesse moyenne »**
 
![Connected Cars - Jauge](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Vous pouvez également ajouter une **jauge** pour l’**huile moteur moyenne**, le **carburant moyen** et la **température moteur moyenne**.

Modifier l’agrégation par défaut des champs de chaque jauge comme indiqué ci-dessus dans la jauge **« Vitesse moyenne »**.

![Connected Cars - Jauges](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualizations, sélectionnez **Line and Clustered Column Chart**, puis faites glisser le champ **City** dans la zone **Shared Axis** et les champs **speed**, **tirepressure et engineoil** dans la zone **Column Values**, puis définissez le type d’agrégation sur **Average**.

Faites glisser le champ **engineTemperature** dans la zone **Line Values** et définissez le type d’agrégation sur **Average**.

![Connected Cars - Champs de visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Renommez le **titre** du graphique **« Vitesse moyenne, pression des pneus, huile moteur et température moteur »**

![Connected Cars - Champs de visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualizations, sélectionnez la visualisation **Treemap**, puis faites glisser le champ **Model** dans la zone **Group** et le champ **MaintenanceProbability** dans la zone **Values**.

Renommez le **titre** du graphique **« Modèles de véhicules nécessitant une maintenance »**

![Connected Cars - Modifier le titre du graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualizations, sélectionnez la visualisation **100% Stacked Bar Chart**, puis faites glisser le champ **city** dans la zone **Axis** et les champs **MaintenanceProbability** et **RecallProbability** dans la zone **Value**.

![Connected Cars - Ajouter une visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Cliquez sur **Format**, sélectionnez **Data Colors**, puis définissez la couleur **MaintenanceProbability** sur la valeur **« F2C80F »**.

Renommez le **titre** du graphique **« Probabilité de maintenance et de rappel de véhicules par ville »**

![Connected Cars - Ajouter une visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Dans la section Visualizations, sélectionnez la visualisation **Area Chart**, puis faites glisser le champ **Model** dans la zone **Axis** et les champs **engineOil, tirepressure, speed et MaintenanceProbability** dans la zone **Values**. Définissez le type d’agrégation sur **« Average »**.

![Connected Cars - Modifier le type d’agrégation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Renommez le graphique pour **« Moyenne d’huile moteur, de pression des pneus et de vitesse et probabilité de maintenance par modèle »**.

![Connected Cars - Modifier le titre du graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

1. Dans la section Visualizations, sélectionnez **Scattered Chart**.
2. Faites glisser le champ **Model** dans les zones **Details** et **Legend**. 
3. Faites glisser le champ **fuel** dans la zone **X Axis** et définissez l’agrégation sur « Average ».
4. Faites glisser le champ **engineTemperature** dans la zone **Y Axis** et définissez l’agrégation sur **Average**.
5. Faites glisser le champ **vin** dans la zone **Size**.

![Connected Cars - Ajouter une visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Renommez le **titre** du graphique **« Moyennes de carburant et de température moteur par modèle »**

![Connected Cars - Modifier le titre du graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

Le rapport final doit être similaire à ce qui suit :

![Connected Cars - Rapport final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### Épingler les visualisations des rapports sur le tableau de bord en temps réel
  
Épinglez la visualisation des rapports ci-dessus sur le tableau de bord en temps réel que nous avons créé dans la section 2 - **Configuration du tableau de bord Power BI**.

Le tableau de bord doit être tel qu’illustré ci-dessous.

![Connected Cars - Tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)


##  Configuration du tableau de bord de traitement par lots de Power BI

>[AZURE.ALERT]Comptez environ 2 heures pour permettre au pipeline de traitement par lots de terminer son exécution. Veuillez patienter avant de procéder aux étapes suivantes.

**Téléchargez le fichier de concepteur Power BI** • Un fichier de concepteur Power BI est inclus dans le cadre du déploiement • Cliquez sur le nœud Power BI sur la vue schématique et cliquez sur « Download the Power BI designer file » dans le volet des propriétés • Enregistrez le fichier en local

**Configurez les rapports Power BI** • Ouvrez le fichier de concepteur « VehicleTelemetryAnalytics - Desktop Report.pbix » à partir de Power BI Desktop. Le cas échéant, installez Power BI Desktop à l’adresse http://www.microsoft.com/fr-FR/download/details.aspx?id=45331 • Cliquez sur **Edit Queries**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

[AZURE.NOTE]Le **nom/mot de passe** de votre base de données SQL Azure correspond à celui que vous avez renseigné pendant l’installation du déploiement

- Cliquez deux fois sur la **Source**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- Mettez à jour la chaîne de connexion au serveur
- Définissez la **base de données** en tant que *connectedcar*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- Cliquez sur **OK**.
- L’onglet **Windows credential** est sélectionné par défaut, basculez sur **Database credentials** en cliquant sur l’onglet **Database** situé à droite.
- Indiquez le **nom d’utilisateur** et le **mot de passe**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- Cliquez sur **Connect**.
- Répétez les étapes ci-dessus pour chacune des 3 requêtes restantes présentes sur le volet de droite, puis mettez à jour les informations de connexion de la source de données.
- Cliquez sur **Close and Load**. Les jeux de données de fichier Power BI Desktop sont connectés à des tables de base de données SQL Azure.
- **Fermez** le fichier Power BI Desktop.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- Cliquez sur **Save** pour enregistrer les modifications. 
 
**Chargement sur *powerbi.com*** Accédez à powerbi.com et connectez-vous. ![Vehicle Telemetry PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/15-vehicle-telemetry-powerbi-site.png)

1.	Cliquez sur Get Data.  
2.	Chargez le fichier Power BI Desktop.  
3.	Cliquez sur **Get Data -> Files Get -> Local file** pour lancer le chargement.  
4.	Accédez à **« Connected Cars – Desktop Report.pbix »**  
5.	Une fois le fichier chargé, vous serez redirigé vers votre espace de travail Power BI.  

	Un jeu de données, un rapport et un tableau de bord vide sont alors créés.

	>**Remarque :** vous devrez peut-être créer un tableau de bord vide en cliquant sur l’icône « plus » en regard des tableaux de bord.

**Épingler des graphiques au tableau de bord** Dans Power BI, cliquez sur le tableau de bord vide que vous avez créé et accédez à la section Reports. Cliquez sur le rapport que vous venez de charger.

![Vehicle Telemetry PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png)


**Notez que le rapport contient six pages :** Page 1 : Vehicle density Page 2 : Real time vehicle health Page 3 : Aggressively Driven Vehicles Page 4 : Recalled vehicles Page 5 : Fuel Efficiently Driven Vehicles Page 6 : Contoso Logo

![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**À partir de la Page 3**, épinglez les éléments suivants : 1. Count of VIN ![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 2. Aggressively driven vehicles by model – Diagramme waterfall ![Vehicle Telemetry - Épingler des graphiques 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**À partir de la Page 5**, épinglez les éléments suivants : 1. Count of vin ![Vehicle Telemetry - Épingler des graphiques 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png) 2. Fuel efficient vehicles by model - Histogramme groupé ![Vehicle Telemetry - Épingler des graphiques 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**À partir de la Page 4**, épinglez les éléments suivants : 1.

1.	Count of vin ![Vehicle Telemetry - Épingler des graphiques 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.	Recalled vehicles by city, model - Carte proportionnelle ![Vehicle Telemetry - Épingler des graphiques 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)

**À partir de la Page 6**, épinglez les éléments suivants :

1.	Contoso Motors logo ![Vehicle Telemetry - Épingler des graphiques 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organiser le tableau de bord**

1.	Accédez au tableau de bord ![Vehicle Telemetry - Organiser le tableau de bord 1](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard1.png)

2.	Placez le curseur sur chaque graphique et renommez-les selon les noms indiqués dans l’image complète du tableau de bord ci-dessous. Déplacez également les graphiques de manière à obtenir un tableau de bord similaire à la capture ci-dessous. ![Vehicle Telemetry - Organiser le tableau de bord 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)

![Vehicle Telemetry PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

<!---HONumber=AcomDC_1203_2015-->