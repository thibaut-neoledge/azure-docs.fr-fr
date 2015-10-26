<properties
	pageTitle="Étape 4 : formation et évaluation des modèles d’analyse prédictive | Microsoft Azure"
	description="Étape 4 de la procédure pas à pas du développement d’une solution prédictive : formation, notation et évaluation des multiples modèles dans Azure Machine Learning Studio."
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
	ms.date="09/08/2015"
	ms.author="garye"/>


# Étape 4 de la procédure pas à pas : formation et évaluation des modèles d’analyse prédictive

Voici la quatrième étape de la procédure pas à pas [Développement d’une solution de prévision avec Azure ML](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Créer un espace de travail Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.	[Créer une expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.	**Former et évaluer les modèles**
5.	[Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

----------

Dans cette expérience, nous allons essayer différents algorithmes sur notre modèle de prévision. Nous allons créer deux types de modèles, puis comparer les résultats d'évaluation afin de décider l'algorithme que nous utiliserons dans notre expérience finale.

Nous pouvons choisir parmi de nombreux modèles. Pour connaître les modèles disponibles, développez le nœud **Machine Learning** dans la palette des modules, puis développez **Initialiser le modèle** et les nœuds inférieurs. Pour cette expérience, nous allons sélectionner les modules Machines à vecteurs de support (Support Vector Machine, SVM) et Arbres de décision optimisés à deux classes. Nous allons utiliser les modules adaptés pour initialiser les algorithmes d'apprentissage et utiliser les modules [Former le modèle][train-model] pour former les modèles.

##Formation des modèles
Configurons d'abord le modèle Arbre de décision optimisé :

1.	Recherchez le module [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree] dans la palette des modules et faites-le glisser sur le canevas.
2.	Recherchez le module [Former le modèle][train-model], faites-le glisser sur le canevas et connectez la sortie du module Arbre de décision optimisé vers le port d'entrée de gauche (« Modèle non formé ») du module [Former le modèle][train-model].
3.	Connectez la sortie gauche (« Jeu de données du résultat ») du module [Exécuter le script R][execute-r-script] de gauche au port d'entrée à droite (« Jeu de données ») du module [Former le modèle][train-model].

	> [AZURE.TIP]Nous n’avons pas besoin de deux des entrées et de l’une des sorties du module [Exécuter le script R][execute-r-script] pour cette expérience ; nous les laisserons donc sans liaison. Cela est courant pour certains modules.


4.	Sélectionnez le module [Former le modèle][train-model]. Dans le panneau **Propriétés**, cliquez sur **Lancer le sélecteur de colonne**, sélectionnez **Inclure** dans la première liste déroulante, sélectionnez **Index des colonnes** dans la deuxième et entrez « 21 » dans le champ textuel (vous pouvez également sélectionner **noms des colonnes** et entrer « Risque de crédit »). Cela identifie la colonne 21, qui correspond à la valeur de risque du crédit, comme colonne de prédiction du modèle.


Cette partie de l'expérience ressemble alors à ce qui suit :

![Training a model][1]

Nous allons ensuite configurer le modèle SVM.

Les arbres de décision optimisés fonctionnent bien avec tout type de caractéristique. Toutefois, le module SVM générant un classifieur linéaire, le modèle qu'il génère obtient la meilleure erreur de test quand toutes les caractéristiques numériques sont à la même échelle. Pour convertir toutes les caractéristiques numériques à la même échelle, nous utilisons le module [Normaliser les données][normalize-data] avec une transformation Tanh qui convertit les caractéristiques dans la plage [0,1]. Notez que les caractéristiques de chaîne sont converties en caractéristiques catégoriques par le module SVM, puis en caractéristiques 0/1 binaires. Il n'est donc pas nécessaire de les transformer manuellement. De même, nous ne voulons pas transformer la colonne Risque du crédit (colonne 21). Elle est numérique et contient la valeur que nous apprenons à prédire au modèle ; nous devons donc la laisser seule.

1.	Recherchez le module [Machine à vecteurs de support à deux classes][two-class-support-vector-machine] dans la palette des modules et faites-le glisser sur le canevas.
2.	Cliquez avec le bouton droit sur le module [Former le modèle][train-model], sélectionnez **Copier**, puis cliquez avec le bouton droit sur le canevas et sélectionnez **Coller**. Notez que la copie du module [Former le modèle][train-model] comporte la même sélection de colonnes que l'original.
3.	Connectez la sortie du module SVM au port d'entrée gauche (« Modèle non formé ») du module [Former le modèle][train-model].
4.	Trouvez le module [Normaliser les données][normalize-data] et faites-le glisser vers le canevas.
5.	Connectez l'entrée de ce module de transformation à la sortie du module [Exécuter le script R][execute-r-script] de gauche.
6.	Connectez le port de sortie de gauche (« Jeu de données transformé ») du module de transformation au port d'entrée de droite (« Jeu de données ») du module [Former le modèle][train-model].
7.	Dans le volet **Propriétés** du module de transformation, sélectionnez **Tanh** comme paramètre de la **Méthode de transformation**.
8.	Cliquez sur **Lancer le sélecteur de colonne**, sélectionnez « Aucune colonne » pour **Commencer par**, sélectionnez **Inclure** dans la première liste déroulante, sélectionnez **type de colonne** dans la deuxième et sélectionnez **Numérique** dans la troisième. Cette action spécifie que toutes les colonnes numériques (et elles seules) seront transformées.
9.	Cliquez sur le signe (+) pour créer une ligne de listes déroulantes. Sélectionnez **Exclure** dans la première liste déroulante, sélectionnez **index de colonnes** dans la seconde et entrez « 21 » dans le champ textuel. Cette action spécifie que la colonne 21 (Risque du crédit) est ignorée.
10.	Cliquez sur **OK**.  


Le module [Normaliser les données][normalize-data] est maintenant configuré pour effectuer une transformation Tanh sur toutes les colonnes numériques à l’exception de la colonne Risque de crédit.

Cette partie de l'expérience ressemble alors à ceci :

![Training the second model][2]

##Notation et évaluation des modèles
Nous allons utiliser les données d'évaluation séparées par le module **Fractionner** pour noter nos modèles formés. Nous pouvons ensuite comparer les résultats des deux modèles pour savoir lequel donne les meilleurs résultats.

1.	Recherchez le module [Noter le modèle][score-model] et faites-le glisser sur le canevas.
2.	Connectez le port d'entrée de gauche de ce module au modèle Arbre de décision optimisé (c.à.d. connectez-le au port de sortie du module [Former le modèle][train-model] connecté au module [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree]).
3.	Connectez le port d'entrée de droite du module [Noter le modèle][score-model] à la sortie du module [Exécuter le script R][execute-r-script] de droite.
4.	Copiez et collez le module [Noter le modèle][score-model] pour créer une deuxième copie ou faites glisser un nouveau module sur le canevas.
5.	Connectez le port d'entrée de gauche de ce module au modèle SVM (c.à.d. connectez-le au port de sortie du module [Former le modèle][train-model] connecté au module [Arbre de décision optimisé à deux classes][two-class-support-vector-machine]).
6.	Pour le modèle SVM, nous devons effectuer la même transformation pour tester les données comme nous l’avons fait pour les données de formation. Donc, copiez et collez le module [Normaliser les données][normalize-data] pour créer une autre copie et connectez-la à la sortie du module [Exécuter le script R][execute-r-script] de droite.
7.	Connectez le port d'entrée de droite du module [Noter le modèle][score-model] à la sortie du module [Normaliser les données][normalize-data].  

Pour évaluer les résultats notés, nous allons utiliser le module [Évaluer le modèle][evaluate-model].

1.	Recherchez le module [Évaluer le modèle][evaluate-model] et faites-le glisser sur le canevas.
2.	Connectez le port d'entrée de gauche au port de sortie du module [Noter le modèle][score-model] associé au modèle Arbre de décision optimisé.
3.	Connectez le port d'entrée de droite à l'autre module [Noter le modèle][score-model].  

L'expérience doit ressembler à ceci :

![Evaluating both models][3]

Cliquez sur le bouton **EXÉCUTER** sous le canevas pour exécuter l'expérience. Cette opération peut prendre quelques minutes. Un indicateur rotatif sur chaque module indique qu'il est en cours d'exécution ; une coche verte s'affiche ensuite pour indiquer que l'exécution est terminée.

Lorsque tous les modules comportent une coche, l'exécution de l'expérience est terminée. Pour examiner les résultats, cliquez sur le port de sortie du module [Évaluer le modèle][evaluate-model] et sélectionnez **Afficher les résultats**.

Le module [Évaluer le modèle][evaluate-model] produit une paire de courbes et de mesures qui permettent de comparer les résultats des deux modèles notés. Vous pouvez afficher les résultats sous forme de courbes Receiver Operator Characteristic (ROC), Precision/Recall ou Lift. Les données supplémentaires affichées comprennent une matrice de confusion, les valeurs cumulées pour l’aire sous la courbe (ASC) et d’autres mesures. Vous pouvez modifier la valeur du seuil en déplaçant le curseur vers la gauche ou la droite et voir son influence sur l'ensemble des mesures.

Cliquez sur **Jeu de données noté** ou **Jeu de données noté à comparer** pour afficher en surbrillance la courbe associée et afficher les mesures associées en dessous. Dans la légende des courbes, « Jeu de données noté » correspond au port d'entrée de gauche du module [Évaluer le modèle][evaluate-model], dans notre cas, le modèle Arbre de décision optimisé. « Jeu de données noté à comparer » correspond au port d’entrée de droite, le modèle SVM dans notre cas. Lorsque vous cliquez sur une de ces étiquettes, vous affichez en surbrillance la courbe de ce modèle et les mesures correspondantes en dessous.

![ROC curves for models][4]

En examinant ces valeurs, vous pouvez décider quel modèle est le plus susceptible de fournir les résultats que vous recherchez. Vous pouvez revenir en arrière et recommencer votre expérience en modifiant les valeurs des différents modèles.

> [AZURE.TIP]À chaque exécution de l’expérience, un enregistrement de cet essai est conservé dans l’historique d’exécution. Vous pouvez afficher tous les essais de votre expérience (et y revenir) en cliquant sur **AFFICHER L'HISTORIQUE D'EXÉCUTION**, sous le canevas. Vous pouvez également cliquer sur **Exécution précédente** dans le volet **Propriétés** pour revenir à l'exécution précédant immédiatement celle que vous avez ouverte. Pour en savoir plus, consultez la page [Gérer les itérations des expériences dans Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md).

Vous pouvez également faire une copie d'un essai de votre expérience en cliquant sur **ENREGISTRER SOUS** sous le canevas. Vous dupliquez ainsi l'expérience et créez un nouvel historique d'exécution pour suivre vos essais sur cette version. La nouvelle copie est affichée dans la liste **EXPÉRIENCES** à côté de l'original. Cela est utile si vous voulez commencer une nouvelle série d'essais pour cette expérience.

Comme aide supplémentaire pour suivre les modifications que vous apportez aux paramètres du module, vous pouvez ajouter des commentaires à n'importe quel module du canevas de l'expérience. Il suffit de double-cliquer sur le module ou de cliquer avec le bouton droit et de sélectionner **Modifier le commentaire**. Ces commentaires sont enregistrés avec les essais de l'expérience et vous aident à documenter votre travail.


----------

**Étape suivante : [déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/

<!---HONumber=Oct15_HO3-->