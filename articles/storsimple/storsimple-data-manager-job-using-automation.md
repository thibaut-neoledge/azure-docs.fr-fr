---
title: "Utiliser Azure Automation pour déclencher un travail | Microsoft Docs"
description: "Découvrez comment utiliser Azure Automation pour déclencher des travaux StorSimple Data Manager (version préliminaire privée)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.contentlocale: fr-fr
ms.lasthandoff: 03/21/2017

---

# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>Utiliser Azure Automation pour déclencher un travail (version préliminaire privée)

Cet article explique comment utiliser Azure Automation pour déclencher un travail StorSimple Data Manager.

## <a name="prerequisites"></a>Composants requis

Avant de commencer, assurez-vous de satisfaire les exigences suivantes :

*   Azure Powershell installé. [Téléchargez Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Les paramètres de configuration pour initialiser le travail de transformation des données (les instructions pour obtenir ces paramètres sont disponibles ici).
*   Une définition de travail qui a été configurée correctement dans une ressource de données hybride dans un groupe de ressources.
*   Téléchargez le fichier [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) `DataTransformationApp.zip` à partir du référentiel github.
*   Téléchargez le [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1) `Get-ConfigurationParams.ps1` à partir du référentiel github.
*   Téléchargez le [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) `Trigger-DataTransformation-Job.ps1` à partir du référentiel github.

## <a name="step-by-step"></a>Procédure pas à pas

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>Obtenir les autorisations Azure Active Directory pour que le travail Automation exécute la définition de travail

1. Pour récupérer les paramètres de configuration pour Active Directory, procédez comme suit :

    1. Ouvrez Windows PowerShell sur votre ordinateur local. Vérifiez que [Azure PowerShell](https://azure.microsoft.com/downloads/) est installé.
    1. Exécutez le script `Get-ConfigurationParams.ps1` (dans le dossier téléchargé ci-dessus). Entrez la commande suivante dans la fenêtre PowerShell :

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        La clé ActiveDirectoryKey correspond à un mot de passe que vous utiliserez plus tard. Entrez un mot de passe de votre choix. N’importe quelle chaîne peut être utilisée pour AppName.

2. Ce script génère les valeurs suivantes qui doivent être utilisés lors du déclenchement du runbook Automation. Prenez note de ces valeurs.

    - ID client
    - ID client
    - Clé Active Directory (identique à celle entrée ci-dessus)
    - Identifiant d’abonnement

### <a name="set-up-the-automation-account"></a>Configurer le compte Automation

1. Connectez-vous à Azure et ouvrez votre compte Automation.
2. Cliquez sur la vignette **Ressources** pour ouvrir la liste des ressources.
3. Cliquez sur la vignette **Modules** pour ouvrir la liste des modules.
4. Cliquez sur le bouton **+ Ajouter un module** et le panneau Ajouter un module s’affiche.

    ![Paramètres du compte Automation](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. Après avoir sélectionné le fichier `DataTransformationApp.zip` sur votre ordinateur local, cliquez sur **OK** pour importer le module.

   Quand Azure Automation importe un module dans votre compte, il extrait les métadonnées sur le module. Cette opération peut prendre plusieurs minutes.

   ![Paramètres du compte Automation](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. Vous recevez une notification indiquant que le module est en cours de déploiement et une autre notification lorsque le processus est terminé.  Vous pouvez également vérifier l’état dans la vignette **Modules**.

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>Pour importer le runbook qui déclenche la définition de travail

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Cliquez sur **+ Ajouter un runbook**, puis sur **Importer un Runbook existant**.

   ![Importer un runbook existant](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. Cliquez sur **Fichier runbook** et sélectionnez le fichier à importer `Trigger-DataTransformation-Job.ps1`.
5. Cliquez sur **Créer** pour importer le runbook. Le nouveau runbook apparaît dans la liste des runbooks pour le compte Automation.
7. Cliquez sur le runbook **Trigger-DataTransformation-Job**, puis sur **Modifier**.
8. Cliquez sur **Publier**, puis sur **Oui** lorsque vous êtes invité à confirmer l’opération.


### <a name="to-run-the-runbook"></a>Pour exécuter le runbook :
1. Dans le portail Azure, ouvrez votre compte Automation.
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Cliquez sur **Trigger-DataTransformation-Job**.
4. Cliquez sur **Démarrer** pour démarrer le Runbook.

   ![Démarrer un Runbook](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. Dans le panneau **Démarrer le runbook**, entrez tous les paramètres. Cliquez sur **OK** pour envoyer le travail Transformation des données.

   ![Démarrer un Runbook](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>Étapes suivantes

[Utilisez l’interface utilisateur de StorSimple Data Manager pour transformer vos données](storsimple-data-manager-ui.md).
