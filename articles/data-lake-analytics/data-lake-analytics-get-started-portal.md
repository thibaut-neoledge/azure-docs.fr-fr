---
title: "Prise en main d’Azure Data Lake Analytics à l’aide du portail Azure | Microsoft Docs"
description: "Découvrez comment utiliser le portail Azure pour créer un compte Data Lake Analytics, créer un travail Data Lake Analytics avec U-SQL et envoyer le travail. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/06/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 3810aa816b1734e56443754e34b2d7024ba571db


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Didacticiel : Prise en main du service Azure Data Lake Analytics à l’aide du portail Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser le portail Azure pour créer des comptes Azure Data Lake Analytics, définir des travaux Data Lake Analytics dans [U-SQL](data-lake-analytics-u-sql-get-started.md) et envoyer des travaux au service Data Lake Analytics. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

Dans ce didacticiel, vous développez un travail qui lit un fichier TSV (valeurs séparées par des tabulations) et le convertit en fichier CSV (valeurs séparées par des virgules). Pour suivre ce didacticiel même à l'aide d'autres outils pris en charge, cliquez sur les onglets en haut de cette section. Une fois votre premier travail terminé, vous pouvez commencer à écrire des transformations de données plus complexes avec U-SQL.

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-data-lake-analytics-account"></a>Créer un compte Analytique Data Lake
Vous devez disposer d'un compte Data Lake Analytics avant de pouvoir exécuter des travaux quelconques.

Chaque compte Data Lake Analytics possède un compte [Azure Data Lake Store]() dépendant.  Ce compte est désigné comme le compte Data Lake Store par défaut.  Vous pouvez créer le compte Data Lake Store au préalable ou lorsque vous créez votre compte Data Lake Analytics. Dans ce didacticiel, vous allez créer le compte Data Lake Store avec le compte Data Lake Analytics.

**Pour créer un compte Analytique Data Lake**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau**, **Données + Analyse**, puis sur **Data Lake Analytics**.
3. Tapez ou sélectionnez les valeurs suivantes :

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

   * **Nom** : nom du compte Data Lake Analytics.
   * **Abonnement**: choisissez l’abonnement Azure utilisé pour le compte Analytics.
   * **Groupe de ressources**. Sélectionnez un groupe de ressources Azure existant ou créez-en un. Azure Resource Manager vous permet de manipuler les ressources de votre application sous la forme d’un groupe. Pour plus d'informations, consultez [Présentation d'Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
   * **Emplacement**. Sélectionnez un centre de données Azure pour le compte Data Lake Analytics.
   * **Data Lake Store**: chaque compte Data Lake Analytics possède un compte Data Lake Store dépendant. Le compte Data Lake Analytics et le compte Data Lake Store dépendant doivent se trouver dans le même centre de données Azure. Suivez les instructions pour créer un compte Data Lake Store ou sélectionnez-en un existant.
4. Cliquez sur **Create**. L'écran d'accueil du portail s'affiche. Une nouvelle vignette est ajoutée au Tableau d'accueil indiquant « Déploiement d'Azure Data Lake Analytics ». La création d'un compte Data Lake Analytics prend plusieurs minutes. Lorsque le compte est créé, le portail ouvre le compte dans un nouveau volet.

Après la création d'un compte Data Lake Analytics, vous pouvez ajouter des comptes Data Lake Store et des comptes Azure Storage supplémentaires. Pour obtenir des instructions, consultez [Gestion des sources de données du compte Data Lake Analytics](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

## <a name="prepare-source-data"></a>Préparer les données source
Dans ce didacticiel, vous traitez des journaux de recherche.  Le journal de recherche peut être stocké dans Data Lake Store ou dans le stockage d’objets blobs Azure.

Le portail Azure fournit une interface utilisateur pour la copie de fichiers de données d’exemple vers le compte Data Lake Store par défaut, y compris un fichier de journal de recherche.

**Pour copier des fichiers de données d'exemple**

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre compte Data Lake Analytics.  Consultez [Gestion des comptes Data Lake Analytics](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account) pour en créer un et ouvrir le compte dans le portail.
2. Développez le volet **Bases**, puis cliquez sur **Explorer les exemples de scripts**. Ceci ouvre un autre volet appelé **Exemples de scripts**.

    ![Volet d’exemple de script du portail Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-sample-scripts.png)
3. Cliquez sur **Exemple de données manquantes** pour copier les fichiers d’exemple de données. Lorsque vous avez terminé, le portail affiche **Exemples de données mis à jour**.
4. Dans le volet du compte Data Lake Analytics, cliquez sur **Explorateur de données** en haut.

    ![Bouton Explorateur de données Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    Ceci ouvre deux volets. L'un est l' **Explorateur de données**et l'autre est le compte Data Lake Store par défaut.
5. Dans le volet du compte Data Lake Store par défaut, cliquez sur **Exemples** pour développer le dossier, puis cliquez sur **Données** pour développer le dossier. Vous devez voir les fichiers et dossiers suivants :

   * AmbulanceData/
   * AdsLog.tsv
   * SearchLog.tsv
   * version.txt
   * WebLog.log

     Dans ce didacticiel, nous utilisons SearchLog.tsv.

Dans la pratique, vous programmez vos applications soit pour écrire les données dans des comptes de stockage liés, soit pour télécharger les données. Pour le téléchargement de fichiers, consultez [Téléchargement de données vers Data Lake Store](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) ou [Téléchargement de données vers le stockage d’objets Blob](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb).

## <a name="create-and-submit-data-lake-analytics-jobs"></a>Création et envoi de travaux Data Lake Analytics
Une fois que vous avez préparé la source de données, vous pouvez démarrer le développement d'un script U-SQL.  

**Pour soumettre un travail**

1. Dans le volet du compte Data Lake Analytics du portail, cliquez sur **Nouveau travail**.

    ![Bouton Nouveau travail Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    Si le volet ne s'affiche pas, consultez [Ouvrir un compte Data Lake Analytics à partir du portail](data-lake-analytics-manage-use-portal.md#access-adla-account).
2. Entrez le **Nom du travail**et le script U-SQL suivant :

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

    ![créer des travaux U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

    Ce script U-SQL lit le fichier de données source avec **Extractors.Tsv()**, puis crée un fichier csv à l’aide d’**Outputters.Csv()**.

    Ne modifiez pas les deux chemins d’accès, sauf si vous copiez le fichier source dans un autre emplacement.  Data Lake Analytics crée le dossier de sortie s’il n’existe pas encore.  Dans ce cas, nous utilisons des chemins d'accès simples et relatifs.  

    Il est plus simple d'utiliser des chemins d'accès relatifs pour les fichiers stockés dans les comptes Data Lake par défaut. Vous pouvez également utiliser des chemins d’accès absolus.  Par exemple :

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Pour en savoir plus sur U-SQL, consultez [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

1. Cliquez sur **Soumettre le travail** en haut.   
2. Attendez que l'état du travail passe à **Réussi**. Vous pouvez voir que l’exécution du travail a pris une minute environ.

    En cas d’échec du travail, consultez [Surveillance et dépannage des travaux Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
3. En bas du volet, cliquez sur l’onglet **Sortie**, puis **SearchLog-from-Data-Lake.csv**. Vous pouvez afficher un aperçu, télécharger, renommer et supprimer le fichier de sortie.

    ![Propriétés du fichier de sortie du travail Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)

## <a name="see-also"></a>Voir aussi
* Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
* Pour obtenir une vue d'ensemble de Data Lake Analytics, consultez [Vue d'ensemble de Data Lake Analytics Azure](data-lake-analytics-overview.md).
* Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
* Pour consigner les informations de diagnostic, consultez [Accès aux journaux de diagnostic d’Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)



<!--HONumber=Nov16_HO2-->


