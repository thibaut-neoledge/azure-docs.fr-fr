<properties
pageTitle="Créer plusieurs modèles à partir d’une seule expérience | Microsoft Azure"
description="Utilisez PowerShell pour créer plusieurs modèles de formation et points de terminaison de service web Machine Learning avec le même algorithme, mais différents jeux de données de formation."
services="machine-learning"
documentationCenter=""
authors="hning86"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="05/12/2016"
ms.author="garye;haining"/>

# Créer de nombreux modèles Machine Learning et points de terminaison de service web à partir d’une expérience à l’aide de PowerShell

Voici un problème d’apprentissage automatique courant : vous souhaitez créer un grand nombre de modèles ayant le même flux de travail d’apprentissage et utilisant le même algorithme, mais avec différents jeux de données d’apprentissage comme entrée. Cet article montre comment procéder dans Azure Machine Learning Studio à l’aide d’une expérience unique.

Supposez par exemple que vous avez une franchise de location de vélos à l’échelle internationale. Vous souhaitez créer un modèle de régression pour prédire la demande en location sur la base de données historiques. Vous avez 1 000 emplacements de location dans le monde entier et vous avez recueilli un jeu de données pour chacun d’entre eux avec des fonctionnalités importantes telles que la date, l’heure, la météo et le trafic propres à chacun.

Vous pourriez former votre modèle une fois à l’aide d’une version fusionnée de tous les jeux de données et de tous les emplacements. Toutefois, chacun de vos emplacements ayant un environnement unique, une meilleure approche consiste à former le modèle de régression séparément à l’aide du jeu de données de chacun. Ainsi, chaque modèle formé peut prendre en compte les différences en termes de taille de magasin, de volume, de géographie, de population, de qualité de l’environnement de circulation pour les vélos, *et ainsi de suite*.

Cela pourrait être la meilleure approche, mais vous ne souhaitez pas créer 1 000 expériences d’apprentissage dans Azure Machine Learning représentant chacune un emplacement unique. Cette tâche serait non seulement intensive mais également assez inefficace, dans la mesure où chaque expérience aurait les mêmes composants, à l’exception du jeu de données d’apprentissage.

Heureusement, nous pouvons obtenir le même résultat en utilisant l’[API de reformation Azure Machine Learning](machine-learning-retrain-models-programmatically.md) et en automatisant la tâche avec [Azure Machine Learning PowerShell](https://blogs.technet.microsoft.com/machinelearning/2016/05/04/announcing-the-powershell-module-for-azure-ml/).

> [AZURE.NOTE] Pour accélérer l’exécution de notre exemple, nous allons réduire le nombre d’emplacements de 1000 à 10, mais les mêmes principes et procédures sont valables pour 1 000 emplacements. La seule différence est que si vous souhaitez effectuer un apprentissage à partir de 1 000 jeux de données, vous devrez sans doute exécuter les scripts PowerShell suivants en parallèle. Cette opération sort du cadre de cet article, mais vous trouverez des exemples de multi-threading PowerShell sur Internet.

## Configurer l’expérience de formation

Nous allons utiliser un exemple d’[expérience de formation](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) que nous avons déjà créé dans la [galerie Cortana Intelligence](http://gallery.cortanaintelligence.com). Ouvrez cette expérience dans votre espace de travail [Azure Machine Learning Studio](https://studio.azureml.net).

>[AZURE.NOTE] Pour suivre cet exemple, il est préférable d’utiliser un espace de travail standard plutôt qu’un espace de travail gratuit. Nous allons créer un point de terminaison pour chaque client (soit 10 points de terminaison en tout), ce qui nécessite un espace de travail standard car un espace de travail gratuit est limité à trois points de terminaison. Si vous disposez uniquement d’un espace de travail gratuit, il suffit de modifier les scripts ci-dessous pour autoriser uniquement trois emplacements.

L’expérience utilise un module **Import Data** pour importer le jeu de données de formation *customer001.csv* à partir d’un compte de stockage Azure. Supposons que nous avons recueilli des jeux de données de formation à partir de tous les emplacements de location de vélos et que nous les avons stockés dans le même emplacement de stockage d’objets blob avec des noms de fichiers allant de *rentalloc001.csv* à *rentalloc10.csv*.

![image](./media/machine-learning-create-models-and-endpoints-with-powershell/reader-module.png)

Notez qu’un module **Web Service Output** a été ajouté au module **Train Model**. Quand cette expérience est déployée comme service web, le point de terminaison associé à cette sortie retourne le modèle formé au format de fichier .ilearner.

Notez également que nous configurons un paramètre de service web pour l’URL utilisée par le module **Import Data**. Cela nous permet d’utiliser le paramètre pour spécifier des jeux de données de formation individuels visant à former le modèle pour chaque emplacement. Nous aurions pu procéder de plusieurs manières, par exemple en utilisant une requête SQL avec un paramètre de service web pour obtenir des données à partir d’une base de données SQL Azure, ou simplement en utilisant un module **Web Service Input** pour transmettre un jeu de données au service web.

![image](./media/machine-learning-create-models-and-endpoints-with-powershell/web-service-output.png)

Exécutons maintenant cette expérience de formation à l’aide de la valeur par défaut *rental001.csv* comme jeu de données de formation. Si vous affichez la sortie du module **Evaluate** (cliquez sur la sortie et sélectionnez **Visualize**), vous constatez que nous obtenons une performance correcte de *AUC* = 0,91. À ce stade, nous sommes prêts à déployer un service web à partir de cette expérience de formation.

## Déployer les services web de formation et de notation

Pour déployer le service web de formation, cliquez sur le bouton **Set Up Web Service** (Configurer le service web) sous le canevas d’expérience et sélectionnez **Deploy Web Service** (Déployer le service web). Nommez ce service web « Bike Rental Training ».

Nous devons à présent déployer le service web de notation. Pour cela, nous pouvons cliquer sur **Set Up Web Service** (Configurer le service web) sous le canevas et sélectionner **Predictive Web Service** (Service web prédictif). Une expérience de notation est créée. Nous allons devoir effectuer quelques petits ajustements pour qu’elle fonctionne comme service web, par exemple supprimer la colonne d’étiquette « cnt » dans les données d’entrée et limiter la sortie à l’ID d’instance et à la valeur prédite correspondante.

Pour vous éviter ce travail, il vous suffit d’ouvrir l’[expérience prédictive](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) déjà préparée dans la galerie.

Pour déployer le service web, exécutez l’expérience prédictive, puis cliquez sur le bouton **Deploy Web Service** sous le canevas. Nommez le service web de notation « Bike Rental Scoring ».

## Créer 10 points de terminaison de service web identiques avec PowerShell

Ce service web est fourni avec un point de terminaison par défaut. Mais celui-ci ne nous intéresse pas, car il ne peut pas être mis à jour. Nous devons créer 10 points de terminaison supplémentaires, un pour chaque emplacement. Nous allons pour cela utiliser PowerShell.

Tout d’abord, nous allons configurer notre environnement PowerShell :

	Import-Module .\AzureMLPS.dll
	# Assume the default configuration file exists and is properly set to point to the valid Workspace.
	$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
	$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Ensuite, nous allons exécuter la commande PowerShell suivante :

	# Create 10 endpoints on the scoring web service.
	For ($i = 1; $i -le 10; $i++){
	    $seq = $i.ToString().PadLeft(3, '0');
	    $endpointName = 'rentalloc' + $seq;
	    Write-Host ('adding endpoint ' + $endpointName + '...')
	    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
	}

Nous avons maintenant créé 10 points de terminaison, qui contiennent tous le même modèle formé sur *customer001.csv*. Vous pouvez les afficher dans le Portail de gestion Azure.

![image](./media/machine-learning-create-models-and-endpoints-with-powershell/created-endpoints.png)

## Mettre à jour les points de terminaison pour utiliser des jeux de données de formation distincts à l’aide de PowerShell

L’étape suivante consiste à mettre à jour les points de terminaison avec des modèles formés de manière unique d’après les données individuelles de chaque client. Mais tout d’abord, nous devons générer ces modèles à partir du service web **Bike Rental Training**. Revenons au service web **Bike Rental Training**. Nous devons appeler son point de terminaison BES 10 fois avec 10 jeux de données de formation différents pour générer 10 modèles différents. Pour cela, nous allons utiliser l’applet de commande PowerShell **InovkeAmlWebServiceBESEndpoint**.

Vous devez également fournir des informations d’identification pour votre compte de stockage d’objets blob dans `$configContent`, à savoir les champs `AccountName`, `AccountKey` et `RelativeLocation`. Le `AccountName` peut être l’un de vos noms de compte, comme illustré dans le **Portail de gestion Azure classique** (onglet *Stockage*). Lorsque vous cliquez sur un compte de stockage, sa `AccountKey` est accessible en appuyant sur le bouton **Gérer les clés d’accès** situé au bas de la page et en copiant la *clé d’accès primaire*. Le `RelativeLocation` est le chemin d’accès relatif à votre espace de stockage où sera stocké le nouveau modèle. Par exemple, le chemin d’accès `hai/retrain/bike_rental/` dans le script ci-dessous pointe vers un conteneur nommé `hai`, et `/retrain/bike_rental/` sont des sous-dossiers. Actuellement, vous ne pouvez pas créer de sous-dossiers via le portail de l’interface utilisateur, mais il existe [plusieurs explorateurs de stockage Azure](../storage/storage-explorers.md) qui permettent de le faire. Il est recommandé de créer un conteneur dans votre stockage pour stocker les nouveaux modèles formés (fichiers .ilearner) comme suit : à partir de votre page de stockage, cliquez sur le bouton **Ajouter** en bas et nommez-le `retrain`. En résumé, les modifications nécessaires pour le script ci-dessous se rapportent à `AccountName`, `AccountKey` et `RelativeLocation` ( :`"retrain/model' + $seq + '.ilearner"`).

	# Invoke the retraining API 10 times
	# This is the default (and the only) endpoint on the training web service
	$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
	$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
	$apiKey = $trainingSvcEp.PrimaryKey;
	For ($i = 1; $i -le 10; $i++){
	    $seq = $i.ToString().PadLeft(3, '0');
	    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
	    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
	    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
	    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
	}

>[AZURE.NOTE] Le point de terminaison BES est le seul mode pris en charge pour cette opération. Vous ne pouvez pas utiliser RRE pour générer des modèles formés.

Comme vous pouvez le voir ci-dessus, au lieu de construire 10 fichiers JSON de configuration de travail BES différents, nous créons la chaîne de configuration de manière dynamique et nous la fournissons au paramètre *jobConfigString* de l’applet de commande **InvokeAmlWebServceBESEndpoint**, car il est inutile d’en conserver une copie sur le disque.

Si tout se passe bien, après un certain temps vous devriez voir 10 fichiers .ilearner, de *model001.ilearner* à *model010.ilearner*, dans votre compte de stockage Azure. Nous pouvons maintenant mettre à jour nos 10 points de terminaison de service web de notation avec ces modèles à l’aide de l’applet de commande PowerShell **AmlWebServiceEndpoint-correctif**. N’oubliez pas que nous pouvons modifier uniquement les points de terminaison autres que les points de terminaison par défaut que nous avons créés précédemment par programmation.

	# Patch the 10 endpoints with respective .ilearner models
	$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
	$sasToken = '<my_blob_sas_token>'
	For ($i = 1; $i -le 10; $i++){
	    $seq = $i.ToString().PadLeft(3, '0');
	    $endpointName = 'rentalloc' + $seq;
	    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
	    Write-Host ('Patching endpoint ' + $endpointName + '...');
	    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
	}

L’exécution devrait être assez rapide. Une fois l’exécution terminée, nous aurons créé 10 points de terminaison de service web prédictifs, chacun contenant un modèle formé de manière unique d’après le jeu de données propre à l’emplacement de location, tout ceci à partir d’une expérience de formation unique. Pour vérifier cela, vous pouvez essayer d’appeler ces points de terminaison à l’aide de l’applet de commande **InvokeAmlWebServiceRRSEndpoint**, en leur fournissant les mêmes données d’entrée. Les résultats de prédiction devraient être différents, puisque les modèles sont formés avec des jeux de données différents.

## Script PowerShell complet

Voici l’intégralité du code source :

	Import-Module .\AzureMLPS.dll
	# Assume the default configuration file exists and properly set to point to the valid workspace.
	$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
	$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

	# Create 10 endpoints on the scoring web service
	For ($i = 1; $i -le 10; $i++){
	    $seq = $i.ToString().PadLeft(3, '0');
	    $endpointName = 'rentalloc' + $seq;
	    Write-Host ('adding endpoint ' + $endpontName + '...')
	    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
	}

	# Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
	$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
	$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
	$apiKey = $trainingSvcEp.PrimaryKey;
	For ($i = 1; $i -le 10; $i++){
	    $seq = $i.ToString().PadLeft(3, '0');
	    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
	    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
	    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
	    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
	}

	# Patch the 10 endpoints with respective .ilearner models
	$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
	$sasToken = '?test'
	For ($i = 1; $i -le 10; $i++){
	    $seq = $i.ToString().PadLeft(3, '0');
	    $endpointName = 'rentalloc' + $seq;
	    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
	    Write-Host ('Patching endpoint ' + $endpointName + '...');
	    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
	}

<!---HONumber=AcomDC_0706_2016-->