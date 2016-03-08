<properties
    pageTitle="Mon premier runbook PowerShell Workflow dans Azure Automation | Microsoft Azure"
    description="Ce didacticiel présente les procédures de création, de test et de publication d’un runbook textuel simple à l’aide de PowerShell Workflow."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren"/>

# Mon premier runbook PowerShell Workflow

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell Workflow](automation-first-runbook-textual.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md)

Ce didacticiel décrit la création d’un [Runbook PowerShell Workflow](automation-runbook-types.md#powerShell-workflow-runbooks) dans Azure Automation. Nous commencerons par un simple Runbook que nous testerons et publierons tout en expliquant comment suivre l'état de la tâche du Runbook. Puis nous modifierons le Runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, nous renforcerons le Runbook en ajoutant des paramètres de Runbook.

## Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-	abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous <a href="/pricing/free-trial/" target="_blank">[inscrire pour un essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
-	Un [compte Automation](automation-configuring.md) pour y stocker le Runbook.
-	Une machine virtuelle Azure. Nous arrêterons et démarrerons cet ordinateur afin qu'il ne soit pas en production.
-	[Un nom d’utilisateur Azure AD et une ressource d’informations d’identification Automation](automation-configuring.md) pour l’authentification auprès des ressources Azure. Cet utilisateur doit avoir l'autorisation de démarrer et arrêter la machine virtuelle.

## Étape 1 - Création d’un Runbook

Nous allons commencer par créer un Runbook simple qui renvoie le texte *Hello World*.

1.	Dans le portail Azure, ouvrez votre compte Automation. La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments multimédias. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).
2.	Cliquez sur la vignette **Runbooks** pour ouvrir la liste des Runbooks.<br> ![Contrôle des Runbooks](media/automation-first-runbook-textual/runbooks-control.png)
3.	Créez un Runbook en cliquant sur le bouton **Ajouter un Runbook**, puis sur **Créer un Runbook**.
4.	Nommez le runbook *MyFirstRunbook-Workflow*.
5.	Dans ce cas précis, nous allons créer un [Runbook PowerShell Workflow](automation-runbook-types.md#powerShell-workflow-runbooks). Par conséquent, sélectionnez **Powershell Workflow** dans le champ **Type de runbook**.<br>![Nouveau Runbook](media/automation-first-runbook-textual/new-runbook.png)
6.	Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## Étape 2 - Ajouter du code au Runbook

Vous pouvez soit taper du code directement dans le Runbook, soit sélectionner des applets de commande, des Runbooks et des ressources à partir du contrôle Bibliothèque et les ajouter au Runbook avec tous les paramètres associés. Pour cette procédure pas à pas, nous taperons directement le code dans le Runbook.

1.	Pour l’instant, notre Runbook est vide, à l’exception du mot clé requis *workflow*, du nom de notre Runbook et des accolades qui entoureront la totalité du workflow. <br> ![Contrôle des Runbooks](media/automation-first-runbook-textual/empty-runbook.png)
2.	Tapez *Write-Output « Hello World. »* entre les accolades. <br> ![Hello world](media/automation-first-runbook-textual/hello-world.png)
3.	Enregistrez le Runbook en cliquant sur **Enregistrer**.<br> ![Enregistrer un Runbook](media/automation-first-runbook-textual/runbook-edit-toolbar-save.png)

## Étape 3 : Test du Runbook

Avant publier le Runbook pour le rendre disponible en production, nous voulons le tester pour s’assurer qu'il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1.	Cliquez sur **Volet de test** pour ouvrir le volet de test.<br> ![Volet de test](media/automation-first-runbook-textual/runbook-edit-toolbar-test-pane.png)
2.	Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3.	Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît. L’état initial de la tâche est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  
4.	Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans notre cas, nous devrions voir *Hello World*.<br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
5.	Fermez le volet de test pour revenir au canevas.

## Étape 4 : Publication et démarrage du Runbook

Le Runbook que nous venons de créer est toujours en mode brouillon. Nous devons le publier pour pouvoir l'exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans notre cas, nous n'avons pas encore de version publiée car nous venons de créer le Runbook.

1.	Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** lorsque vous y êtes invité.<br> ![Publier](media/automation-first-runbook-textual/runbook-edit-toolbar-publish.png)
2.	Si vous faites défiler la page vers la gauche pour visualiser le volet **Runbooks**, celui-ci affichera l’**État de création** **Publié**.
3.	Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook-Workflow**. Les options de la partie supérieure nous permettent de démarrer le Runbook, de planifier son démarrage à un moment ultérieur ou de créer un [Webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
4.	Étant donné que nous voulons simplement démarrer le Runbook, cliquez sur **Démarrer**, puis sur **Oui** lorsque vous y êtes invité.<br> ![Démarrer un Runbook](media/automation-first-runbook-textual/runbook-toolbar-start.png)
5.	Un volet de tâche est ouvert pour la tâche du Runbook que nous venons de créer. Nous pouvons fermer ce volet mais, dans ce cas, nous le laisserons ouvert afin de suivre la progression de la tâche.
6.	L’état de la tâche est indiqué dans le champ **Résumé de la tâche** et correspond aux états que nous avons constatés lors du test du Runbook.<br> ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-summary.png)
7.	Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. Le volet Sortie s’ouvre, affichant *Hello World*.<br>![Résumé des tâches](media/automation-first-runbook-textual/job-pane-output.png)  
8.	Fermez le volet Sortie.
9.	Cliquez sur **Flux** pour ouvrir le volet Flux de la tâche du runbook. Seul le message *Hello World* devrait apparaître dans le flux de sortie, mais d’autres flux peuvent s’afficher pour une tâche de runbook, notamment Mode détaillé et Erreur si le runbook consigne ces informations.<br> ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-streams.png)
10.	Fermez les volets du flux et de la tâche pour revenir au volet MyFirstRunbook.
11.	Cliquez sur **Tâches** pour ouvrir le volet Tâches pour ce runbook. Il répertorie toutes les tâches créées par ce Runbook. Une seule tâche doit apparaître, car nous n’avons exécuté la tâche qu’une seule fois.<br> ![Travaux](media/automation-first-runbook-textual/runbook-control-jobs.png)
12.	Vous pouvez cliquer sur cette tâche pour ouvrir le volet de la tâche que nous avons consulté au démarrage du Runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## Étape 5 : Ajout d’une authentification pour gérer les ressources Azure

Nous avons testé et publié notre Runbook, mais jusqu'à présent, il ne fait rien d'utile. Nous voulons qu’il gère les ressources Azure. Il ne peut le faire que si nous le configurons pour qu’il s’authentifie à l’aide des informations d’identification mentionnées dans les [conditions préalables](#prerequisites). Nous utilisons pour cela l’applet de commande **Add-AzureAccount**.

1.	Ouvrez l’éditeur textuel en cliquant sur **Modifier** dans le volet MyFirstRunbook-Workflow.<br> ![Modifier un Runbook](media/automation-first-runbook-textual/runbook-toolbar-edit.png)
2.	La ligne **Write-Output** ne nous est plus utile. Vous pouvez donc la supprimer.
3.	Positionnez le curseur sur une ligne vide entre les accolades.
4.	Dans le contrôle Bibliothèque, développez **Actifs**, puis **Informations d’identification**.
5.	Cliquez avec le bouton droit sur vos informations d’identification, puis cliquez sur **Ajouter au canevas**. Cette opération ajoute une activité **Get-AutomationPSCredential** pour vos informations d’identification.
6.	Devant la chaîne **Get-AutomationPSCredential**, tapez *$Credential =* pour affecter les informations d’identification à une variable.
7.	Dans la ligne suivante, tapez *Add-AzureAccount -Credential $Credential*. <br> ![Authentifier](media/automation-first-runbook-textual/authentication.png)
8.	Cliquez sur **Volet de test** afin de tester le Runbook.
9.	Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vous devriez obtenir une sortie semblable à celle illustrée ci-dessous et renvoyant les informations de l’utilisateur dans les informations d’identification. Cette sortie confirme la validité des informations d’identification.<br>![Authentifier](media/automation-first-runbook-textual/authentication-test.png)

## Étape 6 - Ajouter du code pour démarrer une machine virtuelle

À présent que notre Runbook s’authentifie auprès de notre abonnement Azure, nous pouvons gérer les ressources. Nous allons ajouter une commande pour démarrer une machine virtuelle. Vous pouvez choisir n'importe quelle machine virtuelle dans votre abonnement Azure, et pour l'instant, nous allons coder ce nom dans l'applet de commande.

1.	Après *Add-AzureAccount*, tapez *Start-AzureVM -Name ’NomVM’ -ServiceName ’NomServiceVM’* en fournissant le nom et le nom de service de la machine virtuelle à démarrer. <br> ![Authentifier](media/automation-first-runbook-textual/start-azurevm.png)
2.	Enregistrez le runbook, puis cliquez sur **Volet de test** pour le tester.
3.	Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

## Étape 7 : Ajout d’un paramètre d'entrée au Runbook

Pour l’instant, notre Runbook démarre la machine virtuelle que nous avons codée en dur dans le Runbook, mais ce dernier serait plus utile si nous pouvions spécifier la machine virtuelle lorsque le Runbook est démarré. Nous allons à présent ajouter des paramètres d’entrée au Runbook pour fournir cette fonctionnalité.

1.	Ajoutez des paramètres au Runbook pour *VMName* et *VMServiceName* et utilisez ces variables avec l’applet de commande **Start-AzureVM** comme illustré dans l’image suivante. <br> ![Authentifier](media/automation-first-runbook-textual/params.png)
2.	Enregistrez le Runbook et ouvrez le volet de test. Notez que vous pouvez désormais fournir des valeurs pour les deux variables d'entrée qui seront utilisées dans le test.
3.	Fermez le volet de test.
4.	Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
5.	Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
6.	Cliquez sur **Démarrer** pour démarrer le Runbook. Entrez les valeurs **VMName** et **VMServiceName** pour la machine virtuelle que vous allez démarrer.<br> ![Démarrage du runbook](media/automation-first-runbook-textual/start-runbook-input-params.png)

7.	Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.

## Articles connexes

-	[Mon premier Runbook graphique](automation-first-runbook-graphical.md)
-	[Mon premier Runbook PowerShell](automation-first-runbook-textual-PowerShell.md)

<!---HONumber=AcomDC_0302_2016-->