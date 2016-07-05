<properties 
	pageTitle="Didacticiel : Créer un pipeline à l’aide de l’Assistant de copie" 
	description="Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie en vous servant de Data Factory Editor pris en charge par le portail Azure." 
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
	ms.topic="get-started-article" 
	ms.date="05/16/2016" 
	ms.author="spelluru"/>

# Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy
> [AZURE.SELECTOR]
- [Vue d’ensemble du didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Utilisation de Data Factory Editor](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Utiliser PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Utilisation de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Utilisation de l’Assistant de copie](data-factory-copy-data-wizard-tutorial.md)

Dans ce didacticiel, vous allez utiliser l’Assistant Data Factory Copy pour créer un pipeline avec l’activité de copie dans la fabrique de données. Tout d’abord, vous allez créer une fabrique de données à l’aide du portail Azure, puis utiliser l’Assistant de copie pour créer des services, des ensembles de données et un pipeline avec une activité de copie pour copier les données à partir d’un stockage blob Azure vers une base de données Azure SQL.

> [AZURE.IMPORTANT] Passez en revue l’article [Vue d’ensemble du didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) et effectuez les étapes préalables avant de suivre ce didacticiel.

## Créer une fabrique de données
Dans cette étape, vous utilisez le portail Azure pour créer une fabrique de données Azure nommée **ADFTutorialDataFactory**.

1.	Une fois connecté au [portail Azure](https://portal.azure.com), cliquez dans le coin inférieur gauche sur **+NOUVEAU**, sélectionnez **Analyse de données** dans le panneau **Créer**, puis cliquez sur **Fabrique de données** dans le panneau **Analyse de données**.

	![Nouveau -> DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)

6. Dans le panneau **Nouvelle fabrique de données** :
	1. Entrez **ADFTutorialDataFactory** comme **nom**.
	
  		![Panneau Nouvelle fabrique de données](./media/data-factory-copy-data-wizard-tutorial/getstarted-new-data-factory.png)
	2. Cliquez sur **NOM DU GROUPE DE RESSOURCES** et procédez comme suit :
		1. Cliquez sur **Créer un groupe de ressources**.
		2. Dans le panneau **Créer un groupe de ressources**, entrez **ADFTutorialResourceGroup** comme **nom** du groupe de ressources, puis cliquez sur **OK**.

			![Créer un groupe de ressources](./media/data-factory-copy-data-wizard-tutorial/create-new-resource-group.png)

		Certaines étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé **ADFTutorialResourceGroup**. Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../resource-group-overview.md).
7. Dans le panneau **Nouvelle fabrique de données**, notez que l'option **Ajouter au tableau d'accueil** est sélectionnée.
8. Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.

	Le nom de la fabrique de données Azure doit être un nom global unique. Si l'erreur suivante s'affiche, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory), puis tentez de la recréer : **Le nom de la fabrique de données « ADFTutorialDataFactory » n'est pas disponible**. Consultez la rubrique [Data Factory - Règles d'affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
	 
	![Nom de la fabrique de données indisponible](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)
	
	> [AZURE.NOTE] Le nom de la fabrique de données pourra être enregistré en tant que nom DNS et devenir ainsi visible publiquement.

9. Cliquez sur le hub **NOTIFICATIONS** situé à gauche et recherchez les notifications relatives au processus de création. Cliquez sur **X** pour fermer le panneau **NOTIFICATIONS** si celui-ci est ouvert.
10. Une fois la création terminée, le panneau **FABRIQUE DE DONNÉES** apparaît de la manière suivante :

    ![Page d'accueil Data Factory](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## Création d’un pipeline

1. Dans la page d’accueil Data Factory, cliquez sur la vignette **Copier les données** pour lancer l’**Assistant de copie**.

	> [AZURE.NOTE] Si vous voyez que le navigateur web est bloqué au niveau « Autorisation... », désactivez/décochez l’option **Bloquer les cookies et les données de site tiers** (ou) laissez cette option activée et créez une exception pour **login.microsoftonline.com**, puis réessayez de lancer l’Assistant.
2. Sur la page **Propriétés** :
	1. Saisissez **CopyFromBlobToAzureSql** comme **Nom de la tâche**
	2. Saisissez une **Description** (facultative).
	3. Notez la **Date et heure de début** et la **Date et heure de fin**. Définissez la **Date et heure de fin** sur le jour suivant la **Date et heure de début**.
	3. Cliquez sur **Next**.

	![Outil de copie - page Propriétés](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png)
3. Sur la page **Magasin de données source**, cliquez sur la vignette **Stockage d’objets blob Azure**. Cette page sert à spécifier le magasin de données source pour la tâche de copie. Vous pouvez utiliser un service lié de magasin de données existant (ou) spécifier un nouveau magasin de données. Pour utiliser un service lié existant, vous devez cliquer sur **À PARTIR DES SERVICES LIÉS EXISTANTS** et sélectionner le service lié à droite.

	![Outil de copie - page Source data store (Magasin de données source)](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
5. Sur la page **Spécifier le compte de stockage d’objets blob Azure** :
	1. Saisissez **AzureStorageLinkedService** comme **nom du service lié**.
	2. Confirmez l’option **À partir des abonnements** comme **Méthode de sélection de compte**.
	3. Sélectionnez un **compte de stockage Azure** dans la liste des comptes de stockage Azure disponibles dans l’abonnement sélectionné. Vous pouvez également choisir de saisir manuellement les paramètres de compte de stockage en sélectionnant l’option **Saisir manuellement** comme **Méthode de sélection de compte**. Cliquez ensuite sur **Suivant**.

	![Outil de copie - spécifiez le compte de stockage d’objets blob Azure](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
6. Sur la page **Choisir le fichier ou le dossier d’entrée** :
	1. Accédez au dossier **adftutorial**.
	2. Sélectionnez **emp.txt**, puis cliquez sur **Choisir**
	3. Cliquez sur **Next**.

	![Outil de copie - choisissez le fichier ou le dossier d’entrée](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
7. Sur la page **Paramètres de format de fichier**, sélectionnez les valeurs **par défaut**, puis cliquez sur **Suivant**.

	![Outil de copie - Paramètres de format de fichier](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)
8. Sur la page du magasin de destination, cliquez sur la vignette **Base de données SQL Azure**, puis cliquez sur **Suivant**.
9. Sur la page **Spécifier la base de données SQL Azure** :
	1. Saisissez **AzureSqlLinkedService** dans le champ **Nom du service lié**.
	2. Vérifiez que la **méthode de sélection du serveur/de la base de données** est définie sur l’option **À partir des abonnements Azure**.
	3. Sélectionnez **Nom du serveur** et **Base de données**.
	4. Saisissez le **Nom d’utilisateur** et le **Mot de passe**.
	5. Cliquez sur **Next**.
9. Sur la page **Mappage de table**, sélectionnez **emp** comme champ **Destination** dans la liste déroulante, puis cliquez sur **Flèche vers le bas** (facultatif) pour afficher le schéma et afficher un aperçu des données.

	![Outil de copie - Mappage de Table](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png)
10. Sur la page **Mappage de schéma** cliquez sur **Suivant**.
11. Passez en revue les informations contenues dans la page **Résumé**, puis cliquez sur **Terminer**. Cela crée deux services liés, deux jeux de données (entrée et sortie) et un pipeline dans la fabrique de données (à partir de l’endroit où vous avez lancé l’Assistant de copie).
12. Sur la page **Déploiement réussi**, cliquez sur **Cliquer ici pour surveiller le pipeline de copie**.

	![Outil de copie - Déploiement réussi](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)
13. Suivez les instructions de la section [Surveillance et gestion du pipeline à l’aide de l’application de surveillance](data-factory-monitor-manage-app.md) pour en savoir plus sur la façon de surveiller le pipeline que vous venez de créer.

	![Application de surveillance](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)
 

## Voir aussi
| Rubrique | Description |
| :---- | :---- |
| [Activités de déplacement des données](data-factory-data-movement-activities.md) | Cet article fournit une description détaillée de l’activité de copie que vous avez utilisée dans ce didacticiel. |
| [Planification et exécution](data-factory-scheduling-and-execution.md) | Cet article explique les aspects de la planification et de l’exécution du modèle d’application Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory et comment les utiliser pour créer des flux de travail pilotés par les données de bout en bout pour votre scénario ou votre entreprise. |
| [Groupes de données](data-factory-create-datasets.md) | Cet article va vous aider à comprendre les jeux de données dans Azure Data Factory.
| [Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle application de surveillance et gestion.](data-factory-monitor-manage-app.md) | Cet article décrit comment surveiller, gérer et déboguer les pipelines à l’aide de l’application de surveillance et gestion. 

<!---HONumber=AcomDC_0629_2016-->