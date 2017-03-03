---
title: "Qu’est-ce qu’une machine virtuelle Science des données ? | Microsoft Docs"
description: "Découvrez les scénarios et fonctionnalités clés, et familiarisez-vous avec les machines virtuelles Science des données, un environnement et les outils prêts pour l’analyse."
keywords: "outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 6c664a055e577881d4fcccd5b0ba4047d88aa9ef
ms.openlocfilehash: 64b413451c6ce47cc3aa14322b2aa0342e1e3ffe
ms.lasthandoff: 02/02/2017


---
# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Présentation de la machine virtuelle Science des données basée sur le cloud pour Linux et Windows
La machine virtuelle Science des données est une image de machine virtuelle personnalisée sur le cloud Microsoft Azure spécialement conçue pour la science des données. Elle inclut de nombreux outils de science des données populaires et d’autres outils sont préinstallés et préconfigurés afin d’accélérer la création d’applications intelligentes à des fins d’analyse avancée. Elle est disponible pour Windows Server 2012 ou sur des versions de Linux basées sur OpenLogic 7.2 CentOS. 

Cette rubrique explique ce que vous pouvez faire avec la machine virtuelle Science des données, présente les principaux scénarios d’utilisation de la machine Virtuelle, détaille les principales fonctionnalités disponibles dans les versions de Windows et Linux, et fournit des instructions sur la façon de commencer à les utiliser.

## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Que puis-je faire avec la machine virtuelle Science des données ?
L’objectif de la machine virtuelle Science des données est de fournir aux professionnels des données, tous niveaux de compétence et rôles confondus, un environnement de science des données sans erreur. Cette machine virtuelle vous fait gagner le temps considérable nécessaire qu’il vous faudrait pour déployer vous-même un environnement comparable. Commencez immédiatement votre projet de science des données dans une nouvelle instance de machine virtuelle. 

La machine virtuelle Science des données a été conçue et configurée pour fonctionner dans un grand nombre de scénarios. Vous pouvez faire évoluer ou réduire votre environnement en fonction des besoins de votre projet. Vous pouvez utiliser votre langage par défaut pour programmer les tâches de science des données. Vous pouvez installer d’autres outils et personnaliser le système en fonction de vos besoins exacts.

## <a name="key-scenarios"></a>Principaux scénarios
Cette section propose des scénarios principaux dans lesquels la machine virtuelle Science des données peut être déployée.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Bureau d’analyse préconfiguré dans le cloud
La machine virtuelle Science des données fournit une configuration de base pour les équipes de science des données cherchant à remplacer leurs postes de travail locaux par un bureau de cloud géré. Cette base garantit que tous les scientifiques de données de l’équipe disposent d’une configuration cohérente permettant de vérifier les expériences et encourageant la collaboration. Cela réduit également les coûts en réduisant les efforts pour l’administrateur système et en permettant de réduire le temps nécessaire à l’évaluation, à l’installation et la à gestion des divers packages logiciels nécessaires à l’analyse avancée.  

### <a name="data-science-training-and-education"></a>Formation et éducation de la science des données
Les formateurs d’entreprise et les instructeurs qui enseignent la science des données fournissent généralement une image de machine virtuelle pour s’assurer que leurs étudiants disposent d’une installation cohérente et que les exemples fonctionnent comme prévu. La machine virtuelle Science des données crée un environnement à la demande avec une configuration cohérente qui simplifie le support et les défis liés à la compatibilité. Ceci est d’autant plus bénéfique dans les cas où ces environnements doivent être créés fréquemment, en particulier lors de formations plus courtes.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Flexibilité à la demande pour les projets d’envergure
Les concours de science des données ou la modélisation et l’exploration de données à grande échelle nécessitent une augmentation de la capacité matérielle, généralement sur une courte durée. La machine virtuelle Science des données permet de répliquer rapidement et à la demande l’environnement de science des données, sur des serveurs montés offrant ainsi des expériences d’exécution des ressources informatique hautes performances.

### <a name="short-term-experimentation-and-evaluation"></a>Expérimentation et évaluation à court terme
La machine virtuelle Science des données peut être utilisée pour évaluer ou découvrir des outils tels que Microsoft R Server, SQL Server, Visual Studio, Jupyter, kits de ressources de formation approfondie / ML et de nouveaux outils populaires dans la communauté, avec un minimum d’efforts d’installation. Étant donné que la machine virtuelle Science des données peut être configurée rapidement, elle peut être appliquée dans d’autres scénarios d’utilisation à court terme comme la réplication d’expériences publiées, l’exécution de démonstrations, le suivi de procédures pas-à-pas dans des sessions en ligne ou des didacticiels de conférence.

## <a name="whats-included-in-the-data-science-vm"></a>Qu’est-ce qui est inclus dans la machine virtuelle Science des données ?
De nombreux outils de science des données populaires sont déjà installés et configurés sur la machine virtuelle Science des données. Elle inclut également des outils simplifiant l’utilisation de différents produits de données et d’analyse Azure. Vous pouvez explorer et créer des modèles prédictifs sur des jeux de données d’envergure à l’aide de Microsoft R Server ou de SQL Server 2016. D’autres outils encore de la communauté open source et de Microsoft sont également inclus, ainsi qu’un exemple de code et des notebooks. Le tableau suivant détaille et compare les principaux composants inclus dans les éditions Windows et Linux de la machine virtuelle Science des données.

| **Édition Windows** | **Édition Linux** |
| -- | --|
| Microsoft R Open avec packages courants préinstallés | Microsoft R Open avec packages courants préinstallés |
| Microsoft R Server Developer Edition avec algorithmes MicrosoftML et Microsoft R opérationnels  |Microsoft R Server Developer Edition |
| Anaconda Python 2.7, 3.5 |Anaconda Python 2.7, 3.5 avec packages courants préinstallés|
| JuliaPro avec packages courants préinstallés | JuliaPro avec packages courants préinstallés |
| Jupyter Notebook Server (R, Python, Julia) |JupyterHub : blocs-notes Jupyter multi-utilisateurs (R, Python, Julia) |
| SQL Server 2016 SP1 Developer Edition : analyse en base de données évolutive avec les services R |PostgreSQL, SQuirreL SQL (outil de base de données), pilotes SQL Server et ligne de commande (bcp, sqlcmd) |
|-  Visual Studio Community Edition 2015 (IDE) </br> - Azure HDInsight (Hadoop), Data Lake, SQL Server Data tools </br> - Node.js, Python et outils R pour Visual Studio (RTVS 0.5) </br>- R Studio Desktop|IDE et éditeurs </br> - Plug-in du kit de ressources Eclipse avec Azure </br> - Emacs (avec SSE, auctex) gedit </br> - IntelliJ IDEA</br> - PyCharm</br> - Atom</br> - Visual Studio Code|
| Power BI Desktop |-- |
| Outils d’apprentissage automatique </br> - Intégration à Azure Machine Learning </br> - Formation approfondie/AI Microsoft Cognitive Toolkit (CNTK 2.0) </br> - Xgboost (outil ML populaire dans les concours de science des données) </br> - Vowpal Wabbit (apprenant en ligne rapide) </br> - Rattle (outils de données et d’analyse à démarrage rapide visuel) </br> - Mxnet (formation approfondie/AI) </br> - Tensorflow  |Outils d’apprentissage automatique </br> - Intégrations à Azure Machine Learning </br> - CNTK (formation approfondie/AI) </br> - Xgboost (outil ML populaire dans les concours de science des données) </br> - Vowpal Wabbit (apprenant en ligne rapide) </br> - Rattle (outils de données et d’analyse à démarrage rapide visuel) </br> - Mxnet (formation approfondie/AI)|
| Kits de développement logiciel (SDK) pour accéder à Azure et à la suite de services Cortana Intelligence |Kits de développement logiciel (SDK) pour accéder à Azure et à la suite de services Cortana Intelligence |
| Outils pour le déplacement des données et la gestion des ressources Azure et Big Data : Storage Explorer, CLI, PowerShell, AdlCopy (Azure Data Lake), AzCopy, dtui (pour DocumentDB), Microsoft Data Management Gateway |Outils pour le déplacement des données et la gestion des ressources Azure et Big Data : Azure Storage Explorer, CLI |
| Git, plug-in Visual Studio Team Services |Git |
| Port Windows des utilitaires de ligne de commande Linux/Unix les plus populaires accessible via GitBash/l’invite de commande |-- |
| Weka | Weka |
| Drill | Drill |
| --| Spark local |

## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Prise en main de la machine virtuelle Science des données Windows
* Créez une instance de la machine virtuelle sur Windows en accédant à [cette page](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) et en sélectionnant le bouton vert **Créer une machine virtuelle**.
* Connectez-vous à la machine virtuelle à partir de votre Bureau à distance en utilisant les informations d’identification que vous avez spécifiées lorsque vous avez créé la machine virtuelle.
* Pour découvrir et lancer les outils disponibles, cliquez sur le menu **Démarrer**.

## <a name="get-started-with-the-linux-data-science-vm"></a>Prise en main de la machine virtuelle Science des données Linux
* Créez une instance de la machine virtuelle sur Linux (basée sur OpenLogic CentOS) en accédant à [cette page](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) et en sélectionnant le bouton vert **Créer une machine virtuelle**.
* Connectez-vous à la machine virtuelle à partir d’un client SSH, comme une commande Putty ou SSH, en utilisant les informations d’identification que vous avez spécifiées lorsque vous avez créé la machine virtuelle.
* Dans l’invite de commandes, entrez dsvm-plus-info.
* Pour un ordinateur de bureau graphique, téléchargez le client X2Go pour votre plateforme cliente [ici](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) et suivez les instructions dans le document de la machine virtuelle Science des données Linux [Approvisionnement d’une machine virtuelle de science des données Linux](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>Étapes suivantes
### <a name="for-the-windows-data-science-vm"></a>Pour la machine virtuelle Science des données Windows
* Pour plus d’informations sur l’exécution des outils spécifiques disponibles sur la version Windows, consultez [Approvisionnement d’une machine virtuelle pour la science des données](machine-learning-data-science-provision-vm.md) et
* Pour plus d’informations sur l’exécution de diverses tâches nécessaires à votre projet de science des données sur la machine virtuelle Windows, consultez [Dix choses que vous pouvez effectuer sur la machine virtuelle pour la science des données](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Pour la machine virtuelle Science des données Linux
* Pour plus d’informations sur l’exécution des outils spécifiques disponibles sur la version Linux, consultez [Approvisionnement d’une machine virtuelle pour la science des données Linux](machine-learning-data-science-linux-dsvm-intro.md).
* Pour une procédure montrant comment effectuer plusieurs tâches courantes relatives à la science des données avec la machine virtuelle Linux, consultez [Science des données sur la machine virtuelle de science des données Linux](machine-learning-data-science-linux-dsvm-walkthrough.md).


