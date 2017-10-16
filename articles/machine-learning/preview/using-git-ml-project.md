---
title: "Utilisation d’un référentiel Git avec un projet Azure Machine Learning Workbench | Microsoft Docs"
description: "Cet article explique comment utiliser un référentiel Git conjointement avec un projet Azure Machine Learning Workbench."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: hning86
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 59b07c9834904e01256b75344ba2e6892e56438c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Utilisation d’un référentiel Git avec un projet Azure Machine Learning Workbench
Ce document fournit des informations sur la façon dont Azure Machine Learning Workbench utilise Git pour garantir la reproductibilité dans votre expérience de science des données. Des instructions sur la façon d’associer votre projet à un référentiel Git cloud sont également fournies.

## <a name="introduction"></a>Introduction
Azure Machine Learning Workbench a été conçu d’emblée pour l’intégration de Git. Lorsque vous créez un projet, le dossier du projet est automatiquement « initialisé dans Git » comme un référentiel Git local tandis qu’un second référentiel Git local masqué est également créé avec une branche nommée _AzureMLHistory/<project_GUID>_ pour effectuer le suivi des modifications apportées au dossier du projet à chaque exécution. 

L’association du projet Azure ML avec un référentiel Git, hébergé dans un projet Visual Studio Team Services (VSTS), permet une gestion de version automatique à la fois localement et à distance. Cette association permet à toute personne ayant accès au référentiel distant de télécharger le dernier code source vers un autre ordinateur (itinérance).  

> [!NOTE]
> VSTS possède sa propre liste de contrôle d’accès, qui est indépendante du service Azure Machine Learning Experimentation. L’accès de l’utilisateur peut varier entre un référentiel Git et un espace de travail ou un projet Azure ML, et devra éventuellement être géré. Si vous souhaitez partager votre projet Azure ML avec un membre de l’équipe, y compris l’accès au niveau du code, en plus de partager uniquement l’espace de travail, vous devez lui accorder explicitement des droits d’accès au référentiel Git VSTS. 

Avec Git, il est également possible de gérer la version explicitement à l’aide de la branche _principale_ ou en en créant d’autres branches sur le référentiel. Vous pouvez simplement utiliser le référentiel Git local vous tourner vers le référentiel Git distant (si configuré).

Ce diagramme illustre la relation entre un référentiel Git VSTS et un projet Azure ML :

![AML Git](media/using-git-ml-project/aml_git.png)

Pour commencer à utiliser un référentiel Git distant, suivez ces instructions de base.

> [!NOTE]
> Actuellement, Azure Machine Learning prend uniquement en charge les référentiels Git de comptes VSTS. Une prise en charge des référentiels Git généraux (par exemple, GitHub) est prévue prochainement.

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>Étape 1. Créer un compte Azure ML Experimentation
Si ce n’est pas déjà fait, créez un compte Azure ML Experimentation et installez l’application Azure ML Workbench. Pour plus de détails, consultez le document [Guide de démarrage rapide d’installation et de création](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Étape 2. Créer un projet d’équipe ou utiliser un projet d’équipe existant
À partir du [portail Azure](https://portal.azure.com/), créez un **projet d’équipe**.
1. Cliquez sur **+**
2. Recherchez **« Projet d’équipe »**
3. Entrez les informations requises.
    - Nom : un nom d’équipe.
    - Gestion de version : **Git**
    - Abonnement : un abonnement incluant un compte Azure Machine Learning Experimentation.
    - Emplacement : idéalement, restez dans une région proche de vos ressources Azure Machine Learning Experimentation.
4. Cliquez sur **Créer**. 

![Créer un projet d’équipe à partir du portail Azure](media/using-git-ml-project/create_vsts_team.png)


> [!TIP]
> Veillez à vous connecter avec le compte Azure Active Directory (AAD) utiliser pour accéder à Azure Machine Learning Workbench. Sinon, le nouveau projet d’équipe risque d’être associé à un ID de client incorrect et Azure Machine Learning ne pourra pas le retrouver. Dans ce cas, vous devrez utiliser l’interface de ligne de commande et fournir le jeton VSTS.

Une fois le projet d’équipe créé, vous êtes prêt à passer à l’étape suivante.

Pour accéder directement au projet d’équipe que vous venez de créer, l’URL est `https://<team_project_name>.visualstudio.com`.

> [!NOTE]
> Actuellement, Azure Machine Learning prend uniquement en charge les référentiels Git vides sans aucune branche principale. Depuis l’interface de ligne de commande, vous pouvez utiliser l’argument force pour supprimer d’abord votre branche principale. 

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Étape 3. Créer un projet Azure ML avec un référentiel Git distant
Lancez Azure ML Workbench et créez un projet. Entrez dans la zone de texte du référentiel Git l’URL du référentiel Git VSTS que vous obtenez à l’étape 2. Elle se présente généralement sous la forme : http://<nom_compte_vsts>.visualstudio.com/_git/<nom_projet>

![Créer un projet Azure ML avec le référentiel Git](media/using-git-ml-project/create_project_with_git_rep.png)

Un nouveau projet Azure ML est maintenant créé avec l’intégration du référentiel Git distant activée et fonctionnelle. Le dossier du projet est toujours initialisé par Git comme un référentiel Git local. Et comme le Git _distant_ est défini sur le référentiel Git VSTS distant, les validations peuvent être transmises au référentiel Git distant.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Étape 3. Associer un projet Azure ML existant avec un référentiel Git VSTS
Vous pouvez également créer un projet Azure ML sans référentiel Git VSTS et vous appuyer uniquement sur le référentiel Git local pour obtenir des instantanés de l’historique des exécutions. Et vous pouvez associer un référentiel Git VSTS ultérieurement à ce projet Azure ML existant à l’aide de la commande suivante :

```azurecli
# make sure you are in the project path so CLI has context of your current project
az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name
```

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Étape 4. Capturer un instantané de projet dans le référentiel Git
Vous pouvez maintenant exécuter plusieurs fois le projet et y apporter des modifications entre ces exécutions. Vous pouvez le faire à partir de l’application de bureau ou à l’aide de la commande `az ml experiment submit` dans l’interface CLI. Pour plus d’informations, vous pouvez suivre le [Didacticiel Classifying Iris (Classification d’Iris)](tutorial-classifying-iris-part-1.md). Pour chaque exécution, si des modifications sont effectuées dans des fichiers du dossier du projet, un instantané de tout le dossier du projet est validé et placé dans le référentiel Git distant. Vous pouvez afficher les branches et les validations en accédant à l’URL de référentiel Git VSTS.

![branche de l’historique d’exécution](media/using-git-ml-project/run_history_branch.png)

## <a name="step-5-restore-a-previous-project-snapshot"></a>Étape 5. Restaurer un instantané de projet précédent 
Pour restaurer le dossier complet du projet à l’état d’un instantané précédent de l’historique des exécutions, à partir d’AML Workbench.
1. Cliquez sur **Exécutions** dans la barre d’activité (icône sablier).
2. Dans la vue **Liste d’exécution**, cliquez sur l’exécution que vous souhaitez restaurer.
3. Dans la vue **Détails de l’exécution**, cliquez sur **Restaurer**.

![branche de l’historique d’exécution](media/using-git-ml-project/restore_project.png)

Vous pouvez également utiliser la commande suivante à partir de la fenêtre de l’interface CLI Azure ML Workbench.

```azurecli
# discover the run I want to restore snapshot from:
az ml history list -o table

# restore the snapshot from a particular run
az ml project restore --run-id <run_id>
```

En exécutant cette commande, nous remplaçons le dossier complet du projet par l’instantané pris lors du lancement de cette exécution spécifique. Cela signifie que vous allez **perdre toutes les modifications** apportées dans votre dossier de projet actuel. Par conséquent, soyez très prudent lorsque vous exécutez cette commande.

## <a name="step-6-use-the-master-branch"></a>Étape 6. Utiliser la branche principale
Pour éviter de perdre accidentellement l’état de votre projet actuel, validez le projet dans la branche principale du référentiel Git. Vous pouvez directement utiliser Git à partir de la ligne de commande (ou de votre outil client Git préféré) pour le faire fonctionner sur la branche principale. Par exemple :

```
# make sure you are on the master branch
git checkout master

# stage all changes
git add -A

# commit all changes locally on the master branch
git commit -m 'this is my updates so far'

# push changes into the remote VSTS Git repo master branch.
git push origin master
```

Vous pouvez maintenant restaurer sans risque le projet vers un instantané antérieur à partir de l’étape 5, en sachant que vous pouvez toujours revenir à la validation que vous venez d’effectuer sur la branche principale.

## <a name="authentication"></a>Authentification
Si vous utilisez uniquement des fonctions d’historique des exécutions dans Azure ML pour prendre des instantanés du projet et les restaurer, vous n’avez pas à vous soucier de l’authentification du référentiel Git. La couche Service d’expérimentation s’en occupe.

Toutefois, si vous utilisez vos propres outils Git pour gérer la version, vous devrez gérer correctement l’authentification pour le référentiel Git distant sur VSTS. Autrement dit, vous devez configurer l’authentification avec le référentiel Git sur l’ordinateur local avant de pouvoir envoyer des commandes Git pour ce référentiel Git distant. 

Pour cela, le plus simple consiste à créer une paire de clés SSH et à charger la partie de la clé publique dans les paramètres de sécurité du référentiel Git.

### <a name="generate-ssh-key"></a>Génération de la clé SSH 
Nous allons d’abord générer une paire de clés SSH sur votre ordinateur.

#### <a name="on-windows"></a>Sous Windows :
Lancez l’application de bureau de l’interface graphique utilisateur Git sous Windows puis dans le menu _Aide_, cliquez sur _Afficher la clé SSH_.

![Clé SSH](media/using-git-ml-project/git_gui.png)

Copiez SSH dans le Presse-papiers.

#### <a name="on-macos"></a>Sur macOS :
Procédure rapide à partir de l’interpréteur de commandes :
```
# generate the SSH key
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# start the SSH agent in the background
$ eval "$(ssh-agent -s)"

# add newly generated SSH key to the SSH agent
$ ssh-add -K ~/.ssh/id_rsa

# display the public key so you can copy it.
$ more ~/.ssh/id_rsa.pub
```
Vous trouverez davantage de détails dans [cet article GitHub](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).

### <a name="upload-public-key-to-git-repo"></a>Charger la clé publique dans le référentiel Git
Accédez à la page d’accueil de votre compte Visual Studio : https://<vsts_account_name>.visualstudio.com, connectez-vous, puis cliquez sur Sécurité sous votre avatar.

Ajoutez ensuite une clé publique SSH, collez la clé publique SSH que vous avez obtenue à l’étape précédente puis nommez-la. Vous pouvez ici ajouter plusieurs clés.

Vous pouvez maintenant émettre des commandes Git localement sur le référentiel de suppression sans qu’aucune autre authentification explicite ne soit requise.

### <a name="read-more"></a>En savoir plus
Suivez ces deux articles (les deux approches fonctionnent) pour plus d’informations sur l’activation de l’authentification locale pour le référentiel Git distant dans VSTS.
- [Utiliser l’authentification par clé SSH](https://www.visualstudio.com/en-us/docs/git/use-ssh-keys-to-authenticate)
- [Utiliser les gestionnaires d’informations d’identification Git](https://www.visualstudio.com/en-us/docs/git/set-up-credential-managers)


## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment utiliser le processus Team Data Science Process (TDSP) afin d’organiser votre structure de projet, consultez [Structurer un projet avec TDSP](how-to-use-tdsp-in-azure-ml.md)
