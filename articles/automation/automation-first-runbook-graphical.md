<properties
    pageTitle="Mon premier Runbook graphique dans Azure Automation | Microsoft Azure"
    description="Ce didacticiel vous familiarise avec la création, le test et la publication d'un Runbook graphique simple."
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

# Mon premier Runbook graphique

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell Workflow](automation-first-runbook-textual.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md)

Ce didacticiel vous familiarise avec la création d’un [Runbook graphique](automation-runbook-types.md#graphical-runbooks) dans Azure Automation. Nous commencerons par un simple Runbook que nous testerons et publierons tout en expliquant comment suivre l'état de la tâche du Runbook. Puis nous modifierons le Runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Puis nous renforcerons le Runbook en ajoutant des paramètres de Runbook et un lien conditionnel.

## Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-	abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous <a href="/pricing/free-trial/" target="_blank">[inscrire pour un essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
-	Un [compte Automation](automation-configuring.md) pour y stocker le Runbook.
-	Une machine virtuelle Azure. Nous arrêterons et démarrerons cet ordinateur afin qu'il ne soit pas en production.
-	[Un nom d’utilisateur Azure AD et une ressource d’informations d’identification Automation](automation-configuring.md) pour l’authentification auprès des ressources Azure. Cet utilisateur doit avoir l'autorisation de démarrer et arrêter la machine virtuelle.

## Étape 1 - Création d’un Runbook

Nous allons commencer par créer un Runbook simple qui renvoie le texte *Hello World*.

1.	Dans le portail Azure, ouvrez votre compte Automation. La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments multimédias. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).
2.	Cliquez sur la vignette **Runbooks** pour ouvrir la liste des Runbooks.<br> ![Contrôle des Runbooks](media/automation-first-runbook-graphical/runbooks-control.png)
3.	Créez un Runbook en cliquant sur le bouton **Ajouter un Runbook** puis**Créer un Runbook**.
4.	Nommez le Runbook *MyFirstRunbook-Graphical*.
5.	Dans ce cas précis, nous allons créer un [Runbook graphique](automation-graphical-authoring-intro.md) ; par conséquent, sélectionnez **Graphique** dans le champ **Type de Runbook**.<br> ![Nouveau Runbook](media/automation-first-runbook-graphical/new-runbook.png)
6.	Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur graphique.

## Étape 2 : Ajout d’activités au Runbook

Le contrôle Bibliothèque à gauche de l'éditeur vous permet de sélectionner des activités à ajouter à votre Runbook. Nous allons ajouter une applet de commande **Write-Output** pour extraire notre texte du Runbook.

1.	Dans le contrôle Bibliothèque, développez le nœud **Applets de commande**, puis **Microsoft.PowerShell.Utility**.<br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/cmdlets-powershell-utility.png)
2.	Faites défiler jusqu'au bas de la liste. Cliquez avec le bouton droit sur **Write-Output**, puis cliquez sur **Ajouter au canevas**.
3.	Cliquez sur l’activité **Write-Output** sur le canevas. Le contrôle Configuration qui vous permet de configurer l'activité s'ouvre.
4.	Par défaut, le champ **Étiquette** affiche le nom de l’applet de commande, mais nous pouvons le modifier pour le rendre plus convivial. Remplacez-le par *Write Hello World to output*.
5.	Cliquez sur **Paramètres** pour renseigner les paramètres de l’applet de commande. Certaines applets de commande comportent plusieurs jeux de paramètres, et vous devez sélectionner celle que vous utiliserez. Dans ce cas précis, **Write-Output** ne comporte qu’un seul paramètre défini ; vous n’avez donc pas besoin d’en sélectionner un. <br> ![Propriétés Write-Output](media/automation-first-runbook-graphical/write-output-properties.png)
6.	Sélectionnez le paramètre **InputObject**. Il s'agit du paramètre où nous spécifierons le texte à envoyer au flux de sortie.
7.	Dans le menu déroulant **Source de données**, sélectionnez **Expression PowerShell**. Le menu déroulant **Source de données** fournit différentes sources que vous utilisez pour renseigner un paramètre. Vous pouvez utiliser la sortie de ces sources, par exemple une autre activité, un élément multimédia Automation ou une expression PowerShell. Dans notre cas, nous voulons simplement extraire le texte *Hello World*. Nous pouvons utiliser une expression PowerShell et spécifiez une chaîne.
8.	Dans le champ **Expression**, tapez *« Hello World »*, puis cliquez deux fois sur **OK** pour revenir au canevas.<br> ![Expression PowerShell](media/automation-first-runbook-graphical/expression-hello-world.png)
9.	Enregistrez le Runbook en cliquant sur **Enregistrer**.<br> ![Enregistrer un Runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-save.png)

## Étape 3 : Test du Runbook

Avant publier le Runbook pour le rendre disponible en production, nous voulons le tester pour s’assurer qu'il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1.	Cliquez sur **Volet de test** pour ouvrir le volet de test.<br> ![Volet de test](media/automation-first-runbook-graphical/runbook-edit-toolbar-test-pane.png)
2.	Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3.	Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît dans le volet. L’état initial de la tâche est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  
4.	Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans notre cas, nous devrions voir *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/test-output-hello-world.png)
5.	Fermez le volet de test pour revenir au canevas.

## Étape 4 : Publication et démarrage du Runbook

Le Runbook que nous venons de créer est toujours en mode brouillon. Nous devons le publier pour pouvoir l'exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans notre cas, nous n'avons pas encore de version publiée car nous venons de créer le Runbook.

1.	Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** lorsque vous y êtes invité.<br> ![Publier](media/automation-first-runbook-graphical/runbook-edit-toolbar-publish.png)
2.	Si vous faites défiler la page vers la gauche pour visualiser le volet **Runbooks**, celui-ci affichera l’**État de création** **Publié**.
3.	Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook**. Les options de la partie supérieure nous permettent de démarrer le Runbook, de planifier son démarrage à un moment ultérieur ou de créer un [Webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
4.	Étant donné que nous voulons simplement démarrer le Runbook, cliquez sur **Démarrer**, puis sur **Oui** lorsque vous y êtes invité.<br> ![Démarrer un Runbook](media/automation-first-runbook-graphical/runbook-toolbar-start.png)
5.	Un volet de tâche est ouvert pour la tâche du Runbook que nous venons de créer. Nous pouvons fermer ce volet mais, dans ce cas, nous le laisserons ouvert afin de suivre la progression de la tâche.
6.	L’état de la tâche est indiqué dans le champ **Résumé de la tâche** et correspond aux états que nous avons constatés lors du test du Runbook.<br> ![Résumé des tâches](media/automation-first-runbook-graphical/job-pane-summary.png)
7.	Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. Le volet **Sortie** s’ouvre et nous présente notre message *Hello World*.<br> ![Résumé des tâches](media/automation-first-runbook-graphical/job-pane-output.png)  
8.	Fermez le volet Sortie.
9.	Cliquez sur **Flux** pour ouvrir le volet Flux de la tâche du runbook. Seul le message *Hello World* devrait apparaître dans le flux de sortie, mais d’autres flux peuvent s’afficher pour une tâche de runbook, notamment Mode détaillé et Erreur si le runbook consigne ces informations.<br> ![Résumé des tâches](media/automation-first-runbook-graphical/job-pane-streams.png)
10.	Fermez les volets du flux et de la tâche pour revenir au volet MyFirstRunbook.
11.	Cliquez sur **Tâches** pour ouvrir le volet Tâches pour ce runbook. Il répertorie toutes les tâches créées par ce Runbook. Une seule tâche doit apparaître, car nous n’avons exécuté la tâche qu’une seule fois.<br> ![Travaux](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12.	Vous pouvez cliquer sur cette tâche pour ouvrir le volet de la tâche que nous avons consulté au démarrage du Runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## Étape 5 : Ajout d’une authentification pour gérer les ressources Azure

Nous avons testé et publié notre Runbook, mais jusqu'à présent, il ne fait rien d'utile. Nous voulons qu’il gère les ressources Azure. Il ne pourra le faire que si nous le configurons pour qu’il s’authentifie à l’aide des informations d’identification mentionnées dans les [composants requis](#prerequisites). Nous utilisons pour cela l’applet de commande **Set-AzureAccount**.

1.	Ouvrez l’éditeur graphique en cliquant sur **Modifier** dans le volet MyFirstRunbook.<br> ![Modifier un Runbook](media/automation-first-runbook-graphical/runbook-toolbar-edit.png)
2.	Étant donné que **Write Hello World to output** ne nous est plus utile, cliquez dessus avec le bouton droit et sélectionnez **Supprimer**.
3.	Dans le contrôle Bibliothèque, développez **Applets de commande**, puis **Azure**.
4.	Ajoutez **Add-AzureAccount** au canevas.<br> ![Add-AzureAccount](media/automation-first-runbook-graphical/add-azureaccount.png)
5.	Sélectionnez **Add-AzureAccount**, puis cliquez sur **Paramètres** dans le volet Configuration.
6.	**Add-AzureAccount** comporte plusieurs jeux de paramètres et vous devez donc en sélectionner un pour fournir les valeurs de paramètre. Cliquez sur **Jeu de paramètres**, puis sélectionnez le jeu de paramètres **Utilisateur**.
7.	Une fois que vous sélectionnez le jeu de paramètres, les paramètres apparaissent dans le volet de configuration des paramètres d'activité. Cliquez sur **Informations d’identification**.<br> ![Ajouter des paramètres de compte Azure](media/automation-first-runbook-graphical/add-azureaccount-parameters.png)
8.	Comme nous voulons que cette applet de commande utilise une ressource d’informations d’identification dans notre compte Automation, sélectionnez **Ressource d’informations d’identification** dans le champ **Source de données**.
9.	Sélectionnez la ressource d’informations d’identification autorisée à démarrer et à arrêter une machine virtuelle dans votre environnement Azure.<br> ![Ajouter une source de données de compte Azure](media/automation-first-runbook-graphical/credential-data-source.png)
10.	Cliquez deux fois sur **OK** pour revenir au canevas.

## Étape 6 : Ajout d’une activité pour démarrer une machine virtuelle

Nous allons maintenant ajouter une activité **Start-AzureVM** pour démarrer une machine virtuelle. Vous pouvez choisir n'importe quelle machine virtuelle dans votre abonnement Azure, et pour l'instant, nous allons coder ce nom dans l'applet de commande.

1.	Développez **Applets de commande**, puis **Azure**.
2.	Ajoutez **Start-AzureVM** au canevas, puis faites-le glisser sous **Add-AzureAccount**.
3.	Pointez sur **Add-AzureAccount** jusqu’à ce qu’un cercle apparaisse au bas de la forme. Cliquez sur le cercle, faites glisser la flèche vers **Start-AzureVM**. La flèche que vous venez de créer est un *lien*. Le Runbook commencera par **Add-AzureAccount**, puis exécutera **Start-AzureVM**.<br> ![Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm.png)
4.	Sélectionnez **Start-AzureVM**. Cliquez sur **Paramètres**, puis sur **Jeu de paramètres** afin d’afficher les jeux pour **Start-AzureVM**. Sélectionnez le jeu de paramètres **ByName**. Notez que des points d’exclamation apparaissent en regard des paramètres **Name** et **ServiceName**. Ils indiquent que ces paramètres sont obligatoires.
5.	Sélectionnez **Name**. Utilisez **Valeur constante** comme **Source de données**, puis tapez le nom de la machine virtuelle à démarrer. Cliquez sur **OK**.
6.	Sélectionnez **ServiceName**. Utilisez **Valeur constante** comme **source de données** puis entrez le nom de la machine virtuelle pour commencer. Cliquez sur **OK**.<br> ![Paramètres Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params.png)
7.	Cliquez sur le volet de test afin de tester le Runbook.
8.	Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

## Étape 7 : Ajout d’un paramètre d'entrée au Runbook

Notre Runbook démarre actuellement la machine virtuelle que nous avons spécifiée dans l’applet de commande **Start-AzureVM**, mais notre Runbook serait plus utile si nous pouvions spécifier la machine virtuelle lorsque le Runbook est démarré. Nous allons maintenant ajouter un paramètre d'entrée au Runbook pour fournir cette fonctionnalité.

1.	Ouvrez l’éditeur graphique en cliquant sur **Modifier** dans le volet **MyFirstRunbook**.
2.	Cliquez sur **Entrée et sortie**, puis sur **Ajouter une entrée** pour ouvrir le volet Paramètre d’entrée de Runbook.<br> ![Entrée et sortie de Runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-inputoutput.png)
3.	Spécifiez *VMName* dans le champ **Nom**. Conservez la sélection *chaîne* pour le champ **Type**, mais définissez la valeur **Obligatoire** sur *Oui*. Cliquez sur **OK**.
4.	Créez un second paramètre d’entrée obligatoire appelé *VMServiceName*, puis cliquez sur **OK** pour fermer le volet **Entrée et sortie**.<br> ![Paramètres d'entrée de Runbook](media/automation-first-runbook-graphical/input-parameters.png)
5.	Sélectionnez l’activité **Start-AzureVM**, puis cliquez sur **Paramètres**.
6.	Définissez le champ **Source de données** du paramètre **Name** sur **Entrée de Runbook**, puis sélectionnez **VMName**.<br> ![Paramètre de nom Start-AzureVM](media/automation-first-runbook-graphical/vmname-property.png)
7.	Définissez le champ **Source de données** du paramètre **ServiceName** sur **Entrée de Runbook**, puis sélectionnez **VMServiceName**.<br> ![Paramètres Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params-inputs.png)
8.	Enregistrez le Runbook et ouvrez le volet de test. Notez que vous pouvez désormais fournir des valeurs pour les deux variables d'entrée qui seront utilisées dans le test.
9.	Fermez le volet de test.
10.	Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
11.	Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
12.	Cliquez sur **Démarrer** pour démarrer le Runbook. Entrez les valeurs **VMName** et **VMServiceName** pour la machine virtuelle que vous allez démarrer.<br> ![Démarrage du runbook](media/automation-first-runbook-graphical/start-runbook-input-params.png)
13.	Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.

## Étape 8 : Création d’un lien conditionnel

Nous allons maintenant modifier le Runbook afin qu'il tente de démarrer uniquement s'il n'est pas déjà démarré. Nous effectuerons cette opération en ajoutant une applet de commande **Get-AzureVM** au Runbook qui inclura l’état actuel de la machine virtuelle. Nous ajouterons ensuite un lien conditionnel qui n’exécutera **Start-AzureVM** que si l’état d’exécution actuel est arrêté. Si le Runbook n'est pas arrêté, un message s’affiche.

1.	Ouvrez **MyFirstRunbook** dans l’éditeur graphique.
2.	Supprimer le lien entre **Add-AzureAccount** et **Start-AzureVM** en cliquant dessus puis en appuyant sur la touche *Supprimer*.
3.	Dans le contrôle Bibliothèque, développez **Applets de commande**, puis **Azure**.
4.	Ajoutez **Get-AzureVM** au canevas.
5.	Créez un lien entre **Add-AzureAccount** et **Get-AzureVM**. Créez un autre lien entre **Get-AzureVM** et **Start-AzureVM**.<br> ![Runbook avec Get-AzureVM](media/automation-first-runbook-graphical/get-azurevm.png)  
6.	Sélectionnez **Get-AzureVM**, puis cliquez sur **Paramètres**. Sélectionnez le jeu de paramètres *GetVMByServiceAndVMName*.
7.	Pour **Name**, définissez le champ **Source de données** sur **Entrée de Runbook**, puis sélectionnez **VMName**.  
8.	Pour **ServiceName**, définissez la **source de données** sur **Entrée de Runbook** puis sélectionnez **VMServiceName**.  
9.	Sélectionnez le lien entre **Get-AzureVM** et **Start-AzureVM**.
10.	Dans le volet Configuration, définissez **Appliquer la condition** sur **Vrai**. Notez que le lien se transforme en une ligne pointillée indiquant que l'activité cible s’exécutera uniquement si la condition est définie sur true.
11.	Dans la zone **Expression de condition**, tapez *$ActivityOutput[’Get-AzureVM’].PowerState -eq "Stopped"*. **Start-AzureVM** s’exécutera uniquement si la machine virtuelle est arrêtée.<br> ![Condition de liaison](media/automation-first-runbook-graphical/link-condition.png)
12.	Dans le contrôle Bibliothèque, développez **Applets de commande**, puis **Microsoft.PowerShell.Utility**.
13.	Ajoutez **Write-Output** au canevas.
14.	Créez un lien entre **Get-AzureVM** et **Write-Output**.
15.	Sélectionnez le lien et définissez **Appliquer la condition** sur **Vrai**.
16.	Dans la zone **Expression de condition**, tapez *$ActivityOutput[’Get-AzureVM’].PowerState -ne "Stopped"*. **Write-Output** ne s’exécutera que si la machine virtuelle n’est pas arrêtée.<br> ![Runbook avec Write-Output](media/automation-first-runbook-graphical/write-output-link.png)
17.	Sélectionnez **Write-Output**, puis cliquez sur **Paramètres**.
18.	Pour **InputObject**, définissez le champ **Source de données** sur **Expression PowerShell**, puis tapez l’expression *"$VMName.Name déjà arrêtée."*.
19.	Enregistrez le Runbook et ouvrez le volet de test.
20.	Démarrez le Runbook avec la machine virtuelle arrêtée, et il devrait démarrer.
21.	Démarrez le Runbook avec la machine virtuelle démarrée, et un message devrait indiquer qu'il est déjà démarré.

## Articles connexes

-	[Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md)
-	[Mon premier Runbook de workflow PowerShell](automation-first-runbook-textual.md)
-	[Mon premier Runbook PowerShell](automation-first-runbook-textual-PowerShell.md)

<!---HONumber=AcomDC_0302_2016-->