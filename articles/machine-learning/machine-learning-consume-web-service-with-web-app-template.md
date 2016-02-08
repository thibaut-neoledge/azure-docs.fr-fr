<properties
	pageTitle="Utilisation d’un service Web Machine Learning à l’aide d’un modèle d’application Web | Microsoft Azure"
	description="Utilisez un modèle d’application Web dans Azure Marketplace pour exploiter un service Web prédictif dans Azure Machine Learning."
	keywords="service Web, opérationnalisation, API REST, apprentissage automatique"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="garye;raymondl"/>

# Utilisation d’un service Web Microsoft Azure Machine Learning à l’aide d’un modèle d’application Web

Une fois vous avez développé votre modèle de prévision et l’avez déployé en tant que service web Azure à l’aide de Machine Learning Studio ou à l’aide d’outils comme R ou Python, vous pouvez accéder au modèle opérationnalisé à l’aide d’une API REST.

Il existe plusieurs moyens d’utiliser l’API REST et d’accéder au service Web. Vous pouvez par exemple écrire une application en C#, R ou Python à l’aide de l’exemple de code généré lors du déploiement du service Web (disponible dans Machine Learning Studio, sur la page d’aide de l’API dans le tableau de bord du service Web). Vous pouvez aussi utiliser l’exemple de classeur Microsoft Excel créé pour vous (également disponible dans le tableau de bord du service Web dans Studio).

Mais le moyen le plus rapide et le plus simple d’accéder à votre service web consiste à utiliser les modèles d’application web disponibles dans [Azure Web App Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Modèles d’applications Web de Microsoft Azure Machine Learning

Les modèles d’applications Web disponibles dans Azure Marketplace peuvent générer une application Web personnalisée qui connaît les données d’entrée et les résultats attendus de votre service Web. Il vous suffit de donner à l’application Web l’accès à votre service Web et aux données associées, et le modèle fait le reste.

Il existe deux modèles :

- [Modèle d’application Web Azure ML Request-Response Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Modèle d’application Web Azure ML Batch Execution Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Chaque modèle crée un exemple d’application ASP.NET, en utilisant l’URI et la clé de l’API correspondant à votre service Web, et le déploie en tant que site Web dans Azure. Le modèle Request-Response (RRS) crée une application Web qui vous permet d’envoyer une seule ligne de données au service Web afin d’obtenir un résultat unique. Le modèle Batch Execution Service (BES) crée une application Web qui vous permet d’envoyer un grand nombre de lignes de données de manière à obtenir plusieurs résultats.

Aucun code n’est nécessaire pour utiliser ces modèles. Vous devez simplement spécifier l’URI et la clé de l’API pour permettre au modèle de générer automatiquement l’application.

## Comment utiliser le modèle Request-Response Service (RRS)

Après avoir déployé votre service Web, vous pouvez suivre la procédure ci-dessous pour utiliser le modèle d’application Web RRS illustré dans le schéma suivant.

![Procédure d’utilisation du modèle Web RSS][image1]

1. Dans Machine Learning Studio, ouvrez l’onglet **Services Web**, puis ouvrez le service web auquel vous souhaitez accéder. Copiez la clé répertoriée sous **Clé de l’API** et enregistrez-la.

	![Clé d’API][image3]

2. Ouvrez la page d’aide de l’API **REQUEST/RESPONSE**. En haut de la page d’aide, sous **Requête**, copiez et enregistrez la valeur **URI de requête**. Exemple de valeur :

		https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

	![URI de demande][image4]

3. Accédez à [Portail Azure](https://portal.azure.com), **Connexion**, cliquez sur **Nouveau**, recherchez et sélectionnez **Azure ML Request-Response Service Web App**, puis cliquez sur **Créer**.

	- Donnez un nom unique à votre application Web. L’URL de l’application web sera ce nom suivi de `.azurewebsites.net.` (par exemple, `http://carprediction.azurewebsites.net.`).

	- Sélectionnez l’abonnement Azure et les services sous lesquels est exécuté votre service Web.

	- Cliquez sur **Create**.

	![Créer une application web][image5]

4. Une fois le déploiement de l’application web terminé, cliquez sur l’**URL** sur la page des paramètres de l’application web dans Azure, ou entrez l’URL dans un navigateur web. Par exemple, `http://carprediction.azurewebsites.net.`

5. À la première exécution de l’application web, vous êtes invité à renseigner l’**URL de publication de l’API** et la **clé de l’API**. Entrez les valeurs que vous avez enregistrées précédemment :
	- **URI de requête** de la page d’aide de l’API pour l’**URL de publication de l’API**
	- **Clé de l’API** du tableau de bord de service web pour la **clé de l’API**.

	Cliquez sur **Envoyer**.

	![Entrer l’URI de publication et la clé de l’API][image6]

6. L’application web affiche la page **Configuration de l’application web** avec les paramètres du service web actif. Vous pouvez ici modifier les paramètres utilisés par l’application Web.

	> [AZURE.NOTE] Une modification des paramètres à ce stade n’affecte que l’application Web concernée. Les paramètres par défaut de votre service Web ne seront pas modifiés. Par exemple, si vous modifiez ici la **Description**, l’opération n’aura aucun effet sur la description affichée sur le tableau de bord du service web dans Machine Learning Studio.

	Quand vous avez terminé, cliquez sur **Enregistrer les modifications**, puis cliquez sur **Atteindre la page de démarrage**.

7. Dans la page d’accueil, vous pouvez saisir les valeurs à envoyer à votre service web et cliquer sur **Envoyer** pour obtenir le résultat.

Si vous souhaitez revenir à la page **Configuration**, accédez à la page `setting.aspx` de l’application web. Par exemple : `http://carprediction.azurewebsites.net/setting.aspx.`. Vous serez invité à saisir de nouveau la clé de l’API pour pouvoir accéder à la page et mettre à jour les paramètres.

Vous pouvez arrêter, redémarrer ou supprimer l’application web dans le portail Azure Classic comme n’importe quelle autre application web. Tant qu’elle est en cours d’exécution, vous pouvez accéder à l’adresse Web de base et saisir les nouvelles valeurs.

## Comment utiliser le modèle Batch Execution Service (BES)

Vous pouvez utiliser le modèle d’application Web BES de la même manière que le modèle RRS, à ceci près que l’application Web créée vous permettra d’envoyer plusieurs lignes de données et de recevoir plusieurs résultats.

Les résultats d’un service Web d’exécution par lot sont stockés dans un conteneur de stockage Azure ; les valeurs d’entrée peuvent provenir du stockage Azure ou d’un fichier local. Vous aurez donc besoin d’un conteneur de stockage Azure pour stocker les résultats renvoyés par l’application Web. Vous devrez également préparer vos données d’entrée.

![Procédure d’utilisation du modèle Web BES][image2]

1. Pour créer l’application Web BES, suivez la même procédure que celle utilisée pour le modèle RRS, à l’exception de ce qui suit :
	- Récupérez l’**URI de requête** dans la page d’aide de l’API **EXÉCUTION PAR LOTS** correspondant au service web.
	- Accédez à [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) pour ouvrir le modèle BES sur Azure Marketplace et cliquez sur **Créer une application web**.

2. Pour spécifier l’emplacement de stockage des résultats, indiquez les informations du conteneur de destination sur la page d’accueil de l’application Web. Indiquez également l’emplacement d’où l’application Web pourra extraire ses valeurs d’entrée, à savoir dans un fichier local ou dans un conteneur de stockage Azure. Cliquez sur **Envoyer**.

	![Informations sur le stockage][image7]

L’application Web affiche une page avec l’état de la tâche. Une fois la tâche terminée, vous obtiendrez l’emplacement des résultats dans le stockage d’objets blob Azure. Vous avez également la possibilité de télécharger les résultats dans un fichier local.

## Pour plus d’informations

Pour en savoir plus sur...

- la création d’une expérience d’apprentissage automatique avec Machine Learning Studio, consultez [Création de votre première expérience dans Azure Machine Learning Studio](machine-learning-create-experiment.md).

- le déploiement de votre expérience d’apprentissage automatique sous la forme d’un service web, consultez [Déploiement d’un service web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

- d’autres manières d’accéder à votre service web, consultez [Utilisation d’un service web Azure Machine Learning](machine-learning-consume-web-services.md).


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png

<!---HONumber=AcomDC_0128_2016-->