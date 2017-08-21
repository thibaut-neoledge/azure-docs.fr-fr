---
title: "Azure Data Lake Tools - Exécution locale et débogage local U-SQL avec Visual Studio Code | Microsoft Docs"
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
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: e5e767a6f31be052c9bae059b49b31d59fdfd894
ms.contentlocale: fr-fr
ms.lasthandoff: 07/17/2017

---

# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Exécution locale et débogage local U-SQL avec Visual Studio Code

## <a name="prerequisites"></a>Composants requis
- Azure Data Lake Tools pour Visual Studio Code. Pour les instructions, consultez [Utilisation d’Azure Data Lake Tools pour Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
- C# pour Visual Studio Code (si vous souhaitez effectuer le débogage local U-SQL).

   ![Data Lake Tools pour Visual Studio Code - Installer vscodeCsharp](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > Les fonctionnalités d’exécution et de débogage U-SQL en local ne prennent actuellement en charge que les utilisateurs Windows. 


## <a name="set-up-u-sql-local-run-environment"></a>Configurer un environnement d’exécution locale U-SQL

1. Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**, puis entrez **ADL: Download LocalRun Dependency** pour télécharger les packages.  

   ![DownloadLocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Recherchez les packages de dépendance à partir du chemin d’accès indiqué dans le volet de sortie ci-dessous, puis installez BuildTools et Win10SDK 10240. Par exemple :  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![LocateDependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

- Installer BuildTools : suivez les instructions de l’assistant d’installation.   

  ![InstallBuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)
- Installer Win10SDK 10240 : suivez les instructions d’installation.  

  ![InstallWin10SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)
3. Définissez la variable d’environnement **SCOPE_CPP_SDK** sur :  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
Veillez à redémarrer le système d’exploitation pour que les paramètres de la variable d’environnement prennent effet.  

   ![ConfigSCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-local-run-service-and-submit-u-sql-job-to-local-account"></a>Démarrer le service d’exécution locale et envoyer un travail U-SQL au compte local 
Pour les nouveaux utilisateurs, l’invite **ADL: Download Localrun Dependency** s’affiche.
1. Appuyez sur **CTRL+MAJ+P** pour ouvrir la palette de commandes et entrez **ADL: Start Local Run Service**.
2. Acceptez le CLUF pour la première fois. 

   ![Accepter le CLUF ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. La console cmd s’affiche. Les nouveaux utilisateurs doivent entrer 3, puis un chemin de dossier local pour l’entrée et la sortie des données. Pour les autres options, vous pouvez simplement utiliser la valeur par défaut. 

   ![Data Lake Tools pour Visual Studio Code - cmd exécution locale](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Appuyez sur CTRL+MAJ+P pour ouvrir la palette de commandes et entrez **ADL: Submit Job**, puis sélectionnez **Local** pour envoyer le travail à votre compte local.

   ![Data Lake Tools pour Visual Studio Code - Sélectionner Local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Après l’envoi du travail, les détails s’affichent lorsque vous cliquez sur l’URL du travail dans la fenêtre de sortie. Vous pouvez également afficher l’état de l’envoi du travail à partir de la console CMD, entrez 7 dans la console CMD pour afficher plus de détails sur le travail.

   ![Data Lake Tools pour Visual Studio Code - Sortie exécution locale](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools pour Visual Studio Code - État cmd exécution locale](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 



## <a name="start-local-debug-for-u-sql-job"></a>Démarrer le débogage local pour le travail U-SQL  
Pour les nouveaux utilisateurs, l’invite **ADL: Download Localrun Dependency** s’affiche.
  
1. Appuyez sur **CTRL+MAJ+P** pour ouvrir la palette de commandes et entrez **ADL: Start Local Run Service**. La fenêtre cmd s’affiche. Vérifiez que la valeur **DataRoot** est définie.
3. Définissez un point d’arrêt dans votre code C# code-behind.
4. Dans l’éditeur de script, appuyez sur **CTRL+MAJ+P** pour ouvrir la palette de commandes et entrez **Local Debug** pour démarrer votre service de débogage local.

![Data Lake Tools pour Visual Studio Code - Résultat débogage local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Étapes suivantes
- Pour utiliser Azure Data Lake Tools pour Visual Studio Code, consultez [Utilisation d’Azure Data Lake Tools pour Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- Consultez [Didacticiel : bien démarrer avec Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md) pour obtenir des informations sur la prise en main de Data Lake Analytics.
- Consultez [Didacticiel : développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) pour obtenir des informations sur l’utilisation de Data Lake Tools pour Visual Studio.
- Pour plus d’informations sur le développement d’assemblys, consultez [Développement d’assemblys U-SQL pour les tâches d’Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).
