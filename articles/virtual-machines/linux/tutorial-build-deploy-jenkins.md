---
title: "Intégration continue/déploiement continu de Jenkins vers des machines virtuelles Azure avec VSTS | Microsoft Docs"
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
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>Déployer votre application sur des machines virtuelles Linux à l’aide de Jenkins et VSTS

L’intégration continue (CI) et le déploiement continu (CD) constituent un pipeline via lequel vous pouvez générer, mettre en production et déployer votre code. Visual Studio Team Services (VSTS) fournit un ensemble complet d’outils d’automatisation CI/CD pour le déploiement sur Azure. Jenkins est un outil serveur CI/CD tiers populaire qui propose également l’automatisation CI/CD. Vous pouvez les utiliser ensemble pour personnaliser la façon dont vous proposez votre service ou application cloud.

Dans ce didacticiel, vous utilisez Jenkins pour générer une **application web Node.js** et VSTS ou Team Foundation Server (TFS) pour la déployer sur un [groupe de déploiement](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) contenant des machines virtuelles Linux.

Vous allez :

> [!div class="checklist"]
> * Obtenir l’exemple d’application
> * Configurer les plug-ins Jenkins
> * Configurer un projet libre (Freestyle) Jenkins pour Node.js
> * Configurer Jenkins pour l’intégration VSTS
> * Créer un point de terminaison de service Jenkins
> * Créer un groupe de déploiement pour les machines virtuelles Azure
> * Créer une définition de mise en production VSTS
> * Exécuter des déploiements déclenchés manuellement ou par CI

## <a name="before-you-begin"></a>Avant de commencer

* Vous devez avoir accès à un serveur Jenkins. Si vous n’avez pas encore créé de serveur Jenkins, consultez [Créer un serveur Jenkins sur une machine virtuelle Azure](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template). 

* Connectez-vous à votre compte VSTS (`https://{youraccount}.visualstudio.com`). 
  Vous pouvez obtenir un [compte VSTS gratuit](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Pour plus d’informations, consultez [Se connecter à VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  Vous avez besoin d’une machine virtuelle Linux pour une cible de déploiement.  Pour plus d’informations, consultez [Créer et gérer des machines virtuelles Linux avec l’interface Azure CLI](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm).

*  Ouvrez le port d’entrée 80 pour votre machine virtuelle.  Pour plus d’informations, consultez [Créer des groupes de sécurité réseau à l’aide du portail Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Obtenir l’exemple d’application

Vous devez déployer une application stockée dans un référentiel Git.
Pour ce didacticiel, nous vous recommandons d’utiliser [cet exemple d’application disponible sur GitHub](https://github.com/azooinmyluggage/fabrikam-node).  Ce didacticiel contient un exemple de script utilisé pour l’installation de Node.js et d’une application.  Si vous souhaitez utiliser votre propre référentiel, vous devez configurer un exemple similaire.

1. Dupliquez cette application et notez son emplacement (URL) pour pouvoir l’utiliser ultérieurement dans ce didacticiel.  Pour plus d’informations, consultez [Fork a repo](https://help.github.com/articles/fork-a-repo/) (Dupliquer un référentiel).    

> [!NOTE]
> L’application a été générée à l’aide de [Yeoman](http://yeoman.io/learning/index.html) ; elle utilise **Express**, **bower** et **grunt** et comprend certains packages **npm** en tant que dépendances.
> L’exemple contient également un script qui configure Nginx et déploie l’application. Il est exécuté sur les machines virtuelles. Plus précisément, le script installe Node, Nginx et PM2. Il configure Nginx et PM2, puis démarre l’application Node.

## <a name="configure-jenkins-plugins"></a>Configurer les plug-ins Jenkins

Tout d’abord, vous devez configurer deux plug-ins Jenkins pour **NodeJS** et **VS Team Services Continuous Deployment**.

1. Ouvrez votre compte Jenkins et choisissez **Manage Jenkins** (Gérer Jenkins).
2. Sur la page **Manage Jenkins** (Gérer Jenkins), choisissez **Manage Plugins** (Gérer les plug-ins).
3. Filtrez la liste pour localiser le plug-in **NodeJS** et sélectionnez l’option **Install without restart** (Installer sans redémarrage).
    ![Ajout du plug-in NodeJS à Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrez la liste pour localiser le plug-in **VS Team Services Continuous Deployment** et sélectionnez l’option **Install without restart** (installer sans redémarrage).
5. Accédez au tableau de bord Jenkins et sélectionnez **Manage Jenkins** (Gérer Jenkins).
6. Sélectionnez **Global Tool Configuration** (Configuration globale d’outils).  Recherchez **NodeJS** et cliquez sur **NodeJS installations** (Installations NodeJS).
7. Activez l’option **Install automatically** (Installer automatiquement), puis entrez un nom sous **Name**.
8. Cliquez sur **Enregistrer**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurer un projet libre (Freestyle) Jenkins pour Node.js

1. Cliquez sur **New Item** (Nouvel élément).  Entrez un **nom d’élément**.
2. Sélectionnez **Freestyle project** (Projet libre).  Cliquez sur **OK**.
3. Dans l’onglet **Gestion du code source**, sélectionnez **Git** et entrez les détails du référentiel et la branche qui contient le code de votre application.    
    ![Ajouter un référentiel à votre build](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Dans l’onglet **Build Triggers** (Générer des déclencheurs), sélectionnez **Poll SCM** (Interroger SCM) et entrez la planification `H/03 * * * *` pour interroger le référentiel Git pour les modifications toutes les trois minutes. 
5. Dans l’onglet **Build Environment** (Générer un environnement), sélectionnez **Provide Node &amp; npm bin/ folder PATH** (Fournir le CHEMIN D’ACCÈS au dossier npm bin/ et à Node) et sélectionnez la valeur**NodeJS Installation** (Installation NodeJS). Laissez **npmrc file** (fichier npmrc) défini sur « utiliser les valeurs par défaut du système ».
6. Dans l’onglet **Build** (Générer), sélectionnez **Execute shell** (Exécuter Shell) et entrez la commande `npm install` pour vous assurer que toutes les dépendances sont mises à jour.


## <a name="configure-jenkins-for-vsts-integration"></a>Configurer Jenkins pour l’intégration VSTS

  > [!NOTE]
  Vérifiez que le jeton d’accès personnel (PAT) que vous utilisez pour les étapes suivantes contient l’**autorisation de (lecture, écriture, exécution et gestion) de mise en production dans VSTS**.
 
1.  Créez un jeton PAT dans votre compte VSTS si vous n’en avez pas. Jenkins requiert ces informations pour accéder à votre compte VSTS.  Assurez-vous de **stocker** les informations sur le jeton qui seront nécessaires pour les prochaines étapes de cette section.
  Consultez [Créer un jeton d’accès personnel pour VSTS et TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) pour savoir comment générer un jeton.
2. Dans l’onglet **Post-build Actions** (Actions post-build), cliquez sur **Add post-build action** (Ajouter une action post-build). Sélectionnez **Archive the artifacts** (Archiver les artefacts).
3. Sous **Files to archive** (Fichiers à archiver), entrez `**/*` pour inclure tous les fichiers.
4. Pour créer une autre action, cliquez sur **Add post-build action** (Ajouter une action post-build).
5. Sélectionnez **Trigger release in TFS/Team Services** (Déclencher la mise en production dans TFS/Team Services) et entrez l’URI de votre compte VSTS, tel que :   `https://{your-account-name}.visualstudio.com`.
6. Entrez le nom du **projet d’équipe**.
7. Attribuez un nom à la **définition de mise en production** (vous créerez celle-ci plus tard dans VSTS).
8. Choisissez les informations d’identification pour la connexion à votre environnement VSTS ou TFS.  Laissez le champ du **nom d’utilisateur** vide si vous utilisez VSTS.
   Entrez **un nom d’utilisateur et un mot de passe** si vous utilisez une version locale de TFS.    
    ![Configuration des actions post-build Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. **Enregistrez** le projet Jenkins.

## <a name="create-a-jenkins-service-endpoint"></a>Créer un point de terminaison de service Jenkins

Un point de terminaison de service permet à VSTS de se connecter à Jenkins.

1. Accédez à la page **Services** dans VSTS, ouvrez la liste **Nouveau point de terminaison de service** et choisissez **Jenkins**.
    ![Ajouter un point de terminaison Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Attribuez un nom à cette connexion.
3. Entrez l’URL de votre serveur Jenkins et cochez l’option **Accepter les certificats SSL non fiables**.  Exemple d’URL : `http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. Entrez **le nom d’utilisateur et le mot de passe** de votre compte Jenkins.
5. Choisissez **Vérifier la connexion** pour vérifier que les informations sont correctes.
6. Sélectionnez **OK** pour créer le point de terminaison de service.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Créer un groupe de déploiement pour les machines virtuelles Azure

Vous avez besoin d’un [groupe de déploiement](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) pour inscrire l’agent VSTS et pouvoir ainsi déployer la définition de mise en production sur votre machine virtuelle.  Les groupes de déploiement permettent de définir facilement des groupes logiques de machines cibles pour le déploiement et d’installer l’agent requis sur chaque machine.

   > [!NOTE]
   > Dans les étapes suivantes, assurez-vous d’installer les composants requis et de **ne pas exécuter le script avec des privilèges sudo**.

1. Ouvrez l’onglet **Mises en production** du hub **Build &amp; mise en production**, puis ouvrez **Groupes de déploiement** et sélectionnez **+ Nouveau**.
2. Entrez un nom pour le groupe de déploiement, et éventuellement une description.
   Sélectionnez ensuite **Créer**.
3. Sélectionnez le système d’exploitation pour votre machine virtuelle cible de déploiement.  Par exemple, sélectionnez **Ubuntu 16.04+**.
4. Cochez l’option **Utiliser un jeton d'accès personnel dans le script pour l'authentification**.
5. Sélectionnez le lien **Prérequis système**.  Installez les composants requis pour votre système d’exploitation.
6. Sélectionnez l’option **Copier le script dans le Presse-papiers** pour copier le script.
7. **Connectez-vous** à votre machine virtuelle cible de déploiement et **exécutez** le script.  **N’exécutez pas** le script avec des privilèges sudo.
8. Après l’installation, vous êtes invité à confirmer les étiquettes du groupe de déploiement.  Acceptez les valeurs par défaut.
9. Dans VSTS, recherchez la machine virtuelle que vous venez d’inscrire dans **Cibles** sous **Groupes de déploiement**.

## <a name="create-a-vsts-release-definition"></a>Créer une définition de mise en production VSTS

Une définition de mise en production spécifie le processus que VSTS exécutera pour déployer l’application.  Dans cet exemple, vous exécutez un script Shell.

Pour créer la définition de mise en production dans VSTS :

1. Ouvrez l’onglet **Mises en production** du hub **Build &amp; mise en production** et sélectionnez **Créer une définition de mise en production**. 
2. Sélectionnez le modèle **Vide** en choisissant de démarrer avec un **processus Vide**.
3. Dans la section **Artefacts**, cliquez sur **+ Ajouter un artefact** et sélectionnez **Jenkins** sous **Type de source**. Sélectionnez votre connexion de point de terminaison de service Jenkins. Sélectionnez ensuite le travail de source Jenkins, puis choisissez **Ajouter**.
4.  Cliquez sur les points de suspension en regard de **Environnement 1**.  Cliquez sur **Phase d'ajout d'un groupe de déploiement**.
5.  Sélectionnez votre **groupe de déploiement**.
5. Cliquez sur **+** pour ajouter une tâche à la **phase de groupe de déploiement**.
6. Sélectionnez la tâche **Script Shell** et cliquez sur **Ajouter**.    
    La tâche **Script Shell** est utilisée pour fournir la configuration d’un script à exécuter sur chaque serveur, afin d’installer Node.js et de démarrer l’application. Configurez-le comme suit :
8. **Chemin d’accès au script** :     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  Cliquez sur **Avancé**, puis activez l’option **Spécifier le répertoire de travail**.
10.  **Répertoire de travail** : `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. Modifiez le nom de la définition de mise en production pour le remplacer par le nom que vous avez spécifié dans l’onglet **Actions post-génération** de la build dans Jenkins. Jenkins exige que ce nom soit en mesure de déclencher une nouvelle mise en production lorsque les artefacts source sont mis à jour.
12. Cliquez sur **Enregistrer**, puis sur **OK** pour enregistrer la définition de mise en production.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Exécuter des déploiements déclenchés manuellement ou par CI

1. Choisissez **+ Mise en production** et sélectionnez **Créer une mise en production**.
2. Sélectionnez la build que vous avez générée dans la liste déroulante en surbrillance et choisissez **File d’attente**.
3. Dans le message qui s’affiche, sélectionnez le lien de la mise en production. Par exemple : « Mise en production **Mise en production-1** a été créé. ».
4. Ouvrez l’onglet **Journaux** pour surveiller la sortie de console de mise en production.
5. Dans votre navigateur, ouvrez l’URL de l’un des serveurs que vous avez ajoutés à votre groupe de déploiement. Par exemple, entrez `http://{your-server-ip-address}`.
6. Accédez au dépôt Git source et modifiez le contenu du titre **h1** dans le fichier app/views/index.jade en apportant quelques corrections au texte.
7. **Validez** vos modifications.
8. Après quelques minutes, vous verrez une nouvelle mise en production sur la page **Mises en production** de VSTS ou TFS. Ouvrez la mise en production pour voir le déploiement en action. Félicitations !

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez automatisé le déploiement d’une application Azure à l’aide d’une build Jenkins et de VSTS pour la mise en production. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Générer votre application dans Jenkins
> * Configurer Jenkins pour l’intégration VSTS
> * Créer un groupe de déploiement pour les machines virtuelles Azure
> * Créer une définition de mise en production qui configure les machines virtuelles et déploie l’application

Passez au didacticiel suivant pour en savoir plus sur le déploiement d’une pile LAMP (Linux, Apache, MySQL et PHP).

> [!div class="nextstepaction"]
> [Déploiement d’une pile LAMP dans Azure](tutorial-lamp-stack.md)