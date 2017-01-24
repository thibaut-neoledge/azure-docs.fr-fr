---
title: Gestion des erreurs dans les runbooks graphiques Azure Automation | Microsoft Docs
description: "Cet article décrit comment implémenter une logique de gestion des erreurs dans les runbooks graphiques Azure Automation."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Gestion des erreurs dans les runbooks graphiques Azure Automation

L’un des principaux objectifs de la conception de runbooks à prendre en compte est d’identifier les différents problèmes qu’un runbook peut rencontrer comme la réussite, les états d’erreur attendus et les conditions d’erreur inattendues.  Les runbooks doivent inclure la gestion des erreurs pour les détecter correctement.  Avec les runbooks graphiques, si vous souhaitez valider la sortie d’une activité ou gérer une erreur correctement, vous pourriez surveiller une activité de code PowerShell, définir la logique conditionnelle sur le lien de sortie de l’activité ou appliquer une autre méthode.          

Souvent, lorsque vos runbooks s’exécutent, si une activité rencontre une erreur sans fin d’exécution, l’activité suivante sera tout de même traitée.  Bien sûr, cela générera probablement une exception, mais l’important, c’est que l’activité suivante peut s’exécuter. La raison d’un tel comportement réside dans la façon dont le langage PowerShell est conçu pour gérer les erreurs.    

Les erreurs PowerShell qui peuvent survenir lors de l’exécution sont avec ou sans fin d’exécution.  La différence est la suivante :

* Erreur avec fin d’exécution : une erreur grave pendant l’exécution qui arrête complètement la commande (ou l’exécution du script). Il peut s’agir d’applets de commande inexistantes, d’erreurs de syntaxe qui empêchent une applet de commande de s’exécuter ou d’autres erreurs irrécupérables.

* Erreur sans fin d’exécution : une erreur non grave qui permet de continuer l’exécution malgré le dysfonctionnement. Il peut s’agit d’erreurs opérationnelles comme un fichier introuvable, des problèmes d’autorisation, etc.

Les runbooks graphiques Azure Automation permettent désormais d’inclure la gestion des erreurs afin que vous puissiez transformer les exceptions en erreur sans fin d’exécution et créer des liens d’erreur entre les activités. Cela permet à un auteur de runbook d’intercepter les erreurs et de gérer la condition réalisée ou inattendue.  

## <a name="when-to-use"></a>Quand utiliser

Le contrôle de l’exécution du flux de travail est important pour vérifier que, lorsqu’une activité critique génère une erreur ou une exception, vous pouvez éviter le passage à l’activité suivante dans votre runbook et gérer l’erreur de façon appropriée.  Cela est nécessaire surtout lorsque vos runbooks prennent en charge un processus d’entreprise ou de service.

Pour chaque activité susceptible de générer une erreur, l’auteur du runbook peut ajouter un lien d’erreur pointant vers une autre activité.  L’activité cible peut être de tout type : activité de code, appel d’une applet de commande, appel d’un autre runbook, etc. 

De plus, comme l’activité cible peut également avoir des liens sortants (normaux ou d’erreur), l’auteur du runbook peut implémenter une logique complexe de gestion d’erreurs sans intégrer une activité de code.  Bien que la pratique recommandée consiste à créer un runbook dédié à la gestion des erreurs avec des fonctionnalités communes, ce n’est pas une obligation et la logique de gestion des erreurs dans une activité de code PowerShell n’est pas la seule alternative.  

Par exemple, imaginez un runbook qui tente de démarrer une machine virtuelle et d’installer une application. Si la machine virtuelle ne démarre pas correctement, il exécute deux actions : 

1. Envoyer une notification concernant ce problème.
2. Démarrer un autre runbook qui configure automatiquement une nouvelle machine virtuelle. 

Une solution serait d’avoir un lien d’erreur pointant vers une activité pour gérer l’étape 1, comme l’applet de commande **Write-Warning**, connectée à une activité de l’étape 2, comme l’applet de commande **AzureRmAutomationRunbook-Start**. 

Vous pouvez également généraliser ce comportement dans de nombreux runbooks et intégrer ces deux activités dans un autre runbook de gestion des erreurs, selon les instructions proposées précédemment.  Avant d’appeler ce runbook de gestion des erreurs, vous pouvez construire un message personnalisé à partir des données du runbook d’origine, puis le transmettre en tant que paramètre au runbook de gestion des erreurs. 

## <a name="how-to-use"></a>Utilisation

Chaque activité possède une configuration permettant de transformer les exceptions en erreurs sans fin d’exécution. Par défaut, cette fonctionnalité est désactivée.  Vous devez l’activer sur l’activité dont vous souhaitez gérer les erreurs.  En activant cette configuration, vous êtes sûr que les erreurs avec et sans fin d’exécution dans l’activité sont traitées comme des erreurs sans fin d’exécution et peuvent ensuite être gérées avec un lien d’erreur.  Lorsque ce paramètre est configuré, vous créez des activités qui gèrent l’erreur.  Si une activité génère une erreur, les liens d’erreur sortants sont suivis, mais pas les liens normaux, même si l’activité a également produit une sortie normale.<br><br> ![Exemple de lien d’erreur d’un runbook Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

Dans l’exemple suivant, nous avons un runbook qui récupère une variable contenant le nom d’une machine virtuelle, puis tente de démarrer la machine virtuelle avec l’activité suivante.<br><br> ![Exemple de gestion des erreurs d’un runbook Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

L’activité **Get-AutomationVariable** et **AzureRmVm-Start** sont configurés pour transformer des exceptions en erreurs.  Si l’obtention de la variable ou le démarrage de la machine virtuelle pose problème, des erreurs sont générées.<br><br> ![Paramètres d’activité de gestion des erreurs d’un runbook Automation](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Les liens d’erreur relient ces activités à une activité **Gestion des erreurs** (activité de code), qui est configurée avec une expression PowerShell simple à l’aide du mot clé *Throw* pour arrêter le traitement avec *$Error.Exception.Message* pour obtenir le message décrivant l’exception actuelle.<br><br> ![Exemple de code de gestion d’erreurs d’un runbook Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les liens et comprendre les types de lien dans les runbooks graphiques, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow).

* Pour en savoir plus sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et d’autres détails techniques, consultez [Suivre une tâche de runbook](automation-runbook-execution.md) 


<!--HONumber=Jan17_HO1-->


