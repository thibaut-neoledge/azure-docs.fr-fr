<properties
	pageTitle="Gérer Azure HDInsight avec Azure Automation"
	description="Découvrez comment utiliser le service Azure Automation pour gérer Azure HDInsight."
	services="HDInsight, automation"
	documentationCenter=""
	authors="elcooper"
	manager="eamono"
	editor=""/>

<tags
	ms.service="HDInsight"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2015"
	ms.author="elcooper"/>



#Gérer Azure HDInsight avec Azure Automation
Ce guide vous présente le service Azure Automation et la manière de l'utiliser pour gérer plus simplement vos clusters et automatiser les tâches courantes dans Azure HDInsight.

## Qu'est-ce qu'Azure Automation ?
[Azure Automation](http://azure.microsoft.com/services/automation/) est un service Azure destiné à simplifier la gestion du cloud via l'automatisation des processus. Il automatise les tâches manuelles, répétitives, fastidieuses et sources d’erreurs pour accroître la fiabilité, l’efficacité et le retour sur investissement de votre organisation.

Azure Automation fournit un moteur d’exécution de workflow à haute fiabilité et à haute disponibilité, qui s’adapte à vos besoins. Dans Azure Automation, les processus peuvent être lancés manuellement, par des systèmes tiers ou à des intervalles planifiés, afin que les tâches interviennent exactement au moment opportun.

Réduisez les coûts opérationnels et libérez du temps pour que votre équipe d'informaticiens et de développeurs puisse se concentrer sur des tâches qui génèrent une valeur ajoutée pour l'entreprise, en planifiant l'exécution automatique de vos tâches de gestion de cloud sur Azure Automation.


## Comment Azure Automation peut-il aider à gérer Azure HDInsight ?

HDInsight peut être géré dans Azure Automation à l'aide des [applets de commande Azure HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx), disponibles dans les [outils Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Comme Azure Automation dispose de ces applets de commande prêtes à l'emploi, vous pouvez effectuer toutes vos tâches de gestion HDInsight au sein du service. Dans Azure Automation, vous pouvez également associer ces applets de commande à des applets de commande d'autres services Azure, afin d'automatiser des tâches complexes entre des services Azure et des systèmes tiers.

Avec les applets de commande Azure HDInsight, vous pouvez automatiser différentes tâches, comme la configuration des clusters HDInsight sous Linux ou Windows, la mise à l'échelle des clusters, la gestion des clusters et l'envoi de tâches MapReduce. Ce ne sont là que quelques-unes des nombreuses tâches que vous pouvez automatiser à l'aide de PowerShell dans Azure Automation.


## Étapes suivantes
Maintenant que vous connaissez les bases d'Azure Automation et que vous savez l'utiliser pour gérer Azure HDInsight, cliquez sur ce lien pour en savoir plus sur Azure Automation.

* Consultez le [didacticiel de mise en route](../automation-create-runbook-from-samples.md) d'Azure Automation.
* Découvrez des exemples dans le [centre de scripts](http://aka.ms/scriptcentergallery).  

 

<!---HONumber=Oct15_HO1-->