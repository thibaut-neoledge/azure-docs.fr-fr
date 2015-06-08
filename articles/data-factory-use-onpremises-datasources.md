<properties 
	pageTitle="Activation de vos pipelines pour utiliser des données locales | Azure Data Factory" 
	description="Découvrez comment inscrire une source de données locale dans une fabrique de données Azure fabrique et copier les données vers/depuis la source de données." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Activation de vos pipelines pour utiliser des données locales

Pour activer vos pipelines dans une fabrique de données Azure pour utiliser une source de données locale, vous devez ajouter la source de données locale comme service lié à la fabrique de données à l'aide du portail de gestion Azure ou d'Azure PowerShell.
 
Pour ajouter une source de données locale comme service lié à une fabrique de données, vous devez d'abord télécharger et installer la passerelle de gestion de données Microsoft sur un ordinateur local et configurer le service lié pour que la source de données locale utilise la passerelle.


## <a href="DMG"></a> Passerelle de gestion des données

**Passerelle de gestion des données** est un logiciel qui se connecte à des sources de données sur site pour les services en nuage de façon gérée et sécurisée. Avec la passerelle de gestion de données, vous pouvez :

- **Se connecter à des données locales pour l'accès aux données hybride** – vous pouvez connecter des données locales aux services de cloud à bénéficier de services en nuage tout en conservant l'activité en cours d'exécution avec les données locales.
- **Définir un proxy de sécuriser les données** – vous pouvez définir les sources de données sur site sont affichés avec la passerelle de gestion des données afin que cette passerelle authentifie la demande de données à partir des services de cloud computing et protège les sources de données sur site.
- **Gérer votre passerelle pour la gouvernance complète** – vous sont fournis avec analyse complète et l'enregistrement de toutes les activités à l'intérieur de la passerelle de gestion des données de gestion et de gouvernance.
- **Déplacer efficacement des données** – les données sont transférées en parallèle, les problèmes de réseau résistante aux intermittents avec auto logique de nouvelle tentative.


La passerelle de gestion de données a une gamme complète de fonctionnalités de connexion de données locales qui incluent les éléments suivants :

- **Non intrusive au pare-feu d'entreprise** – passerelle de gestion des données fonctionne uniquement après l'installation, sans avoir à ouvrir un pare-feu connexion nécessitant intrusive modifie à votre infrastructure de réseau d'entreprise. 
- **Chiffrer les informations d'identification avec votre certificat** – informations d'identification utilisées pour se connecter aux sources de données sont cryptées avec un certificat entièrement détenu par un utilisateur. Sans certificat, personne ne peut déchiffrer les informations d'identification en texte brut, y compris Microsoft.

### Considérations sur l'utilisation de la passerelle de gestion de données
1.	Une seule instance de passerelle de gestion des données peut être utilisé pour plusieurs sources de données sur site, mais n'oubliez pas qu'un **passerelle est liée à une fabrique de données Azure** et ne peut pas être partagée avec une autre usine de données.
2.	Vous pouvez avoir **qu'une seule instance de passerelle de gestion des données** installé sur votre ordinateur. Si deux fabriques de données doivent accéder aux sources de données locales, vous devez installer des passerelles sur deux ordinateurs locaux où chaque passerelle est liée à une fabrique de données distincte.
3.	Le **passerelle ne pas besoin d'être sur le même ordinateur que la source de données**, mais rester proche de la source de données réduit le temps de la passerelle pour se connecter à la source de données. Nous vous recommandons d'installer la passerelle sur un ordinateur différent de celui qui héberge la source de données sur site afin que la passerelle de ne pas en concurrence pour les ressources avec la source de données.
4.	Vous pouvez avoir **plusieurs passerelles sur différents ordinateurs connectés à la même source de données sur site**. Par exemple, vous pouvez avoir deux passerelles desservant deux fabriques de données, mais la même source de données locale est enregistrée avec à la fois les fabriques de données. 
5.	Si vous disposez déjà d'une passerelle installée sur votre serveur de l'ordinateur un **Power BI** scénario, vous devez installer une **passerelle distinct pour Azure Data Factory** sur un autre ordinateur.

### Considérations de sécurité et de ports 
- Le programme d'installation de passerelle de gestion des données ouvre **8050** et **8051** ports sur l'ordinateur de passerelle. Ces ports sont utilisés par le **Gestionnaire d'informations d'identification** (application ClickOnce), qui vous permet de définir les informations d'identification d'un service lié sur site et pour tester la connexion à la source de données. Ces ports ne peut pas être atteint à partir d'internet et vous ne devez sont pas ouvert dans le pare-feu d'entreprise.
- Lors de la copie des données depuis/vers une base de données SQL Server sur site vers/à partir d'une base de données SQL Azure, vérifiez les points suivants :
 
	- Le pare-feu sur l'ordinateur de passerelle autorise les communications TCP sortantes sur **TCP** port **1433**
	- Configurer [les paramètres de pare-feu SQL Azure](https://msdn.microsoft.com/library/azure/jj553530.aspx) pour ajouter le **adresse IP de l'ordinateur de passerelle** à la **adresses IP autorisées**.

- Lors de la copie des données depuis ou vers local SQL Server vers une destination et la passerelle et les ordinateurs SQL Server sont différents, procédez comme suit : [configurer le pare-feu Windows](https://msdn.microsoft.com/library/ms175043.aspx) sur le serveur SQL Server de l'ordinateur afin que la passerelle puisse accéder à la base de données via les ports utilisés par l'instance de SQL Server écoute. L'instance par défaut, il est le port 1433.

- Vous devez lancer le **Gestionnaire d'informations d'identification** de l'application sur un ordinateur qui est en mesure de se connecter à la passerelle de gestion des données pour pouvoir définir les informations d'identification pour la source de données et pour tester la connexion à la source de données.

### Installation de la passerelle - configuration requise 

1.	La prise en charge **système d'exploitation** versions sont Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012.
2.	L'architecture recommandée **configuration** pour la passerelle machine est au moins 2 GHz, 4 cœurs, 8 Go de RAM et 80 Go de disque.
3.	Si l'ordinateur hôte en veille prolongée, la passerelle sera en mesure de répondre aux demandes de données. Par conséquent, configurer une **de l'alimentation** sur l'ordinateur avant d'installer la passerelle. L'installation de la passerelle vous invite à entrer un message si l'ordinateur est configuré pour la mise en veille prolongée.  


 

## Procédure pas à pas

Dans cette procédure pas à pas, vous créez une fabrique de données avec un pipeline qui déplace les données d'une base de données SQL Server locale vers un objet blob Azure.

## Étape 1: Créer une fabrique de données Azure
Dans cette étape, vous utilisez le portail de gestion Azure pour créer une instance de fabrique de données Azure nommée **ADFTutorialOnPremDF**. Vous pouvez également créer une fabrique de données à l'aide des cmdlets Azure Data Factory.

1.	Après la connexion à la [portail Azure Preview][azure-preview-portal], cliquez sur **nouveau** dans le coin inférieur gauche, sélectionnez **l'analyse de données** dans les **créer** lame, puis cliquez sur **Data Factory** sur la **l'analyse de données** lame.

	![Nouveau -> DataFactory][image-data-factory-new-datafactory-menu]
  
6. Dans le **nouvelle fabrique de données** lame :
	1. Entrez **ADFTutorialOnPremDF** pour le **nom**.
	2. Cliquez sur **nom de groupe de ressources** et sélectionnez **ADFTutorialResourceGroup** (si vous aviez réalisé le didacticiel de [prise en main Azure Data Factory][adfgetstarted]. Vous pouvez sélectionner un groupe de ressources existant ou en créer un. Pour créer un groupe de ressources :
		1. Cliquez sur **créer un groupe de ressources**.
		2. Dans le **volet du groupe de ressources créer**, entrez un **nom** pour le groupe de ressources, cliquez sur **OK**.

7. Notez que **Ajouter au tableau d'accueil** est vérifiée sur le **nouvelle fabrique de données** lame.

	![Ajouter au Tableau d'accueil][image-data-factory-add-to-startboard]

8. Dans le **nouvelle fabrique de données** lame, cliquez sur **créer**.

	> [AZURE.NOTE]**Nom d'usine de données « ADFTutorialOnPremDF » n'est pas disponible**  
9. Recherchez des notifications à partir du processus de création dans le **NOTIFICATIONS** hub sur la gauche. Cliquez sur **X** pour fermer la **NOTIFCATIONS** lame si elle est ouverte.

	![Concentrateur de NOTIFICATIONS][image-data-factory-notifications-hub]

11. Une fois la création terminée, vous verrez la **Data Factory** lame comme indiqué ci-dessous :

	![Page d'accueil de fabrique de données][image-data-factory-datafactory-homepage]

## Étape 2: Créer une passerelle de gestion des données
5.	Sur le **Data Factory** lame pour **ADFTutorialOnPremDF**, cliquez sur **Services liés**. 

	![Page d'accueil de fabrique de données][image-data-factory-home-age]

2.	Sur le **Services liés** lame, cliquez sur **+ données passerelle**.

	![Services liés - ajouter un bouton de passerelle][image-data-factory-linkedservices-add-gateway-button]

2. Dans le **créer** lame, entrez **adftutorialgateway** pour le **nom**, puis cliquez sur **OK**.

	![Créer la lame de passerelle][image-data-factory-create-gateway-blade]

3. Dans le panneau **Configurer**, cliquez sur **Installer directement sur cet ordinateur**. Cette opération télécharge le package d'installation de la passerelle, installe, configure et inscrit la passerelle sur l'ordinateur.

	> [AzURE.NOTE]Utilisez Internet Explorer ou un navigateur compatible Microsoft ClickOnce.

	![Passerelle - Panneau de configuration][image-data-factory-gateway-configure-blade]

	Il s'agit de la méthode la plus simple (un clic) pour télécharger, installer, configurer et inscrire la passerelle en une seule étape. Vous pouvez voir les **Gestionnaire de Configuration de passerelle de gestion de données de Microsoft** application est installée sur votre ordinateur. Vous pouvez également trouver l'exécutable **ConfigManager.exe** dans le dossier : **C:\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.

	Vous pouvez également télécharger et installer passerelle manuellement en utilisant les liens dans ce volet et inscrire à l'aide de la clé indiquée dans le **Enregistrer avec la clé** zone de texte.
	
	Voir [passerelle de gestion des données](#DMG) section pour plus d'informations sur la passerelle, y compris les meilleures pratiques et des considérations importantes.

	>[AZURE.NOTE]Vous devez être administrateur sur l'ordinateur local pour installer et configurer la passerelle de gestion des données avec succès. Vous pouvez ajouter des utilisateurs supplémentaires au groupe Windows local utilisateurs de gestion des données à l'aide de la passerelle. Les membres de ce groupe sera en mesure d'utiliser l'outil Gestionnaire de Configuration de passerelle de gestion de données pour configurer la passerelle.

4. Cliquez sur le **NOTIFICATIONS** hub sur la gauche. Patientez jusqu'à ce que vous voyiez **Express le programme d'installation pour « adftutorialgateway » a réussi** de message dans le **Notifications** lame.

	![Réussi de l'installation expresse][express-setup-succeeded]
5. Cliquez sur **OK** sur la **créer** lame puis, dans le **nouvelle passerelle de données** lame.
6. Fermer la **Services liés** blade (en appuyant sur **X** bouton dans le coin supérieur droit) et rouvrez le **Services liés** blade pour afficher le dernier état de la passerelle. 
7. Vérifiez que le **état** de la passerelle est **Online**. 

	![État de la passerelle][gateway-status]
5. Lancez **Gestionnaire de Configuration de passerelle de gestion de données de Microsoft** de l'application sur votre ordinateur.

	![Gestionnaire de Configuration de passerelle][image-data-factory-gateway-configuration-manager]

6. Attendez que les valeurs soient définies comme suit :
	1. Si le Service **état** n'a pas la valeur **démarré**, cliquez sur **Démarrer le service** pour démarrer le service et attendez une minute pour les autres champs à actualiser.
	2. **Nom de la passerelle** a **adftutorialgateway**.
	3. **Nom de l'instance** a **adftutorialgateway**.
	4. **État de la passerelle clé** est définie sur **inscrit**.
	5. L'état de la barre du bas affiche **connecté au Service de Cloud de passerelle de gestion de données** avec un **coche verte**.
	
7. Sur le **Services liés** lame, vérifiez que le **état** de la passerelle est **bonne**.
8. Fermez toutes les lames jusqu'à ce que vous atteigniez le **Data Factory** page d'accueil. 

## Étape 2: Créer des services liés 
Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **SqlServerLinkedService**. Le **SqlServerLinkedService** lie une base de données SQL Server sur site et la **StorageLinkedService** service lié lie un magasin d'objets blob Azure pour le **ADFTutorialDataFactory**. Plus loin dans cette procédure pas à pas, vous allez créer un pipeline qui copie les données de la base de données SQL Server locale vers le magasin d'objets blob Azure.

### Ajout d'un service lié à une base de données SQL Server locale
1.	Dans le **DATA FACTORY** lame, cliquez sur **auteur et déployer** vignette pour lancer le **éditeur** pour la fabrique de données.

	![Vignette Créer et déployer][image-author-deploy-tile]

	> [AZURE.NOTE][Éditeur de fabrique de données][data-factory-editor]
2.	Dans le **éditeur**, cliquez sur **nouveau magasin de données** bouton sur la barre d'outils et sélectionnez **base de données locale SQL server** dans le menu déroulant. 

	![Éditeur nouveau bouton de magasin de données][image-editor-newdatastore-onpremsql-button]
    
3.	Vous devez voir le modèle JSON pour la création d'un service de serveur SQL lié sur site dans le volet droit. ![En l'inscrivant SQL Service lié - paramètres][image-editor-newdatastore-onpremsql-settings]

4.	procédez comme suit dans le volet JSON :
	1.	Pour le **gatewayName** propriété, entrez **adftutorialgateway** de remplacer tout le texte à l'intérieur des guillemets doubles.  
	2.	Si vous utilisez **l'authentification SQL**: 
		1.	Pour le **connectionString** propriété, remplacez **< nom_serveur >**, **< databasename >**, **< nom_utilisateur >**, et **< mot de passe >** avec des noms de votre SQL server sur site, une base de données, un compte d'utilisateur et un mot de passe.	
		2.	Supprimer les deux dernières propriétés (** username ** et **mot de passe**) depuis le JSON de fichiers et de supprimer le **virgule (,)** caractère à la fin de la dernière ligne du script JSON restants.
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	Si vous utilisez **l'authentification Windows**:
		1. Pour le **connectionString** propriété, remplacez **< nom_serveur >** et **< databasename >** avec des noms de votre serveur SQL server sur site et la base de données. Définissez **la sécurité intégrée de** à **True**. Supprimer **ID** et **mot de passe** à partir de la chaîne de connexion.
			
				{
    				"name": "SqlServerLinkedService",
    				"properties": {
        				"type": "OnPremisesSqlLinkedService",
        				"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
		   				"gatewayName": "adftutorialgateway",
				        "username": "<Specify user name if you are using Windows Authentication>",
				        "password": "<Specify password for the user account>"
    				}
				}		
		
6. Cliquez sur **déployer** sur la barre d'outils pour déployer le SqlServerLinkedService. Vérifiez que le message **lié SERVICE créé avec succès** sur la barre de titre. Vous devez également voir les **SqlServerLinkedService** dans l'arborescence sur la gauche.
		   
	![Déploiement SqlServerLinkedService réussi][image-editor-sql-linked-service-successful]
	
  
> [AZURE.NOTE]Vous pouvez également créer un service SQL Server est lié en cliquant sur **nouveau magasin de données** bouton de barre d'outils sur le **Services liés** lame. Si vous choisissez cette méthode, vous définissez les informations d'identification pour la source de données à l'aide de l'application ClickOnce de gestionnaire de références qui s'exécute sur l'ordinateur que l'accès au portail. Si vous accédez au portail à partir d'un ordinateur différent de l'ordinateur de passerelle, il se peut que vous devez vous assurer que l'application du Gestionnaire d'informations d'identification peut se connecter à l'ordinateur de passerelle. Si l'application ne peut pas atteindre l'ordinateur de passerelle, il n'autorise pas de définir les informations d'identification pour la source de données et de tester la connexion à la source de données.

#### Ajout d'un service lié pour un compte de stockage Azure
 
1. Dans le **éditeur**, cliquez sur **nouveau magasin de données** bouton sur la barre d'outils et sélectionnez **stockage Azure** dans le menu déroulant. Vous devez voir le modèle JSON pour la création d'un service de stockage Azure lié dans le volet droit. 

	![Éditeur nouveau bouton de magasin de données][image-editor-newdatastore-button]
    
6. Remplacez **< nom_compte >** et **< accountkey >** avec le nom de compte et les valeurs de clé de compte pour votre compte de stockage Azure.

	![Stockage d'objets Blob éditeur JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Voir [référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) pour plus d'informations sur les propriétés JSON.

6. Cliquez sur **déployer** sur la barre d'outils pour déployer le StorageLinkedService. Vérifiez que le message **lié SERVICE créé avec succès** sur la barre de titre.

	![Stockage d'objets Blob éditeur déployer][image-editor-blob-storage-deploy]

 
## Étape 3: Créer d'entrée et sortie de datasets
Dans cette étape, vous allez créer des jeux de données d'entrée et de sortie qui représentent les données d'entrée et de sortie pour l'opération de copie (base de données SQL Server locale = > stockage d'objets blob Azure). Avant de créer des jeux de données ou des tables (jeux de données rectangulaires), vous devez effectuer les opérations suivantes (les étapes sont détaillées après la liste) :

- Créez une table nommée **emp** dans la base de données SQL Server vous ajouté comme un service lié aux données en usine et insérer deux exemples d'entrées dans la table.
- - Si vous n'avez pas suivi le didacticiel de [prise en main Azure Data Factory][adfgetstarted] l'article, créez un conteneur blob appelé **adftutorial** dans Azure blob compte de stockage que vous avez ajouté un service lié à la fabrication de données.

### Préparation du serveur SQL Server local pour le didacticiel

1. Dans la base de données que vous avez spécifié pour le serveur local SQL Server liées service (** SqlServerLinkedService **), le script SQL suivant permet de créer le **emp** table dans la base de données.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. Insérer des exemples dans la table :


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### Créer une table d'entrée

1.	Dans le **données fabrique éditeur**, cliquez sur **nouveau dataset** dans la barre de commandes, cliquez sur **SQL sur site**. 
2.	Remplacez le JSON dans le volet droit avec le texte suivant :    

        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "SqlServerLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1,       
	    			"waitOnExternal":
	    			{
        				"retryInterval": "00:01:00",
	        			"retryTimeout": "00:10:00",
	        			"maximumRetry": 3
	    			}
		  
        		}
    		}
		}

	Notez les points suivants :
	
	- emplacement **type** a **OnPremisesSqlServerTableLocation**.
	- **tableName** est définie sur **emp**.
	- **linkedServiceName** a **SqlServerLinkedService** (vous avez créé ce service lié à l'étape 2).
	- Pour une table d'entrée n'est pas générée par un autre pipeline fabrique de données Azure, vous devez spécifier **waitOnExternal** section dans le JSON. Cela signifie que les données d'entrée sont produites à l'extérieur du service Azure Data Factory.   

	Voir [référence de script JSON][json-script-reference] pour plus d'informations sur les propriétés JSON.

2. Cliquez sur **déployer** sur la barre de commandes pour déployer le groupe de données (table est un dataset rectangulaire). Vérifiez que vous voyez un message sur la barre de titre indique **TABLE déployé avec succès**.


### Créer la table de sortie

1.	Dans le **données fabrique éditeur**, cliquez sur **nouveau dataset** dans la barre de commandes, cliquez sur **stockage d'objets Blob Azure**.
2.	Remplacez le JSON dans le volet droit avec le texte suivant : 

		{
    		"name": "OutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/outfromonpremdf",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Notez les points suivants :
	
	- emplacement **type** a **AzureBlobLocation**.
	- **linkedServiceName** a **StorageLinkedService** (vous avez créé ce service lié à l'étape 2).
	- **folderPath** a **adftutorial/outfromonpremdf** où outfromonpremdf est le dossier dans le conteneur adftutorial. Vous devez simplement créer le **adftutorial** conteneur.
	- Le **disponibilité** est définie sur **toutes les heures** (** fréquence ** valeur **heure** et **intervalle** la valeur **1**). Le service de données fabrique génère une tranche de données de sortie toutes les heures dans les **emp** table dans la base de données SQL Azure. 

	Si vous ne spécifiez pas un **nom de fichier** pour un **table d'entrée**, tous les fichiers BLOB à partir du dossier d'entrée (** folderPath **) sont considérés comme entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Consultez les exemples de fichiers dans le [didacticiel][adf-tutorial] pour obtenir des exemples.
 
	Si vous ne spécifiez pas de **fileName** pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

	Pour définir **folderPath** et **fileName** dynamiquement selon le **SliceStart** fois, utilisez la propriété partitionedBy. Dans l'exemple suivant, folderPath utilise les valeurs Year, Month et Day à partir de SliceStart (heure de début de la partie en cours de traitement), alors que fileName utilise la valeur Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2014-10-20T08:00:00, la valeur folderName est wikidatagateway/wikisampledataout/2014/10/20, alors que la valeur de fileName est 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Voir [référence de script JSON][json-script-reference] pour plus d'informations sur les propriétés JSON.

2.	Cliquez sur **déployer** sur la barre de commandes pour déployer le groupe de données (table est un dataset rectangulaire). Vérifiez que vous voyez un message sur la barre de titre indique **TABLE déployé avec succès**.
  

## Étape 4: Créer et exécuter un pipeline
Dans cette étape, vous allez créer un **pipeline** avec un **activité copie** qui utilise **EmpOnPremSQLTable** en tant qu'entrée et **OutputBlobTable** en tant que sortie.

1.	Cliquez sur **nouveau pipeline** sur la barre de commandes. Si vous ne voyez pas le bouton, cliquez sur **... (sélection)** pour développer la barre de commandes.
2.	Remplacez le JSON dans le volet droit avec le texte suivant :   


        {
			"name": "ADFTutorialPipelineOnPrem",
    		"properties":
    		{
        		"description" : "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
	       		 "activities":
	        	[
			    	{
						"name": "CopyFromSQLtoBlob",
						"description": "Copy data from on-prem SQL server to blob",		
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpOnPremSQLTable"} ],
						"outputs": [ {"name": "OutputBlobTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "SqlSource",
								"sqlReaderQuery": "select * from emp"
							},
							"sink":
							{
								"type": "BlobSink"
							}	
						},
						"Policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 0,
							"timeout": "01:00:00"
						}		

				     }
	        	],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	Notez les points suivants :
 
	- Dans la section des activités, il est uniquement d'activité dont **type** a **CopyActivity**.
	- **Entrée** pour l'activité est définie sur **EmpOnPremSQLTable** et **sortie** pour l'activité est définie sur **OutputBlobTable**.
	- Dans le **transformation** section, **SqlSource** est spécifié comme le **type de source de** et **BlobSink **est spécifié comme le **récepteur type**. - Requête SQL **sélectionner * de la table emp** est spécifié pour le **sqlReaderQuery** propriété du **SqlSource**.

	> [AZURE.NOTE]Remplacez la valeur de la **Démarrer** propriété avec la date du jour et **fin** valeur avec le jour suivant. Les deux commencer et dates/heures de fin doivent être dans [au format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. Le **fin** temps est facultative, mais nous allons l'utiliser dans ce didacticiel. Si vous ne spécifiez pas de valeur pour le **fin** propriété, il est calculé en tant que « ** début + 48 heures ** ». Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** comme valeur pour le **fin** propriété. Vous définissez la durée dans lequel les tranches de données seront traités selon le **disponibilité** propriétés ont été définies pour chaque table de la fabrique de données Azure. Dans l'exemple ci-dessus, 24 tranches de données existent, car une tranche de données est générée toutes les heures.
	
2. Cliquez sur **déployer** sur la barre de commandes pour déployer le groupe de données (table est un dataset rectangulaire). Vérifiez que vous voyez un message sur la barre de titre indique **PIPELINE déployé avec succès**.
5. Maintenant, fermez le **éditeur** lame en cliquant sur **X**. Cliquez sur **X** à nouveau pour fermer le volet ADFTutorialDataFactory avec la vue arborescence et la barre d'outils. Si vous voyez **vos modifications non enregistrées seront ignorées** cliquez sur **OK**.
6. Vous devez être à la **DATA FACTORY** lame pour le **ADFTutorialOnPremDF**.

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, les services liés, les tables et un pipeline et avez planifié le pipeline.

### Vue de la fabrique de données dans une vue de diagramme 
1. Dans le **portail Azure Preview**, cliquez sur **diagramme** vignette sur la page d'accueil de la **ADFTutorialOnPremDF** factory de données. :

	![Lien de diagramme][image-data-factory-diagram-link]

2. Le diagramme ressemble à ce qui suit :

	![Vue de diagramme][image-data-factory-diagram-view]

	Vous pouvez faire un zoom, un zoom arrière, zoom à 100 %, zoom pour ajuster automatiquement positionner les pipelines et les tables et afficher les informations de lignage (met en surbrillance des éléments en amont et en aval des éléments sélectionnés). Vous pouvez double-blick sur un objet (table d'entrée/sortie ou pipeline) pour afficher les propriétés pour celle-ci.

## Étape 5: Surveiller les datasets et les pipelines
Dans cette étape, vous allez utiliser le portail Azure pour surveiller ce qui se passe dans une fabrique de données Azure. Vous pouvez également utiliser les applets de commande PowerShell pour surveiller les jeux de données et les pipelines. Pour plus d'informations sur l'utilisation des applets de commande pour l'analyse, consultez [analyse et gestion de fabrique données Azure à l'aide de PowerShell][monitor-manage-powershell].

1. Accédez à **portail Azure Preview** (si vous l'avez fermé)
2. Si le volet de **ADFTutorialOnPremDF** est pas ouvert, ouvrez-le en cliquant sur **ADFTutorialOnPremDF** sur la **tableau d'accueil**.
3. Vous devez voir les **nombre** et **noms** de tables et de pipeline que vous avez créé sur ce panneau.

	![Page d'accueil de fabrique de données][image-data-factory-homepage-2]
4. Maintenant, cliquez sur **Datasets** vignette.
5. Sur le **Datasets** lame, cliquez sur le **EmpOnPremSQLTable**.

	![Tranches EmpOnPremSQLTable][image-data-factory-onprem-sqltable-slices]

6. Notez que les tranches de données à l'heure actuelle ont déjà été produits et qu'ils sont **prêt**. Cela est dû au fait que vous avez inséré les données dans la base de données SQL Server et qu'elles y sont tout le temps. Vérifiez qu'aucune coupe n'apparaît dans le **tranches problème** section en bas.


	Les deux **récemment mis à jour les tranches** et **Échec récemment tranches** listes sont triés par le **heure de dernière mise à jour**. L'heure de mise à jour d'une tranche de données est modifié dans les situations suivantes.
    

	-  Vous mettre à jour l'état de la coupe manuellement, par exemple, à l'aide de la **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **exécuter** sur la **tranche** lame pour la tranche.
	-  La tranche modifie l'état en raison d'une exécution (par exemple, une exécution démarré, une exécution s'est terminée et a échoué, une exécution s'est terminée et réussie, etc.).
 
	Cliquez sur le titre des listes ou des **... (points de suspension)** Pour consulter la liste supérieure des tranches. Cliquez sur **filtre** sur la barre d'outils pour filtrer les secteurs.
	
	Pour afficher les tranches de données triés par les heures de début et de fin de tranche au lieu de cela, cliquez sur **tranches de données (par heure de tranche)** vignette.

7. Maintenant, dans le **Datasets** lame, cliquez sur **OutputBlobTable**.

	![Tranches OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Vérifiez que les tranches jusqu'à l'heure actuelle sont produites et **prêt**. Attendez que les États des tranches jusqu'à l'heure actuelle sont définies sur **prêt**.
9. Cliquez sur un secteur de données à partir de la liste et vous devez voir les **tranche de données** lame.

	![Lame de tranche de données][image-data-factory-dataslice-blade]

	Si la section n'est pas dans le **prêt** état, vous pouvez voir les tranches en amont qui ne sont pas prêts et bloquent la tranche actuelle à partir de l'exécution dans le **tranches en amont qui ne sont pas prêts** liste.

10. Cliquez sur le **activité exécuter** dans la liste du bas pour afficher **Détails d'activité l'exécution**.

	![Volet de détails de l'exécution d'activité][image-data-factory-activity-run-details]

11. Cliquez sur **X** pour fermer toutes les lames jusqu'à ce que vous reveniez à la lame de base pour le **ADFTutorialOnPremDF**.
14. (facultatif) Cliquez sur **Pipelines**, cliquez sur **ADFTutorialOnPremDF**, et l'extraction des tables d'entrée (** consommé **) ou tables de sortie (** produit **).
15. Utiliser des outils tels que **Azure Storage Explorer** pour vérifier la sortie.

	![Azure Storage Explorer][image-data-factory-stroage-explorer]


## Création et enregistrement d'une passerelle à l'aide d'Azure PowerShell 
Cette section décrit comment créer et enregistrer une passerelle à l'aide des cmdlets Azure PowerShell.

1. Lancez **Azure PowerShell** en mode administrateur. 
2. Les applets de commande Azure Data Factory sont disponibles dans le **AzureResourceManager** mode. Exécutez la commande suivante pour basculer vers le **AzureResourceManager** mode.     

        switch-azuremode AzureResourceManager


2. Utilisez le **New-AzureDataFactoryGateway** applet de commande pour créer une passerelle logique comme suit :

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Exemple de commande et de sortie**:


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. Utilisez le **New-AzureDataFactoryGatewayKey** applet de commande pour générer une clé d'inscription de la passerelle nouvellement créée et stocker la clé dans une variable locale **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Exemple de sortie de commande :**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Dans Azure PowerShell, basculez vers le dossier : **C:\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** et exécutez **RegisterGateway.ps1** script associé à la variable locale **$Key** comme indiqué dans la commande suivante pour inscrire l'agent du client installé sur votre ordinateur avec la passerelle logique, vous créez précédemment.

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Vous pouvez utiliser la **Get-AzureDataFactoryGateway** pour obtenir la liste des passerelles dans votre fabrique de données. Lors de la **état** montre **en ligne**, cela signifie que votre passerelle est prête à utiliser.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Vous pouvez supprimer une passerelle à l'aide de la **Remove-AzureDataFactoryGateway** applet de commande et de mise à jour la description d'une passerelle à l'aide du **AzureDataFactoryGateway de jeu** applets de commande. Pour obtenir la syntaxe et d'autres détails sur ces cmdlets, consultez la rubrique Référence des cmdlets Azure Data Factory.



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-editor]: data-factory-editor.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: data-factory-get-started.md
[monitor-manage-powershell]: data-factory-monitor-manage-using-powershell.md





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[image-author-deploy-tile]: ./media/data-factory-use-onpremises-datasources/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-use-onpremises-datasources/editor-blob-storage-settings.png
[image-editor-blob-storage-deploy]: ./media/data-factory-use-onpremises-datasources/editor-deploy-blob-linked-service.png
[image-editor-newdatastore-onpremsql-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-onpremsql-button.png
[image-editor-newdatastore-onpremsql-settings]: ./media/data-factory-use-onpremises-datasources/editor-onprem-sql-settings.png
[image-editor-sql-linked-service-successful]: ./media/data-factory-use-onpremises-datasources/editor-sql-linked-service-successful.png

[gateway-status]: ./media/data-factory-use-onpremises-datasources/gateway-status.png
[express-setup-succeeded]: ./media/data-factory-use-onpremises-datasources/express-setup-succeeded.png
[image-data-factory-onprem-new-everything]: ./media/data-factory-use-onpremises-datasources/OnPremNewEverything.png

[image-data-factory-onprem-datastorage-cache-backup]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageCacheBackup.png

[image-data-factory-onprem-datastorage-see-all]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageSeeAll.png

[image-data-factory-onprem-dataservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremDataServicesBlade.png

[image-data-factory-onprem-datafactory-preview-blade]: ./media/data-factory-use-onpremises-datasources/DataFactoryPreviewBlade.png

[image-data-factory-onprem-create-resource-group]: ./media/data-factory-use-onpremises-datasources/OnPremCreateResourceGroup.png

[image-data-factory-add-to-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataFactoryAddToStartboard.png

[image-data-factory-notifications-hub]: ./media/data-factory-use-onpremises-datasources/OnPremNotificationsHub.png

[image-data-factory-datafactory-homepage]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage.png

[image-data-factory-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremStartboard.png

[image-data-factory-linkedservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesBlade.png

[image-data-factory-linkedservices-add-datastore-button]: ./media/data-factory-use-onpremises-datasources/ONPremLinkedServicesAddDataStoreButton.png

[image-data-factory-new-datastore-blade]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreBlade.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!---HONumber=GIT-SubDir-->