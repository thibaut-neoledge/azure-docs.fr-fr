---
title: "Itinérance et collaboration dans Azure Machine Learning Workbench  | Microsoft Docs"
description: "Liste des problèmes connus et guide de dépannage"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 156dd1b7f928df22b3feb9e7a13396d3b53a91d7
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Itinérance et collaboration dans Azure Machine Learning Workbench
Ce document vous explique en détail comment Azure Machine Learning Workbench facilite l’itinérance de vos projets entre différents ordinateurs ainsi que la collaboration avec vos coéquipiers. 

Lorsque vous créez un projet Azure Machine Learning avec un lien vers un référentiel Git distant, les instantanés et les métadonnées du projet sont stockés dans le cloud. Le lien cloud vous permet d’accéder au projet depuis un autre ordinateur (itinérance). Vous pouvez également accorder un accès à vos collègues, ce qui permet la collaboration. 

## <a name="prerequisites"></a>Composants requis
Installez d’abord Azure Machine Learning Workbench avec un accès à un compte Experimentation. Consultez le [guide d’installation](quickstart-installation.md) pour plus de détails.

Accédez ensuite à [Visual Studio Team System](https://www.visualstudio.com) et créez un référentiel auquel lier votre projet. Pour obtenir des informations détaillées sur Git, consultez l’article [Utilisation d’un référentiel Git avec un projet Azure Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-azure-machine-learning-project"></a>Créer un projet Azure Machine Learning
Lancez Azure Machine Learning Workbench et créez un projet (par exemple, _iris_). Saisissez l’URL du référentiel Git VSTS dans la zone de texte **URL du référentiel GIT Visualstudio.com**. 
>[!IMPORTANT]
>La création du projet échoue si vous n’avez pas accès en lecture/écriture au référentiel Git ou si le référentiel Git n’est pas vide, autrement dit, s’il contient déjà une branche principale.

Une fois le projet créé, exécutez quelques scripts au sein du projet. Cette action valide l’état du projet dans la branche de l’historique des exécutions du référentiel Git distant. 

Si vous avez configuré l’authentification Git, vous pouvez également explicitement travailler dans la branche principale ou créer une branche. 

Par exemple : 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Itinérance
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Ouvrir Azure Machine Learning Workbench sur un second ordinateur
Une fois le référentiel Git VSTS lié à votre projet, vous pouvez accéder au projet _iris_ depuis n’importe quel ordinateur sur lequel vous avez installé Azure Machine Learning Workbench. 

Pour accéder au projet iris sur un autre ordinateur, vous devez vous connecter à l’application avec les mêmes informations d’identification utilisées lors de la création du projet. En outre, vous devez accéder au même compte Experimentation et au même espace de travail. Le projet _iris_ est répertorié par ordre alphabétique avec les autres projets dans l’espace de travail. 

### <a name="download-project-on-second-machine"></a>Télécharger le projet sur le deuxième ordinateur
Lorsque l’espace de travail est ouvert sur le deuxième ordinateur, l’icône située en regard du projet _iris_ diffère de l’icône de dossier par défaut. L’icône de téléchargement indique que le contenu du projet se trouve dans le cloud et doit être téléchargé sur l’ordinateur actuel. 

![créer un projet](./media/roaming-and-collaboration/downloadable-project.png)

Lorsque vous cliquez sur le projet _iris_, vous lancez un téléchargement. Après quelques instants, une fois le téléchargement terminé, le projet est accessible sur le deuxième ordinateur. 

Emplacement sous Windows : `C:\Users\<username>\Documents\AzureML`

Emplacement sous macOS : `/home/<username>/Documents/AzureML`

Dans une prochaine version, nous prévoyons d’améliorer les fonctionnalités pour vous permettre de sélectionner un dossier de destination. 

>Notez que si un dossier dans le répertoire Azure ML a exactement le même nom que le projet, le téléchargement échoue. À l’heure actuelle, vous devez renommer le dossier existant afin de contourner ce problème.


### <a name="work-on-the-downloaded-project"></a>Travailler sur le projet téléchargé 
Le projet qui vient d’être téléchargé reflète l’état du projet depuis la dernière exécution du projet. Un instantané de l’état du projet est automatiquement validé sur la branche de l’historique des exécutions dans le référentiel Git VSTS à chaque exécution du projet. Nous utilisons l’instantané associé à la dernière exécution lors de l’instanciation du projet sur le deuxième ordinateur. 
 

## <a name="collaboration"></a>Collaboration
Vous pouvez collaborer avec vos coéquipiers sur des projets liés à un référentiel Git VSTS. Vous pouvez affecter des autorisations aux utilisateurs sur le compte Experimentation, l’espace de travail et le projet. À ce stade, vous pouvez exécuter les commandes Azure Resource Manager à l’aide de l’interface de ligne de commande Azure. Vous pouvez également utiliser le [portail Azure](https://portal.azure.com). Voir la [section suivante](#portal).    

### <a name="using-command-line-to-add-users"></a>Utilisation de la ligne de commande pour ajouter des utilisateurs
Prenons un exemple. Par exemple, Alice est la propriétaire du projet Iris et souhaite en partager l’accès avec Bob. 

Alice clique sur le menu **Fichier** puis sélectionne l’option **Invite de commande** pour ouvrir l’invite de commande configurée pour le projet _iris_. Alice peut alors choisir le niveau accès à accorder à Bob en exécutant les commandes suivantes.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Reader.
# Bob now has read access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Reader --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as a Contributor.
# Bob now has read/write access to all projects under the Workspace by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <workspace ARM ID>

# find ARM ID of the project 
az ml project show --query "id"

# Add Bob to the Project as an Owner.
# Bob now has read/write access to the Project, and can add others too.
az role assignment create --assignee bob@contoso.com --role Owner --scope <project ARM ID>
```

Après l’attribution du rôle, directement ou par héritage, Bob peut voir le projet dans la liste des projets Workbench. L’application peut nécessiter un redémarrage pour afficher le projet. Bob peut ensuite télécharger le projet comme décrit dans la section [Itinérance](#roaming) et collaborer avec Alice. 

L’historique des exécutions de tous les utilisateurs qui collaborent sur un projet est validé dans le même référentiel Git distant. Par conséquent, quand Alice exécute le projet, Bob peut voir cette exécution dans la section de l’historique des exécutions du projet dans l’application Workbench. Bob peut également restaurer le projet à l’état de n’importe quelle exécution, y compris celles lancées par Alice. 

Le partage d’un référentiel Git distant pour le projet permet également à Alice et Bob de collaborer sur la branche principale. Si nécessaire, ils peuvent aussi créer des branches personnelles et utiliser des demandes de tirage (pull requests) et des fusions Git pour collaborer. 

### <a name="using-azure-portal-to-add-users"></a>Utilisation du portail Azure pour ajouter des utilisateurs
<a name="portal"></a>

Les comptes, espaces de travail et projets Azure Machine Learning Experimentation sont des ressources Azure Resource Manager. Vous pouvez utiliser le lien de contrôle d’accès du [portail Azure](https://portal.azure.com) pour attribuer des rôles. 

Trouvez la ressource à ajouter aux utilisateurs à partir de la vue Toutes les ressources. Cliquez sur le lien de contrôle d’accès (IAM) dans la page. Ajouter des utilisateurs 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">


