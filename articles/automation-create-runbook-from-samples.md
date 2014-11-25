<properties linkid="automation-create-runbook-from-samples" urlDisplayName="Get Started with Azure Automation" pageTitle="Get Started with Azure Automation" metaKeywords="" description="Learn how to import and run an automation job in Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# Prise en main d'Azure Automation

L'automatisation Microsoft Azure permet aux développeurs d'automatiser les tâches répétitives, manuelles, de longue durée et susceptibles d'engendrer des erreurs qui sont couramment exécutées dans un environnement cloud. Vous pouvez créer, surveiller, gérer et déployer des ressources dans votre environnement Azure en utilisant des runbooks, qui sont finalement des workflows Windows PowerShell. Pour en savoir plus sur Automation, consultez le [Guide de présentation d'Automation][Guide de présentation d'Automation].

Ce didacticiel va vous guider lors de l'importation d'un exemple de runbook « HelloWorld » dans Azure Automation, puis lors de son exécution et de l'affichage de sa sortie.

> [WACOM.NOTE] To learn how to automate Azure operations using the [Azure PowerShell cmdlets][Azure PowerShell cmdlets] see [Azure Automation: Authenticating to Azure using Azure Active Directory][Azure Automation: Authenticating to Azure using Azure Active Directory].

## Exemples et runbooks utilitaires

L'équipe Azure Automation a créé un certain nombre d'exemples de runbooks pour vous aider à démarrer avec Automation. Ils couvrent différents concepts élémentaires d'Automation et ont été conçus pour vous apprendre à écrire vos propres runbooks.

L'équipe Automation a également créé des runbooks utilitaires qui peuvent vous servir de blocs fonctionnels pour des tâches Automation plus importantes.

> [WACOM.NOTE] Il est d'usage d'écrire de petits runbooks, modulaires et réutilisables. Nous vous recommandons également, lorsque vous connaissez suffisamment Automation, de créer vos propres runbooks utilitaires pour les scénarios courants.

Vous pouvez afficher et télécharger les exemples et les runbooks utilitaires de l'équipe Automation sur le [Centre de scripts][Centre de scripts].

## Communauté Automation et commentaires

Les runbooks de la communauté et d'autres équipes Microsoft sont également publiés sur le [Centre de scripts][1].

**Envoyez-nous vos commentaires !** Si vous recherchez une solution de runbook ou un module d'intégration Automation, envoyez une demande de script au Centre de scripts. Si vous avez une idée de nouvelle fonctionnalité pour Automation, envoyez-la sur le site [User Voice][User Voice].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Étapes générales pour ce didacticiel

1.  [Inscription à la version préliminaire d'Automation][Inscription à la version préliminaire d'Automation]
2.  [Téléchargement d'un exemple de runbook][Téléchargement d'un exemple de runbook]
3.  [Importation, exécution et affichage de la sortie de l'exemple de runbook][Importation, exécution et affichage de la sortie de l'exemple de runbook]

## <a name="preview"></a>Inscription à la version préliminaire d'Azure Automation

Pour commencer à utiliser Automation, vous aurez besoin d'un abonnement Azure actif, pour lequel la fonctionnalité préliminaire Microsoft Azure Automation est activée.

-   Sur la page **Fonctionnalités préliminaires**, cliquez sur **try it now**.

    ![Activer la fonctionnalité préliminaire][Activer la fonctionnalité préliminaire]

## <a name="download-sample"></a>Téléchargement d'un exemple de runbook sur le Centre de scripts

1.  Accédez au [Centre de scripts][Centre de scripts] et cliquez sur **Hello World for Azure Automation**.

2.  Cliquez sur le nom de fichier **Write-HelloWorld.ps1**, en regard de **Download**, et enregistrez le fichier sur l'ordinateur.

## <a name="import-sample"></a>Importation, exécution et affichage de la sortie de l'exemple de runbook dans Azure Automation

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Dans le portail de gestion, cliquez sur **Create an Automation Account**.

    > [WACOM.NOTE] If you’ve already created an automation account, you can skip to step 4.

    ![Créer un compte][Créer un compte]

3.  Sur la page **Add a New Automation Account**, entrez un nom pour le compte et cliquez sur la coche.

    ![Ajouter un nouveau compte][Ajouter un nouveau compte]

4.  Sur la page **Automation**, cliquez sur le compte que vous venez de créer.

    ![Nouveau compte][Nouveau compte]

5.  Cliquez sur **RUNBOOKS**.

    ![Onglet Runbooks][Onglet Runbooks]

6.  Cliquez sur **IMPORT**.

    ![Importer][Importer]

7.  Accédez au script **Write-HelloWorld.ps1** que vous avez téléchargé et cliquez sur la coche.

    ![Naviguer][Naviguer]

8.  Cliquez sur **Write-HelloWorld**.

    ![Runbook importé][Runbook importé]

9.  Cliquez sur **AUTHOR**, puis sur **DRAFT**. Vous n'avez aucune modification à apporter à ce runbook.

    Vous pouvez voir le contenu de **Write-HelloWorld.ps1**. Vous pouvez modifier le contenu d'un runbook en mode Brouillon.

    ![Créer un brouillon][Créer un brouillon]

10. Cliquez sur **PUBLISH** pour promouvoir le runbook afin qu'il soit prêt à être utilisé.

    ![Publier][Publier]

11. Lorsque vous êtes invité à enregistrer et publier le runbook, cliquez sur **Yes**.

    ![Invite d'enregistrement et de publication][Invite d'enregistrement et de publication]

12. Cliquez sur **PUBLISHED**, puis sur **START**.

    ![Publié][Publié]

13. Sur la page **Specify the runbook parameter values**, tapez un **nom** qui servira de paramètre d'entrée pour le script Write-HelloWorld.ps1 et cliquez sur la coche.

    ![Paramètres du runbook][Paramètres du runbook]

14. Cliquez sur **JOBS** pour vérifier l'état de la tâche de runbook que vous venez de commencer, et cliquez sur l'horodatage dans la colonne **JOB START** pour afficher le résumé de la tâche.

    ![État du runbook][État du runbook]

15. Sur la page **SUMMARY** sont affichés le résumé, les paramètres d'entrée et la sortie de la tâche.

    ![Résumé du runbook][Résumé du runbook]

# Gestion des services Azure à partir d'un runbook

L'exemple ci-dessus affiche un simple runbook qui ne gère pas les services Azure. Les [cmdlets Azure][Azure PowerShell cmdlets] nécessitent une authentification auprès d'Azure. Vous pouvez suivre les instructions données dans [Azure Automation : authentification auprès d'Azure à l'aide d'Azure Active Directory][Azure Automation: Authenticating to Azure using Azure Active Directory] afin de configurer la gestion de votre abonnement Azure par le biais d'Azure Automation.

# Voir aussi

-   [Vue d'ensemble d'Automation][Vue d'ensemble d'Automation]
-   [Guide de création du runbook][Guide de création du runbook]
-   [Forum Automation][Forum Automation]
-   [Azure Automation : authentification auprès d'Azure à l'aide d'Azure Active Directory][Azure Automation: Authenticating to Azure using Azure Active Directory]

  [Guide de présentation d'Automation]: http://go.microsoft.com/fwlink/p/?LinkId=392861
  [Azure PowerShell cmdlets]: http://msdn.microsoft.com/fr-fr/library/jj156055.aspx
  [Azure Automation: Authenticating to Azure using Azure Active Directory]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [Centre de scripts]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [User Voice]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [Inscription à la version préliminaire d'Automation]: #preview
  [Téléchargement d'un exemple de runbook]: #download-sample
  [Importation, exécution et affichage de la sortie de l'exemple de runbook]: #import-sample
  [Activer la fonctionnalité préliminaire]: ./media/automation/automation_00_EnablePreview.png
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Créer un compte]: ./media/automation/automation_01_CreateAccount.png
  [Ajouter un nouveau compte]: ./media/automation/automation_02_addnewautoacct.png
  [Nouveau compte]: ./media/automation/automation_03_NewAutoAcct.png
  [Onglet Runbooks]: ./media/automation/automation_04_RunbooksTab.png
  [Importer]: ./media/automation/automation_05_Import.png
  [Naviguer]: ./media/automation/automation_06_Browse.png
  [Runbook importé]: ./media/automation/automation_07_ImportedRunbook.png
  [Créer un brouillon]: ./media/automation/automation_08_AuthorDraft.png
  [Publier]: ./media/automation/automation_085_Publish.png
  [Invite d'enregistrement et de publication]: ./media/automation/automation_09_SavePubPrompt.png
  [Publié]: ./media/automation/automation_10_PublishStart.png
  [Paramètres du runbook]: ./media/automation/automation_11_RunbookParams.png
  [État du runbook]: ./media/automation/automation_12_RunbookStatus.png
  [Résumé du runbook]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [Vue d'ensemble d'Automation]: http://go.microsoft.com/fwlink/p/?LinkId=392860
  [Guide de création du runbook]: http://go.microsoft.com/fwlink/p/?LinkID=301740
  [Forum Automation]: http://go.microsoft.com/fwlink/p/?LinkId=390561
