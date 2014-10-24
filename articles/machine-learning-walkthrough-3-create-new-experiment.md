<properties title="Step 3: Create a new Azure Machine Learning experiment" pageTitle="Step 3: Create a new Machine Learning experiment | Azure" description="Step 3: Create a new training experiment in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

Vous voici arrivé à la troisième étape du didacticiel pas à pas [Développement d'une solution de prévision avec Azure ML][Développement d'une solution de prévision avec Azure ML] :

1.  [Création d'un espace de travail ML][Création d'un espace de travail ML]
2.  [Téléchargement de données existantes][Téléchargement de données existantes]
3.  **Création d'une expérience**
4.  [Formation et évaluation des modèles][Formation et évaluation des modèles]
5.  [Publication du service web][Publication du service web]
6.  [Accès au service web][Accès au service web]

------------------------------------------------------------------------

# Étape 3 : Création d'une expérience Azure Machine Learning

Nous devons créer une expérience dans ML Studio, qui utilise le jeu de données que nous avons téléchargé.

1.  Dans ML Studio, cliquez sur **+NOUVEAU** en bas de la fenêtre.
2.  Sélectionnez **EXPÉRIENCE**.
3.  Dans la palette des modules à gauche du canevas d'expérience, développez **Jeux de données enregistrés**.
4.  Recherchez le jeu de données que vous avez créé et faites-le glisser sur le canevas. Vous pouvez également le rechercher en entrant son nom dans la zone **Rechercher** au-dessus de la palette.

## Préparation des données

Vous pouvez voir les 100 premières lignes de données et quelques informations statistiques concernant tout le jeu de données en cliquant avec le bouton droit sur le port de sortie du jeu de données et en sélectionnant **Visualiser**. Notez que ML Studio a déjà identifié le type de données pour chaque colonne. Il a également donné des en-têtes génériques aux colonnes, car le fichier de données n'en avait pas.

Les en-têtes de colonne ne sont pas essentiels, mais ils facilitent l'utilisation des données dans le modèle. En outre, lors de la publication de ce modèle dans un service web, les en-têtes permettent à l'utilisateur du service d'identifier les colonnes.

Nous pouvons ajouter des en-têtes de colonne en utilisant le module **Éditeur de métadonnées**.

1.  Dans la palette des modules, tapez « métadonnées » dans la zone **Rechercher**. L'**Éditeur de métadonnées** est affiché dans la liste des modules.
2.  Cliquez sur le module **Éditeur de métadonnées** et faites-le glisser sur le canevas avant de le déposer sous le jeu de données.
3.  Connectez le jeu de données et l'**Éditeur de métadonnées** : cliquez sur le port de sortie du jeu de données, faites-le glisser sur le port d'entrée de l'**Éditeur de métadonnées**, puis relâchez le bouton de la souris. Le jeu de données et le module restent connectés même si vous opérez des déplacements sur le canevas.
4.  Dans le volet **Propriétés** à droite du canevas, cliquez sur **Lancer le sélecteur de colonne**.
5.  Vérifiez que l'option « Toutes les colonnes » est sélectionnée dans la liste déroulante, et cliquez sur **OK**. Cela indique à l'**Éditeur de métadonnées** de fonctionner sur toutes les colonnes de données.
6.  Pour le paramètre **Nouveau nom de colonne**, entrez une liste de noms pour les 21 colonnes du jeu de données, séparés par des virgules et dans l'ordre de la colonne. Vous pouvez obtenir le nom des colonnes dans la documentation du jeu de données sur le site web UCI ou, par commodité, vous pouvez copier et coller ce qui suit :

    Statut du compte de vérification, Durée en mois, Historique de crédit, Objectif, Montant du crédit, Compte/Obligation d'épargne, Emploi actuel depuis, Taux de versement en pourcentage de revenus disponibles, Statut personnel et genre, Autres débiteurs, Résidence actuelle depuis, Propriété, Âge en années, Autres plans de versement, Habitation, Nombre de crédits en cours, Poste, Nombre de personnes à qui vous fournissez de la maintenance, Téléphone, Travailleur étranger, Risque de crédit

Le volet Propriétés ressemble à ceci :

![Properties for Metadata Editor][Properties for Metadata Editor]

> Conseil : pour vérifier les en-têtes de colonne, lancez l'expérience (cliquez sur **EXÉCUTER** sous le canevas d'expérience), cliquez avec le bouton droit sur le port de sortie du module **Éditeur de métadonnées** et sélectionnez **Visualiser**. Vous pouvez voir la sortie de tous les modules en procédant de même pour afficher la progression des données dans l'expérience.

L'expérience doit ressembler à ceci :

![Adding Metadata Editor][Adding Metadata Editor]

## Création de jeux de données d'apprentissage et de test

L'étape suivante de l'expérience consiste à générer des jeux de données séparés qui serviront pour l'apprentissage et le test de notre modèle. Pour ce faire, nous utilisons le module **Fractionner**.

1.  Recherchez le module **Fractionner**, faites-le glisser sur le canevas, et connectez-le au dernier module **Éditeur de métadonnées**.
2.  Par défaut, le rapport de division est 0,5 et le paramètre **Fractionnement aléatoire** est défini. Cela signifie qu'une moitié aléatoire des données est sortie par un port du module **Fractionner**, et l'autre moitié par l'autre port. Vous pouvez ajuster ces paramètres, de même que le paramètre **Valeur de départ aléatoire**, pour changer la répartition entre les données d'apprentissage et de notation. Pour cet exemple, nous ne changeons rien.

    > Conseil : le rapport de division détermine essentiellement la quantité de données sortie par le port de sortie gauche. Par exemple, si vous définissez le rapport sur 0,7, 70 % des données sont sorties par le port gauche et 30 % par le port droit.

Nous pouvons utiliser les sorties du module **Fractionner** à notre gré, mais choisissons la sortie gauche pour les données d'apprentissage et la sortie droite pour les données de notation.

Comme indiqué sur le site web UCI, le coût d'une erreur consistant à classer un crédit à risque élevé comme étant à faible risque est 5 fois plus élevé que celui de l'erreur consistant à classer un crédit à faible risque comme étant à risque élevé. Pour tenir compte de cela, nous générons un nouveau jeu de données qui reflète cette fonction de coût. Dans le nouveau jeu de données, chaque exemple élevé est répliqué 5 fois, alors que les exemples faibles ne sont pas répliqués.

Nous pouvons procéder à la réplication en utilisant le code R :

1.  Recherchez et faites glisser le module **Exécuter un script R** vers le canevas d'expérience et connectez-le au port de sortie gauche du module **Fractionner**.
2.  Dans le volet **Propriétés**, supprimez le texte par défaut du paramètre **Script** et entrez le script suivant :

        dataset1 <- maml.mapInputPort(1)
        data.set<-dataset1[dataset1[,21]==1,]
        pos<-dataset1[dataset1[,21]==2,]
        for (i in 1:5) data.set<-rbind(data.set,pos)
        maml.mapOutputPort("data.set")

Nous devons répéter cette opération de réplication pour chaque sortie du module **Fractionner** pour que les données d'apprentissage et de notation aient le même ajustement des coûts.

1.  Cliquez avec le bouton droit sur le module **Exécuter un script R** et sélectionnez **Copier**.
2.  Cliquez avec le bouton droit sur le canevas d'expérience et sélectionnez **Coller**.
3.  Connectez ce module **Exécuter un script R** au port de sortie droit du module **Fractionner**.

> Conseil : la copie du module Exécuter un script R contient le même script que le module d'origine. Lorsque vous copiez-collez un module sur le canevas, la copie conserve toutes les propriétés de l'original.
>
> À ce stade, notre expérience ressemble à cela :

![Adding Split module and R scripts][Adding Split module and R scripts]

  [Développement d'une solution de prévision avec Azure ML]: ../machine-learning-walkthrough-develop-predictive-solution/
  [Création d'un espace de travail ML]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Téléchargement de données existantes]: ../machine-learning-walkthrough-2-upload-data/
  [Formation et évaluation des modèles]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Publication du service web]: ../machine-learning-walkthrough-5-publish-web-service/
  [Accès au service web]: ../machine-learning-walkthrough-6-access-web-service/
  [Properties for Metadata Editor]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
  [Adding Metadata Editor]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
  [Adding Split module and R scripts]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
