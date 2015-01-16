<properties title="Step 5: Publish the Azure Machine Learning web service" pageTitle="Étape 5 : publication du service web Machine Learning | Azure" description="Étape 5 : publication d'une expérience de notation en tant que service web d'API ML dans Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


Vous voici arrivé à la cinquième étape du didacticiel pas à pas, [Développement d'une solution de prévision avec Azure ML][develop] :

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Créer un espace de travail ML][create-workspace]
2.	[Télécharger les données existantes][upload-data]
3.	[Créer une nouvelle expérience][create-new]
4.	[Former et évaluer les modèles][train-models]
5.	**Publication du service web**
6.	[Accéder au service web][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Étape 5 : publication du service web Azure Machine Learning

Pour que ce modèle prédictif soit utile à d'autres personnes, nous le publions comme service web sur Azure. Un utilisateur pourra envoyer au service un ensemble de données d'application sur le crédit : le service renverra la prédiction du risque sur le crédit.  

Pour cela, nous devons :  

-	préparer l'expérience pour la publication ;
-	la publier sur un serveur intermédiaire où nous pouvons la tester ;
-	la transférer sur le serveur en activité lorsque nous pensons qu'elle est prête.  

Avant de continuer, vous devez créer une copie de cette expérience à modifier. Vous pouvez ainsi revenir à votre expérience d'apprentissage à tout moment pour continuer à travailler sur vos modèles.  

1.	Cliquez sur **Enregistrer sous** sous la zone de dessin.
2.	Donnez à la copie un nom significatif. Par défaut, " - Copy " est ajouté au nom d'origine de l'expérience. Dans notre cas, appelons-la " Prédiction du risque sur le crédit - Expérience de notation ".
3.	Cliquez sur **OK**.  


L'expérience d'origine et la copie sont affichées dans la liste Expériences de ML Studio.  

![Experiments list][1]
 
##Préparation de l'expérience de notation
Nous devons réaliser deux tâches pour préparer la publication de notre modèle comme service web.  

D'abord, nous devons convertir l'expérience d'*expérience d'apprentissage* en *expérience de notation*. Jusqu'à présent, nous avons réalisé l'expérience avec la formation de notre modèle. Mais le service publié ne va plus être formé ; il va noter l'entrée de l'utilisateur. Nous allons donc enregistrer une copie du modèle que nous avons formé et supprimer tous les composants de l'expérience qui étaient dédiés à la formation.  

Ensuite, le service web Azure ML acceptera une entrée de l'utilisateur et renverra un résultat : nous devons donc identifier ces points d'entrée et de sortie dans notre expérience.  

###Conversion d'une expérience d'apprentissage en expérience de notation
Supposons que nous ayons décidé que le modèle Arbre de décision optimisé est le plus adapté. La première chose à faire est donc de supprimer les modules de formation SVM.  

1.	Supprimez le module **Machine à vecteurs de support à deux classes**
2.	Supprimez le module **Former le modèle** ainsi que les modules S**core Model** qui y sont connectés
3.	Supprimez le module **Transformer les données par mise à l'échelle**  

Nous allons maintenant enregistrer le modèle d'arbre de décision optimisé que nous avons formé. Nous pouvons à présent supprimer les modules restants de l'expérience que nous avons utilisés et les remplacer par le modèle formé.  

1.	Cliquez avec le bouton droit sur le port de sortie du module restant **Former le modèle** et sélectionnez **Enregistrer comme modèle formé**.
2.	Entrez un nom et une description pour le modèle formé. Pour cet exemple, nous l'appelons " Prédiction du risque sur le crédit ".

	>**Remarque** : lorsque nous enregistrons ce modèle formé, il s'affiche dans la palette des modules et est utilisable dans d'autres expériences.

3.	Recherchez ce modèle dans la palette des modules : tapez " risque sur le crédit " dans le champ **Rechercher**, puis faites glisser le modèle formé **Prédiction du risque sur le crédit** sur le canevas de l'expérience.
4.	Supprimez les modules **Arbre de décision optimisé à deux classes** et **Former le modèle**.
5.	Connectez la sortie du modèle **Prédiction du risque sur le crédit** à l'entrée gauche du module **Noter le modèle**.   

Nous avons alors la version enregistrée et formée du modèle de notre expérience à la place des modules de formation d'origine.  

Il y a plus de composants dans l'expérience, que nous avons ajoutés pour former et évaluer nos deux algorithmes de modèles. Nous pouvons également les supprimer :  

-	**Fractionner**
-	Les deux modules **Exécuter le script R**
-	**Évaluer le modèle**  

Autre chose : la carte de crédit d'origine contenait la colonne Risque sur le crédit. Nous avons passé cette colonne dans le module **Former le modèle** afin qu'il puisse former le modèle pour que celui-ci soit en mesure de prédire ces valeurs. Maintenant que le modèle a été formé, nous ne voulons pas continuer à passer cette colonne : le modèle formé prédira cette valeur pour nous. Pour supprimer cette colonne du flux de données, nous utilisons le module **Colonne de projet**.  

1.	Recherchez et faites glisser le module **Colonne de projet** sur le canevas.
2.	Connectez ce module à la sortie du module **Éditeur de métadonnées** (maintenant que les modules **Fractionner** et **Exécuter le script R** sont supprimés)
3.	Sélectionnez le module **Colonnes de projet** et cliquez sur **Lancer le sélecteur de colonne**.
4.	Laissez " Toutes les colonnes " dans la liste déroulante.
5.	Cliquez sur le signe (+) pour créer une ligne de liste déroulante.
6.	Dans cette nouvelle liste déroulante, sélectionnez " Exclure les noms des colonnes " et entrez " Risque sur le crédit " dans le champ de texte (vous pouvez également spécifier la colonne par son numéro, à savoir 21).
7.	Cliquez sur **OK**.

	>Astuce : le sélecteur de colonnes respecte la logique des listes déroulantes dans l'ordre où elles apparaissent. Dans ce cas, nous avons dirigé le module **Colonnes de projet** pour " passer toutes les colonnes à l'exception de la colonne 'Risque sur le crédit' ". Si nous avions omis la première liste déroulante, le module ne passerait aucune colonne.

8.	Connectez la sortie du module **Colonnes de projet** à l'entrée droite du module **Noter le modèle**.  

Notre expérience doit alors ressembler à cela :  

![Scoring the trained model][2]  

###Sélection de l'entrée et de la sortie du service
Dans le modèle d'origine, les données à noter ont été passées au port d'entrée de droite (" Jeu de données ") du module **Noter le modèle** et le résultat noté est affiché dans le port de sortie (" Jeu de données noté "). Lorsque le service est en cours d'exécution, nous voulons que les données de l'utilisateur et les résultats utilisent les mêmes ports.  

1.	Cliquez avec le bouton droit sur le port d'entrée de droite du module **Noter le modèle** et sélectionnez **Définir comme entrée de publication**. Les données de l'utilisateur doivent inclure toutes les données du vecteur de caractéristique.

	>**Astuce :** si vous devez manipuler les données de l'utilisateur avant de les passer au module de notation (de la même manière que nous avons utilisé le module **Transformer les données par mise à l'échelle** pour préparer les données pour le modèle SVM), laissez simplement le module dans le service web et configurez l'entrée du service sur le port d'entrée de ce module.

2.	Cliquez avec le bouton droit sur le port de sortie et sélectionnez **Définir comme sortie de publication**. Le service renvoie la sortie du module Noter le modèle. Cela comprend le vecteur de caractéristique, la prédiction du risque sur le crédit et la probabilité de la notation.

	>**Astuce :** si vous voulez que le service web renvoie uniquement une partie de ces données (ex. si vous ne voulez pas renvoyer l'ensemble du vecteur de caractéristique), vous pouvez ajouter le module **Colonnes de projet** après le module **Noter le modèle**, le configurer pour exclure les colonnes que vous ne voulez pas, puis configurer ensuite la sortie du module **Colonnes de projet** comme sortie du service web.  
  

>**Remarque :** vous vous demandez peut-être pourquoi nous avons laissé le jeu de données UCI German Credit Card Data et les modules associés connectés au module **Noter le modèle**. Ce service va utiliser les données de l'utilisateur et non le jeu de données d'origine : pourquoi les laisser connectés ?

Il est vrai que ce service n'a pas besoin des données de la carte de crédit d'origine. Mais il a besoin du schéma pour ces données, incluant des informations telles que le nombre de colonnes et lesquelles sont numériques. Ces informations sur le schéma sont indispensables pour interpréter les données de l'utilisateur. Nous laissons ces composants connectés de façon à ce que le module de notation comporte le schéma du jeu de données lorsque le service est en cours d'exécution. Les données ne sont pas utilisées, uniquement le schéma.  

Exécutez une dernière fois l'expérience (cliquez sur **EXÉCUTER**). Si vous voulez vérifier que le modèle fonctionne toujours, cliquez avec le bouton droit sur la sortie du module **Noter le modèle** et sélectionnez **Visualiser**. Vous constatez que les données d'origine sont affichées, ainsi que la valeur du risque sur le crédit (" Étiquettes notées ") et la probabilité de la notation (" Probabilités notées ").  

##Publication du service web
Pour publier un service web dérivé de notre expérience, cliquez sur **PUBLIER LE SERVICE WEB** sous le canevas et cliquez sur **OUI** lorsque vous y êtes invité. ML Studio publie l'expérience en tant que service web sur le serveur intermédiaire ML et vous amène au tableau de bord du service.   

>**Astuce :** vous pouvez mettre à jour le service web après l'avoir publié. Par exemple, si vous voulez modifier votre modèle, il suffit de modifier l'expérience d'apprentissage enregistrée précédemment, de régler les paramètres du modèle et d'enregistrer le modèle formé (en remplaçant celui que vous avez enregistré précédemment). Lorsque vous rouvrez l'expérience de notation, vous remarquez une note indiquant que quelque chose est modifié (votre modèle formé) ; vous pouvez mettre à jour l'expérience. Lorsque vous republiez l'expérience, elle remplace le service web en utilisant alors votre modèle mis à jour.  

Vous pouvez configurer le service en cliquant sur l'onglet **CONFIGURATION**. Vous pouvez modifier le nom du service (il s'agit par défaut du nom de l'expérience) et lui attribuer une description. Vous pouvez également attribuer des étiquettes plus significatives aux colonnes d'entrée et de sortie.  

Nous examinerons le commutateur **PRÊT POUR LA PRODUCTION ?** un peu plus tard.  

##Test du service web
Dans la page **TABLEAU DE BORD**, cliquez sur le lien **Test** sous **Services intermédiaires**. Une boîte de dialogue vous demande d'entrer les données du service. Les colonnes sont identiques à celles du jeu de données d'origine du risque sur le crédit allemand.  

Entrez un jeu de données, puis cliquez sur **OK**.  

Les résultats générés par le service web sont affichés au bas du tableau de bord. D'après la manière dont nous avons configuré le service, les résultats affichés sont générés par le module de notation.   

##Transfert du service web sur le serveur en activité
Jusqu'à présent, le service s'exécutait sur le serveur intermédiaire ML. Lorsque vous êtes prêt à le mettre en activité, vous pouvez demander qu'il soit promu sur le serveur en activité.  

Sous l'onglet **CONFIGURATION**, cliquez sur " OUI " à côté de **PRÊT POUR LA PRODUCTION ?** Cette action signale à votre administrateur informatique que ce service web est prêt à entrer en activité. L'administrateur peut alors le transférer sur le serveur en activité.

![Promoting the service to the live environment][3]  

----------

**Suivant : [Accéder au service web][access-ws]**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
