<properties
	pageTitle="Mon premier Runbook graphique dans Azure Automation"
	description="Ce didacticiel vous familiarise avec la création, le test et la publication d'un Runbook graphique simple. Plusieurs concepts sont abordés, notamment l'authentification auprès de ressources Azure, les paramètres d'entrée et les liens conditionnels."
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
	ms.topic="get-started-article" 
	ms.date="07/03/2015"
	ms.author="bwren"/>


# Mon premier Runbook

Ce didacticiel vous familiarise avec la création d'un [Runbook graphique](automation-graphical-authoring-intro.md) dans Azure Automation. Nous commencerons par un simple Runbook que nous testerons et publierons tout en expliquant comment suivre l'état de la tâche du Runbook. Puis nous modifierons le Runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Puis nous renforcerons le Runbook en ajoutant des paramètres de Runbook et un lien conditionnel.

## Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

- abonnement Azure. Si vous n'avez pas encore d'abonnement, vous pouvez [activer vos avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous <a href="/pricing/free-trial/" target="_blank">[inscrire pour un essai gratuit](http://azure.microsoft.com/pricing/free-trial/).
- Un [compte Automation](automation-configuring.md) pour y stocker le Runbook.
- Une machine virtuelle Azure. Nous arrêterons et démarrerons cet ordinateur afin qu'il ne soit pas en production.
- [Un nom d’utilisateur Active Directory et des ressources d'informations d'identification](automation-configuring.md) pour s'authentifier auprès des ressources Azure. Cet utilisateur doit avoir l'autorisation de démarrer et arrêter la machine virtuelle.

## Étape 1 - Création d’un Runbook

Nous allons créer un Runbook simple qui renvoie le texte *Hello World*.

1. Dans le portail Azure en version préliminaire, ouvrez votre compte Automation. La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments multimédias. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également avoir la ressource d'identification mentionnée dans les [conditions préalables](#prerequisites).
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des Runbooks.<br> ![Contrôle des Runbooks](media/automation-first-runbook-graphical/runbooks-control.png)
2. Créez un Runbook en cliquant sur le bouton **Ajouter un Runbook** puis**Créer un Runbook**.
3. Nommez le Runbook *MyFirstRunbook-Graphical*.
4. Dans ce cas, nous allons créer un [Runbook graphique](automation-graphical-authoring-intro.md) ; sélectionnez **Graphique** comme **type de Runbook**.<br> ![Nouveau Runbook](media/automation-first-runbook-graphical/new-runbook.png)
5. Cliquez sur **Créer** pour créer le Runbook et ouvrez l'éditeur graphique.

## Étape 2 : Ajout d’activités au Runbook

Le contrôle Bibliothèque à gauche de l'éditeur vous permet de sélectionner des activités à ajouter à votre Runbook. Nous allons ajouter une applet de commande **Write-Output** pour extraire notre texte du Runbook.

1.   Dans le contrôle Bibliothèque, développez le nœud **Applets de commande**, puis **Microsoft.PowerShell.Utility**.<br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/cmdlets-powershell-utility.png)
2.   Faites défiler jusqu'au bas de la liste. Cliquez avec le bouton droit sur **Write-Output** puis sur **Ajouter au canevas**.
4.   Cliquez sur l’activité **Write-Output** sur le canevas. Le contrôle Configuration qui vous permet de configurer l'activité s'ouvre.
5.   Par défaut, le champ **Étiquette** affiche le nom de l'applet de commande, mais nous pouvons le modifier pour le rendre plus convivial. Remplacez-le par *Write Hello World to output*.
6.   Cliquez sur **Paramètres** pour fournir les valeurs des paramètres de l’applet de commande. Certaines applets de commande comportent plusieurs jeux de paramètres, et vous devez sélectionner celle que vous utiliserez. Dans ce cas, **Write-Output** ne comporte qu’un seul paramètre défini et vous n'avez donc pas besoin d’en sélectionner un. <br> ![Propriétés Write-Output](media/automation-first-runbook-graphical/write-output-properties.png)
7.   Sélectionnez le paramètre **InputObject**. Il s'agit du paramètre où nous spécifierons le texte à envoyer au flux de sortie.
9.   Dans le menu déroulant **Source de données**, sélectionnez **Expression PowerShell**. Le menu déroulant **Source de données** fournit différentes sources que vous utilisez pour remplir une valeur de paramètre. Vous pouvez utiliser la sortie de ces sources, par exemple une autre activité, un élément multimédia Automation ou une expression PowerShell. Dans ce cas, nous voulons simplement extraire le texte *Hello World*. Nous pouvons utiliser une expression PowerShell et spécifiez une chaîne.
10.   Dans le champ **Expression**, tapez *« Hello World »*, puis cliquez deux fois sur **OK** pour revenir au canevas.<br> ![Expression PowerShell](media/automation-first-runbook-graphical/expression-hello-world.png)
11.   Enregistrez le Runbook en cliquant sur **Enregistrer**.<br> ![Enregistrer un Runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-save.png)

## Étape 3 : Test du Runbook

Avant publier le Runbook pour le rendre disponible en production, nous voulons le tester pour s’assurer qu'il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **brouillon** et affichez sa sortie de manière interactive.
 
2. Cliquez sur **Volet de test** pour ouvrir le volet de test.<br> ![Volet de test](media/automation-first-runbook-graphical/runbook-edit-toolbar-test-pane.png)
2. Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3. Une [tâche de Runbook](automation-runbook-execution) est créée et son état apparaît dans le volet. L’état de la tâche commencera par *Mis en file d'attente* pour indiquer qu'il attend qu’un Runbook Worker dans le cloud soit disponible. Il passe ensuite à l’état *Démarrage en cours* lorsqu'un Worker sélectionne la tâche, puis à l’état *En cours d'exécution* lorsque le Runbook est réellement en cours d'exécution.  
4. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans notre cas, nous devrions voir *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/test-output-hello-world.png)
5. Fermez le volet de test pour revenir au canevas.


## Étape 4 : Publication et démarrage du Runbook

Le Runbook que nous venons de créer est toujours en mode brouillon. Nous devons le publier pour pouvoir l'exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans notre cas, nous n'avons pas encore de version publiée car nous venons de créer le Runbook.

1. Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** lorsque vous y êtes invité.<br> ![Publier](media/automation-first-runbook-graphical/runbook-edit-toolbar-publish.png)
2. Si vous faites défiler vers la gauche pour afficher le volet **Runbook**, celui-ci affichera l’**état de création** **Publié**.
3. Faites défiler vers la droite pour afficher le volet **MyFirstRunbook**. Les options dans la partie supérieure permettent de démarrer le Runbook, de planifier son démarrage à un moment ultérieur, ou de créer un [webhook](automation-webhooks.md) afin de le démarrer via un appel HTTP. 
4. Comme nous voulons simplement démarrer le Runbook, cliquez sur **Démarrer** puis sur **Oui** lorsque vous y êtes invité.<br> ![Démarrer un Runbook](media/automation-first-runbook-graphical/runbook-toolbar-start.png)
5. Un volet de tâche est ouvert pour la tâche du Runbook que nous venons de créer. Nous pouvons fermer ce volet mais, dans ce cas, nous le laisserons ouvert afin de suivre la progression de la tâche.
6.  L'état de la tâche est indiqué dans le champ **Résumé des tâches** et correspond aux états que nous avons constatés lors du test du Runbook.<br> ![Résumé des tâches](media/automation-first-runbook-graphical/job-pane-summary.png)
7.  Lorsque l'état du Runbook indique *Terminé*, cliquez sur **Sortie**. Le volet **Sortie** est ouvert, et nous pouvons voir notre message *Hello World*.<br> ![Résumé des tâches](media/automation-first-runbook-graphical/job-pane-output.png)  
8.  Fermez le volet Sortie.
9.  Cliquez sur **Flux** afin d’ouvrir le volet Flux de la tâche du Runbook. Seul le message *Hello World* devrait apparaître dans le flux de sortie, mais d’autres flux peuvent s’afficher pour une tâche de Runbook, notamment Verbose et Error si le Runbook consigne ces informations.<br> ![Résumé des tâches](media/automation-first-runbook-graphical/job-pane-streams.png) 
9. Fermez les volets du flux et de la tâche pour revenir au volet MyFirstRunbook.
9.  Cliquez sur **Tâches** afin d’ouvrir le volet des tâches pour ce Runbook. Il répertorie toutes les tâches créées par ce Runbook. Une seule tâche devrait apparaître car nous avons exécuté la tâche qu’une seule fois.<br> ![Travaux](media/automation-first-runbook-graphical/runbook-control-jobs.png) 
9. Vous pouvez cliquer sur cette tâche pour ouvrir le volet de la tâche que nous avons consulté au démarrage du Runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## Étape 5 : Ajout d’une authentification pour gérer les ressources Azure

Nous avons testé et publié notre Runbook, mais jusqu'à présent, il ne fait rien d'utile. Nous voulons qu’il gère les ressources Azure. Il ne pourra le faire que s’il est configuré pour authentifier les informations d'identification mentionnées dans les [conditions préalables](#prerequisites). Nous utilisons pour cela l’applet de commande **Set-AzureAccount**.

1.  Ouvrez l'éditeur graphique en cliquant sur **Modifier** dans le volet MyFirstRunbook.<br> ![Modifier un Runbook](media/automation-first-runbook-graphical/runbook-toolbar-edit.png) 
2.  Comme nous n'avons plus besoin de **Write Hello World to output**, nous cliquez avec le bouton droit dessus puis sélectionnons **Supprimer**.
8.  Dans le contrôle Bibliothèque, développez **Applets de commande** puis**Azure**. 
9.  Ajoutez **Add-AzureAccount** au canevas.<br> ![Add-AzureAccount](media/automation-first-runbook-graphical/add-azureaccount.png) 
12.  Sélectionnez **Add-AzureAccount** puis cliquez sur **Paramètres** dans le volet Configuration.
13.  **Add-AzureAccount** comporte plusieurs jeux de paramètres et vous devez donc en sélectionner un pour fournir les valeurs de paramètre. Cliquez sur **Jeu de paramètres** puis sélectionnez le jeu de paramètres **Utilisateur**.
14.  Une fois que vous sélectionnez le jeu de paramètres, les paramètres apparaissent dans le volet de configuration des paramètres d'activité. Cliquez sur **Informations d'identification**.<br> ![Ajouter des paramètres de compte Azure](media/automation-first-runbook-graphical/add-azureaccount-parameters.png)
15.  Comme nous voulons que cette applet de commande utilise une ressource d'informations d'identification dans notre compte Automation, sélectionnons **Ressource d’informations d’identification** comme **source de données**.
16.  Sélectionnez la ressource d'informations d'identification autorisée à démarrer et à arrêter une machine virtuelle dans votre environnement Azure.<br> ![Ajouter une source de données de compte Azure](media/automation-first-runbook-graphical/credential-data-source.png)
17.  Cliquez deux fois sur **OK** pour revenir au canevas.


## Étape 6 : Ajout d’une activité pour démarrer une machine virtuelle

Nous allons maintenant ajouter une activité **Start-AzureVM** pour démarrer une machine virtuelle. Vous pouvez choisir n'importe quelle machine virtuelle dans votre abonnement Azure, et pour l'instant, nous allons coder ce nom dans l'applet de commande.

1. Développez **Applets de commande** puis **Azure**.
2. Ajoutez **Start-AzureVM** au canevas, puis faites-le glisser sous **Add-AzureAccount**.
11.  Passez la souris sur **Add-AzureAccount** jusqu'à ce qu'un cercle apparaît au bas de la forme. Cliquez sur le cercle, faites glisser la flèche vers **Start-AzureVM**. La flèche que vous venez de créer est un *lien*. Le Runbook commencera par **Add-AzureAccount**, puis exécutera **Start-AzureVM**.<br> ![Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm.png)
5. Sélectionnez **Start-AzureVM**. Cliquez sur **Paramètres** puis sur **Jeu de paramètres** afin d’afficher les jeux pour **Start-AzureVM**. Sélectionnez le jeu de paramètres **ByName**. Notez que des points d’exclamation apparaissent en regard des champs **Name** et **ServiceName**. Ils indiquent que ces paramètres sont obligatoires.
7. Sélectionnez **Name**. Utilisez **Valeur constante** comme **source de données** puis entrez le nom de la machine virtuelle pour commencer. Cliquez sur **OK**.
8. Sélectionnez **ServiceName**. Utilisez **Valeur constante** comme **source de données** puis entrez le nom de la machine virtuelle pour commencer. Cliquez sur **OK**.<br> ![Paramètres Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params.png)
9. Cliquez sur le volet de test afin de tester le Runbook.
10. Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.


## Étape 7 : Ajout d’un paramètre d'entrée au Runbook

Notre Runbook démarre actuellement la machine virtuelle que nous avons spécifiée dans l’applet de commande **Start-AzureVM**, mais notre Runbook serait plus utile si nous pouvions spécifier la machine virtuelle lorsque le Runbook est démarré. Nous allons maintenant ajouter un paramètre d'entrée au Runbook pour fournir cette fonctionnalité.

1. Ouvrez l'éditeur graphique en cliquant sur **Modifier** dans le volet **MyFirstRunbook**.
2. Cliquez sur **Entrée et sortie**, puis sur **Ajouter une entrée** pour ouvrir le volet Paramètres d'entrée de Runbook.<br> ![Entrée et sortie de Runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-inputoutput.png)
4. Spécifiez *VMName* dans le champ **Nom**. Conservez *chaîne* comme **Type**, mais définissez la valeur **Obligatoire** sur *Oui*. Cliquez sur **OK**.
5. Créez un second paramètre d'entrée obligatoire appelé *VMServiceName* puis cliquez sur **OK** pour fermer le volet **Entrée et sortie**.<br> ![Paramètres d'entrée de Runbook](media/automation-first-runbook-graphical/input-parameters.png) 
6. Sélectionnez l’activité **Start-AzureVM**, puis cliquez sur **Paramètres**.
7. Définissez la **source de données** pour **Nom** sur **Entrée de Runbook**, puis sélectionnez **VMName**.<br> ![Paramètre de nom Start-AzureVM](media/automation-first-runbook-graphical/vmname-property.png) 
8. Définissez la **source de données** pour **ServiceName** sur **Entrée de Runbook**, puis sélectionnez **VMServiceName**.<br> ![Paramètres Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params-inputs.png) 
9. Enregistrez le Runbook et ouvrez le volet de test. Notez que vous pouvez désormais fournir des valeurs pour les deux variables d'entrée qui seront utilisées dans le test. 
11.  Fermez le volet de test.
12.  Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
13.  Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
13.  Cliquez sur **Démarrer** pour démarrer le Runbook. Entrez les valeurs **VMName** et **VMServiceName** pour la machine virtuelle que vous allez démarrer.<br> ![Démarrage du runbook](media/automation-first-runbook-graphical/start-runbook-input-params.png) 
14.  Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.

## Étape 8 : Création d’un lien conditionnel

Nous allons maintenant modifier le Runbook afin qu'il tente de démarrer uniquement s'il n'est pas déjà démarré. Nous verrons comment procéder en ajoutant une applet de commande **Get-AzureVM** qui inclura l'état actuel de la machine virtuelle. Nous ajouterons ensuite un lien conditionnel qui exécutera **Start-AzureVM** uniquement si l'état en cours d'exécution est arrêté. Si le Runbook n'est pas arrêté, un message s’affiche.

1. Ouvrez **MyFirstRunbook** dans l'éditeur graphique.
2. Supprimer le lien entre **Add-AzureAccount** et **Start-AzureVM** en cliquant dessus puis en appuyant sur la touche *Supprimer*.
3. Dans le contrôle Bibliothèque, développez **Applets de commande** puis**Azure**.
4. Ajoutez **Get-AzureVM** au canevas.
5. Créez un lien entre **Add-AzureAccount** et **Get-AzureVM**. Créez un autre lien entre **Get-AzureVM** et **Start-AzureVM**.<br> ![Runbook avec Get-AzureVM](media/automation-first-runbook-graphical/get-azurevm.png)   
6.  Sélectionnez **Get-AzureVM** puis cliquez sur **paramètres**. Sélectionnez le jeu de paramètres *GetVMByServiceAndVMName*.
7.  Pour **Nom**, définissez la **source de données** sur **Entrée de Runbook** puis sélectionnez **VMName**.   
7.  Pour **ServiceName**, définissez la **source de données** sur **Entrée de Runbook** puis sélectionnez **VMServiceName**.  
8.  Sélectionnez le lien entre **Get-AzureVM** et **Start-AzureVM**.
9.  Dans le volet Configuration, définissez **Appliquer la condition** sur **True**. Notez que le lien se transforme en une ligne pointillée indiquant que l'activité cible s’exécutera uniquement si la condition est définie sur true.
10.  Pour l’**expression de condition**, entrez *$ActivityOutput['Get-AzureVM'].PowerState -eq "Arrêtée"*. **Start-AzureVM** s’exécutera uniquement si la machine virtuelle est arrêtée.<br> ![Condition de liaison](media/automation-first-runbook-graphical/link-condition.png) 
11.  Dans le contrôle Bibliothèque, développez **Applets de commande**, puis **Microsoft.PowerShell.Utility**.
12.  Ajoutez **Write-Output** au canevas.
13.  Créez un lien entre **Get-AzureVM** et **Write-Output**.
14.  Sélectionnez le lien et définissez **Appliquer la condition** sur **True**.
14.  Pour l’**expression de condition**, entrez *$ActivityOutput['Get-AzureVM'].PowerState -ne "Arrêtée"*. **Write-Output** s’exécutera uniquement si la machine virtuelle n’est pas arrêtée.<br> ![Runbook avec Write-Output](media/automation-first-runbook-graphical/write-output-link.png) 
15.  Sélectionnez **Write-Output** et cliquez sur **Paramètres**.
16.  Pour **InputObject**, définissez **Source de données** sur **Expression PowerShell** et entrez l'expression *"$VMName.Name déjà arrêtée."*.
17.  Enregistrez le Runbook et ouvrez le volet de test.
18.  Démarrez le Runbook avec la machine virtuelle arrêtée, et il devrait démarrer.
19.  Démarrez le Runbook avec la machine virtuelle démarrée, et un message devrait indiquer qu'il est déjà démarré.
 

## Articles connexes

- [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md)


 

<!---HONumber=July15_HO4-->