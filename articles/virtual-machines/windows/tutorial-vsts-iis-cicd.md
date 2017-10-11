---
title: "Création d’un pipeline CI/CD dans Azure avec Team Services | Microsoft Docs"
description: "Découvrez comment créer un pipeline Visual Studio Team Services pour une intégration continue et une fourniture qui déploie une application web vers IIS sur une machine virtuelle Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a587f58fad2ec74c7633823c4d34f900e7c01f7e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Création d’un pipeline d’intégration continue avec Visual Studio Team Services et IIS
Pour automatiser les phases de création, de test et de déploiement du développement de l’application, vous pouvez utiliser un pipeline d’intégration et de déploiement continus (CI/CD). Dans ce didacticiel, vous créez un pipeline CI/CD à l’aide de Visual Studio Team Services et d’une machine virtuelle Windows (VM) dans Azure qui exécute IIS. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Publier une application web ASP.NET dans un projet Team Services
> * Créer une définition de build qui est déclenchée par les validations de code
> * Installer et configurer IIS sur une machine virtuelle dans Azure
> * Ajouter l’instance IIS à un groupe de déploiement dans Team Services
> * Créer une définition de version pour publier les nouveaux packages de déploiement web sur IIS
> * test du pipeline CI/CD

Ce didacticiel requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="create-project-in-team-services"></a>Création d’un projet dans Team Services
Visual Studio Team Services simplifie la collaboration et permet le développement sans recours à une solution de gestion de code en local. Team Services offre des fonctionnalités de test de code, de création et d’analyse de l’application dans le cloud. Vous pouvez choisir le référentiel de contrôle de version et l’IDE qui conviennent le mieux au développement de votre code. Pour ce didacticiel, vous pouvez utiliser un compte gratuit pour créer une application web ASP.NET de base et un pipeline CI/CD. Si vous n’avez pas encore de compte Team Services, [créez-en un](http://go.microsoft.com/fwlink/?LinkId=307137).

Pour gérer le processus de validation de code, de définition de build et de définition de version, créez un projet dans Team Services en procédant comme suit :

1. Ouvrez votre tableau de bord Team Services dans un navigateur web et choisissez **Nouveau projet**.
2. Entrez *myWebApp* comme **nom de projet**. Laissez toutes les autres valeurs par défaut pour utiliser le contrôle de version *Git* et le processus d’élément de travail *Agile*.
3. Choisissez l’option **Partager avec** *Membres de l’équipe*, puis sélectionnez **Créer**.
5. Une fois votre projet créé, sélectionnez l’option **Initialize with a README or gitignore** (Initialiser avec un fichier README ou gitignore), puis **Initialiser**.
6. À l’intérieur de votre nouveau projet, choisissez **Tableaux de bord** en haut, puis sélectionnez **Ouvrir dans Visual Studio**.


## <a name="create-aspnet-web-application"></a>Création d’une application web ASP.NET
À l’étape précédente, vous avez créé un projet dans Team Services. La dernière étape ouvre votre nouveau projet dans Visual Studio. Vous gérez vos validations de code dans la fenêtre **Team Explorer**. Créez une copie locale de votre nouveau projet, puis créez une application web ASP.NET à partir d’un modèle comme suit :

1. Sélectionnez **Cloner** pour créer un référentiel git local de votre projet Team Services.
    
    ![Clonage du référentiel à partir du projet Team Services](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. Sous **Solutions**, sélectionnez **Nouveau**.

    ![Création de la solution d’application web](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Sélectionnez les modèles **web**, puis choisissez le modèle **Application web ASP.NET**.
    1. Entrez un nom pour votre application, tel que *myWebApp*, et décochez la case **Créer un répertoire pour la solution**.
    2. Si l’option est disponible, décochez la case **Add Application Insights to project** (Ajouter Application Insights au projet). Application Insights nécessite que vous autorisiez votre application web dans Azure Application Insights. Pour faire simple, dans ce didacticiel, ignorez cette procédure.
    3. Sélectionnez **OK**.
4. Dans la liste des modèles, choisissez **MVC**.
    1. Pour **Modifier l’authentification**, sélectionnez **Aucune authentification**, puis sélectionnez **OK**.
    2. Sélectionnez **OK** pour créer votre solution.
5. Dans la fenêtre **Team Explorer**, choisissez **Modifications**.

    ![Validation des modifications locales dans le référentiel git Team Services](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. Dans la zone de texte de validation, entrez un message tel que *Validation initiale*. Dans le menu déroulant, choisissez **Commit All and Sync** (Tout valider et synchroniser).


## <a name="create-build-definition"></a>Création d’une définition de build
Dans Team Services, vous utilisez une définition de build pour montrer la manière dont votre application doit être conçue. Dans ce didacticiel, nous créons une définition de base qui prend notre code source, génère la solution, puis crée le package de déploiement web que nous pouvons utiliser pour exécuter l’application web sur un serveur IIS.

1. Dans votre projet Team Services, choisissez l’option **Build & Release** (Générer et publier) en haut, puis sélectionnez **Builds**.
3. Sélectionnez **+ Nouvelle définition**.
4. Choisissez le modèle **ASP.NET (préversion)** et sélectionnez **Appliquer**.
5. Laissez les valeurs par défaut pour les tâches. Sous **Obtenir les sources**, assurez-vous que le référentiel *myWebApp* et la branche *master* sont sélectionnés.

    ![Création d’une définition de build dans le projet Team Services](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Sur l’onglet **Déclencheurs**, déplacez le curseur **Enable this trigger** (Activer ce déclencheur) sur *Activé*.
7. Enregistrez la définition de build et mettez en file d’attente une nouvelle build en sélectionnant **Save & queue** (Enregistrer et mettre en file d’attente), puis de nouveau **Save & queue** (Enregistrer et mettre en file d’attente). Conservez les valeurs par défaut et sélectionnez **File d’attente**.

Vérifiez que la build est planifiée sur un agent hébergé, puis commencez la génération. Le résultat ressemble à l’exemple suivant :

![Génération réussie du projet Team Services](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Create virtual machine
Pour fournir une plateforme sur laquelle exécuter votre application web ASP.NET, vous avez besoin d’une machine virtuelle Windows qui exécute IIS. Team Services utilise un agent pour interagir avec l’instance IIS lorsque vous validez le code et que les builds sont déclenchées.

Créez une machine virtuelle Windows Server 2016 à l’aide de [cet exemple de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json). Il faut quelques minutes pour que le script s’exécute et crée la machine virtuelle. Une fois la machine virtuelle créée, ouvrez le port 80 pour le trafic web avec [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.resources/new-azurermresourcegroup) comme suit :

```powershell
Get-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $resourceGroup `
  -Name "myNetworkSecurityGroup" | `
Add-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWeb" `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority "1001" `
  -SourceAddressPrefix "*" `
  -SourcePortRange "*" `
  -DestinationAddressPrefix "*" `
  -DestinationPortRange "80" `
  -Access "Allow" | `
Set-AzureRmNetworkSecurityGroup
```

Pour vous connecter à votre machine virtuelle, obtenez l’adresse IP publique avec [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) comme suit :

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup | Select IpAddress
```

Créez une session Bureau à distance vers votre machine virtuelle :

```cmd
mstsc /v:<publicIpAddress>
```

Sur la machine virtuelle, ouvrez une invite de commande **Administrateur PowerShell**. Installez IIS et les fonctionnalités .NET requises comme suit :

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Créer un groupe de déploiement
Pour diffuser le package de déploiement web vers le serveur IIS, vous définissez un groupe de déploiement dans Team Services. Ce groupe vous permet de spécifier quels serveurs sont la cible de nouvelles builds lorsque vous validez du code dans Team Services et que les builds sont terminées.

1. Dans Team Services, choisissez **Build & Release** (Générer et publier), puis sélectionnez **Groupes de déploiement**.
2. Choisissez **Add Deployment group** (Ajouter un groupe de déploiement).
3. Entrez un nom pour le groupe, tel que *myIIS*, puis sélectionnez **Créer**.
4. Dans la section **Register machines** (Enregistrer des ordinateurs), assurez-vous que l’option *Windows* est sélectionnée, puis cochez la case **Use a personal access token in the script for authentication** (Utiliser un jeton d’accès personnel dans le script pour l’authentification).
5. Sélectionnez **Copy script to clipboard** (Copier le script dans le Presse-papiers).


### <a name="add-iis-vm-to-the-deployment-group"></a>Ajouter une machine virtuelle IIS au groupe de déploiement
Une fois le groupe de déploiement créé, ajoutez-y chaque instance IIS. Team Services génère un script qui télécharge et configure un agent sur la machine virtuelle qui reçoit les nouveaux packages de déploiement web, puis l’applique à IIS.

1. Dans la session **Administrateur PowerShell** sur votre machine virtuelle, collez le script copié à partir de Team Services et exécutez-le.
2. Lorsque vous êtes invité à configurer les balises pour l’agent, choisissez *Y* et entrez *web*.
3. Lorsque vous êtes invité à indiquer le compte d’utilisateur, appuyez sur *Retour* pour accepter les valeurs par défaut.
4. Attendez que le script se termine avec un message de type *Service vstsagent.account.computername démarré correctement*.
5. Dans la page **Groupes de déploiement** du menu **Build & Release** (Générer et publier), ouvrez le groupe de déploiement *myIIS*. Sur l’onglet **Machines**, vérifiez que votre machine virtuelle est répertoriée.

    ![Machine virtuelle ajoutée au groupe de déploiement Team Services](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>Création d’une définition de mise en production
Pour publier vos builds, vous créez une définition de mise en production dans Team Services. Cette définition est déclenchée automatiquement en cas de réussite de la génération de votre application. Vous choisissez le groupe de déploiement dans lequel distribuer votre application web et définissez les paramètres IIS appropriés.

1. Choisissez **Build & Release** (Générer et publier), puis sélectionnez **Builds**. Sélectionnez la définition de build créée à l’étape précédente.
2. Sous **Effectuées récemment**, choisissez la build la plus récente, puis sélectionnez **Mise en production**.
3. Choisissez **Oui** pour créer une définition de mise en production.
4. Choisissez le modèle **vide**, puis sélectionnez **Suivant**.
5. Vérifiez que le projet et la définition de build source sont renseignés avec votre projet.
6. Cochez la case **Déploiement continu**, puis sélectionnez **Créer**.
7. Sélectionnez la liste déroulante en regard de **+ Ajouter des tâches** et choisissez *Add a deployment group phase* (Ajouter une phase de déploiement de groupe).
    
    ![Ajouter une tâche à la définition de mise en production dans Team Services](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Choisissez **Ajouter** en regard de **IIS Web App Deploy (Preview)** (Déploiement d’application web IIS (préversion)), puis sélectionnez **Fermer**.
9. Sélectionnez la tâche parente **Run on deployment group** (Exécuter sur le groupe de déploiement).
    1. Pour **Groupe de déploiement**, sélectionnez le groupe de déploiement que vous avez créé précédemment, par exemple *myIIS*.
    2. Dans la zone **Machine tags** (Balises de machine), sélectionnez **Ajouter** et choisissez la balise *web*.
    
    ![Tâche de groupe de déploiement de définition de mise en production pour IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Sélectionnez la tâche **Déployer : déploiement de l’application web IIS** pour configurer les paramètres de votre instance IIS comme suit :
    1. Pour **Nom du site web**, entrez *Site web par défaut*.
    2. Laissez tous les autres paramètres par défaut.
12. Choisissez **Enregistrer**, puis sélectionnez **OK** deux fois.


## <a name="create-a-release-and-publish"></a>Création d’une version et publication
Vous pouvez désormais distribuer votre package de déploiement web en tant que nouvelle version. Cette étape communique avec l’agent sur chaque instance qui fait partie du groupe de déploiement, distribue le package de déploiement web, puis configure IIS pour exécuter l’application web mise à jour.

1. Dans votre définition de mise en production, sélectionnez **+ Mise en production**, puis choisissez **Créer une mise en production**.
2. Vérifiez que la build la plus récente est sélectionnée dans la liste déroulante, ainsi que **Déploiement automatisé : après la création de la mise en production**. Sélectionnez **Créer**.
3. Une petite bannière apparaît en haut de votre définition de mise en production, par exemple *La mise en production 'Mise en production-1' a été créée*. Sélectionnez le lien de la mise en production.
4. Ouvrez l’onglet **Journaux** pour surveiller la progression de la mise en production.
    
    ![Réussite de la mise en production Team Services et de la distribution du package de déploiement web](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Une fois la mise en production terminée, ouvrez un navigateur web et entrez l’adresse IP publique de votre machine virtuelle. Votre application web ASP.NET est en cours d’exécution.

    ![Application web ASP.NET s’exécutant sur une machine virtuelle IIS](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Test de l’ensemble du pipeline CI/CD
Votre application web s’exécutant sur IIS, testez à présent l’ensemble du pipeline CI/CD. Lorsque vous apportez une modification dans Visual Studio et validez votre code, une build est déclenchée, qui déclenche ensuite une mise en production de votre package de déploiement web mis à jour sur IIS :

1. Dans Visual Studio, ouvrez la fenêtre **Explorateur de solutions**.
2. Recherchez et ouvrez *myWebApp | Vues | Accueil | Index.cshtml*
3. Modifiez la ligne 6 en :

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Enregistrez le fichier.
5. Ouvrez la fenêtre **Team Explorer**, sélectionnez le projet *myWebApp*, puis choisissez **Modifications**.
6. Entrez un message de validation, tel que *Test du pipeline CI/CD*, puis choisissez **Commit All and Sync** (Tout valider et synchroniser) dans le menu déroulant.
7. Dans l’espace de travail de Team Services, une nouvelle build est déclenchée à partir de la validation du code. 
    - Choisissez **Build & Release** (Générer et publier), puis sélectionnez **Builds**. 
    - Choisissez votre définition de build, puis sélectionnez la build **en file d’attente et en cours d’exécution** pour afficher la progression.
9. Une fois la génération réussie, une nouvelle mise en production est déclenchée.
    - Choisissez **Build & Release** (Générer et publier), puis **Mises en production**, pour voir le package de déploiement web distribué sur votre machine virtuelle IIS. 
    - Sélectionnez l’icône **Actualiser** pour mettre à jour l’état. Lorsque la colonne *Environnements* affiche une coche verte, cela signifie que la mise en production a bien été déployée sur IIS.
11. Pour afficher les modifications que vous avez appliquées, actualisez votre site web IIS dans un navigateur.

    ![Application web ASP.NET s’exécutant sur une machine virtuelle IIS à partir d’un pipeline CI/CD](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une application web ASP.NET dans Team Services et configuré des définitions de build et de mise en production pour déployer de nouveaux packages de déploiement web sur IIS lors de la validation de chaque code. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Publier une application web ASP.NET dans un projet Team Services
> * Créer une définition de build qui est déclenchée par les validations de code
> * Installer et configurer IIS sur une machine virtuelle dans Azure
> * Ajouter l’instance IIS à un groupe de déploiement dans Team Services
> * Créer une définition de version pour publier les nouveaux packages de déploiement web sur IIS
> * test du pipeline CI/CD

Passez au didacticiel suivant pour savoir comment mieux protéger les serveurs SSL à l’aide des certificats SSL.

> [!div class="nextstepaction"]
> [Sécuriser un serveur web avec SSL](tutorial-secure-web-server.md)