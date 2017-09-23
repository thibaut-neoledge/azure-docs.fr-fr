---
title: "Tester et déboguer des travaux U-SQL à l’aide d’une exécution locale et du Kit de développement logiciel (SDK) Azure Data Lake U-SQL | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio et le Kit de développement logiciel (SDK) Azure Data Lake U-SQL pour tester et déboguer des travaux U-SQL sur votre station de travail locale."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: yanacai
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 771a96df5cc66bac46e7144785be8cc072b57b31
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Tester et déboguer des travaux U-SQL à l’aide d’une exécution locale et du Kit de développement logiciel (SDK) Azure Data Lake U-SQL

Vous pouvez utiliser Azure Data Lake Tools pour Visual Studio et le Kit de développement logiciel (SDK) U-SQL pour exécuter des travaux U-SQL sur votre station de travail, comme vous pouvez le faire dans le service Azure Data Lake. Ces deux fonctionnalités d’exécution locale accélèrent les procédures de test et de débogage de vos travaux U-SQL.

## <a name="understand-the-data-root-folder-and-the-file-path"></a>Comprendre le dossier racine de données et le chemin d’accès

L’exécution locale et le Kit de développement logiciel (SDK) U-SQL nécessitent un dossier racine de données. Ce dossier est un « magasin local » pour le compte de calcul local. Il est équivalent au compte Azure Data Lake Store d’un compte Data Lake Analytics dans Azure. Basculer vers un dossier racine de données différent revient à basculer vers un autre compte de magasin. Si vous souhaitez accéder aux données généralement partagées avec des dossiers racines de données différents, vous devez utiliser des chemins d’accès absolus dans vos scripts. Vous pouvez aussi créer des liens symboliques de système de fichiers (par exemple, **mklink** sur NTFS) sous le dossier racine de données, afin de pointer vers les données partagées.

Le dossier racine de données est utilisé pour :

- Stocker les métadonnées, notamment des bases de données, des tables, des fonctions table (TVF) et des assemblys.
- Rechercher les chemins d’entrée et de sortie qui sont définis comme des chemins relatifs dans U-SQL. Les chemins relatifs facilitent le déploiement de vos projets U-SQL dans Azure.

Vous pouvez utiliser un chemin d’accès relatif et un chemin d’accès absolu local dans des scripts SQL-U. Le chemin d’accès relatif dépend du chemin d’accès au dossier racine de données spécifié. Il est recommandé d’utiliser « / » en tant que séparateur de chemin afin de rendre vos scripts compatibles avec le côté serveur. Voici quelques exemples de chemins relatifs, avec leurs chemins absolus équivalents. Dans ces exemples, le chemin C:\LocalRunDataRoot correspond à la racine de données.

|Chemin relatif|Chemin absolu|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Utiliser l’exécution locale depuis Visual Studio

Data Lake Tools pour Visual Studio fournit une expérience d’exécution locale U-SQL dans Visual Studio. À l’aide de cette fonctionnalité, vous pouvez :

- Exécuter des scripts U-SQL en local, ainsi que des assemblys C#.
- Déboguer un assembly C# en local.
- Créer, afficher et supprimer des catalogues U-SQL (bases de données locales, assemblys, schémas et tables) de l’Explorateur de serveurs. Le catalogue local est également accessible depuis l’Explorateur de serveurs.

    ![Data Lake Tools pour Visual Studio exécution locale catalogue local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Le programme d’installation de Data Lake Tools crée un dossier C:\LocalRunRoot, qui est utilisé en tant que dossier racine de données par défaut. Le parallélisme d’exécution locale par défaut est 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Pour configurer l’exécution locale dans Visual Studio

1. Ouvrez Visual Studio.
2. Ouvrez l’**Explorateur de serveurs**.
3. Développez **Azure** > **Data Lake Analytics**.
4. Cliquez sur le menu **Data Lake**, puis sur **Options et paramètres**.
5. Dans l’arborescence de gauche, développez **Azure Data Lake**, puis **Général**.

    ![Data Lake Tools pour Visual Studio exécution locale configurer paramètres](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Un projet U-SQL Visual Studio est requis pour exécuter l’exécution locale. Cette partie diffère de l'exécution de scripts U-SQL à partir d'Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Pour exécuter un script U-SQL localement
1. Dans Visual Studio, ouvrez votre projet U-SQL.   
2. Cliquez avec le bouton droit sur un script U-SQL dans l'Explorateur de solutions, puis cliquez sur **Soumettre le script**.
3. Sélectionnez **(Local)** en tant que compte Analytics pour exécuter votre script en local.
Vous pouvez également cliquer sur le compte **(Local)** en haut de la fenêtre de script, puis sur **Envoyer** (ou utiliser le raccourci clavier Ctrl + F5).

    ![Data Lake Tools pour Visual Studio exécution locale soumettre travaux](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>Déboguer localement des scripts et des assemblys C#

Vous pouvez déboguer des assemblys C# sans les envoyer ni les inscrire auprès du service Azure Data Lake Analytics. Vous pouvez définir des points d'arrêt à la fois dans les fichier code-behind et dans un projet C# référencé.

#### <a name="to-debug-local-code-in-code-behind-file"></a>Pour déboguer le code local dans le fichier code-behind

1. Définissez des points d'arrêt dans le fichier code-behind.
2. Appuyez sur F5 pour déboguer le script localement.

> [!NOTE]
   > La procédure suivante fonctionne uniquement dans Visual Studio 2015. Dans les versions Visual Studio plus anciennes, vous devrez peut-être ajouter manuellement les fichiers pdb.  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Pour déboguer le code local dans un projet C# référencé

1. Créez un projet d'assembly C# et générez-le pour obtenir la dll de sortie.
2. Inscrivez la dll à l'aide d'une instruction SQL-U :

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Définissez des points d'arrêt dans le code C#.
4. Appuyez sur F5 pour déboguer le script en faisant référence à la DLL C# localement.

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Utiliser une exécution locale à partir du Kit de développement logiciel (SDK) U-SQL Data Lake

Outre l’exécution locale de scripts U-SQL à l’aide de Visual Studio, vous pouvez également utiliser le Kit de développement logiciel (SDK) U-SQL Azure Data Lake pour exécuter les scripts U-SQL en local, avec la ligne de commande et les interfaces de programmation. Par ce biais, vous pouvez mettre votre test local U-SQL à l’échelle.

En savoir plus sur le [Kit de développement logiciel (SDK) U-SQL Azure Data Lake](data-lake-analytics-u-sql-sdk.md).


## <a name="next-steps"></a>Étapes suivantes

* Pour consulter une requête plus complexe, voir [Analyse de journaux des sites web à l’aide d’Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour afficher les détails d’un travail, voir [Utilisation de l’Explorateur de travaux et de la Vue des travaux pour les travaux Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Pour utiliser la vue d’exécution du vertex, voir [Utilisation de la vue d’exécution du vertex dans Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

