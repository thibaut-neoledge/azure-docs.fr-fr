<properties 
	pageTitle="Approvisionnement d’une machine virtuelle pour la science des données | Microsoft Azure" 
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
	ms.date="11/23/2015" 
	ms.author="bradsev" />


# Approvisionnement d’une machine virtuelle pour la science des données

## Introduction

La machine virtuelle pour la science des données Microsoft est une image de machine virtuelle Azure préalablement installée et configurée avec plusieurs outils populaires couramment utilisés dans le cadre de l’analyse de données et de l’apprentissage automatique. Elle intègre les outils suivants :

- Revolution R Open
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

    - **Name**: Name of your data science server you are creating.
    - **User Name**: Admin account login id
    - **Password**: Admin account password
    - **Subscription**: If you have more than one subscription, select the one on which the machine will be created and billed
    - **Resource Group**: You can create a new one or use an existing group
    - **Location**: Select the data center that is most appropriate. Usually it is the data center that has most of your data or is closest to your physical location for fastest network access

  **b. Taille** :

    - Select one of the server types that meets your functional requirement and cost constraints. You can get more choices of VM sizes by selecting “View All”

  **c. Paramètres**

    - **Disk Type**: Choose Premium if you prefer a solid state drive (SSD), else choose “Standard”.
    - **Storage Account**: You can create a new Azure storage account in your subscription or use an existing one in the same *Location* that was chosen on the Basics step of the wizard.
    - **Other parameters**: In most cases you will just use the default values. You can hover over the informational link for help on the specific fields in case you want to consider the use of non-default values.

  **d. Résumé** :

    - Verify that all information you entered is correct.

  **e. Acheter** :

    - Click on **Buy** to start the provisioning. A link is provided to the terms of the transaction. The VM does not have any additional charges beyond the compute for the server size you chose in the **Size** step. 


L’approvisionnement prend environ 10 à 20 minutes. L’état de l’approvisionnement est affiché sur le portail Azure.

## Accès à une machine virtuelle pour la science des données

Une fois la machine virtuelle créée, vous pouvez vous y connecter à l'aide du bureau distant en utilisant les informations d'identification du compte administrateur créé dans la section Paramètres de base de l'étape 4.

Une fois votre machine virtuelle créée et approvisionnée, vous pouvez commencer à utiliser les outils qui y sont installés et configurés. Pour la plupart des outils, vous disposez d’icônes de bureau et de vignettes dans le menu de démarrage.

## Outils installés sur la machine virtuelle de science des données

### R
Si vous souhaitez utiliser R dans le cadre de vos tâches d’analyse, l’outil Revolution R Open (RRO) est préinstallé sur votre machine virtuelle. Cette distribution open source de R est totalement compatible avec CRAN-R. Elle contient le tout dernier moteur R open source ainsi que la bibliothèque Intel Math Kernel Library. Un IDE appelé « RRO RGui » est également inclus dans la machine virtuelle. Vous pouvez télécharger et utiliser également d’autres IDE tels que [RStudio](http://www.rstudio.com).

### Python
Pour un développement basé sur Python, la version 2.7 de la distribution Anaconda Python a été installée. Cette distribution contient le langage Python de base avec environ 300 packages de mathématiques, d’ingénierie et d’analyse de données figurant parmi les plus populaires. Vous pouvez utiliser les outils Python pour Visual Studio (PTVS) installés dans l’édition Visual Studio 2015 Community ou l’un des IDE fournis avec Anaconda comme IDLE ou Spyder. Pour lancer l’un de ces IDE, vous pouvez effectuer une recherche dans la barre de recherche (**Win** + touche **S**).

### IPython Notebook
La distribution Anaconda est également fournie avec un interpréteur IPython, un environnement conçu pour le partage de code et d’analyses. Un serveur d'interpréteur Ipython a été préconfiguré. Il existe une icône de bureau qui permet de lancer le navigateur pour accéder au serveur de l’interpréteur. Si vous accédez à la machine virtuelle par l’intermédiaire d’un bureau distant, vous pouvez également utiliser l’URL [https://localhost:9999 /](https://localhost:9999/) pour accéder au serveur de l’interpréteur IPython (Remarque : si vous recevez des avertissements relatifs au certificat, vous pouvez simplement les ignorer).

### Visual Studio 2015 Community Edition
Visual Studio Community Edition est installé sur la machine virtuelle. Vous pouvez utiliser cette version gratuite de l’IDE populaire de Microsoft à des fins d’évaluation et dans le cadre de projets en petites équipes. Vous pouvez consulter les termes du contrat de licence [ici](https://www.visualstudio.com/support/legal/mt171547). Ouvrez Visual Studio en double-cliquant sur l’icône du bureau ou par le biais du menu **Démarrer**. Vous pouvez également lancer une recherche de programmes en utilisant les touches **Win** + **S** et en entrant « Visual Studio ».

Remarque : il est possible que vous receviez un message indiquant que votre période d’évaluation a expiré. Vous pouvez saisir les informations d’identification de votre compte Microsoft ou en créer un nouveau et entrer les informations pour accéder à Visual Studio Community Edition. Là, vous pouvez créer des projets dans des langages tels que C# ou Python

### SQL Server Express
Une version limitée de SQL Server est également fournie avec Visual Studio Community Edition. Vous pouvez accéder à SQL Server en lançant **SQL Server Management Studio**. Le nom de votre machine virtuelle sera celui du serveur. Utilisez l’authentification Windows une fois connecté à Windows en tant qu’administrateur. Dans SQL Server Management Studio, vous pouvez créer d’autres utilisateurs, créer des bases de données, importer des données et exécuter des requêtes SQL.

### Microsoft Azure 
Plusieurs outils Azure sont installés sur la machine virtuelle. - Il existe un raccourci sur le bureau pour accéder à la documentation du SDK Azure. - **AzCopy** permet de déplacer des données vers et depuis votre compte de stockage Microsoft Azure. - **Azure Storage Explorer** est utilisé pour parcourir les objets que vous avez stockés dans votre compte de stockage Azure. - **Microsoft Azure Powershell** est un langage de script qui permet d’administrer vos ressources Azure dans un langage de script dans votre machine virtuelle .

###Power BI

**Power BI Desktop** a été installé pour vous aider à créer des tableaux de bord et des visualisations de haute qualité. Utilisez cet outil pour extraire des données de différentes sources, créer vos tableaux de bord et vos rapports, puis les publier sur le cloud. Pour plus d’informations, consultez le site de [Power BI](http://powerbi.microsoft.com).

Remarque : vous devez disposer d’un compte Office 365 pour accéder à Power BI.

## Autres outils de développement Microsoft
Le programme [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) vous permet de détecter et de télécharger d’autres outils de développement Microsoft. Il existe également un raccourci vers l’outil fourni sur le bureau de la machine virtuelle pour la science des données.

<!---HONumber=AcomDC_1125_2015-->