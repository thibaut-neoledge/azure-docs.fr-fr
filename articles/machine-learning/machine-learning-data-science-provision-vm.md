<properties 
	pageTitle="Approvisionnement d’une machine virtuelle pour la science des données | Microsoft Azure" 
	description="Configurez et créez une machine virtuelle pour la science des données sur Windows Azure pour vos besoins d’analyse et d’apprentissage automatique." 
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
	ms.date="10/20/2015" 
	ms.author="bradsev" />


# Approvisionnement d’une machine virtuelle pour la science des données

## Introduction

La machine virtuelle pour la science des données est une image de machine virtuelle Azure préalablement installée et configurée avec plusieurs outils populaires couramment utilisés dans le cadre de l’analyse de données et de l’apprentissage automatique. Elle intègre les outils suivants :

- Revolution R Open
- Distribution Anaconda Python
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server Express Edition


La science des données implique une itération sur une séquence de tâches : recherche, chargement et prétraitement des données, création et test de modèles, et déploiement des modèles en vue d’une utilisation dans des applications intelligentes. Les chercheurs de données sont souvent amenés à utiliser divers outils pour effectuer ces tâches. Il peut être assez long de trouver les bonnes versions des logiciels, puis de les télécharger et les installer. La machine virtuelle pour la science des données est là pour leur simplifier une grande partie du processus.

Utilisez la machine virtuelle pour la science des données pour alléger votre charge de travail. Démarrez rapidement votre projet d’analyse en créant une image intégrant le logiciel couramment utilisé pour les tâches d’analyse et d’apprentissage automatique dans divers langages, notamment R, Python, SQL ou C#. Visual Studio propose un IDE qui vous permet de développer et tester très simplement votre code. Le SDK Azure inclus dans la machine virtuelle vous permet de créer des applications à l’aide de divers services disponibles sur la plateforme cloud de Microsoft.

## Configuration requise

Avant de créer une machine virtuelle Azure, vous devez disposer des éléments suivants :

- **Un abonnement Azure** : consultez la page [Obtention d’un essai gratuit Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Un compte de stockage Azure** : consultez la page [À propos des comptes de stockage Azure](storage-whatis-account.md). Le compte de stockage peut être créé dans le cadre du processus de création de la machine virtuelle si vous ne souhaitez pas utiliser un compte existant.


## Créer votre machine virtuelle pour la science des données

Voici les différentes étapes à suivre pour créer votre instance de machine virtuelle pour la science des données :

1.	Connectez-vous au portail Azure.
2.	Accédez à **Nouveau** -> **Calculer** -> **Marketplace** et recherchez *Data Science Virtual Machine*. Sélectionnez la machine virtuelle intitulée « **Data Science Virtual Machine** » et publiée par Microsoft pour afficher un panneau décrivant la machine virtuelle pour la science des données
3.	Cliquez sur le bouton **Créer** au bas de l’écran pour accéder à un Assistant. ![provision-data-science-vm](./media/machine-learning-data-science-provision-vm/provision-data-science-virtual-machine.png)
4.	 Les sections suivantes indiquent les données à saisir pour chaque étape de l’Assistant de création de la machine virtuelle pour la science des données


 **1. Paramètres de base** :

- **Name** (Nom) : nom du serveur de science des données que vous créez.
- **User Name** (Nom d’utilisateur) : identifiant de connexion du compte administrateur
- **Password** (Mot de passe) : mot de passe du compte administrateur
- **Subscription** (Abonnement) : si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine
- **Resource Group** (Groupe de ressources) : vous pouvez créer un nouveau groupe ou utiliser un groupe existant
- **Location** (Emplacement) : sélectionnez le centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau

 **2. Taille** :

- Sélectionnez l’un des types de serveur qui répond à vos exigences fonctionnelles et à vos contraintes de coût. Sélectionnez « View All » (Afficher tout) pour obtenir d’autres choix de tailles de machines virtuelles

 **3. Paramètres**

 **4. Type de disque** :

- Choisissez « Premium » si vous préférez un SSD. Sinon, choisissez « Standard »

 **5. Compte de stockage** :


- Vous pouvez créer un nouveau compte de stockage Windows Azure associé à votre abonnement ou utiliser un compte existant au même emplacement que celui que vous avez sélectionné à l’étape des paramètres de base de l’Assistant.
- Dans la plupart des cas, vous utiliserez simplement la valeur par défaut pour le reste des paramètres. Si vous ne souhaitez pas utiliser les valeurs par défaut, vous pouvez survoler le lien d’informations pour obtenir de l’aide sur un champ spécifique.
- Vérifiez que toutes les informations que vous avez saisies sont correctes.
- Cliquez sur **Buy** (Acheter) pour démarrer l’approvisionnement. Les conditions de la transaction vous sont communiquées via un lien. La machine virtuelle n’est assortie d’aucuns frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l’étape **Taille**. 

L’approvisionnement prend environ 10 à 20 minutes. L’état de l’approvisionnement est affiché sur le portail Azure.

## Comment accéder à la machine virtuelle pour la science des données

Une fois créée, vous pouvez vous connecter à votre machine virtuelle à l’aide du bureau distant en utilisant les informations d’identification du compte administrateur créé dans la section Paramètres de base de l’étape 4.

Une fois votre machine virtuelle créée et approvisionnée, vous pouvez commencer à utiliser les outils qui y sont installés et configurés. Pour la plupart des outils, vous disposez d’icônes de bureau et de vignettes dans le menu de démarrage.

## Outils installés sur la machine virtuelle pour la science des données

### R
Si vous souhaitez utiliser R dans le cadre de vos tâches d’analyse, l’outil Revolution R Open (RRO) est préinstallé sur votre machine virtuelle. Cette distribution open source de R est totalement compatible avec CRAN-R. Elle contient le tout dernier moteur R open source ainsi que la bibliothèque Intel Math Kernel Library. Un IDE appelé « RRO RGui » est également inclus dans la machine virtuelle. Vous pouvez également télécharger et utiliser d’autres IDE, tels que [RStudio](http://www.rstudio.com).

### Python
Pour un développement basé sur Python, la version 2.7 de la distribution Anaconda Python a été installée. Cette distribution contient le langage Python de base avec environ 300 packages de mathématiques, d’ingénierie et d’analyse de données figurant parmi les plus populaires. Vous pouvez utiliser les IDE fournis avec Anaconda (par exemple, IDLE ou Spyder). Pour lancer l’un de ces IDE, vous pouvez effectuer une recherche sur la barre de recherche (**Win** + touche **S**).

### IPython Notebook
La distribution Anaconda est également fournie avec un interpréteur IPython, un environnement conçu pour le partage de code et d’analyses. Un serveur d’interpréteur Ipython a été préconfiguré. Il existe une icône de bureau qui permet de lancer le navigateur pour accéder au serveur de l’interpréteur. Si vous accédez à la machine virtuelle via un bureau distant, vous pouvez également utiliser l’URL [https://localhost:9999 /](https://localhost:9999/) pour accéder au serveur de l’interpréteur IPython (Remarque : si vous recevez des avertissements relatifs au certificat, vous pouvez simplement les ignorer).

### Visual Studio 2015 Community Edition
Visual Studio Community Edition est installé sur la machine virtuelle. Vous pouvez utiliser cette version gratuite de l’IDE populaire de Microsoft à des fins d’évaluation et dans le cadre de projets en petites équipes. Cliquez ***ici*** (lien à déterminer) pour consulter les conditions d’utilisation. Ouvrez Visual Studio en double-cliquant sur l’icône du bureau ou sur le menu **Démarrer**. Vous pouvez également lancer une recherche de programmes en utilisant les touches **Win** + **S** et en entrant « Visual Studio ».

Remarque : il est possible que vous receviez un message indiquant que votre période d’évaluation a expiré. Vous pouvez saisir les informations d’identification de votre compte Microsoft ou en créer un nouveau et entrer les informations pour accéder à Visual Studio Community Edition. Là, vous pouvez créer des projets dans des langages tels que C# ou Python

### SQL Server Express
Une version limitée de SQL Server est également fournie avec Visual Studio Community Edition. Vous pouvez accéder à SQL Server en lançant **SQL Server Management Studio**. Le nom de votre machine virtuelle sera celui du serveur. Utilisez l’authentification Windows une fois connecté à Windows en tant qu’administrateur. Dans SQL Server Management Studio, vous pouvez créer d’autres utilisateurs, créer des bases de données, importer des données et exécuter des requêtes SQL.

### Microsoft Azure 
Plusieurs outils Azure sont installés sur la machine virtuelle : - il existe un raccourci bureau pour accéder à la documentation du SDK Azure. - **AzCopy** permet de déplacer des données vers et depuis votre compte de stockage Microsoft Azure. - **Azure Storage Explorer** est utilisé pour parcourir les objets que vous avez stockés dans votre compte de stockage Azure. - **Microsoft Azure Powershell** est un langage de script installé sur votre machine virtuelle qui permet d’administrer vos ressources Azure dans un langage de script.

###Power BI

Le **Power BI Desktop** a été installé pour vous aider à créer des tableaux de bord et de superbes visualisations. Utilisez cet outil pour extraire des données de différentes sources, créer vos tableaux de bord et vos rapports, puis les publier sur le cloud. Pour plus d’informations, consultez le site de [Power BI](http://powerbi.microsoft.com).

Remarque : vous devez disposer d’un compte Office 365 pour accéder à Power BI.

## Autres outils de développement Microsoft
Le programme [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) vous permet de détecter et de télécharger d’autres outils de développement Microsoft. Vous disposez également d’un raccourci vers cet outil sur le Bureau de votre machine virtuelle.

<!---HONumber=Oct15_HO4-->