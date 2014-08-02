<properties linkid="automation-create-runbook-from-samples" urlDisplayName="Get Started with Azure Automation" pageTitle="Get Started with Azure Automation" metaKeywords="" description="Learn how to import and run an automation job in Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="" solutions="" manager="" editor="" />

Prise en main d'Azure Automation
================================

Ce didacticiel va vous guider lors de l'importation et de l'exécution d'un runbook Automation dans Microsoft Azure.

L'automatisation Microsoft Azure permet aux développeurs d'automatiser les tâches répétitives, manuelles, de longue durée et susceptibles d'engendrer des erreurs qui sont couramment exécutées dans un environnement cloud. Vous pouvez créer, surveiller, gérer et déployer des ressources dans votre environnement Azure en utilisant des runbooks, qui sont finalement des workflows Windows PowerShell. Pour en savoir plus sur Automation, consultez le [Guide de présentation d'Automation](http://go.microsoft.com/fwlink/p/?LinkId=392861).

Ce didacticiel va vous guider lors de l'importation d'un exemple de runbook Automation dans Azure Automation, puis lors de son exécution et de l'affichage de sa sortie.

Exemples et runbooks utilitaires
--------------------------------

L'équipe Azure Automation a créé un certain nombre d'exemples de runbooks pour vous aider à démarrer avec Automation. Ils couvrent différents concepts élémentaires d'Automation et ont été conçus pour vous apprendre à écrire vos propres runbooks.

L'équipe Automation a également créé des runbooks utilitaires qui peuvent vous servir de blocs fonctionnels pour des tâches Automation plus importantes.

> [WACOM.NOTE] Il est d'usage d'écrire de petits runbooks, modulaires et réutilisables. Nous vous recommandons également, lorsque vous connaissez suffisamment Automation, de créer vos propres runbooks utilitaires pour les scénarios courants.

Vous pouvez afficher et télécharger les exemples et les runbooks utilitaires de l'équipe Automation sur le [Centre de scripts](http://go.microsoft.com/fwlink/p/?LinkId=393029).

Communauté Automation et commentaires
-------------------------------------

Les runbooks de la communauté et d'autres équipes Microsoft sont également publiés sur le [Centre de scripts](http://go.microsoft.com/fwlink/?LinkID=391681).

**Envoyez-nous vos commentaires !** Si vous recherchez une solution de runbook ou un module d'intégration PowerShell, envoyez une demande de script au Centre de scripts. Si vous avez une idée de nouvelle fonctionnalité pour Automation, envoyez-la sur le site [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback).

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Téléchargement d'un exemple de runbook sur le Centre de scripts
---------------------------------------------------------------

1.  Accédez au [Centre de scripts](http://go.microsoft.com/fwlink/p/?LinkId=393029) et cliquez sur **Hello World for Azure Automation**.

2.  Cliquez sur le nom de fichier **Write-HelloWorld.ps1**, en regard de **Download**, et enregistrez le fichier sur l'ordinateur.

Importation de l'exemple de runbook dans Azure
----------------------------------------------

1.  Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2.  Dans le portail de gestion, cliquez sur **Create an Automation Account**.

    > [WACOM.NOTE] Si vous avez déjà un compte Automation, passez à l'étape 4.

    ![Créer un compte](./media/automation/automation_01_CreateAccount.png)

3.  Sur la page **Add a New Automation Account**, entrez un nom pour le compte et cliquez sur la coche.

    ![Ajouter un nouveau compte](./media/automation/automation_02_addnewautoacct.png)

4.  Sur la page **Automation**, cliquez sur le compte que vous venez de créer.

    ![Nouveau compte](./media/automation/automation_03_NewAutoAcct.png)

5.  Cliquez sur **RUNBOOKS**.

    ![Onglet Runbooks](./media/automation/automation_04_RunbooksTab.png)

6.  Cliquez sur **IMPORT**.

    ![Importer](./media/automation/automation_05_Import.png)

7.  Accédez au script **Write-HelloWorld.ps1** que vous avez téléchargé et cliquez sur la coche.

    ![Naviguer](./media/automation/automation_06_Browse.png)

8.  Cliquez sur **Write-HelloWorld**.

    ![Runbook importé](./media/automation/automation_07_ImportedRunbook.png)

9.  Cliquez sur **AUTHOR**, puis sur **DRAFT**. Vous n'avez aucune modification à apporter à ce runbook.

    Vous pouvez voir le contenu de **Write-HelloWorld.ps1**. Vous pouvez modifier le contenu d'un runbook en mode Brouillon.

    ![Créer un brouillon](./media/automation/automation_08_AuthorDraft.png)

10. Cliquez sur **PUBLISH** pour promouvoir le runbook afin qu'il soit prêt à être utilisé.

    ![Publier](./media/automation/automation_085_Publish.png)

11. Lorsque vous êtes invité à enregistrer et publier le runbook, cliquez sur **Yes**.

    ![Invite d'enregistrement et de publication](./media/automation/automation_09_SavePubPrompt.png)

12. Cliquez sur **PUBLISHED**, puis sur **START**.

    ![Publié](./media/automation/automation_10_PublishStart.png)

13. Sur la page **Specify the runbook parameter values**, tapez un **nom** qui servira de paramètre d'entrée pour le script Write-HelloWorld.ps1 et cliquez sur la coche.

    ![Paramètres du runbook](./media/automation/automation_11_RunbookParams.png)

14. Cliquez sur **JOBS** pour vérifier l'état de la tâche de runbook que vous venez de commencer, et cliquez sur l'horodatage dans la colonne **JOB START** pour afficher le résumé de la tâche.

    ![État du runbook](./media/automation/automation_12_RunbookStatus.png)

15. Sur la page **SUMMARY** sont affichés le résumé, les paramètres d'entrée et la sortie de la tâche.

    ![Résumé du runbook](./media/automation/automation_13_RunbookSummary_callouts.png)

Voir aussi
----------

-   [Vue d'ensemble d'Automation](http://go.microsoft.com/fwlink/p/?LinkId=392860)
-   [Guide de création du runbook](http://go.microsoft.com/fwlink/p/?LinkID=301740)
-   [Forum Automation](http://go.microsoft.com/fwlink/p/?LinkId=390561)

