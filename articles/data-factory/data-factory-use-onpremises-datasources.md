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
	ms.date="07/10/2015" 
	ms.author="spelluru"/>

# Activation de vos pipelines pour utiliser des données locales

Pour activer vos pipelines dans une fabrique de données Azure pour utiliser une source de données locale, vous devez ajouter la source de données locale comme service lié à la fabrique de données à l’aide du portail de gestion Azure ou d’Azure PowerShell.
 
Pour ajouter une source de données locale comme service lié à une fabrique de données, vous devez d’abord télécharger et installer la passerelle de gestion de données Microsoft sur un ordinateur local et configurer le service lié pour que la source de données locale utilise la passerelle.


## <a href="DMG"></a> Passerelle de gestion des données

La **passerelle de gestion des données** est un logiciel qui connecte des sources de données locales à des services cloud, de manière gérée et sécurisée. Avec la passerelle de gestion de données, vous pouvez :

- **Effectuer la connexion à des données locales pour accéder aux données hybrides** : vous pouvez connecter des données locales aux services cloud pour bénéficier des services cloud tout en conservant l’exécution des activités sur les données locales.
- **Définir un proxy de données sécurisées** : vous pouvez définir les sources de données locales qui sont exposées avec la passerelle de gestion des données, afin que cette dernière authentifie la demande de données des services cloud et protège les sources de données locales.
- **Gérer votre passerelle pour obtenir une gouvernance intégrale** : vous disposez de fonctions de surveillance et de journalisation intégrales de toutes les activités dans la passerelle de gestion des données, à des fins de gestion et de gouvernance.
- **Déplacer les données efficacement** : les données sont transférées en parallèle et résistent aux problèmes intermittents du réseau, grâce à la logique de nouvelle tentative automatique.


La passerelle de gestion de données a une gamme complète de fonctionnalités de connexion de données locales qui incluent les éléments suivants :

- **Du pare-feu discret au pare-feu d’entreprise** : la passerelle de gestion des données fonctionne juste après son installation, sans vous obliger à ouvrir une connexion de pare-feu ni à apporter des modifications importantes à votre infrastructure de réseau d’entreprise. 
- **Chiffrer les informations d’identification avec votre certificat** : les informations d’identification permettant de se connecter aux sources de données sont chiffrées avec un certificat entièrement détenu par un utilisateur. Sans certificat, personne ne peut déchiffrer les informations d’identification en texte brut, y compris Microsoft.

### Considérations sur l’utilisation de la passerelle de gestion de données
1.	Une instance unique de la passerelle de gestion de données peut être utilisée pour plusieurs sources de données locales, mais n’oubliez pas qu’une **passerelle est liée à une fabrique de données Microsoft Azure** et ne peut pas être partagée avec une autre fabrique de données.
2.	Vous ne pouvez installer qu’**une seule instance de la passerelle de gestion de données** sur votre ordinateur. Si deux fabriques de données doivent accéder aux sources de données locales, vous devez installer des passerelles sur deux ordinateurs locaux où chaque passerelle est liée à une fabrique de données distincte.
3.	Il n’est pas nécessaire que la **passerelle soit sur le même ordinateur que la source de données**, mais le fait qu’elle soit proche de cette dernière réduit le temps nécessaire à la passerelle pour se connecter à la source de données. Nous vous recommandons d’installer la passerelle sur un ordinateur différent de celui qui héberge la source de données locale, afin que la passerelle ne soit pas en concurrence avec la source de données pour l’octroi des ressources.
4.	Vous pouvez avoir **plusieurs passerelles sur différents ordinateurs connectés à la même source de données locale**. Par exemple, vous pouvez avoir deux passerelles desservant deux fabriques de données, alors que la même source de données locale est enregistrée auprès des deux fabriques de données. 
5.	Si une passerelle est déjà installée sur votre ordinateur desservant un scénario **Power BI**, installez une **passerelle distincte pour Azure Data Factory** sur un autre ordinateur.

### Considérations liées aux ports et à la sécurité 
- Le programme d’installation de la passerelle de gestion des données ouvre les ports **8050** et **8051** sur l’ordinateur de passerelle. Ces ports sont utilisés par le **Gestionnaire des informations d’identification** (application ClickOnce), qui vous permet de définir les informations d’identification d’un service lié local et de tester la connexion à la source de données. Ces ports ne sont pas accessibles depuis Internet et il n’est pas nécessaire de les ouvrir dans le pare-feu d’entreprise.
- Lors de la copie des données depuis/vers une base de données SQL Server locale vers/à partir d’une base de données SQL Azure, vérifiez les points suivants :
 
	- Le pare-feu sur l’ordinateur de passerelle autorise les communications TCP sortantes sur le port **TCP** **1433**.
	- Configurez les [paramètres de pare-feu SQL Azure](https://msdn.microsoft.com/library/azure/jj553530.aspx) pour ajouter l’**adresse IP de l’ordinateur de passerelle** aux **adresses IP autorisées**.

- Lors de la copie des données depuis ou vers le serveur SQL Server local vers une destination, si la passerelle et les ordinateurs SQL Server sont différents, procédez comme suit : [configurez le pare-feu Windows](https://msdn.microsoft.com/library/ms175043.aspx) sur l’ordinateur SQL Server, afin que la passerelle puisse accéder à la base de données via les ports qu’écoute l’instance de SQL Server. Pour l’instance par défaut, il s’agit du port 1433.

- Vous devez lancer le **Gestionnaire des informations d’identification** de l’application sur un ordinateur en mesure de se connecter à la passerelle de gestion des données pour pouvoir définir les informations d’identification de la source de données et tester la connexion à cette dernière.

### Installation de la passerelle : configuration requise 

1.	Les versions de **système d’exploitation** prises en charge sont les suivantes : Windows 7, Windows 8/8.1, Windows Server 2008 R2 et Windows Server 2012.
2.	La **configuration** recommandée pour l’ordinateur de passerelle est la suivante : au moins 2 GHz, 4 cœurs, 8 Go de RAM et 80 Go d’espace disque.
3.	Si l’ordinateur hôte est en veille prolongée, la passerelle n’est pas en mesure de répondre à la demande de données. Vous devez donc configurer un **plan de gestion de l’alimentation** approprié sur l’ordinateur avant d’installer la passerelle. L’installation de la passerelle ouvre une invite si l’ordinateur est configuré pour la mise en veille prolongée.  

La passerelle de gestion des données sérialise et désérialise les données sources et de récepteur sur l'ordinateur sur lequel elles sont hébergées. Elle effectue également des conversions de type sur les données qu'elle copie. Pendant l'opération de copie, la passerelle lit des données de la source vers une mémoire tampon pendant qu’un autre enregistreur de threads enregistre cette mémoire tampon sur le récepteur. Plusieurs tâches d'activité de copie peuvent s'exécuter en même temps sur l'ordinateur hôte de la passerelle pendant les heures d’activité maximale. De ce fait, la consommation de mémoire et de ressources du processeur est beaucoup plus élevée qu’en période d’inactivité. L'ordinateur hôte exécutant une passerelle de gestion des données peut donc nécessiter plus de ressources que ce qui est décrit ci-dessus dans la configuration minimale requise de l’ordinateur, ou moins de ressources pendant les périodes d'inactivité.


## Procédure pas à pas

Dans cette procédure pas à pas, vous créez une fabrique de données avec un pipeline qui déplace les données d’une base de données SQL Server locale vers un objet blob Azure.

## Étape 1 : Création d’une fabrique de données Microsoft Azure
Dans cette étape, vous utilisez le portail de gestion Microsoft Azure pour créer une instance Azure Data Factory nommée **ADFTutorialOnPremDF**. Vous pouvez également créer une fabrique de données à l’aide des cmdlets Azure Data Factory.

1.	Une fois connecté au [portail Azure en version préliminaire][azure-preview-portal], cliquez dans le coin inférieur gauche sur **NOUVEAU**, sélectionnez **Analyse de données** dans le panneau **Créer**, puis cliquez sur **Data Factory** dans le panneau **Analyse de données**.

	![Nouveau -> DataFactory][image-data-factory-new-datafactory-menu]
  
6. Dans le panneau **Nouvelle fabrique de données** :
	1. Saisissez **ADFTutorialOnPremDF** dans le champ **Nom**.
	2. Cliquez sur **NOM DU GROUPE DE RESSOURCES** et sélectionnez **ADFTutorialResourceGroup** (si vous avez effectué le didacticiel de [prise en main d’Azure Data][adfgetstarted]). Vous pouvez sélectionner un groupe de ressources existant ou en créer un. Pour créer un groupe de ressources :
		1. Cliquez sur **Créer un groupe de ressources**.
		2. Dans le panneau **Créer un groupe de ressources**, saisissez un **nom** pour le groupe de ressources et cliquez sur **OK**.

7. Notez que l’option **Ajouter au tableau d’accueil** est activée sur le panneau **Nouvelle fabrique de données**.

	![Ajouter au Tableau d’accueil][image-data-factory-add-to-startboard]

8. Dans le panneau **Nouvelle fabrique de données**, cliquez sur **Créer**.

	Le nom de la fabrique de données Azure doit être un nom global unique. Si l'erreur suivante s'affiche : **Le nom de la fabrique de données « ADFTutorialOnPremDF » n'est pas disponible**, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialOnPremDF), puis essayez de la recréer. Utilisez ce nom à la place d'ADFTutorialOnPremDF quand vous effectuez les étapes restantes de ce didacticiel.

9. Recherchez des notifications du processus de création dans le concentrateur **NOTIFICATIONS** sur la gauche. Cliquez sur **X** pour fermer le panneau **NOTIFICATIONS** si celui-ci est ouvert.

	![Concentrateur NOTIFICATIONS][image-data-factory-notifications-hub]

11. Une fois la création terminée, vous verrez apparaître le panneau **Data Factory** comme indiqué ci-dessous :

	![Page d’accueil Data Factory][image-data-factory-datafactory-homepage]

## Étape 2 : Créer un portail de gestion des données
5.	Dans le panneau **Data Factory** de **ADFTutorialOnPremDF**, cliquez sur **Services liés**. 

	![Page d’accueil Data Factory][image-data-factory-home-age]

2.	Dans le panneau **Services liés**, cliquez sur **+ Passerelle de données**.

	![Services liés - Ajouter un bouton Passerelle][image-data-factory-linkedservices-add-gateway-button]

2. Dans le panneau **Créer**, saisissez **adftutorialgateway** dans le champ **Nom**, puis cliquez sur **OK**.

	![Panneau Créer une passerelle][image-data-factory-create-gateway-blade]

3. Dans le panneau **Configurer**, cliquez sur **Installer directement sur cet ordinateur**. Cette opération télécharge le package d’installation de la passerelle, installe, configure et inscrit la passerelle sur l’ordinateur.

	> [AZURE.NOTE]Utilisez Internet Explorer ou un navigateur web compatible Microsoft ClickOnce.

	![Passerelle - Panneau Configurer][image-data-factory-gateway-configure-blade]

	Il s’agit de la méthode la plus simple (un clic) pour télécharger, installer, configurer et inscrire la passerelle en une seule étape. Vous pouvez voir que l’application **Gestionnaire de configuration de la passerelle de gestion de données Microsoft** est installée sur votre ordinateur. Vous pouvez également trouver l’exécutable **ConfigManager.exe** dans le dossier suivant : **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.

	Vous pouvez également télécharger et installer manuellement la passerelle en utilisant les liens de ce panneau et l’enregistrer à l’aide de la clé indiquée dans la zone de texte **ENREGISTRER AVEC LA CLÉ**.
	
	Pour en savoir plus sur la passerelle, y compris les meilleures pratiques et des remarques importantes, consultez la section [Passerelle de gestion des données](#DMG).

	>[AZURE.NOTE]Vous devez être administrateur sur l’ordinateur local pour pouvoir installer et configurer la passerelle de gestion des données avec succès. Vous pouvez ajouter des utilisateurs supplémentaires au groupe Windows local d’utilisateurs de la passerelle de gestion des données. Les membres de ce groupe sont en mesure d’utiliser l’outil Gestionnaire de configuration de la passerelle de gestion de données pour configurer la passerelle.

4. Cliquez sur le concentrateur **NOTIFICATIONS**, sur la gauche. Patientez jusqu’à ce que le message **L’installation rapide de « adftutorialgateway » a réussi** s’affiche dans le panneau **Notifications**.

	![Réussite de l’installation rapide][express-setup-succeeded]
5. Cliquez sur **OK** dans le panneau **Créer**, puis dans le panneau **Nouvelle passerelle de données**.
6. Fermez le panneau **Services liés** (en appuyant sur le bouton **X** dans l’angle supérieur droit de la fenêtre) et rouvrez le panneau **Services liés** pour afficher le dernier état de la passerelle. 
7. Vérifiez que l’**état** de la passerelle est **En ligne**. 

	![État de la passerelle][gateway-status]
5. Lancez l’application **Gestionnaire de configuration de la passerelle de gestion de données Microsoft** sur votre ordinateur.

	![Gestionnaire de configuration de la passerelle][image-data-factory-gateway-configuration-manager]

6. Attendez que les valeurs soient définies comme suit :
	1. Si l’**état** du service n’est pas défini sur **Démarré**, cliquez sur **Démarrer le service** pour démarrer le service et attendez une minute que les autres champs soient actualisés.
	2. Le **nom de la passerelle** est défini sur **adftutorialgateway**.
	3. Le **nom de l’instance** est défini sur **adftutorialgateway**.
	4. L’**état de la clé de la passerelle** est défini sur **Enregistré**.
	5. La barre d’état située au bas de l’écran affiche le message **Connecté au service de cloud de la passerelle de gestion de données** accompagné d’une **coche verte**.
	
7. Dans le panneau **Services liés**, vérifiez que l’**état** de la passerelle est bien défini sur **Correct**.
8. Fermez tous les panneaux jusqu’à ce que vous atteigniez la page d’accueil de **Data Factory**. 

## Étape 2 : Créer des services liés 
Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **SqlServerLinkedService**. Le service **SqlServerLinkedService** lie une base de données SQL Server locale et le service lié **StorageLinkedService** lie un magasin d’objets blob Microsoft Azure à **ADFTutorialDataFactory**. Plus loin dans cette procédure pas à pas, vous allez créer un pipeline qui copie les données de la base de données SQL Server locale vers le magasin d’objets blob Azure.

### Ajout d’un service lié à une base de données SQL Server locale
1.	Dans le panneau **DATA FACTORY**, cliquez sur la vignette **Créer et déployer** pour lancer l’**éditeur** de la fabrique de données.

	![Vignette Créer et déployer][image-author-deploy-tile]

	Pour obtenir une présentation détaillée de Data Factory Editor, consultez la rubrique [Data Factory Editor][data-factory-editor].

2.	Dans l’**éditeur**, cliquez sur le bouton **Nouveau magasin de données** de la barre d’outils, puis sélectionnez **Base de données SQL Server locale** dans le menu déroulant.

	![Éditeur - Bouton Nouveau magasin de données][image-editor-newdatastore-onpremsql-button]
    
3.	Vous devez voir apparaître le modèle JSON pour la création d’un service lié SQL Server local dans le volet droit. ![Service lié SQL local - Paramètres][image-editor-newdatastore-onpremsql-settings]

4.	Dans le volet JSON, procédez comme suit :
	1.	Pour la propriété **gatewayName**, saisissez **adftutorialgateway** afin de remplacer l’ensemble du texte à l’intérieur des guillemets doubles.  
	2.	Si vous utilisez l’**authentification SQL**, procédez comme suit : 
		1.	Pour la propriété **connectionString**, remplacez **<nom\_serveur>**, **<nom\_BD>**, **<utilisateur>** et **<mot\_de\_passe>** par le nom de votre système SQL Server local, le nom de la base de données, le nom du compte d’utilisateur et le mot de passe. Pour spécifier un nom d'instance, utilisez le caractère d'échappement : . Par exemple :**server\\instancename**. 	
		2.	Supprimez les deux dernières propriétés (\*\*username\*\* et **password**) du fichier JSON et supprimez la **virgule (,)** à la fin de la dernière ligne du script JSON restant.
		
				{
				  "name": "SqlServerLinkedService",
				  "properties": {
				    "type": "OnPremisesSqlServer",
				    "typeProperties": {
				      "ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
				      "gatewayName": "adftutorialgateway"
				    }
				  }
				}
	3.	Si vous utilisez **l’authentification Windows**, procédez comme suit :
		1. Pour la propriété **connectionString**, remplacez **<nom\_serveur>** et **<nom\_BD>** par le nom de votre serveur SQL local et de votre base de données. Définissez le paramètre **Sécurité intégrée** sur **True**. Supprimez les éléments **ID** et **Password** de la chaîne de connexion.
			
				{
				  "name": "SqlServerLinkedService",
				  "properties": {
				    "type": "OnPremisesSqlServer",
				    "typeProperties": {
				      "ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
				      "gatewayName": "adftutorialgateway",
				      "username": "<Specify user name if you are using Windows Authentication>",
				      "password": "<Specify password for the user account>"
				    }
				  }
				}		
		
6. Cliquez sur l’option **Déployer** de la barre d’outils pour déployer le service lié SqlServerLinkedService. Vérifiez que le message **SERVICE LIÉ CRÉÉ AVEC SUCCÈS** s’affiche dans la barre de titre. Vous devez également voir apparaître **SqlServerLinkedService** dans l’arborescence de gauche.
		   
	![Déploiement SqlServerLinkedService réussi][image-editor-sql-linked-service-successful]
	
  
Vous pouvez également créer un service lié SQL Server en cliquant sur le bouton **Nouveau magasin de données** de la barre d’outils dans le panneau **Services liés**. Si vous choisissez cette méthode, vous définissez les informations d’identification pour la source de données à l’aide de l’application ClickOnce de gestionnaire d’informations d’identification qui s’exécute sur l’ordinateur accédant au portail. Si vous accédez au portail à partir d’un ordinateur différent de l’ordinateur de passerelle, vous devrez peut-être vous assurer que l’application Gestionnaire d’informations d’identification peut se connecter à l’ordinateur de passerelle. Sinon, vous ne pourrez pas définir les informations d’identification de la source de données, ni tester la connexion à la source de données.

#### Ajout d’un service lié pour un compte de stockage Azure
 
1. Dans l’**éditeur**, cliquez sur le bouton **Nouveau magasin de données** de la barre d’outils, puis sélectionnez **Stockage Azure** dans le menu déroulant. Le modèle JSON de création d’un service lié Microsoft Azure Storage doit apparaître dans le volet droit. 

	![Éditeur - Bouton Nouveau magasin de données][image-editor-newdatastore-button]
    
6. Remplacez les éléments **<nom\_compte>** et **<clé\_compte>** par le nom du compte et les valeurs de clé de compte de votre compte Microsoft Azure Storage.

	![Éditeur - Stockage d’objets blob - JSON][image-editor-blob-storage-json]
	
	Pour en savoir plus sur les propriétés JSON, voir [Référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

6. Cliquez sur l’option **Déployer** de la barre d’outils pour déployer le service lié StorageLinkedService. Vérifiez que le message **SERVICE LIÉ CRÉÉ AVEC SUCCÈS** s’affiche dans la barre de titre.

	![Éditeur - Stockage d’objets blob - Déployer][image-editor-blob-storage-deploy]

 
## Étape 3 : Créer des jeux de données d’entrée et de sortie
Dans cette étape, vous allez créer des jeux de données d’entrée et de sortie qui représentent les données d’entrée et de sortie pour l’opération de copie (base de données SQL Server locale = > stockage d’objets blob Azure). Avant de créer des jeux de données ou des tables (jeux de données rectangulaires), vous devez effectuer les opérations suivantes (les étapes sont détaillées après la liste) :

- Créez une table nommée **emp** dans la base de données SQL Server que vous avez ajoutée en tant que service lié à la fabrique de données et insérez quelques exemples d’entrées dans la table.
- - Si vous n’avez pas suivi le didacticiel de l’article [Prise en main d’Azure Data Factory][adfgetstarted], créez un conteneur d’objets blob nommé **adftutorial** dans le compte de stockage d’objets blob Azure que vous avez ajouté en tant que service lié à la fabrique de données.

### Préparation du serveur SQL Server local pour le didacticiel

1. Dans la base de données que vous avez spécifiée pour le service lié SQL Server local (\*\*SqlServerLinkedService\*\*), utilisez le script SQL suivant pour créer la table **emp** dans la base de données.


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



### Créer une table d’entrée

1.	Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données** dans la barre de commande, puis sur **SQL local**. 
2.	Remplacez le code JSON du volet droit par le texte suivant :    

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	Notez les points suivants :
	
	- Le **type** est défini sur **SqlServerTable**.
	- Le paramètre **tablename** est défini sur **emp**.
	- Le paramètre **linkedServiceName** est défini sur **SqlServerLinkedService** (vous avez créé ce service lié à l’étape 2).
	- Pour une table d’entrée qui n’est pas générée par un autre pipeline dans Azure Data Factory, vous devez définir la propriété **external** sur **true**. Vous pouvez éventuellement spécifier des stratégies dans la section **externalData**.   

	Pour en savoir plus sur les propriétés JSON, voir [Référence de script JSON][json-script-reference].

2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le jeu de données (la table est un jeu de données rectangulaire). Vérifiez que le message **TABLE DÉPLOYÉE AVEC SUCCÈS** s’affiche dans la barre de titre.


### Créer la table de sortie

1.	Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données** dans la barre de commandes, puis sur **Stockage d’objets blob Azure**.
2.	Remplacez le code JSON du volet droit par le texte suivant : 

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	Notez les points suivants :
	
	- Le **type** est défini sur **AzureBlob**.
	- Le paramètre **linkedServiceName** est défini sur **StorageLinkedService** (vous avez créé ce service lié à l’étape 2).
	- Le paramètre **folderPath** est défini sur **adftutorial/outfromonpremdf**, où « outfromonpremdf » est le dossier dans le conteneur adftutorial. Vous devez simplement créer le conteneur **adftutorial**.
	- Le paramètre de **disponibilité** est défini sur **Toutes les heures** (l’élément \*\*frequency\*\* est défini sur **hour** et l’élément **intervalle** a la valeur **1**). Le service Data Factory génère une tranche de données de sortie toutes les heures dans la table **emp** de la base de données SQL Microsoft Azure. 

	Si vous ne spécifiez pas de nom (**fileName**) pour une **table d’entrée**, tous les fichiers/objets blob du dossier d’entrée (\*\*folderPath\*\*) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Consultez les exemples de fichiers dans le [didacticiel][adf-tutorial] pour en savoir plus.
 
	Si vous ne spécifiez pas de **fileName** pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

	Pour affecter une valeur aux paramètres **folderPath** et **fileName** de manière dynamique, en fonction de l’heure de **SliceStart**, utilisez la propriété partitionedBy. Dans l’exemple suivant, folderPath utilise les valeurs Year, Month et Day à partir de SliceStart (heure de début de la partie en cours de traitement), alors que fileName utilise la valeur Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2014-10-20T08:00:00, la valeur folderName est wikidatagateway/wikisampledataout/2014/10/20, alors que la valeur de fileName est 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Pour en savoir plus sur les propriétés JSON, voir [Référence de script JSON][json-script-reference].

2.	Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le jeu de données (la table est un jeu de données rectangulaire). Vérifiez que le message **TABLE DÉPLOYÉE AVEC SUCCÈS** s’affiche dans la barre de titre.
  

## Étape 4 : Créer et exécuter un pipeline
Dans cette étape, vous créez un **pipeline** avec une **activité Copier l’activité** qui utilise **EmpOnPremSQLTable** en tant qu’entrée et **OutputBlobTable** en tant que sortie.

1.	Cliquez sur **Nouveau pipeline** dans la barre de commandes. Si le bouton ne s’affiche pas, cliquez sur **... (points de suspension)** pour développer la barre de commandes.
2.	Remplacez le code JSON du volet droit par le texte suivant :   


		{
		  "name": "ADFTutorialPipelineOnPrem",
		  "properties": {
		    "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    "activities": [
		      {
		        "name": "CopyFromSQLtoBlob",
		        "description": "Copy data from on-prem SQL server to blob",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpOnPremSQLTable"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "OutputBlobTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "SqlSource",
		            "sqlReaderQuery": "select * from emp"
		          },
		          "sink": {
		            "type": "BlobSink"
		          }
		        },
		        "Policy": {
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
 
	- Dans la section des activités, toutes les activités ont le **type** **Copy**.
	- L’**entrée** de l’activité est définie sur **EmpOnPremSQLTable** et la **sortie** de l’activité, sur **OutputBlobTable**.
	- Dans la section **Transformation**, le paramètre **SqlSource** est spécifié comme **type de source**, et **BlobSink** comme **type sink**. - La requête SQL **select \* from emp** est spécifiée pour la propriété **sqlReaderQuery** de **SqlSource**.

	Remplacez la valeur de la propriété **start** par le jour actuel et la valeur **end**, par le jour suivant. Les dates/heures de début et de fin doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de fin (**end**) est facultative, mais nous allons l’utiliser dans ce didacticiel.
	
	Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « \*\*start + 48 heures\*\* ». Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** comme valeur pour la propriété **end**.
	
	En spécifiant la période active pour un pipeline, vous définissez la durée pendant laquelle les tranches de données seront traitées, selon les propriétés de **disponibilité** qui ont été définies pour chaque table Azure Data Factory.
	
	Dans l'exemple ci-dessus, 24 tranches de données existent, car une tranche de données est générée toutes les heures.
	
2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le jeu de données (la table est un jeu de données rectangulaire). Vérifiez que le message **PIPELINE DÉPLOYÉ AVEC SUCCÈS** s’affiche dans la barre de titre.
5. Maintenant, fermez le panneau **Éditeur** en cliquant sur **X**. Cliquez de nouveau sur **X** pour fermer le panneau ADFTutorialDataFactory avec la vue d’arborescence et la barre d’outils. Si le message **Vos modifications non enregistrées seront ignorées** s’affiche, cliquez sur **OK**.
6. Vous devez être revenu au panneau **DATA FACTORY** pour **ADFTutorialOnPremDF**.

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, les services liés, les tables et un pipeline et avez planifié le pipeline.

### Affichage d’une vue schématique d’une fabrique de données 
1. Dans la **version préliminaire du portail Azure**, cliquez sur la vignette **Diagramme** sur la page d’accueil de la fabrique de données **ADFTutorialOnPremDF**.

	![Lien Diagramme][image-data-factory-diagram-link]

2. Le diagramme ressemble à ce qui suit :

	![Vue schématique][image-data-factory-diagram-view]

	Vous pouvez faire un zoom avant, un zoom arrière, un zoom à 100 %, un zoom pour ajuster, positionner automatiquement les pipelines et les tables, et afficher les informations de lignage (mise en surbrillance des éléments en amont et en aval des éléments sélectionnés). Vous pouvez double-cliquer sur un objet (table ou pipeline d’entrée/de sortie) pour afficher les propriétés associées.

## Étape 5 : surveiller les jeux de données et les pipelines
Dans cette étape, vous allez utiliser le portail Azure pour surveiller ce qui se passe dans une fabrique de données Azure. Vous pouvez également utiliser les applets de commande PowerShell pour surveiller les jeux de données et les pipelines. Pour en savoir plus sur l’utilisation des applets de commande à des fins de surveillance, consultez la page [Surveillance et gestion d’Azure Data Factory à l’aide de PowerShell][monitor-manage-powershell].

1. Ouvrez la **version préliminaire du portail Azure** (si vous l’avez fermée).
2. Si le panneau **ADFTutorialOnPremDF** n’est pas ouvert, ouvrez-le en cliquant sur **ADFTutorialOnPremDF** dans le **tableau d’accueil**.
3. Vous devez voir le **nombre** et les **noms** des tables et du pipeline que vous avez créés dans ce panneau.

	![Page d’accueil Data Factory][image-data-factory-homepage-2]
4. Cliquez maintenant sur la vignette **Jeux de données**.
5. Dans le panneau **Jeux de données**, cliquez sur **EmpOnPremSQLTable**.

	![Tranches EmpOnPremSQLTable][image-data-factory-onprem-sqltable-slices]

6. Notez que les tranches de données jusqu’à l’heure actuelle ont déjà été produites et ont l’état **Prêt**. Cela est dû au fait que vous avez inséré les données dans la base de données SQL Server et qu’elles y sont tout le temps. Vérifiez qu’aucune tranche n’apparaît dans la section **Tranches problématiques**, sur la partie inférieure de la fenêtre.


	Les listes **Tranches récemment mises à jour** et **Tranches ayant échoué récemment** sont triées en fonction de l’**HEURE DE LA DERNIÈRE MISE À JOUR**. L’heure de mise à jour d’une tranche est modifiée dans les situations suivantes.
    

	-  Vous mettez à jour l’état de la tranche manuellement, par exemple, à l’aide de **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **EXÉCUTER** dans le panneau **TRANCHE** de la tranche.
	-  La tranche change d’état en raison d’une exécution (par exemple, une exécution a démarré, une exécution s’est terminée et a échoué, une exécution s’est terminée et a réussi, etc.).
 
	Cliquez sur le titre des listes ou sur **... (points de suspension)** pour consulter la liste plus complète des tranches. Cliquez sur l’option **Filtre** de la barre d’outils pour filtrer les tranches.
	
	Pour afficher les tranches de données triées en fonction des heures de début et de fin de la tranche, cliquez sur la vignette **Tranches de données (par heure de tranche)**.

7. Dans le panneau **Jeux de données**, cliquez sur **OutputBlobTable**.

	![Tranches OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Vérifiez que les tranches jusqu’à l’heure actuelle sont produites et à l’état **Prêt**. Attendez que les états des tranches jusqu’à l’heure actuelle soient définis sur **Prêt**.
9. Cliquez sur une tranche de données dans la liste pour afficher le panneau **TRANCHE DE DONNÉES**.

	![Panneau Tranche de données][image-data-factory-dataslice-blade]

	Si la tranche n’a pas l’état **Prêt**, vous pouvez afficher les tranches en amont qui ne sont pas prêtes et qui empêchent l’exécution de la tranche actuelle dans la liste **Tranches en amont qui ne sont pas prêtes**.

10. Cliquez sur l’**exécution d’activité** dans la liste de la partie inférieure de la fenêtre pour afficher les **détails sur l’exécution d’activité**.

	![Panneau Détails sur l’exécution d’activité][image-data-factory-activity-run-details]

11. Cliquez sur **X** pour fermer tous les panneaux jusqu’à ce que vous reveniez au panneau d’accueil de l’élément **ADFTutorialOnPremDF**.
14. (facultatif) Cliquez sur **Pipelines**, puis sur **ADFTutorialOnPremDF**, et accédez aux tables d’entrée (\*\*Consommé\*\*) ou aux tables de sortie (\*\*Produit\*\*).
15. Utilisez des outils tels que l’**Explorateur de stockage Azure** pour contrôler la sortie.

	![Azure Storage Explorer][image-data-factory-stroage-explorer]


## Création et enregistrement d’une passerelle à l’aide d’Azure PowerShell 
Cette section décrit comment créer et enregistrer une passerelle à l’aide des cmdlets Azure PowerShell.

1. Lancez **Azure PowerShell** en mode administrateur. 
2. Les applets de commande Azure Data Factory sont disponibles en mode **AzureResourceManager**. Exécutez la commande suivante pour basculer vers le mode **AzureResourceManager**.     

        switch-azuremode AzureResourceManager


2. Utilisez l’applet de commande **New-AzureDataFactoryGateway** pour créer une passerelle logique, comme suit :

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Exemple de commande et de sortie** :


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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


3. Utilisez l’applet de commande **New-AzureDataFactoryGatewayKey** pour générer une clé d’inscription pour la nouvelle passerelle et stockez la clé dans une variable locale **$Key** :

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Exemple de sortie de commande** :


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Dans Azure PowerShell, accédez au dossier suivant : **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScrip\*\* et exécutez le script **RegisterGateway.ps1** associé à la variable locale **$Key**, comme indiqué dans la commande suivante pour enregistrer l’agent client installé sur votre ordinateur avec la passerelle logique que vous avez créée précédemment.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Vous pouvez utiliser l’applet de commande **Get-AzureDataFactoryGateway** pour obtenir la liste des passerelles dans votre fabrique de données. Lorsque l’**état** est **online**, cela signifie que votre passerelle est prête.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Vous pouvez supprimer une passerelle à l’aide de l’applet de commande **Remove-AzureDataFactoryGateway** et mettre à jour la description de la passerelle en utilisant les applets de commande **Set-AzureDataFactoryGateway**. Pour obtenir la syntaxe et d’autres détails sur ces cmdlets, consultez la rubrique Référence des cmdlets Azure Data Factory.



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

<!---HONumber=July15_HO5-->