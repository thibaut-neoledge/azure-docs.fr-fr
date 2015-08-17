<properties
	pageTitle="Qu'est-ce qu'Azure Automation ?"
	description="Découvrez les avantages d'Azure Automation et obtenez des réponses aux questions afin de commencer à créer et à utiliser des Runbooks."
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
	ms.date="07/06/2015"
	ms.author="bwren"/>

# Qu'est-ce qu'Azure Automation ?

Microsoft Azure Automation permet aux utilisateurs d'automatiser les tâches répétitives, manuelles, de longue durée et susceptibles d'engendrer des erreurs, qui sont communément exécutées dans un environnement cloud et d’entreprise. Cet article fournit des réponses rapides aux questions courantes sur Azure Automation. Vous pouvez vous référer à d'autres articles de cette bibliothèque pour des informations plus détaillées sur les différentes rubriques.

## Quelle valeur fournit Azure Automation ?

Azure Automation vous fait gagner du temps et augmente la fiabilité des tâches administratives courantes que vous effectuez dans les environnements de cloud et d'entreprise. Vous pouvez implémenter ces processus sous forme de Runbooks qui peuvent exécuter plusieurs tâches sans intervention humaine et même les planifier pour s'exécuter automatiquement à intervalles réguliers.

## Qu'est-ce qu’un Runbook ?

Un Runbook est un ensemble de tâches qui exécutent des processus automatisés dans Azure Automation. Il peut s'agir d'un processus simple comme le démarrage d'une machine virtuelle ou la création d'une entrée de journal, ou d'un Runbook complexe combinant d'autres Runbooks plus petites pour effectuer un processus complexe sur plusieurs ressources voire plusieurs clouds.

Par exemple, vous pouvez utiliser un processus manuel existant pour approvisionner une nouvelle machine virtuelle incluant plusieurs étapes comme la création d'une machine virtuelle, sa connexion à un réseau, l'affectation d'une adresse IP puis la notification à un utilisateur que la machine virtuelle est prête. Au lieu d'exécuter manuellement chacune de ces étapes, vous pouvez créer un Runbook qui effectue toutes ces tâches comme un processus unique. Vous démarrez le Runbook, fournissez les informations requises comme le nom de la machine virtuelle, l'adresse IP et l'adresse électronique du destinataire, puis le processus s'occupe du reste.


## Que peuvent automatiser les Runbooks ?

Comme les Runbooks dans Azure Automation reposent sur PowerShell Workflow, ils peuvent accomplir tout ce que permet PowerShell. Un Runbook peut fonctionner avec toute application ou tout service disposant d'une API. Si vous avez un module PowerShell pour cela, vous pouvez charger ce module dans Azure Automation et inclure ces applets de commande dans votre Runbook. Les Runbooks Azure Automation s'exécutent dans le cloud Azure et peuvent ainsi accéder à toutes les ressources du cloud ou à des ressources externes accessibles à partir du cloud. Grâce à un [Runbook Worker hybride](automation-hybrid-runbook-worker.md), les Runbooks peuvent s'exécuter sur votre centre de données local pour gérer des ressources locales.


## Où puis-je obtenir des Runbooks ?

La [galerie de Runbooks](http://msdn.microsoft.com/library/azure/dn781422.aspx) contient des Runbooks provenant de Microsoft et de la communauté, que vous pouvez utiliser tels quels dans votre environnement ou personnaliser selon vos besoins. Ils sont également utiles comme références pour apprendre à créer vos propres Runbooks. Vous pouvez même ajouter vos propres Runbooks à la galerie si vous pensez qu'ils peuvent être utiles à d'autres utilisateurs.


## Comment créer mes propres Runbooks ?

Vous pouvez intégralement [créer vos Runbooks](http://msdn.microsoft.com/library/azure/dn643637.aspx) ou modifier les Runbooks à partir de la [galerie de Runbooks](http://msdn.microsoft.com/library/azure/dn781422.aspx) selon vos besoins. Si vous préférez travailler directement avec le code PowerShell, vous pouvez [modifier le Runbook à l'aide de l'éditeur de texte](http://msdn.microsoft.com/library/azure/dn879137.aspx) du portail Azure ou le modifier hors connexion. Si vous préférez modifier un Runbook sans passer par le code sous-jacent, vous pouvez utiliser l'[éditeur graphique](automation-graphical-authoring-intro.md) du portail Azure en version préliminaire.


## Quels sont les liens entre Azure Automation et d'autres outils d'automatisation ?

[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) vise à automatiser les tâches de gestion dans le cloud privé. Il est installé localement dans votre centre de données en tant que composant de [Windows Azure Pack](http://www.microsoft.com/server-cloud/products/windows-azure-pack/default.aspx). SMA et Azure Automation utilisent le même format de Runbook basé sur un workflow Windows PowerShell, mais SMA ne prend pas en charge les [Runbooks graphiques](automation-graphical-authoring-intro.md).

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) est conçu pour l'automatisation des ressources locales. Il utilise un autre format de Runbooks qu'Azure Automation et Service Management Automation, et dispose d'une interface graphique permettant de créer des Runbooks sans aucun script. Ses Runbooks sont constitués d'activités provenant de packs d'intégration spécifiquement écrits pour Orchestrator.

## Où puis-je obtenir plus d'informations ?

De nombreuses ressources sont disponibles pour vous permettre d'en savoir plus sur Azure Automation et de créer vos propres Runbooks.

- Vous vous trouvez actuellement dans la **bibliothèque Azure Automation**. Les articles de cette bibliothèque fournissent une documentation complète sur la configuration et l'administration d'Azure Automation et pour la création de vos propres Runbooks.
- Les [applets de commande Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) fournissent des informations sur les opérations d’automatisation Azure à l'aide de Windows PowerShell. Les Runbooks recourent à ces applets de commande pour utiliser les ressources Azure.
- Le [blog Azure Automation](http://azure.microsoft.com/blog/tag/azure-automation) fournit les dernières informations Microsoft sur Azure Automation. Vous devriez vous abonner à ce blog pour rester à jour avec la dernière version de l'équipe Azure Automation.
- Le [forum Automation Forum](http://go.microsoft.com/fwlink/p/?LinkId=390561) vous permet de publier des questions sur Azure Automation à destination de Microsoft et de la communauté Automation.

## Puis-je fournir des commentaires ?

**Faites-nous part de vos commentaires !** Si vous recherchez un module d'intégration ou une solution de Runbook Azure Automation, envoyez une demande de script au Centre de scripts. Le cas échéant, publiez vos commentaires ou demandes de fonctionnalités pour Azure Automation sur [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback). Merci !

<!---HONumber=August15_HO6-->