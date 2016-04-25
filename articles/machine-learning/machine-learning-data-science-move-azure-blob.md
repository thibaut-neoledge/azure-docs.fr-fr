<properties
	pageTitle="Déplacer des données vers et depuis un stockage Azure Blob | Microsoft Azure"
	description="Déplacer des données vers et depuis un stockage Azure Blob"
	services="machine-learning,storage"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="bradsev;sunliangms;sachouks;mohabib" />

# Déplacer des données vers et depuis un stockage Azure Blob

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

## Introduction

Des conseils sur les technologies utilisées pour déplacer des données vers et/ou depuis le stockage d’objets blob Azure sont disponibles ici :

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

La méthode la mieux adaptée à vos besoins dépend de votre scénario. L’article sur les [Scénarios du processus de science des données Azure en action dans Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md) vous aide à déterminer les éléments multimédias dont vous avez besoin pour différents flux de travail d’analyse avancée.

> [AZURE.NOTE] Pour une présentation complète du stockage d’objets blob Azure, consultez les articles [Fonctionnalités de base des objets blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et [Service Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

> [AZURE.TIP] Comme autre solution, vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pour créer et planifier un pipeline qui permet de télécharger des données depuis le stockage d’objets blob Azure, de le transférer à un service web Azure Machine Learning publié, de recevoir les résultats d’analyse prédictive et de télécharger les résultats dans le stockage. Pour plus d’informations, consultez la page [Créer des pipelines prédictifs à l’aide d’Azure Data Factory et Azure Machine Learning](../data-factory/data-factory-azure-ml-batch-execution-activity.md).

## Composants requis

Ce document suppose que vous disposez d’un abonnement Azure, d’un compte de stockage et de la clé de stockage correspondante pour ce compte. Avant de charger ou télécharger des données, vous devez connaître le nom et la clé de votre compte Azure Storage.

- Pour configurer un abonnement Azure, consultez [Essai gratuit pendant un mois](https://azure.microsoft.com/pricing/free-trial/).
- Pour obtenir des instructions sur la création d’un compte de stockage et pour obtenir des informations de compte et de clé, consultez [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

<!---HONumber=AcomDC_0413_2016-->