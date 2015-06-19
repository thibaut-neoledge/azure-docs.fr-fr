<properties 
	pageTitle="Convertir une expérience d'apprentissage Machine Learning en expérience de notation | Azure" 
	description="Découvrez comment convertir une expérience d'apprentissage Machine Learning, utilisée pour l'apprentissage du modèle d'analyse prédictive, en expérience de notation, qui peut être publiée en tant que service web." 
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
	ms.date="02/10/2015" 
	ms.author="garye"/>

#Convertir une expérience d'apprentissage Machine Learning en expérience de notation

Microsoft Azure Machine Learning vous permet de générer, tester et déployer des solutions d'analyse prédictive. 

Une fois que vous avez créé et effectué l'itération sur une *training experiment* pour gérer le modèle d'analyse prédictive et que vous êtes prêt à l'utiliser pour noter les nouvelles données, vous devez préparer et rationaliser votre expérience à des fins de notation. Vous pouvez ensuite publier cette *scoring experiment* en tant que service web Microsoft Azure, afin que les utilisateurs puissent envoyer des données à votre modèle et recevoir les prédictions de ce dernier.

En convertissant une expérience de notation, vous préparez votre modèle formé à être publié en tant que service web de notation. Les utilisateurs du service web envoient des données d'entrée à votre modèle, qui leur renvoie les résultats de sa prédiction. Par conséquent, lorsque vous convertissez l'expérience en expérience de notation, vous devez tenir compte du mode d'utilisation de votre modèle par les autres utilisateurs.

Le processus de conversion d'une expérience d'apprentissage en expérience de notation comprend trois étapes :

1.	Enregistrez le modèle ML que vous avez formé, puis remplacez l'algorithme ML et les modules **Train Model** par le modèle formé enregistré.
2.	Découpez l'expérience pour ne garder que les modules nécessaires au calcul des notations. Une expérience d'apprentissage inclut un certain nombre de modules nécessaires pour l'apprentissage, mais qui ne sont plus requis une fois le modèle formé et prêt à servir lors du calcul des notations.
3.	Définissez à quel niveau de votre expérience vous voulez accepter les données de l'utilisateur du service web et choisissez le type des données qui seront renvoyées.

##Bouton Créer une expérience de notation

Le bouton **Créer une expérience de notation**, situé sur la partie inférieure de la zone de dessin de l'expérience, exécute pour vous les trois étapes de conversion de l'expérience d'apprentissage en expérience de notation :

1.	Il enregistre votre modèle formé en tant que module dans la section **Modèles formés** de la palette du module (située à gauche de la zone de dessin de l'expérimentation), puis remplace l'algorithme ML et les modules **Train Model** par le module formé enregistré. 
2.	Il supprime les modules qui ne sont pas nécessaires. Dans notre exemple, cela inclut le module **Split**, le deuxième module **Score Model** et le module **Evaluate Model**.
3.	Il crée les modèles d'entrée et de sortie du service web et les ajoute aux emplacements par défaut prévus dans votre expérience.

Par exemple, l'expérience suivante effectue l'apprentissage d'un modèle d'arborescence de décision augmenté incluant deux classes, au moyen des données de recensement :

![Training experiment][figure1]

Les modules de cette expérience effectuent quatre fonctions de base :

![Module functions][figure2]

Lorsque vous convertissez cette expérience d'apprentissage en expérience de notation, certains de ces modules ne sont plus nécessaires ou présentent un objectif différent :

- **Data** : les données de cet exemple de jeu de données ne sont pas utilisées pour le calcul de la notation ; l'utilisateur du service web fournit les données à noter. Toutefois, les métadonnées de ce jeu de données, comme les types de données, sont utilisées par le modèle formé. Vous devez donc conserver le jeu de données dans l'expérience de notation, afin qu'il puisse fournir ces métadonnées.

- **Prep** : selon les données qui seront soumises pour le calcul de la notation, il se peut que ces modules soient ou non requis pour le traitement des données entrantes. 

	Par exemple, l'exemple de jeu de données indiqué ici peut présenter des valeurs manquantes ; il inclut des colonnes qui ne sont pas nécessaires pour former le modèle. Par conséquent, un module **Clean Missing Data** a été fourni pour gérer les valeurs manquantes et un autre module, **Project Columns**, permet d'exclure ces colonnes supplémentaires du flux de données. Si vous savez que les données qui seront soumises à des fins de calcul de la notation via le service web ne présentent aucune valeur manquante, vous pouvez retirer le module **Clean Missing Data**. Toutefois, étant donné qu'il permet de définir l'ensemble de fonctionnalités qui sont notées, le module **Project Columns** doit être conservé.

- **Train** : une fois le modèle formé, vous pouvez l'enregistrer en tant que module unique de modèle formé. Vous devez ensuite remplacer ces modules individuels par le modèle formé enregistré.

- **Score** : dans cet exemple, le module Split est utilisé pour diviser le flux de données en un ensemble de données de test, d'une part, et un ensemble de données d'apprentissage, d'autre part. Dans l'expérience de notation, ce module n'est pas nécessaire et peut être supprimé. De même, le deuxième module **Score Model** et le module **Evaluate Model** sont utilisés pour comparer les résultats à partir des données de test. Ils ne sont donc pas requis pour l'expérience de notation. Le module **Score Model** restant est cependant requis pour renvoyer le résultat de la notation via le service web.

Voici comment notre exemple apparaît une fois que vous avez cliqué sur **Créer une expérience de notation** :	

![Converted scoring experiment][figure3]

Cela peut être suffisant pour préparer votre expérience à être publiée en tant que service web. Toutefois, certaines tâches supplémentaires, spécifiques à votre expérience, seront peut-être à prévoir.

###Ajuster des modules d'entrée et de sortie

Dans votre expérience d'apprentissage, vous avez utilisé un ensemble de données d'apprentissage, puis effectué un certain traitement pour obtenir les données au format requis par l'algorithme ML. Si les données que vous vous attendez à recevoir via le service web n'ont pas besoin d'être soumises à ce traitement, vous pouvez déplacer le **module d'entrée du service web** vers un autre nœud dans votre expérience.

Par exemple, le bouton **Créer une expérience de notation** place par défaut le **module d'entrée du service web** en haut de votre flux de données, comme indiqué dans la figure ci-dessus. Toutefois, si les données d'entrée n'ont pas besoin d'être soumises à ce traitement, vous pouvez placer manuellement l'**entrée du service web** après les modules de traitement des données :

![Moving the web service input][figure4]

Les données d'entrée fournies via le service web accéderont directement au module Score Model, sans être soumises à un traitement préalable.

De même, le bouton **Créer une expérience de notation** place par défaut le module Web service output au bas du flux de données. Dans cet exemple, le service web renvoie à l'utilisateur la sortie du module **Score Model**, qui inclut le vecteur de données d'entrée complet, ainsi que les résultats de la notation.

Toutefois, si vous souhaitez renvoyer d'autres données (par exemple, les résultats de la notation sans le vecteur de données d'entrée complet), vous pouvez insérer un module **Project Columns** pour exclure toutes les colonnes, sauf celle des résultats de la notation. Ensuite, déplacez le module **Web Service Output** vers la sortie du module **Project Columns** :

![Moving the web service output][figure5]

###Ajouter ou supprimer des modules de traitement des données supplémentaires

Si votre expérience inclut un plus grand nombre de modules que nécessaire pour le calcul de la notation, vous pouvez en supprimer. Par exemple, étant donné que nous avons déplacé le module **Web service input** vers un point situé après les modules de traitement des données, nous pouvons supprimer le module **Clean Missing Data** de l'expérience de notation. 

Notre expérience de notation ressemble à présent à ce qui suit :

![Removing additional module][figure6]

###Ajouter des paramètres de service web facultatifs

Dans certains cas, vous voudrez permettre à l'utilisateur de votre service web de modifier le comportement des modules en cas d'accès au service. Les *Web Service Parameters* vous permettent d'y parvenir.

Un exemple courant consiste à configurer le module **Reader**, afin que l'utilisateur du service web publié puisse spécifier une autre source de données lors de l'accès au service web. Il est également possible de configurer le module **Writer** de façon à spécifier une destination différente. 

Vous pouvez définir des paramètres de service web et les associer à un ou plusieurs paramètres de module, en spécifiant s'ils sont obligatoires ou facultatifs. L'utilisateur du service web peut ensuite fournir des valeurs pour ces paramètres lors de l'accès au service ; les actions de module seront modifiées en conséquence.

Pour en savoir plus sur les paramètres de service web, consultez la section [Utilisation des paramètres de service web de Microsoft Azure Machine Learning
][webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


##Publier l'expérience de notation sous la forme d'un service web

Maintenant que l'expérience de notation est correctement préparée, vous pouvez la publier en tant que service web Microsoft Azure. En accédant au service web, les utilisateurs peuvent envoyer des données à votre modèle, qui renvoie alors ses prédictions.

Pour en savoir plus sur le processus de publication complet, consultez la section [Publier un service web Microsoft Azure Machine Learning][publish]

[publish]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!--HONumber=49--> 