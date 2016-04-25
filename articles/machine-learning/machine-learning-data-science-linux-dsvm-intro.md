<properties
	pageTitle="Approvisionnement d’une machine virtuelle de science des données Linux | Microsoft Azure"
	description="Configurez et créez une machine virtuelle de science des données Linux sur Azure pour vos besoins d’analyse et d’apprentissage automatique."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/05/2016"
	ms.author="gokuma;bradsev" />

# Approvisionnement d’une machine virtuelle de science des données Linux 

## Introduction

La machine virtuelle de science des données Linux est une image de machine virtuelle Azure préalablement installée et configurée avec plusieurs outils populaires couramment utilisés dans le cadre de l’analyse de données et de l’apprentissage automatique. Les principaux composants logiciels inclus sont les suivants :

- Microsoft R Open
- Distribution Anaconda Python (versions 2.7 et 3.5), y compris les populaires bibliothèques d’analyse des données
- Jupyter Notebook (R, Python)
- Azure Storage Explorer
- Ligne de commande Azure pour la gestion des ressources Azure
- Base de données PostgresSQL
- Outils d’apprentissage automatique
    - [Computational Network Toolkit (CNTK)](https://github.com/Microsoft/CNTK) : logiciel d’apprentissage profond de Microsoft Research
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) : système d’apprentissage automatique rapide prenant en charge des techniques (apprentissage en ligne, hachage, allreduce, réductions, learning2search, actif et interactif).
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/) : outil offrant une implémentation rapide et précise des arborescences optimisées
    - [Rattle (R Analytical Tool To Learn Easily)](http://rattle.togaware.com/) : outil qui facilite la prise en main de l’analyse des données et de l’apprentissage automatique dans R avec une modélisation et une exploration des données basées sur une interface graphique utilisateur avec la génération automatique de code R. 
- Kit de développement logiciel (SDK) Azure dans Java, Python, node.js, Ruby, PHP
- Bibliothèques dans les langages R et Python à utiliser dans Azure Machine Learning et d’autres services Azure
- Éditeurs et outils de développement (Eclipse, Emacs, gedit, vi)

La science des données consiste à itérer sur une séquence de tâches :

- recherche, chargement et traitement des données 
- création et test des modèles 
- déploiement des modèles à des fins d’utilisation dans des applications intelligentes 

Les chercheurs de données sont souvent amenés à utiliser divers outils pour effectuer ces tâches. La recherche des versions adéquates des logiciels, puis leur téléchargement et leurs téléchargement, compilation et installation peuvent prendre un certain temps.

La machine virtuelle de science des données Linux est là pour vous soulager en grande partie de cette charge. Utilisez-la pour démarrer rapidement votre projet d’analyse. Elle vous permet de travailler sur des tâches basées sur différents langages, notamment R, Python, SQL, Java et C++. Eclipse propose un environnement de développement intégré (IDE) qui vous permet de développer et de tester très simplement votre code. Le Kit de développement logiciel (SDK) Azure inclus dans la machine virtuelle vous permet de créer des applications à l’aide de divers services sur Linux disponibles sur la plateforme cloud de Microsoft. En outre, vous avez accès à d’autres langages tels que Ruby, Perl, PHP et node.js, déjà préinstallés.

Cette image de machine virtuelle de science des données ne génère pas de frais. Vous payez uniquement les frais d’utilisation matérielle Azure en fonction de la taille de la machine virtuelle approvisionnée avec l’image de machine virtuelle. Vous trouverez d’autres informations sur les frais de calcul [ici](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linuxdsvm/).


## Configuration requise

Avant de pouvoir créer une machine virtuelle de science des données Linux, vous devez disposer des éléments suivants :

- **Un abonnement Azure** : pour obtenir un abonnement, consultez la page [Obtenir une version d’évaluation gratuite d’Azure](https://azure.microsoft.com/free/).
- **Un compte de stockage Azure** : pour créer un compte, référez-vous à la rubrique [Création d’un compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account). Le compte de stockage peut être également créé dans le cadre du processus de création de la machine virtuelle si vous ne souhaitez pas utiliser un compte existant.


## Création d’une machine virtuelle de science des données Linux

Voici les étapes de création d’une instance de la machine virtuelle de sciences des données Linux :

1.	Accédez à la liste des machines virtuelles présentes sur le [portail Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2.	 Cliquez sur le bouton **Créer** au bas de l’écran pour accéder à un assistant.![configure-data-science-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.	 Les sections suivantes fournissent les **entrées** de chacune des **5 étapes** (énumérées à droite de la figure ci-dessus) de l’Assistant utilisé pour créer la machine virtuelle de sciences de données. Voici les entrées nécessaires à la configuration de chacune de ces étapes :

  **a. Paramètres de base** :

   - **Name** (Nom) : nom du serveur Data Science que vous créez.
   - **User Name** (Nom d’utilisateur) : premier identifiant de connexion du compte.
   - **Password** (Mot de passe) : premier mot de passe de compte (vous pouvez utiliser une clé publique SSH au lieu d’un mot de passe).
   - **Subscription** (Abonnement) : si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine. Remarque : vous devez disposer des privilèges de création de ressources pour cet abonnement. 
   - **Resource Group** (Groupe de ressources) : vous pouvez créer un nouveau groupe ou utiliser un groupe existant.
   - **Location** (Emplacement) : sélectionnez le centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau

  **b. Taille** :

   - Sélectionnez l’un des types de serveur qui répond à vos exigences fonctionnelles et à vos contraintes de coût. Sélectionnez « View All » (Afficher tout) pour obtenir d’autres choix de tailles de machines virtuelles

  **c. Paramètres**

   - **Disk Type** (Type de disque) : choisissez Premium si vous préférez un disque SSD. Sinon, choisissez « Standard ».
   - **Storage Account** (Compte de stockage) : vous pouvez créer un nouveau compte de stockage Azure associé à votre abonnement ou utiliser un compte existant au même *emplacement* que celui que vous avez sélectionné à l’étape de définition des paramètres de base de l’Assistant.
   - **Other parameters** (Autres paramètres) : dans la plupart des cas, vous utiliserez simplement la valeur par défaut. Si vous ne souhaitez pas utiliser les valeurs par défaut, vous pouvez survoler le lien d'informations pour obtenir de l'aide sur des champs spécifiques.

  **d. Résumé** :

   - Vérifiez que toutes les informations que vous avez saisies sont correctes.

  **e. Acheter** :

   - Cliquez sur **Buy** (Acheter) pour démarrer l’approvisionnement. Les conditions de la transaction vous sont communiquées via un lien. La machine virtuelle n'est pas assortie de frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l'étape **Taille**. 


L’approvisionnement prend environ 10 à 20 minutes. L’état de l’approvisionnement est affiché sur le portail Azure.

## Accès à une machine virtuelle de science des données Linux

Une fois la machine virtuelle créée, vous pouvez vous y connecter à l’aide de SSH en utilisant les informations d’identification de compte créées dans la section Paramètres de base de l’étape 3 de l’interface de l’interpréteur de commandes texte. Sous Windows, vous pouvez télécharger un outil client SSH tel que [Putty](http://www.putty.org). Si vous préférez un bureau graphique (système Windows X), vous pouvez utiliser le transfert X11 sur Putty ou installer le client X2Go.

>[AZURE.NOTE] Lors des tests, le client X2go a eu des performances sensiblement meilleures que le transfert X11. Nous recommandons donc d’utiliser le client X2Go pour l’interface de bureau graphique.


## Installation et configuration du client X2Go

La machine virtuelle Linux est déjà approvisionnée avec le serveur X2Go et elle est prête à accepter des connexions clientes. Pour vous connecter au bureau graphique de la machine virtuelle Linux, vous devez effectuer les opérations suivantes sur votre client.

1. Téléchargez et installez le client X2Go pour votre plateforme cliente [ici](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Exécutez le client X2Go et sélectionnez *New Session* (Nouvelle session). Une fenêtre de configuration avec plusieurs onglets s’ouvre. Entrez les paramètres de configuration suivants : 
    * **Onglet Session** :
        - **Host** (Hôte) : nom d’hôte ou adresse IP de votre machine virtuelle de science de données Linux.
        - **Login** (Connexion) : nom d’utilisateur de la machine virtuelle VM.
        - **SSH Port** (Port SSH) : conservez la valeur par défaut 22.
        - **Session Type** (Type de session) : remplacez la valeur par XFCE. REMARQUE : la machine virtuelle Linux ne prend actuellement en charge que le bureau XFCE.
    * **Onglet Media** (Média) : vous pouvez désactiver l’impression client et la prise en charge du son si vous n’en avez pas besoin. 
    * **Shared folders** (Dossiers partagés) : si vous souhaitez que les répertoires de vos ordinateurs clients soient montés sur la machine virtuelle Linux, ajoutez ceux que vous souhaitez partager avec la machine virtuelle sous cet onglet. 

Une fois que vous êtes connecté à la machine virtuelle à l’aide du client SSH ou du bureau graphique XFCE par le biais du client X2Go, vous pouvez commencer à utiliser les outils qui sont installés et configurés sur la machine virtuelle. Sur XFCE, vous pouvez voir les icônes de bureau et raccourcis du menu d’applications pour la plupart des outils.

## Création d'un mot de passe fort sur le serveur Jupyter Notebook 

Exécutez la commande suivante à partir de l'invite de commandes sur la machine virtuelle de sciences de données pour créer votre propre mot de passe fort pour le serveur Jupyter Notebook installé sur la machine.

	python -c "import IPython;print(IPython.lib.passwd())"

À l'invite, entrez un mot de passe fort.

Vous verrez le hachage du mot de passe au format « sha1:xxxxxx » dans la sortie. Copiez ce hachage de mot de passe et remplacez le hachage existant dans votre fichier de configuration de notebook situé dans le répertoire **C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py** avec un nom de paramètre ***c.NotebookApp.password***. L’accès **racine** est requis pour modifier ce fichier.

Vous devez remplacer seulement la valeur existante du hachage qui figure entre les guillemets. Les guillemets et le préfixe ***sha1:*** de la valeur du paramètre doivent être conservés.

Enfin, vous devez arrêter et redémarrer le service Jupyter installé dans le dossier /etc/init.d/jupyter.

>[AZURE.NOTE] Si votre nouveau mot de passe n’est pas accepté après le redémarrage de Jupyter ou si vous ne parvenez pas à arrêter Jupyter, essayez de redémarrer la machine virtuelle.

## Outils installés sur la machine virtuelle de science des données Linux

### Microsoft R Open 
R est le langage le plus répandu pour l’analyse des données et l’apprentissage automatique. Si vous souhaitez utiliser R pour votre analyse, Microsoft R Open (MRO) et Math Kernel Library (MKL) sont installés sur la machine virtuelle. MKL optimise les opérations mathématiques courantes dans les algorithmes d’analyse. MRO est entièrement compatible avec CRAN-R et les bibliothèques R publiées dans CRAN peuvent être installées sur MRO. Vous pouvez modifier vos programmes R dans un des éditeurs par défaut, comme vi, Emacs ou gedit. Vous pouvez aussi télécharger et utiliser d’autres IDE, comme [RStudio](http://www.rstudio.com). Pour votre commodité, un script simple (installRStudio.sh) est fourni dans le répertoire **/dsvm/tools** qui installe RStudio. Si vous utilisez l’éditeur Emacs, notez que le package ESS Emacs (Emacs Speaks Statistics), qui simplifie l’utilisation de fichiers R dans l’éditeur Emacs, est préinstallé.

Pour lancer R, tapez ***R*** dans l’interpréteur de commandes. Vous accédez alors à un environnement interactif. Pour développer votre programme R, vous utilisez généralement un éditeur comme Emacs, vi ou gedit, puis vous exécutez les scripts dans R. Si vous installez RStudio, vous disposez d’un environnement de développement intégré graphique complet pour développer votre programme R.

Il existe également un script R qui permet d’installer les [packages Top 20 R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) si vous le souhaitez. Ce script peut être exécuté une fois que vous êtes dans l’interface interactive R, dans laquelle vous entrez (comme indiqué) en tapant R dans l’interpréteur de commandes.

### Python
Pour un développement basé sur Python, les versions 2.7 et 3.5 de la distribution Anaconda Python ont été installées. Cette distribution contient le langage Python de base avec environ 300 packages de mathématiques, d’ingénierie et d’analyse de données figurant parmi les plus populaires. Vous pouvez utiliser les éditeurs de texte par défaut. En outre, vous pouvez utiliser Spyder, un IDE Python fourni avec les distributions Anaconda Python. Spyder requiert un bureau graphique ou le transfert X11. Un raccourci vers Spyder est fourni dans le bureau graphique.

Étant donné que Python 2.7 et 3.5 sont tous deux disponibles, vous devez spécifiquement activer la version souhaitée de Python à utiliser dans la session en cours. Le processus d’activation définit la variable PATH sur la version souhaitée de Python.

Pour activer Python 2.7, exécutez la commande suivante à partir de l’interpréteur de commandes :

	source /anaconda/bin/activate root

Python 2.7 est installé dans */anaconda/bin*.

Pour activer Python 3.5, exécutez la commande suivante à partir de l’interpréteur de commandes :

	source /anaconda/bin/activate py35


Python 3.5 est installé dans */anaconda/envs/py35/bin*

Pour appeler la session interactive Python, tapez ***python*** dans l’interpréteur de commandes. Si vous travaillez sur une interface graphique ou si vous avez le programme d’installation du transfert X11, vous pouvez taper la commande ***spyder*** pour lancer l’IDE Python.

### Bloc-notes Jupyter
La distribution Anaconda est également fournie avec un serveur Jupyter Notebook, un environnement conçu pour le partage de code et d’analyses. Un serveur Jupyter Notebook a été préconfiguré avec Python 2, Python 3 et les noyaux R. Une icône de bureau nommée « Jupyter Notebook » permet de lancer le navigateur pour accéder au serveur. Si vous accédez à la machine virtuelle par le biais de SSH ou du client X2go, vous pouvez également accéder à l’URL [https://localhost:9999/](https://localhost:9999/) pour accéder au serveur Jupyter Notebook.

>[AZURE.NOTE] Si vous recevez des avertissements relatifs au certificat, vous pouvez les ignorer.

Vous pouvez accéder au serveur Jupyter Notebook à partir de n’importe quel hôte. Tapez simplement https://<nom DNS de la machine virtuelle ou adresse IP>:9999/. Nous avons inclus quelques exemples de notebooks : l’un dans Python et l’autre dans R. Après vous être authentifié auprès du serveur Jupyter Notebook avec le mot de passe créé à l’étape précédente, vous pouvez voir le lien vers les exemples sur la page d’accueil du notebook. Vous pouvez créer un notebook en sélectionnant Nouveau, puis le noyau du langage. Si vous ne voyez pas le bouton Nouveau, cliquez sur l’icône Jupyter en haut à gauche pour accéder à la page d’accueil du serveur du notebook.


### IDE et éditeurs 

Vous avez le choix entre plusieurs éditeurs de code, notamment vi/VIM, Emacs, gEdit et Eclipse. gEdit et Eclipse sont des éditeurs graphiques et vous devez être connecté à un bureau graphique pour les utiliser. Des raccourcis de menu d’applications et bureau pour permettent de lancer ces éditeurs.

**VIM** et **Emacs** sont des éditeurs de texte. Sur Emacs, nous avons installé un package sous forme de module complémentaire appelé ESS (Speaks Statistics) qui facilite l’utilisation de R dans l’éditeur Emacs. Des informations supplémentaires sont disponibles [ici](http://ess.r-project.org/).

**Eclipse** est un IDE open source et extensible qui prend en charge plusieurs langages. L’édition Java pour les développeurs est l’instance installée sur la machine virtuelle. Des plug-ins disponibles pour plusieurs langages courants peuvent être installés pour étendre l’environnement Eclipse. Nous avons également un plug-in installé dans Eclipse, appelé **Kit de ressources Azure pour Eclipse**, qui vous permet de facilement créer, développer, tester et déployer des applications Azure avec l’environnement de développement Eclipse qui prend en charge des langages tels que Java. Il existe également un **Kit de développement logiciel (SDK) Azure pour Java** qui permet d’accéder à différents services Azure à partir d’un environnement Java. Vous trouverez plus d’informations sur la page du [Kit de ressources Azure pour Eclipse](../azure-toolkit-for-eclipse/).

**LaTex** est installé par le biais du package texlive avec un package Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) sous forme de module complémentaire, ce qui simplifie la création de vos documents LaTex avec Emacs.

### Bases de données

#### Postgres
La base de données open source **Postgres** est disponible sur la machine virtuelle avec les services en cours d’exécution et la commande initdb déjà terminée. Vous devez toujours créer des bases de données et des utilisateurs. Reportez-vous à la documentation Postgres.

####  Client SQL graphique
**SQuirrel SQL**, un client SQL graphique, a été fourni pour vous connecter à différentes bases de données (Microsoft SQL Server, Postgres, MySQL, etc.) et pour exécuter des requêtes SQL. Vous pouvez l’exécuter à partir d’une session de bureau graphique (en utilisant le client X2Go par exemple). Lancez SQuirrel SQL à partir de l’icône sur le bureau ou exécutez la commande suivante dans l’interpréteur de commandes.

	/usr/local/squirrel-sql-3.7/squirrel-sql.sh 

Avant la première utilisation, vous devez configurer vos pilotes et les alias de base de données. Les pilotes JDBC sont situés dans le répertoire suivant :

*/usr/share/Java/jdbcdrivers*

Vous trouverez plus d’informations sur SQL SQuirrel sur le site web [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### Outils en ligne de commande pour l’accès à Microsoft SQL Server

Le package de pilotes ODBC pour Microsoft SQL Server est également fourni avec deux outils en ligne de commande :

**bcp** : cet utilitaire copie les données en bloc entre une instance de Microsoft SQL Server et un fichier de données dans un format spécifié par l’utilisateur. L'utilitaire bcp peut être utilisé pour importer un grand nombre de nouvelles lignes dans des tables SQL Server ou pour exporter des données hors des tables sous forme de fichiers de données. Pour importer des données dans une table, vous devez utiliser un fichier de format créé pour cette table ou comprendre la structure de la table et les types de données valides pour ses colonnes.

Des informations supplémentaires sont disponibles dans [Connexion avec bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd** : cet utilitaire vous permet de saisir des procédures système, des instructions Transact-SQL et des fichiers de script à l’invite de commandes. Il utilise ODBC pour exécuter des lots Transact-SQL.

Des informations supplémentaires sont disponibles dans [Connexion avec sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

>[AZURE.NOTE] L’utilitaire diffère légèrement entre les plateformes Linux et Windows. Consultez la page de documentation ci-dessus pour plus d’informations.


#### Bibliothèques pour l’accès aux bases de données

Des bibliothèques sont disponibles en Python et R pour l’accès aux bases de données.

- Dans R, le package **RODBC** ou **dplyr** vous permet d’interroger ou d’exécuter des instructions SQL sur le serveur de base de données. 
- Dans Python, la bibliothèque **pyodbc** fournit l’accès aux bases de données avec ODBC en tant que couche sous-jacente.  

Pour accéder à **Postgres** :

- Dans Python : utilisez la bibliothèque **psycopg2**. 
- Dans R : utilisez le package **RPostgreSQL**.


### Outils Azure 
Les outils Azure suivants sont installés sur la machine virtuelle :

- **Interface de ligne de commande azure** : celle-ci vous permet de créer et de gérer des ressources Azure par le biais de commandes dans un interpréteur. Pour appeler les outils Azure, tapez simplement ***azure help***. Pour plus d’informations, consultez la [page de documentation de l’interface de ligne de commande Azure](../virtual-machines-command-line-tools/).
- **Explorateur de stockage Microsoft Azure** : il s’agit d’un outil graphique qui permet de parcourir les objets stockés dans votre compte de stockage Azure et de télécharger des données vers et depuis des blobs Azure. Vous pouvez accéder à l’Explorateur de stockage à partir de l’icône de raccourci sur le bureau. Vous pouvez l’appeler à partir d’une invite de commandes en tapant ***StorageExplorer***. Vous devez être connecté à partir d’un client X2go ou avoir configuré le transfert X11. 
- **Bibliothèques Azure** : voici certaines des bibliothèques installées et disponibles :

- **Python** : les bibliothèques Azure installées dans Python sont ***azure***, ***azureml***, ***pydocumentdb*** et ***pyodbc***. Les trois premières bibliothèques permettent d’accéder aux services de stockage Azure, à Azure Machine Learning et à Azure DocumentDB (base de données NoSQL sur Azure). pyodbc, la quatrième bibliothèque (avec le pilote Microsoft ODBC pour SQL Server) permet l’accès à Microsoft SQL Server, Azure SQL Database et Azure SQL Datawarehouse à partir de Python à l’aide d’une interface ODBC. Entrez ***pip list*** pour voir toutes les bibliothèques. Veillez à exécuter cette commande dans l’environnement Python 2.7 et 3.5.

- **R** : les bibliothèques Azure installées dans R sont ***AzureML*** et ***RODBC***.

- **Java** : la liste des bibliothèques Azure pour Java est disponible dans le répertoire ***/dsvm/sdk/AzureSDKJava*** sur la machine virtuelle. Les bibliothèques principales sont les API de gestion et de stockage Azure, DocumentDB et les pilotes JDBC pour SQL Server.

Vous pouvez accéder au [portail Azure](https://portal.azure.com) à partir du navigateur Firefox préinstallé. Sur le portail Azure, vous pouvez créer, gérer et surveiller les ressources Azure.

### Azure Machine Learning

Azure Machine Learning (Azure ML) est un service cloud entièrement géré permettant de créer, déployer et partager facilement des solutions d’analyse prédictive. Créez vos expériences et modèles dans Azure Machine Learning Studio. Il est accessible à partir d’un navigateur web sur la machine virtuelle de science des données en vous rendant sur [Microsoft Azure Machine Learning](https://studio.azureml.net).

Une fois que vous êtes connecté à Azure Machine Learning Studio, vous avez accès à un canevas d’expérience où vous pouvez générer un flux logique pour les algorithmes d’apprentissage automatique. Vous avez également accès à un notebook Jupyter hébergé sur Azure ML et vous pouvez travailler en toute transparence sur les expériences dans Studio. Azure ML vous permet d’exploiter les modèles ML que vous avez générés en les encapsulant dans une interface de service web. Ainsi, les clients écrits dans n’importe quel langage peuvent appeler des prévisions à partir des modèles ML. Des informations supplémentaires sur Azure ML sont disponibles dans la [documentation Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Vous pouvez également générer vos modèles dans R ou Python sur la machine virtuelle, puis les déployer en production sur Azure ML. Nous avons des bibliothèques installées dans R et Python pour activer cette fonctionnalité.

- La bibliothèque R est appelée ***AzureML***. 
- La bibliothèque Python est appelée ***AzureML***. 

Pour plus d’informations sur la façon de déployer des modèles dans R et Python dans Azure ML, consultez la section *Générer des modèles avec R ou Python et les rendre opérationnels à l’aide d’Azure Machine Learning* dans [Dix choses que vous pouvez effectuer sur la machine virtuelle pour la science des données](machine-learning-data-science-vm-do-ten-things.md).
 
>[AZURE.NOTE] Ces instructions ont été écrites pour la version Windows de la machine virtuelle de science de données, mais les informations fournies sur le déploiement des modèles dans Azure ML sont également applicables à la machine virtuelle Linux.

### Outils d’apprentissage automatique

La machine virtuelle est fournie avec quelques outils/algorithmes ML qui ont été précompilés et installés localement. Il s’agit des actions suivantes :

* **CNTK (Computational Network Toolkit de Microsoft Research)** : logiciel d’apprentissage profond
* **Vowpal Wabbit** : algorithme d’apprentissage en ligne rapide
* **xgboost** : outil qui fournit des algorithmes d’arborescence optimisés
* **Python** : Anaconda Python est fourni avec des algorithmes ML et des bibliothèques comme Scikit-learn. Vous pouvez installer d’autres bibliothèques en exécutant pip install.
* **R** : riche bibliothèque de fonctions ML disponible pour R. Certaines des bibliothèques préinstallées sont lm, glm, randomForest et rpart. D’autres bibliothèques peuvent être installées en exécutant la commande suivante : 

		install.packages(<lib name>)

Voici quelques informations supplémentaires sur les trois premiers outils ML de la liste.

#### CNTK
Il s’agit d’un kit de ressources open source d’apprentissage profond. Cet outil en ligne de commande (cntk) est déjà dans PATH.

Pour exécuter un exemple de base, procédez comme suit dans l’interpréteur de commandes :

	# Copy samples to your home directory and execute cntk
	cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo 
	cd cntkdemo/Data
	cntk configFile=../Config/Simple.cntk

La sortie du modèle est disponible dans *~/cntkdemo/Output/Models*

Des informations supplémentaires sur CNTK sont disponibles sur [github.com/Microsoft/CNTK](https://github.com/Microsoft/CNTK) et dans le [wiki CNTK](https://github.com/Microsoft/CNTK/wiki).


#### Vowpal Wabbit(VW) :

Vowpal Wabbit est un système d’apprentissage automatique qui repousse les limites de l’apprentissage à l’aide de nombreuses techniques (apprentissage en ligne, hachage, allreduce, réductions, learning2search, actif et interactif).

Pour exécuter l’outil sur un exemple très simple, procédez comme suit :

	cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
	cd vwdemo
	vw house_dataset

Ce répertoire inclut d’autres démonstrations plus conséquentes. Des informations supplémentaires sur VW sont disponibles sur [github.com/JohnLangford/vowpal\_wabbit](https://github.com/JohnLangford/vowpal_wabbit) et dans le [wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### xgboost
Cette bibliothèque a été conçue et optimisée pour les algorithmes d’arborescence optimisés. Son objectif est de repousser les limites de calcul des machines de manière à fournir une optimisation des arborescences à grande échelle qui est évolutive, portable et précise.

Elle est fournie sous forme de ligne de commande ou de bibliothèque R.

Pour utiliser cette bibliothèque dans R, vous pouvez démarrer la session R interactive (en tapant *R* dans l’interpréteur de commandes) et le chargement de la bibliothèque.

Voici un exemple simple que vous pouvez exécuter dans l’invite R :

	library(xgboost)

	data(agaricus.train, package='xgboost')
	data(agaricus.test, package='xgboost')
	train <- agaricus.train
	test <- agaricus.test
	bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
	pred <- predict(bst, test$data)

Pour exécuter la ligne de commande xgboost, voici les commandes à exécuter dans l’interpréteur de commandes :

	cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
	cd xgboostdemo
	xgboost mushroom.conf


Un fichier .model est écrit dans le répertoire spécifié. Des informations sur cet exemple de démonstration sont disponibles [ici](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Des informations supplémentaires sur xgboost sont disponibles dans la [page de documentation xgboost](https://xgboost.readthedocs.org/en/latest/) et dans son [dépôt Github](https://github.com/dmlc/xgboost).

#### Rattle
Rattle (R Analytical Tool To Learn Easily) facilite la prise en main de l’exploration des données dans R avec des fonctionnalités d’exploration et de modélisation des données basées sur une interface graphique utilisateur. Cet outil présente des statistiques et une synthèse visuelle des données, transforme les données qui peuvent être facilement modélisées, génère des modèles supervisés ou non à partir des données, présente les performances des modèles graphiquement et note les nouveaux jeux de données. Il génère également du code R qui réplique les opérations dans l’interface utilisateur qui peut être exécuté directement dans R ou utilisé comme point de départ pour une analyse plus approfondie.

Pour exécuter Rattle, vous devez ouvrir une session de connexion à un bureau graphique. Dans le terminal, tapez ```R``` pour entrer dans l’environnement R. À l’invite R, entrez les commandes suivantes :

	library(rattle)
	rattle()
	
À présent, une interface graphique s’ouvre avec un jeu d’onglets. Voici une procédure de démarrage rapide dans Rattle qui utilise un exemple de jeu de données météorologiques et permet de créer un modèle. Dans certaines étapes, vous êtes invité à installer et charger automatiquement tous les packages R requis qui ne sont pas déjà installés sur le système. **Remarque** : dans la fenêtre de console R, vous pouvez voir une invite qui vous demande si vous souhaitez installer des packages dans votre bibliothèque personnelle si vous n’êtes pas autorisé à l’installer dans le répertoire système (valeur par défaut). Répondez « o » si vous voyez ces invites.

1. Cliquez sur Exécuter.
2. La boîte de dialogue qui s’affiche vous demande si vous souhaitez utiliser l’exemple de jeu de données météorologiques. Cliquez sur Oui pour charger l’exemple.
3. Cliquez sur l’onglet Modéliser.
4. Cliquez sur Exécuter pour générer un arbre de décision.
5. Cliquez sur Dessin pour afficher l’arbre de décision. 
6. Cliquez sur la case d’option Forêt, puis cliquez sur Exécuter pour générer une forêt aléatoire. 
7. Cliquez sur l’onglet Évaluer.
8. Cliquez sur la case d’option Risque, puis cliquez sur Exécuter pour afficher deux tracés de performances (cumulatifs) Risque.
9. Cliquez sur l’onglet Journal pour afficher le code de génération R pour les opérations ci-dessus. Remarque : il y a un bogue dans la version actuelle de Rattle : insérez « # » devant « Exporter ce journal... » dans le texte du journal.
10. Cliquez sur le bouton Exporter pour enregistrer le script R dans le fichier weather\_script. R dans le dossier de base.

Vous pouvez quitter Rattle et R. Vous pouvez maintenant modifier le script R généré ou l’utiliser tel quel, pour l’exécuter à tout moment et répéter tout ce qui a été fait dans l’interface utilisateur Rattle. Pour les débutants en langage R, c’est un moyen particulièrement facile d’effectuer rapidement une analyse et un apprentissage automatique dans une interface graphique simple tout en générant automatiquement du code dans R pour le modifier et/ou en tirer des apprentissages.


## Étapes suivantes
Voici quelques étapes supplémentaires pour poursuivre votre travail d'apprentissage et d'exploration.

* Explorez les différents outils de science des données sur la machine virtuelle de science des données en testant les outils répertoriés dans cet article. Vous pouvez également exécuter *dsvm-plus-info* dans l’interpréteur de commandes au niveau de la machine virtuelle pour accéder à une présentation de base et des liens vers des informations supplémentaires sur les outils installés sur la machine virtuelle.  
* Apprenez à créer des solutions analytiques de bout en bout systématiquement à l’aide du [processus de science des données](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visitez la [galerie Cortana Analytics](http://gallery.cortanaanalytics.com) pour obtenir des exemples d’apprentissage automatique et d’analyse des données utilisant Cortana Analytics Suite. 

<!---HONumber=AcomDC_0413_2016-->