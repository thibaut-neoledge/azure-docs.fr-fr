<properties
	pageTitle="Reformation des modèles Machine Learning par programme | Microsoft Azure"
	description="Apprenez à reformer un modèle par programme et à mettre à jour le service Web pour utiliser le modèle reformé dans Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="raymondl;garye"/>


#Reformation des modèles Machine Learning par programme  

Dans le cadre du processus de mise en œuvre opérationnelle des modèles d’apprentissage automatique d’Azure Machine Learning, un modèle doit être formé et enregistré, puis utilisé pour créer un service Web de score. Le service Web peut ensuite être utilisé dans des sites Web, des tableaux de bord et des applications mobiles.

Il est fréquent de devoir reformer le modèle créé lors de la première étape avec de nouvelles données. Auparavant, cela ne pouvait être effectué que par le biais de l’interface utilisateur d’Azure ML, mais avec l’introduction de la fonctionnalité d’API de reformage par programme, vous pouvez reformer le modèle et mettre à jour le service Web, afin d’utiliser le modèle qui vient d’être formé, par programme, en utilisant les API de reformation.

Ce document décrit le processus ci-dessus et montre comment utiliser les API de reformation.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Les raisons de la reformation : définition du problème  
Dans le cadre du processus de formation ML, un modèle est formé à l’aide d’un jeu de données. Les modèles doivent être reformés dans les scénarios dans lesquels de nouvelles données sont disponibles, ou lorsque le consommateur de l’API dispose de ses propres données pour former le modèle ou lorsque les données doivent être filtrées et le modèle formé avec le sous-ensemble de données, etc.

Dans ces scénarios, une API par programme offre un moyen pratique pour vous ou les utilisateurs de vos API de créer un client qui peut, à titre exceptionnel ou de manière régulière, reformer le modèle à l’aide de ses propres données. Ils peuvent ensuite évaluer les résultats de la reformation et mettre à jour l’API du service Web, afin d’utiliser le modèle reformé.

##Méthode de reformation : le processus de bout en bout  
Pour commencer, le processus implique les éléments suivants : une expérience d’apprentissage et une expérience de notation publiées comme Service Web. Pour activer la reformation d’un modèle formé, l’expérience d’apprentissage doit également être publiée en tant que service Web avec la sortie d’un modèle formé. Cela permet à l’API d’accéder au modèle en vue de la reformation. Le processus de configuration de la reformation implique les étapes suivantes :

![][1]

Diagramme 1 : Présentation du processus de reformation

1. *Création d’une expérience d’apprentissage* Nous utiliserons l’expérience « Sample 5 (Train, Test, Evaluate for Binary Classification: Adult Dataset) » des exemples d’expériences d’Azure ML pour cet exemple. Comme vous le verrez ci-dessous, nous avons simplifié l’exemple en supprimant certains modules. Nous avons également nommé l’expérience « Modèle de recensement ».

 	![][2]

	Avec ces éléments en place, nous pouvons maintenant cliquer sur Exécuter en bas de l’écran pour exécuter cette expérience.  
2. *Création d’une expérience de notation et publication en tant que service Web*  

	![][3]

	À l’issue de l’exécution de l’expérience, nous cliquons sur Créer une expérience de notation. Cela crée une expérience de notation, enregistre le modèle sous la forme d’un modèle formé et ajoute des modules d’entrée et de sortie du service Web, comme illustré ci-dessous. Nous cliquons ensuite sur Exécuter.

	Après l’exécution de l’expérience, en cliquant sur « Publication du service Web », l’expérience de notation est publiée en tant que service Web et crée un point de terminaison par défaut. Le modèle formé de ce service Web est mis à jour, comme illustré ci-dessous. Les détails de ce point de terminaison apparaissent ensuite sur l’écran.  
3. *Publier l’expérience d’apprentissage en tant que service Web* Afin de reformer le modèle formé, nous devons publier l’expérience d’apprentissage, créée à l’étape 1 ci-dessus, en tant que service Web. Ce service Web a besoin d’un module de sortie de service Web connecté au module [Train Model][train-model], afin de produire de nouveaux modèles formés. Cliquez sur l’icône Expériences dans le volet gauche, puis cliquez sur l’expérience appelée Modèle de recensement pour revenir à l’expérience d’apprentissage.  

	Nous ajoutons ensuite un module d’entrée du service Web et deux modules de sortie du service Web vers le flux de travail. La sortie du service Web pour le modèle formé nous fournit le nouveau modèle formé. La sortie liée à Évaluer le modèle retourne la sortie du module Évaluer le modèle.

	Nous pouvons maintenant cliquer sur Exécuter. Une fois l’expérience terminée, le flux de travail doit ressembler à ce qui suit :

	![][4]

	Nous cliquons ensuite sur le bouton Publier un service Web, puis sur Oui. Cette opération publie l’expérience d’apprentissage en tant que service web qui produit un modèle formé et des résultats de notation du modèle. Le tableau de bord du service Web s’affiche avec la clé d’API et la page d’aide d’API pour l’exécution par lots. Notez que la méthode d’exécution par lots peut être utilisée pour créer des modèles formés.  
4. *Ajouter un nouveau point de terminaison* Le service Web de notation publié lors l’étape 2 ci-dessus a été créé avec un point de terminaison par défaut. Les points de terminaison par défaut sont toujours synchronisés avec l’expérience originale d’apprentissage et de notation. Par conséquent, un modèle formé du point de terminaison par défaut ne peut pas être remplacé. Pour créer un point de terminaison pouvant être mis à jour, visitez le portail Azure et cliquez sur Ajouter un point de terminaison (plus de détails [ici](machine-learning-create-endpoint.md)).

5. *Reformer le modèle avec de nouvelles données et BES* Pour appeler les API de reformation, nous créons une nouvelle application console C# dans Visual Studio (Nouveau -> projet -> Bureau Windows -> Application console).

	Ensuite, nous copions l’exemple de code C# à partir de la page d’aide de l’API de service Web d’apprentissage pour l’exécution par lots (créée lors de l’étape 3 ci-dessus) et la collons dans le fichier Program.cs, en vérifiant que l’espace de noms reste intact.

	Notez que l’exemple de code comporte des commentaires indiquant les parties du code nécessitant des mises à jour. En outre, si vous indiquez l’emplacement « output1 » dans la charge utile de demande, l’extension de fichier pour « RelativeLocation » doit être modifiée en « .ileaner » comme dans :

	```c#
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
	```
	1. Fournir des informations Azure Storage L’exemple de code pour BES télécharge un fichier à partir d’un lecteur local (par exemple « C:\\temp\\CensusIpnput.csv ») vers Azure Storage, le traite et réécrit les résultats dans Azure Storage.  

		Pour cela, vous devez récupérer les informations de nom de compte, de clé et de conteneur de stockage à partir du portail de gestion Azure pour votre compte de stockage et mettre le code à jour ici. Vous devez également vous assurer que le fichier d’entrée est disponible à l’emplacement spécifié dans le code.

		Nous avions paramétré cette expérience d’apprentissage avec deux sorties. Les résultats doivent donc inclure des informations d’emplacement de stockage pour les deux, comme indiqué ci-dessous. « output1 » est la sortie du modèle formé, « output2 » celle d’Évaluer le modèle. Notez également que l’extension du fichier de sortie du modèle appris (Output1) est « .ilearner » et non « .csv ».

		![][6]

6. *Évaluer les résultats de la reformation* À l’aide de la combinaison de BaseLocation, RelativeLocaiton et SasBlobToken des résultats de sortie ci-dessus pour « output2 », nous pouvons voir les résultats des performances du modèle reformé en collant l’URL complète dans la barre d’adresses du navigateur.

	Cela indique si le modèle reformé est suffisamment performant pour remplacer le modèle existant.

7. *Mise à jour du modèle formé du point de terminaison ajouté* Pour terminer le processus, nous devons mettre à jour le modèle formé du point de terminaison de notation créé lors de l’étape 4 ci-dessus.

	La sortie BES ci-dessus affiche les informations pour le résultat de la reformation de « output1 », qui contient les informations d’emplacement du modèle reformé. Nous devons maintenant prendre ce modèle formé et mettre à jour le point de terminaison de notation (créé à l’étape 4 ci-dessus). Voici un exemple de code :

	```C#
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
	```

	« apiKey » et « endpointUrl » pour cet appel sont visibles dans le tableau de bord du point de terminaison.

	Après le succès de cet appel, le nouveau point de terminaison commence à utiliser le modèle reformé dans les 15 secondes environ.

##Résumé  
À l’aide des API de reformation, nous pouvons mettre à jour le modèle formé d’un service Web prédictif, ce qui permet des scénarios comme la reformation périodique de modèles avec de nouvelles données ou la distribution de modèles à des clients dans le but de leur permettre de reformer le modèle à l’aide de leurs propres données.

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=Sept15_HO2-->