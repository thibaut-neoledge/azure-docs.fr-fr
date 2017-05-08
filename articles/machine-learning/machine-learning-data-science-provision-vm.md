---
title: "Approvisionnement d’une machine virtuelle pour la science des données Microsoft | Microsoft Docs"
description: "Configurez et créez une machine virtuelle pour la science des données sur Microsoft Azure pour vos besoins d’analyse et d’apprentissage automatique."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 8df9fbf4b09cb63cafc9d43592edc68068a28f14
ms.lasthandoff: 04/20/2017


---
# <a name="provision-the-microsoft-data-science-virtual-machine"></a>Approvisionnement d’une machine virtuelle pour la science des données
La machine virtuelle pour la science des données Microsoft est une image de machine virtuelle Azure préalablement installée et configurée avec plusieurs outils populaires couramment utilisés dans le cadre de l’analyse de données et de l’apprentissage automatique. Elle intègre les outils suivants :

* Microsoft R Server Developer Edition
* Distribution Anaconda Python
* Jupyter Notebook (avec noyaux R, Python)
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2016 Developer Edition
* Outils Machine Learning et Analyse des données
  * [Computational Network Toolkit (CNTK)](https://github.com/Microsoft/CNTK): kit de ressources logicielles d’apprentissage approfondi de Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): système de Machine Learning rapide prenant en charge des techniques (apprentissage en ligne, hachage, allreduce, réductions, learning2search, actif et interactif).
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): outil offrant une implémentation rapide et précise des arborescences optimisées.
  * [Rattle (R Analytical Tool To Learn Easily)](http://rattle.togaware.com/) : outil qui facilite la prise en main de l’analyse des données et du Machine Learning dans R avec une exploration des données basée sur une interface graphique utilisateur et une modélisation utilisant la génération automatique de code R.
  * [mxnet](https://github.com/dmlc/mxnet): infrastructure de formation approfondie conçue pour offrir efficacité et flexibilité.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : logiciel Machine Learning et d’exploration des données visuelle dans Java.
  * [Apache Drill](https://drill.apache.org/) : moteur de requêtes SQL sans schéma pour Hadoop, NoSQL et le stockage cloud.  Prend en charge les interfaces ODBC et JDBC pour activer l’interrogation de NoSQL et des fichiers à partir d’outils décisionnels standard tels que Power BI, Excel, Tableau.
* Bibliothèques dans les langages R et Python à utiliser dans Azure Machine Learning et d’autres services Azure
* Git incluant Git Bash pour travailler avec les référentiels de code source, notamment GitHub, Visual Studio Team Services
* Ports Windows de plusieurs utilitaires de ligne de commande Linux populaires (notamment awk, sed, perl, grep, find, wget, curl, etc.) accessibles via l’invite de commandes. 

La science des données implique une itération sur une séquence de tâches : recherche, chargement et prétraitement des données, création et test de modèles, et déploiement des modèles en vue d’une utilisation dans des applications intelligentes. Les scientifiques de données utilisent différents outils pour effectuer ces tâches. La recherche des versions adéquates des logiciels, puis leur téléchargement et leur installation peuvent prendre un certain temps. La machine virtuelle pour la science des données Microsoft peut faciliter cette tâche en fournissant une image prête à l’emploi qui peut être approvisionnée sur Azure avec tous les outils populaires préalablement installés et configurés. 

Le renvoi de la machine virtuelle pour la science des données démarre votre projet d’analyse. Elle vous permet de travailler sur des tâches basées sur différents langages, notamment R, Python, SQL et C#. Visual Studio propose un IDE qui vous permet de développer et tester très simplement votre code. Le SDK Azure inclus dans la machine virtuelle vous permet de créer des applications à l’aide de divers services disponibles sur la plateforme cloud de Microsoft. 

Cette image de machine virtuelle de science des données ne génère pas de frais. Vous payez uniquement les frais d’utilisation Azure en fonction de la taille de la machine virtuelle approvisionnée. Vous trouverez plus d’informations sur les frais de calcul dans la section sur les détails de tarification de la page [Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) (Machine virtuelle pour la science des données). 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Autres versions de la machine virtuelle pour la science des données
Une image [CentOS](machine-learning-data-science-linux-dsvm-intro.md) est également disponible avec la plupart des même outils que l’image Windows. Une image [Ubuntu](machine-learning-data-science-dsvm-ubuntu-intro.md) est également disponible, avec de nombreux outils similaires et des cadres de formation approfondis.

## <a name="prerequisites"></a>Composants requis
Avant de pouvoir créer une machine virtuelle de science des données Microsoft, vous devez disposer des éléments suivants :

* **Un abonnement Azure**: pour obtenir un abonnement, consultez la page [Obtenir une version d’évaluation gratuite d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un compte de stockage Azure**: pour en créer un, consultez la page [Créer un compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account). Le compte de stockage peut également être créé dans le cadre du processus de création de la machine virtuelle si vous ne souhaitez pas utiliser de compte existant.

## <a name="create-your-microsoft-data-science-virtual-machine"></a>Création d’une machine virtuelle pour la science des données
Voici les étapes de création d’une instance de la machine virtuelle de sciences des données :

1. Accédez à la liste des machines virtuelles présentes sur le [portail Azure](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2. Sélectionnez le bouton **Créer** au bas de l’écran pour accéder à un Assistant.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3. L’Assistant utilisé pour créer la machine virtuelle pour la science des données Microsoft nécessite les **entrées** de chacune des **cinq étapes** énumérées à droite de cette figure. Voici les entrées nécessaires à la configuration de chacune de ces étapes :
   
   1. **Concepts de base**
      
      1. **Name**(Nom) : nom du serveur Data Science que vous créez.
      2. **Nom d’utilisateur**: identifiant de connexion du compte administrateur.
      3. **Mot de passe**: mot de passe du compte administrateur.
      4. **Subscription**(Abonnement) : si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine.
      5. **Resource Group**(Groupe de ressources) : vous pouvez créer un nouveau groupe ou utiliser un groupe existant.
      6. **Location**(Emplacement) : sélectionnez le centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau
   2. **Taille**: sélectionnez l’un des types de serveur qui répond à vos exigences fonctionnelles et à vos contraintes de coût. Sélectionnez « Afficher tout » pour obtenir d’autres choix de tailles de machines virtuelles
   3. **Paramètres**:
      
      1. **Type de disque** : choisissez Premium si vous préférez un disque SSD. Sinon, choisissez « Standard ».
      2. **Compte de stockage** : vous pouvez créer un compte de stockage Azure associé à votre abonnement ou utiliser un compte existant au même *emplacement* que celui que vous avez sélectionné à l’étape de définition des **Paramètres de base** de l’Assistant.
      3. **Autres paramètres**: généralement, vous utilisez simplement la valeur par défaut. Si vous ne souhaitez pas utiliser les valeurs par défaut, vous pouvez survoler le lien d'informations pour obtenir de l'aide sur des champs spécifiques.
   4. **Résumé**: vérifiez que toutes les informations que vous avez saisies sont correctes.
   5. **Acheter** : cliquez sur **Acheter** pour démarrer l’approvisionnement. Les conditions de la transaction vous sont communiquées via un lien. La machine virtuelle n'est pas assortie de frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l'étape **Taille** . 

> [!NOTE]
> L’approvisionnement prend environ 10 à 20 minutes. L’état de l’approvisionnement est affiché sur le portail Azure.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Accès à une machine virtuelle pour la science des données
Une fois la machine virtuelle créée, vous pouvez vous y connecter à l’aide du bureau distant en utilisant les informations d’identification du compte administrateur que vous avez configurées dans la section **Paramètres de base** précédente. 

Une fois votre machine virtuelle créée et approvisionnée, vous pouvez commencer à utiliser les outils qui y sont installés et configurés. Pour la plupart des outils, vous disposez d'icônes de bureau et de vignettes dans le menu de démarrage. 

## <a name="how-to-create-a-strong-password-for-jupyter-and-start-the-notebook-server"></a>Création d’un mot de passe fort pour Jupyter et démarrage du serveur Notebook
Par défaut, le serveur Jupyter Notebook est préconfiguré mais désactivé sur la machine virtuelle jusqu’à ce que vous définissiez un mot de passe Jupyter. Pour créer un mot de passe fort pour le serveur Jupyter Notebook installé sur l’ordinateur, exécutez la commande suivante à partir d’une invite de commandes sur la machine virtuelle pour la science des données OU double-cliquez sur le raccourci du bureau que nous vous avons fourni appelé **Jupyter Set Password & Start** (Définir le mot de passe Jupyter et démarrer) à partir d’un compte d’administrateur de machine virtuelle local.

    C:\dsvm\tools\setup\JupyterSetPasswordAndStart.cmd

Suivez les messages et à l’invite, choisissez un mot de passe fort.

Le script ci-dessus crée un hachage de mot de passe et le stocke dans le fichier de configuration Jupyter situé dans : **C:\ProgramData\jupyter\jupyter_notebook_config.py** sous le nom de paramètre ***c.NotebookApp.password***.

En outre, le script active et exécute le serveur Jupyter en arrière-plan. Le serveur Jupyter est créé en tant que tâche Windows dans le Planificateur de tâches Windows appelé **Start_IPython_Notebook**.  Après la définition du mot de passe, vous devrez peut-être attendre quelques secondes avant d’ouvrir le notebook dans votre navigateur. Consultez la section ci-dessous intitulée **Jupyter Notebook** sur la façon d’accéder au serveur Jupyter Notebook. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Outils installés sur la machine virtuelle de science des données
### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Developer Edition
Si vous souhaitez utiliser R pour votre analyse, Microsoft R Server Developer Edition est installé sur la machine virtuelle. Microsoft R Server est une plateforme d’analyse d’entreprise basée sur R, prise en charge, extensible et sécurisée, que vous pouvez largement déployer. Prenant en charge les statistiques Big Data, la modélisation prédictive et des fonctionnalités Machine Learning, R Server prend en charge la totalité de l’analyse : exploration, analyse, visualisation et modélisation. En utilisant et en étendant R Open Source, Microsoft R Server est entièrement compatible avec les scripts, les fonctions et les packages CRAN R pour analyser les données à l'échelle de l'entreprise. Il traite également les limitations en mémoire de R Open Source en ajoutant le traitement des données en parallèle et mémorisé en bloc. Cela vous permet d’exécuter des analyses de données plus volumineuses que ce que peut contenir la mémoire principale.  Visual Studio Community Edition inclus sur la machine virtuelle contient les outils R pour l’extension Visual Studio qui fournit un IDE complet pour travailler avec R. Vous pouvez également télécharger et utiliser d’autres IDE, comme par exemple [RStudio](http://www.rstudio.com). 

### <a name="python"></a>Python
Pour un développement basé sur Python, les versions 2.7 et 3.5 de la distribution Anaconda Python ont été installées. Cette distribution contient le langage Python de base avec environ 300 packages de mathématiques, d’ingénierie et d’analyse de données figurant parmi les plus populaires. Vous pouvez utiliser les outils Python pour Visual Studio (PTVS) installés dans l’édition Visual Studio 2015 Community ou l’un des IDE fournis avec Anaconda comme IDLE ou Spyder. Pour lancer l’un de ces IDE, vous pouvez effectuer une recherche dans la barre de recherche (**Win** + **S**).

> [!NOTE]
> Pour pointer les Python Tools pour Visual Studio sur Anaconda Python 2.7 et 3.5, vous devez créer des environnements personnalisés pour chaque version. Pour définir les chemins d’accès à ces environnements dans Visual Studio 2015 Community Edition, accédez à **Outils** -> **Outils Python** -> **Environnements Python**, puis cliquez sur **+ personnalisé**. 
> 
> 

Anaconda Python 2.7 est installé sous C:\Anaconda et Anaconda Python 3.5 est installé sous c:\Anaconda\envs\py35. Pour obtenir des instructions détaillées, consultez la [documentation de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) . 

### <a name="jupyter-notebook"></a>Jupyter Notebook
La distribution Anaconda est également fournie avec un serveur Jupyter Notebook, un environnement conçu pour le partage de code et d’analyses. Un serveur Jupyter Notebook a été préconfiguré avec Python 2.7, Python 3.4, Python 3.5 et les noyaux R. Une icône de bureau nommée « Jupyter Notebook » permet de lancer le navigateur pour accéder au serveur. Si vous accédez à la machine virtuelle par le biais du bureau à distance, vous pouvez également utiliser l’URL [https://localhost:9999/](https://localhost:9999/) pour accéder au serveur Jupyter Notebook lorsque vous êtes connecté à la machine virtuelle.

> [!NOTE]
> Si vous recevez des avertissements relatifs au certificat, vous pouvez les ignorer. 
> 
> 

Nous avons inclus plusieurs exemples de notebooks dans Python et R. Les exemples Jupyter Notebook vous montrent comment travailler avec Microsoft R Server, SQL Server 2016 R Services (analyse dans la base de données), Python, Microsoft Cognitive ToolKit (CNTK) pour un apprentissage approfondi et d’autres technologies Azure, lorsque vous vous connectez à Jupyter. Après vous être authentifié auprès du serveur Jupyter Notebook avec le mot de passe créé à l’étape précédente, vous pouvez voir le lien vers les exemples sur la page d’accueil du notebook. 

### <a name="visual-studio-2015-community-edition"></a>Visual Studio 2015 Community Edition
Visual Studio Community Edition est installé sur la machine virtuelle. Vous pouvez utiliser cette version gratuite de l’IDE populaire de Microsoft à des fins d’évaluation et dans le cadre de projets en petites équipes. Vous pouvez consulter les termes du contrat de licence [ici](https://www.visualstudio.com/support/legal/mt171547).  Ouvrez Visual Studio en double-cliquant sur l’icône du bureau ou via le menu **Démarrer** . Vous pouvez également lancer une recherche de programmes avec **Win** + **S** et en entrant « Visual Studio ». Là, vous pouvez créer des projets dans des langages tels que C#, Python, R, node.js. Des plug-ins sont également installés pour faciliter l’utilisation des services Azure tels que Azure Data Catalog, Azure HDInsight (Hadoop, Spark) et Azure Data Lake. 

> [!NOTE]
> Il est possible que vous receviez un message indiquant que votre période d’évaluation a expiré. Saisissez vos informations d’identification de compte Microsoft ou créez un compte gratuit pour accéder à Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition
Une version de développement de SQL Server 2016 avec R Services pour exécuter l’analyse dans la base de données est fournie sur la machine virtuelle. R Services fournit une plateforme pour développer et déployer des applications intelligentes. Pour créer des modèles et générer des prédictions à l’aide de vos données SQL Server, vous pouvez utiliser le langage R riche et puissant, et les nombreux packages issus de la Communauté. Vous pouvez conserver les analyses à proximité des données, car R Services (dans la base de données) intègre le langage R à SQL Server. Cela élimine les coûts et les risques de sécurité liés au déplacement des données.

> [!NOTE]
> L’édition développeur de SQL Server 2016 peut uniquement être utilisée à des fins de test et de développement. Vous devez posséder une licence pour l’exécuter en production. 
> 
> 

Vous pouvez accéder à SQL Server en lançant **SQL Server Management Studio**. Le nom de votre machine virtuelle est celui du serveur. Utilisez l’authentification Windows une fois connecté à Windows en tant qu’administrateur. Dans SQL Server Management Studio, vous pouvez créer d’autres utilisateurs, créer des bases de données, importer des données et exécuter des requêtes SQL. 

Pour activer l’analyse dans la base de données à l’aide de Microsoft R, exécutez la commande suivante en tant qu’action unique dans SQL Server Management Studio après vous être connecté en tant qu’administrateur du serveur. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Microsoft Azure
Plusieurs outils Azure sont installés sur la machine virtuelle :

* Il existe un raccourci bureau pour accéder à la documentation du Kit de développement logiciel (SDK) Azure. 
* **AzCopy**: permet de déplacer des données vers et à partir de votre compte de stockage Microsoft Azure. Il suffit de taper **Azcopy** dans une invite de commandes pour afficher l’utilisation. 
* **Explorateur de stockage Microsoft Azure**: permet de parcourir les objets que vous avez stockés dans votre compte de stockage Azure et de transférer des données vers et à partir du stockage Azure. Vous pouvez taper **Explorateur de stockage** dans la recherche ou utiliser le menu Démarrer de Windows pour accéder à cet outil. 
* **Adlcopy**: permet de déplacer des données vers Azure Data Lake. Il suffit de taper **adlcopy** dans une invite de commandes pour afficher l’utilisation. 
* **dtui**: permet de déplacer des données vers et à partir d’Azure DocumentDB, une base de données NoSQL sur le cloud. Il suffit de taper **dtui** dans une invite de commandes. 
* **Passerelle de gestion des données de Microsoft** : permet le déplacement des données entre des sources de données locales et le cloud. Elle est utilisée dans les outils tels que Azure Data Factory. 
* **Microsoft Azure PowerShell** : un outil qui permet de gérer vos ressources Azure dans le langage de script PowerShell est également installé sur votre machine virtuelle. 

### <a name="power-bi"></a>Power BI
**Power BI Desktop** a été installé pour vous aider à créer des tableaux de bord et des visualisations attrayantes. Utilisez cet outil pour extraire des données de différentes sources, créer vos tableaux de bord et vos rapports, puis les publier sur le cloud. Pour plus d'informations, consultez le site de [Power BI](http://powerbi.microsoft.com) . Vous pouvez trouver le bureau Power BI dans le menu Démarrer. 

> [!NOTE]
> Vous devez disposer d’un compte Office 365 pour accéder à Power BI. 
> 
> 

## <a name="additional-microsoft-development-tools"></a>Autres outils de développement Microsoft
Le programme [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) vous permet de découvrir et de télécharger d’autres outils de développement Microsoft. Il existe également un raccourci vers l’outil fourni sur le bureau de la machine virtuelle pour la science des données.  

## <a name="important-directories-on-the-vm"></a>Répertoires importants sur la machine virtuelle
| Item | Répertoire |
| --- | --- |
| Configurations de serveur de Jupyter Notebook |C:\ProgramData\jupyter |
| Répertoire des exemples de Jupyter Notebook |c:\dsvm\notebooks |
| Autres exemples |c:\dsvm\samples |
| Anaconda (valeur par défaut : Python 2.7) |c:\Anaconda |
| Environnement Anaconda Python 3.5 |c:\Anaconda\envs\py35 |
| Répertoire de l’instance R Server autonome (instance par défaut de R basée sur R3.2.2) |C:\Program Files\Microsoft SQL Server\130\R_SERVER |
| Répertoire de l’instance de base de données R Server (R3.2.2) |C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES |
| Répertoire de l’instance Microsoft R Open (R3.3.1) |C:\Program Files\Microsoft\MRO-3.3.1 |
| Outils divers |c:\dsvm\tools |

> [!NOTE]
> Les instances de la machine virtuelle pour la science des données Microsoft créées avant 1.5.0 (avant le 3 septembre 2016) utilisaient une structure de répertoire légèrement différente à celle spécifiée dans la table précédente. 
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Voici quelques étapes supplémentaires pour poursuivre votre travail d'apprentissage et d'exploration. 

* Explorez les différents outils de science des données sur la machine virtuelle pour la science des données en cliquant sur le menu Démarrer et en consultant les outils répertoriés dans le menu.
* Accédez à **C:\Program Files\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts** pour obtenir des exemples utilisant la bibliothèque RevoScaleR dans R qui prend en charge l’analyse des données à l’échelle de l’entreprise.  
* Lisez l’article intitulé [Dix choses que vous pouvez effectuer sur la machine virtuelle pour la science des données](http://aka.ms/dsvmtenthings)
* Découvrez comment créer des solutions analytiques de bout en bout systématiquement à l’aide du [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visitez la [galerie Cortana Intelligence](http://gallery.cortanaintelligence.com) pour obtenir des exemples Machine Learning et d’analyse des données utilisant Cortana Intelligence Suite. Nous avons également inclus une icône dans le menu **Démarrer** et sur le bureau de la machine virtuelle pour accéder à cette galerie.


