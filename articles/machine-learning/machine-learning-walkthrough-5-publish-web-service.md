<properties
	pageTitle="Étape 5 : Déploiement du service web Machine Learning | Microsoft Azure"
	description="Étape 5 de la procédure pas à pas de développement d’une solution prédictive : Déploiement d’une expérience prédictive en tant que service web dans Machine Learning Studio."
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
	ms.date="11/04/2015"
	ms.author="garye"/>


# Étape 5 du didacticiel pas à pas : Déploiement du service web Azure Machine Learning

Vous voici arrivé à la cinquième étape de la procédure pas à pas, [Développement d’une solution de prévision avec Azure ML](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Créer un espace de travail Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.	[Créer une expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**Déployer le service web**
6.	[Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

----------

Pour que ce modèle prédictif soit utile à d'autres personnes, nous le déploierons comme service web sur Azure.

Jusqu’à présent, nous avons réalisé l’expérience avec la formation de notre modèle. Mais le service déployé n’effectuera plus l’apprentissage, il produira des prédictions en fonction de l'entrée de l'utilisateur. Par conséquent, nous allons effectuer certaines tâches de préparation, puis déployer cette expérience en tant que service web opérationnel accessible par les utilisateurs. Un utilisateur pourra envoyer au service un ensemble de données d’application sur le crédit : le service renverra la prédiction du risque sur le crédit.

Pour cela, nous devons :

- Convertir l’*expérience de formation* que nous avons créée en *expérience prédictive*
- Déploiement de l'expérience prédictive sous la forme d'un service web

Mais tout d’abord, nous devons réduire un peu cette expérience. Nous disposons actuellement de deux modèles différents dans l’expérience, mais nous devons en sélectionner un seul à déployer.

Supposons que nous ayons décidé que le modèle Arbre de décision optimisé est le plus adapté. La première chose à faire est de supprimer le module [Machine à vecteur de support à deux classes][two-class-support-vector-machine], ainsi que les modules qui ont été utilisés pour sa formation. Vous pouvez d'abord copier l'expérience en cliquant sur **Enregistrer sous** dans la partie inférieure de la zone de dessin.

Nous devons supprimer les modules suivants :

1.	[Machine à vecteurs de support à deux classes][two-class-support-vector-machine]
2.	Modules associés [Former le modèle][train-model] et [Noter le modèle][score-model]
3.	[Normaliser les données][normalize-data] (les deux)
4.	[Évaluer le modèle][evaluate-model]

Nous sommes maintenant prêts à déployer ce modèle.

## Convertir l'expérience de formation en expérience prédictive

La conversion en expérience prédictive se déroule en trois étapes :

1. Enregistrer le modèle que nous avons formé et remplacer nos modules de formation par ce modèle
2. Réduire l’expérience en supprimant les modules uniquement nécessaires à l’apprentissage
3. Définir l’emplacement des nœuds d’entrée et de sortie du service web

Heureusement, vous pouvez accomplir ces trois étapes en cliquant simplement sur **Déployer le service web** dans la partie inférieure de la zone de dessin de l’expérience (sélectionnez l’option **Service web prédictif**).

Lorsque vous cliquez sur **Déployer le service web**, plusieurs choses se produisent :

- Le modèle dont nous avons effectué l’apprentissage est enregistré en tant que module **Modèle entraîné** dans la palette de module située à gauche de la zone de dessin de l’expérience (vous pouvez le trouver dans la palette sous **Modèles entraînés**).
- Les modules qui ont été utilisés pour l’apprentissage sont supprimés. Plus précisément :
  - [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree]
  - [Former le modèle][train-model]
  - [Fractionner][split]
  - le second module [Exécuter le script R][execute-r-script] qui a été utilisé pour les données de test
- Le modèle formé enregistré est ajouté à l’expérience.
- Les modules **Entrée du service web** et **Sortie du service web** sont ajoutés.

> [AZURE.NOTE]L’expérience a été enregistrée en deux parties : l’expérience de formation d’origine et la nouvelle expérience prédictive. Vous pouvez y accéder à l’aide des onglets situés dans la partie supérieure du canevas des expériences.

Nous devons effectuer une étape supplémentaire avec notre expérience. Machine Learning Studio a supprimé un module [Exécuter le script R][execute-r-script] lors de la suppression du module [Fractionner][split], mais il reste l'autre module [Exécuter le script R][execute-r-script]. Étant donné que ce module a été utilisé uniquement pour l'apprentissage et le test (il a fourni une fonction de pondération sur les échantillons de données), nous pouvons maintenant le supprimer et connecter [Éditeur de métadonnées][metadata-editor] à [Noter le modèle][score-model].

Notre expérience doit alors ressembler à cela :

![Scoring the trained model][4]


Vous vous demandez peut-être pourquoi nous avons laissé le jeu de données Données de carte de crédit allemande UCI dans l’expérience prédictive. Ce service va utiliser les données de l'utilisateur et non le jeu de données d'origine : pourquoi les laisser connectés ?

Il est vrai que ce service n'a pas besoin des données de la carte de crédit d'origine. Mais il a besoin du schéma pour ces données, incluant des informations telles que le nombre de colonnes et lesquelles sont numériques. Ces informations sur le schéma sont indispensables pour interpréter les données de l'utilisateur. Nous laissons ces composants connectés de façon à ce que le module de notation comporte le schéma du jeu de données lorsque le service est en cours d'exécution. Les données ne sont pas utilisées, uniquement le schéma.

Exécutez une dernière fois l'expérience (cliquez sur **Exécuter**). Si vous voulez vérifier que le modèle fonctionne toujours, cliquez sur la sortie du module [Noter le modèle][score-model] et sélectionnez **Afficher les résultats**. Vous constatez que les données d'origine sont affichées, ainsi que la valeur du risque sur le crédit (« Étiquettes notées ») et la probabilité de la notation (« Probabilités notées »).

## Déploiement du service web

Pour déployer un service web dérivé de notre expérience, cliquez sur **Déployer le service web** sous la zone de dessin. Machine Learning Studio déploie l'expérience en tant que service web et vous amène au tableau de bord du service.

> [AZURE.TIP]Vous pouvez mettre à jour le service web après l’avoir déployé. Par exemple, si vous souhaitez modifier votre modèle, modifiez simplement l'expérience de formation, ajustez les paramètres du modèle et cliquez sur **Déployer le service web**. Lorsque vous redéploierez l'expérience, elle remplacera le service web en utilisant alors votre modèle mis à jour.

Vous pouvez configurer le service en cliquant sur l'onglet **CONFIGURATION**. Vous pouvez modifier le nom du service (il s'agit par défaut du nom de l'expérience) et lui attribuer une description. Vous pouvez également attribuer des étiquettes plus significatives aux colonnes d'entrée et de sortie.

![Configuration du service web][5]

## Test du service web
Sur la page **TABLEAU DE BORD**, cliquez sur le lien **Test** sous **Point de terminaison par défaut**. Une boîte de dialogue vous demande d’entrer les données du service. Les colonnes sont identiques à celles du jeu de données d'origine du risque de crédit allemand. ![Test du service web][6]


Entrez un jeu de données, puis cliquez sur **OK**.

Les résultats générés par le service web sont affichés au bas du tableau de bord. D'après la manière dont nous avons configuré le service, les résultats affichés sont générés par le module de notation.


----------

**Étape suivante : [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/

<!---HONumber=Nov15_HO2-->