<properties 
	pageTitle="Utilisation des travaux Azure Media Services" 
	description="Cette rubrique offre une vue d’ensemble sur la façon de gérer les travaux Azure Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/05/2015" 
	ms.author="juliako"/>

#Utilisation des travaux Azure Media Services

Un **travail** contient des métadonnées sur le traitement à effectuer. Chaque **travail** contient une ou plusieurs **tâches** qui spécifient une tâche de traitement atomique, ses éléments multimédias d’entrée, ses éléments multimédias de sortie, un processeur multimédia et ses paramètres associés. Pour plus d’informations sur les paramètres d’encodage, consultez Guides d’encodage et Schémas d’encodage.

Un travail d’encodage est généralement combiné à d’autres traitements comme, par exemple, l’empaquetage ou le chiffrement de la ou des ressources générées par l’encodeur. Les tâches d’un travail peuvent être chaînées, c’est-à-dire que la ressource de sortie d’une tâche peut être transmise comme ressource d’entrée à la tâche suivante. De cette façon, un travail peut contenir tout le traitement nécessaire à une présentation multimédia.

Cette section fournit des liens vers les tâches courantes que vous exécuterez quand vous manipulerez des travaux/tâches.

>[AZURE.NOTE]Il existe actuellement une limite de 30 tâches par travail. Si vous devez chaîner plus de 30 tâches, créez plusieurs travaux pour contenir les tâches.


##Obtention du processeur multimédia

Procurez-vous le processeur multimédia avec **.NET** ou l’**API REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

##Création de travaux

Un travail est une entité qui contient des métadonnées sur un ensemble de tâches (par exemple, l’encodage ou l’indexation). Chaque tâche effectue une opération atomique sur la ou les ressources d’entrée. Pour obtenir un exemple sur la façon de créer des travaux d’encodage, consultez :

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##Indexation

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

##Encodage

Procédez à l’encodage à l’aide d’**Azure Media Encoder** en utilisant le **portail Azure Classic**, **.NET** ou l’**API REST**.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##Surveillance de la progression des travaux

Vous pouvez surveiller la progression des travaux à l’aide du **portail Azure Classic**, de **.NET** ou de l’**API REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

##Énumération 

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md/#list-jobs-and-assets)
- [REST](media-services-rest-manage-entities.md/#querying-entities)

##Suppression de tâches

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md/#delete-a-job)
- [REST](media-services-rest-manage-entities.md/##deleting-entities)

##Liens connexes

[Quotas et limitations](media-services-quotas-and-limitations.md) : décrit les quotas utilisés et les limitations de l’Encodeur de Media Services


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1210_2015-->