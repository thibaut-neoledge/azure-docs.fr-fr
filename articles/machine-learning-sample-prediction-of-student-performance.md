<properties title="Azure Machine Learning Sample: Prediction of student performance" pageTitle="Machine Learning Sample: Predict student performance | Azure" description="A sample Azure Machine Learning experiment to develop a model that predicts student performance on tests." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Exemple Azure Machine Learning : prédiction des performances d'étudiants

*Vous pouvez retrouver l'exemple d'expérience associé à ce modèle dans la section **EXPÉRIENCE** de l'onglet **EXEMPLES**. Le nom de l'expérience est :*

    Sample Experiment - Student Performance - Development

## Description du jeu de données et du problème

Dans cette expérience, notre jeu de données est le jeu d'apprentissage Algebra\_2008\_2009 de la KDD Cup 2010. Il peut être téléchargé à l'adresse [][]<https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp></a>. Il contient les fichiers journaux du système d'apprentissage des étudiants. Les caractéristiques fournies incluent l'identifiant du problème avec une description sommaire, l'identifiant de l'étudiant, un horodatage et le nombre de tentatives de résolution du problème avant la réussite. Le problème d'apprentissage consiste à déterminer si un étudiant donné va résoudre un problème spécifique dès sa première tentative. Conformément aux règles de la KDD Cup, nous mesurons l'exactitude des algorithmes d'apprentissage en utilisant la [Erreur quadratique moyenne][Erreur quadratique moyenne] (RMSE). Le jeu de données d'origine contient 8,9 millions d'enregistrements. Pour accélérer l'expérience, nous l'avons limité aux 100 000 premières lignes. Le jeu de données contient 23 colonnes de différents types : numérique, catégorie et horodatage. Les colonnes sont séparées par tabulation.

## Flux de travail de développement

Le jeu de données contient des caractères non-ASCII qui ne peuvent pas être gérés par le module **Appliquer une opération R**. Avant d'utiliser un jeu de données dans Passau, nous avons supprimé les caractères non-ASCII au moyen des commandes Powershell suivantes :

    gc algebra_2008_2009_train.txt –head 100000 | sc algebra_train_small.txt
    sc tmp.txt -Encoding ASCII -Value (gc algebra_train_small.txt)
    cat tmp.txt | %{$_ -replace "\?\?sqrt","+sqrt"} | sc algebra_train_small.txt_ascii  

Le fichier algebra\_train\_small.txt\_ascii qui en résulte reste volumineux, avec presque 36 Mo. Nous le stockons dans le stockage d'objets blob Azure avant de le charger dans l'expérience en utilisant le module **Lecteur**. Les commandes Powershell permettant de stocker le fichier dans le stockage d'objets blob sont les suivantes :

    Add-AzureAccount
    $key = Get-AzureStorageKey -StorageAccountName <your storage account name>
    $ctxt = New-AzureStorageContext -StorageAccountName $key.StorageAccountName -StorageAccountKey $key.Primary
    Set-AzureStorageBlobContent –Container <container name in your storage account> -File "algebra_train_small.txt_ascii" –Context $ctxt

![][]

Les paramètres du module **Lecteur** sont indiqués ci-dessus. Dans cet exemple, le nom du compte de stockage est « datascience » et le fichier du jeu de données algebra\_train\_small.txt\_ascii est placé dans le conteneur « sampleexperiments ». La clé de compte est une clé d'accès du compte de stockage Azure. Elle peut être récupérée sur votre compte sur le portail de gestion Azure ([][1]<https://manage.windowsazure.com></a>).

![][2]

Au cours des prochaines étapes, indiquées précédemment, nous réalisons un certain nombre de transformations pour passer le jeu de données dans un format qui correspond à nos algorithmes d'apprentissage. Nous utilisons l'**Éditeur de métadonnées** pour convertir la colonne d'horodatage « First Transaction Time » au format de chaîne. Cette opération est nécessaire pour générer la répartition formation/test. Ensuite, au moyen de **Colonnes de projet**, nous supprimons un certain nombre de colonnes qui ne serviront pas dans l'expérience. Nous utilisons le Programme de nettoyage des valeurs manquantes pour remplacer toutes les valeurs manquantes par 0. Au cours de l'étape suivante, nous divisons la colonne « Unit Name, Section Name » en deux colonnes, « Unit Name » et « Section Name ». Pour cela, nous utilisons le code R suivant dans le module **Exécuter le script R** :

    dataset <- maml.mapInputPort(1)
    b <- data.frame(do.call(rbind,strsplit(as.vector(dataset[,3]),",")))
    names(b) <- c("Unit Name","Section Name")
    data.set <- cbind(dataset[,1:2],b,dataset[,4:15])
    maml.mapOutputPort("data.set")  

Les premières étapes de l'expérience, qui incluent le chargement des données et les transformations initiales, sont indiquées ci-dessus. Après avoir manipulé les données, nous divisons l'expérience en 4 branches. Dans chacune, nous testons un jeu de caractéristiques différent. Certains de nos jeux de caractéristiques ont déjà été utilisés par [1][1]. Dans la première branche, décrite ci-dessous, notre jeu de caractéristiques comprend les fonctions StudentID, Unit

![][3]

![][4]

Name, Section Name, Problem Name, Problem View et Opportunity, et la description textuelle du problème. Les caractéristiques non entières sont représentées comme des fonctions de catégorie. Dans cette branche, nous commençons par supprimer la colonne Hints en utilisant **Colonnes de projet**, car la colonne Hints n'est pas disponible pour prédire la réussite d'un nouvel étudiant. Nous utilisons ensuite le module **Fractionner** pour diviser le jeu de données en jeu d'apprentissage et jeu de test. Comme tous les exemples ont un horodatage, la répartition est basée sur l'heure. Toutes les lignes avec une heure First Transaction Time qui commence en 2008 entrent dans le jeu d'apprentissage, et celles qui commencent en 2009 entrent dans le jeu de test. Les paramètres du module **Fractionner** sont indiqués à droite. Après le fractionnement, nous générons un modèle de classification binaire en utilisant un arbre de décision optimisé et nous notons les données de test. L'intitulé de la colonne dans le module **Apprentissage du modèle** est Correct First Attempt.

![][5]

Dans la séquence suivante des modules **Appliquer une opération mathématique** et **Statistiques élémentaires**, présentée ci-dessus, nous calculons l'erreur quadratique moyenne (RMSE) du modèle en utilisant les notes brutes et les vrais intitulés. Notez que pour les modèles de régression, cette mesure est calculée par le module **Évaluateur**. Pour les autres modèles, nous devons faire le calcul manuellement. Dans le premier module **Appliquer une opération mathématique**, nous calculons la différence entre la colonne des intitulés et celle des notes générée par le module **Noter le modèle**. Dans le deuxième module **Appliquer une opération mathématique**, nous calculons le carré de cette différence. Le module **Statistiques élémentaires** calcule la moyenne des différences au carré. Pour terminer, le dernier module **Appliquer une opération mathématique** permet de calculer la racine carrée. Les paramètres de ces quatre modules sont présentés ci-dessous.

![][6]

![][7]


![][8]


![][9]

Dans la deuxième branche, présentée ci-dessus, nous utilisons la dimension de temps. En plus des caractéristiques de la première branche, nous utilisons les noms des deux dernières étapes du problème actuellement résolu par l'utilisateur avec leur description. Dans le jeu de données fourni, toutes les activités de l'utilisateur sont stockées dans l'ordre croissant de l'horodatage. Également, dans le jeu de données fourni, les activités ne s'entrecroisent pas : il y a d'abord toutes les lignes du premier utilisateur, puis toutes celles du deuxième, etc. Enfin, pour trouver les dernières étapes de l'utilisateur, nous utilisons la colonne RowID. Pour un utilisateur fixe, cette colonne sert d'axe du temps. En ajoutant 1 à cette colonne, au moyen du module Appliquer une opération mathématique\*\*\*\*, nous décalons chaque ligne d'une unité de temps suivante. Au moyen du module **Joindre**, nous joignons le jeu de données d'origine et le jeu décalé en utilisant RowID, StudentID et ProblemName comme clés. Le résultat est un jeu de données développé dans lequel chaque ligne contient un enregistrement des heures t et t-1 pour les mêmes StudentID et ProblemName. Nous utilisons *Left Outer Join* pour conserver les mêmes StudentID et ProblemID pour les lignes qui n'ont pas d'étape précédente. Nous appliquons encore une fois la combinaison des modules **Appliquer une opération mathématique** et **Joindre** pour obtenir les caractéristiques de deux étapes plus haut. Les paramètres exacts des modules **Appliquer une opération mathématique** et **Joindre** sont présentés ci-dessous.

![][10]

![][11]

![][12]

Après deux exécutions, nous obtenons un nombre identique de colonnes. Par exemple, en raison de notre utilisation du module Joindre, la colonne ProblemName est répliquée 3 fois, pour les étapes t, t-1 et t-2. Nous utilisons le module **Project Columns** pour supprimer les colonnes redondantes. Enfin, comme nous avons utilisé *Left Outer Join*, il peut manquer des valeurs à certaines lignes générées par l'opérateur Join. Nous utilisons le **Programme de nettoyage des valeurs manquantes** pour remplacer toutes les valeurs manquantes par la chaîne « 0 ». Les paramètres du **Programme de nettoyage des valeurs manquantes** sont présentés ci-dessus.

Après le calcul du nouveau jeu de caractéristiques, le flux de travail de la deuxième branche est identique à celui de la première.

![][13]

Dans la troisième branche, en plus des caractéristiques utilisées dans la deuxième branche, nous utilisons les fonctionnalités quadratique et cubique d'origine de la première branche. Ces caractéristiques quadratique et cubique sont calculées en utilisant le module **Exécuter une opération R** avec le code R présenté ci-dessus. Après le calcul du nouveau jeu de caractéristiques, nous effectuons la formation, la notation et l'évaluation comme dans les deux premières branches.

Dans la quatrième branche, nous utilisons des caractéristiques complètement différentes des trois premières branches. Pour chaque StudentID, nous calculons la valeur moyenne de Correct First Attempt jusqu'à l'heure t (non incluse). Nous notons cette valeur CFA(StudentID,t). De même, nous notons Hints(StudentID,t) la valeur moyenne de Hints pour un StudentID fixe jusqu'à l'heure t (non incluse). Pour accélérer le calcul de ces moyennes, nous ne tenons pas compte du jeu de données complet, mais uniquement des 10 enregistrements les plus récents avant l'heure t. Les définitions de CFA(Problem Name,t), CFA(Step Name,t), CFA(Description,t), Hints(Problem Name,t), Hints(Step Name,t) et Hints(Description,t) sont similaires. À partir d'un exemple x avec First Transaction Time=t(x) et les valeurs de colonnes StudentID(x), Problem Name(x), Step Name(x) et Description(x), nous générons les 8 caractéristiques CFA et Hints ci-dessus :

    CFA(StudentID(x),t(x)), CFA(Problem Name(x),t(x)), CFA(Step Name(x),t(x)), CFA(Description(x),t(x)), 
    Hints(StudentID(x),t(x)), Hints(Problem Name(x),t(x)), Hints(Step Name(x),t(x)), Hints(Description(x),t(x))  

![][14]

De même, nous calculons 8 caractéristiques CFA et Hints supplémentaires en utilisant les concaténations de StudentID et Problem Name, Problem Name et Step Name, StudentId et UnitName, StudentID et Problem Description. En tout, nous obtenons 16 caractéristiques utilisées pour prédire la valeur de la colonne Correct First Attempt. Le calcul de ces 16 caractéristiques est effectué avec le code R dans le module **Exécuter une opération R**. Ce code est long et fastidieux, mais optimisé. Après le calcul de ces caractéristiques, nous supprimons des colonnes auxiliaires ajoutées par le code R. Pour cela, nous utilisons le module **Colonnes de projet**. Le flux de travail complet du calcul des caractéristiques dans la quatrième branche est présenté ci-dessus. Après le calcul du nouveau jeu de caractéristiques, nous effectuons la formation, la notation et l'évaluation comme dans les trois premières branches.

Après le calcul des valeurs RMSE dans les quatre branches, nous collectons les résultats en utilisant le module **Ajouter des lignes**. Nous générons des annotations en utilisant le module **Exécuter un script R**. Le flux de travail de cette dernière partie de l'expérience est présenté ci-dessous.

![][15]

Le résultat final de l'expérience est le tableau suivant, où la première colonne est le nom du jeu de caractéristiques et la deuxième ligne est la valeur RMSE qui a été mesurée dans les exemples de test :

![][16]

En conclusion, le quatrième jeu de caractéristiques donne la plus petite valeur RMSE.

## Références

H.-F. Yu et al. Feature Engineering and Classifier Ensemble for KDD Cup 2010. KDD Cup 2010 Workshop, 2010.

  []: https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp
  [Erreur quadratique moyenne]: http://en.wikipedia.org/wiki/Root-mean-square_deviation
  []: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-1.jpg
  [1]: https://manage.windowsazure.com
  [2]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-2.jpg
  [3]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-3.jpg
  [4]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-4.jpg
  [5]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-5.jpg
  [6]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-6.jpg
  [7]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-7.jpg
  [8]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-8.jpg
  [9]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-9.jpg
  [10]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-10.jpg
  [11]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-11.jpg
  [12]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-12.jpg
  [13]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-13.jpg
  [14]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-14.jpg
  [15]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-15.jpg
  [16]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-16.jpg
