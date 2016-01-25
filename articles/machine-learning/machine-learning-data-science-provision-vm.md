<properties 
	pageTitle="Approvisionnement d’une machine virtuelle pour la science des données | Microsoft Azure" 
	description="Configurez et créez une machine virtuelle pour la science des données sur Microsoft Azure pour vos besoins d’analyse et d’apprentissage automatique." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="bradsev" />


# Approvisionnement d’une machine virtuelle pour la science des données

## Introduction

La machine virtuelle pour la science des données Microsoft est une image de machine virtuelle Azure préalablement installée et configurée avec plusieurs outils populaires couramment utilisés dans le cadre de l’analyse de données et de l’apprentissage automatique. Elle intègre les outils suivants :

- Microsoft R Server Developer Edition
- Distribution Anaconda Python
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server Express Edition
- Kit de développement logiciel (SDK) Azure


La science des données implique une itération sur une séquence de tâches : recherche, chargement et prétraitement des données, création et test de modèles, et déploiement des modèles en vue d’une utilisation dans des applications intelligentes. Les chercheurs de données sont souvent amenés à utiliser divers outils pour effectuer ces tâches. La recherche des versions adéquates des logiciels, puis leur téléchargement et leur installation peuvent prendre un certain temps. La machine virtuelle pour la science des données est là pour vous soulager de cette charge.

Le renvoi de la machine virtuelle pour la science des données démarre votre projet d’analyse. Elle vous permet de travailler sur des tâches basées sur différents langages, notamment R, Python, SQL et C#. Visual Studio propose un IDE qui vous permet de développer et tester très simplement votre code. Le SDK Azure inclus dans la machine virtuelle vous permet de créer des applications à l’aide de divers services disponibles sur la plateforme cloud de Microsoft.

Cette image de machine virtuelle de science des données ne génère pas de frais. Vous payez uniquement les frais d’utilisation Azure en fonction de la taille de l’ordinateur virtuel approvisionnée avec cette image de machine virtuelle. Vous trouverez d’autres informations sur les frais de calcul [ici](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/).


## Composants requis

Avant de pouvoir créer une machine virtuelle de science des données Microsoft, vous devez disposer des éléments suivants :

- **Un abonnement Azure** : pour obtenir un abonnement, consultez la page [Obtenir une version d’évaluation gratuite d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Un compte de stockage Azure** : pour créer un compte, référez-vous à la rubrique [Création d’un compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account). Le compte de stockage peut être également créé dans le cadre du processus de création de la machine virtuelle si vous ne souhaitez pas utiliser un compte existant.


## Création d’une machine virtuelle pour la science des données

Voici les étapes de création d’une instance de la machine virtuelle de sciences des données :

1.	Accédez à la liste des machines virtuelles présentes sur le [portail Azure](https://portal.azure.com/#gallery/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.	 Cliquez sur le bouton **Créer** au bas de l’écran pour accéder à un assistant.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 Les sections suivantes fournissent les **entrées** de chacune des **5 étapes** (énumérées à droite de la figure ci-dessus) de l’Assistant utilisé pour créer la machine virtuelle de sciences de données. Voici les entrées nécessaires à la configuration de chacune de ces étapes :

  **a. Paramètres de base** :

   - **Name** (Nom) : nom du serveur Data Science que vous créez.
   - **User Name** (Nom d’utilisateur) : identifiant de connexion du compte administrateur.
   - **Password** (Mot de passe) : mot de passe du compte administrateur.
   - **Subscription** (Abonnement) : si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine.
   - **Resource Group** (Groupe de ressources) : vous pouvez créer un nouveau groupe ou utiliser un groupe existant.
   - **Location** (Emplacement) : sélectionnez le centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau

  **b. Taille** :

   - Sélectionnez l’un des types de serveur qui répond à vos exigences fonctionnelles et à vos contraintes de coût. Sélectionnez « View All » (Afficher tout) pour obtenir d’autres choix de tailles de machines virtuelles

  **c. Paramètres**

   - **Disk Type** (Type de disque) : choisissez Premium si vous préférez un disque SSD. Sinon, choisissez « Standard ».
   - **Storage Account** (Compte de stockage) : vous pouvez créer un nouveau compte de stockage Azure associé à votre abonnement ou utiliser un compte existant au même *emplacement* que celui que vous avez sélectionné à l’étape de définition des paramètres de base de l’Assistant.
   - **Other parameters** (Autres paramètres) : dans la plupart des cas, vous utiliserez simplement la valeur par défaut. Si vous ne souhaitez pas utiliser les valeurs par défaut, vous pouvez survoler le lien d'informations pour obtenir de l'aide sur des champs spécifiques.

  **d. Résumé** :

   - Vérifiez que toutes les informations que vous avez saisies sont correctes.

  **e. Acheter** :

   - Cliquez sur **Buy** (Acheter) pour démarrer l’approvisionnement. Les conditions de la transaction vous sont communiquées via un lien. La machine virtuelle n'est pas assortie de frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l'étape **Taille**. 


L’approvisionnement prend environ 10 à 20 minutes. L’état de l’approvisionnement est affiché sur le portail Azure.

## Accès à une machine virtuelle pour la science des données

Une fois la machine virtuelle créée, vous pouvez vous y connecter à l'aide du bureau distant en utilisant les informations d'identification du compte administrateur créé dans la section Paramètres de base de l'étape 4.

Une fois votre machine virtuelle créée et approvisionnée, vous pouvez commencer à utiliser les outils qui y sont installés et configurés. Pour la plupart des outils, vous disposez d'icônes de bureau et de vignettes dans le menu de démarrage.

## Création d'un mot de passe fort sur le serveur Jupyter Notebook 

Exécutez la commande suivante à partir de l'invite de commandes sur la machine virtuelle de sciences de données pour créer votre propre mot de passe fort pour le serveur Jupyter Notebook installé sur la machine.

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

À l'invite, choisissez un mot de passe fort.

Vous verrez le hachage du mot de passe au format « sha1:xxxxxx » dans la sortie. Copiez ce hachage de mot de passe et remplacez le hachage existant dans votre fichier de configuration de bloc-notes qui se trouve dans **C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py** avec un nom de paramètre ***c.NotebookApp.password***.

Vous devez remplacer seulement la valeur existante du hachage qui figure entre les guillemets. Les guillemets et le préfixe ***sha1:*** de la valeur du paramètre doivent être conservés.

Enfin, vous devez arrêter et redémarrer le serveur Ipython qui s’exécute sur la machine virtuelle comme une tâche planifiée Windows appelée « Start\_IPython\_Notebook ». Si votre nouveau mot de passe n’est pas accepté après le redémarrage de cette tâche, essayez en redémarrant la machine virtuelle.

## Outils installés sur la machine virtuelle de science des données

### Microsoft R Server Developer Edition
Si vous souhaitez utiliser R pour votre analyse, Microsoft R Server Developer Edition est installé sur la machine virtuelle. Microsoft R Server est une plateforme d'analyse d'entreprise très déployable basée sur R qui est prise en charge, évolutive et sécurisée. Prenant en charge les statistiques Big Data, la modélisation prédictive et des fonctionnalités Machine Learning, R Server prend en charge la totalité de l'analyse : exploration, analyse, visualisation et modélisation. En utilisant et en étendant R Open Source, Microsoft R Server est entièrement compatible avec les scripts, les fonctions et les packages CRAN R pour analyser les données à l'échelle de l'entreprise. Il gère également les limitations de mémoire de R Open Source en ajoutant le traitement parallèle et en bloc des données dans Microsoft R Server, permettant aux utilisateurs d'exécuter des analyses de données plus volumineuses que ce que la mémoire principale peut contenir. Un IDE pour R est également inclus dans la machine virtuelle auquel vous pouvez accéder en cliquant sur l'icône « Revolution R Enterprise 8.0 » dans le menu Démarrer ou sur le bureau. Vous pouvez télécharger et utiliser d’autres IDE, comme [RStudio](http://www.rstudio.com).

### Python
Pour un développement basé sur Python, les versions 2.7 et 3.5 de la distribution Anaconda Python ont été installées. Cette distribution contient le langage Python de base avec environ 300 packages de mathématiques, d’ingénierie et d’analyse de données figurant parmi les plus populaires. Vous pouvez utiliser les outils Python pour Visual Studio (PTVS) installés dans l’édition Visual Studio 2015 Community ou l’un des IDE fournis avec Anaconda comme IDLE ou Spyder. Pour lancer l’un de ces IDE, vous pouvez effectuer une recherche dans la barre de recherche (**Win**+**S**).

### Bloc-notes Jupyter
La distribution Anaconda est également fournie avec un serveur Jupyter Notebook, un environnement conçu pour le partage de code et d'analyses. Un serveur Jupyter Notebook a été préconfiguré avec Python 2, Python 3 et les noyaux R. Une icône de bureau nommée « Jupyter Notebook » permet de lancer le navigateur pour accéder au serveur. Si vous accédez à la machine virtuelle via Bureau à distance, vous pouvez également utiliser l'URL [https://localhost:9999/](https://localhost:9999/) pour accéder au serveur Jupyter Notebook. Remarque : si vous recevez des avertissements relatifs au certificat, vous pouvez les ignorer. Nous avons inclus des exemples de blocs-notes : l'un dans Python et l'autre dans R. Après vous être authentifié auprès du serveur Jupyter Notebook avec le mot de passe créé à l'étape précédente, vous pouvez voir le lien vers les exemples sur la page d'accueil du bloc-notes.

### Visual Studio 2015 Community Edition
Visual Studio Community Edition est installé sur la machine virtuelle. Vous pouvez utiliser cette version gratuite de l’IDE populaire de Microsoft à des fins d’évaluation et dans le cadre de projets en petites équipes. Vous pouvez consulter les termes du contrat de licence [ici](https://www.visualstudio.com/support/legal/mt171547). Ouvrez Visual Studio en double-cliquant sur l’icône du bureau ou via le menu **Démarrer**. Vous pouvez également lancer une recherche de programmes avec **Win**+**S** et en entrant « Visual Studio ».

Remarque : il est possible que vous receviez un message indiquant que votre période d’évaluation a expiré. Vous pouvez saisir les informations d’identification de votre compte Microsoft ou en créer un nouveau et entrer les informations pour accéder à Visual Studio Community Edition. Là, vous pouvez créer des projets dans des langages tels que C# ou Python. Vous trouverez également des plug-ins installés. Ceux-ci facilitent l'utilisation des services Azure tels que Azure Data Catalog, Azure HDInsight (Hadoop, Spark) et Azure Data Lake.

### SQL Server Express
Une version limitée de SQL Server est également fournie avec Visual Studio Community Edition. Vous pouvez accéder à SQL Server en lançant **SQL Server Management Studio**. Le nom de votre machine virtuelle sera celui du serveur. Utilisez l’authentification Windows une fois connecté à Windows en tant qu’administrateur. Dans SQL Server Management Studio, vous pouvez créer d’autres utilisateurs, créer des bases de données, importer des données et exécuter des requêtes SQL.

### Microsoft Azure 
Plusieurs outils Azure sont installés sur la machine virtuelle : - Il existe un raccourci sur le Bureau pour accéder à la documentation du Kit de développement logiciel (SDK) Azure. - **AzCopy** permet de déplacer des données vers et depuis votre compte de stockage Microsoft Azure. - L’**Explorateur de stockage Azure** est utilisé pour parcourir les objets que vous avez stockés dans votre compte de stockage Azure. - **Microsoft Azure Powershell** est un langage de script qui permet d’administrer vos ressources Azure dans un langage de script qui est également installé sur votre machine virtuelle.

###Power BI

**Power BI Desktop** a été installé pour vous aider à créer des tableaux de bord et des visualisations attrayantes. Utilisez cet outil pour extraire des données de différentes sources, créer vos tableaux de bord et vos rapports, puis les publier sur le cloud. Pour plus d'informations, consultez le site de [Power BI](http://powerbi.microsoft.com).

Remarque : vous devez disposer d’un compte Office 365 pour accéder à Power BI.

## Autres outils de développement Microsoft
Le programme [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) vous permet de découvrir et de télécharger d’autres outils de développement Microsoft. Il existe également un raccourci vers l’outil fourni sur le bureau de la machine virtuelle pour la science des données.

## Étapes suivantes
Voici quelques étapes supplémentaires pour poursuivre votre travail d'apprentissage et d'exploration.

* Explorez les différents outils de science des données sur la machine virtuelle de science des données en cliquant sur le menu Démarrer et en consultant les outils répertoriés dans le menu.
* Accédez à **C:\\Program Files\\Microsoft\\MRO-for-RRE\\8.0\\R-3.2.2\\library\\RevoScaleR\\demoScripts** pour obtenir des exemples utilisant la bibliothèque RevoScaleR dans R qui prend en charge l'analyse des données à l'échelle de l'entreprise.  
* Apprenez à créer des solutions analytiques de bout en bout systématiquement à l'aide du [processus de science des données](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visitez la [galerie Cortana Analytics](http://gallery.cortanaanalytics.com) pour obtenir des exemples de Machine Learning et d'analyse des données utilisant Cortana Analytics Suite. Nous avons également inclus une icône dans le menu Démarrer et sur le bureau de la machine virtuelle pour un accès aisé. 

<!---HONumber=AcomDC_0114_2016-->