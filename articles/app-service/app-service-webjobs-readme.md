<properties
	pageTitle="WebJobs dans Azure App Service"
	description="Découvrez comment créer des tâches WebJobs pour exécuter des tests en arrière-plan, interagir avec des services tels que Storage et Service Bus, et créer des tâches planifiées."
	services="app-service"
	documentationCenter=""
	authors="christopheranderson"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="chrande"/>

# Utilisation de WebJobs dans Azure App Service

Cet article fournit des liens vers des ressources de documentation sur l’utilisation d’Azure WebJobs et du Kit de développement logiciel (SDK) Azure WebJobs. Azure WebJobs permet d’exécuter facilement des scripts ou des programmes sous la forme de processus d’arrière-plan dans [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Vous pouvez télécharger et exécuter un fichier exécutable comme cmd, bat, exe (.NET), ps1, sh, php, py, js et jar. Ces programmes s’exécutent en tant que tâches WebJobs selon une planification (cron) ou en continu.

Le Kit de développement logiciel (SDK) Azure WebJobs facilite l’utilisation d’Azure Storage. Il comporte un système de liaison et de déclencheur qui fonctionne avec les objets blob Microsoft Azure Storage, les files d’attente et les tables ainsi que les files d’attente Service Bus.

La création, le déploiement et la gestion des tâches web WebJobs sont parfaitement compatibles avec les outils intégrés dans Visual Studio. Vous pouvez créer les tâches web WebJobs à partir de modèles, les publier et les gérer (exécuter/arrêter/analyser/déboguer).

Le tableau de bord WebJobs dans le portail Azure fournit de puissantes fonctionnalités de gestion qui vous donnent un contrôle total sur l’exécution des tâches WebJobs, notamment la possibilité d’appeler des fonctions individuelles dans WebJobs. Le tableau de bord affiche également les runtimes de fonction et la sortie d'enregistrement.

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]

<!---HONumber=AcomDC_0413_2016-->