---
title: "Débogage de travaux U-SQL | Microsoft Docs"
description: "Découvrez comment déboguer un vertex U-SQL ayant échoué à l’aide de Visual Studio."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Débogage de code C# défini par l’utilisateur pour des travaux U-SQL ayant échoué

U-SQL fournit un modèle d’extensibilité en C#. Vous pouvez donc écrire votre code pour ajouter des fonctionnalités telles qu’un extracteur ou un réducteur personnalisés. Pour plus d’informations, voir le [Guide de programmabilité U-SQL](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). Dans la pratique, tout code peut nécessiter un débogage, et les systèmes de Big Data ne peuvent fournir, pour le débogage du runtime, que des informations limitées, telles que des fichiers journaux.

Azure Data Lake Tools pour Visual Studio fournit une fonctionnalité appelée **débogage d'échec du vertex**, ce qui vous permet de cloner une tâche ayant échoué à partir du cloud sur votre ordinateur local à des fins de débogage. Le clone local capture l’environnement cloud entier, y compris les données d'entrée et le code utilisateur.

La vidéo suivante montre la fonctionnalité Débogage d'échec du vertex dans Azure Data Lake Tools pour Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Visual Studio requiert les deux mises à jour suivantes, si elle ne sont pas encore installées : [Redistributable Microsoft Visual C++ 2015 Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) et [Windows 10 Universal C Runtime](https://www.microsoft.com/download/details.aspx?id=50410).

## <a name="download-failed-vertex-to-local-machine"></a>Télécharger le vertex ayant échoué sur l’ordinateur local

Lorsque vous ouvrez un travail ayant échoué dans Azure Data Lake Tools pour Visual Studio, vous voyez une barre d’alerte jaune avec des messages d’erreur détaillés sous l’onglet d’erreur.

1. Cliquez sur **Télécharger** pour télécharger toutes les ressources et les flux d’entrée requis. Si le téléchargement ne s’achève pas, cliquez sur **Réessayer**.

2. Une fois le téléchargement terminé, cliquez sur **Ouvrir** pour générer un environnement de débogage local. Une nouvelle instance Visual Studio avec une solution de débogage est automatiquement créée et ouverte.

![Télécharger le vertex Visual Studio pour le débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

Les travaux peuvent inclure des fichiers sources code-behind ou des assemblys inscrits, et les scénarios de débogage diffèrent pour ces deux types.

- [Débogage de tâches ayant échoué avec code-behind](#debug-job-failed-with-code-behind)
- [Débogage de tâches ayant échoué avec des assemblys](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>Débogage de tâches ayant échoué avec code-behind

Si un travail U-SQL échoue, qui inclut du code utilisateur (généralement nommé `Script.usql.cs` dans un projet U-SQL), ce code source est importé dans la solution de débogage.  À partir de là, vous pouvez vous servir des outils de débogage de Visual Studio (espion, variables, etc.) pour résoudre le problème.

> [!NOTE]
> Avant de déboguer, vérifiez que vous avez activé **Exceptions Common Language Runtime** dans la fenêtre Paramètres d'exception (**Ctrl+Alt+E**).

![Configuration de Visual Studio pour le débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Appuyez sur **F5** pour exécuter le code du fichier code-behind. Celui-ci s’exécute jusqu'à ce qu’il soit arrêté par une exception.

2. Ouvrez le fichier `ADLTool_Codebehind.usql.cs` et définissez des points d’arrêt, puis appuyez sur **F5** pour déboguer le code pas à pas.

    ![Exception de débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Débogage de tâches ayant échoué avec des assemblys

Si vous utilisez des assemblys inscrits dans votre script U-SQL, le système ne peut pas obtenir automatiquement le code source. Dans ce cas, ajoutez manuellement les fichiers de code source des assemblys à la solution.

### <a name="configure-the-solution"></a>Configuration de la solution

1. Cliquez avec le bouton droit sur **Solution 'VertexDebug' > Ajouter > Projet existant...**  pour rechercher le code source des assemblys et ajouter le projet à la solution de débogage.

    ![Projet d’ajout de débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Cliquez avec le bouton droit **LocalVertexHost > Propriétés** dans la solution, puis copiez le chemin du **Répertoire de travail**.

3. Cliquez avec le bouton droit sur **projet de code source d’assembly > Propriétés**, sélectionnez l’onglet **Générer** à gauche, puis collez le chemin d’accès copié en tant que **Sortie > Chemin de sortie**.

    ![Chemin d’accès de pdb de définition de débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Appuyez sur **Ctrl + Alt + E**, vérifiez les **exceptions Common Language Runtime** dans la fenêtre des paramètres d’exception.

### <a name="start-debug"></a>Début du débogage

1. Cliquez avec le bouton droit sur **projet de code source d’assembly > Régénérer** pour sortir les fichiers .pdb dans le répertoire de travail `LocalVertexHost`.

2. Appuyez sur **F5**. Le projet s’exécute jusqu'à ce qu’il soit arrêté par une exception. Si le message d’avertissement suivant s’affiche, vous pouvez l’ignorer en toute sécurité. Il peut s’écouler jusqu'à une minute avant que l’écran de débogage s’affiche.

    ![Avertissement Visual Studio pour le débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Ouvrez le code source et définissez des points d’arrêt, puis appuyez sur **F5** pour déboguer le code pas à pas.

Vous pouvez également vous servir des outils de débogage de Visual Studio (espion, variables, etc.) pour résoudre le problème.

> [!NOTE]
> Régénérez le projet de code source d’assembly chaque fois vous modifiez le code, afin de générer des fichiers .pdb à jour.

Après le débogage, si le projet se termine correctement, la fenêtre de sortie affiche le message suivant :

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Débogage U-SQL Azure Data Lake Analytics réussi](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Renvoyer le travail

Une fois le débogage terminé, renvoyez le travail ayant échoué.

1. Pour les travaux avec des solutions code-behind, copiez votre code C# dans le fichier source code-behind (généralement `Script.usql.cs`).
2. Pour les travaux avec des assemblys, inscrivez les assemblys .dll mis à jour dans votre base de données ADLA :
    1. À partir de l’Explorateur de serveurs ou de Cloud Explorer, développez le nœud **Compte ADLA > Bases de données**.
    2. Cliquez avec le bouton droit sur **Assemblys**, puis enregistrez vos nouveaux assemblys .dll auprès de la base de données ADLA : ![Enregistrement d’assembly débogué U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
3. Renvoyez le travail.

## <a name="next-steps"></a>Étapes suivantes

- [Guide de programmabilité U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Développer des opérateurs U-SQL définis par l’utilisateur pour des travaux Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Didacticiel : Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
