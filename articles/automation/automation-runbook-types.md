<properties 
   pageTitle="Types de Runbooks Azure Automation"
   description="Décrit les différents types de Runbooks que vous pouvez utiliser dans Azure Automation et les considérations à prendre en compte pour déterminer le type à utiliser."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/24/2015"
   ms.author="bwren" />

# Types de Runbooks Azure Automation

Azure Automation prend en charge trois types de Runbooks qui sont brièvement décrits dans le tableau suivant. Les sections suivantes fournissent de plus amples informations sur chaque type, notamment des considérations sur l'utilisation de chacun de ces types.


| Type | Description |
|:---|:---|
| [Graphique](#graphical-runbooks) | Basé sur le workflow Windows PowerShell et créé et modifié entièrement dans l'éditeur graphique du portail Azure. | 
| [Workflow PowerShell](#powershell-workflow-runbooks) | Runbook texte basé sur le workflow Windows PowerShell. |
| [PowerShell](#powershell-runbooks) | Runbook texte basé sur le script Windows PowerShell. |

## Runbooks graphiques

Les [Runbooks graphiques](automation-runbook-types.md#graphical-runbooks) sont créés et modifiés avec l'éditeur graphique dans le portail Azure. Vous pouvez les exporter vers un fichier puis les importer dans un autre compte Automatisation, mais vous ne pouvez pas les créer ni les modifier avec un autre outil. Les Runbooks graphiques génèrent un code de workflow PowerShell, mais vous ne pouvez pas directement afficher ou modifier ce code. Les Runbooks graphiques ne peuvent pas être convertis en [formats texte](automation-runbook-types.md), et un Runbook texte ne peut pas être converti au format graphique.

### Avantages

- Créez des Runbooks avec une connaissance minimale du [workflow PowerShell](automation-powershell-workflow.md).
- Représentez visuellement les processus de gestion.
- Utilisez des [points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre le Runbook en cas d'erreur.
- Utilisez un [traitement en parallèle](automation-powershell-workflow.md#parallel-processing) pour effectuer plusieurs activités en parallèle.
- Peut inclure d'autres Runbooks graphiques et Runbooks de workflow PowerShell en tant que Runbooks enfants afin de créer des workflows de haut niveau.


### Limites

- Impossible de modifier le Runbook en dehors du portail Azure.
- Peut nécessiter un [contrôle Script de workflow](automation-powershell-workflow.md#activities) contenant du code de workflow PowerShell pour exécuter une logique complexe.
- Impossible d'afficher ou de modifier directement le code de Workflow PowerShell créé par le workflow graphique. Notez que vous pouvez afficher le code dans toute activité Script de Workflow.
- Un Runbook prend plus de temps à démarrer que les Runbooks PowerShell car il doit être compilé avant l'exécution.
- Les Runbooks PowerShell peuvent uniquement être inclus en tant que Runbooks enfants à l'aide de l'applet de commande Start-AzureAutomationRunbook qui crée une tâche.


## Runbooks de workflow PowerShell

Les Runbooks de workflow PowerShell sont des Runbooks texte basés sur un [workflow Windows PowerShell](automation-powershell-workflow.md). Vous modifiez directement le code du Runbook à l'aide de l'éditeur de texte du portail Azure. Vous pouvez également utiliser n'importe quel éditeur de texte hors ligne et [importer le Runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) dans Azure Automation.

### Avantages

- Implémentez tout type de logique complexe avec le code de workflow PowerShell.
- Utilisez des [points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre le Runbook en cas d'erreur.
- Utilisez un [traitement en parallèle](automation-powershell-workflow.md#parallel-processing) pour effectuer plusieurs actions en parallèle.
- Peut inclure d'autres Runbooks graphiques et Runbooks de workflow PowerShell en tant que Runbooks enfants afin de créer des workflows de haut niveau.


### Limites

- L’auteur doit être familiarisé avec les workflows PowerShell.
- Un Runbook doit pouvoir gérer la complexité supplémentaire liée au workflow PowerShell, notamment les [objets désérialisés](automation-powershell-workflow.md#code-changes).
- Un Runbook prend plus de temps à démarrer que les Runbooks PowerShell car il doit être compilé avant l'exécution.
- Les Runbooks PowerShell peuvent uniquement être inclus en tant que Runbooks enfants à l'aide de l'applet de commande Start-AzureAutomationRunbook qui crée une tâche.


## Runbooks PowerShell

Les Runbooks PowerShell sont basés sur Windows PowerShell. Vous modifiez directement le code du Runbook à l'aide de l'éditeur de texte du portail Azure. Vous pouvez également utiliser n'importe quel éditeur de texte hors ligne et [importer le Runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) dans Azure Automation.

### Avantages

- Implémentez tout type de la logique complexe avec le code PowerShell sans la complexité supplémentaire liée au workflow PowerShell. 
- Le Runbook démarre plus rapidement que les Runbooks graphiques ou de workflow PowerShell dans la mesure où il n'a pas besoin d'être compilé avant l'exécution.

### Limites

- Doit être familiarisé avec les scripts PowerShell.
- Impossible d'utiliser un [traitement en parallèle](automation-powershell-workflow.md#parallel-processing) pour effectuer plusieurs actions en parallèle.
- Impossible d'utiliser des [points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre le Runbook en cas d'erreur.
- Impossible de démarrer des Runbooks sur un [Runbook Worker hybride](automation-hybrid-runbook-worker.md).
- Les Runbooks graphiques et les Runbooks de Workflow PowerShell peuvent uniquement être inclus en tant que Runbooks enfants à l'aide de l'applet de commande Start-AzureAutomationRunbook qui crée une tâche.

### Problèmes connus
Voici les problèmes connus actuels rencontrés avec les Runbooks PowerShell.

- Les Runbooks PowerShell ne peuvent pas récupérer une [ressource variable](automation-variables.md) non chiffrée avec une valeur null.
- Les Runbooks PowerShell ne peuvent pas récupérer une [ressource variable](automation-variables.md) avec le symbole *~* dans le nom.
- Get-Process dans une boucle d’un Runbook PowerShell peut se bloquer après environ 80 itérations. 
- Un Runbook PowerShell peut échouer s’il tente d'écrire une très grande quantité de données dans un flux de sortie. Vous pouvez généralement contourner ce problème en exportant uniquement les informations dont vous avez besoin lorsque vous travaillez avec des objets volumineux. Par exemple, au lieu d'exporter un contenu de type *Get-Process*, vous pouvez exporter uniquement les champs requis avec *Get-Process | Select ProcessName, CPU*.

## Considérations

Vous devez prendre en compte les considérations supplémentaires suivantes pour déterminer le type à utiliser pour un Runbook donné.

- Vous ne pouvez pas convertir des Runbooks d'un type à un autre.
- Il existe des limitations à l’utilisation de Runbooks de différents types comme un Runbook enfant. Consultez la page [Runbooks enfants dans Azure Automation](automation-child-runbooks.md) pour plus d'informations.



  
## Articles connexes

- [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md)
- [Apprentissage du workflow Windows PowerShell](automation-powershell-workflow.md)
- [Création ou importation d'un runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx)

<!---HONumber=Oct15_HO1-->