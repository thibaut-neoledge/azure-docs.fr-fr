---
title: Intégration continue pour Service Fabric | Microsoft Docs
description: Découvrez comment configurer l’intégration continue pour une application Service Fabric à l’aide de Visual Studio Team Services (VSTS).
services: service-fabric
documentationcenter: na
author: mthalman-msft
manager: timlt
editor: ''

ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 08/01/2016
ms.author: mthalman

---
# Configuration de l’intégration continue pour une application Service Fabric à l’aide de Visual Studio Team Services
Cet article décrit les étapes de la configuration de l’intégration continue pour une application Service Fabric Azure à l’aide de Visual Studio Team Services (VSTS) de sorte que votre application soit créée, empaquetée et déployée de manière automatique.

Ce document reflète la procédure actuelle et est susceptible d'évoluer au fil du temps.

## Composants requis
Pour commencer, suivez ces instructions :

1. Assurez-vous d’avoir accès à un compte Team Services, ou [créez-en un](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).
2. Assurez-vous d’avoir accès à un projet d’équipe Team Services, ou [créez-en un](https://www.visualstudio.com/docs/setup-admin/create-team-project).
3. Vérifiez que vous disposez d’un cluster Service Fabric sur lequel vous pouvez déployer votre application, ou créez-en un à l’aide du [Portail Azure](service-fabric-cluster-creation-via-portal.md), d’un [modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) ou de [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).
4. Vérifiez qu’un projet d’application Service Fabric (.sfproj) a été créé. Vous devez utiliser un projet créé ou mis à niveau avec Service Fabric SDK 2.1 ou version ultérieure (le fichier .sfproj doit contenir une valeur de propriété ProjectVersion 1.1 ou version ultérieure).

> [!NOTE]
> Les agents de build personnalisés ne sont plus nécessaires. Les agents hébergés par Team Services sont désormais installés au préalable avec le logiciel de gestion du cluster Service Fabric, ce qui permet le déploiement de vos applications depuis ces agents, directement.
> 
> 

## Configurer et partager vos fichiers source
La première chose à faire est de préparer un profil de publication devant être utilisé par le processus de déploiement qui s’exécute au sein de Team Services. Le profil de publication doit être configuré pour cibler le cluster que vous avez précédemment préparé :

1. Au sein de votre projet d’application, choisissez le profil de publication que vous souhaitez utiliser pour votre flux de travail d’intégration continue et suivez les [instructions](service-fabric-publish-app-remote-cluster.md) expliquant comment publier une application sur un cluster distant. Toutefois, vous n’avez pas réellement besoin de publier votre application. Vous pouvez simplement cliquer sur le lien hypertexte **Enregistrer** figurant dans la boîte de dialogue Publier une fois que vous avez configuré les fonctions correctement.
2. Si vous souhaitez que votre application soit mise à niveau pour chaque déploiement effectué au sein de Team Services, vous devez configurer le profil de publication de façon à autoriser cette mise à niveau. Dans la boîte de dialogue déjà utilisée à l’étape 1, assurez-vous que la case à cocher **Upgrade the application** (Mettre à niveau l’application) est activée. Plus d’informations sur la [configuration de paramètres de mise à niveau supplémentaires](service-fabric-visualstudio-configure-upgrade.md). Cliquez sur le lien hypertexte **Enregistrer** pour enregistrer les paramètres dans le profil de publication.
3. Assurez-vous que vous avez enregistré vos modifications pour le profil de publication et annulez la boîte de dialogue Publier.
4. Il est à présent temps de [partager les fichiers source du projet d’application](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#vs) avec Team Services. Une fois que vos fichiers source sont accessibles dans Team Services, vous pouvez maintenant passer à l’étape suivante de la génération de builds.

## Créer une définition de build
Une définition de build Team Services décrit un flux de travail qui se compose d’un ensemble d’étapes de génération exécutées séquentiellement. L’objectif de la définition de build que vous créez est de produire un package d’application Service Fabric, mais aussi d’inclure certains fichiers supplémentaires, qui peuvent être utilisés pour déployer l’application sur un cluster, en fin de compte. Plus d’informations sur les [définitions de build](https://www.visualstudio.com/docs/build/define/create) Team Services.

### Créer une définition à partir du modèle de build
1. Ouvrez votre projet d’équipe dans Visual Studio Team Services.
2. Sélectionnez l’onglet **Build**.
3. Cliquez sur le signe **+** vert pour créer une définition de build.
4. Dans la boîte de dialogue qui s’ouvre, sélectionnez **Application Azure Service Fabric** dans la catégorie de modèle **Build**.
5. Sélectionnez **Suivant**.
6. Sélectionnez le référentiel et la branche associés à votre application Service Fabric.
7. Sélectionnez la file d’attente de l’agent que vous souhaitez utiliser. Les agents hébergés sont pris en charge.
8. Sélectionnez **Créer**.
9. Enregistrez la définition de génération et donnez-lui un nom.
10. Voici une description des étapes de génération créées par le modèle :

| Étape de génération | Description |
| --- | --- |
| Restauration NuGet |Restaure les packages NuGet pour la solution. |
| Génération de la solution *.sln |Génère la solution dans son ensemble. |
| Génération de la solution *.sfproj |Génère le package d’application Service Fabric qui sera utilisé pour déployer l’application. Notez que l’emplacement du package d’application est spécifié dans le répertoire d’artefact de la build. |
| Mise à jour des versions de l’application Service Fabric |Met à jour les valeurs de version contenues dans les fichiers manifeste du package d’application, afin de permettre la prise en charge des mises à niveau. Pour en savoir plus, consultez la [page de documentation sur les tâches](https://go.microsoft.com/fwlink/?LinkId=820529). |
| Copie des fichiers |Copie le profil de publication et les fichiers de paramètres d’application sur les artefacts de la build, afin de permettre leur utilisation pour le déploiement. |
| Publication d’artefact |Publie les artefacts de la build. Cela permet de créer une définition de version pour l’utilisation des artefacts de la build. |

### Vérifier l’ensemble de tâches par défaut
1. Vérifiez le champ d’entrée **Solution** associé aux étapes de génération **Restauration NuGet** et **Génération de la solution**. Par défaut, ces étapes de génération s’exécutent sur tous les fichiers solution contenus dans le référentiel associé. Si vous souhaitez que la définition de build fonctionne uniquement sur l’un de ces fichiers solution, vous devez mettre à jour le chemin vers ce fichier et ce, de manière explicite.
2. Vérifiez le champ d’entrée **Solution** associé à l’étape de génération **Empaqueter une application**. Par défaut, cette étape de génération suppose qu’un seul projet d’application Service Fabric (.sfproj) existe dans le référentiel. Si votre référentiel inclut plusieurs fichiers de ce genre et si vous ne souhaitez en cibler qu’un seul pour cette définition de build, vous devez mettre à jour le chemin vers ce fichier et ce, de manière explicite. Si vous souhaitez empaqueter plusieurs projets d’application dans votre référentiel, vous devez créer d’autres étapes de **génération Visual Studio** dans la définition de build, chacune devant cibler un projet d’application. Vous devez ensuite mettre à jour le champ **Arguments MSBuild** pour chacune de ces étapes de génération, afin que l’emplacement du package soit unique pour chacune.
3. Vérifiez le comportement de contrôle de version défini dans l’étape de génération **Mise à jour des versions de l’application Service Fabric**. Par défaut, cette étape de génération ajoute le numéro de build à l’ensemble des valeurs de version dans les fichiers manifeste du package d’application. Pour en savoir plus, consultez la [page de documentation sur les tâches](https://go.microsoft.com/fwlink/?LinkId=820529) . Cette page est utile pour prendre en charge de la mise à niveau de votre application, car chaque déploiement de mise à niveau nécessite des valeurs de version différentes de celles du déploiement précédent. Si vous n’envisagez pas d’utiliser la mise à niveau de l’application dans votre flux de travail, désactivez cette étape de génération. En fait, elle doit être désactivée si votre comptez créer une build qui peut être utilisée pour remplacer une application Service Fabric existante car le déploiement échouera si la version de l’application générée par la build ne correspond pas à la version de l’application dans le cluster.
4. Si votre solution contient un projet .NET Core, vous devez vous assurer que votre définition de build contient une étape de génération qui restaure les dépendances définies par les fichiers project.json. Pour ce faire, procédez comme suit :
   1. Sélectionnez **Ajouter une étape de build...**.
   2. Recherchez la tâche **Ligne de commande** dans l’onglet Utilitaire puis cliquez sur le bouton Ajouter.
   3. Pour les champs d’entrée de la tâche, utilisez les valeurs suivantes :
   4. Outil : dotnet
   5. Arguments : restore
   6. Faites glisser la tâche immédiatement après l’étape **Restauration NuGet**.
5. Enregistrez toutes les modifications apportées à la définition de build.

### Essayer
Sélectionnez l’option **Mettre la build en file d’attente** pour démarrer une build manuellement. Les builds seront également déclenchées au moment de l’archivage ou d’une opération de type push. Lorsque vous avez vérifié que la build s’exécute correctement, vous pouvez passer à la définition d’une définition de version qui déploiera votre application sur un cluster.

## Création d’une définition de version
Une définition de version Team Services décrit un flux de travail qui se compose d’un ensemble d’étapes de génération exécutées séquentiellement. L’objectif de la définition de version que vous créez est de déployer un package d’application sur un cluster. Lorsqu’elles sont utilisées ensemble, la définition de version et la définition de génération peuvent démarrer le flux de travail dans son ensemble, depuis le démarrage avec des fichiers source jusqu’à l’achèvement, avec une application exécutée dans votre cluster. Plus d’informations sur les [définitions de version](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) Team Services.

### Créer une définition du modèle de version
1. Ouvrez votre projet dans Visual Studio Team Services.
2. Sélectionnez l’onglet **Version**.
3. Cliquez sur le signe **+** vert pour créer une définition de version, puis sélectionnez l’option **Créer une définition de mise en production** dans le menu.
4. Dans la boîte de dialogue qui s’ouvre, sélectionnez l’option **Déploiement d’Azure Service Fabric** dans la catégorie de modèle **Déploiement**.
5. Sélectionnez **Suivant**.
6. Sélectionnez la définition de build que vous souhaitez utiliser en tant que source de cette définition de version. La définition de version fait référence aux artefacts générés par la définition de build sélectionnée.
7. Sélectionnez la case à cocher **Déploiement continu** si vous souhaitez que Team Services crée automatiquement une version et déploie l’application Service Fabric chaque fois qu’une build se termine.
8. Sélectionnez la file d’attente de l’agent que vous souhaitez utiliser. Les agents hébergés sont pris en charge.
9. Sélectionnez **Créer**.
10. Modifiez le nom de la définition en cliquant sur l’icône en forme de crayon figurant sur la partie supérieure de la page.
11. Sélectionnez le cluster sur lequel votre application doit être déployée à partir du champ d’entrée **Cluster Connection** (Connexion au cluster) de la tâche. La connexion de cluster fournit les informations nécessaires, qui permettent à la tâche de déploiement de se connecter au cluster. Si vous n’avez pas encore configuré de connexion pour votre cluster, sélectionnez le lien hypertexte **Gérer** en regard du champ en ajouter un. Dans la page qui s’affiche, procédez comme suit :
    1. Sélectionnez **Nouveau point de terminaison du service**, puis **Azure Service Fabric** dans le menu.
    2. Sélectionnez le type d’authentification utilisé par le cluster que cible ce point de terminaison.
    3. Donnez un nom à votre connexion dans le champ **Nom de la connexion**. En général, le nom du cluster est utilisé.
    4. Définissez l’URL du point de terminaison de connexion client dans le champ **Point de terminaison de cluster**. Exemple : https://contoso.westus.cloudapp.azure.com:19000.
    5. En ce qui concerne les informations d’identification d’Azure Active Directory, définissez les informations à utiliser pour vous connecter au cluster dans les champs **Nom d’utilisateur** et **Mot de passe**.
    6. Pour l’authentification basée sur un certificat, définissez l’encodage Base64 du fichier de certificat client dans le champ **Certificat client**. Consultez l’aide contextuelle sur ce champ pour en savoir plus sur la façon d’obtenir cette valeur. Si votre certificat est protégé par mot de passe, définissez ce dernier dans le champ **Mot de passe**.
    7. Confirmez vos modifications en cliquant sur **OK**. Après avoir de nouveau accédé à votre définition de version, cliquez sur l’icône Actualiser figurant dans le champ **Cluster Connection** (Connexion au cluster) pour afficher le point de terminaison que vous venez d’ajouter.
12. Enregistrez la définition de version.

La définition créée se compose d’une tâche de type : **Déploiement d’applications Service Fabric**. Pour en savoir plus sur cette tâche, consultez la [page de documentation sur les tâches](https://go.microsoft.com/fwlink/?LinkId=820528).

### Vérification des valeurs par défaut du modèle
1. Vérifiez le champ d’entrée **Profil de publication** de la tâche **Déploiement de votre application Service Fabric**. Par défaut, ce champ fait référence à un profil de publication nommé Cloud.xml, qui est inclus dans les artefacts de la build. Si vous souhaitez faire référence à un autre profil ou si la build contient plusieurs packages d’applications dans ses artefacts, vous devez mettre à jour le chemin d’accès en conséquence.
2. Vérifiez le champ d’entrée **Package d’application** de la tâche **Déploiement de votre application Service Fabric**. Par défaut, ce champ fait référence au chemin d’accès au package d’application par défaut qui est utilisé dans le modèle de définition de la build. Si vous avez modifié le chemin d’accès au package d’application par défaut dans la définition de build, vous devez également mettre ce chemin à jour ici.

### Essayer
Sélectionnez **Créer une version** dans le menu du bouton **Version** pour créer manuellement une version. Dans la boîte de dialogue qui s’ouvre, sélectionnez la build sur laquelle baser la version, puis cliquez sur **Créer**. Si vous avez activé le déploiement continu, les versions sont créées automatiquement lorsque la définition de build associée termine une build.

## Étapes suivantes
Pour en savoir plus sur l’intégration continue avec les applications de Service Fabric, consultez les articles suivants :

* [Page d’accueil de la documentation relative à Team Services](https://www.visualstudio.com/docs/overview)
* [Page relative à la gestion des builds dans Team Services](https://www.visualstudio.com/docs/build/overview)
* [Page relative à la gestion des versions dans Team Services](https://www.visualstudio.com/docs/release/overview)

<!---HONumber=AcomDC_0803_2016-->