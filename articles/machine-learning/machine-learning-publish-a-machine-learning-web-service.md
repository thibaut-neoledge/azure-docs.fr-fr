<properties
	pageTitle="Déploiement d’un service web Machine Learning | Microsoft Azure"
	description="Comment convertir une expérience de formation en expérience prédictive, la préparer en vue de son déploiement, puis la déployer en tant que service web Azure Machine Learning."
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
	ms.date="09/09/2015"
	ms.author="garye"/>

# Déploiement d’un service web Azure Machine Learning

Microsoft Azure Machine Learning vous permet de générer, tester et déployer des solutions d’analyse prédictive.

D'un point de vue très général, cela s'effectue en trois étapes :

- **[Créez une expérience d'apprentissage]** : Azure Machine Learning Studio est un environnement de développement visuel collaboratif qui vous permet d'effectuer l'apprentissage et de tester un modèle d'analyse prédictive à l'aide de données d'apprentissage que vous fournissez.
- **[Convertissez-la en expérience prédictive]** : une fois que vous avez effectué l'apprentissage de votre modèle avec des données existantes et que vous êtes prêt à l'utiliser pour la notation de nouvelles données, vous préparez et simplifiez votre expérience de notation.
- **[Déployez-la en tant que service web]** : en un seul clic, vous pouvez publier votre expérience de notation en tant que service web Azure. Les utilisateurs peuvent envoyer des données à votre modèle et recevoir des prédictions de votre modèle.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Une fois que vous avez publié le service web, vous pouvez :

- **[y accéder]** via l'API du service web
- **[le gérer]** via le portail de gestion Azure et
- **[le mettre à jour]** si vous modifiez votre modèle

[Créez une expérience d'apprentissage]: #create-a-training-experiment
[Convertissez-la en expérience prédictive]: #convert-the-training-experiment-to-a-predictive-experiment
[Déployez-la en tant que service web]: #deploy-the-predictive-experiment-as-a-web-service
[y accéder]: #access-the-web-service
[le gérer]: #manage-the-web-service-in-the-azure-management-portal
[le mettre à jour]: #update-the-web-service


## Création d'une expérience d'apprentissage

Pour effectuer l'apprentissage d'un modèle d'analyse prédictive, utilisez Azure Machine Learning Studio pour créer une expérience d'apprentissage dans laquelle vous incluez plusieurs modules pour charger des données d'apprentissage, préparer les données en fonction de vos besoins, appliquer des algorithmes d'apprentissage automatique et évaluer les résultats. Vous pouvez effectuer une itération sur une expérience et essayer des algorithmes d'apprentissage automatique différents pour comparer et évaluer les résultats.

Le processus de création et gestion d'expériences d'apprentissage est traité de manière plus approfondie dans d'autres sections. Consultez les articles suivants pour plus d'informations et des exemples :

- [créez une expérience simple dans Azure Machine Learning Studio](machine-learning-create-experiment.md)
- [développez une solution prédictive avec Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)
- [importez vos données d'apprentissage dans Azure Machine Learning Studio](machine-learning-import-data.md)
- [gérez des itérations d'expériences dans Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md)

## Convertir l'expérience de formation en expérience prédictive

Une fois que vous avez effectué l'apprentissage de votre modèle, vous êtes prêt à l'utiliser pour la notation de nouvelles données. Pour cela, convertissez votre expérience de formation en expérience prédictive. En la convertissant en expérience prédictive, vous préparez votre modèle entraîné à être publié en tant que service web de notation. Les utilisateurs du service web envoient des données d’entrée à votre modèle, qui leur renvoie les résultats de sa prédiction. Par conséquent, lorsque vous convertissez l’expérience en expérience prédictive, vous devez tenir compte du mode d’utilisation de votre modèle par les autres utilisateurs.

Pour convertir votre expérience de formation en expérience prédictive, cliquez sur **Exécuter** en bas de la zone de dessin de l’expérience, puis cliquez sur **Configurer le service web**.

![Convertir une expérience de notation](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Pour plus d'informations sur la façon d'effectuer cette conversion, consultez [Conversion d'une expérience de formation Machine Learning en expérience prédictive](machine-learning-convert-training-experiment-to-scoring-experiment.md)


## Déploiement de l'expérience prédictive sous la forme d'un service web

Maintenant que l’expérience prédictive est correctement préparée, vous pouvez la publier en tant que service web Azure. En accédant au service web, les utilisateurs peuvent envoyer des données à votre modèle, qui renvoie alors ses prédictions.

Pour publier votre expérience prédictive, cliquez sur **Exécuter** en bas de la zone de dessin de l'expérience, puis cliquez sur **Déployer le service web**. Le service web est configuré et vous êtes placé dans le tableau de bord du service web.

![Déploiement du service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Pour tester le service web, cliquez sur le lien **Test** dans le tableau de bord du service web. Une boîte de dialogue s'affiche pour vous demander les données d'entrée du service. Il s'agit des colonnes attendues par l'expérience de notation. Entrez un jeu de données, puis cliquez sur **OK**. Les résultats générés par le service web sont affichés au bas du tableau de bord.

![Test du service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

Sur l'onglet **CONFIGURATION**, vous pouvez modifier le nom d'affichage du service et lui donner une description. Le nom et la description s'affichent dans le portail de gestion Azure où vous gérez vos services web. Vous pouvez fournir une description de vos données d'entrée, de vos données de sortie et des paramètres de service web en saisissant une chaîne pour chaque colonne sous **SCHÉMA D'ENTRÉE**, **SCHÉMA DE SORTIE** et **PARAMÈTRE DU SERVICE WEB**. Ces descriptions sont utilisées dans la documentation relative à l'exemple de code fournie pour le service web. Vous pouvez également activer la journalisation pour diagnostiquer toute défaillance que vous observez lors de l’accès à votre service web.

Pour plus d'informations, consultez [Activation de la journalisation pour les services web de Machine Learning](machine-learning-web-services-logging.md).

![Configuration du service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)


## Accès au service web

Une fois que vous avez déployé votre service web à partir de Machine Learning Studio, vous pouvez envoyer des données au service et recevoir des réponses par programme.

Le tableau de bord fournit toutes les informations dont vous avez besoin pour accéder à votre service web. Par exemple, la clé API est fournie afin de permettre un accès autorisé au service et des pages d'aide API sont fournies pour vous aider à commencer à écrire votre code.

Pour plus d'informations sur l'accès à un service web Machine Learning, consultez [Comment consommer un service web Azure Machine Learning publié](machine-learning-consume-web-services.md).


## Gestion du service web dans le portail de gestion Azure

Dans le portail de gestion Azure, vous pouvez gérer vos services web en cliquant sur le service **Machine Learning**, en ouvrant votre espace de travail Machine Learning puis en ouvrant le service web à partir de l'onglet **SERVICES WEB**. À partir de cette page, vous pouvez contrôler le service web, le mettre à jour et le supprimer. Vous pouvez également ajouter un deuxième point de terminaison pour votre service web en plus du point de terminaison par défaut qui est créé lorsque vous le publiez.

Pour plus d'informations, consultez [Gestion d'un espace de travail Azure Machine Learning](machine-learning-manage-workspace.md).
<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->


## Mise à jour du service web

Vous pouvez apporter des modifications à votre service web, par exemple mettre à jour le modèle avec des données d'apprentissage supplémentaires et le publier à nouveau, en remplaçant le service web d'origine.

Pour mettre à jour le service web, ouvrez l'expérience prédictive d'origine utilisée pour déployer le service web et effectuez une copie modifiable en cliquant sur **ENREGISTRER SOUS**. Effectuez vos modifications, puis cliquez sur **Déployer le service web**. Étant donné que vous avez déjà publié cette expérience, Machine Learning Studio vous demande si vous souhaitez remplacer le service existant. Si vous cliquez sur **OUI**, le service web existant est arrêté et la nouvelle expérience prédictive est publiée à la place.

> [AZURE.NOTE]Si vous avez apporté des modifications de configuration dans le service web d'origine, par exemple, si vous saisissez un nouveau nom d'affichage ou une description, vous devrez saisir ces valeurs à nouveau.

Pour mettre à jour votre service web, vous pouvez reformer le modèle à l’aide d’un programme. Pour plus d’informations, consultez la page [Nouvel apprentissage des modèles Machine Learning par programme](machine-learning-retrain-models-programmatically.md).

<!---HONumber=Oct15_HO3-->