---
title: "Guide de démarrage rapide pour installer les services Azure Machine Learning | Microsoft Docs"
description: "Ce guide de démarrage rapide montre comment créer des ressources Azure Machine Learning et comment installer Azure Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining, raymondl, chhavib
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: hero-article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 91d2f47a528050f644973044f96c0354b91dba25
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>Créer des comptes en préversion Azure Machine Learning et installer Azure Machine Learning Workbench
Azure Machine Learning constitue une solution d’analytique avancée et de science des données de bout en bout intégrée, destinée aux chercheurs de données professionnels pour préparer des données, développer des expérimentations et déployer des modèles à l’échelle du cloud.

Ce guide de démarrage rapide vous montre comment créer des comptes Expérimentation et Gestion des modèles dans la préversion des services Azure Machine Learning. Il vous montre également comment installer l’application de bureau Azure Machine Learning Workbench et les outils CLI. Les caractéristiques en préversion Azure Machine Learning vous sont ensuite rapidement présentées à l’aide de l’intemporel [jeu de données Fleurs d’iris](https://en.wikipedia.org/wiki/iris_flower_data_set) pour générer un modèle afin de prédire le type d’iris en fonction de certaines de ses caractéristiques physiques.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Composants requis
Actuellement, Azure Machine Learning Workbench peut être installé sur les systèmes d’exploitation suivants uniquement : Windows 10, Windows Server 2016 et macOS Sierra.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-azure-machine-learning-accounts"></a>Créer des comptes Azure Machine Learning
Utilisez le portail Azure pour provisionner les comptes Azure Machine Learning. 
1. Cliquez sur le bouton **Nouveau** (+) dans le coin supérieur gauche du portail.

2. Tapez « Machine Learning » dans la barre de recherche. Sélectionnez le résultat de la recherche intitulé **Machine Learning Experimentation (preview)** (Expérimentation Machine Learning préversion.  Cliquez sur l’étoile pour ajouter cette sélection aux favoris dans votre portail Azure.

   ![Recherche Azure Machine Learning](media/quickstart-installation/portal-more-services.png)

3. Cliquez sur **+ Ajouter** pour configurer un nouveau compte Expérimentation Machine Learning. Le formulaire détaillé s’ouvre.

   ![Compte Expérimentation Machine Learning](media/quickstart-installation/portal-create-experimentation.png)

4. Remplissez le formulaire Expérimentation Machine Learning avec les informations suivantes :

   Paramètre|Valeur suggérée|Description
   ---|---|---
   Nom du compte Expérimentation | _Nom unique_ |Choisissez un nom unique qui identifie votre compte. Vous pouvez utiliser votre propre nom, ou celui d’un service ou projet qui identifie le mieux l’expérimentation. Le nom doit compter entre 2 et 32 caractères, et ne peut contenir que des caractères alphanumériques et le tiret « - ». 
   Abonnement | _Votre abonnement_ |Abonnement Azure que vous souhaitez utiliser pour votre expérimentation. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource est facturée.
   Groupe de ressources | _Votre groupe de ressources_ | Vous pouvez définir un nouveau nom de groupe de ressources ou utiliser un nom de groupe existant dans votre abonnement.
   Lieu | _La région la plus proche de vos utilisateurs_ | Choisissez l’emplacement le plus proche de vos utilisateurs et des ressources de données.
   Nombre d’utilisateurs | 2 | Tapez le nombre d’utilisateurs. Cette sélection a une incidence sur les [tarifs](https://azure.microsoft.com/pricing/details/machine-learning/). Les deux premiers utilisateurs sont gratuits. Utilisez deux utilisateurs dans le cadre de ce guide de démarrage rapide. Vous pouvez mettre à jour le nombre d’utilisateurs ultérieurement si nécessaire dans le portail Azure.
   Compte de stockage | _Nom unique_ | Choisissez **Créer un nouveau** et indiquez un nom pour créer un compte de stockage Azure, ou choisissez **Utiliser l’existant** et sélectionnez votre compte de stockage existant dans la liste déroulante. Le compte de stockage est obligatoire et sert à stocker les artefacts de projet et les données de l’historique des exécutions. 
   Espace de travail pour le compte Expérimentation | _Nom unique_ | Entrez un nom pour le nouvel espace de travail. Le nom doit compter entre 2 et 32 caractères, et ne peut contenir que des caractères alphanumériques et le tiret « - ».
   Affecter le propriétaire de l’espace de travail | _Votre compte_ | Sélectionnez votre propre compte comme propriétaire de l’espace de travail.
   Créer un compte Gestion des modèles | *check* | Dans le cadre de l’expérience de création du compte Expérimentation, vous avez également la possibilité de créer le compte Gestion des modèles Machine Learning. Cette ressource est utilisée une fois que vous êtes prêt à déployer et gérer vos modèles en tant que services web en temps réel. Nous vous recommandons de créer le compte Gestion des modèles en même temps que le compte Expérimentation.
   Nom du compte | _Nom unique_ | Choisissez un nom unique qui identifie votre compte Gestion des modèles. Vous pouvez utiliser votre propre nom, ou celui d’un service ou projet qui identifie le mieux l’expérimentation. Le nom doit compter entre 2 et 32 caractères, et ne peut contenir que des caractères alphanumériques et le tiret « - ». 
   Niveau tarifaire de Gestion des modèles | **DEVTEST** | Cliquez sur **Aucun niveau tarifaire sélectionné** pour spécifier le niveau tarifaire de votre nouveau compte Gestion des modèles. Pour réduire les coûts, sélectionnez le niveau tarifaire **DEVTEST** s’il est disponible sur votre abonnement (disponibilité limitée), sinon sélectionnez le niveau tarifaire S1. Cliquez sur **Sélectionner** pour enregistrer la sélection du niveau tarifaire. 
   Épingler au tableau de bord | _check_ | Cochez la case **Épingler au tableau de bord** pour faciliter le suivi de votre compte Expérimentation Machine Learning sur la première page du tableau de bord de votre portail Azure.

5. Cliquez sur **Créer** pour commencer le processus de création.

6. Dans la partie supérieure droite de la barre d’outils du portail Azure, cliquez sur **Notifications** (icône représentant une cloche) pour surveiller le processus de déploiement. 

   La notification indique « Déploiement en cours... ». L’état devient « Déploiement réussi » une fois que l’opération est terminée. La page de votre compte Expérimentation Machine Learning s’ouvre en cas de réussite.
   
   ![Notifications du portail Azure](media/quickstart-installation/portal-notification.png)

À présent, selon le système d’exploitation que vous utilisez sur votre ordinateur local, suivez l’une des deux sections suivantes pour installer Azure Machine Learning Workbench sur votre ordinateur. 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>Installer Azure Machine Learning Workbench sur Windows
Installez Azure Machine Learning Workbench sur votre ordinateur exécutant Windows 10, Windows Server 2016, ou une version plus récente.

1. Téléchargez le programme d’installation d’Azure Machine Learning Workbench le plus récent, **[AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi)**.

2. Double-cliquez sur le programme d’installation téléchargé _AmlWorkbenchSetup.msi_ à partir de l’Explorateur de fichiers.

   >[!IMPORTANT]
   >Téléchargez le programme d’installation entièrement sur le disque, puis lancez-le à partir de là. Ne le lancez pas directement à partir du widget de téléchargement de votre navigateur.

3. Terminez l’installation en suivant les instructions à l’écran.

   Le programme d’installation télécharge tous les composants dépendants nécessaires, tels que Python, Miniconda et d’autres bibliothèques associées. L’installation peut prendre environ une demi-heure pour l’ensemble des composants. 

4. Azure Machine Learning Workbench est maintenant installé dans le répertoire suivant :
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>Installer Azure Machine Learning Workbench sur macOS
Installez Azure Machine Learning Workbench sur votre ordinateur exécutant macOS Sierra.

1. Installez la bibliothèque openssl avec [Homebrew](http://brew.sh). Consultez [Configuration requise pour .NET Core sur Mac](https://docs.microsoft.com/dotnet/core/macos-prerequisites) pour plus d’informations.
   ```
   # install Homebrew first if you don't have it already
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

   # install latest openssl needed for .NET Core 1.x
   brew update
   brew install openssl
   mkdir -p /usr/local/lib
   ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
   ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
   ```

2. Téléchargez le programme d’installation d’Azure Machine Learning Workbench le plus récent, **[AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg)**.

   >[!IMPORTANT]
   >Téléchargez le programme d’installation entièrement sur le disque, puis lancez-le à partir de là. Ne le lancez pas directement à partir du widget de téléchargement de votre navigateur.

3. Double-cliquez sur le programme d’installation téléchargé _AmlWorkbench.dmg_ à partir de Finder.

4. Terminez l’installation en suivant les instructions à l’écran.

   Le programme d’installation télécharge tous les composants dépendants nécessaires, tels que Python, Miniconda et d’autres bibliothèques associées. L’installation peut prendre environ une demi-heure pour l’ensemble des composants. 

5. Azure Machine Learning Workbench est maintenant installé dans le répertoire suivant : 

   _/Applications/AmlWorkbench.app_

## <a name="run-azure-machine-learning-workbench-to-log-in-the-first-time"></a>Exécuter Azure Machine Learning Workbench pour la première connexion
1. Cliquez sur le bouton **Launch Workbench** (Lancer Workbench) sur le dernier écran du programme d’installation une fois le processus d’installation terminé. Si vous avez fermé le programme d’installation, recherchez le raccourci vers Machine Learning Workbench sur votre bureau et démarrez le menu nommé **Azure Machine Learning Workbench** pour lancer l’application.

2. Connectez-vous à Workbench avec le même compte que vous avez utilisé précédemment pour provisionner vos ressources Azure. 

3. Une fois le processus de connexion terminé, Workbench tente de trouver les comptes Expérimentation Machine Learning que vous avez créés précédemment. Il recherche tous les abonnements Azure auxquels vos informations d’identification ont accès. Quand au moins un compte Expérimentation est trouvé, Workbench s’ouvre avec ce compte. Il répertorie ensuite les espaces de travail et projets se trouvant dans ce compte. 

   >[!TIP]
   > Si vous avez accès à plusieurs comptes Expérimentation, vous pouvez basculer vers un autre en cliquant sur l’icône représentant un avatar dans le coin inférieur gauche de l’application Workbench.

Consultez [Configuration de l’environnement de déploiement](deployment-setup-configuration.md) pour créer un environnement pour le déploiement de vos services web.

## <a name="create-a-new-project"></a>Création d'un projet
1. Lancez l’application Azure Machine Learning Workbench et connectez-vous. 

2. Cliquez sur **Fichier** --> **Nouveau projet** (ou cliquez sur le signe **+** dans le volet **PROJETS**). 

3. Renseignez les champs **Nom du projet** et **Répertoire du projet**. Le champ **Description du projet** est facultatif, mais utile. Laissez le champ **URL du dépôt GIT Visualstudio.com** vide pour l’instant. Choisissez un espace de travail, puis sélectionnez **Classifying Iris** (Classification d’iris) comme modèle de projet.

   >[!TIP]
   >Si vous le souhaitez, vous pouvez renseigner le champ du texte du dépôt Git avec l’URL d’un dépôt Git qui est hébergé dans un projet [VSTS (Visual Studio Team Services)](https://www.visualstudio.com). Ce dépôt Git doit déjà exister et il doit être vide, sans aucune branche maître. Vous devez disposer d’un accès en écriture à ce référentiel. L’ajout d’un dépôt Git maintenant vous permet d’activer des scénarios d’itinérance et de partage plus tard. [En savoir plus](using-git-ml-project.md).

4. Cliquez sur le bouton **Créer** pour créer le projet. Un projet est créé et ouvert pour vous. À ce stade, vous pouvez explorer la page d’accueil du projet, les sources de données, les blocs-notes, les fichiers de code source. 

    >[!TIP]
    >Vous pouvez également ouvrir le projet dans Visual Studio Code ou d’autres éditeurs simplement en configurant un lien de l’IDE (Integrated Development Environment), puis en y ouvrant le répertoire du projet. [En savoir plus](how-to-configure-your-IDE.md). 

## <a name="run-a-python-script"></a>Exécuter un script Python
Nous allons exécuter un script sur votre ordinateur local. 

1. Chaque projet s’ouvre dans sa propre page **Tableau de bord Projet**. Sélectionnez `local` comme cible d’exécution à partir de la barre de commandes près du haut de l’application à gauche du bouton Exécuter et `iris_sklearn.py` comme script à exécuter.  Il existe plusieurs autres fichiers inclus dans l’exemple que vous pouvez extraire plus tard. 

   ![img](media/quickstart-installation/run_control.png)

2. Dans le champ de texte **Arguments**, entrez `0.01`. Ce nombre est utilisé dans le code pour définir le taux de régularisation, une valeur utilisée pour configurer la façon dont le modèle de régression linéaire est formé. 

3. Cliquez sur le bouton **Exécuter** pour commencer l’exécution d’`iris_sklearn.py` sur votre ordinateur. 

   Ce code utilise l’algorithme de [régression logistique](https://en.wikipedia.org/wiki/logistic_regression) de la populaire bibliothèque [scikit-learn](http://scikit-learn.org/stable/index.html) Python pour générer le modèle.

4. Le panneau **Tâches** sort de la droite s’il n’est pas déjà visible et une tâche `iris_sklearn` est ajoutée au panneau. Son état passe d’**Envoi** à **Exécution** quand l’exécution de la tâche commence, puis à **Terminé** en quelques secondes. 

5. Félicitations ! Vous avez exécuté un script Python dans Azure Machine Learning Workbench.

6. Répétez les étapes 2 à 4 plusieurs fois. Chaque fois, utilisez des valeurs d’arguments différentes allant de `10` à `0.001`.

## <a name="view-run-history"></a>Afficher l’historique des exécutions
1. Accédez à l’affichage **Exécutions**, puis cliquez sur **iris_sklearn.py** dans la liste des exécutions. Le tableau de bord de l’historique des exécutions pour `iris_sklearn.py` s’ouvre. Il montre chaque exécution effectuée sur `iris_sklearn.py`. 

   ![img](media/quickstart-installation/run_view.png)

2. Le tableau de bord de l’historique des exécutions affiche également les principales métriques, un ensemble de graphes par défaut et une liste de métriques pour chaque exécution. Vous pouvez personnaliser cet affichage en triant, filtrant et ajustant les configurations en cliquant sur l’icône de configuration ou l’icône de filtre.

   ![img](media/quickstart-installation/run_dashboard.png)

3. Cliquez sur une exécution terminée et vous pouvez obtenir un affichage détaillé pour cette exécution spécifique, dont des métriques supplémentaires, les fichiers générés ainsi que d’autres journaux potentiellement utiles.

## <a name="next-steps"></a>Étapes suivantes
Vous avez maintenant créé un compte Expérimentation Azure Machine Learning et un compte Gestion des modèles Azure Machine Learning. Vous avez installé l’application de bureau Azure Machine Learning Workbench et une interface de ligne de commande. Vous avez créé un projet, créé un modèle en exécutant un script et exploré l’historique des exécutions du script.

Pour obtenir une expérience plus approfondie de ce flux de travail, notamment la façon de déployer votre modèle Iris comme un service web, suivez le didacticiel Classifying Iris (Classification d’iris) intégral qui contient des étapes détaillées pour la [préparation des données](tutorial-classifying-iris-part-1.md), l’[expérimentation](tutorial-classifying-iris-part-2.md) et la [gestion des modèles](tutorial-classifying-iris-part-3.md). 

> [!div class="nextstepaction"]
> [Didacticiel Classifying Iris](tutorial-classifying-iris-part-1.md) (Classification d’iris)

