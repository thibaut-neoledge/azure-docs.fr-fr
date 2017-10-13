---
title: "Intégration continue/déploiement continu de Jenkins vers des machines virtuelles Azure avec Team Services | Microsoft Docs"
description: "Configurer l’intégration continue (CI) et le déploiement continu (CD) d’une application Node.js à l’aide de Jenkins vers des machines virtuelles Azure à partir de Release Management dans Visual Studio Team Services (VSTS) ou Microsoft Team Foundation Server (TFS)"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/15/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: feaced0d0784b5724fb1e30be5e66cb7c808d77f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-team-services"></a>Déployer votre application sur des machines virtuelles Linux à l’aide de Jenkins et Team Services

L’intégration continue (CI) et le déploiement continu (CD) constituent un pipeline via lequel vous pouvez générer, mettre en production et déployer votre code. Team Services fournit un ensemble complet d’outils d’automatisation CI/CD pour le déploiement sur Azure. Jenkins est un outil serveur CI/CD tiers populaire qui propose également l’automatisation CI/CD. Vous pouvez les utiliser ensemble pour personnaliser la façon dont vous proposez votre service ou application cloud.

Dans ce didacticiel, vous utilisez Jenkins pour générer une **application web Node.js** et Visual Studio Team Services pour la déployer sur un [groupe de déploiement](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) contenant des machines virtuelles Linux.

Vous allez :

> [!div class="checklist"]
> * Générer votre application dans Jenkins
> * Configurer Jenkins pour l’intégration Team Services
> * Créer un groupe de déploiement pour les machines virtuelles Azure
> * Créer une définition de mise en production qui configure les machines virtuelles et déploie l’application

## <a name="before-you-begin"></a>Avant de commencer

* Vous devez avoir accès à un compte de Jenkins. Si vous n’avez pas encore créé de serveur Jenkins, consultez la [documentation Jenkins](https://jenkins.io/doc/). 

* Connectez-vous à votre compte Team Services (`https://{youraccount}.visualstudio.com`). 
  Vous pouvez obtenir un [compte Team Services gratuit](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Pour plus d’informations, consultez [Connect to Visual Studio Team Services from Eclipse, Xcode, Visual Studio, and more](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) (Se connecter à Visual Studio Team Services depuis Eclipse, Xcode, Visual Studio, etc.).

* Créez un jeton d’accès personnel (PAT) dans votre compte Team Services si vous n’en avez pas encore. Jenkins requiert ces informations pour accéder à votre compte Team Services.
  Consultez [Authenticate access with personal access tokens for Team Services and TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) (Authentifier l’accès avec des jetons d’accès personnel pour Team Services et TFS) pour savoir comment en générer un.

## <a name="get-the-sample-app"></a>Obtenir l’exemple d’application

Vous devez déployer une application stockée dans un référentiel Git.
Pour ce didacticiel, nous vous recommandons d’utiliser [cet exemple d’application disponible sur GitHub](https://github.com/azooinmyluggage/fabrikam-node).

1. Dupliquez cette application et notez son emplacement (URL) pour pouvoir l’utiliser ultérieurement dans ce didacticiel.

1. La duplication de l’application doit être **publique** pour simplifier la connexion à GitHub plus tard.

> [!NOTE]
> Pour plus d’informations, consultez [Fork A Repo](https://help.github.com/articles/fork-a-repo/) (Dupliquer un référentiel)et [Making a private repository public](https://help.github.com/articles/making-a-private-repository-public/) (Rendre public un référentiel privé).

> [!NOTE]
> L’application a été générée à l’aide de [Yeoman](http://yeoman.io/learning/index.html) ; elle utilise **Express**, **bower** et **grunt** et comprend certains packages **npm** en tant que dépendances.
> L’exemple d’application contient un ensemble de [modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) qui sont utilisés pour créer dynamiquement les machines virtuelles pour le déploiement sur Azure. Ces modèles sont utilisés par des tâches de la [définition de mise en production Team Services](https://www.visualstudio.com/docs/build/actions/work-with-release-definitions).
> Le modèle principal crée un groupe de sécurité réseau, une machine virtuelle et un réseau virtuel. Il attribue une adresse IP publique et ouvre le port 80 entrant. Il ajoute également une balise qui est utilisée par le groupe de déploiement pour sélectionner les machines devant recevoir le déploiement.
>
> L’exemple contient également un script qui configure Nginx et déploie l’application. Il est exécuté sur chacune des machines virtuelles. Plus précisément, le script installe Node, Nginx et PM2. Il configure Nginx et PM2, puis démarre l’application Node.

## <a name="configure-jenkins-plugins"></a>Configurer les plug-ins Jenkins

Tout d’abord, vous devez configurer deux plug-ins Jenkins pour **NodeJS** et **l’intégration avec Team Services**.

1. Ouvrez votre compte Jenkins et choisissez **Manage Jenkins** (Gérer Jenkins).

1. Sur la page **Manage Jenkins** (Gérer Jenkins), choisissez **Manage Plugins** (Gérer les plug-ins).

1. Filtrez la liste pour localiser le plug-in **NodeJS** et l’installer sans redémarrage.

   ![Ajout du plug-in NodeJS à Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)

1. Filtrez la liste pour rechercher le plug-in **Team Foundation Server** et installez-le. (Ce plug-in fonctionne pour Team Services et Team Foundation Server.) Le redémarrage de Jenkins n’est pas nécessaire.

## <a name="configure-jenkins-build-for-nodejs"></a>Configurer la génération Jenkins pour Node.js

Dans Jenkins, créez un projet de génération et configurez-le comme suit :

1. Dans l’onglet **Général**, entrez un nom pour votre projet de génération.

1. Dans l’onglet **Gestion du code source**, sélectionnez **Git** et entrez les détails du référentiel et la branche qui contient le code de votre application.

   ![Ajouter un référentiel à votre génération](media/tutorial-build-deploy-jenkins/jenkins-git.png)

   > [!NOTE]
   > Si votre référentiel n’est pas public, choisissez **Ajouter** et fournissez des informations d’identification pour la connexion.

1. Dans l’onglet **Build Triggers** (Générer des déclencheurs), sélectionnez **Poll SCM** (Interroger SCM) et entrez la planification `H/03 * * * *` pour interroger le référentiel Git pour les modifications toutes les trois minutes. 

1. Dans l’onglet **Build Environment** (Générer un environnement), sélectionnez **Provide Node &amp; npm bin/ folder PATH** (Fournir le CHEMIN D’ACCÈS au dossier npm bin/ et à Node) et saisissez `NodeJS` pour la valeur d’installation de Node JS. Laissez **npmrc file** (fichier npmrc) défini sur « utiliser les valeurs par défaut du système ».

1. Dans l’onglet **Générer**, entrez la commande `npm install` pour vous assurer que toutes les dépendances sont mises à jour.

## <a name="configure-jenkins-for-team-services-integration"></a>Configurer Jenkins pour l’intégration Team Services

1. Dans l’onglet **Post-build Actions** (Actions post-génération), pour **Files to archive** (Fichiers à archiver), entrez `**/*` pour inclure tous les fichiers.

1. Pour **Trigger release in TFS/Team Services** (Déclencher la mise en production dans TFS/Team Services), entrez l’URL complète de votre compte (tel que `https://your-account-name.visualstudio.com`), le nom du projet, un nom pour la définition de mise en production (créée ultérieurement) et les informations d’identification pour vous connecter à votre compte.
   Vous avez besoin de votre nom d’utilisateur et du jeton d’accès personnel créé précédemment. 

   ![Configuration des actions post-génération Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)

1. Enregistrez le projet de génération.

## <a name="create-a-jenkins-service-endpoint"></a>Créer un point de terminaison de service Jenkins

Un point de terminaison de service permet à Team Services de se connecter à Jenkins.

1. Ouvrez la page **Services** dans Team Services, ouvrez la liste **Nouveau point de terminaison de service** et choisissez **Jenkins**.

   ![Ajouter un point de terminaison Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)

1. Entrez un nom à utiliser pour faire référence à cette connexion.

1. Entrez l’URL de votre serveur Jenkins et cochez l’option **Accepter les certificats SSL non fiables**.

1. Entrez le nom d’utilisateur et le mot de passe de votre compte Jenkins.

1. Choisissez **Vérifier la connexion** pour vérifier que les informations sont correctes.

1. Sélectionnez **OK** pour créer le point de terminaison de service.

## <a name="create-a-deployment-group"></a>Créer un groupe de déploiement

Vous avez besoin d’un [groupe de déploiement](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) pour héberger les machines virtuelles.

1. Ouvrez l’onglet **Mises en production** du hub **Build et mise en production**, puis ouvrez l’onglet **Groupes de déploiement**, puis choisissez **+ Nouveau**.

1. Entrez un nom pour le groupe de déploiement, et éventuellement une description.
   Sélectionnez ensuite **Créer**.

La tâche Déploiement de groupe de ressources Azure crée et inscrit les machines virtuelles lorsqu’elle s’exécute à l’aide du modèle Azure Resource Manager.
Vous n’avez pas besoin de créer, ni d’inscrire les machines virtuelles vous-même.

## <a name="create-a-release-definition"></a>Création d’une définition de version

Une définition de mise en production spécifie le processus que Team Services exécutera pour déployer l’application.
Pour créer la définition de mise en production dans Team Services :

1. Ouvrez l’onglet **Mises en production** du hub **Build et mise en production**, ouvrez le menu déroulant **+** dans la liste des définitions de mise en production et choisissez **Créer une définition de mise en production**. 

1. Choisissez le modèle **Vide** et sélectionnez **Suivant**.

1. Dans la section **Artefacts**, cliquez sur **Lier une source d’artefact** et choisissez **Jenkins**. Sélectionnez votre connexion de point de terminaison de service Jenkins. Sélectionnez ensuite le travail de source Jenkins, puis choisissez **Créer**. 

1. Dans la nouvelle définition de mise en production, choisissez **+ Ajouter des tâches** et ajoutez une tâche **Déploiement de groupe de ressources Azure** à l’environnement par défaut.

1. Choisissez la flèche déroulante en regard du lien **+ Ajouter des tâches** et ajoutez une phase de groupe de déploiement à la définition.

   ![Ajout d’une phase de groupe de déploiement](media/tutorial-build-deploy-jenkins/deployment-group-phase-in-release-definition.png) 

1. Dans le catalogue Tâche, ouvrez la section **Utilitaire** et ajoutez une instance de la tâche **Script Shell**.

1. Le modèle de paramètres utilisé dans la tâche Déploiement de groupe de ressources Azure définit le mot de passe administrateur servant à se connecter aux machines virtuelles.
   Vous fournissez ce mot de passe avec la variable **$(adminpassword)** :
   
   - Ouvrez l’onglet **Variables**et, dans la section **Variables**, entrez le nom `adminpassword`.

   - Entrez le mot de passe d’administrateur.

   - Cliquez sur l’icône de cadenas en regard de la zone de texte de valeur pour protéger le mot de passe. 

## <a name="configure-the-azure-resource-group-deployment-task"></a>Configurer la tâche Déploiement de groupe de ressources Azure

La tâche **Déploiement de groupe de ressources Azure** est utilisée pour créer le groupe de déploiement. Configurez-le comme suit :

* **Abonnement Azure :** sélectionnez une connexion dans la liste sous **Connexions au service Azure disponibles**. 
  Si aucune connexion n’apparaît, choisissez **Gérer**, sélectionnez **Nouveau point de terminaison de service**, puis **Azure Resource Manager** et suivez les invites.
  Revenez à votre définition de mise en production, actualisez la liste **Abonnement AzureRM** et sélectionnez la connexion que vous avez créée.

* **Groupe de ressources** : entrez un nom pour le groupe de ressources que vous avez créé précédemment.

* **Emplacement** : sélectionnez une région pour le déploiement.

  ![Création d’un groupe de ressources](media/tutorial-build-deploy-jenkins/provision-web-server.png)

* **Emplacement du modèle** : `URL of the file`

* **Lien du modèle** : `{your-git-repo}/ARM-Templates/UbuntuWeb1.json`

* **Lien des paramètres de modèle** : `{your-git-repo}/ARM-Templates/UbuntuWeb1.parameters.json`

* **Remplacer les paramètres du modèle** : une liste des valeurs de remplacement, par exemple : `-location {location} -virtualMachineName {machine] -virtualMachineSize Standard_DS1_v2 -adminUsername {username} -virtualNetworkName fabrikam-node-rg-vnet -networkInterfaceName fabrikam-node-websvr1 -networkSecurityGroupName fabrikam-node-websvr1-nsg -adminPassword $(adminpassword) -diagnosticsStorageAccountName fabrikamnodewebsvr1 -diagnosticsStorageAccountId Microsoft.Storage/storageAccounts/fabrikamnodewebsvr1 -diagnosticsStorageAccountType Standard_LRS -addressPrefix 172.16.8.0/24 -subnetName default -subnetPrefix 172.16.8.0/24 -publicIpAddressName fabrikam-node-websvr1-ip -publicIpAddressType Dynamic`.<br />Insérez vos propres valeurs spécifiques pour les {espaces réservés}. 

* **Activer les prérequis** : `Configure with Deployment Group agent`

* **Point de terminaison TFS/VSTS** : choisissez **Ajouter** et, dans la boîte de dialogue « Add new Team Foundation Server/Team Services Connection » (Ajouter une connexion Team Foundation Server/Team Services), sélectionnez **Authentification basée sur un jeton**. Entrez un nom pour la connexion et l’URL de votre projet d’équipe. Ensuite, générez et saisissez un [jeton d’accès personnel (PAT)]( https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) pour authentifier la connexion à votre projet d’équipe.

  ![Créer un jeton d’accès personnel](media/tutorial-build-deploy-jenkins/create-a-pat.png)

* **Projet d’équipe** : sélectionnez votre projet actuel.

* **Groupe de déploiement** : entrez le même nom de groupe de déploiement que celui que vous avez utilisé pour le paramètre **Groupe de ressources**.

Les paramètres par défaut de la tâche Déploiement de groupe de ressources Azure servent à créer ou mettre à jour une ressource de façon incrémentielle. La tâche crée les machines virtuelles la première fois qu’elle est exécutée, puis les met simplement à jour.

## <a name="configure-the-shell-script-task"></a>Configurer la tâche Shell Script

La tâche **Script Shell** est utilisée pour fournir la configuration d’un script à exécuter sur chaque serveur, afin d’installer Node.js et de démarrer l’application. Configurez-le comme suit :

* **Chemin d’accès au script** : `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`

* **Spécifier le répertoire de travail** : `Checked`

* **Répertoire de travail** : `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
   
## <a name="rename-and-save-the-release-definition"></a>Renommer et enregistrer la définition de mise en production

1. Modifiez le nom de la définition de mise en production pour le remplacer par le nom que vous avez spécifié dans l’onglet **Actions post-génération** de la build dans Jenkins. Jenkins exige que ce nom soit en mesure de déclencher une nouvelle mise en production lorsque les artefacts source sont mis à jour.

1. Si vous le souhaitez, modifiez le nom de l’environnement en cliquant sur dessus. 

1. Choisissez **Enregistrer**, puis **OK**.

## <a name="start-a-manual-deployment"></a>Démarrer un déploiement manuel

1. Choisissez **+ Mise en production** et sélectionnez **Créer une mise en production**.

1. Sélectionnez la génération que vous avez effectuée dans la liste déroulante en surbrillance et choisissez **Créer**.

1. Dans le message qui s’affiche, sélectionnez le lien de la mise en production. Par exemple : « Mise en production **Mise en production-1** a été créé. ».

1. Ouvrez l’onglet **Journaux** pour surveiller la sortie de console de mise en production.

1. Dans votre navigateur, ouvrez l’URL de l’un des serveurs que vous avez ajoutés à votre groupe de déploiement. Par exemple, entrez `http://{your-server-ip-address}`.

## <a name="start-a-cicd-deployment"></a>Démarrer un déploiement CI/CD

1. Dans la définition de mise en production, décochez la case **Activé** dans la section **Options de contrôle** des paramètres de la tâche Déploiement de groupe de ressources Azure.
   Pour les futurs déploiements sur le groupe de déploiement existant, vous n’avez pas besoin de réexécuter cette tâche.

1. Accédez au référentiel Git source et modifiez le contenu du titre **h1** dans le fichier [app/views/index.jade](https://github.com/azooinmyluggage/fabrikam-node/blob/master/app/views/index.jade).

1. Validez votre modification.

1. Après quelques minutes, vous verrez une nouvelle mise en production sur la page **Mises en production** de Team Services ou TFS. Ouvrez la mise en production pour voir le déploiement en action. Félicitations !

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez automatisé le déploiement d’une application Azure à l’aide d’une build Jenkins et de Team Services pour la mise en production. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Générer votre application dans Jenkins
> * Configurer Jenkins pour l’intégration Team Services
> * Créer un groupe de déploiement pour les machines virtuelles Azure
> * Créer une définition de mise en production qui configure les machines virtuelles et déploie l’application

Passez au didacticiel suivant pour en savoir plus sur le déploiement d’une pile LAMP (Linux, Apache, MySQL et PHP).

> [!div class="nextstepaction"]
> [Déploiement d’une pile LAMP dans Azure](tutorial-lamp-stack.md)