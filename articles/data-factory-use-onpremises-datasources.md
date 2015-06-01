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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Activation de vos pipelines pour utiliser des données locales

Pour activer vos pipelines dans une fabrique de données Azure pour utiliser une source de données locale, vous devez ajouter la source de données locale comme service lié à la fabrique de données à l'aide du portail de gestion Azure ou d'Azure PowerShell.
 
Pour ajouter une source de données locale comme service lié à une fabrique de données, vous devez d'abord télécharger et installer la passerelle de gestion de données Microsoft sur un ordinateur local et configurer le service lié pour que la source de données locale utilise la passerelle.
 
> [WACOM.NOTE] Actuellement, SQL Server est la seule source de données locale prise en charge.

## <a href="DMG"></a> Passerelle de gestion de données

**La passerelle de gestion de données** est un logiciel qui connecte des sources de données locales à des services cloud de manière gérée et sécurisée. Avec la passerelle de gestion de données, vous pouvez :

- **Vous connecter à des données locales pour accéder aux données hybrides** : vous pouvez connecter des données locales aux services cloud pour bénéficier des services cloud tout en conservant l'exécution des activités sur les données locales ;
- **Définir un proxy de données sécurisées** : vous pouvez définir les sources de données locales qui sont exposées avec la passerelle de gestion de données pour que cette passerelle authentifie la demande de données des services cloud et protège les sources de données locales ;
- **Gérer votre passerelle pour une gouvernance intégrale** : vous disposez de la surveillance et de la journalisation intégrales de toutes les activités dans la passerelle de gestion de données pour la gestion et la gouvernance ;
- **Déplacer les données efficacement** : les données sont transférées en parallèle et résistent aux problèmes intermittents du réseau avec la logique de nouvelle tentative automatique.


La passerelle de gestion de données a une gamme complète de fonctionnalités de connexion de données locales qui incluent les éléments suivants :

- **du pare-feu discret au pare-feu d'entreprise** : la passerelle de gestion de données fonctionne juste après son installation, sans vous obliger à ouvrir une connexion de pare-feu ni à apporter des modifications importantes à votre infrastructure de réseau d'entreprise ;
- **possibilité de chiffrer les informations d'identification avec votre certificat** : les informations d'identification permettent de se connecter aux sources de données chiffrées avec un certificat entièrement détenu par un utilisateur. Sans certificat, personne ne peut déchiffrer les informations d'identification en texte brut, y compris Microsoft.

### Installation de la passerelle - Meilleures pratiques

1.	Si l'ordinateur hôte est en veille prolongée, la passerelle n'est pas en mesure de répondre à la demande de données. Vous devez donc configurer un **plan de gestion de l'alimentation** approprié sur l'ordinateur avant d'installer la passerelle. 
2.	La passerelle de gestion de données doit être en mesure de se connecter aux sources de données locales qui seront enregistrées avec le service Azure Data Factory. Il est inutile qu'elle soit sur le même ordinateur que la source de données, mais **le fait d'être proche de la source de données** réduit le temps nécessaire à la passerelle pour se connecter à la source de données.

### Considérations sur l'utilisation de la passerelle de gestion de données
1.	Une instance unique de la passerelle de gestion de données peut être utilisée pour plusieurs sources de données locales, mais n'oubliez pas qu'une** passerelle est liée à une fabrique de données Azure** et ne peut pas être partagée avec une autre fabrique de données.
2.	Vous pouvez avoir **une seule instance de la passerelle de gestion de données** installée sur votre ordinateur. Si deux fabriques de données doivent accéder aux sources de données locales, vous devez installer des passerelles sur deux ordinateurs locaux où chaque passerelle est liée à une fabrique de données distincte.
3.	Si une passerelle est déjà installée sur votre ordinateur desservant un scénario **Power BI**, installez une **passerelle séparée pour Azure Data Factory** sur un autre ordinateur. 
 

## Procédure pas à pas

Dans cette procédure pas à pas, vous créez une fabrique de données avec un pipeline qui déplace les données d'une base de données SQL Server locale vers un objet blob Azure. 

### Étape 1 : créez une fabrique de données Azure.
Dans cette étape, vous utilisez le portail de gestion Azure pour créer une instance Azure Data Factory nommée **ADFTutorialOnPremDF**. Vous pouvez également créer une fabrique de données à l'aide des cmdlets Azure Data Factory. 

1.	Après la connexion à la[ version préliminaire du portail Azure][
2.	azure-preview-portal], cliquez sur **NOUVEAU** dans l'angle inférieur gauche, puis cliquez sur **Data Factory** sur le panneau **Nouveau**.

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Si **Fabrique de données** ne s'affiche pas dans le panneau **Nouveau**, faites défiler vers le bas.  
6. Dans le panneau **Nouvelle fabrique de données** :
	1. Entrez **ADFTutorialOnPremDF** comme **nom**.
	2. Cliquez sur **NOM DU GROUPE DE RESSOURCES** et sélectionnez **ADFTutorialResourceGroup** (si vous avez effectué le didacticiel de [Prise en main d'Azure Data Factory][adfgetstarted]. Vous pouvez sélectionner un groupe de ressources existant ou en créer un. Pour créer un groupe de ressources :
		1. Cliquez sur **Créer un groupe de ressources**.
		2. Dans le panneau **Créer un groupe de ressources**, entrez un **nom** pour le groupe de ressources et cliquez sur **OK**.

7. Notez que l'option **Ajouter au tableau d'accueil** est activée sur le panneau **Nouvelle fabrique de données**.

	![Add to Startboard][image-data-factory-add-to-startboard]

8. Dans le panneau **Nouvelle fabrique de données**, cliquez sur **Créer**.
9. Recherchez des notifications du processus de création dans le concentrateur **NOTIFICATIONS** sur la gauche.

	![NOTIFICATIONS hub][image-data-factory-notifications-hub]

11. Une fois la création terminée, vous verrez le panneau de fabrique de données comme indiqué ci-dessous :

	![Data Factory Home Page][image-data-factory-datafactory-homepage]

12. Vous devez également le voir dans le **tableau d'accueil** comme illustré ci-dessous. Cliquez dessus pour ouvrir le **panneau de la fabrique de données**, s'il n'est pas déjà ouvert.

	![Startboard][image-data-factory-startboard]

### Étape 2 : créez des services liés 
Dans cette étape, vous allez créer deux services liés : **MyBlobStore** et **OnPremSqlLinkedService**. Le service **OnPremSqlLinkedService** lie une base de données SQL Server locale et le service lié **MyBlobStore** lie un magasin d'objets blob Azure à **ADFTutorialDataFactory**. Plus loin dans cette procédure pas à pas, vous allez créer un pipeline qui copie les données de la base de données SQL Server locale vers le magasin d'objets blob Azure. 

### Ajout d'un service lié à une base de données SQL Server locale
1.	Sur le panneau **Fabrique de données** pour **ADFTutorialOnPremDF**, cliquez sur **Services liés**. 

	![Data Factory Home Page][image-data-factory-home-age]

2.	Sur le panneau **Services liés**, cliquez sur **+ Passerelle de données**.

	![Linked Services - Add a Gateway button][image-data-factory-linkedservices-add-gateway-button]

2. Sur le panneau **Créer**, entrez **adftutorialgateway** comme **nom**, puis cliquez sur **OK**. 	

	![Create Gateway blade][image-data-factory-create-gateway-blade]

3. Dans le panneau **Configurer**, cliquez sur **Installer directement sur cet ordinateur**. Cette opération télécharge le package d'installation de la passerelle, installe, configure et inscrit la passerelle sur l'ordinateur.

	![Gateway - Configure blade][image-data-factory-gateway-configure-blade]

	Il s'agit de la méthode la plus simple (un clic) pour télécharger, installer, configurer et inscrire la passerelle en une seule étape. Vous pouvez voir que l'application **gestionnaire de configuration de la passerelle de gestion de données Microsoft** est installée sur votre ordinateur. Vous trouverez également l'exécutable **ConfigManager.exe** dans le dossier : **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**.

	Vous pouvez également télécharger et installer manuellement la passerelle en utilisant les liens de ce panneau et l'enregistrer à l'aide de la clé indiquée dans la zone de texte **ENREGISTRER AVEC LA CLÉ.**
	
	Pour plus d'informations sur la passerelle, incluant les meilleures pratiques et des remarques importantes, consultez la rubrique [Passerelle de gestion de données](#DMG). 

4. Cliquez sur **OK** dans le panneau **Nouvelle passerelle de données**.
5. Lancez l'application **gestionnaire de configuration de la passerelle de gestion de données Microsoft**   sur votre ordinateur.

	![Gateway Configuration Manager][image-data-factory-gateway-configuration-manager]

6. Attendez que les valeurs soient définies comme suit :
	1. Si l'**état** du Service n'est pas défini sur **Démarré**, cliquez sur **Démarrer le service** pour démarrer le service et attendez une minute que les autres champs soient actualisés.
	2. **Le nom de la passerelle** est défini sur **adftutorialgateway**.
	3. **Le nom de l'instance** est défini sur **adftutorialgateway**.
	4. **L'état de la clé de la passerelle ** est défini sur **Enregistré**.
	5. La barre d'état du bas affiche **Connecté au service de cloud de la passerelle de gestion de données** avec une **coche verte**.  

7. Sur le panneau **Services liés**, vérifiez que l'**état** de la passerelle est **Correct** et cliquez sur **+ Magasin de données**. Vous devrez peut-être fermer et rouvrir le panneau pour l'actualiser. 

	![Add Data Store button][image-data-factory-add-datastore-SQL-button]

8. Dans le panneau **Nouveau magasin de données**, entrez **OnPremSqlLinkedService** comme **nom**.
9. Cliquez sur **TYPE (paramètres requis)** et sélectionnez **SQL Server**. 
10. Dans le panneau **Nouveau magasin de données**, cliquez sur **PASSERELLE DE DONNÉES (configurer les paramètres requis)**.

	![Data Gateway Configure link][image-data-factory-gateway-configure-link]
  
11. Sélectionnez **adftutorialgateway** créé précédemment. 
12.	Dans le panneau **Nouveau magasin de données**, cliquez sur **INFORMATIONS D'IDENTIFICATION** pour afficher le panneau **Informations d'identification**.

	![Data Source Credentials Blade][image-data-factory-credentials-blade]

13.	Dans le panneau **Informations d'identification**, cliquez sur **Cliquez ici pour définir les informations d'identification en toute sécurité**. Cette action lance la boîte de dialogue contextuelle suivante.

	![One Click application install][image-data-factory-oneclick-install]

14. Cliquez sur **Exécuter** pour installer l'application **Gestionnaire des informations d'identification** pour afficher la boîte de dialogue Configuration des informations d'identification. 
15.	Dans la boîte de dialogue **Configuration des informations d'identification**, entrez le**nom de l'utilisateur** et le **mot de passe** que le service peut utiliser pour accéder à la base de données SQL Server locale, et cliquez sur **OK**. Seule l'**authentification SQL** est prise en charge par la boîte de dialogue. Attendez la fermeture de la boîte de dialogue.
16.	Cliquez sur **OK** sur le panneau **Informations d'identification** et cliquez sur **OK** sur le panneau **Nouveau magasin de données**. Vous devez voir le service lié **OnPremSqlLinkedService** que vous avez ajouté sur le panneau Services liés.

	![Linked Services Blade with OnPrem Store][image-data-factory-linkedservices-blade-onprem]

	
   


#### Ajout d'un service lié pour un compte de stockage Azure

1.	Dans le panneau **Fabrique de données**, cliquez sur la vignette **Services liés** pour lancer le panneau **Services liés**.
2. Dans le panneau **Services liés**, cliquez sur **Ajouter un magasin de données**.	
3. Dans le panneau **Nouveau magasin de données** :
	1. Entrez le **nom** du magasin de données. Dans le contexte de ce didacticiel, entrez **MyBlobStore** comme **nom**.
	2. Entrez la **description (facultatif)** pour le magasin de données.
	3. Cliquez sur **Type**.
	4. Sélectionnez le **compte de stockage Azure**, puis cliquez sur **OK**.
		
		![Azure Storage Account][image-data-factory-azure-storage-account]

4. Vous êtes à nouveau dans le panneau **Nouveau magasin de données** qui ressemble à celui-ci :

	![Azure Storage settings][image-data-factory-azure-storage-settings]

5. Entrez le **Nom de compte** et la **Clé de compte** de votre compte Azure Storage, puis cliquez sur **OK**.

  
6. Après avoir cliqué sur **OK** sur le panneau **Nouveau magasin de données**, vous devez voir **MyBlobStore** dans la liste des **MAGASINS DE DONNÉES** sur le panneau **Services liés**. Vérifiez la présence d'éventuels messages dans le concentrateur de **NOTIFICATIONS** (à gauche).

	![Linked Services blade with MyBlobStore][image-data-factory-linkedservices-with-storage]

 
## Étape 3 : Création de jeux de données d'entrée et de sortie
Dans cette étape, vous allez créer des jeux de données d'entrée et de sortie qui représentent les données d'entrée et de sortie pour l'opération de copie (base de données SQL Server locale = > stockage d'objets blob Azure). La création de jeux de données et de pipelines n'est pas encore prise en charge par le portail Azure, vous devrez donc utiliser les cmdlets Azure PowerShell pour créer des tables et des pipelines. Avant de créer des jeux de données ou des tables (jeux de données rectangulaires), vous devez effectuer les opérations suivantes (les étapes sont détaillées après la liste) :

- Créez une table nommée **emp** dans la base de données SQL Server que vous avez ajouté comme service lié à la fabrique de données et insérez deux exemples d'entrées dans la table.
- Si vous n'avez pas suivi le didacticiel de l'article [Prise en main d'Azure Data Factory][adfgetstarted], créez un conteneur d'objets blob nommé **adftutorial** dans le compte de stockage d'objets blob Azure que vous avez ajouté comme service lié à la fabrique de données.

### Préparation du serveur SQL Server local pour le didacticiel

1. Dans la base de données que vous avez spécifiée pour le service lié SQL Server local (**OnPremSqlLinkedService**), utilisez le script SQL suivant pour créer la table **emp** dans la base de données.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. Insérer des exemples dans la table : 


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### Créer une table d'entrée

1.	Créez un fichier JSON pour une table Data Factory qui représente les données de la table **emp** dans la base de données SQL Server. Lancez le **Bloc-notes**, copiez le script JSON suivant et enregistrez-le comme **EmpOnPremSQLTable.json** dans le dossier C:\ADFGetStarted**OnPrem**. Créez le sous-dossier **OnPrem** dans le dossier **C:\ADFGetStarted** s'il n'existe pas. 


        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "OnPremSqlLinkedService"
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

	Notez les points suivants : 
	
	- le **type** de l'emplacement est défini sur **OnPremisesSqlServerTableLocation**.
	- **tableName** est défini sur **emp**.
	- **linkedServiceName** est défini sur **OnPremSqlLinkedService** (vous avez créé ce service lié à l'étape 2).
	- Pour une table d'entrée qui n'est pas générée par un autre pipeline dans Azure Data Factory, vous devez spécifier la section **waitOnExternal** dans le fichier JSON. Cela signifie que les données d'entrée sont produites à l'extérieur du service Azure Data Factory.   

	Pour plus d'informations sur les propriétés JSON, consultez la rubrique [Référence de script JSON][json-script-reference].

2. Les cmdlets Azure Data Factory sont disponibles en mode **AzureResourceManager**. Lancer **Azure PowerShell** et exécutez la commande suivante pour basculer vers le mode **AzureResourceManager**.     

        switch-azuremode AzureResourceManager

	Téléchargez [Azure PowerShell][azure-powershell-install]s'il n'est pas installé sur votre ordinateur.
3. Utilisez la cmdlet **New-AzureDataFactoryTable** pour créer la table d'entrée en utilisant le fichier **EmpOnPremSQLTable.json**.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\EmpOnPremSQLTable.json  
	
	Mettez à jour la commande si vous utilisez un autre groupe de ressources.

### Créer la table de sortie

1.	Créez un fichier JSON pour une table Data Factory à utiliser comme sortie pour le pipeline que vous allez créer à l'étape suivante. Lancez le Bloc-notes, copiez le script JSON suivant et enregistrez-le sous **OutputBlobTable.json** dans le dossier **C:\ADFGetStarted\OnPrem**.

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
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Notez les points suivants : 
	
	- Le **type** d'emplacement a la valeur **AzureBlobLocation**.
	- **linkedServiceName** est défini sur **MyBlobStore** (vous avez créé ce service lié à l'étape 2).
	- **folderPath** est défini sur **adftutorial/outfromonpremdf** où outfromonpremdf est le dossier dans le conteneur adftutorial. Vous devez simplement créer le conteneur **adftutorial**.
	- La **disponibilité** est définie **à l'heure** (**frequency** est défini sur **hour** et **interval** est défini sur **1**).  Le service Data Factory génère une tranche de données de sortie par heure dans la table **emp** de la base de données Azure SQL. 

	Si vous ne spécifiez pas de **fileName** pour une **table d'entrée**, tous les fichiers/objets blob du répertoire d'entrée (**folderPath**) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Pour en savoir plus, consultez les fichiers d'exemple du [didacticiel][adf-tutorial].
 
	Si vous ne spécifiez pas de **fileName** pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Pour affecter une valeur à **folderPath** et **fileName** de manière dynamique en fonction de l'heure de **SliceStart**, utilisez la propriété partitionedBy. Dans l'exemple suivant, folderPath utilise l'année, le mois et le jour à partir de SliceStart (heure de début de la tranche en cours de traitement) et fileName utilise Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2014-10-20T08:00:00, la valeur folderName est définie sur wikidatagateway/wikisampledataout/2014/10/20, tandis que la valeur de fileName est définie sur 08.csv. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Pour plus d'informations sur les propriétés JSON, consultez la rubrique [Référence de script JSON][json-script-reference].

2.	Dans **Azure PowerShell**, exécutez la commande suivante pour créer une autre table Data Factory représentant la table Data Factory dans la base de données SQL Azure.

		New-AzureDataFactoryTable -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\OutputBlobTable.json -ResourceGroupName ADFTutorialResourceGroup  

## Étape 4 : créer et exécuter un pipeline
Dans cette étape, vous créez un **pipeline** avec une activité **Copier l'activité** qui utilise **EmpOnPremSQLTable** comme entrée et **OutputBlobTable** comme sortie.

1.	Créez un fichier JSON pour le pipeline. Lancez le Bloc-notes, copiez le script JSON suivant et enregistrez-le sous **ADFTutorialPipelineOnPrem.json** dans le dossier **C:\ADFGetStarted\OnPrem**.
	 

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
	        	]
			}
		}

	Notez les points suivants :
 
	- Dans la section des activités, toutes les activités ont le **type** **CopyActivity**.
	- **L'entrée** de l'activité est définie sur **EmpOnPremSQLTable** et la **sortie** de l'activité est définie sur **OutputBlobTable**.
	- Dans la section **transformation**, **SqlSource** est spécifié comme **le type de source** et **BlobSink** est spécifié comme **le type sink**.
	- La requêter SQL **select * from emp** est spécifiée pour la propriété **sqlReaderQuery** de **SqlSource**.

2. Utilisez la cmdlet **New-AzureDataFactoryPipeline** pour créer un pipeline à l'aide du fichier **ADFTutorialPipeline.json** que vous avez créé.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\ADFTutorialPipelineOnPrem.json -ResourceGroupName ADFTutorialResourceGroup  

3. Une fois que le pipeline est créé, vous pouvez spécifier la durée du traitement des données. En spécifiant la période active pour un pipeline, vous définissez la durée de traitement des tranches de données basée sur les propriétés **de disponibilité** qui ont été définies pour chaque table Azure Data Factory.  Exécutez la commande PowerShell suivante pour définir la période active sur le pipeline et entrez Y pour confirmer. 


		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineOnPrem  

	> [WACOM.NOTE] Remplacez la valeur **StartDateTime** par le jour en cours et la valeur **EndDateTime** par le jour suivant. Les valeurs StartDateTime et EndDateTime sont exprimées en heure UTC et doivent être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. La valeur EndDateTime est facultative, mais nous allons l'utiliser dans ce didacticiel.
	> Si vous ne spécifiez pas la valeur **EndDateTime**, elle est calculée comme suit : **StartDateTime + 48 heures**. Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** comme **EndDateTime**.

	Dans l'exemple ci-dessus, 24 tranches de données existent, car une tranche de données est générée toutes les heures.
4. Dans la **version préliminaire du portail Azure**, cliquez sur la vignette **Diagramme** sur la page d'accueil de la fabrique de données **ADFTutorialOnPremDF**. :

	![Diagram Link][image-data-factory-diagram-link]

5. Le diagramme ressemble à ce qui suit :

	![Diagram View][image-data-factory-diagram-view]

	**Félicitations !**Vous avez correctement créé une fabrique de données Azure, des services liés, des tables et un 	 pipeline et démarré le flux de travail.

## Étape 5 : surveiller les jeux de données et les pipelines
Dans cette étape, vous allez utiliser le portail Azure pour surveiller ce qui se passe dans une fabrique de données Azure. Vous pouvez également utiliser les cmdlets PowerShell pour surveiller les jeux de données et les pipelines. Pour plus d'informations sur l'utilisation des cmdlets pour la surveillance, consultez la section [Surveillance et gestion d'Azure Data Factory à l'aide de PowerShell][monitor-manage-powershell].

1. Ouvrez la **version préliminaire du portail Azure** (si vous l'avez fermée)
2. Si le panneau **ADFTutorialOnPremDF** n'est pas ouvert, ouvrez-le en cliquant sur **ADFTutorialOnPremDF** sur le **tableau d'accueil**.
3. Vous devez voir le **nombre** et les **noms** des tables et du pipeline que vous avez créés sur ce panneau.

	![Data Factory Home Page][image-data-factory-homepage-2]
4. Cliquez maintenant sur la vignette **Jeux de données**.
5. Sur le panneau **Jeux de données**, cliquez sur **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable slices][image-data-factory-onprem-sqltable-slices]

6. Notez que les tranches de données jusqu'à l'heure actuelle ont déjà été produites et sont **prêtes**. Cela est dû au fait que vous avez inséré les données dans la base de données SQL Server et qu'elles y sont tout le temps. Vérifiez qu'aucune tranche n'apparaît dans la section **Tranches problématiques** en bas.
7. Sur le panneau **Jeux de données**, cliquez sur **OutputBlobTable**.

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. Vérifiez que les tranches jusqu'à l'heure actuelle sont produites et à l'état **Prêt**.
9. Cliquez sur une tranche de données dans la liste pour voir le panneau **TRANCHE DE DONNÉES**.

	![Data Slice Blade][image-data-factory-dataslice-blade]
10. Cliquez sur **l'exécution d'activité** dans la liste en bas pour afficher le **détail sur l'exécution d'activité**.

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. Cliquez sur **X** pour fermer tous les panneaux jusqu'à ce que vous reveniez au panneau d'accueil de **ADFTutorialOnPremDF**.
14. (facultatif) Cliquez sur **Pipelines**, puis sur **ADFTutorialOnPremDF**, et accédez aux tables d'entrée (**Consommé**) ou aux tables de sortie (**Produit**).
15. Utilisez des outils tels que l'**Explorateur de stockage Azure** pour contrôler la sortie.

	![Azure Storage Explorer][image-data-factory-storage-explorer]


## Création et enregistrement d'une passerelle à l'aide des cmdlets Azure PowerShell
Cette section décrit comment créer et enregistrer une passerelle à l'aide des cmdlets Azure PowerShell. 

1. Lancez **Azure PowerShell** en mode administrateur. 
2. Les cmdlets Azure Data Factory sont disponibles en mode **AzureResourceManager**. Exécutez la commande suivante pour basculer vers le mode **AzureResourceManager**.     

        switch-azuremode AzureResourceManager


2. Utilisez la cmdlet **New-AzureDataFactoryGateway** pour créer une passerelle logique comme suit :

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description <desc>

	**Exemple de commande et de sortie** :


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description "gateway for walkthrough"

		Name            : MyGateway
		Location        : West US
		Description     : gateway for walkthrough
		Version         :
		Status          : NeedRegistration
		VersionStatus   : None
		CreateTime      : 9/28/2014 10:58:22
		RegisterTime    :
		LastConnectTime :
		ExpiryTime      :


3. Utilisez la cmdlet **New-AzureDataFactoryGatewayKey** pour générer une clé d'enregistrement pour la nouvelle passerelle et stockez la clé dans une variable locale **$Key** :

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName $df 

	
	**Exemple de sortie de commande :**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Dans Azure PowerShell, basculez vers le dossier : **C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript** et exécutez le script **RegisterGateway.ps1** associé à la variable locale **$Key** comme indiqué dans la commande suivante pour enregistrer l'agent client installé sur votre ordinateur avec la passerelle logique que vous avez créée précédemment.

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Vous pouvez utiliser la cmdlet **Get-AzureDataFactoryGateway** pour obtenir la liste des passerelles dans votre fabrique de données. Lorsque l'**état** est **online**, votre passerelle est prête.

		Get-AzureDataFactoryGateway -DataFactoryName $df -ResourceGroupName ADF

Vous pouvez supprimer une passerelle à l'aide de la cmdlet **Remove-AzureDataFactoryGateway** et mettre à jour la description pour la passerelle en utilisant les cmdlets **Set-AzureDataFactoryGateway**. Pour obtenir la syntaxe et d'autres détails sur ces cmdlets, consultez la rubrique Référence des cmdlets Azure Data Factory.  




## Voir aussi

Article | Description
------ | ---------------
[Prise en main d'Azure Data Factory][adf-getstarted] | Cet article propose un didacticiel de bout en bout qui permet de créer un exemple de fabrique de données Azure qui copie les données d'un objet blob Azure vers une base de données Azure SQL.
[Utilisation de Pig et Hive avec Data Factory][use-pig-and-hive-with-data-factory] | Cet article contient une procédure pas à pas permettant d'exécuter un script Hive/Pig à l'aide de HDInsight Activity pour traiter les données d'entrée afin de produire des données de sortie. 
[Didacticiel : Déplacement et traitement des fichiers journaux à l'aide de Data Factory][adf-tutorial] | Cet article contient une procédure pas à pas permettant d'implémenter un scénario proche du monde réel à l'aide d'Azure Data Factory pour transformer les données des fichiers journaux en données détaillées.
[Utilisation des activités personnalisées de Data Factory][use-custom-activities] | Cet article contient une procédure pas à pas permettant de créer une activité personnalisée et de l'utiliser dans un pipeline. 
[Résolution des problèmes liés à Data Factory][troubleshoot] | Cet article explique comment résoudre des problèmes liés à Azure Data Factory.  
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence du développeur comportent des informations complètes sur les cmdlets, le script JSON, les fonctions, etc. 



[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[adf-getstarted]: ../data-factory-get-started
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: ../data-factory-get-started
[monitor-manage-powershell]: ../data-factory-monitor-manage-using-powershell





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/


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

[image-data-factory-azure-storage-account]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageAccount.png

[image-data-factory-azure-storage-settings]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageSettings.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-with-storage]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesWithMyBlobStore.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-add-datastore-SQL-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddSQLDataStoreButton.png

[image-data-factory-gateway-configure-link]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreDataGatewayConfigureLink.png

[image-data-factory-credentials-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCredentionlsBlade.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-linkedservices-blade-onprem]: ./media/data-factory-use-onpremises-datasources/LinkedServiceBladeWithOnPremSql.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!--HONumber=46--> 
