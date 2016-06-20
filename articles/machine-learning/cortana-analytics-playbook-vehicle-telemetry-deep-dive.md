<properties 
	pageTitle="Guide de la solution Vehicle Telemetry Analytics : découverte approfondie de la solution | Microsoft Azure" 
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
	ms.date="05/27/2016" 
	ms.author="bradsev" />


# Guide de la solution Vehicle Telemetry Analytics : découverte approfondie de la solution

Ce **menu** contient des liens vers les sections de ce manuel :

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Cette section explore en détail chacune des étapes présentées dans l’Architecture de la solution et contient des instructions et des pointeurs à des fins de personnalisation.

## Sources de données

La solution utilise deux sources de données différentes :

- **jeu de données de diagnostic et de signaux des véhicules simulés** et 
- **catalogue de véhicules**

Un simulateur de télématique des véhicules est intégré à cette solution. Ce simulateur émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et au schéma de conduite à un moment donné dans le temps. Cliquez sur [Vehicle Telematics Simulator](http://go.microsoft.com/fwlink/?LinkId=717075) pour télécharger la **solution Vehicle Telematics Simulator Visual Studio** afin de la personnaliser en fonction de vos besoins. Le catalogue de véhicules contient un jeu de données de référence associé à un mappage VIN/modèle.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*Figure 2 – Simulateur de télématique des véhicules*

Il s’agit d’un jeu de données au format JSON qui contient le schéma ci-dessous.

des colonnes | Description | Valeurs   
 ------- | ----------- | ---------  
VIN | Numéro d’identification du véhicule généré de manière aléatoire | Ce numéro est obtenu à partir d’une liste de référence contenant 10 000 numéros d’identification de véhicule générés de manière aléatoire
Outside temperature | Température extérieure mesurée dans la zone de conduite du véhicule | Nombre généré de manière aléatoire et compris entre 0 et 100
Engine temperature | Température moteur du véhicule | Nombre généré de manière aléatoire et compris entre 0 et 500
Vitesse | Régime de conduite du véhicule | Nombre généré de manière aléatoire et compris entre 0 et 100
Fuel | Niveau de carburant du véhicule | Nombre généré de manière aléatoire et compris entre 0 et 100 (indique le niveau de carburant en pourcentage)
EngineOil | Niveau d’huile moteur du véhicule | Nombre généré de manière aléatoire et compris entre 0 et 100 (indique le niveau d’huile moteur en pourcentage)
Tirepressure | Pression des pneus du véhicule | Nombre généré de manière aléatoire et compris entre 0 et 50 (indique le niveau de pression des pneus en pourcentage)
Odometer | Valeur lue sur le compteur kilométrique du véhicule | Nombre généré de manière aléatoire et compris entre 0 et 200 000
Accelerator\_pedal\_position | Position de la pédale d’accélérateur du véhicule | Nombre généré de manière aléatoire et compris entre 0 et 100 (indique le niveau d’accélération en pourcentage)
Parking\_brake\_status | Indique si le véhicule est stationné ou non | True ou False
Headlamp\_status | Indique si les phares sont allumés ou non | True ou False
Brake\_pedal\_status | Indique si la pédale de frein est enfoncée ou non | True ou False
Transmission\_gear\_position | Position de la boîte de vitesses du véhicule | États : première, deuxième, troisième, quatrième, cinquième, sixième, septième, huitième
Ignition\_status | Indique si le véhicule roule ou s’il est arrêté | True ou False
Windshield\_wiper\_status | Indique si les essuie-glaces sont activés ou non | True ou False
ABS | Indique si l’ABS est activé ou non | True ou False
Timestamp | Date et heure de création du point de données | Date
City | Emplacement du véhicule | 4 villes dans cette solution : Bellevue, Redmond, Sammamish, Seattle


Le jeu de données de référence du modèle de véhicule contient un mappage entre le numéro d’identification du véhicule (VIN) et le modèle.

VIN | Modèle |
--------------|------------------
FHL3O1SA4IEHB4WU1 | Berline |
8J0U8XCPRGW4Z3NQE | Hybride |
WORG68Z2PLTNZDBI7 | Berline familiale |
JTHMYHQTEPP4WBMRN | Berline |
W9FTHG27LZN1YWO0Y | Hybride |
MHTP9N792PHK08WJM | Berline familiale |
EI4QXI2AXVQQING4I | Berline |
5KKR2VB4WHQH97PF8 | Hybride |
W9NSZ423XZHAONYXB | Berline familiale |
26WJSGHX4MA5ROHNL | Cabriolet |
GHLUB6ONKMOSI7E77 | Break |
9C2RHVRVLMEJDBXLP | Voiture compacte |
BRNHVMZOUJ6EOCP32 | Petit SUV |
VCYVW0WUZNBTM594J | Voiture de sport |
HNVCE6YFZSA5M82NY | SUV de taille moyenne |
4R30FOR7NUOBL05GJ | Break |
WYNIIY42VKV6OQS1J | Grand SUV |
8Y5QKG27QET1RBK7I | Grand SUV |
DF6OX2WSRA6511BVG | Coupé |
Z2EOZWZBXAEW3E60T | Berline |
M4TV6IEALD5QDS3IR | Hybride |
VHRA1Y2TGTA84F00H | Berline familiale |
R0JAUHT1L1R3BIKI0 | Berline |
9230C202Z60XX84AU | Hybride |
T8DNDN5UDCWL7M72H | Berline familiale |
4WPYRUZII5YV7YA42 | Berline |
D1ZVY26UV2BFGHZNO | Hybride |
XUF99EW9OIQOMV7Q7 | Berline familiale
8OMCL3LGI7XNCC21U | Cabriolet |
……. | |


### Générer les données simulées
1.	Cliquez sur la flèche située en haut à droite du nœud Vehicle Telematics Simulator pour télécharger le package du simulateur de données. Enregistrez les fichiers en local et extrayez-les sur votre ordinateur. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png) *Figure 3 – Plan de la solution Vehicle Telemetry Analytics*

2.	Sur votre ordinateur local, accédez au dossier où vous avez extrait le package Vehicle Telematics Simulator. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png) *Figure 4 – Dossier Vehicle Telematics Simulator*

3.	Exécutez l’application **CarEventGenerator.exe**.

### Références

[Solution Vehicle Telematics Simulator Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075)

[Hub d'événement d'Azure](https://azure.microsoft.com/services/event-hubs/)

[Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## Ingestion
Les composants Event Hubs, Stream Analytics et Data Factory d’Azure sont combinés pour recevoir les signaux et les événements de diagnostic des véhicules, ainsi que les analyses de traitement par lots et en temps réel. Tous ces composants sont créés et configurés dans le cadre du déploiement de la solution.

### Analyse en temps réel
Les événements générés par le composant Vehicle Telematics Simulator sont publiés dans Azure Event Hub à l’aide du SDK Event Hub. La tâche Stream Analytics reçoit ces événements à partir d’Event Hub et traite les données en temps réel pour analyser l’état du véhicule.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png)

*Figure 5 - Tableau de bord Event Hub*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png)

*Figure 6 - Données de traitement de la tâche Stream Analytics*

La tâche Stream Analytics reçoit les données du composant Event Hub, effectue une jointure avec les données de référence pour mapper le numéro d’identification du véhicule au modèle correspondant, et les conserve dans le stockage Azure Blob Storage pour une analyse en mode batch approfondie. La requête Stream Analytics ci-dessous est utilisée pour conserver les données dans Azure Blob Storage.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png)

*Figure 7 - Requête de la tâche Stream Analytics pour l’ingestion de données*

### Analyse en mode batch
Nous allons également générer un volume supplémentaire de signaux de véhicules simulés ainsi qu’un jeu de données de diagnostic pour permettre une analyse en mode batch approfondie. Cette étape est nécessaire pour garantir un volume de données représentatif dans le cadre d’un traitement par lots. Nous utilisons pour cela un pipeline nommé « PrepareSampleDataPipeline » dans le flux de travail Azure Data Factory pour générer l’équivalent d’une année de signaux et de diagnostics de véhicules simulés. Cliquez sur [Activité personnalisée Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077) pour télécharger la solution Data Factory custom DotNet activity Visual Studio afin de la personnaliser en fonction de vos besoins.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png)

*Figure 8 - Préparer des exemples de données pour le workflow de traitement par lots*

Le pipeline est composé d’un élément ADF .NET Activity personnalisé, comme indiqué ci-dessous :

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png)

*Figure 9 - PrepareSampleDataPipeline*

Une fois que le pipeline est correctement exécuté et que le jeu de données « RawCarEventsTable » est marqué comme « Prêt », l’équivalent d’une année de données de signaux et de diagnostics de véhicules simulés est généré. Le dossier et le fichier ci-dessous sont créés dans votre compte de stockage sous le conteneur « connectedcar » :

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png)

*Figure 10 - Résultat du pipeline PrepareSampleDataPipeline*

### Références

[Kit Azure Event Hub SDK pour la réception de flux](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Fonctionnalités de déplacement de données Azure Data Factory](../data-factory/data-factory-data-movement-activities.md) [Azure Data Factory DotNet Activity](../data-factory/data-factory-use-custom-activities.md)

[Solution Azure Data Factory DotNet Activity Visual Studio pour la préparation des exemples de données](http://go.microsoft.com/fwlink/?LinkId=717077)


## Préparation
>[AZURE.ALERT] Cette étape de la solution s’applique uniquement au traitement par lots.

Le jeu de données de signaux et de diagnostic brut semi-structuré est partitionné au cours de l’étape de préparation des données au format ANNÉE/MOIS pour permettre une interrogation efficace et un stockage extensible à long terme (*c’est-à-dire* qu’il bascule d’un compte de stockage d’objets blob à un autre dès que le premier est alimenté). Les données de sortie (intitulées *PartitionedCarEventsTable*) doivent être conservées pendant une longue période sous une forme primaire/« la plus brute » dans le « “Data Lake (lac de données) » du client. Les données d’entrée de ce pipeline sont généralement ignorées car les données de sortie représentent fidèlement les données d’entrée. Ces dernières sont simplement stockées (partitionnées) en vue d’être utilisées ultérieurement.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*Figure 11 - Workflow Partition Car Events*

Les données brutes sont partitionnées à l’aide d’une activité Hive HDInsight dans « PartitionCarEventsPipeline ». L’équivalent d’une année d’exemples de données générés à l’étape 1 est partitionné par ANNÉE/MOIS pour générer les partitions de signaux de véhicule et de données de diagnostic correspondant à chaque mois (12 partitions au total) de l’année.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*Figure 12 - PartitionCarEventsPipeline*

Le script Hive ci-dessous, nommé « partitioncarevents.hql » et situé dans le dossier « \\demo\\src\\connectedcar\\scripts » du fichier zip téléchargé, est utilisé pour le partitionnement.

	SET hive.exec.dynamic.partition=true;
	SET hive.exec.dynamic.partition.mode = nonstrict;
	set hive.cli.print.header=true;

	DROP TABLE IF EXISTS RawCarEvents; 
	CREATE EXTERNAL TABLE RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
	) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

	DROP TABLE IF EXISTS Stage_RawCarEvents; 
	CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string,
				YearNo 							int,
				MonthNo 						int) 
	ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

	INSERT OVERWRITE TABLE Stage_RawCarEvents
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		Year(gendate),
		Month(gendate)

	FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

	INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		YearNo,
		MonthNo
	FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*Figure 13 - Script Hive PartitionConnectedCarEvents*

Une fois le pipeline exécuté, les partitions suivantes seront générées dans votre compte de stockage sous le conteneur « connectedcar ».

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*Figure 14 - Sortie partitionnée*

Les données sont maintenant optimisées, plus faciles à gérer et prêtes à être traitées pour obtenir des informations de traitement par lots complètes.

## Analyse des données

Dans cette section, vous verrez comment nous avons utilisé les composants Azure Stream Analytics, Azure Machine Learning, Azure Data Factory et Azure HDInsight pour analyser avec précision l’état des véhicules et les habitudes de conduite. Nous proposons ici 3 sous-sections :

1.	**Apprentissage automatique** : cette sous-section contient des informations relatives à l’expérience de détection des anomalies que nous avons utilisée dans cette solution pour identifier de manière proactive les véhicules ayant besoin d’une intervention de maintenance ainsi que les véhicules devant faire l’objet d’un rappel pour des raisons de sécurité.
2.	**Analyse en temps réel** : cette sous-section contient des informations concernant l’analyse en temps réel utilisant le langage de requête Stream Analytics et l’amélioration de l’expérience d’apprentissage automatique en temps réel à l’aide d'une application personnalisée.
3.	**Analyse par lots** : cette sous-section contient des informations relatives à la transformation et au traitement des données par lots à l’aide des composants Azure HDInsight et Azure Machine Learning déployés par Azure Data Factory

### Apprentissage automatique

Notre objectif est ici de prédire quels véhicules devront faire l’objet d’une intervention de maintenance ou d’un rappel en fonction de certaines statistiques de contrôle d’intégrité. Nous formulons les hypothèses suivantes :

- Les véhicules ont besoin d’une **intervention de maintenance** si l’une des trois conditions suivantes est remplie :
	- La pression des pneus faible
	- Le niveau d’huile moteur est faible
	- La température du moteur est élevée

- Les véhicules peuvent présenter un **problème de sécurité** et nécessiter un **rappel** si l’une des conditions suivantes est remplie :
	- La température du moteur est élevée mais la température extérieure est faible
	- La température du moteur est faible mais la température extérieure est élevée

Compte tenu des exigences ci-dessus, nous avons créé deux modèles distincts afin de détecter des anomalies : le premier pour détecter les besoins de maintenance des véhicules, le second pour détecter les rappels de véhicules. Dans ces deux modèles, l’algorithme Principal Component Analysis (PCA) intégré est utilisé pour la détection des anomalies.

**Modèle de détection de maintenance** Dans le modèle de détection de maintenance, le modèle signale une anomalie si l’un des trois indicateurs (pression des pneus, huile moteur ou température du moteur) remplit sa condition respective. Nous devons donc uniquement prendre en compte ces trois variables dans la construction du modèle. Pour notre expérience dans Azure Machine Learning, nous utilisons tout d’abord un module **de sélection de colonnes dans le jeu de données** afin d’extraire ces trois variables. Nous utilisons ensuite le module de détection d’anomalies basé sur l’algorithme PCA pour générer le modèle de détection d’anomalies.

Principal Component Analysis (PCA) est une technique d’apprentissage automatique reconnue qui peut être appliquée à la sélection des fonctionnalités, à la classification et à la détection d’anomalies. PCA convertit un ensemble de cas contenant des variables potentiellement corrélées en un ensemble de valeurs appelées composants principaux. La modélisation PCA vise essentiellement à projeter des données dans un espace de plus faible dimension afin de faciliter l’identification des fonctionnalités et des anomalies.
 
Si une anomalie est détectée, pour chaque nouvelle entrée, le détecteur d’anomalies calcule d’abord sa projection sur les vecteurs propres avant de calculer l’erreur de reconstruction normalisée. Cette erreur normalisée représente le score d’anomalies. Plus l’erreur est élevée, plus l’instance est anormale.

Dans le problème de la détection de maintenance, chaque enregistrement peut être considéré comme un point dans un espace tridimensionnel défini par les coordonnées « pression des pneus », « huile moteur » et « température du moteur ». Pour capturer ces anomalies, nous pouvons projeter les données d’origine en 3 dimensions dans un espace 2D à l’aide de PCA. Nous définissons donc sur « 2 » le paramètre « nombre de composants à utiliser » dans PCA. Ce paramètre joue un rôle important dans l’application de la détection d’anomalies basée sur l’algorithme PCA. Une fois les données projetées à l’aide de PCA, nous pouvons identifier plus facilement ces anomalies.

**Modèle de détection des anomalies de rappel** Dans le modèle de détection des anomalies de rappel, nous utilisons le module de sélection de colonnes dans le jeu de données et le module de détection d’anomalies reposant sur le PCA de manière similaire. Plus précisément, nous commençons par extraire trois variables (température du moteur, température extérieure et vitesse) à l’aide du module **de sélection de colonnes dans le jeu de données**. Nous ajoutons également la variable vitesse étant donné que la température du moteur est généralement corrélée à la vitesse. Nous utilisons ensuite le module de détection d’anomalies PCA pour projeter les données de l’espace 3D sur un espace 2D. Les critères de rappel étant satisfaits, le véhicule nécessite un rappel lorsque de la température du moteur et la température extérieure sont corrélées de façon très négative. L’algorithme de détection d’anomalies PCA nous permet de regrouper les anomalies après l’exécution de l’algorithme PCA.

Notez que lors de l’apprentissage des deux modèles, nous devons utiliser des données normales qui ne nécessitent ni maintenance ni rappel comme données d’entrée pour former le modèle de détection d’anomalies basée sur PCA. Dans l’expérience d’évaluation, nous utilisons le modèle de détection d’anomalies formé pour déterminer si le véhicule nécessite une maintenance ou un rappel.


### Analyse en temps réel

La requête SQL suivante de Stream Analytics est utilisée pour calculer la moyenne de tous les paramètres de véhicule significatifs, tels que la vitesse du véhicule, le niveau de carburant, la température du moteur, le kilométrage, la pression des pneus, le niveau d’huile, etc. afin de détecter les anomalies, d’émettre des alertes et de déterminer les conditions d’intégrité globale des véhicules utilisés dans une région spécifique et de les corréler à des données démographiques.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Figure 15 - Requête Stream Analytics pour le traitement en temps réel

Toutes les moyennes sont calculées sur un TumblingWindow de 3 secondes. Nous utilisons TubmlingWindow dans ce cas car nous avons besoin d’intervalles de temps contigus et sans chevauchement.

Pour en savoir plus sur les fonctionnalités de « fenêtrage » dans Azure Stream Analytics, cliquez sur [Fenêtrage (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Prédiction en temps réel**

Une application est incluse dans le cadre de la solution pour configurer le modèle d’apprentissage automatique en temps réel. Cette application appelée « RealTimeDashboardApp » est créée et configurée dans le cadre du déploiement de la solution. L’application exécute les tâches suivantes :

1.	Écoute une instance Event Hub dans laquelle Stream Analytics publie les événements en continu. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)* Figure 16 – Requête Stream Analytics pour la publication des données vers une instance Event Hub de sortie * 

2.	Pour chaque événement reçu par cette application :

	- Traite les données à l’aide du point de terminaison Request-Response Scoring (RRS) de Machine Learning. Le point de terminaison RRS est automatiquement publié dans le cadre du déploiement.
	- La sortie RRS est publiée dans un ensemble de données PowerBI à l’aide d’API push.

Ce modèle s’applique également aux scénarios dans lesquels vous souhaitez intégrer une application métier avec le flux d’analyse en temps réel pour des scénarios tels que les alertes, les notifications, la messagerie, etc.

Cliquez sur [RealtimeDashboardApp download](http://go.microsoft.com/fwlink/?LinkId=717078) pour télécharger la solution RealtimeDashboardApp Visual Studio pour les personnalisations.

**** Pour exécuter l’application de tableau de bord en temps réel **

1.	Cliquez sur le nœud PowerBI sur la vue schématique, puis cliquez sur le lien Télécharger l’application de tableau de bord en temps réel dans le volet des propriétés. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png) *Figure 17 - Instructions de configuration du tableau de bord Power BI*
2.	Extrayez le fichier et enregistrez-le en local ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *Figure 18 – Dossier RealtimeDashboardApp*
3.	Exécutez l’application RealtimeDashboardApp.exe
4.	Entrez des informations d’identification Power BI valides, connectez-vous, puis cliquez sur Accept ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Figure 19 – RealtimeDashboardApp : connexion à Power BI*

>[AZURE.NOTE] Remarque : si vous voulez vider le jeu de données PowerBI, exécutez l’application RealtimeDashboardApp avec le paramètre « flushdata » :

	RealtimeDashboardApp.exe -flushdata

### Analyse en mode batch

L’objectif ici est de décrire la manière dont Contoso Motors utilise les capacités de calcul Azure pour exploiter des Big Data afin d’obtenir de précieuses informations sur les schémas de conduite, le comportement d’utilisation et l’état du véhicule, ce dans le but :

- d’améliorer l’expérience client à moindres coûts en offrant des perspectives sur les habitudes de conduite et sur les comportements de conduite économes en carburant ;
- de s’informer de manière proactive sur les clients et leurs modèles de conduite afin d’orienter les décisions commerciales et de fournir des produits et services de meilleure qualité.

Dans cette solution, nous ciblons les mesures suivantes :

1.	**Comportement de conduite agressive** Identifie la tendance des modèles, des emplacements, des conditions de conduite et de la période de l’année pour obtenir un aperçu du mode de conduite agressive afin de permettre à Contoso Motors d’utiliser ces informations dans ses campagnes marketing, de créer de nouvelles fonctionnalités personnalisées et de proposer des assurances adaptées à l’utilisation.
2.	**Comportement de conduite économe en carburant** Identifie la tendance des modèles, des emplacements, des conditions de conduite et de la période de l’année pour obtenir un aperçu du mode de conduite économe en carburant afin de permettre à Contoso Motors d’utiliser ces informations dans ses campagnes marketing, de proposer aux conducteurs de nouvelles fonctionnalités et des rapports proactifs pour encourager les habitudes de conduite économique et respectueuses de l’environnement. 
3.	**Modèles de rappel** Identifie les modèles nécessitant des rappels en améliorant l’expérience d’apprentissage automatique pour la détection des anomalies

Examinons en détail chacune de ces mesures :


**Mode de conduite agressive**

Les signaux de véhicule et les données de diagnostic partitionnés sont traités dans le pipeline nommé « AggresiveDrivingPatternPipeline » qui utilise Hive pour déterminer les modèles, emplacements, conditions de véhicule et de conduite, etc. dénotant un mode de conduite agressive.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) *Figure 20 – Workflow du mode de conduite agressive*

Le script Hive nommé « aggresivedriving.hql » et utilisé pour analyser le modèle de condition de conduite agressive se trouve dans le dossier « \\demo\\src\\connectedcar\\scripts » du fichier zip téléchargé.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS CarEventsAggresive; 
	CREATE EXTERNAL TABLE CarEventsAggresive
	(
               	vin 						string, 
				model						string,
                timestamp					string,
				city						string,
				speed 			 			string,
				transmission_gear_position	string,
				brake_pedal_status			string,
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



	INSERT OVERWRITE TABLE CarEventsAggresive
	select
	vin,
	model,
	timestamp,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*Figure 21 – Requête Hive du mode de conduite agressive*

Il utilise à la fois la position du levier de vitesses, l’état de la pédale de frein et la vitesse du véhicule pour détecter un comportement de conduite imprudent/agressif en fonction du schéma de freinage à vitesse élevée.

Une fois le pipeline exécuté, les partitions suivantes seront générées dans votre compte de stockage sous le conteneur « connectedcar ».

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png)

*Figure 22 – Sortie AggressiveDrivingPatternPipeline*


**Mode de conduite économe en carburant**

Les signaux de véhicule et les données de diagnostic partitionnés sont traités dans le pipeline nommé « FuelEfficientDrivingPatternPipeline » qui utilise Hive pour déterminer les modèles, emplacements, conditions de véhicule et de conduite, etc. dénotant un mode de conduite économe en carburant.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png)

*Figure 23 - Workflow du mode de conduite économe en carburant*

Le script Hive nommé « fuelefficientdriving.hql » et utilisé pour analyser le modèle de condition de conduite économe en carburant se trouve dans le dossier « \\demo\\src\\connectedcar\\scripts » du fichier zip téléchargé.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS FuelEfficientDriving; 
	CREATE EXTERNAL TABLE FuelEfficientDriving
	(
               	vin 						string, 
				model						string,
               	city						string,
				speed 			 			string,
				transmission_gear_position	string,                
				brake_pedal_status			string,            
				accelerator_pedal_position	string,                             
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



	INSERT OVERWRITE TABLE FuelEfficientDriving
	select
	vin,
	model,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	accelerator_pedal_position,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*Figure 24 - Requête Hive du mode de conduite économe en carburant*

Il utilise à la fois la position du levier de vitesses, l’état de la pédale de frein, la vitesse du véhicule et la position de la pédale d’accélérateur pour détecter un comportement de conduite économe en carburant en fonction des schémas d’accélération, de freinage et de vitesse élevée.

Une fois le pipeline exécuté, les partitions suivantes seront générées dans votre compte de stockage sous le conteneur « connectedcar ».

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png)

*Figure 25 – Sortie FuelEfficientDrivingPatternPipeline*


**Prédictions des rappels**

L’expérience d’apprentissage automatique est configurée et publiée en tant que service web dans le cadre du déploiement de la solution. Le point de terminaison de notation par lots est utilisé dans ce workflow, enregistré tant que service Data Factory lié et mis en œuvre à l’aide de l’activité de notation par lots de Data Factory.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png)

*Figure 26 – Point de terminaison Machine Learning enregistré comme service lié dans Data Factory*

Le service lié enregistré est utilisé dans le DetectAnomalyPipeline pour noter les données à l’aide du modèle de détection d’anomalies.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png)

*Figure 27 – Activité de notation par lot d'Azure Machine Learning dans Data Factory*

Quelques étapes sont exécutées dans ce pipeline pour préparer les données afin de les rendre opérationnelles avec le service web de notation par lots.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png)

*Figure 28 – DetectAnomalyPipeline pour la prédiction des véhicules nécessitant des rappels*

Une fois l’évaluation terminée, une activité HDInsight est utilisée pour traiter et agréger les données que le modèle considère comme des anomalies, avec un score de probabilité d’au moins 0,60.

	DROP TABLE IF EXISTS CarEventsAnomaly; 
	CREATE EXTERNAL TABLE CarEventsAnomaly 
	(
            	vin							string,
				model						string,
				gendate						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
				fuel						string,
				engineoil					string,
				tirepressure				string,
				odometer					string,
				city						string,
				accelerator_pedal_position	string,
				parking_brake_status		string,
				headlamp_status				string,
				brake_pedal_status			string,
				transmission_gear_position	string,
				ignition_status				string,
				windshield_wiper_status		string,
				abs  						string,
				maintenanceLabel			string,
				maintenanceProbability		string,
				RecallLabel					string,
				RecallProbability			string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

	DROP TABLE IF EXISTS RecallModel; 
	CREATE EXTERNAL TABLE RecallModel 
	(

				vin							string,
				model						string,
				city						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
            	Year						string,
				Month						string				
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

	INSERT OVERWRITE TABLE RecallModel
	select
	vin,
	model,
	city,
	outsidetemperature,
	enginetemperature,
	speed,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from CarEventsAnomaly
	where RecallLabel = '1' AND RecallProbability >= '0.60'

*Figure 29 - Requête Hive d’agrégation des données de rappel*

Une fois le pipeline exécuté, les partitions suivantes seront générées dans votre compte de stockage sous le conteneur « connectedcar ».

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png)

*Figure 30 – Sortie DetectAnomalyPipeline*


## Publier

### Analyse en temps réel

L’une des requêtes de la tâche Stream Analytics publie les événements dans une instance Event Hub de sortie.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Figure 31 - Publication des événements de la tâche Stream Analytics dans une instance Event Hub de sortie*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Figure 32 - Requête Stream Analytics pour la publication d’événements dans une instance Event Hub de sortie*

Ce flux d’événements est utilisé par l’application RealTimeDashboardApp intégrée dans la solution. Cette application s’appuie sur le service web Request-Response de Machine Learning pour calculer les scores en temps réel, et publie les données obtenues dans un jeu de données Power BI.

### Analyse en mode batch

Les résultats du traitement par lots et en temps réel sont publiés dans les tables de base de données SQL Azure pour être consommés. Le serveur SQL, la base de données et les tables Azure sont créés automatiquement dans le cadre du script d’installation.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Figure 33 – Copie des résultats du traitement par lots dans le workflow d’entrepôt de données*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Figure 34 - Publication des événements de la tâche Stream Analytics dans un entrepôt de données*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Figure 35 – Configuration de l’entrepôt de données dans la tâche Stream Analytics*


## Utiliser

Power BI offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives.

Cliquez ici pour obtenir des instructions détaillées sur la configuration des rapports et du tableau de bord Power BI. Le tableau de bord final doit ressembler à ceci :

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*Figure 36 - Tableau de bord Power BI*

## Résumé

Ce document explore de façon détaillée la solution Vehicle Telemetry Analytics. Il présente un modèle d’architecture lambda pour une analyse en temps réel et par lots reposant sur des prédictions et des actions. Ce modèle s’applique à un large éventail de scénarios qui requièrent des analyses à chaud (en temps réel) et à froid (par lots).

<!---HONumber=AcomDC_0608_2016-->