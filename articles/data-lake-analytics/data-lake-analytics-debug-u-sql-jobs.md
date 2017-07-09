---
title: "Débogage de travaux U-SQL | Microsoft Docs"
description: "Apprenez à déboguer un vertex U-SQL ayant échoué à l’aide de Visual Studio. "
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: yanacai
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: aac455520ab62d69b406a254a54b0f000ea2e5bc
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017


---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Débogage du code C# défini par l’utilisateur pour les travaux U-SQL ayant échoué

U-SQL fournit le modèle d’extensibilité via C#. Les utilisateurs peuvent écrire du code C# défini par l’utilisateur, comme un extracteur ou un réducteur défini par l’utilisateur, etc., pour obtenir davantage d’extensibilité (en savoir plus sur le [Code U-SQL défini par l’utilisateur](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#user-defined-functions---udf)). Toutefois, aucun développeur ne peut coder sans faire d’erreur et le débogage dans des systèmes de données volumineux est compliqué, car de nombreux systèmes ne fournissent que des informations limitées sur le débogage du runtime comme, par exemple, des journaux, etc. 

Azure Data Lake Tools pour Visual Studio offre une fonctionnalité appelée **Débogage d'échec du vertex** qui vous permet de cloner aisément un environnement ayant échoué (notamment les données d’entrée intermédiaires et le code utilisateur, etc.) à partir du cloud sur votre ordinateur local pour le suivi et le débogage de travaux ayant échoué avec le même runtime et des données d’entrée exactes dans le cloud.

La vidéo suivante illustre la fonction **Débogage d'échec du vertex** dans Azure Data Lake Tools pour Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Visual Studio peut se bloquer si vous n’avez pas les deux mises à niveau Windows suivantes : [Microsoft Visual C++ 2015 Redistributable Update 2](https://www.microsoft.com/download/details.aspx?id=51682) et [Universal C Runtime pour Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).
> 

## <a name="download-failed-vertex-to-local"></a>Téléchargement en local du vertex ayant échoué

Lorsque vous ouvrez un travail ayant échoué dans Azure Data Lake Tools pour Visual Studio, une alerte s’affiche. Les messages d’erreur détaillés s’affichent sous l’onglet Erreur et dans la barre d’alerte jaune en haut de la fenêtre.

1. Cliquez sur **Télécharger** pour télécharger toutes les ressources et les flux d’entrée requis. Cliquez sur **Réessayer** si le téléchargement échoue. 

2. Cliquez sur **Ouvrir** une fois le téléchargement terminé pour générer un environnement de débogage local. Une nouvelle instance de Visual Studio avec une solution de débogage est créée et ouverte automatiquement. 

3. La procédure de débogage est légèrement différente selon les assemblys et les travaux ayant échoué avec code-behind.

    - [Débogage de tâches ayant échoué avec code-behind](#debug-job-failed-with-code-behind)
    - [Débogage de tâches ayant échoué avec des assemblys](#debug-job-failed-with-assemblies)

![Télécharger le vertex Visual Studio pour le débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="debug-job-failed-with-code-behind"></a>Débogage de tâches ayant échoué avec code-behind

Si vous avez écrit le code défini par l’utilisateur dans un fichier code-behind (généralement appelé « Script.usql.cs » dans le projet U-SQL) et si le travail U-SQL a ensuite échoué, le code source est automatiquement importé dans la solution de débogage générée. Vous pouvez simplement utiliser l’expérience de débogage Visual Studio (espion, variables, etc.) pour résoudre le problème. 

Avant le débogage, vérifiez que vous avez activé **Exceptions Common Language Runtime** dans la fenêtre des paramètres d’exception (**Ctrl + Alt + E**).

![Configuration de Visual Studio pour le débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Appuyez sur **F5**. Le code code-behind s’exécute automatiquement jusqu’à ce qu’il soit arrêté par une exception.

2. Ouvrez **ADLTool_Codebehind.usql.cs** dans le projet, définissez des points d’arrêt, puis appuyez sur F5 pour déboguer le code pas à pas.

    ![Exception de débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Débogage de tâches ayant échoué avec des assemblys

Si vous utilisez des assemblys dans le script U-SQL, le système ne peut pas obtenir automatiquement le code source. Vous devez effectuer certaines configurations avant le débogage du code défini par l’utilisateur, à savoir ajouter le code source des assemblys à la solution générée automatiquement.

### <a name="configure-the-solution"></a>Configuration de la solution

1. Cliquez avec le bouton droit sur **Solution 'VertexDebug'** > **Ajouter** > **Projet existant...**  pour rechercher le code source des assemblys et ajouter le projet à la solution de débogage.

    ![Projet d’ajout de débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Cliquez avec le bouton droit sur **LocalVertexHost** > **Propriétés** dans la solution, copiez le chemin d’accès **Répertoire de travail**.

3. Cliquez avec le bouton droit sur **projet de code source d’assembly** > **Propriétés**, sélectionnez l’onglet **Créer** à gauche, collez le chemin d’accès copié à l’étape 2 dans **Sortie** > **Chemin de sortie**.  

    ![Chemin d’accès de pdb de définition de débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Appuyez sur **Ctrl + Alt + E**, vérifiez les **exceptions Common Language Runtime** dans la fenêtre des paramètres d’exception.

### <a name="start-debug"></a>Début du débogage

1. Cliquez avec le bouton droit sur **projet de code source d’assembly** > **Régénérer** pour créer des fichiers pdb dans le répertoire de travail LocalVertexHost.

2. Appuyez sur **F5**. Le projet s’exécute automatiquement jusqu’ ce qu’il soit arrêté par une exception. Le message suivant peut s’afficher pour la première fois. Vous pouvez l’ignorer. L’affichage de l’écran de débogage peut prendre jusqu'à une minute.

    ![Avertissement Visual Studio pour le débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Ouvrez le code source et définissez des points d’arrêt, puis appuyez sur **F5** pour déboguer le code pas à pas.

Vous pouvez également utiliser d’autres expériences de débogage Visual Studio (espion, variables, etc.) pour résoudre le problème. 

**Remarque** : vous devez régénérer le projet de code source d’assembly chaque fois que vous modifiez le code afin que les nouveaux fichiers pdb soient appliqués.

Une fois le débogage terminé, la fenêtre de sortie affiche le message suivant :

    The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).

![Débogage U-SQL Azure Data Lake Analytics réussi](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Renvoyer le travail
Une fois le débogage terminé, vous pouvez renvoyer le travail ayant échoué.

1. Enregistrez les nouveaux assemblys .dll dans votre base de données ADLA.

    1. À partir de l’Explorateur de serveurs/Cloud Explorer, développez le nœud **bases de données** > **du compte ADLA**.
    2. Cliquez avec le bouton droit sur **Assemblys** pour enregistrer les assemblys. 
    3. Enregistrez vos nouveaux assemblys .dll dans la base de données ADLA.
    ![Assembly d’enregistrement de débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
2. Vous pouvez également recopier votre code C# dans le fichier code-behind C# script.usql.cs.
3. Renvoyez le travail.

## <a name="next-steps"></a>Étapes suivantes

* [Guide de programmabilité U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
* [Développer des opérateurs définis par l’utilisateur U-SQL pour des travaux Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* [Didacticiel : Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)



