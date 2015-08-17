<properties 
	pageTitle="Déplacement de données entre des emplacements locaux et le cloud à l'aide d’Azure Data Factory" 
	description="Découvrez comment déplacer des données entre des emplacements locaux et le cloud à l'aide de la passerelle de gestion des données et d’Azure Data Factory." 
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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données
Un des défis de l’intégration de données modernes consiste à déplacer en toute transparence des données vers et depuis un site local et le cloud. Data Factory facilite cette intégration grâce à une passerelle de gestion des données. La passerelle de gestion Data Factory est un agent que vous pouvez installer en local pour créer des pipelines hybrides.

Cet article fournit une vue d'ensemble de l'intégration de magasins de données locaux dans des magasins de données sur le cloud et le traitement sur le cloud à l’aide de Data Factory. Cet article s'appuie sur l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) et d’autres articles sur les principaux concepts Data Factory. La vue d’ensemble suivante suppose que vous maîtrisez les concepts Data Factory tels que les pipelines, les activités, les jeux de données et l'activité de copie.

La passerelle de données offre les fonctionnalités suivantes :

1.	Modélisation des sources de données locales et des sources de données sur le cloud au sein de la même fabrique de données, et déplacement des données.
2.	Un point unique de surveillance et de gestion avec une visibilité du statut de la passerelle et un tableau de bord sur un cloud de fabrique de données.
3.	Gestion sécurisée de l’accès aux sources de données locales.
	1. Aucune modification du pare-feu d’entreprise n’est requise. La passerelle établit uniquement des connexions HTTP sortantes pour l’accès à Internet.
	2. Chiffrement des informations d'identification pour vos magasins de données locaux à l’aide de votre certificat.
4.	Déplacer les données efficacement : les données sont transférées en parallèle et résistent aux problèmes intermittents du réseau, grâce à la logique de nouvelle tentative automatique.

## Considérations sur l’utilisation de la passerelle de gestion de données
1.	Une instance unique de la passerelle de gestion de données peut être utilisée pour plusieurs sources de données locales, mais notez qu’une **seule instance de passerelle est liée à une seule fabrique de données Microsoft Azure** et ne peut pas être partagée avec une autre fabrique de données.
2.	Vous ne pouvez installer qu’**une seule instance de la passerelle de gestion de données** sur un même ordinateur. Si deux fabriques de données doivent accéder aux sources de données locales, vous devez installer des passerelles sur deux ordinateurs locaux où chaque passerelle est liée à une fabrique de données distincte.
3.	Il n’est pas nécessaire que la **passerelle soit sur le même ordinateur que la source de données**, mais le fait qu’elle soit proche de cette dernière réduit le temps nécessaire à la passerelle pour se connecter à la source de données. Nous vous recommandons d’installer la passerelle sur un ordinateur différent de celui qui héberge la source de données locale, afin que la passerelle ne soit pas en concurrence avec la source de données pour l’octroi des ressources.
4.	Vous pouvez avoir **plusieurs passerelles sur différents ordinateurs connectés à la même source de données locale**. Par exemple, vous pouvez avoir deux passerelles desservant deux fabriques de données, alors que la même source de données locale est enregistrée auprès des deux fabriques de données.
5.	Si une passerelle est déjà installée sur votre ordinateur desservant un scénario **Power BI**, installez une **passerelle distincte pour Azure Data Factory** sur un autre ordinateur.
6.	Vous devez **utiliser la passerelle même lorsque vous utilisez ExpressRoute**. 
7.	Vous devez considérer votre source de données comme une source de données locale (qui se trouve derrière un pare-feu) même lorsque vous utilisez **ExpressRoute** et **utilisez la passerelle** pour établir la connectivité entre le service et la source de données. 

## Installation de la passerelle : configuration requise
1.	Les versions de **système d’exploitation** prises en charge sont les suivantes : Windows 7, Windows 8/8.1, Windows Server 2008 R2 et Windows Server 2012.
2.	La **configuration** recommandée pour l’ordinateur de passerelle est la suivante : au moins 2 GHz, 4 cœurs, 8 Go de RAM et 80 Go d’espace disque.
3.	Si l’ordinateur hôte est en veille prolongée, la passerelle n’est pas en mesure de répondre à la demande de données. Vous devez donc configurer un **plan de gestion de l’alimentation** approprié sur l’ordinateur avant d’installer la passerelle. L’installation de la passerelle ouvre une invite si l’ordinateur est configuré pour la mise en veille prolongée.

Etant donné que l’activité de copie s'exécute selon une fréquence spécifique, l'utilisation des ressources (processeur, mémoire) sur l'ordinateur suit également le même modèle avec des pics et des baisses d'inactivité. L'utilisation des ressources dépend également en grande partie de la quantité de données déplacées. Lorsque plusieurs tâches sont en cours, vous constaterez une augmentation des ressources utilisées pendant les heures de pointe. L’exemple ci-dessus représente la configuration minimale, et il est toujours préférable d'avoir une configuration avec plus de ressources que cette configuration minimale en fonction de votre charge spécifique pour le déplacement des données.

## Installation
La passerelle de gestion des données peut être installée en téléchargeant un package d'installation MSI à partir du Centre de téléchargement Microsoft. Le fichier MSI peut également servir à mettre à niveau la passerelle de gestion des données existante vers la version la plus récente, en conservant tous les paramètres. Vous trouverez le lien vers le package MSI sur le portail Azure en suivant la procédure étape par étape ci-dessous.

### Meilleures pratiques d’installation :
1.	Définissez un plan d'alimentation sur l'ordinateur hôte de la passerelle afin d’empêcher la mise en veille prolongée. Si l’ordinateur hôte est en veille prolongée, la passerelle n’est pas en mesure de répondre à la demande de données.
2.	Vous devriez sauvegarder le certificat associé à la passerelle.

### Dépannage de l'installation :
Si votre entreprise utilise un pare-feu ou un serveur proxy, des étapes supplémentaires peuvent être requises si la passerelle de gestion des données est incapable de se connecter aux services de cloud Microsoft.

#### Analyse des journaux de la passerelle à l’aide de l'Observateur d'événements :

Le gestionnaire de configuration de la passerelle affiche l'état de passerelle : « Déconnecté » ou « En cours de connexion ».

Pour plus d'informations, vous pouvez consulter les journaux de la passerelle dans les journaux des événements Windows. Vous les trouverez à l'aide de **l'Observateur d'événements** Windows sous **Journaux des applications et des services** > **Passerelle de gestion des données**. Lors de la résolution des problèmes liés à la passerelle, recherchez des événements de type erreur dans l’Observateur d’événements.


#### Symptômes possibles des erreurs liées au pare-feu :

1. Lorsque vous tentez d’inscrire la passerelle, vous recevez le message d’erreur suivant : « Nous n’avons pas pu enregistrer la clé de passerelle. Avant de réessayer d’enregistrer la clé de passerelle, vérifiez que la passerelle de gestion des données est connectée et que le service d’hébergement de la passerelle de gestion des données est en cours d’exécution. »
2. Lorsque vous ouvrez le Gestionnaire de configuration, l’état indiqué est « Déconnecté » ou « En cours de connexion ». Lors de l’affichage de journaux d’événements Windows, sous « Observateur d’événements » > « Journaux d’applications et de services » > « Passerelle de gestion des données », des messages d’erreur apparaissent, tels que « Impossible de se connecter au serveur distant » ou « Un composant de la passerelle de gestion des données ne répond plus et va redémarrer automatiquement. Nom du composant : Passerelle. »

Ces messages sont dus à une configuration incorrecte du pare-feu ou du serveur de proxy, qui empêche la passerelle de gestion des données de se connecter aux services cloud en vue de son authentification.

Les deux pare-feu concernés peuvent être : les pare-feu d'entreprise en cours d'exécution sur le routeur central de l'entreprise et le pare-feu Windows configuré en tant que démon sur l'ordinateur local où la passerelle est installée. Voici quelques considérations :

- Il est inutile de modifier la stratégie entrante du pare-feu d'entreprise.
- Le pare-feu d'entreprise et le pare-feu Windows doivent activer la règle de trafic sortant pour les ports TCP 80, 440 et de 9305 à 9354. Ces ports sont utilisés par Microsoft Azure Service Bus pour établir la connexion entre les services de cloud et la passerelle de gestion des données.

Le programme d'installation MSI configurera automatiquement les règles de pare-feu Windows pour les ports entrants de l'ordinateur de la passerelle (voir la section consacrée aux ports et à la sécurité ci-dessus).

Mais le programme d'installation suppose que les ports de sortie mentionnés ci-dessus sont activés par défaut sur l'ordinateur local et le pare-feu d'entreprise. Vous devez activer ces ports sortants si ce n'est déjà fait. Si vous avez remplacé le pare-feu Windows par un pare-feu tiers, ces ports peuvent nécessiter une ouverture manuelle.

Si votre entreprise utilise un serveur proxy, vous devez ajouter Microsoft Azure à la liste blanche. Vous pouvez télécharger une liste des adresses IP Microsoft Azure valides à partir du [Centre de téléchargement Microsoft](http://msdn.microsoft.com/library/windowsazure/dn175718.aspx).

## Utilisation de la passerelle de données – Procédure pas à pas
Dans cette procédure pas à pas, vous créez une fabrique de données avec un pipeline qui déplace les données d’une base de données SQL Server locale vers un objet blob Azure.

### Étape 1 : Création d’une fabrique de données Microsoft Azure
Dans cette étape, vous utilisez le portail de gestion Microsoft Azure pour créer une instance Azure Data Factory nommée **ADFTutorialOnPremDF**. Vous pouvez également créer une fabrique de données à l’aide des cmdlets Azure Data Factory.

1.	Une fois connecté au [portail Azure en version préliminaire](https://portal.azure.com), cliquez dans le coin inférieur gauche sur **NOUVEAU**, sélectionnez **Analyse de données** dans le panneau **Créer**, puis cliquez sur **Data Factory** dans le panneau **Analyse de données**.

	![Nouveau -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. Dans le panneau **Nouvelle fabrique de données** :
	1. Saisissez **ADFTutorialOnPremDF** dans le champ **Nom**.
	2. Cliquez sur **RESOURCE GROUP NAME** et sélectionnez **ADFTutorialResourceGroup**. Vous pouvez sélectionner un groupe de ressources existant ou en créer un. Pour créer un groupe de ressources :
		1. Cliquez sur **Créer un groupe de ressources**.
		2. Dans le panneau **Créer un groupe de ressources**, saisissez un **nom** pour le groupe de ressources et cliquez sur **OK**.

7. Notez que l’option **Ajouter au tableau d’accueil** est activée sur le panneau **Nouvelle fabrique de données**.

	![Ajouter au Tableau d’accueil](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. Dans le panneau **Nouvelle fabrique de données**, cliquez sur **Créer**.

	Le nom de la fabrique de données Azure doit être un nom global unique. Si l'erreur suivante s'affiche : **Le nom de la fabrique de données « ADFTutorialOnPremDF » n'est pas disponible**, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialOnPremDF), puis essayez de la recréer. Utilisez ce nom à la place d'ADFTutorialOnPremDF quand vous effectuez les étapes restantes de ce didacticiel.

9. Recherchez des notifications du processus de création dans le concentrateur **NOTIFICATIONS** sur la gauche. Cliquez sur **X** pour fermer le panneau **NOTIFICATIONS** si celui-ci est ouvert.

	![Concentrateur NOTIFICATIONS](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. Une fois la création terminée, vous verrez apparaître le panneau **Data Factory** comme indiqué ci-dessous :

	![Page d’accueil Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### Étape 2 : Créer un portail de gestion des données
5.	Dans le panneau **Data Factory** de **ADFTutorialOnPremDF**, cliquez sur **Services liés**. 

	![Page d’accueil Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/DataFactoryHomePage.png)

2.	Dans le panneau **Services liés**, cliquez sur **+ Passerelle de données**.

	![Services liés - Ajouter un bouton Passerelle](./media/data-factory-move-data-between-onprem-and-cloud/OnPremLinkedServicesAddGaewayButton.png)

2. Dans le panneau **Créer**, saisissez **adftutorialgateway** dans le champ **Nom**, puis cliquez sur **OK**.

	![Panneau Créer une passerelle](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. Dans le panneau **Configurer**, cliquez sur **Installer directement sur cet ordinateur**. Cette opération télécharge le package d’installation de la passerelle, installe, configure et inscrit la passerelle sur l’ordinateur.

	> [AZURE.NOTE]Utilisez Internet Explorer ou un navigateur web compatible Microsoft ClickOnce.

	![Passerelle - Panneau Configurer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	Il s’agit de la méthode la plus simple (un clic) pour télécharger, installer, configurer et inscrire la passerelle en une seule étape. Vous pouvez voir que l’application **Gestionnaire de configuration de la passerelle de gestion de données Microsoft** est installée sur votre ordinateur. Vous pouvez également trouver l’exécutable **ConfigManager.exe** dans le dossier suivant : **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.

	Vous pouvez également télécharger et installer manuellement la passerelle en utilisant les liens de ce panneau et l’enregistrer à l’aide de la clé indiquée dans la zone de texte **ENREGISTRER AVEC LA CLÉ**.
	
	Pour en savoir plus sur la passerelle, y compris les meilleures pratiques et des remarques importantes, consultez la section [Passerelle de gestion des données](#DMG).

	>[AZURE.NOTE]Vous devez être administrateur sur l’ordinateur local pour pouvoir installer et configurer la passerelle de gestion des données avec succès. Vous pouvez ajouter des utilisateurs supplémentaires au groupe Windows local d’utilisateurs de la passerelle de gestion des données. Les membres de ce groupe sont en mesure d’utiliser l’outil Gestionnaire de configuration de la passerelle de gestion de données pour configurer la passerelle.

4. Cliquez sur le concentrateur **NOTIFICATIONS**, sur la gauche. Patientez jusqu’à ce que le message **L’installation rapide de « adftutorialgateway » a réussi** s’affiche dans le panneau **Notifications**.

	![Réussite de l’installation rapide](./media/data-factory-move-data-between-onprem-and-cloud/express-setup-succeeded.png)
6. Cliquez sur **OK** dans le panneau **Créer**, puis dans le panneau **Nouvelle passerelle de données**.
6. Fermez le panneau **Services liés** (en appuyant sur le bouton **X** dans l’angle supérieur droit de la fenêtre) et rouvrez le panneau **Services liés** pour afficher le dernier état de la passerelle. 
7. Vérifiez que l’**état** de la passerelle est **En ligne**. 

	![État de la passerelle](./media/data-factory-move-data-between-onprem-and-cloud/gateway-status.png)

5. Lancez l’application **Gestionnaire de configuration de la passerelle de gestion de données Microsoft** sur votre ordinateur.

	![Gestionnaire de configuration de la passerelle](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. Attendez que les valeurs soient définies comme suit :
	1. Si l’**état** du service n’est pas défini sur **Démarré**, cliquez sur **Démarrer le service** pour démarrer le service et attendez une minute que les autres champs soient actualisés.
	2. Le **nom de la passerelle** est défini sur **adftutorialgateway**.
	3. Le **nom de l’instance** est défini sur **adftutorialgateway**.
	4. L’**état de la clé de la passerelle** est défini sur **Enregistré**.
	5. La barre d’état située au bas de l’écran affiche le message **Connecté au service de cloud de la passerelle de gestion de données** accompagné d’une **coche verte**.
	
7. Basculez vers **Certificats**. Le certificat spécifié dans cet onglet sert à chiffrer/déchiffrer les informations d'identification pour le magasin de données local que vous spécifiez dans le portail. Cliquez sur **Change** pour utiliser votre propre certificat à la place. Par défaut, la passerelle utilise le certificat généré automatiquement par le service Data Factory.

	![Configuration de certificat de la passerelle](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

8. Dans le panneau **Services liés** du portail, vérifiez que l’**état** de la passerelle est bien défini sur **Correct**.


### Étape 2 : Créer des services liés 
Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **SqlServerLinkedService**. Le service **SqlServerLinkedService** lie une base de données SQL Server locale et le service lié **StorageLinkedService** lie un magasin d’objets blob Microsoft Azure à **ADFTutorialDataFactory**. Plus loin dans cette procédure pas à pas, vous allez créer un pipeline qui copie les données de la base de données SQL Server locale vers le magasin d’objets blob Azure.

#### Ajout d’un service lié à une base de données SQL Server locale
1.	Dans le panneau **Services liés**, cliquez sur **Nouveau magasin de données** dans la barre de commandes.
2.	Entrez le **nom** **SqlServerLinkedService**. 
2.	Cliquez sur la flèche en regard du **type**, puis sélectionnez **SQL Server**.

	![Créer un magasin de données](./media/data-factory-move-data-between-onprem-and-cloud/new-data-store.png)
3.	D’autres paramètres devraient apparaître sous **Type**.
4.	Pour le paramètre **Passerelle de données**, sélectionnez la passerelle que vous venez de créer. 

	![Paramètres de SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-settings.png)
4.	Entrez le nom de votre serveur de base de données pour le paramètre **Serveur**.
5.	Entrez le nom de la base de données pour le paramètre **Base de données**.
6.	Cliquez sur la flèche en regard de **Informations d'identification**.

	![Panneau Informations d’identification](./media/data-factory-move-data-between-onprem-and-cloud/credentials-dialog.png)
7.	Dans le panneau **Informations d’identification**, cliquez sur **Cliquez ici pour définir les informations d’identification**.
8.	Dans la boîte de dialogue **Configuration des informations d’identification**, procédez comme suit :

	![Boîte de dialogue des paramètres d’informations d'identification](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png) 1. Sélectionnez l’**authentification** que le service de Data Factory utilisera pour se connecter à la base de données. 2. Entrez le nom de l'utilisateur ayant accès à la base de données dans le paramètre **USERNAME**. 3. Entrez le mot de passe de l’utilisateur dans le paramètre **PASSWORD**. 4. Cliquez sur **OK** pour fermer la boîte de dialogue. 
4. Cliquez sur **OK** pour fermer le panneau **Informations d'identification**. 
5. Cliquez sur **OK** dans le panneau **Nouveau magasin de données**. 	
6. Vérifiez que l'état de **SqlServerLinkedService** est défini sur En ligne dans le panneau Services liés.![État du service SQL Server lié](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-linked-service-status.png)

Si vous accédez au portail à partir d’un ordinateur différent de l’ordinateur de passerelle, vous devrez peut-être vous assurer que l’application Gestionnaire d’informations d’identification peut se connecter à l’ordinateur de passerelle. Sinon, vous ne pourrez pas définir les informations d’identification de la source de données, ni tester la connexion à la source de données.

##### Configuration des informations d'identification et de la sécurité
Lorsque vous utilisez la méthode décrite ci-dessus avec l’application « Configuration des informations d'identification » lancée à partir du portail Azure pour définir les informations d'identification d’une source de données locale, le portail chiffre les informations d'identification avec le certificat que vous avez spécifié dans l'onglet Certificat du gestionnaire de configuration de la passerelle de gestion des données sur l'ordinateur de passerelle.

Si vous recherchez une approche basée sur une API pour chiffrer les informations d'identification, vous pouvez utiliser l’applet de commande PowerShell [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/azure/dn834940.aspx) pour chiffrer les informations d'identification. L'applet de commande utilise le certificat qui a servi à configurer la passerelle pour chiffrer les informations d'identification. Les informations d'identification chiffrées retournées par cette applet de commande peuvent être ajoutées à l'élément EncryptedCredential de connectionString dans le fichier JSON que vous utiliserez avec l’applet de commande [New-AzureDataFactoryLinkedService](https://msdn.microsoft.com/library/azure/dn820246.aspx) ou dans l'extrait de code JSON dans Data Factory Editor sur le portail.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

**Remarque :** si vous utilisez l'application « Configuration des informations d'identification », celle-ci définit automatiquement les informations d'identification chiffrées dans le service lié, comme indiqué ci-dessus.

Il existe une autre approche pour définir les informations d'identification à l'aide de Data Factory Editor. Si vous créez un service SQL Server lié à l'aide de l'éditeur et entrez les informations d'identification en texte brut, ces informations d'identification sont chiffrées à l'aide d'un certificat appartenant au service Data Factory et NON le certificat qui a servi à configurer la passerelle. Bien que cette approche puisse être un peu plus rapide dans certains cas, elle reste moins sécurisée. Par conséquent, nous vous recommandons de suivre cette approche uniquement à des fins de développement/test.

#### Ajout d’un service lié pour un compte de stockage Azure
 
1. Dans le panneau **Services liés**, cliquez sur **Nouveau magasin de données** dans la barre d’outils. 
2. Entrez **StorageLinkedService** dans le champ **Nom**. 
3. Cliquez sur la flèche en regard du **type**, puis sélectionnez **Azure Storage**.
4. De nouveaux champs devraient apparaître : **Nom du compte** et **Clé du compte**, sous le paramètre **Type**. 
3. Entrez le nom de votre compte de stockage Azure dans le champ **Nom du compte**.
4. Entrez la clé de votre compte de stockage Azure dans le champ **Clé du compte**. 
5. Cliquez sur **OK** pour fermer la boîte de dialogue. 

 
### Étape 3 : Créer des jeux de données d’entrée et de sortie
Dans cette étape, vous allez créer des jeux de données d’entrée et de sortie qui représentent les données d’entrée et de sortie pour l’opération de copie (base de données SQL Server locale = > stockage d’objets blob Azure). Avant de créer des jeux de données ou des tables (jeux de données rectangulaires), vous devez effectuer les opérations suivantes (les étapes sont détaillées après la liste) :

- Créez une table nommée **emp** dans la base de données SQL Server que vous avez ajoutée en tant que service lié à la fabrique de données et insérez quelques exemples d’entrées dans la table.
- Créez un conteneur d'objets blob nommé **adftutorial** dans le compte de stockage Azure blob que vous avez ajouté comme service lié à la fabrique de données.

### Préparation du serveur SQL Server local pour le didacticiel

1. Dans la base de données que vous avez spécifiée pour le service lié SQL Server local (**SqlServerLinkedService**), utilisez le script SQL suivant pour créer la table **emp** dans la base de données.


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

1.	Dans le panneau **DATA FACTORY**, cliquez sur la vignette **Créer et déployer** pour lancer l’**éditeur** de la fabrique de données.

	![Vignette Créer et déployer](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
1. Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données** dans la barre de commande, puis sur **SQL local**. 
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
	- Pour une table d’entrée qui n’est pas générée par un autre pipeline dans Azure Data Factory, vous devez définir **external** sur **true**. Cela signifie que les données d’entrée sont produites à l’extérieur du service Azure Data Factory. Vous pouvez éventuellement spécifier des stratégies de données externes à l'aide de l’élément **externalData** dans la section **Stratégie**.    

	Pour plus d'informations sur les propriétés JSON, consultez la rubrique [Référence de script JSON][json-script-reference].

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
	- **availability** est défini sur **hourly** (**frequency** a la valeur **hour** et **interval** à la valeur **1**). Le service Data Factory génère une tranche de données de sortie toutes les heures dans la table **emp** de la base de données SQL Microsoft Azure. 

	Si vous ne spécifiez pas de nom (**fileName**) pour une **table d'entrée**, tous les fichiers/objets blob du dossier d'entrée (**folderPath**) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Pour en savoir plus, consultez les fichiers d'exemple du [didacticiel][adf-tutorial].
 
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

 

	Pour plus d'informations sur les propriétés JSON, consultez la rubrique [Référence de script JSON][json-script-reference].

2.	Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le jeu de données (la table est un jeu de données rectangulaire). Vérifiez que le message **TABLE DÉPLOYÉE AVEC SUCCÈS** s’affiche dans la barre de titre.
  

### Étape 4 : Créer et exécuter un pipeline
Dans cette étape, vous créez un **pipeline** avec une **activité Copier l’activité** qui utilise **EmpOnPremSQLTable** en tant qu’entrée et **OutputBlobTable** en tant que sortie.

1.	Dans le panneau **DATA FACTORY**, cliquez sur la vignette **Créer et déployer** pour lancer l’**éditeur** de la fabrique de données.

	![Vignette Créer et déployer](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
2.	Cliquez sur **Nouveau pipeline** dans la barre de commandes. Si le bouton ne s’affiche pas, cliquez sur **... (points de suspension)** pour développer la barre de commandes.
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
	- Dans la section **Transformation**, le paramètre **SqlSource** est spécifié comme **type de source**, et **BlobSink** comme **type sink**.
- La requête SQL **select * from emp** est spécifiée pour la propriété **sqlReaderQuery** de **SqlSource**.

	Remplacez la valeur de la propriété **start** par le jour actuel et la valeur **end**, par le jour suivant. Les dates/heures de début et de fin doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de fin (**end**) est facultative, mais nous allons l’utiliser dans ce didacticiel.
	
	Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « **start + 48 heures** ». Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** comme valeur pour la propriété **end**.
	
	En spécifiant la période active pour un pipeline, vous définissez la durée pendant laquelle les tranches de données seront traitées, selon les propriétés de **disponibilité** qui ont été définies pour chaque table Azure Data Factory.
	
	Dans l'exemple ci-dessus, 24 tranches de données existent, car une tranche de données est générée toutes les heures.
	
2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le jeu de données (la table est un jeu de données rectangulaire). Vérifiez que le message **PIPELINE DÉPLOYÉ AVEC SUCCÈS** s’affiche dans la barre de titre.
5. Maintenant, fermez le panneau **Éditeur** en cliquant sur **X**. Cliquez de nouveau sur **X** pour fermer le panneau ADFTutorialDataFactory avec la vue d’arborescence et la barre d’outils. Si le message **Vos modifications non enregistrées seront ignorées** s’affiche, cliquez sur **OK**.
6. Vous devez être revenu au panneau **DATA FACTORY** pour **ADFTutorialOnPremDF**.

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, les services liés, les tables et un pipeline et avez planifié le pipeline.

#### Affichage d’une vue schématique d’une fabrique de données 
1. Dans la **version préliminaire du portail Azure**, cliquez sur la vignette **Diagramme** sur la page d’accueil de la fabrique de données **ADFTutorialOnPremDF**.

	![Lien Diagramme](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Le diagramme ressemble à ce qui suit :

	![Vue schématique](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	Vous pouvez faire un zoom avant, un zoom arrière, un zoom à 100 %, un zoom pour ajuster, positionner automatiquement les pipelines et les tables, et afficher les informations de lignage (mise en surbrillance des éléments en amont et en aval des éléments sélectionnés). Vous pouvez double-cliquer sur un objet (table ou pipeline d’entrée/de sortie) pour afficher les propriétés associées.

### Étape 5 : surveiller les jeux de données et les pipelines
Dans cette étape, vous allez utiliser le portail Azure pour surveiller ce qui se passe dans une fabrique de données Azure. Vous pouvez également utiliser les applets de commande PowerShell pour surveiller les jeux de données et les pipelines. Pour plus de détails sur la surveillance, voir [Surveillance et gestion des pipelines](monitor-manage-pipelines.md).

1. Ouvrez la **version préliminaire du portail Azure** (si vous l’avez fermée).
2. Si le panneau **ADFTutorialOnPremDF** n’est pas ouvert, ouvrez-le en cliquant sur **ADFTutorialOnPremDF** dans le **tableau d’accueil**.
3. Vous devez voir le **nombre** et les **noms** des tables et du pipeline que vous avez créés dans ce panneau.

	![Page d’accueil Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. Cliquez maintenant sur la vignette **Jeux de données**.
5. Dans le panneau **Jeux de données**, cliquez sur **EmpOnPremSQLTable**.

	![Tranches EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

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

	![Panneau Tranche de données](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	Si la tranche n’a pas l’état **Prêt**, vous pouvez afficher les tranches en amont qui ne sont pas prêtes et qui empêchent l’exécution de la tranche actuelle dans la liste **Tranches en amont qui ne sont pas prêtes**.

10. Cliquez sur l’**exécution d’activité** dans la liste de la partie inférieure de la fenêtre pour afficher les **détails sur l’exécution d’activité**.

	![Panneau Détails sur l’exécution d’activité][image-data-factory-activity-run-details]

11. Cliquez sur **X** pour fermer tous les panneaux jusqu'à ce que vous
12. reveniez au panneau d’accueil de l’élément **ADFTutorialOnPremDF**.
14. (facultatif) Cliquez sur **Pipelines**, puis sur **ADFTutorialOnPremDF**, et accédez aux tables d’entrée (**Consommé**) ou aux tables de sortie (**Produit**).
15. Utilisez des outils tels que l’**Explorateur de stockage Azure** pour contrôler la sortie.

	![Explorateur du stockage Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)


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

	
4. Dans Azure PowerShell, accédez au dossier suivant : C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScrip** et exécutez le script **RegisterGateway.ps1** associé à la variable locale **$Key**, comme indiqué dans la commande suivante pour enregistrer l’agent client installé sur votre ordinateur avec la passerelle logique que vous avez créée précédemment.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Vous pouvez utiliser l’applet de commande **Get-AzureDataFactoryGateway** pour obtenir la liste des passerelles dans votre fabrique de données. Lorsque l’**état** est **online**, cela signifie que votre passerelle est prête.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Vous pouvez supprimer une passerelle à l’aide de l’applet de commande **Remove-AzureDataFactoryGateway** et mettre à jour la description de la passerelle en utilisant les applets de commande **Set-AzureDataFactoryGateway**. Pour obtenir la syntaxe et d’autres détails sur ces cmdlets, consultez la rubrique Référence des cmdlets Azure Data Factory.


## Flux de données pour la copie à l’aide de la passerelle de gestion s données
Lorsque vous utilisez une activité de copie dans un pipeline de données pour recevoir des données locales vers le cloud en vue d’un traitement ultérieur, ou exportez les données de résultat du cloud vers un magasin de données local, l'activité de copie utilise en interne une passerelle pour transférer les données de la source de données locale vers le cloud et vice versa.

Voici un flux de données de haut niveau et un résumé des étapes pour la copie à l’aide de la passerelle de données : ![Flux de données à l'aide de la passerelle](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	Le développeur des données crée une passerelle pour une fabrique de données Azure à l'aide du [portail Azure](http://portal.azure.com)ou d’une [applet de commande PowerShell](https://msdn.microsoft.com/library/dn820234.aspx). 
2.	Le développeur des données utilise le panneau « Services liés » afin de définir un nouveau service lié pour un magasin de données local à l’aide de la passerelle. Dans le cadre de la configuration du service lié, le développeur des données utilise l'application de configuration des informations d’identification comme indiqué dans la procédure étape par étape pour spécifier les types d'authentification et les informations d’identification. La boîte de dialogue de l’application de configuration des informations d'identification communiquera avec le magasin de données pour tester la connexion et la passerelle afin d’enregistrer les informations d'identification.
3.	La passerelle chiffrera les informations d'identification avec le certificat associé à la passerelle (fourni par le développeur des données) avant d'enregistrer les informations d'identification dans le cloud.
4.	Le service de déplacement de Data Factory communique avec la passerelle pour la planification et la gestion des tâches via un canal de contrôle qui utilise une file d'attente de bus de service Azure partagé. Lorsque la tâche de l’activité de copie doit être lancée, Data Factory place en file d'attente la requête ainsi que les informations d'identification. La passerelle lance la tâche après avoir interrogé la file d'attente.
5.	La passerelle déchiffre les informations d'identification avec le même certificat puis se connecte au magasin de données local avec le type d'authentification approprié.
6.	La passerelle copie les données du magasin local vers un stockage cloud, ou d'un stockage cloud vers un magasin de données local selon la configuration de l'activité de copie dans le pipeline de données. Remarque : pour cette étape, la passerelle communique directement avec le service de stockage basé sur le cloud (par exemple, Azure Blob, SQL Azure) via un canal sécurisé (HTTPS).

### Considérations liées aux ports et à la sécurité

1. Comme mentionné ci-dessus dans la procédure étape par étape, il existe plusieurs façons de configurer les informations d’identification de magasins de données locaux à l’aide de Data Factory. Les considérations liées aux ports varient selon ces options.	

	- À l'aide de l’application **Configuration des informations d'identification** : le programme d'installation de la passerelle de gestion des données ouvre par défaut ouvre les ports **8050** et **8051** sur le pare-feu Windows pour l'ordinateur de passerelle. Ces ports sont utilisés par l'application Configuration des informations d’identification pour transmettre ces informations d'identification à la passerelle. Ces ports sont ouverts uniquement pour l'ordinateur sur le pare-feu Windows local. Ils ne sont pas accessibles depuis Internet et il n’est pas nécessaire de les ouvrir dans le pare-feu d’entreprise.
	2.	Utilisation de l’applet de commande PowerShell [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) : a. Si vous utilisez une commande PowerShell pour chiffrer les informations d'identification et par conséquent ne souhaitez pas que l'installation de la passerelle ouvre les ports entrants sur l'ordinateur de passerelle dans le pare-feu Windows, vous pouvez le faire en utilisant la commande suivante lors de l’installation :
	
			msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
3.	Si vous utilisez l’application **Configuration des informations d’identification**, vous devez la lancer sur un ordinateur en mesure de se connecter à la passerelle de gestion des données pour pouvoir définir les informations d’identification de la source de données et tester la connexion à cette dernière.
4.	Lors de la copie des données depuis/vers une base de données SQL Server locale vers/à partir d’une base de données SQL Azure, vérifiez les points suivants :	
	- 	Le pare-feu sur l’ordinateur de passerelle autorise les communications TCP sortantes sur le port **TCP** **1433**.
	- 	Configurez les [paramètres de pare-feu SQL Azure](https://msdn.microsoft.com/library/azure/jj553530.aspx) pour ajouter l’**adresse IP de l’ordinateur de passerelle** aux **adresses IP autorisées**.
5.	Lors de la copie des données depuis ou vers le serveur SQL Server local vers une destination, si la passerelle et les ordinateurs SQL Server sont différents, procédez comme suit : [configurez le pare-feu Windows](https://msdn.microsoft.com/library/ms175043.aspx) sur l’ordinateur SQL Server, afin que la passerelle puisse accéder à la base de données via les ports qu’écoute l’instance de SQL Server. Pour l’instance par défaut, il s’agit du port 1433.

<!---HONumber=August15_HO6-->