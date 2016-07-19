<properties
    pageTitle="Mon premier Runbook PowerShell dans Azure Automation | Microsoft Azure"
    description="Ce didacticiel vous familiarise avec la création, le test et la publication d’un Runbook Powershell simple."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="azure powershell, didacticiel sur le script powershell, automatisation powershell"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/02/2016"
    ms.author="magoedte;sngun"/>

# Mon premier Runbook PowerShell

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [PowerShell Workflow](automation-first-runbook-textual.md)

Ce didacticiel vous guide dans la création d’un [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) dans Azure Automation. Nous commencerons par un simple Runbook que nous testerons et publierons tout en expliquant comment suivre l'état de la tâche du Runbook. Puis nous modifierons le Runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, nous renforcerons le Runbook en ajoutant des paramètres de Runbook.

## Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-	abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-account/" target="_blank">[créer un compte gratuit](https://azure.microsoft.com/free/).
-	Un [compte Automation](automation-security-overview.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure. Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
-	Une machine virtuelle Azure. Nous arrêterons et démarrerons cet ordinateur afin qu'il ne soit pas en production.

## Étape 1 - Création d’un Runbook

Nous allons commencer par créer un Runbook simple qui renvoie le texte *Hello World*.

1.	Dans le portail Azure, ouvrez votre compte Automation. La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments multimédias. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).
2.	Cliquez sur la vignette **Runbooks** pour ouvrir la liste des Runbooks. ![RunbooksControl](media/automation-first-runbook-textual-powershell/automation-runbooks-control.png)
3.	Créez un Runbook en cliquant sur le bouton **Ajouter un Runbook**, puis sur **Créer un Runbook**.
4.	Nommez le Runbook *MyFirstRunbook-PowerShell*.
5.	Dans ce cas précis, nous allons créer un [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks). Par conséquent, sélectionnez **Powershell ** comme **Type de Runbook**.![Types de Runbook](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)
6.	Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## Étape 2 - Ajouter du code au Runbook

Vous pouvez soit taper du code directement dans le Runbook, soit sélectionner des applets de commande, des Runbooks et des ressources à partir du contrôle Bibliothèque et les ajouter au Runbook avec tous les paramètres associés. Pour cette procédure pas à pas, nous taperons directement le code dans le Runbook.

1.	Notre Runbook est actuellement vide, saisissez *Write-Output « Hello World ».*. ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)
2.	Enregistrez le Runbook en cliquant sur **Enregistrer**. ![Bouton Enregistrer](media/automation-first-runbook-textual-powershell/automation-save-button.png)

## Étape 3 : Test du Runbook

Avant publier le Runbook pour le rendre disponible en production, nous voulons le tester pour s’assurer qu'il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1.	Cliquez sur **Volet de test** pour ouvrir le volet de test. ![Volet Test](media/automation-first-runbook-textual-powershell/automation-testpane.png)
2.	Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3.	Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît. L’état initial de la tâche est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.
4.	Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans notre cas, nous devrions voir *Hello World* ![Résultat du volet de test](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)
5.	Fermez le volet de test pour revenir au canevas.

## Étape 4 : Publication et démarrage du Runbook

Le Runbook que nous venons de créer est toujours en mode brouillon. Nous devons le publier pour pouvoir l'exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans notre cas, nous n'avons pas encore de version publiée car nous venons de créer le Runbook.

1.	Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** lorsque vous y êtes invité. ![Bouton Publier](media/automation-first-runbook-textual-powershell/automation-publish-button.png)
2.	Si vous faites défiler la page vers la gauche pour visualiser le volet **Runbooks**, celui-ci affichera l’**État de création** **Publié**.
3.	Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook-PowerShell**. Les options de la partie supérieure nous permettent de démarrer le Runbook, de l’afficher, de planifier son démarrage à un moment ultérieur ou de créer un [webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
4.	L’objectif est seulement de démarrer le Runbook. Cliquez sur **Démarrer**, puis sur **OK** lorsque le panneau Démarrer le Runbook s’ouvre. ![Bouton Démarrer](media/automation-first-runbook-textual-powershell/automation-start-button.png)
5.	Un volet de tâche est ouvert pour la tâche du Runbook que nous venons de créer. Nous pouvons fermer ce volet mais, dans ce cas, nous le laisserons ouvert afin de suivre la progression de la tâche.
6.	L’état de la tâche est indiqué dans le champ **Résumé de la tâche** et correspond aux états que nous avons constatés lors du test du Runbook. ![Résumé des tâches](media/automation-first-runbook-textual-powershell/automation-job-summary.png)
7.	Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. Le volet Sortie s’ouvre, affichant *Hello World*. ![Sortie de travail](media/automation-first-runbook-textual-powershell/automation-job-output.png)
8.	Fermez le volet Sortie.
9.	Cliquez sur **Tous les journaux** pour ouvrir le volet Flux de la tâche du Runbook. Seul le message *Hello World* devrait apparaître dans le flux de sortie, mais d’autres flux peuvent s’afficher pour une tâche de Runbook, notamment Mode détaillé et Erreur si le Runbook consigne ces informations. ![Tous les journaux](media/automation-first-runbook-textual-powershell/automation-alllogs.png)
10.	Fermez les volets du flux et de la tâche pour revenir au volet MyFirstRunbook PowerShell.
11.	Cliquez sur **Tâches** pour ouvrir le volet Tâches pour ce runbook. Il répertorie toutes les tâches créées par ce Runbook. Une seule tâche doit apparaître, car nous n’avons exécuté la tâche qu’une seule fois. ![Liste des postes](media/automation-first-runbook-textual-powershell/automation-job-list.png)
12.	Vous pouvez cliquer sur cette tâche pour ouvrir le volet de la tâche que nous avons consulté au démarrage du Runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## Étape 5 : Ajout d’une authentification pour gérer les ressources Azure

Nous avons testé et publié notre Runbook, mais jusqu'à présent, il ne fait rien d'utile. Nous voulons qu’il gère les ressources Azure. Il ne peut le faire que si nous le configurons pour qu’il s’authentifie à l’aide des informations d’identification mentionnées dans les [conditions préalables](#prerequisites). Nous utilisons pour cela l’applet de commande **Add-AzureRmAccount**.

1.	Ouvrez l’éditeur de texte en cliquant sur **Modifier** dans le volet MyFirstRunbook PowerShell. ![Modifier un Runbook](media/automation-first-runbook-textual-powershell/automation-edit-runbook.png)
2.	La ligne **Write-Output** ne nous est plus utile. Vous pouvez donc la supprimer.
3.	Tapez ou copiez-collez le code suivant qui gérera l’authentification avec votre compte d’authentification Automation :

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    ```
<br>
4.	Cliquez sur **Volet de test** afin de tester le Runbook.
5.	Cliquez sur **Démarrer** pour démarrer le test. Une fois terminé, la sortie générée devrait afficher les informations de base sur votre compte. Cette sortie confirme la validité des informations d’identification. <br> ![Authentifier](media/automation-first-runbook-textual-powershell/runbook-auth-results.png)

## Étape 6 - Ajouter du code pour démarrer une machine virtuelle

À présent que notre Runbook s’authentifie auprès de notre abonnement Azure, nous pouvons gérer les ressources. Nous allons ajouter une commande pour démarrer une machine virtuelle. Vous pouvez choisir n'importe quelle machine virtuelle dans votre abonnement Azure, et pour l'instant, nous allons coder ce nom dans l'applet de commande.

1.	Après *Add-AzureRmAccount*, tapez *Start-AzureRmVM -Name ’VMName’ -ResourceGroupName ’NameofResourceGroup’* en fournissant le nom et le nom de groupe de ressources de la machine virtuelle à démarrer.

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationID `$Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
     Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
     ```
<br>
2.	Enregistrez le runbook, puis cliquez sur **Volet de test** pour le tester.
3.	Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

## Étape 7 : Ajout d’un paramètre d'entrée au Runbook

Pour l’instant, notre Runbook démarre la machine virtuelle que nous avons codée en dur dans le Runbook, mais ce dernier serait plus utile si nous pouvions spécifier la machine virtuelle lorsque le Runbook est démarré. Nous allons à présent ajouter des paramètres d’entrée au Runbook pour fournir cette fonctionnalité.

1.	Ajoutez des paramètres au Runbook pour *VMName* et *ResourceGroupName* et utilisez ces variables avec l’applet de commande **Start-AzureRmVM** comme dans l’exemple ci-dessous.

    ```
    Param(
       [string]$VMName,
       [string]$ResourceGroupName
    )
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationID `$Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
     Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
     ```
<br>
2.	Enregistrez le Runbook et ouvrez le volet de test. Notez que vous pouvez désormais fournir des valeurs pour les deux variables d'entrée qui seront utilisées dans le test.
3.	Fermez le volet de test.
4.	Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
5.	Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
6.	Cliquez sur **Démarrer** pour démarrer le Runbook. Entrez les valeurs **VMName** et **ResourceGroupName** pour la machine virtuelle que vous allez démarrer. ![Paramètre de réussite](media/automation-first-runbook-textual-powershell/automation-pass-params.png)
7.	Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.

## Différences par rapport à PowerShell Workflow

Les Runbooks PowerShell ont les mêmes cycle de vie, fonctionnalités et mode de gestion que PowerShell Workflow, avec toutefois quelques différences et limitations :

1.	Les Runbooks PowerShell s’exécutent rapidement par rapport aux Runbooks PowerShell Workflow, car ils n’ont pas à subir l’étape de compilation.
2.	Les Runbooks PowerShell Workflow prennent en charge les points de contrôle. Avec les points de contrôle, les Runbooks PowerShell Workflow peuvent reprendre à n’importe quel point du Runbook, tandis que les Runbooks PowerShell ne peuvent recommencer qu’à partir du début.
3.	Les Runbooks PowerShell Workflow prennent en charge l’exécution parallèle et série, alors que les Runbooks PowerShell peuvent exécuter des commandes en série uniquement.
4.	Dans un Runbook PowerShell Workflow, une activité, une commande ou un bloc de script peut avoir sa propre instance d’exécution alors que dans un Runbook PowerShell, tous les éléments du script s’exécutent dans une instance d’exécution unique. Il existe également certaines [différences syntaxiques](https://technet.microsoft.com/magazine/dn151046.aspx) entre un Runbook PowerShell natif et un Runbook PowerShell Workflow.

## Étapes suivantes

-	Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
-	Pour une prise en main des Runbooks de workflow PowerShell, consultez [Mon premier Runbook PowerShell Workflow](automation-first-runbook-textual.md)
-	Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
-	Pour plus d’informations sur la fonctionnalité de prise en charge de script PowerShell, consultez le billet [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Prise en charge de script PowerShell natif dans Azure Automation)

<!---HONumber=AcomDC_0713_2016-->