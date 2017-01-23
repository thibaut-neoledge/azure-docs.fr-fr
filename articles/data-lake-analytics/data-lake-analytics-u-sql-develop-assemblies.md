---
title: "Développer des assemblys U-SQL pour des travaux Azure Data Lake Analytics | Microsoft Docs"
description: "Apprenez à développer des assemblys pour les utiliser et les réutiliser dans des travaux Data Lake Analytics. "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 98b04d28d1b905dad19ad6cf608733c6554f01cf


---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>Développer des assemblys U-SQL pour des travaux Azure Data Lake Analytics
Apprenez à convertir des fichiers code-behind and assemblys pour les utiliser et les réutiliser dans des travaux Data Lake Analytics. 

U-SQL permet d’ajouter facilement votre propre code personnalisé dans les langages .Net, tels que C#, VB.Net ou F#. Vous pouvez même déployer votre propre runtime pour prendre en charge d’autres langages.

Le moyen le plus simple d’utiliser un code personnalisé consiste à se servir des outils Data Lake pour les capacités de code-behind de Visual Studio. Pour plus d’informations, consultez le [didacticiel : Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Il existe quelques inconvénients à l’utilisation de code-behind :

- Le code source est chargé à chaque envoi de script.
- Le fichier code-behind ne peut pas être partagé avec d’autres tâches.

Pour remédier à ces inconvénients, vous pouvez convertir le code-behind en assemblys et inscrire les assemblys dans le catalogue Data Lake Analytics.

## <a name="prerequisites"></a>Composants requis
* Visual Studio 2015, Visual Studio 2013 mise à jour 4 ou Visual Studio 2012 avec Visual C++.
* Kit de développement logiciel (SDK) Microsoft Azure pour .NET version 2.5 ou ultérieure.  Installez-le avec Web Platform Installer.
* Un compte Data Lake Analytics.  Consultez l’article [Prise en main d’Azure Data Lake Analytics à l’aide du Portail Azure](data-lake-analytics-get-started-portal.md).
* Parcourez le didacticiel [Prise en main d’Azure Data Lake Analytics avec U-SQL Studio](data-lake-analytics-u-sql-get-started.md) .
* Connexion à Azure.
* Téléchargez la source de données, consultez [Prise en main d’Azure Data Lake Analytics avec U-SQL Studio](data-lake-analytics-u-sql-get-started.md). 

## <a name="develop-assemblies-for-u-sql"></a>Développement d’assemblys pour U-SQL

**Pour créer et soumettre un travail U-SQL**

1. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
2. Développez **Installé**, **Modèles**, **Azure Data Lake**, **U-SQL (ADLA)**, sélectionnez le modèle **Bibliothèque de classes (pour application U-SQL)**, puis cliquez sur **OK**.
3. Écrivez votre code dans le fichier Class1.cs.  Voici un exemple de code.

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. Cliquez sur le menu **Générer**, puis sur **Générer la solution** pour créer la dll.

## <a name="register-assemblies"></a>Inscription d’assemblys

Consultez [Utilisation du catalogue Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md).


## <a name="use-the-assemblies"></a>Utilisation des assemblys

Consultez la page [Utilisation d’Azure Data Lake Tools pour Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

## <a name="see-also"></a>Voir aussi
* [Prise en main de Data Lake Analytics à l’aide de PowerShell](data-lake-analytics-get-started-powershell.md)
* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Utiliser les outils Data Lake pour Visual Studio pour le développement d’applications U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilisation du catalogue Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
* [Utilisation d’Azure Data Lake Tools pour Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)


<!--HONumber=Dec16_HO2-->


