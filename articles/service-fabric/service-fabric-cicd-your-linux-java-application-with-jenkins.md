---
title: "Génération et déploiement en continu pour votre application Linux java Linux à l’aide de Jenkins | Microsoft Docs"
description: "Génération et déploiement en continu pour votre application Linux java Linux à l’aide de Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: raunakp
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: sayantancs
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d3c64e3ed78a5379592b5e4276c83d26f04d2e1c
ms.lasthandoff: 03/08/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Génération et déploiement pour votre application Linux java à l’aide de Jenkins
Jenkins est un outil populaire pour l’intégration continue et le déploiement. Dans ce document, nous vous guidons dans la création et le déploiement de votre application Service Fabric à l’aide de Jenkins.

## <a name="general-prerequisites"></a>Conditions préalables
1. Vous devez disposer de Git installé localement. Vous pouvez installer la version appropriée de Git depuis [ici](https://git-scm.com/downloads) en fonction de votre système d’exploitation. Si vous débutez avec Git, vous pouvez suivre les étapes présentées dans la [documentation](https://git-scm.com/docs) afin de vous familiariser avec Git.
2. Vous devez avoir le plug-in Jenkins de Service Fabric à portée de main. Téléchargez le plug-in Jenkins de Service Fabric depuis [ici](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Configuration de Jenkins à l’intérieur d’un Cluster Service Fabric

### <a name="prerequisites"></a>Composants requis
1. Avoir un cluster Linux Service Fabric de prêt. Docker doit être installé sur les Clusters Service Fabric créés à partir du Portail Azure. Si vous exécutez le cluster local, veuillez vérifier si Docker est installé ou non à l’aide de la commande ``docker info``. S’il n’est pas installé, installez-le à l’aide de :
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
2. Ayez déployé l’application conteneur de Service Fabric sur le cluster ``http://PublicIPorFQDN:19080``. Procédez comme suit :
```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker && cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080 # Azure CLI cluster connect command
bash Scripts/install.sh
```
  Cela installera le conteneur Jenkins dans le cluster que vous avez connecté. Vous pouvez analyser votre application conteneur à l’aide de Service Fabric Explorer.

### <a name="steps"></a>Étapes
1. Accédez à l’URL ``http://PublicIPorFQDN:8081`` à partir de votre navigateur. Il vous fournira le chemin d’accès du mot de passe administrateur initial requis pour se connecter. Vous pouvez continuer à utiliser Jenkins en tant qu’administrateur, ou vous pouvez créer et modifier l’utilisateur, une fois que vous êtes connecté avec le compte Administrateur initial.
> [!NOTE]
> Vous devez vérifier que le port 8081 est spécifié en tant que port de point de terminaison d’application lors de la création du cluster
>

2. Obtenez l’id d’instance conteneur à l’aide de ``docker ps -a``.
3. Connectez-vous en SSH au conteneur et collez le chemin d’accès indiqué sur le portail Jenkins. Par exemple, si dans le portail il indique le chemin d’accès `PATH_TO_INITIAL_ADMIN_PASSWORD`, vous pouvez effectuer :  
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash # This takes you inside docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Configurez GitHub pour fonctionner avec Jenkins en utilisant [ceci](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Suivez les instructions fournies à partir de GitHub pour générer la clé SSH et ajouter la clé SSH au compte GitHub qui héberge (ou devrait héberger) le référentiel.
    * Exécutez les commandes mentionnées dans le lien ci-dessus dans le shell Jenkins-Docker (et pas sur l’hôte)
    * Pour vous connecter au shell Jenkins de votre hôte, vous devez faire :
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Configuration de Jenkins à l’extérieur d’un Cluster Service Fabric

### <a name="prerequisites"></a>Composants requis
1. Vous devez disposer de Docker installé. Si ce n’est pas le cas, vous pouvez taper les commandes suivantes dans le terminal pour installer Docker.
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
Désormais, lorsque vous exécutez ``docker info`` dans le terminal, vous devriez voir dans la sortie que le service Docker est en cours d’exécution.

### <a name="steps"></a>Étapes
  1. Extraire le conteneur Jenkins de Service Fabric :``docker pull IMAGE_NAME ``
  2. Exécutez l’image de conteneur :``docker run -itd -p 8080:8080 IMAGE_NAME``
  3. Obtenez l’identifiant de votre conteneur Docker qui contient l’image Jenkins (que vous venez d’installer). Vous pouvez répertorier tous les conteneurs Docker avec la commande ``docker ps –a``
  4. Obtenez le mot de passe d’administrateur pour le compte Jenkins. Vous pouvez effectuer les opérations suivantes :
    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Ici, si l’ID de conteneur est 2d24a73b5964, vous devez insérer seulement 2d24
    * Ce mot de passe sera nécessaire pour vous connecter au tableau de bord Jenkins à partir du portail : ``http://<HOST-IP>:8080``
    * Lors de votre première connexion, vous pouvez créer votre propre compte d’utilisateur pour l’utiliser à l’avenir ou vous pouvez continuer à utiliser le compte d’administrateur. Une fois que vous créez un nouvel utilisateur, vous devez continuer ainsi.
  5. Configurez GitHub pour fonctionner avec Jenkins en utilisant [ceci](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * Suivez les instructions fournies à partir de GitHub pour générer la clé SSH et ajoutez la clé SSH au compte GitHub qui héberge (ou devrait héberger) le référentiel.
      * Exécutez les commandes mentionnées dans le lien ci-dessus dans le shell Jenkins-Docker (et pas sur l’hôte)
      * Pour vous connecter au shell Jenkins de votre hôte, vous devez faire :
    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

> [!NOTE]
> Vérifiez que le cluster / l’ordinateur où l’image de conteneur Jenkins est hébergée possède une adresse IP publique permettant la réception des notifications de GitHub par l’instance Jenkins.
>

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>Installez le Plug-in Jenkins de Service Fabric depuis le portail

1. Accédez à ``http://PublicIPorFQDN:8081``
2. Dans le tableau de bord Jenkins, sélectionnez ``Manage Jenkins`` -> ``Manage Plugins`` -> ``Advanced``.
Ici, vous pouvez charger un plug-in. Sélectionnez l’option ``Choose file``, puis sélectionnez le fichier serviceFabric.hpi, que vous avez téléchargé sous Conditions préalables. Une fois que vous sélectionnez le chargement, Jenkins va automatiquement installer le plug-in pour vous. Il peut également demander un redémarrage, veuillez accepter.

## <a name="creating-and-configuring-a-jenkins-job"></a>Création et configuration d’un travail Jenkins

1. Créer un ``new item`` à partir du tableau de bord
2. Entrez un nouveau nom d’élément - par exemple ``MyJob``, sélectionnez projet de forme libre, puis cliquez sur OK
3. Accédez à la page du travail, puis cliquez sur ``Configure`` -
  * Dans la section générale, sous ``Github project``, spécifiez l’URL de votre projet GitHub qui héberge l’application Java de Service Fabric que vous souhaitez intégrer avec le flux Jenkins CI/CD (par exemple, - ``https://github.com/sayantancs/SFJenkins``).
  * Dans la section ``Source Code Management``, sélectionnez ``Git``. Spécifiez l’URL du référentiel qui héberge l’application Java de Service Fabric que vous souhaitez intégrer avec le flux Jenkins CI/CD (par exemple, - ``https://github.com/sayantancs/SFJenkins.git``). Vous pouvez également spécifier ici la branche à générer, par exemple, - ``*/master``.
4. Configurer votre *Github* (c’est-à-dire qui héberge le référentiel) afin qu’il soit en mesure de communiquer avec Jenkins, en procédant comme suit :
  - Accédez à la page de votre référentiel GitHub. Accédez à ``Settings`` -> ``Integrations and Services``.
  - Sélectionnez ``Add Service``, tapez Jenkins, sélectionnez le ``Jenkins-Github plugin``.
  - Entrez votre URL du webhook Jenkins (par défaut, il doit être ``http://<PublicIPorFQDN>:8081/github-webhook/``). Cliquez sur Ajouter/Mettre à jour service.
  - Un événement de test est envoyé à votre instance Jenkins. Vous devez voir une coche verte à côté du webhook dans GitHub et votre projet sera généré !
  - Dans la section ``Build Triggers``, sélectionnez les options de génération que vous souhaitez. Pour ce cas de figure, nous prévoyons de déclencher une génération chaque fois qu’un envoi vers le référentiel se produit. L’option correspondante est ``GitHub hook trigger for GITScm polling`` (elle était auparavant « générer lorsqu’une modification est envoyée au GitHub »).
  - Sous le ``Build section`` : dans la liste déroulante ``Add build step``, sélectionnez l’option ``Invoke Gradle Script``. Dans le widget qui apparaît, spécifiez le chemin d’accès ``Root build script``, pour votre application. Il récupère le Gradle de la génération à partir du chemin spécifié et fonctionne en conséquence. Notez que - si vous créez un projet nommé ``MyActor``(à l’aide du plug-in Eclipse ou Yeoman generator), alors le script de compilation racine doit contenir - ``${WORKSPACE}/MyActor``. Par exemple, cette section ressemble principalement à :

    ![Action de génération Jenkins de Service Fabric][build-step]
  - Dans la liste déroulante ``Post-Build Actions``, sélectionnez ``Deploy Service Fabric Project``. Ici, vous devez fournir des détails de cluster où l’application Service Frabric compilée par Jenkins est déployée ainsi que des détails d’application supplémentaires utilisés pour déployer l’application. La capture d’écran suivante peut être utilisée comme référence :

    ![Action de génération Jenkins de Service Fabric][post-build-step]

 >[!Note]
 > Le cluster ici peut être identique à celui qui héberge l’application de conteneur Jenkins dans le cas où vous utilisez Service Fabric pour déployer l’image de conteneur Jenkins.
 >

### <a name="end-to-end-scenario"></a>Scénario de bout en bout
À ce stade, le GitHub et Jenkins sont configurés et vous pouvez alors faire des modifications dans votre projet ``MyActor`` dans le référentiel, par exemple https://github.com/sayantancs/SFJenkins et les envoyer à une branche ``master`` distante (ou à n’importe quelle branche avec laquelle vous pouvez travailler). Cela déclenche alors le travail Jenkins ``MyJob`` que vous avez configuré. Cela va donc - extraire les modifications à partir de GitHub, de les générer et de déployer l’application sur le point de terminaison du cluster spécifié dans les actions après génération.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

