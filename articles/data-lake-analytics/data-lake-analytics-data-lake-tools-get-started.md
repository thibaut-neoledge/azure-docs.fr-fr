---
title: "Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio | Microsoft Docs"
description: "Découvrez comment installer Data Lake Tools Visual Studio et développer et tester des scripts U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: edmaca, yanacai
ms.translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: c26ac89bd7ef494331ba309aacf87de03506ac4c
ms.contentlocale: fr-fr
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-develop-u-sql-scripts-using-data-lake-tools-for-visual-studio"></a>Didacticiel : Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Écrivez et testez des scripts U-SQL avec Data Lake Tools pour Visual Studio.

U-SQL est un langage hyper évolutif et hautement extensible conçu pour préparer, transformer et analyser toutes les données dans le lac de données et au-delà. Pour plus d'informations, consultez [Référence U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

## <a name="prerequisites"></a>Composants requis
* **Visual Studio 2017 (sous la charge de travail de stockage et de traitement de données), Visual Studio 2015 Update 3, Visual Studio 2013 Update 4 ou Visual Studio 2012. Les éditions Enterprise (Ultimate/Premium), Professional et Community sont prises en charge ; l’édition Express n’est pas prise en charge.**
* **Kit de développement logiciel (SDK) Microsoft Azure pour .NET version 2.7.1 ou ultérieure**.  Installez-le avec [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* **[Data Lake Tools pour Visual Studio](http://aka.ms/adltoolsvs)**.

    Une fois les outils Data Lake pour Visual Studio installés, vous verrez un nœud « Data Lake Analytics » dans l’Explorateur de serveurs sous le nœud « Azure » (ouvrez l’Explorateur de serveurs en appuyant sur Ctrl+Alt+S).

* **Compte Data Lake Analytics et exemples de données** Data Lake Tools ne prend pas en charge la création de comptes Data Lake Analytics. Créez un compte avec le Portail Azure, Azure PowerShell, le Kit de développement logiciel (SDK) .NET ou l’interface de ligne de commande Azure.
Pour vous faciliter la tâche, un exemple de script PowerShell pour la création d’un service Data Lake Analytics et le chargement du fichier de données source vers le serveur est fourni dans [Annexe A - Exemple PowerShell pour la préparation du didacticiel](data-lake-analytics-data-lake-tools-get-started.md#appx-a-powershell-sample-for-preparing-the-tutorial).

    En option, vous pouvez parcourir les deux sections ci-après de l’article [Prise en main d’Azure Data Lake Analytics à l’aide du Portail Azure](data-lake-analytics-get-started-portal.md) pour créer votre compte et charger les données manuellement :

    1. [Créer un compte Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account).
    2. [Télécharger SearchLog.tsv sur le compte Data Lake Storage par défaut](data-lake-analytics-get-started-portal.md#prepare-source-data).

## <a name="connect-to-azure"></a>Connexion à Azure
**Se connecter à Data Lake Analytics**

1. Ouvrez Visual Studio.
2. Dans le menu **Affichage**, cliquez sur **Explorateur de serveurs** pour ouvrir la fenêtre du même nom. Ou appuyez sur **[CTRL] + [ALT] + S**.
3. Cliquez avec le bouton droit sur **Azure**, cliquez sur Se connecter à un abonnement Microsoft Azure, puis suivez les instructions.
4. Dans l’**Explorateur de serveurs**, développez **Azure**, puis **Data Lake Analytics**. Le cas échéant, la liste de vos comptes Data Lake Analytics s'affiche. Vous ne pouvez pas créer de comptes Data Lake Analytics à partir de Visual Studio. Pour créer un compte, consultez [Prise en main d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md) ou [Prise en main d’Azure Data Lake Analytics avec Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="upload-source-data-files"></a>Téléchargement des fichiers de données source
Vous avez téléchargé des données dans la section **Configuration requise** précédemment dans le didacticiel.  

Pour utiliser vos propres données, procédez comme suit pour charger des données à partir des outils Data Lake.

**Charger les fichiers dans le compte Azure Data Lake dépendant**

1. Dans l’**Explorateur de serveurs**, développez successivement **Azure**, **Data Lake Analytics**, votre compte Data Lake Analytics, **Comptes de stockage**. Le compte de stockage Data Lake par défaut doit s'afficher, ainsi que les comptes de stockage Data Lake liés et les comptes Azure Storage liés. Le compte Data Lake par défaut a une étiquette « Compte de stockage par défaut ».
2. Cliquez avec le bouton droit sur le compte de stockage Data Lake par défaut, puis cliquez sur **Explorer**.  Le volet de l'Explorateur Data Lake Tools pour Visual Studio s'ouvre.  Une arborescence est affichée à gauche, l'affichage du contenu se trouve à droite.
3. Accédez au dossier où vous souhaitez télécharger des fichiers,
4. Cliquez avec le bouton droit sur n’importe quel espace vide, puis cliquez sur **Télécharger**.

    ![U-SQL Visual Studio projet U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Charger les fichiers dans un compte Azure Blob Storage lié**

1. Dans l’**Explorateur de serveurs**, développez successivement **Azure**, **Data Lake Analytics**, votre compte Data Lake Analytics, **Comptes de stockage**. Le compte de stockage Data Lake par défaut doit s'afficher, ainsi que les comptes de stockage Data Lake liés et les comptes Azure Storage liés.
2. Développez le compte Azure Storage.
3. Cliquez avec le bouton droit sur le conteneur dans lequel vous souhaitez télécharger des fichiers, puis cliquez sur **Explorer**. Si vous ne disposez pas d’un conteneur, vous devez commencer par en créer un à l’aide du portail Azure, d’Azure PowerShell ou d’autres outils.
4. Accédez au dossier où vous souhaitez télécharger des fichiers,
5. Cliquez avec le bouton droit sur n’importe quel espace vide, puis cliquez sur **Télécharger**.

## <a name="develop-u-sql-scripts"></a>Développer des scripts U-SQL
Les travaux Data Lake Analytics sont écrits en langage U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main du langage U-SQL](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Créer et soumettre une tâche Data Lake Analytics**

1. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
2. Sélectionnez le type **Projet U-SQL** .

    ![nouveau projet U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Cliquez sur **OK**. Visual Studio crée une solution avec un fichier **Script.usql** .
4. Insérez le script suivant dans le fichier **Script.usql**:

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

        @res =
            SELECT *
            FROM @searchlog;        

        OUTPUT @res   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Ce script U-SQL lit le fichier de données source avec **Extractors.Tsv()**, puis crée un fichier csv à l’aide d’**Outputters.Csv()**.

    Ne modifiez pas les deux chemins d'accès, sauf si vous avez copié le fichier source dans un autre emplacement.  Data Lake Analytics créera le dossier de sortie s'il n'existe pas encore.

    Il est plus simple d’utiliser des chemins d’accès relatifs pour les fichiers stockés dans les comptes Data Lake par défaut. Vous pouvez également utiliser des chemins d’accès absolus.  Par exemple :

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés.  La syntaxe des fichiers stockés dans des comptes Azure Storage liés est la suivante :

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

   > [!NOTE]
   > Les conteneurs d'objets Blob Azure avec des autorisations d'accès aux objets Blob publics ou aux conteneurs publics ne sont pas pris en charge actuellement.  
   >
   >

    Notez les caractéristiques suivantes :

   * **IntelliSense**

       Nom renseigné automatiquement et les membres seront affichés pour Ensemble de lignes, Classes, Bases de données, Schémas et Objets définis par l'utilisateur (UDO).

       IntelliSense pour les entités de catalogue (Bases de données, Schémas, Tables, UDO, etc.) est lié à votre compte de calcul. Vous pouvez vérifier le compte de calcul, la base de données et le schéma actifs actuels dans la barre d'outils supérieure et les faire commuter via les listes déroulantes.
   * **Développer * les colonnes**

       Cliquez à droite de *. Un trait de soulignement bleu doit s’afficher sous *. Placez le curseur sur le trait de soulignement bleu, puis cliquez sur la flèche vers le bas.
       ![Outils Data Lake Visual Studio : développer *](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-expand-asterisk.png)

       Cliquez sur **Développer les colonnes**. L’outil remplace alors * par le nom des colonnes.
   * **Mise en forme automatique**

       Les utilisateurs peuvent modifier la mise en retrait du script U-SQL basé sur la structure de code sous Modifier->Avancé :

     * Mettre le document en forme (Ctrl+E, D) : met en forme l'ensemble du document   
     * Mettre la sélection en forme (Ctrl+K, Ctrl+F) : met en forme la sélection. Si aucune sélection n'a été effectuée, ce raccourci met en forme la ligne où se trouve le curseur.  

       Toutes les règles de mise en forme peuvent être configurées sous Outils->Options->Éditeur de texte->SIP->Mise en forme.  
   * **Mise en retrait intelligente**

       Data Lake Tools pour Visual Studio peut mettre en retrait automatiquement les expressions lorsque vous écrivez des scripts. Cette fonctionnalité est désactivée par défaut. Les utilisateurs doivent l'activer sous U-SQL->Options et paramètres->Commutateurs->Activer la mise en retrait intelligente.
   * **Accès à la définition et recherche de toutes les références**

       Cliquez avec le bouton droit sur le nom d'un ensemble de lignes/paramètre/d'une colonne/d'un UDO, etc. et cliquez sur Atteindre la définition (F12) pour accéder à sa définition. Cliquez sur Rechercher toutes les références (Maj+F12) pour afficher toutes les références.
   * **Insertion du chemin d'accès Azure**

       Plutôt que de mémoriser le chemin d’accès du fichier Azure et de le taper manuellement lors de l’écriture du script, Data Lake Tools pour Visual Studio offre une méthode facile : cliquez avec le bouton droit dans l’éditeur, puis cliquez sur Insérer le chemin d’accès Azure. Accédez au fichier dans la boîte de dialogue de l'Explorateur d'objets Blob Azure. Cliquez sur **OK**. Le chemin d’accès est inséré dans votre code.
5. Spécifiez le compte Data Lake Analytics, la base de données et le schéma. Vous pouvez sélectionner **(local)** pour exécuter le script en local à des fins de test. Pour plus d’informations, consultez [Exécuter U-SQL localement](#run-u-sql-locally).

    ![Soumettre un projet U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Pour plus d'informations, consultez [Utilisation du catalogue U-SQL](data-lake-analytics-use-u-sql-catalog.md).
6. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Générer le script**. Vérifiez le résultat dans le volet Sortie.
7. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Soumettre le script**. Si vous le souhaitez, vous pouvez également cliquer sur **Soumettre** dans le volet Script.usql.  Consultez la capture d’écran précédente.  Cliquez sur la flèche vers le bas en regard du bouton Soumettre pour envoyer à l'aide des options avancées :
8. Indiquez le **nom du travail**, vérifiez le **compte Analytics**, puis cliquez sur **Envoyer**. Les résultats de la soumission et le lien vers la tâche sont disponibles dans la fenêtre Résultats de Data Lake Tools pour Visual Studio à l'issue de la soumission.

    ![Soumettre un projet U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
9. Vous devez cliquer sur le bouton Actualiser pour afficher le dernier état du travail et actualiser l’écran. Si le travail réussit, vous verrez le **Graphique** associé, les **Opérations sur les métadonnées**, l’**Historique de l’état**, les **Diagnostics** :

    ![Graphique des performances du travail U-SQL Visual Studio Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * Résumé du travail. Affiche les informations de résumé du travail en cours, par exemple : état, progression, durée d'exécution, nom de l'exécution, demandeur, etc.   
   * Détails du travail. Informations détaillées sur ce travail, y compris le script, les ressources, la vue d'exécution du vertex.
   * Graphique du travail. Plusieurs graphiques permettent de visualiser les informations du travail : progression, données lues, données écrites, durée d’exécution, durée d’exécution moyenne par nœud, débit d’entrée, débit de sortie.
   * Opérations sur les métadonnées. Affiche toutes les opérations sur les métadonnées.
   * Historique de l'état.
   * Diagnostics. Data Lake Tools pour Visual Studio diagnostique automatiquement l'exécution du travail. Vous recevrez des alertes lorsqu'il y a des erreurs ou des problèmes de performances dans les travaux. Consultez la section Diagnostics de travaux (lien TBD) pour plus d'informations.

**Pour vérifier l'état du travail**

1. Dans l’Explorateur de serveurs, développez successivement **Azure**, **Data Lake Analytics**, le nom du compte Data Lake Analytics
2. Double-cliquez sur **Travaux** pour répertorier les travaux.
3. Cliquez sur un travail pour en afficher l'état.

**Pour voir la sortie du travail**

1. Dans l’**Explorateur de serveurs**, développez **Azure**, **Data Lake Analytics**, puis votre compte Data Lake Analytics et **Comptes de stockage**. Cliquez avec le bouton droit sur le compte de stockage Data Lake par défaut, puis sur **Explorateur**.
2. Double-cliquez sur **sortie** pour ouvrir le dossier
3. Double-cliquez sur **SearchLog-From-adltools.csv**.

### <a name="job-playback"></a>Lecture de travail
La lecture de travail vous permet de surveiller la progression de l'exécution du travail et de détecter visuellement les anomalies de performance et les goulots d'étranglement. Cette fonctionnalité peut être utilisée avant la fin de l'exécution du travail (c'est-à-dire, pendant que le travail est en cours d'exécution), ainsi que lorsque l'exécution est terminée. Exécuter la lecture pendant l'exécution du travail permet à l'utilisateur de lire la progression jusqu'à l'heure actuelle.

**Pour afficher la progression de l'exécution du travail**  

1. Cliquez sur **CHARGER LE PROFIL** dans le coin supérieur droit. Reportez-vous à la capture d'écran précédente.
2. Cliquez sur le bouton Lecture dans le coin inférieur gauche pour passer en revue la progression de l'exécution du travail.
3. Lors de la lecture, cliquez sur **Pause** pour l’arrêter ou faites glisser directement la barre de progression sur des positions spécifiques.

### <a name="heat-map"></a>Carte thermique
Data Lake Tools pour Visual Studio fournit des superpositions en couleurs sélectionnables par l'utilisateur dans la vue du travail pour indiquer la progression, les E/S de données, la durée d'exécution, le débit E/S de chaque étape. Grâce à cela, les utilisateurs peuvent déterminer les problèmes potentiels et la distribution des propriétés du travail directement et de façon intuitive. Vous pouvez choisir une source de données à afficher dans la liste déroulante.  

## <a name="run-u-sql-locally"></a>Exécuter U-SQL localement

Vous pouvez utiliser Azure Data Lake Tools pour Visual Studio et le Kit de développement logiciel (SDK) U-SQL pour exécuter des travaux U-SQL sur votre station de travail, comme vous pouvez le faire dans le service Azure Data Lake. Ces deux fonctionnalités d’exécution locale accélèrent les procédures de test et de débogage de vos travaux U-SQL. 

* [Tester et déboguer des travaux U-SQL à l’aide d’une exécution locale et du Kit de développement logiciel (SDK) Azure Data Lake U-SQL](data-lake-analytics-data-lake-tools-local-run.md)


## <a name="see-also"></a>Voir aussi
Pour commencer à utiliser Data Lake Analytics à l'aide de différents outils, consultez :

* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Prise en main de Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Prise en main de Data Lake Analytics à l'aide du Kit de développement logiciel (SDK) .NET](data-lake-analytics-get-started-net-sdk.md)
* [Débogage de travaux U-SQL](data-lake-analytics-debug-u-sql-jobs.md)

Pour découvrir les outils Data Lake pour Visual Studio Code, consultez la page [Use the Azure Data Lake Tools for Visual Studio Code (Utiliser les outils Azure Data Lake pour Visual Studio Code)](data-lake-analytics-data-lake-tools-for-vscode.md).

Pour afficher d’autres rubriques sur le développement :

* [Analyser les journaux web à l'aide de Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)
* [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
* [Développer des opérateurs définis par l'utilisateur U-SQL pour des travaux Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)

## <a name="appx-a-powershell-sample-for-preparing-the-tutorial"></a>Annexe A - Exemple PowerShell pour la préparation du didacticiel
Le script PowerShell ci-après prépare un compte Azure Data Lake Analytics et la source de données pour vous. Vous pouvez donc passer à la section [Développer des scripts U-SQL](data-lake-analytics-data-lake-tools-get-started.md#develop-u-sql-scripts).

    #region - used for creating Azure service names
    $nameToken = "<Enter an alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - service names
    $resourceGroupName = $namePrefix + "rg"
    $dataLakeStoreName = $namePrefix + "adas"
    $dataLakeAnalyticsName = $namePrefix + "adla"
    $location = "East US 2"
    #endregion


    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an Azure Data Lake Analytics service account
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsName  
    #endregion

    #region - prepare the source data
    Write-Host "Import the source data ..."  -ForegroundColor Green
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

    Write-Host "List the source data ..."  -ForegroundColor Green
    Get-AzureRmDataLakeStoreChildItem -Account $dataLakeStoreName -Path  "/Samples/Data/"
    #endregion

