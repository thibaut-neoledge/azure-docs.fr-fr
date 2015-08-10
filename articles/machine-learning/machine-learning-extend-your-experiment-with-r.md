<properties
	pageTitle="Prolongez votre expérience avec R | Microsoft Azure"
	description="Comment étendre les fonctionnalités d'Azure Machine Learning Studio en utilisant le langage R et le module Exécuter le script R."
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
	ms.date="04/29/2015"
	ms.author="garye" />


#Prolongez votre expérience avec R 

Vous pouvez étendre les fonctionnalités de ML Studio grâce au langage R en utilisant le module [Exécuter le script R][execute-r-script].

Ce module accepte plusieurs jeux de données d'entrée et génère un jeu de données unique en sortie. Vous pouvez taper un script R dans le paramètre **Script R** du module [Exécuter le script R][execute-r-script].

Pour accéder à chaque port d'entrée du module, utilisez un code semblable au suivant :

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Liste de tous les packages actuellement installés

La liste des packages installés peut changer. Pour obtenir la liste complète, insérez les lignes suivantes dans le module [Exécuter le script R][execute-r-script] pour envoyer la liste au jeu de données de sortie :

    out <- data.frame(installed.packages())
    maml.mapOutputPort("out")

Pour afficher la liste des packages, connectez un module de conversion comme [Convertir en CSV][convert-to-csv] à la sortie du module [Exécuter le script R][execute-r-script] puis exécutez le test. Cliquez ensuite sur la sortie du module de conversion et sélectionnez **Télécharger**.

##Importation de packages

Vous pouvez également importer des packages qui ne sont pas déjà installés à partir d'un référentiel ML Studio intermédiaire à l'aide des commandes suivantes dans le module [Exécuter le script R][execute-r-script], ainsi que dans l'archive du package zippé :

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

où `my_favorite_package.zip` contient le fichier zip de votre package.

##Liste des packages installés

Par commodité, la liste des packages inclus dans la version actuelle est fournie dans le tableau suivant.

Pour obtenir la liste complète des packages actuellement disponibles, consultez la section **Liste de tous les packages actuellement installés** ci-dessus. La documentation R actuelle est disponible [ici](http://cran.r-project.org/manuals.html).

- [Modules R commençant par les lettres A à E]
- [Modules R commençant par les lettres F à L]
- [Modules R commençant par les lettres M à R]
- [Modules R commençant par les lettres S à Z]

[Modules R commençant par les lettres A à E]: #r-modules-beginning-with-a-through-e
[Modules R commençant par les lettres F à L]: #r-modules-beginning-with-f-through-l
[Modules R commençant par les lettres M à R]: #r-modules-beginning-with-m-through-r
[Modules R commençant par les lettres S à Z]: #r-modules-beginning-with-s-through-z


###Modules R commençant par les lettres A à E

| Nom du package | Description du package |
| ------------ | ------------------- |
| abc | Outils de calcul bayésien approximatif (ABC) |
| abind | Combinaison de tableaux multidimensionnels |
| actuar | Fonctions actuarielles |
| ade4 | Analyse de données écologiques : méthodes exploratoires et euclidiennes en sciences environnementales |
| AdMit | Mélange adaptatif de distributions de Student t |
| aod | Analyse de données très dispersées |
| ape | Analyses de la phylogénétique et de l'évolution |
| approximator | Prédiction bayésienne de codes informatiques complexes |
| arm | Analyse des données à l'aide de modèles de régression et de modèles à plusieurs niveaux/hiérarchiques |
| arules | Exploration de règles d'association et jeux d'éléments fréquents |
| arulesViz | Visualisation de règles d'association et jeux d'éléments fréquents |
| ash | Routines ASH de David Scott |
| assertthat | Pré et post-assertions faciles |
| AtelieR | Interface utilisateur graphique GTK pour enseigner les concepts fondamentaux de l'inférence statistique et tests bayésiens élémentaires |
| BaBooN | Correspondance moyenne prédictive d'amorçage bayésienne : imputation multiple et unique pour données discrètes |
| BACCO | Analyse bayésienne de sortie de code d'ordinateur (BACCO) |
| BaM | Fonctions et jeux de données pour livres de Jeff Gill |
| bark | Noyaux de régression additifs bayésiens |
| BAS | Moyenne de modèle bayésien avec échantillonnage adaptatif bayésien |
| base | Package de Base R |
| BayesDA | Fonctions et jeux de données pour le livre Bayesian Data Analysis |
| bayesGARCH | Estimation bayésienne du modèle GARCH(1,1) avec innovations Student-t |
| bayesm | Inférence bayésienne pour marketing/micro-économétrie |
| bayesmix | Modèles de mélange bayésien avec JAGS |
| bayesQR | Régression quantile bayésienne |
| bayesSurv | Régression de survie bayésienne avec erreur flexible et distributions d'effets aléatoires |
| Bayesthresh | Modèles d'effets mixtes de seuils bayésiens pour données catégorielles |
| BayesTree | Méthodes bayésiennes pour modèles basés sur une arborescence |
| BayesValidate | Package BayesValidate |
| BayesX | Utilitaires R accompagnant le package logiciel BayesX |
| BayHaz | Fonctions R pour l'estimation du taux de risque bayésien |
| bbemkr | Estimation de la bande passante bayésienne pour la régression du noyau multicritère avec erreur gaussienne |
| BCBCSF | Classification bayésienne de correction de décalage avec fonctionnalités sélectionnées |
| BCE | Estimateur de composition bayésien : estimation de la composition (taxonomique) d'échantillon pour données de biomarqueur |
| bclust | Clustering bayésien utilisant un modèle hiérarchique de type spike-and-slab, compatible avec le clustering de données de grande dimension |
| bcp | Package pour réaliser une analyse bayésienne de problèmes de point de modification |
| BenfordTests | Tests statistiques pour évaluer la conformité de la loi de Benford |
| bfp | Polynômes fractionnels bayésiens |
| BH | Améliorer les fichiers d'en-tête C++ |
| bisoreg | Régression isotonique bayésienne avec Polynômes de Bernstein |
| bit | Classe de vecteurs de booléens de 1 bit |
| bitops | Opérations de bit à bit |
| BLR | Régression linéaire bayésienne |
| BMA | Moyenne de modèle bayésien |
| Bmix | Échantillonnage bayésien pour mélanges de ruptures de tige |
| BMS | Bibliothèque de moyenne de modèle bayésien |
| bnlearn | Apprentissage de la structure réseau bayésienne, apprentissage des paramètres et inférence |
| boa | Programme d'analyse de sortie bayésienne (BOA) pour MCMC |
| Bolstad | Fonctions Bolstad |
| boot | Fonctions d'amorçage (à l'origine par Angelo Canty pour S) |
| bootstrap | Fonctions pour le livre An Introduction to the Bootstrap |
| bqtl | Kit de ressources de mappage QTL bayésien |
| BradleyTerry2 | Modèles Bradley-Terry |
| brew | Infrastructure de développement de modèles pour la génération de rapports |
| brglm | Réduction de décalage en modèles linéaires généralisés de réponse binomiale |
| bspec | Inférence de spectre bayésien |
| bspmma | bspmma : modèles semi-paramétriques bayésiens pour l'analyse de métadonnées |
| BVS | Bayesian Variant Selection : techniques incertaines du modèle bayésien pour études d'association génétique |
| cairoDevice | Pilote d'appareil graphique avec anticrénelage multiplate-forme basé sur Cairo |
| calibrator | Étalonnage bayésien de codes informatiques complexes |
| car | Guide de régression appliquée |
| caret | Apprentissage de la classification et de la régression |
| catnet | Inférence réseau bayésienne catégorielle |
| caTools | Outils : statistiques de déplacement de la fenêtre, GIF, Base64, ROC ASC, etc. |
| chron | Objets chronologiques pouvant gérer des dates et des heures |
| class | Fonctions de classification |
| Hadoop | Analyse de cluster étendue Rousseeuw et al. |
| clusterSim | Recherche de procédure de clustering optimale pour un jeu de données |
| coda | Analyse et diagnostics de sortie pour MCMC |
| codetools | Outils d'analyse de code pour R |
| coin | Procédures d'inférence conditionnelle dans une infrastructure de développement de test de permutation |
| colorspace | Manipulation de l'espace colorimétrique |
| combinat | Utilitaires de combinatoires |
| compiler | Package du compilateur R |
| corpcor | Estimation efficace de la covariance et de la corrélation (partielle) |
| cslogistic | Régression logistique spécifique de façon conditionnelle |
| ctv | Vues des tâches CRAN |
| cubature | Intégration multicritère adaptative sur hypercubes |
| data.table | Extension de data.frame |
| datasets | Package de jeux de données R |
| date | Fonctions de gestion des dates |
| dclone | Clonage de données et outils MCMC pour méthodes de probabilité maximale |
| deal | Apprentissage des réseaux bayésiens avec variables mixtes |
| Deducer | Deducer : interface utilisateur graphique pour l'analyse des données pour R |
| DeducerExtras | Boîtes de dialogue et fonctions supplémentaires pour Deducer |
| deldir | Triangulation de Delaunay et tesselation de Dirichlet (Voronoi). |
| DEoptimR | Optimisation de l'évolution différentielle dans R pur |
| deSolve | Solutionneurs généraux pour les problèmes de valeur initiale d'équations dérivées ordinaires (ODE), équations aux dérivées partielles (PDE), équations algébriques dérivées (DAE) et équations dérivées avec retard (DDE) |
| devtools | Outils pour faciliter le développement de code R |
| dichromat | Thèmes de couleurs pour Dichromats |
| digest | Créer des résumés de hachage cryptographique d'objets R |
| distrom | Régression multinomiale distribuée |
| dlm | Analyse de probabilité et analyse bayésienne de modèles linéaires dynamiques |
| doSNOW | Adaptateur parallèle Foreach pour le package snow |
| dplyr | dplyr : grammaire de manipulation de données |
| DPpackage | Modélisation non paramétrique bayésienne dans R |
| dse | Estimation de systèmes dynamiques (package de série chronologique) |
| e1071 | Fonctions diverses du département de statistiques (e1071) de l'Université technique de Vienne |
| EbayesThresh | Seuil de Bayes empirique et méthodes associées |
| ebdbNet | Estimation de Bayes empirique des réseaux bayésiens dynamiques |
| effects | Affichages des effets pour les modèles linéaires, linéaires généralisés, multinomiaux Logit, Odds proportionnels Logit et les modèles à effets mixtes |
| emulator | Émulation bayésienne de programmes pour ordinateurs |
| ensembleBMA | Prévision probabiliste utilisant des ensembles et des moyennes de modèles bayésiens |
| entropy | Estimation de l'entropie, des informations mutuelles et des quantités relatives |
| EvalEst | Estimation de systèmes dynamiques - extensions |
| evaluate | Outils d'analyse et d'évaluation qui fournissent plus de détails que les outils par défaut |
| evdbayes | Analyse bayésienne en théorie des valeurs extrêmes |
| evora | Valeurs aberrantes de variable épigénétique pour l'analyse prédictive de risque |
| exactLoglinTest | Tests exacts de Monte Carlo pour modèles log-linéaires |
| expm | Exponentielle de matrice |
| extremevalues | Détection de valeurs aberrantes unidimensionnelle |


###Modules R commençant par les lettres F à L

| Nom du package | Description du package |
| ------------ | ------------------- |
| factorQR | Modèles de facteur de régression quantile bayésienne |
| faoutlier | Méthodes de détection de cas saillants pour l'analyse des facteurs et SEM |
| fitdistrplus | Aide pour l'ajustement d'une distribution paramétrique par rapport à des données non censurées ou censurées |
| FME | Environnement de modélisation flexible pour la modélisation inverse, la sensibilité, l'identifiabilité, l'analyse de Monte Carlo |
| foreach | Construction de boucle Foreach pour R |
| forecast | Fonctions de prévisions de séries chronologiques et de modèles linéaires |
| foreign | Lire des données stockées par Minitab, S, SAS, SPSS, Stata, Systat, Weka, dBase... |
| formatR | Mettre en forme du code R automatiquement |
| Formula | Formules de modèle étendu |
| fracdiff | ARIMA différenciés de façon fractionnelle, aussi connus sous le nom de modèles ARFIMA(p,d,q) |
| gam | Modèles additifs généralisés |
| gamlr | Régression Gamma Lasso |
| gbm | Modèles de régression augmentés et généralisés |
| gclus | Graphiques de clustering |
| gdata | Divers outils de programmation R pour la manipulation de données |
| gee | Résolution d'équations d'estimation généralisée |
| genetics | Génétique des populations |
| geoR | Analyse des données géostatistiques |
| geoRglm | geoRglm : package pour modèles spatiaux linéaires généralisés |
| geosphere | Trigonométrie sphérique |
| ggmcmc | Outils graphiques pour l'analyse de simulations de Monte-Carlo par chaînes de Markov à partir d'une inférence bayésienne |
| ggplot2 | Implémentation de la grammaire de graphiques |
| glmmBUGS | Modèles mixtes linéaires et spatiaux généralisés avec WinBUGS, BRugs ou OpenBUGS |
| glmnet | Modèles linéaires généralisés et régularisés Lasso et elastic-net |
| gmodels | Divers outils de programmation R pour l'ajustement de modèle |
| gmp | Arithmétique multiprécision |
| gnm | Modèles non linéaires généralisés |
| googlePublicData | Bibliothèque R pour générer des fichiers de métadonnées DSPL Public Data Explorer de Google |
| googleVis | Interface entre R et Google Charts |
| GPArotation | Rotation de facteur GPA |
| gplots | Divers outils de programmation R pour données de points |
| graphics | Package graphique R |
| grDevices | Appareils graphiques et prise en charge des polices et couleurs |
| gregmisc | Fonctions diverses de Greg |
| grid | Package graphique de grille |
| gridExtra | Fonctions de graphiques de grille |
| growcurves | Modèles de courbe de croissance semi-bayésienne et non paramétrique qui incluent en outre plusieurs effets aléatoires d'appartenance |
| grpreg | Chemins d'accès de régularisation pour les modèles de régression avec covariables groupées |
| gsubfn | Utilitaires pour chaînes et arguments de fonction |
| gtable | Réorganiser les grobs en tableau |
| gtools | Divers outils de programmation R |
| gWidgets | API gWidgets pour la création d'interface utilisateur graphique interactive sans kit de ressources |
| gWidgetsRGtk2 | Implémentation du kit de ressources de gWidgets pour RGtk2 |
| haplo.stats | Analyse statistique de haplotypes avec traits et covariables lorsque de la phase de liaison est ambiguë |
| hbsae | Estimation de petite zone bayésienne hiérarchique |
| hdrcde | Régions à densité élevée et estimation de densité conditionnelle |
| heavy | Package pour hébergement des valeurs aberrantes à l'aide de lois de probabilité à queue lourde |
| hflights | Vols au départ de Houston en 2011 |
| HH | Analyse statistique et affichage de données : Heiberger et Holland |
| HI | Simulation de distributions prises en charge par des hyperplans imbriqués |
| highr | Coloration syntaxique pour R |
| Hmisc | Divers Harrell |
| htmltools | Outils HTML |
| httpuv | Bibliothèque de serveur HTTP et WebSocket |
| httr | Outils permettant de travailler avec des URL et avec HTTP |
| IBrokers | API R pour Interactive Brokers Trader Workstation |
| igraph | Analyse et visualisation du réseau |
| intervals | Outils permettant de travailler avec des points et des intervalles |
| iplots | iPlots : graphiques interactifs pour R |
| ipred | Prédicateurs améliorés |
| irr | Différents coefficients d'accord et de fiabilité Interrater |
| iterators | Construction d'itérateur pour R |
| JavaGD | Appareil graphique Java |
| JGR | JGR : interface utilisateur graphique Java pour R |
| kernlab | Laboratoire Machine Learning basé sur le noyau |
| KernSmooth | Fonctions de lissage de noyau pour Wand et Jones (1995) |
| KFKSDS | Filtre de Kalman, lisseur et lisseur de perturbations |
| kinship2 | Fonctions d'ascendance |
| kknn | K plus proches voisins pondérés |
| klaR | Classification et visualisation |
| knitr | Package universel pour la génération de rapports dynamiques dans R |
| ks | Lissage du noyau |
| labeling | Étiquetage des axes |
| Lahman | Base de données de base-ball de Sean Lahman |
| lars | Régression Least Angle, Lasso et Forward Stagewise |
| lattice | Graphiques Lattice |
| latticeExtra | Utilitaires graphiques supplémentaires basés sur Lattice |
| lava | Modèles de variable latente linéaire |
| lavaan | Analyse de variable latente |
| leaps | Sélection de sous-ensembles de régression |
| LearnBayes | Fonctions d'apprentissage d'inférence bayésienne |
| limSolve | Modèles inverses linéaires de résolution |
| lme4 | Modèles d'effets mixtes linéaires utilisant Eigen et S4 |
| lmm | Modèles mixtes linéaires |
| lmPerm | Tests de permutation pour modèles linéaires |
| lmtest | Test de modèles de régression linéaire |
| locfit | Régression locale, probabilité et estimation de densité |
| lpSolve | Interface Lp\_solve v. 5.5 pour résoudre des programmes linéaires/entiers |


###Modules R commençant par les lettres M à R

| Nom du package | Description du package |
| ------------ | ------------------- |
| magic | Créer et étudier des carrés magiques |
| magrittr | magrittr : opérateur de pipe de transfert pour R |
| mapdata | Bases de données de mappage supplémentaires |
| mapproj | Projections de carte |
| maps | Dessiner des cartes géographiques |
| maptools | Outils pour la lecture et la gestion d'objets spatiaux |
| maptree | Mappage, étalage et dessins de modèles d'arborescence |
| markdown | Rendu Markdown pour R |
| MASS | Fonctions de prise en charge et de jeux de données pour MASS de Venables et Ripley |
| MasterBayes | Méthodes ML et MCMC pour la reconstruction d'ascendance et l'analyse |
| Matrix | Classes et méthodes de matrice creuse et dense |
| matrixcalc | Collection de fonctions pour les calculs de matrice |
| MatrixModels | Modélisation des matrices creuses et denses |
| maxent | Régression logistique multinomiale de mémoire insuffisante avec prise en charge de la classification de texte |
| maxLik | Estimation de la probabilité maximale |
| mcmc | Monte Carlo par chaînes de Markov |
| MCMCglmm | Modèles mixtes linéaires généralisés MCMC |
| MCMCpack | Package Monte Carlo par chaînes de Markov (MCMC) |
| memoise | Fonctions memoise |
| methods | Méthodes et classes formelles |
| mgcv | Véhicule de calcul GAM mixte avec estimation de lissage GCV/AIC/REML |
| mice | Imputation multivariable par équations chaînées |
| microbenchmark | Sous-fonctions de minutage de précision en microseconde |
| mime | Mapper des noms de fichiers en types MIME |
| minpack.lm | Interface R pour l'algorithme de moindres carrés non linéaires de Levenberg-Marquardt que l'on trouve dans MINPACK, avec prise en charge des limites |
| minqa | Algorithmes d'optimisation sans dérivée par approximation quadratique |
| misc3d | Points 3D divers |
| miscF | Fonctions diverses |
| miscTools | Outils et utilitaires divers |
| mixtools | Outils pour modèles de mélange fini d'analyse |
| mlbench | Problèmes de banc d'essai de Machine Learning |
| mlogitBMA | Moyenne de modèle bayésien pour modèles Logit multinomiaux |
| mnormt | Distributions normale multivariable et t |
| MNP | Package R pour l'ajustement du modèle Probit multinomial |
| modeltools | Outils et classes pour modèles statistiques |
| mombf | Facteurs de Bayes de moment inverse et de moment |
| monomvn | Estimation de données Student t et normales mulitvariables avec absence monotone |
| monreg | Régression monotone non paramétrique |
| mosaic | Utilitaires d'apprentissage de statistiques et de mathématiques de projet MOSAIC (mosaic-web.org) |
| MSBVAR | Modèles de commutation de Markov, d'autorégression de vecteur et modèles bayésiens |
| msm | Modèles de Markov multiétat et modèles de Markov masqués dans le temps en continu |
| multcomp | Inférence simultanée dans les modèles paramétriques généraux |
| multicool | Permutations de multiensembles en ordre cool-lex. |
| munsell | Système de couleurs de Munsell |
| mvoutlier | Détection de valeurs aberrantes multivariables basée sur des méthodes fiables |
| mvtnorm | Distributions normale multivariable et t |
| ncvreg | Chemins d'accès de régularisation pour modèles de régression pénalisés SCAD et MCP |
| nlme | Modèles d'effets mixtes linéaires et non linéaires |
| NLP | Infrastructure de traitement de langage naturel |
| nnet | Réseaux neuronaux directs et modèles de journal linéaire multinomial |
| numbers | Fonctions de théorie des nombres |
| numDeriv | Dérivées numériques exactes |
| openNLP | Interface d'outils OpenNLP Apache |
| openNLPdata | Modèles de langue anglaise Jars et Basic OpenNLP Apache |
| OutlierDC | Détection de valeurs aberrantes à l'aide de régression quantile pour données censurées |
| OutlierDM | Détection de valeurs aberrantes pour données répliquées à débit élevé |
| outliers | Tests de valeurs aberrantes |
| pacbpred | Estimation PAC bayésienne et prédiction dans des modèles additifs creux |
| parallel | Prise en charge de calcul parallèle dans R |
| partitions | Partitions additives d'entiers |
| party | Laboratoire pour partition récursive |
| PAWL | Implémentation de l'algorithme de PAWL |
| pbivnorm | CDF normalisée bivariable vectorisée |
| pcaPP | PCA robuste par poursuite de projection |
| permute | Fonctions de génération de permutations limitées de données |
| pls | Régression des moindres carrés partiels et de composant principal |
| plyr | Outils de fractionnement, d'application et d'association de données |
| png | Lire et écrire des images PNG |
| polynom | Collection de fonctions pour implémenter une classe pour manipulations polynomiales unidimensionnelles |
| PottsUtils | Fonctions utilitaires des modèles Potts |
| predmixcor | Règle de classification basée sur des modèles de mélange bayésien avec sélection de correction de décalage |
| PresenceAbsence | Évaluation du modèle d'absence de présence |
| prodlim | Estimation de la limite de produit. Méthode Kaplan-Meier et Aalen-Johansson pour l'analyse de l'historique (survie) d'événements censurés |
| profdpm | Mélanges de processus Dirichlet de profil |
| profileModel | Outils de profilage des fonctions d'inférence pour diverses classes de modèle |
| proto | Programmation orientée objet de prototype |
| pscl | Laboratoire informatique de sciences politiques, Université de Stanford |
| psych | Procédures de recherche de personnalité, procédures psychologiques et psychométriques |
| quadprog | Fonctions de résolution des problèmes de programmation quadratiques |
| quantreg | Régression quantile |
| qvcalc | Quasi-écarts pour effets de facteur dans des modèles statistiques |
| R.matlab | Lire et écrire des fichiers MAT avec connectivité R-MATLAB |
| R.methodsS3 | Fonction utilitaire pour la définition de méthodes S3 |
| R.oo | Programmation R orientée objet avec ou sans références |
| R.utils | Divers utilitaires de programmation |
| R2HTML | Exportation HTML pour objets R |
| R2jags | Package pour exécuter Jags à partir de R |
| R2OpenBUGS | Exécution d'OpenBUGS à partir de R |
| R2WinBUGS | Exécution de WinBUGS et d'OpenBUGS à partir de R/S-PLUS |
| ramps | Modélisation géostatistique bayésienne avec RAMPS |
| RandomFields | Simulation et analyse de champs aléatoires |
| randomForest | Forêts aléatoires de Breiman et Cutler pour la classification et la régression |
| RArcInfo | Fonctions d'importation des données à partir de couvertures binaires Arc/Info V7.x |
| raster | raster : analyse et modélisation de données géographiques |
| rbugs | Fusion de R et d'OpenBugs, et au-delà |
| RColorBrewer | Palettes ColorBrewer |
| Rcpp | Intégration de R et de C++ transparente |
| RcppArmadillo | Intégration Rcpp pour la bibliothèque algébrique linéaire basée sur Armadillo |
| rcppbugs | Liaison R pour cppbugs |
| RcppEigen | Intégration Rcpp pour la bibliothèque algébrique linéaire basée sur Eigen |
| RcppExamples | Exemples d'utilisation de Rcpp pour connecter R et C++ |
| RCurl | Interface client de réseau général (HTTP/FTP /...) pour R |
| relimp | Contribution relative des effets dans un modèle de régression |
| reshape | Données de réorganisation de flexibilité |
| reshape2 | Données de réorganisation de flexibilité : recréation du package de réorganisation |
| rgdal | Liaisons pour la bibliothèque d'abstraction de données géospatiales |
| rgeos | Interface de Geometry Engine : Open Source (GEOS) |
| rgl | Dispositif de visualisation 3D (OpenGL) |
| RGraphics | Données et fonctions du livre R Graphics, Second Edition |
| RGtk2 | Liaisons R pour Gtk 2.8.0 et versions ultérieures |
| RJaCGH | Saut réversible MCMC pour l'analyse de tableaux CGH |
| rjags | Modèles de graphiques bayésiens utilisant MCMC |
| rJava | R de bas niveau pour interface Java |
| RJSONIO | Sérialiser des objets R pour JSON, JavaScript Object Notation |
| robCompositions | Estimation fiable pour les données compositionnelles |
| robustbase | Statistiques fiables basiques |
| RODBC | Accès à une base de données ODBC |
| rootSolve | Recherche racine non linéaire, analyse de l'équilibre et de la stabilité d'équations dérivées ordinaires |
| roxygen | Programmation lettrée dans R |
| roxygen2 | Documentation dans la source pour R |
| rpart | Partition récursive et arbres de régression |
| rrcov | Estimations fiables et évolutives avec point de rupture élevé |
| rscproxy | statconn : fournit une interface de style C portable pour R (StatConnector) |
| RSGHB | Fonctions d'estimation bayésienne hiérarchique : approche flexible |
| RSNNS | Réseaux neuronaux dans R utilisant le simulateur de réseaux de neurones de Stuttgart (SNNS, Stuttgart Neural Network Simulator) |
| RTextTools | Classification de texte automatique via apprentissage contrôlé |
| RUnit | Infrastructure de développement de test unitaire R |
| runjags | Utilitaires d'interface, méthodes de calcul parallèle et distributions supplémentaires pour les modèles MCMC dans JAGS |
| Runuran | Interface R pour les générateurs de variantes aléatoires UNU.RAN |
| rworldmap | Données globales de mappage, vecteur et raster |
| rworldxtra | Frontières des pays à la résolution élevée |


###Modules R de S à Z

| Nom du package | Description du package |
| ------------ | ------------------- |
| SampleSizeMeans | Calculs de taille d'échantillon pour moyens normaux |
| SampleSizeProportions | Calcul des exigences en termes de taille d'échantillon lors de l'estimation de la différence entre deux proportions binomiales |
| sandwich | Estimations de matrice de covariance fiable |
| sbgcop | Estimation et imputation de copule gaussienne bayésienne semi-paramétrique |
| scales | Fonctions d'échelle pour graphiques |
| scapeMCMC | Points de diagnostic MCMC |
| scatterplot3d | Nuage de points 3D |
| sciplot | Fonctions graphiques scientifiques pour les plans factoriels |
| segmented | Relations segmentées dans les modèles de régression avec estimation de points d'arrêt/de changement |
| sem | Modèles d'équations structurelles |
| seriation | Infrastructure de sériation |
| setRNG | Définir un générateur et une graine de nombres aléatoires (normal) |
| sgeostat | Infrastructure de développement orientée objet pour la modélisation géostatistique dans S+ |
| shapefiles | Lire et écrire des fichiers de mise en forme ESRI |
| shiny | Infrastructure de développement d'applications web pour R |
| SimpleTable | Inférence bayésienne et analyse de sensibilité pour les effets de causalité de tableaux 2 x 2 et 2 x 2 K en cas de variable parasite non mesurée |
| slam | Matrices et tableaux légers et creux |
| smoothSurv | Régression de survie avec erreur de distribution d'erreur lissée |
| sna | Outils d'analyse de réseaux sociaux |
| snow | Réseau simple de stations de travail |
| SnowballC | Racinisateur Snowball basé sur la bibliothèque UTF-8 libstemmer C |
| snowFT | Réseau simple à tolérance de pannes de stations de travail |
| sp | Classes et méthodes pour données spatiales |
| spacetime | Classes et méthodes pour données spatio-temporelles |
| SparseM | Algèbre linéaire creuse |
| spatial | Fonctions pour l'analyse de Kriging et l'analyse de modèle de point |
| spBayes | Modélisation spatio-temporelle multivariable et unidimensionnelle |
| spdep | Dépendance spatiale : schéma de pondération, statistiques et modèles |
| spikeslab | Sélection de prévisions et de variables à l'aide de la régression de type spike-and-slab |
| splancs | Analyse de modèle de point d'espace-temps et spatial |
| splines | Fonctions et classes de spline de régression |
| spTimer | Modélisation bayésienne spatio-temporelle utilisant R |
| stats | Package de statistiques R |
| stats4 | Fonctions statistiques utilisant des classes S4 |
| stochvol | Inférence bayésienne efficace pour modèles de volatilité stochastique (SV) |
| stringr | Faciliter l'utilisation de chaînes |
| strucchange | Test, surveillance et modifications structurelles de dates |
| stsm | Modèles de séries chronologiques structurelles |
| stsm.class | Classes et méthodes pour modèles de séries chronologiques structurelles |
| SuppDists | Distributions supplémentaires |
| survival | Analyse de survie |
| svmpath | svmpath : algorithme de chemin d'accès SVM |
| tau | Utilitaires d'analyse de texte |
| tcltk | Interface Tcl/Tk |
| tcltk2 | Ajouts TclTk |
| TeachingDemos | Démonstrations pour l'enseignement et l'apprentissage |
| tensorA | Arithmétique de tenseurs avancés avec index nommés |
| testthat | Code Testthat. Outils pour rendre les tests amusants |
| textcat | Catégorisation de texte basé sur N-Gram |
| textir | Régression inverse pour l'analyse de texte |
| tfplot | Utilitaires utilisateur délai d'exécution |
| tframe | Noyau de codage délai d'exécution |
| tgp | Modèles de processus gaussien bayésien avec arborescence |
| TH.data | Archivage de données TH |
| timeDate | Rmetrics : objets chronologiques et objets de calendrier |
| tm | Package d'exploration de données de texte |
| outils | Outils pour le développement de packages |
| translations | Package de traductions R |
| tree | Arborescence de classification et de régression |
| tseries | Analyse de séries chronologiques et finance mathématique |
| tsfa | Analyse de facteurs de séries chronologiques |
| tsoutliers | Détection automatique de valeurs aberrantes dans des séries chronologiques |
| TSP | Problème du voyageur de commerce (TSP) |
| UsingR | Jeux de données pour le texte utilisant R pour la présentation de statistiques |
| utils | Package d'utilitaires R |
| varSelectIP | Sélection de modèles de Bayes objectif |
| vcd | Visualisation de données catégorielles |
| vegan | Package de communauté écologiste |
| VGAM | Modèles additifs et linéaires généralisés de vecteur |
| VIF | Régression VIF : algorithme de régression rapide pour données volumineuses |
| whisker | {{mustache}} pour R, création de modèles sans logique |
| wordcloud | Word Clouds |
| XLConnect | Connecteur Excel pour R |
| XML | Outils pour l'analyse et la génération de code XML au sein de R et de S-Plus |
| xtable | Exporter des tableaux vers LaTeX ou HTML |
| xts | Séries chronologiques eXtensible |
| yaml | Méthodes de conversion de données R vers YAML et inversement |
| zic | Inférence bayésienne pour les modèles de comptes à zéro inflation |
| zipfR | Modèles de statistiques pour les distributions de fréquence de mots |
| zoo | Infrastructure S3 pour séries chronologiques régulières et irrégulières (observations ordonnées Z) |


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
 

<!---HONumber=July15_HO5-->