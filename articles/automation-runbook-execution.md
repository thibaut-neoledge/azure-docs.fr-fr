<properties 
   pageTitle="Exécution d'un Runbook dans Azure Automation"
   description="Décrit les détails du traitement d'un Runbook dans Azure Automation."
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
   ms.date="04/28/2015"
   ms.author="bwren" />

# Exécution d'un Runbook dans Azure Automation


Lorsque vous démarrez un Runbook dans Azure Automation, une tâche est créée. Une tâche est une instance d'exécution unique d'un Runbook. Un travail Azure Automation est assigné pour exécuter chaque tâche. Même si les travaux sont partagés par plusieurs comptes Azure, les tâches des différents comptes Automation sont isolées les unes des autres. Vous n'avez pas le contrôle du travail qui traitera la demande de votre tâche. Un même Runbook peut avoir plusieurs tâches s'exécutant à la fois. Lorsque vous affichez la liste des Runbooks du portail Azure, vous voyez l'état de la dernière tâche démarrée pour chaque Runbook. Vous pouvez afficher la liste des tâches de chaque Runbook pour en assurer le suivi de l'état. Pour obtenir une description des différents états des tâches, consultez [États des tâches](automation-viewing-the-status-of-a-runbook-job.md#job-statuses).

![États des tâches](./media/automation-runbook-execution/job-statuses.png)


Vos tâches auront accès à vos ressources Azure en créant une connexion à votre abonnement Azure. Ils auront uniquement accès aux ressources de votre centre de données si ces ressources sont accessibles depuis le cloud public.

## Autorisations

Les Runbooks d'Azure Automation nécessitent généralement l'accès aux ressources de votre abonnement Azure. [Authentification auprès d'Azure à l'aide d'Azure Active Directory](http://aka.ms/runbookauthor/authentication) décrit comment configurer Azure Active Directory et les [informations d'identification]() dans Azure Automation pour s'authentifier auprès des ressources Azure. Cette rubrique inclut également des informations sur l'utilisation de l'[applet de commande Add-AzureAccount](http://aka.ms/runbookauthor/azurecmdlets/addazureaccount) pour accéder aux ressources Azure dans les Runbooks que vous créez. Reportez-vous à la documentation sur les Runbooks que vous importez pour connaître leurs conditions de sécurité.

## Répartition de charge équilibrée

Afin de partager les ressources entre tous les Runbooks du cloud, Azure Automation décharge temporairement toute tâche après qu'elle a été exécutée pendant 3 heures, puis la redémarre à partir de son dernier [point de contrôle](http://aka.ms/runbookauthor/checkpoints). Pendant ce temps, la tâche affiche l'état En cours d'exécution, en attente de ressources. Si le Runbook n'a aucun point de contrôle ou que la tâche n'a pas atteint le premier point de contrôle avant d'être déchargée, il redémarre à partir du début.

Si le Runbook redémarre à partir du même point de contrôle ou du début du Runbook trois fois de suite, il se termine avec l'état Échec, en attente de ressources. L'objectif est d'empêcher que les Runbooks ne s'exécutent indéfiniment sans s'achever, car ils ne sont pas en mesure d'accéder au point de contrôle suivant sans être à nouveau déchargés. Dans ce cas, vous recevez l'exception suivante avec l'échec.

La tâche ne peut pas continuer, car elle a été exclue à plusieurs reprises du même point de contrôle. Assurez-vous que votre Runbook n'effectue pas des opérations de longue durée sans conserver son état.

Lorsque vous créez un Runbook, vous devez vous assurer que la durée d'exécution de toute activité entre deux points de contrôle ne dépasse pas 3 heures. Vous devrez peut-être ajouter des points de contrôle à votre Runbook pour vous assurer qu'il n'excède pas cette limite de 3 heures. Vous devrez peut-être également scinder les longues opérations. Par exemple, votre Runbook peut effectuer une réindexation sur une base de données SQL volumineuse. Si cette opération unique ne se termine pas dans la limite de la répartition de charge équilibrée, la tâche est déchargée et redémarrée depuis le début. Dans ce cas, vous devez décomposer l'opération de réindexation en plusieurs étapes, comme la réindexation d'une table à la fois, puis insérer un point de contrôle après chaque opération, afin que la tâche puisse reprendre après que la dernière opération s'est terminée.

## Articles connexes

- [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook)
- [Affichage de l'état d'une tâche du Runbook dans Azure Automation](automation-viewing-the-status-of-a-runbook-job)

<!---HONumber=58-->