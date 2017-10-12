---
title: "Article du didacticiel sur les fonctionnalités Azure Machine Learning (préversion) - Interface de ligne de commande | Microsoft Docs"
description: "Ce didacticiel décrit toutes les étapes nécessaires pour réaliser une classification Iris de bout en bout à partir de l’interface de ligne de commande."
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: c2a3b9702afd99c29b64133a05515a1b5f395130
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Didacticiel : Classification Iris à l’aide de l’interface de ligne de commande
Les services Azure Machine Learning (préversion) forment une solution d’analytique avancée et de science des données intégrée complète qui permet aux scientifiques des données professionnels de préparer des données, de développer des expérimentations et de déployer des modèles à l’échelle du cloud.

Dans ce didacticiel, vous allez apprendre à utiliser les outils de l’interface de ligne de commande (CLI) dans les fonctionnalités Azure Machine Learning en préversion pour : 
> [!div class="checklist"]
> * Configurer un compte d’expérimentation et créer un espace de travail
> * Création d’un projet
> * Soumettre une expérimentation à plusieurs cibles de calcul
> * Promouvoir et inscrire un modèle formé
> * Déployer un service web pour évaluer de nouvelles données

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Composants requis
- Vous avez besoin d’un accès à un abonnement Azure et des autorisations adéquates pour créer des ressources dans cet abonnement. 
- Vous devez installer l’application Azure Machine Learning Workbench en suivant la procédure indiquée dans [Guide de démarrage rapide sur l’installation et la création](quickstart-installation.md). 

  >[!NOTE]
  >Vous devez uniquement installer Azure Machine Learning Workbench localement. Il vous suffit de suivre les étapes décrites dans les sections intitulées Installer Azure Machine Learning Workbench, puisque vous allez effectuer les étapes de création du compte et de création d’un projet en utilisant la ligne de commande, comme indiqué dans cet article.
 
## <a name="getting-started"></a>Prise en main
L’interface de ligne de commande (CLI) Azure Machine Learning vous permet d’effectuer toutes les tâches nécessaires à un flux de travail de science des données complet. Vous pouvez accéder à des outils CLI des manières suivantes :

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Option 1. Lancer l’interface de ligne de commande Azure ML à partir de la boîte de dialogue de connexion Azure Machine Learning Workbench
Quand vous lancez Azure Machine Learning Workbench et vous connectez pour la première fois, l’écran suivant s’affiche si vous n’avez pas encore accès à un compte d’expérimentation :

![aucun compte trouvé](media/tutorial-iris-azure-cli/no_account_found.png)

Cliquez sur le lien **Fenêtre de ligne de commande** dans la boîte de dialogue pour ouvrir la fenêtre de ligne de commande.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Option 2. Lancer l’interface de ligne de commande Azure ML à partir de l’application Azure Machine Learning Workbench
Si vous avez déjà accès à un compte d’expérimentation, vous pouvez vous y connecter. Ouvrez ensuite la fenêtre de ligne de commande en cliquant sur le menu **Fichier** --> **Ouvrir l’invite de commandes**.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Option 3. Activer l’interface de ligne de commande Azure ML dans une fenêtre de ligne de commande de votre choix
Vous pouvez également activer l’interface de ligne de commande Azure ML dans n’importe quelle fenêtre de ligne de commande. Il vous suffit de lancer une fenêtre de commande et d’entrer les commandes suivantes :

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Pour rendre la modification permanente, vous pouvez utiliser `SETX` sur Windows. Pour macOS, vous pouvez utiliser `setenv`.

>[!TIP]
>Vous pouvez activer l’interface de ligne de commande Azure dans votre fenêtre de terminal préférée en définissant les variables d’environnement ci-dessus.

## <a name="step-1-log-in-to-azure"></a>Étape 1. Connexion à Azure
La première étape consiste à ouvrir l’interface de ligne de commande à partir de l’application AMLWorkbench (Fichier > Ouvrir l’invite de commandes). Ainsi, nous sommes sûrs d’utiliser l’environnement python approprié et de disposer des commandes de l’interface de ligne de commande (CLI) ML. 

Ensuite, nous devons définir le bon contexte dans votre CLI pour accéder à des ressources Azure et les gérer.
 
```bash
az login
az account set -s d128f140-94e6-1206-80a7-954b9d27d007
```

> [!TIP]
> Pour obtenir la liste de tous vos abonnements, exécutez : 
>```
>az account list -o table
>```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Étape 2. Créer un compte et un espace de travail Azure Machine Learning - Expérimentation
Nous commençons par créer un compte d’expérimentation et un espace de travail. Consultez [Concepts Azure Machine Learning](overview-general-concepts.md) pour plus d’informations sur les comptes d’expérimentation et les espaces de travail. 

> [!NOTE]
> Les comptes d’expérimentation nécessitent un compte de stockage, lequel est utilisé pour stocker les résultats de vos exécutions d’expérimentations. Le nom du compte de stockage doit être globalement unique dans Azure car une URL y est associée. Le nom de votre compte d’expérimentation sert à créer, en votre nom, un compte de stockage. Veillez à utiliser un nom unique, sans quoi vous obtenez une erreur comme _« Le compte de stockage portant le nom amlsampleexp est déjà pris. »_ Autrement, vous pouvez utiliser l’argument `--storage` pour utiliser un compte de stockage existant.

```bash
az group create --name amlsample --location eastus2
az ml account experimentation create --name amlsampleexp --resource-group amlsample
az ml account experimentation create --name amlsampleexp --resource-group amlsample --storage /subscriptions/6d48cffb-b787-47bd-8d20-1696afa33b67/resourceGroups/existing/providers/Microsoft.Storage/storageAccounts/mystorageacct
az ml workspace create --name amlsamplew --account amlsampleexp --resource-group amlsample
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Étape 2.a. (facultative) Partager un espace de travail avec un collaborateur
Ici, nous expliquons comment partager l’accès à un espace de travail avec un collègue. Les étapes permettant de partager l’accès à un compte d’expérimentation ou à un projet sont identiques. Seule la façon d’obtenir l’ID de ressource Azure a besoin d’être mise à jour.

```bash
az ml workspace show --name amlsamplew --account amlsampleexp --resource-group amlsample 
az role assignment create --assignee ahgyger@microsoft.com --role owner --scope "/subscriptions/d128f140-94e6-4175-87a7-954b9d27db16/resourceGroups/amlsample/providers/Microsoft.MachineLearningExperimentation/accounts/amlsampleexp/workspaces/amlsamplew"
```

> [!TIP]
> Vous devez utiliser la véritable adresse e-mail du collaborateur, et non un alias. 

## <a name="step-3-create-a-new-project"></a>Étape 3. Création d'un projet
Notre prochaine étape consiste à créer un projet. Il existe plusieurs façons de démarrer un nouveau projet.

### <a name="create-a-new-blank-project"></a>Créer un projet vide

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\
```

### <a name="create-a-new-project-with-template-files"></a>Créer un projet avec des fichiers de modèle
Les fichiers de modèle ne sont pas des exemples, mais ils donnent une structure à votre nouveau projet. Le projet est prérempli avec deux fichiers : `train.py` et `score.py`.

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Créer un projet associé à un dépôt Git cloud
Nous pouvons créer un projet associé à un dépôt Git cloud. Chaque fois qu’une expérimentation est envoyée, un instantané du contenu du projet est capturé à titre de validation Git dans votre branche d’historique des exécutions. Pour plus d’informations, consultez [Utilisation d’un dépôt Git avec un projet Azure Machine Learning Workbench](using-git-ml-project.md).

> [!NOTE]
> Azure Machine Learning prend uniquement en charge un projet d’équipe vide (VSTS) avec Git comme gestion des versions.

> [!TIP]
> Si vous obtenez une erreur qui stipule que l’URL du dépôt n’est peut-être pas valide ou que l’utilisateur n’a peut-être pas accès, vous pouvez créer un jeton de sécurité dans VSTS (Sécurité, ajouter des jetons d’accès personnels) et utiliser l’argument __vststoken__ lors de la création de votre projet. 

```bash
az ml project create --name 9_25_2 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --vststoken m2fholwwrcn7u4nrdqfxx007u5rztjfhgofnemvuvtue6pbwo3sa
```

### <a name="sample_create"></a>Créer un projet à partir d’un exemple en ligne
Dans cet exemple, nous utilisons un modèle provenant d’un projet Git Hub et nous l’utilisons lors de la création de notre projet. 

```bash
# List the project samples
az ml project sample list

# Create a new project from a sample
az ml project create --name 9_25_3 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```

Une fois votre projet créé, modifiez votre répertoire avant de passer à l’étape suivante.

```bash
cd c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\9_25_1
```

## <a name="step-4-run-the-training-experiment"></a>Étape 4. Exécuter l’expérimentation de formation 
Les étapes ci-dessous partent du principe que vous avez un projet qui utilise l’exemple Iris (consultez [Créer un projet à partir d’un exemple en ligne](#sample_create)).

### <a name="prepare-your-environment"></a>Préparation de votre environnement 
Pour l’exemple Iris, nous devons installer matplotlib.

```bash
pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Soumettre l’expérimentation

```bash
# Execute the file
az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Itérer au sein de votre expérimentation avec des taux de régularisation décroissants
En étant quelque peu créatif, il est facile de former un script python qui envoie des expérimentations avec des taux de régularisation différents. (Vous devrez peut-être modifier le fichier pour pointer vers le chemin de projet approprié.)

```bash
python run.py
```

## <a name="step-5-view-run-history"></a>Étape 5. Afficher l’historique des exécutions
La commande suivante liste toutes les exécutions précédentes qui ont eu lieu. 

```bash
az ml history list -o table
```
L’exécution de la commande ci-dessus affiche la liste de toutes les exécutions qui appartiennent à ce projet. Vous pouvez voir que les mesures des taux de précision et de régularisation sont également listées. Ainsi, vous identifiez facilement la meilleure exécution dans la liste.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Étape 5.a. Afficher la pièce jointe créée par une exécution donnée 
Pour afficher la pièce jointe associée à une exécution donnée, nous pouvons utiliser la commande info de l’historique des exécutions.

```bash
az ml history info --run 9_16_4_1505589545267 --artifact driver_log
```

Pour télécharger les artefacts d’une exécution, vous pouvez utiliser la commande ci-dessous :

```bash
# Stream a given attachment 
az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Étape 6. Promouvoir les artefacts d’une exécution 
L’une de nos exécutions présente un meilleur AUC, nous voulons donc l’utiliser pour créer un service web d’évaluation à déployer en production. Pour cela, nous devons d’abord promouvoir les artefacts dans une ressource.

```bash
az ml history promote --run 9_25_1505346632975 --artifact-path outputs/model.pkl --name model.pkl
```

Cette opération crée un dossier __assets__ dans le répertoire de votre projet qui contient pickle.link. Ce fichier link est utilisé pour suivre la version du fichier promu.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Étape 7. Télécharger les fichiers à opérationnaliser
Nous devons maintenant télécharger les fichiers promus et les utiliser pour créer notre service web de prédiction. 

```bash
az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>Étape 8 : Configurer votre environnement de gestion des modèles 
Nous créons un environnement pour déployer des services web. Nous pouvons exécuter le service web sur l’ordinateur local à l’aide de Docker. Ou bien, déployez-le sur un cluster ACS pour les opérations à grande échelle. 

```bash
# Create new environment
az ml env setup -l eastus2 -n amlsamplesenv
# Once setup is complete, set your environment for current context
az ml env set -g amlsamplesenvrg -n amlsamplesenv
```

## <a name="step-9-create-a-model-management-account"></a>Étape 9. Créer un compte de gestion des modèles 
Un compte de gestion des modèles est exigé pour déployer et suivre vos modèles en production. 

```bash
az ml account modelmanagement create -n amlsamplesacct -g amlsamplesenvrg -l eastus2
```

## <a name="step-10-create-a-web-service"></a>Étape 10. Création d’un service web
Ensuite, nous créons un service web qui retourne une prédiction en utilisant le modèle que nous avons déployé. 

```bash
az ml service create realtime -m modelfilename -f score.py -r python –n amlsamplews
```

## <a name="step-10-run-the-web-service"></a>Étape 10. Exécuter le service web
À l’aide de l’ID de service web issu de la sortie de l’étape précédente, nous pouvons appeler le service web et le tester. 

```
# Get web service usage infomration
az ml service usage realtime -i <web service id>

# Call the web service with the run command:
az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>Suppression de toutes les ressources 
Terminons ce didacticiel en supprimant toutes les ressources que nous avons créées, sauf si vous souhaitez continuer à les utiliser. 

Pour cela, nous supprimons simplement le groupe de ressources qui contient toutes nos ressources. 

```bash
az group delete --name amlsample
az group delete --name amlsamplesenvrg
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à utiliser les fonctionnalités Azure Machine Learning en préversion pour : 
> [!div class="checklist"]
> * Configurer un compte d’expérimentation, puis créer un espace de travail
> * Créer des projets
> * Envoyer des expérimentations à plusieurs cibles de calcul
> * Promouvoir et inscrire un modèle formé
> * Créer un compte de gestion des modèles pour la gestion des modèles
> * Créer un environnement pour déployer un service web
> * Déployer un service web et l’évaluer avec de nouvelles données

