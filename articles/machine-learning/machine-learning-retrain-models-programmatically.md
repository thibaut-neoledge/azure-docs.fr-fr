<properties
	pageTitle="Reformation des modèles Machine Learning par programme | Microsoft Azure"
	description="Apprenez à reformer un modèle par programmation et à mettre à jour le service web de sorte qu’il utilise le modèle reformé dans Azure Machine Learning."
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
	ms.date="09/28/2016"
	ms.author="raymondl;garye;v-donglo"/>


#Reformation des modèles Machine Learning par programme  

Dans le cadre du processus de mise en œuvre opérationnelle des modèles d’apprentissage automatique d’Azure Machine Learning, votre modèle est entraîné et enregistré. Vous l’utilisez ensuite pour créer un service web prédictif. Le service web peut ensuite être utilisé dans des sites web, des tableaux de bord et des applications mobiles.

Les modèles que vous créez à l’aide de Machine Learning ne sont généralement pas statiques. Lorsque de nouvelles données sont disponibles ou lorsque le consommateur de l’API a ses propres données, il faut effectuer à nouveau l’apprentissage du modèle. Sinon, vous devrez peut-être appliquer des filtres afin d’obtenir un sous-ensemble des données et d’effectuer à nouveau l’apprentissage du modèle.

Il peut être très fréquent d’avoir à effectuer à nouveau l’apprentissage du modèle. Avec la fonctionnalité d’API Programmatic Retraining, vous pouvez reformer le modèle par programmation à l’aide des API Retraining et mettre à jour le service web avec le modèle reformé.

Ce document décrit le processus de nouvel apprentissage et vous montre comment utiliser les API Retraining.

## Les raisons de la reformation : définition du problème  

Dans le cadre du processus de formation Machine Learning, un modèle est formé à l’aide d’un jeu de données. Les modèles que vous créez à l’aide de Machine Learning ne sont généralement pas statiques. Lorsque de nouvelles données sont disponibles ou lorsque le consommateur de l’API a ses propres données, il faut effectuer à nouveau l’apprentissage du modèle. Dans d’autres scénarios, vous devrez peut-être appliquer des filtres afin d’obtenir un sous-ensemble des données et d’effectuer à nouveau l’apprentissage du modèle.

Dans ces scénarios, une API par programme offre un moyen pratique pour vous ou les utilisateurs de vos API de créer un client qui peut, à titre exceptionnel ou de manière régulière, reformer le modèle à l’aide de ses propres données. Il est alors possible d’évaluer les résultats de la reformation et de mettre à jour l’API du service web de sorte qu’elle utilise le modèle reformé.

##Méthode de reformation : le processus de bout en bout  

Pour commencer, le processus implique les éléments suivants : une expérience de formation et une expérience prédictive publiées en tant que service web. Pour permettre la reformation d’un modèle, l’expérience de formation doit être publiée en tant que service web avec la sortie d’un modèle formé. Cela permet à l’API d’accéder au modèle en vue de la reformation.

Le processus de configuration de la reformation pour un service web classique implique les étapes suivantes :

![Présentation du processus de nouvel apprentissage.][1]

Figure 1 : Vue d’ensemble du processus de reformation pour un service web classique

Le processus de configuration de la reformation pour un nouveau service web implique les étapes suivantes :

![Présentation du processus de nouvel apprentissage.][7]

Figure 2 : Vue d’ensemble du processus de reformation pour un nouveau service web

## Création d’une expérience d’apprentissage
 
Pour cet exemple, vous allez utiliser « Sample 5 : Train, Test, Evaluate for Binary Classification : Adult Dataset » dans les exemples Microsoft Azure Machine Learning.
	
Pour créer l’expérience :

1.	Connectez-vous à Microsoft Azure Machine Learning Studio.
2.	En bas à droite du tableau de bord, cliquez sur **Nouveau**.
3.	Parmi les exemples Microsoft, sélectionnez l’exemple 5.
4.	Pour renommer l’expérience, en haut du canevas de l’expérience, sélectionnez le nom de l’expérience « Sample 5 : Train, Test, Evaluate for Binary Classification : Adult Dataset ».
5.	Tapez Modèle de recensement.
6.	En bas de la zone de dessin de l’expérience, cliquez sur **Exécuter**.
7.	Cliquez sur **Configurer le service web** et sélectionnez **Reformation du service web**.

 	![Expérience initiale.][2]

Diagramme 2 : Expérience initiale.

## Création d’une expérience de notation et publication en tant que service web  

Ensuite, vous créez une expérience prédictive.

1.	En bas de la zone de dessin de l’expérience, cliquez sur **Configurer le service web** puis sélectionnez **Service web prédictif**. Le modèle est enregistré sous forme de modèle formé et des modules d’entrée et de sortie du service web sont ajoutés.
2.	Cliquez sur **Exécuter**.
3.	Une fois l’exécution de l’expérience terminée, cliquez sur **Déployer le service web [classique]** ou **Déployer le service web [nouveau]**.

## Déployer l’expérience de formation en tant que service web de formation

Pour reformer le modèle, vous devez déployer l’expérience de formation que vous avez créée en tant que service web de reformation. Ce service web a besoin d’un module *Sortie du service web* connecté au module *[Former la modèle][train-model]* afin de pouvoir produire de nouveaux modèles formés.

1. Pour revenir à l’expérience d’apprentissage, cliquez sur l’icône Expériences dans le volet gauche, puis sur l’expérience nommée Modèle de recensement.
2. Dans la zone de recherche des éléments d’expérience, tapez Service web.
3. Faites glisser un module *Entrée du service web* dans le canevas de l’expérience et connectez sa sortie au module *Nettoyer les données manquantes*.
4. Faites glisser deux modules *Sortie du service web* dans le canevas de l’expérience. Connectez la sortie du module *Effectuer l’apprentissage du modèle* à l’un et la sortie du module *Évaluer le modèle* à l’autre. La sortie du service web pour **Former le modèle** nous fournit le nouveau modèle formé. La sortie liée à **Évaluer le modèle** retourne la sortie de ce module.
5. Cliquez sur **Exécuter**.

Ensuite, vous devez déployer l’expérience de formation en tant que service web qui produit un modèle formé et des résultats d’évaluation du modèle. La procédure diffère selon que vous utilisez un service web classique ou un nouveau service web.
  
**Service web classique**

En bas du canevas de l’expérience, cliquez sur **Configurer le service web**, puis sélectionnez **Service web [classique]**. Le **Tableau de bord** du service web s’affiche avec la clé API et la page d’aide d’API pour l’exécution par lots. Seule la méthode d’exécution par lots peut être utilisée pour créer des modèles entraînés.

**Nouveau service web**

En bas du canevas de l’expérience, cliquez sur **Configurer le service web**, puis sélectionnez **Service web [nouveau]**. Le portail de services web Azure Machine Learning s’ouvre sur la page Déployer le service web. Tapez un nom pour votre service web, choisissez un plan de paiement, puis cliquez sur **Déployer**. Seule la méthode d’exécution par lot peut être utilisée pour créer des modèles formés.

Dans les deux cas, une fois l’exécution de l’expérience terminée, le flux de travail doit ressembler à ce qui suit :

![Flux de travail produit après l’exécution.][4]

Diagramme 3 : Flux de travail produit après l’exécution.

## Effectuer à nouveau l’apprentissage du modèle avec de nouvelles données à l’aide de BES

Pour appeler les API Retraining :

1. Créez une application console C# dans Visual Studio (Nouveau -> projet -> Bureau Windows -> Application console).
2.	Connectez-vous au portail de services web Azure Machine Learning.
3.	Si vous utilisez un service web classique, cliquez sur **Classic Web Services** (Services web classiques).
	1.	Cliquez sur le service web utilisé.
	2.	Cliquez sur le point de terminaison par défaut.
	3.	Cliquez sur **Consommer**.
	4.	En bas de la page **Consommer**, dans la section **Exemple de code**, cliquez sur **Lot**.
	5.	Passez à l’étape 5 de cette procédure.
4.	Si vous utilisez un nouveau service web, cliquez sur **Services web**.
	1.	Cliquez sur le service web utilisé.
	2.	Cliquez sur **Consommer**.
	3.	En bas de la page Consommer, dans la section **Exemple de code**, cliquez sur **Lot**.
5.	Copiez l’exemple de code C# pour l’exécution par lot et collez-le dans le fichier Program.cs en veillant à ne pas modifier l’espace de noms.

Ajoutez le package NuGet Microsoft.AspNet.WebApi.Client comme indiqué dans les commentaires. Pour ajouter une référence à Microsoft.WindowsAzure.Storage.dll, il se peut que vous deviez au préalable installer la bibliothèque cliente pour les services de stockage Microsoft Azure. Pour plus d’informations, consultez [cette page](https://www.nuget.org/packages/WindowsAzure.Storage).

### Mettre à jour la déclaration apiKey

Localisez la déclaration **apiKey**.

	const string apiKey = "abc123"; // Replace this with the API key for the web service

Dans la section des **informations de base sur la consommation** de la page **Consommer**, recherchez la clé primaire et copiez-la dans la déclaration **apiKey**.

### Mettre à jour les informations Azure Storage

L’exemple de code BES charge un fichier à partir d’un lecteur local (par exemple, « C:\\temp\\CensusInput.csv ») vers Azure Storage, le traite et réécrit les résultats dans Azure Storage.

Pour réaliser cette tâche, vous devez récupérer les informations de nom de compte, de clé et de conteneur de stockage pour votre compte de stockage depuis le portail Azure Classic et mettre à jour les valeurs correspondantes dans le code.

1. Connectez-vous au portail Azure Classic.
1. Dans la colonne de navigation de gauche, cliquez sur **Stockage**.
1. Dans la liste des comptes de stockage, sélectionnez-en un pour stocker le modèle reformé.
1. En bas de la page, cliquez sur **Gérer les clés d’accès**.
1. Copiez et enregistrez la **clé d’accès primaire**, puis fermez la boîte de dialogue.
1. En haut de la page, cliquez sur **Conteneurs**.
1. Sélectionnez un conteneur existant ou créez-en un et enregistrez le nom.

Localisez les déclarations *StorageAccountName*, *StorageAccountKey* et *StorageContainerName*, et mettez à jour les valeurs que vous avez enregistrées depuis le portail Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
Vous devez également vous assurer que le fichier d’entrée est disponible à l’emplacement spécifié dans le code.

### Spécifier l’emplacement de sortie

Lorsque vous spécifiez l’emplacement de sortie dans la charge utile des demandes, l’extension du fichier spécifiée dans *RelativeLocation* doit avoir pour valeur ilearner. Consultez l’exemple qui suit.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] Le nom de vos emplacements de sortie peut être différent de ceux de cette procédure pas à pas, selon l’ordre dans lequel vous avez ajouté les modules de sortie du service web. Étant donné que vous avez défini cette expérience d’apprentissage avec deux sorties, les résultats incluent les informations d’emplacement de stockage des deux.

![Sortie du nouvel apprentissage.][6]

Diagramme 4 : Sortie du nouvel apprentissage.

## Évaluer les résultats du nouvel apprentissage
 
Lorsque vous exécutez l’application, la sortie inclut le jeton SAP et l’URL nécessaires pour accéder aux résultats de l’évaluation.

Vous pouvez consulter les résultats des performances du modèle de nouveau entraîné en combinant *BaseLocation*, *RelativeLocation* et *SasBlobToken* dans les résultats de sortie de *output2* (comme le montre l’image de sortie du nouvel apprentissage précédente) et en collant l’URL complète dans la barre d’adresses du navigateur.

Examinez les résultats pour déterminer si le modèle de nouveau entraîné est suffisamment performant pour remplacer le modèle existant.

Copiez les éléments *BaseLocation*, *RelativeLocation* et *SasBlobToken* dans les résultats de sortie. Ils seront utilisés pendant le processus de reformation.

## Étapes suivantes

[Reformer un service web classique](machine-learning-retrain-a-classic-web-service.md)

[Retrain a New Web service using the Machine Learning Management cmdlets](machine-learning-retrain-new-web-service-using-powershell.md) (Reformer un nouveau service web à l’aide des applets de commande de gestion Machine Learning)

<!-- Retrain a New Web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0928_2016-->