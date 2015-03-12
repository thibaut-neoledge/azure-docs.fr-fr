<properties 
	pageTitle="Prise en main d'Azure Automation" 
	description="Découvrez comment importer et exécuter une tâche d'automatisation dans Azure." 
	services="automation" 
	documentationCenter="" 
	authors="bwren" 
	manager="stevenka" 
	editor=""/>

<tags 
	ms.service="automation" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/13/2015" 
	ms.author="bwren"/>


# Prise en main d'Azure Automation

L'automatisation Microsoft Azure permet aux développeurs d'automatiser les tâches répétitives, manuelles, de longue durée et susceptibles d'engendrer des erreurs qui sont couramment exécutées dans un environnement cloud. Vous pouvez créer, surveiller, gérer et déployer des ressources dans votre environnement Azure en utilisant des runbooks, qui sont finalement des workflows Windows PowerShell. Pour en savoir plus sur Automation, consultez le [Guide de présentation d'Automation](http://go.microsoft.com/fwlink/p/?LinkId=392861). 

Ce didacticiel va vous guider lors de l'importation d'un exemple de runbook " HelloWorld " dans Azure Automation, puis lors de son exécution et de l'affichage de sa sortie.

>[WACOM.NOTE] Pour apprendre à automatiser les opérations Azure à l'aide des [applets de commande Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) consultez <a href="http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/">Azure Automation : authentification auprès d'Azure à l'aide d'Azure Active Directory</a>.

## Exemples et runbooks utilitaires

L'équipe Azure Automation a créé un certain nombre d'exemples de runbooks pour vous aider à démarrer avec Automation.  Ils couvrent différents concepts élémentaires d'Automation et ont été conçus pour vous apprendre à écrire vos propres runbooks.  

L'équipe Automation a également créé des runbooks utilitaires qui peuvent vous servir de blocs fonctionnels pour des tâches Automation plus importantes.  

>[WACOM.NOTE] Il est d'usage d'écrire de petits Runbooks, modulaires et réutilisables. Nous vous recommandons également, lorsque vous connaissez suffisamment Automation, de créer vos propres runbooks utilitaires pour les scénarios courants.  

Vous pouvez afficher et télécharger les exemples et les Runbooks utilitaires de l'équipe Automation sur le [Centre de scripts](http://go.microsoft.com/fwlink/p/?LinkId=393029), ou les importer directement depuis la [galerie de Runbooks](http://aka.ms/runbookgallery). 

## Communauté Automation et commentaires

Les Runbooks de la communauté et d'autres équipes Microsoft sont également publiés sur le [Centre de scripts](http://go.microsoft.com/fwlink/?LinkID=391681) et dans la [galerie de Runbooks](http://aka.ms/runbookgallery). 

<strong>Envoyez-nous vos commentaires !</strong>  Si vous recherchez une solution de Runbook ou un module d'intégration Automation, envoyez une demande de script au Centre de scripts. Si vous avez une idée de nouvelle fonctionnalité pour Automation, envoyez-la sur le site [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback).

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Étapes générales pour ce didacticiel

1. [Création d'un compte Automation](#automationaccount)
2. [Importation d'un Runbook à partir de la galerie de Runbooks](#importrunbook)
3. [Publication du Runbook](#publishrunbook)
4. [Démarrage du Runbook](#startrunbook)


## <a name="automationaccount"></a>Création d'un compte Automation

1.	Connectez-vous au [Portail de gestion Azure](http://manage.windowsazure.com).

2.	Dans le Portail de gestion, cliquez sur **Créer un compte Automation**.

	>[WACOM.NOTE] Si vous avez déjà créé un compte Automation, passez à l'étape 4.

	![Create Account](./media/automation/automation_01_CreateAccount.png)

3.	Dans la page **Ajouter un nouveau compte Automation**, entrez un nom pour le compte, puis cliquez sur la coche.

	![Add New Account](./media/automation/automation_02_addnewautoacct.png)

## <a name="importrunbook"></a>Importation d'un runbook à partir de la galerie de runbooks
 
4.	Dans la page **Automation**, cliquez sur le compte que vous venez de créer.
 
	![New Account](./media/automation/automation_03_NewAutoAcct.png)

5.	Cliquez sur **RUNBOOKS**.

	![Runbooks Tab](./media/automation/automation_04_RunbooksTab.png)
  
6.	Cliquez sur **Nouveau** > **Runbook** > **À partir de la galerie**.

	![Runbook Gallery](./media/automation/automation_05_ImportGallery.png)

7.  Sélectionnez la catégorie **Didacticiel**, puis **Hello World pour Azure Automation**. Cliquez sur le bouton avec la flèche vers la droite.

	![Import Runbook](./media/automation/automation_06_ImportRunbook.png)

8.  Relisez le contenu du runbook, puis cliquez sur le bouton avec la flèche vers la droite.

	![Runbook Definition](./media/automation/automation_07_RunbookDefinition.png)

8.	Relisez les détails du runbook, puis cliquez sur le bouton avec la coche.

	![Runbook Details](./media/automation/automation_08_RunbookDetails.png)

## <a name="publishrunbook"></a>Publication du runbook 

9.	Une fois le Runbook importé, cliquez sur **Write-HelloWorld**.

	![Imported Runbook](./media/automation/automation_07_ImportedRunbook.png)

9.	Cliquez sur **AUTEUR**, puis sur **BROUILLON**.  

	Vous pouvez modifier le contenu d'un runbook en mode Brouillon. Vous n'avez aucune modification à apporter à ce runbook.

	![Author Draft](./media/automation/automation_08_AuthorDraft.png)  
 
10.	Cliquez sur **PUBLIER** pour promouvoir le Runbook afin qu'il soit prêt à être utilisé.

	![Publish](./media/automation/automation_085_Publish.png)
   
11.	Quand vous êtes invité à enregistrer et publier le Runbook, cliquez sur **Oui**.
 
	![Save and Pub prompt](./media/automation/automation_09_SavePubPrompt.png)

## <a name="startrunbook"></a>Démarrage du runbook

12.	Une fois le Runbook **Write-HelloWorld** ouvert, cliquez sur **DÉMARRER**.

	![Published](./media/automation/automation_10_PublishStart.png)
 
13.	Dans la page **Spécifier les valeurs de paramètre de Runbook**, tapez un **nom** qui servira de paramètre d'entrée pour le script Write-HelloWorld.ps1 et cliquez sur la coche.

	![Runbook Parameters](./media/automation/automation_11_RunbookParams.png)
  
14.	Cliquez sur **TÂCHES** pour vérifier l'état de la tâche de Runbook que vous venez de commencer, puis cliquez sur l'horodatage dans la colonne **DÉBUT DE LA TÂCHE** pour afficher le résumé de la tâche.

	![Runbook Status](./media/automation/automation_12_RunbookStatus.png)

15.	Dans la page **RÉSUMÉ** sont affichés le résumé, les paramètres d'entrée et la sortie de la tâche.
 
	![Runbook Summary](./media/automation/automation_13_RunbookSummary_callouts.png)


# Gestion des services Azure à partir d'un runbook 
L'exemple ci-dessus affiche un simple runbook qui ne gère pas les services Azure. Les [applets de commande Azure](http://msdn.microsoft.com/library/jj156055.aspx) nécessitent une authentification auprès d'Azure. Vous pouvez suivre les instructions données dans [Azure Automation : authentification auprès d'Azure à l'aide d'Azure Active Directory](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/) pour configurer la gestion de votre abonnement Azure par le biais d'Azure Automation.

# Voir aussi

- [Vue d'ensemble d'Automation](http://go.microsoft.com/fwlink/p/?LinkId=392860)
- [Guide de création du Runbook](http://go.microsoft.com/fwlink/p/?LinkID=301740)
- [Forum Automation](http://go.microsoft.com/fwlink/p/?LinkId=390561)
- [Azure Automation : authentification auprès d'Azure à l'aide d'Azure Active Directory](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)

<!--HONumber=35.2-->

<!--HONumber=46--> 
