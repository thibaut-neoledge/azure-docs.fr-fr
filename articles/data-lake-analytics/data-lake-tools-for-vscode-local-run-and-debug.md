---
title: "Azure Data Lake Tools : exécution locale et débogage local U-SQL avec Visual Studio Code | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code pour l’exécution locale et le débogage local."
Keywords: "VScode,Azure Data Lake Tools,exécution locale,débogage local,Débogage local,aperçu du fichier de stockage,charger vers le chemin de stockage"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.openlocfilehash: 367e4ba792f83d6ee246208306e4c09b69cb49ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Exécution locale et débogage local U-SQL avec Visual Studio Code

## <a name="prerequisites"></a>Composants requis
Vérifiez que les prérequis suivants sont remplis avant de démarrer ces procédures :
- Azure Data Lake Tools pour Visual Studio Code. Pour les instructions, consultez [Utilisation d’Azure Data Lake Tools pour Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- C# pour Visual Studio Code (si vous souhaitez effectuer un débogage local U-SQL).

   ![Installer C# dans Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > Les fonctionnalités d’exécution et de débogage U-SQL en local ne prennent en charge que les utilisateurs Windows. 


## <a name="set-up-the-u-sql-local-run-environment"></a>Configurer l’environnement d’exécution locale U-SQL

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes, puis entrez **ADL: Download LocalRun Dependency** pour télécharger les packages.  

   ![Télécharger les packages ADL LocalRun Dependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Recherchez les packages de dépendance à partir du chemin indiqué dans le volet **Sortie**, puis installez BuildTools et Win10SDK 10240. Voici un exemple de chemin :  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![Rechercher les packages de dépendance](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   a. Pour installer BuildTools, suivez les instructions de l’assistant.   

  ![Installer BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   b. Pour installer Win10SDK 10240, suivez les instructions de l’assistant.  

  ![Installer Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Définissez la variable d’environnement. Définissez la variable d’environnement **SCOPE_CPP_SDK** sur :  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
4. Redémarrez le système d’exploitation pour que les paramètres de la variable d’environnement prennent effet.  

   ![Vérifier que la variable d’environnement SCOPE_CPP_SDK est installée](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Démarrer le service d’exécution locale et envoyer le travail U-SQL à un compte local 
Les nouveaux utilisateurs sont invités à télécharger les packages ADL: Download LocalRun Dependency s’ils ne sont pas déjà installés.
1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes, puis entrez **ADL: Start Local Run Service**.
2. Sélectionnez **Accepter** pour accepter les termes du contrat de licence du logiciel Microsoft pour la première fois. 

   ![Accepter les termes du contrat de licence du logiciel Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. La console cmd s’ouvre. Les nouveaux utilisateurs doivent entrer **3**, puis rechercher le chemin de dossier local pour l’entrée et la sortie des données. Pour les autres options, vous pouvez utiliser les valeurs par défaut. 

   ![Data Lake Tools pour Visual Studio Code - cmd exécution locale](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes et entrez **ADL: Submit Job**, puis sélectionnez **Local** pour envoyer le travail à votre compte local.

   ![Data Lake Tools pour Visual Studio Code - Sélectionner Local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Après avoir envoyé le travail, vous pouvez afficher les détails de l’envoi. Pour afficher les détails de l’envoi, sélectionnez **jobUrl** dans la fenêtre **Sortie**. Vous pouvez également afficher l’état de l’envoi du travail à partir de la console cmd. Entrez **7** dans la console cmd si vous souhaitez connaître plus de détails sur le travail.

   ![Data Lake Tools pour Visual Studio Code - Sortie exécution locale](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools pour Visual Studio Code - État cmd exécution locale](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Démarrer un débogage local pour le travail U-SQL  
Les nouveaux utilisateurs sont invités à télécharger les packages ADL: Download LocalRun Dependency s’ils ne sont pas déjà installés.
  
1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes, puis entrez **ADL: Start Local Run Service**. La console cmd s’ouvre. Vérifiez que la valeur **DataRoot** est définie.
3. Définissez un point d’arrêt dans votre code C# code-behind.
4. Dans l’éditeur de script, sélectionnez Ctrl+Maj+P pour ouvrir la console de commandes, puis entrez **Local Debug** pour démarrer votre service de débogage local.

![Data Lake Tools pour Visual Studio Code - Résultat débogage local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Étapes suivantes
- Pour utiliser Azure Data Lake Tools pour Visual Studio Code, consultez [Utilisation d’Azure Data Lake Tools pour Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- Consultez [Didacticiel : Bien démarrer avec Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md) pour obtenir des informations sur la prise en main de Data Lake Analytics.
- Pour obtenir des informations sur l’utilisation de Data Lake Tools pour Visual Studio, consultez [Didacticiel : développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour plus d’informations sur le développement d’assemblys, consultez [Développement d’assemblys U-SQL pour les tâches d’Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).
