<properties
	pageTitle="Déplacer des ressources d’application web vers un autre groupe de ressources"
	description="Décrit les scénarios dans lesquels vous pouvez déplacer des applications web et services d’application d’un groupe de ressources vers un autre."
	services="app-service\web"
	documentationCenter=""
	authors="ZainRizvi"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="zarizvi"/>
	
# Configurations de déplacement prises en charge

Vous pouvez déplacer des ressources d’application web Azure (applications web et services d’application) à l’aide de l’[API de déplacement des ressources ARM](../resource-group-move-resources.md).

Les applications web Azure prennent actuellement en charge les scénarios de déplacement suivants :

* Déplacement de tout le contenu d’un groupe de ressources vers un autre groupe de ressources
	* Remarque : le groupe de ressources de destination ne peut pas contenir de ressources Microsoft.Web dans ce scénario
* Déplacement des applications web individuelles vers un groupe de ressources différent, tout en les hébergeant toujours dans leur service d’application actuel (le service d’application reste dans l’ancien groupe de ressources)

<!---HONumber=July15_HO5-->