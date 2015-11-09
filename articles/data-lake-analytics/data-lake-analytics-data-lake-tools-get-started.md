<properties 
   pageTitle="Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio | Azure" 
   description="Découvrez comment installer Data Lake Tools pour Visual Studio, développer et tester des scripts U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="jgao"/>

# Didacticiel : Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Découvrez comment installer Data Lake Tools pour Visual Studio et utiliser Data Lake Tools pour Visual Studio pour écrire et tester des scripts U-SQL.

U-SQL est un langage hyper évolutif et hautement extensible conçu pour préparer, transformer et analyser toutes les données dans le lac de données et au-delà. Pour plus d'informations, consultez [Référence U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).


**Configuration requise**

- **Visual Studio 2015, Visual Studio 2013 mise à jour 4 ou Visual Studio 2012 avec Visual C++ installé** 
- **Kit de développement logiciel (SDK) Microsoft Azure pour .NET version 2.5 ou ultérieure**. Installez-le avec [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Data Lake Tools pour Visual Studio](http://aka.ms/adltoolsvs)**. 

    Une fois les Data Lake Tools pour Visual Studio installés, vous verrez un menu Data Lake dans Visual Studio :
    
    ![Menu U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Consultez les deux sections suivantes dans [Prise en main d'Azure Data Lake Analytics à l'aide du portail Azure en version préliminaire](data-lake-analytics-get-started-portal.md)**.

	- [Créer un compte Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).
	- [Télécharger SearchLog.tsv sur le compte Data Lake Storage par défaut](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

	Data Lake Tools ne prend pas en charge la création de comptes Data Lake Analytics. Par conséquent, vous devez le créer avec le portail Azure en version préliminaire, Azure PowerShell, le Kit de développement logiciel (SDK) .NET ou Azure CLI. Pour lancer une tâche Data Lake Analytics, vous aurez besoin de données. Bien que Data Lake Tools prenne en charge le téléchargement de données, vous allez utiliser le portail pour télécharger les exemples de données, ce qui facilitera la progression dans ce didacticiel.

## Connexion à Azure

**Se connecter à Data Lake Analytics**

1. Ouvrez Visual Studio.
2. Dans le menu **U-SQL**, cliquez sur **Options et paramètres**.
4. Cliquez sur **Connexion** ou sur **Changer d'utilisateur** si un autre utilisateur est connecté et suivez les instructions de connexion.
5. Cliquez sur **OK** pour fermer la boîte de dialogue Options et paramètres.

**Pour parcourir vos comptes Data Lake Analytics**

1. Dans Visual Studio, ouvrez l'**Explorateur de serveurs** en appuyant sur **CTRL+ALT+S**.
2. Dans l'**Explorateur de serveurs**, développez **Azure**, puis **Data Lake Analytics**. Le cas échéant, la liste de vos comptes Data Lake Analytics s'affiche. Vous ne pouvez pas créer de comptes Data Lake Analytics à partir de Visual Studio. Pour créer un compte, consultez [Prise en main d'Azure Data Lake Analytics avec le portail Azure en version préliminaire](data-lake-analytics-get-started-portal.md) ou [Prise en main d'Azure Data Lake Analytics avec Azure PowerShell](knoa-get-started-powershell.md).

## Téléchargement des fichiers de données source

Vous avez téléchargé des données dans la section **Configuration requise** précédemment dans le didacticiel.

Si vous voulez utiliser vos propres données, voici les procédures de téléchargement des données à partir de Data Lake Tools.

**Pour télécharger le fichier dans le compte Azure Data Lake correspondant**

1. À partir de l'**Explorateur de serveurs**, développez successivement **Azure**, **Data Lake Analytics**, votre compte Data Lake Analytics, **Comptes de stockage**. Le compte de stockage Data Lake par défaut doit s'afficher, ainsi que les comptes de stockage Data Lake liés et les comptes Azure Storage liés. Le compte Data Lake par défaut a une étiquette « Compte de stockage par défaut ».
2. Cliquez avec le bouton droit sur le compte de stockage Data Lake par défaut, puis cliquez sur **Explorer**. Le volet de l'Explorateur Data Lake Tools pour Visual Studio s'ouvre. Une arborescence est affichée à gauche, l'affichage du contenu se trouve à droite.
3. Accédez au dossier où vous souhaitez télécharger des fichiers, 
4. Cliquez avec le bouton droit sur n'importe quel espace vide, puis cliquez sur **Télécharger**. 

	![U-SQL Visual Studio projet U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Pour télécharger le fichier vers un compte de stockage d'objets Blob Azure**

1. À partir de l'**Explorateur de serveurs**, développez successivement **Azure**, **Data Lake Analytics**, votre compte Data Lake Analytics, **Comptes de stockage**. Le compte de stockage Data Lake par défaut doit s'afficher, ainsi que les comptes de stockage Data Lake liés et les comptes Azure Storage liés. 
2. Développez le compte Azure Storage.
3. Cliquez avec le bouton droit sur le conteneur dans lequel vous souhaitez télécharger des fichiers, puis cliquez sur **Explorer**.
4. Accédez au dossier où vous souhaitez télécharger des fichiers, 
5. Cliquez avec le bouton droit sur n'importe quel espace vide, puis cliquez sur **Télécharger**. 

## Développement et test de scripts U-SQL 

Les travaux Data Lake Analytics sont écrits en langage U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main langage U-SQL](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Pour créer et soumettre une tâche Data Lake Analytics**

1. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
2. Sélectionnez le type **Projet U-SQL**.

	![nouveau projet U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Cliquez sur **OK**. Visual Studio crée une solution avec un fichier **Script.usql**.
4. Insérez le script suivant dans le fichier **Script.usql** :

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Ce script U-SQL lit le fichier de données source avec **Extractors.Tsv()**, puis crée un fichier csv avec **Outputters.Csv()**.
    
    Ne modifiez pas les deux chemins d'accès, sauf si vous avez copié le fichier source dans un autre emplacement. Data Lake Analytics créera le dossier de sortie s'il n'existe pas encore.
	
	Il est plus simple d'utiliser des chemins d'accès relatifs pour les fichiers stockés dans les comptes Data Lake par défaut. Vous pouvez également utiliser des chemins d'accès absolus. Par exemple :
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Vous devez utiliser des chemins d'accès absolus pour accéder aux fichiers dans les comptes de stockage liés. La syntaxe des fichiers stockés dans des comptes Azure Storage liés est la suivante :
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Les conteneurs d'objets Blob Azure avec des autorisations d'accès aux objets Blob publics ou aux conteneurs publics ne sont pas pris en charge actuellement.

	Notez les caractéristiques suivantes :

	- **IntelliSense**
	 
		Nom renseigné automatiquement et les membres seront affichés pour Ensemble de lignes, Classes, Bases de données, Schémas et Objets définis par l'utilisateur (UDO).
		 
		IntelliSense pour les entités de catalogue (Bases de données, Schémas, Tables, UDO, etc.) est lié à votre compte de calcul. Vous pouvez vérifier le compte de calcul, la base de données et le schéma actifs actuels dans la barre d'outils supérieure et les faire commuter via les listes déroulantes.
 
	- **Mise en forme automatique**
	
		Les utilisateurs peuvent modifier la mise en retrait du script Scope basé sur la structure de code sous Modifier->Avancé :
  
		- Mettre le document en forme (Ctrl+E, D) : met en forme l'ensemble du document   
		- Mettre la sélection en forme (Ctrl+K, Ctrl+F) : met en forme la sélection. Si aucune sélection n'a été effectuée, ce raccourci met en forme la ligne où se trouve le curseur.  
		
		Toutes les règles de mise en forme peuvent être configurées sous Outils->Options->Éditeur de texte->SIP->Mise en forme.  
	- **Mise en retrait intelligente**
	 
		Data Lake Tools pour Visual Studio peut mettre en retrait automatiquement les expressions lorsque vous écrivez des scripts. Cette fonctionnalité est désactivée par défaut. Les utilisateurs doivent l'activer sous U-SQL->Options et paramètres->Commutateurs->Activer la mise en retrait intelligente.

	- **Accès à la définition et recherche de toutes les références**
	
		Cliquez avec le bouton droit sur le nom d'un ensemble de lignes/paramètre/d'une colonne/d'un UDO, etc. et cliquez sur Atteindre la définition (F12) pour accéder à sa définition. Cliquez sur Rechercher toutes les références (Maj+F12) pour afficher toutes les références.

	- **Insertion du chemin d'accès Azure**
		
		Plutôt que de mémoriser le chemin d'accès du fichier Azure et de le taper manuellement lors de l'écriture du script, Data Lake Tools pour Visual Studio offre une méthode facile : cliquez avec le bouton droit dans l'éditeur, cliquez sur Insérer le chemin d'accès Azure. Accédez au fichier dans la boîte de dialogue de l'Explorateur d'objets Blob Azure. Cliquez sur le bouton OK pour insérer le chemin d'accès dans votre code.

5. Spécifiez le compte Data Lake Analytics, la base de données et le schéma :

	![Soumettre un projet U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Pour plus d'informations, consultez [Utilisation du catalogue U-SQL](data-lake-analytics-use-u-sql-catalog.md).

5. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Générer le script**. Vérifiez le résultat dans le volet Sortie.
6. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Soumettre le script**. Si vous le souhaitez, vous pouvez également cliquer sur **Soumettre** dans le volet Script.usql. Consultez la capture d’écran précédente. Cliquez sur la flèche vers le bas en regard du bouton Soumettre pour envoyer à l'aide des options avancées :
7. Indiquez le **Nom de la tâche**, vérifiez le **Compte Analytics**, puis cliquez sur **Soumettre**. Les résultats de la soumission et le lien vers la tâche sont disponibles dans la fenêtre Résultats de Data Lake Tools pour Visual Studio à l'issue de la soumission.

	![Soumettre un projet U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)

8. Vous devez cliquer sur le bouton Actualiser pour afficher le dernier état du travail et actualiser l'écran. Si le travail réussit, vous verrez le **Graphique** associé, les **Opérations sur les métadonnées**, l'**Historique de l'état**, les **Diagnostics** :

	![Graphique des performances du travail U-SQL Visual Studio Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

	* Résumé du travail. Affiche les informations de résumé du travail en cours, par exemple : état, progression, durée d'exécution, nom de l'exécution, demandeur, etc.   
	* Détails du travail. Informations détaillées sur ce travail, y compris le script, les ressources, la vue d'exécution du vertex.
	* Graphique du travail. Quatre graphiques permettent de visualiser les informations du travail : progression, données lues, données écrites, durée d'exécution, durée d'exécution moyenne par nœud, débit d'entrée, débit de sortie.
	* Opérations sur les métadonnées. Affiche toutes les opérations sur les métadonnées.
	* Historique de l'état. 
	* Diagnostics. Data Lake Tools pour Visual Studio diagnostique automatiquement l'exécution du travail. Vous recevrez des alertes lorsqu'il y a des erreurs ou des problèmes de performances dans les travaux. Consultez la section Diagnostics de travaux (lien TBD) pour plus d'informations. 
	
**Pour vérifier l'état du travail**

1. À partir de l'Explorateur de serveurs, développez successivement **Azure**, **Data Lake Analytics**, le nom du compte Data Lake Analytics.
2. Double-cliquez sur **Travaux** pour répertorier les travaux.
2. Cliquez sur un travail pour en afficher l'état.

**Voir le résultat de la tâche**

1. Dans l'**Explorateur de serveurs**, développez **Azure**, **Data Lake Analytics**, puis votre compte Data Lake Analytics et **Comptes de stockage**. Cliquez avec le bouton droit sur le compte de stockage Data Lake par défaut, puis sur **Explorateur**. 
2.  Double-cliquez sur **sortie** pour ouvrir le dossier
3.  Double-cliquez sur **SearchLog-From-adltools.csv**.


###Lecture de travail 

La lecture de travail vous permet de surveiller la progression de l'exécution du travail et de détecter visuellement les anomalies de performance et les goulots d'étranglement. Cette fonctionnalité peut être utilisée avant la fin de l'exécution du travail (c'est-à-dire, pendant que le travail est en cours d'exécution), ainsi que lorsque l'exécution est terminée. Exécuter la lecture pendant l'exécution du travail permet à l'utilisateur de lire la progression jusqu'à l'heure actuelle.

**Pour afficher la progression de l'exécution du travail**

1. Cliquez sur **Charger le profil** dans le coin supérieur droit. Reportez-vous à la capture d'écran précédente.
2. Cliquez sur le bouton Lecture dans le coin inférieur gauche pour passer en revue la progression de l'exécution du travail. 
3. Lors de la lecture, cliquez sur **Pause** pour l'arrêter ou faites glisser directement la barre de progression sur des positions spécifiques. 


###Carte thermique 

Data Lake Tools pour Visual Studio fournit des superpositions en couleurs sélectionnables par l'utilisateur dans la vue du travail pour indiquer la progression, les E/S de données, la durée d'exécution, le débit E/S de chaque étape. Grâce à cela, les utilisateurs peuvent déterminer les problèmes potentiels et la distribution des propriétés du travail directement et de façon intuitive. Vous pouvez choisir une source de données à afficher dans la liste déroulante.



##Voir aussi

Pour commencer à utiliser Data Lake Analytics à l'aide de différents outils, consultez :

- [Prise en main de Data Lake Analytics à l'aide du portail Azure en version préliminaire](data-lake-analytics-get-started-portal.md)
- [Prise en main de Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Prise en main de Data Lake Analytics à l'aide du Kit de développement logiciel (SDK) .NET](data-lake-analytics-get-started-net-sdk.md)

Pour afficher les autres rubriques sur le développement :

- [Analyser les journaux web à l'aide de Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)
- [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
- [Développer des opérateurs définis par l'utilisateur U-SQL pour des travaux Data Lake Analytics](data-lake-analytics-u-sql-user-defined-operators.md)

<!---HONumber=Nov15_HO1-->