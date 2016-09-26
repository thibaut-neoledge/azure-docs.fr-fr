<properties
	pageTitle="Reformation des modèles Machine Learning par programme | Microsoft Azure"
	description="Apprenez à reformer un modèle par programme et à mettre à jour le service Web pour utiliser le modèle reformé dans Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="raymondl;garye;v-donglo"/>


#Reformation des modèles Machine Learning par programme  

Dans le cadre du processus de mise en œuvre opérationnelle des modèles d’apprentissage automatique d’Azure Machine Learning, votre modèle est entraîné et enregistré. Vous l’utilisez ensuite pour créer un service web prédictif. Le service Web peut ensuite être utilisé dans des sites Web, des tableaux de bord et des applications mobiles.

Les modèles que vous créez à l’aide de Machine Learning ne sont généralement pas statiques. Lorsque de nouvelles données sont disponibles ou lorsque le consommateur de l’API a ses propres données, il faut effectuer à nouveau l’apprentissage du modèle. Sinon, vous devrez peut-être appliquer des filtres afin d’obtenir un sous-ensemble des données et d’effectuer à nouveau l’apprentissage du modèle.

Il peut être très fréquent d’avoir à effectuer à nouveau l’apprentissage du modèle. Avec la fonctionnalité d’API Programmatic Retraining, vous pouvez effectuer à nouveau l’apprentissage du modèle par programmation à l’aide des API Retraining et mettre à jour le service web avec le modèle nouvellement entraîné.

Ce document décrit le processus de nouvel apprentissage et vous montre comment utiliser les API Retraining.

## Les raisons de la reformation : définition du problème  

Dans le cadre du processus de formation ML, un modèle est formé à l’aide d’un jeu de données. Les modèles que vous créez à l’aide de Machine Learning ne sont généralement pas statiques. Lorsque de nouvelles données sont disponibles ou lorsque le consommateur de l’API a ses propres données, il faut effectuer à nouveau l’apprentissage du modèle. Dans d’autres scénarios, vous devrez peut-être appliquer des filtres afin d’obtenir un sous-ensemble des données et d’effectuer à nouveau l’apprentissage du modèle.

Dans ces scénarios, une API par programme offre un moyen pratique pour vous ou les utilisateurs de vos API de créer un client qui peut, à titre exceptionnel ou de manière régulière, reformer le modèle à l’aide de ses propres données. Ils peuvent ensuite évaluer les résultats de la reformation et mettre à jour l’API du service Web, afin d’utiliser le modèle reformé.

##Méthode de reformation : le processus de bout en bout  

Pour commencer, le processus implique les éléments suivants : une expérience de formation et une expérience prédictive publiées comme service web. Pour activer le nouvel apprentissage d’un modèle entraîné, l’expérience d’apprentissage doit être publiée en tant que service web avec la sortie d’un modèle entraîné. Cela permet à l’API d’accéder au modèle en vue de la reformation.

Le processus de configuration de la reformation implique les étapes suivantes :

![Présentation du processus de nouvel apprentissage.][1]

Diagramme 1 : Présentation du processus de reformation

## Création d’une expérience d’apprentissage
 
Pour cet exemple, vous allez utiliser « Sample 5 : Train, Test, Evaluate for Binary Classification : Adult Dataset » dans les exemples Microsoft Azure Machine Learning.
	
Pour créer l’expérience :

1.	Connectez-vous à Microsoft Azure Machine Learning Studio.
2.	En bas à droite du tableau de bord, cliquez sur **Nouveau**.
3.	Parmi les exemples Microsoft, sélectionnez l’exemple 5.
4.	Pour renommer l’expérience, en haut du canevas de l’expérience, sélectionnez le nom de l’expérience « Sample 5 : Train, Test, Evaluate for Binary Classification : Adult Dataset ».
5.	Tapez Modèle de recensement.
6.	En bas de la zone de dessin de l’expérience, cliquez sur **Exécuter**.
7.	Cliquez sur **Configurer le service web** et sélectionnez **Effectuer à nouveau l’apprentissage du service web**.

 	![Expérience initiale.][2]

Diagramme 2 : Expérience initiale.

## Création d’une expérience de notation et publication en tant que service Web  

Ensuite, vous créez une expérience prédictive.

1.	En bas de la zone de dessin de l’expérience, cliquez sur **Configurer le service web** puis sélectionnez **Service web prédictif**. Cela enregistre le modèle sous la forme d’un modèle entraîné et ajoute des modules d’entrée et de sortie du service web.
2.	Cliquez sur **Exécuter**.
3.	Une fois l’expérience terminée, cliquez sur **Déployer le service web [classique]**. Cela déploie l’expérience prédictive sous la forme d’un service web classique.

## Déployer l’expérience d’apprentissage comme service web d’apprentissage

Pour effectuer à nouveau l’apprentissage du modèle entraîné, vous devez déployer l’expérience d’apprentissage que vous avez créée comme service web de nouvel apprentissage. Ce service Web a besoin d’un module de sortie de service Web connecté au module [Train Model][train-model], afin de produire de nouveaux modèles formés.

1. Pour revenir à l’expérience d’apprentissage, cliquez sur l’icône Expériences dans le volet gauche, puis sur l’expérience nommée Modèle de recensement.
2. Dans la zone de recherche des éléments d’expérience, tapez Service web.
3. Faites glisser un module Entrée du service web vers le canevas de l’expérience et connectez sa sortie au module Nettoyer les données manquantes.
4. Faites glisser deux modules *Sortie du service web* vers le canevas. Connectez la sortie du module *Effectuer l’apprentissage du modèle* à l’un et la sortie du module *Évaluer le modèle* à l’autre. La sortie du service web pour Effectuer l’apprentissage du modèle nous fournit le nouveau modèle entraîné. La sortie liée à Évaluer le modèle retourne la sortie du module Évaluer le modèle.
5. Cliquez sur **Exécuter**.
6. En bas de la zone de dessin de l’expérience, cliquez sur **Configurer le service web** puis sélectionnez **Service web de nouvel apprentissage**. Cette opération déploie l’expérience de formation en tant que service web qui produit un modèle entraîné et des résultats de notation du modèle. Le **Tableau de bord** du service web s’affiche avec la clé API et la page d’aide d’API pour l’exécution par lots. Seule la méthode d’exécution par lots peut être utilisée pour créer des modèles entraînés.
  
Une fois l’expérience terminée, le flux de travail doit ressembler à ce qui suit :

![Flux de travail produit après l’exécution.][4]

Diagramme 3 : Flux de travail produit après l’exécution.

## Ajouter un nouveau point de terminaison
 
Le service web prédictif que vous avez déployé est le point de terminaison de notation par défaut. Les points de terminaison par défaut sont toujours synchronisés avec l’expérience originale d’apprentissage et de notation. Par conséquent, le modèle entraîné du point de terminaison par défaut ne peut pas être remplacé.

Pour créer un nouveau point de terminaison de notation, sur le service web prédictif pouvant être mis à jour avec le modèle entraîné :

>[AZURE.NOTE] Veillez à ajouter le point de terminaison au service web prédictif et non au service web d’apprentissage. Si vous avez correctement déployé à la fois un service web prédictif et un service web d’apprentissage, vous devez voir deux services web distincts répertoriés. Le service web prédictif doit se terminer par « [exp. prédictive] ».

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Dans le menu de gauche, cliquez sur **Machine Learning**.
3. Sous Nom, cliquez sur votre espace de travail, puis sur **Services web**.
4. Sous Nom, cliquez sur **Modèle de recensement [exp. prédictive]**.
5. En bas de la page, cliquez sur **Ajouter un point de terminaison**. Pour plus d’informations sur l’ajout de points de terminaison, consultez [Création de points de terminaison](machine-learning-create-endpoint.md).

Vous pouvez également ajouter des points de terminaison de notation à l’aide de l’exemple de code fourni dans ce [référentiel GitHub](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

## Effectuer à nouveau l’apprentissage du modèle avec de nouvelles données à l’aide de BES

Pour appeler les API Retraining :

1. Créez une application console C# dans Visual Studio (Nouveau -> projet -> Bureau Windows -> Application console).
2. Copiez l’exemple de code C# à partir de la page d’aide de l’API du service web d’apprentissage pour l’exécution par lots et collez-le dans le fichier Program.cs, en vérifiant que l’espace de noms reste intact.
3. L’exemple de code comporte des commentaires indiquant les parties du code que vous devez mettre à jour.
4. Lorsque vous spécifiez l’emplacement de sortie dans la charge utile des demandes, l’extension du fichier spécifiée dans *RelativeLocation* doit être modifiée de csv à ilearner. Consultez l’exemple qui suit.

		Outputs = new Dictionary<string, AzureBlobDataReference>()
		{
			{
				"output1",
				new AzureBlobDataReference()
				{
					ConnectionString = "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==",
					RelativeLocation = "mycontainer/output1results.ilearner"
				}
			},
		},

>[AZURE.NOTE] Le nom de vos emplacements de sortie peut être différent de ceux de cette procédure pas à pas, selon l’ordre dans lequel vous avez ajouté les modules de sortie du service web. Étant donné que vous avez défini cette expérience d’apprentissage avec deux sorties, les résultats incluent les informations d’emplacement de stockage des deux.

### Mettre à jour les informations Azure Storage

L’exemple de code BES charge un fichier à partir d’un lecteur local (par exemple, « C:\\temp\\CensusInput.csv ») vers Azure Storage, le traite et réécrit les résultats dans Azure Storage.

Pour réaliser cette tâche, vous devez récupérer les informations de nom de compte, de clé et de conteneur de stockage à partir du Portail Azure Classic pour votre compte de stockage et mettre à jour les valeurs correspondantes dans le code.

Vous devez également vous assurer que le fichier d’entrée est disponible à l’emplacement spécifié dans le code.

![Sortie du nouvel apprentissage.][6]

Diagramme 4 : Sortie du nouvel apprentissage.

## Évaluer les résultats du nouvel apprentissage
 
Lorsque vous exécutez l’application, la sortie inclut le jeton SAP et l’URL nécessaires pour accéder aux résultats de l’évaluation.

Vous pouvez consulter les résultats des performances du modèle de nouveau entraîné en combinant *BaseLocation*, *RelativeLocation* et *SasBlobToken* dans les résultats de sortie de *output2* (comme le montre l’image de sortie du nouvel apprentissage précédente) et en collant l’URL complète dans la barre d’adresses du navigateur.

Examinez les résultats pour déterminer si le modèle de nouveau entraîné est suffisamment performant pour remplacer le modèle existant.

## Mettre à jour le modèle entraîné du point de terminaison ajouté

Pour terminer le processus de nouvel entraînement, vous devez mettre à jour le modèle entraîné du nouveau point de terminaison que vous avez ajouté.

* Si vous avez ajouté le nouveau point de terminaison à l’aide du Portail Azure, vous pouvez cliquer sur le nom du nouveau point de terminaison dans le Portail Azure, puis sur le lien **UpdateResource** pour obtenir l’URL dont vous avez besoin pour mettre à jour le modèle du point de terminaison.
* Si vous avez ajouté le point de terminaison à l’aide de l’exemple de code, cela inclut l’emplacement de l’URL d’aide identifiée par la valeur *HelpLocationURL* dans la sortie.

Pour récupérer l’URL du chemin d’accès :

1. Copiez et collez l’URL dans votre navigateur.
2. Cliquez sur le lien Mettre à jour les ressources.
3. Copiez l’URL de la publication de la requête PATCH. Par exemple :

		PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Vous pouvez maintenant utiliser le modèle entraîné pour mettre à jour le point de terminaison de notation que vous avez créé précédemment.

L’exemple de code suivant vous montre comment utiliser *BaseLocation*, *RelativeLocation*, *SasBlobToken* et l’URL PATCH pour mettre à jour le point de terminaison.

	private async Task OverwriteModel()
	{
		var resourceLocations = new
		{
			Resources = new[]
			{
				new
				{
					Name = "Census Model [trained model]",
					Location = new AzureBlobDataReference()
					{
						BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
						RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
						SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
					}
				}
			}
		};

		using (var client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

			using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
			{
				request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
				HttpResponseMessage response = await client.SendAsync(request);

				if (!response.IsSuccessStatusCode)
				{
					await WriteFailedResponse(response);
				}

				// Do what you want with a successful response here.
			}
		}
	}

*L’apiKey* et *l’endpointUrl* de cet appel sont récupérables sur le tableau de bord du point de terminaison.

La valeur du paramètre *Name* dans *Ressources* doit correspondre au nom de ressource du modèle entraîné enregistré dans l’expérience prédictive. Pour obtenir le nom de la ressource :

1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com).
2. Dans le menu de gauche, cliquez sur **Machine Learning**.
3. Sous Nom, cliquez sur votre espace de travail, puis sur **Services web**.
4. Sous Nom, cliquez sur **Modèle de recensement [exp. prédictive]**.
5. Cliquez sur le nouveau point de terminaison que vous avez ajouté.
6. Dans le tableau de bord du point de terminaison, cliquez sur *Mettre à jour les ressources*.
7. Sur la page de Documentation de l’API Mettre à jour les ressources du service web, vous trouverez le **Nom de la ressource** sous **Ressources actualisables**.

Si votre jeton SAP expire avant la fin de la mise à jour du point de terminaison, vous devez effectuer une opération GET avec l’ID du travail pour obtenir un nouveau jeton.

Lorsque le code a été exécuté avec succès, le nouveau point de terminaison doit commencer à utiliser le modèle de nouveau entraîné après environ 30 secondes.

##Résumé  
À l’aide des API Retraining, vous pouvez mettre à jour le modèle entraîné d’un service web prédictif pour prendre en charge des scénarios tels que :

* Nouvel apprentissage périodique d’un modèle avec de nouvelles données.
* Distribution d’un modèle auprès des clients dans le but de leur permettre d’effectuer à nouveau l’apprentissage du modèle avec leurs propres données.

## Étapes suivantes
[Dépannage de la reformation d’un service web classique Azure Machine Learning](machine-learning-troubleshooting-retraining-models.md)

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0914_2016-->