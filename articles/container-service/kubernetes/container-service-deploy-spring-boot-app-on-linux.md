---
title: "Déployer une application web Spring Boot sur Linux dans Azure Container Service | Microsoft Docs"
description: "Ce didacticiel vous guide à travers les étapes pour déployer une application Spring Boot en tant qu’application web Linux sur Microsoft Azure."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: asirveda;robmcm
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 6035b0e2fe27b46c441012144caf545eb17e1825
ms.contentlocale: fr-fr
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-a-spring-boot-application-on-linux-in-azure-container-service"></a>Déployer une application Spring Boot sur Linux dans Azure Container Service

L’[infrastructure Spring] est une solution open source qui aide les développeurs Java à créer des applications d’entreprise. Un des projets les plus connus basés sur Java est [Spring Boot], qui fournit une approche simplifiée pour la création d’applications Java autonomes.

[client Docker] est une solution open source qui aide les développeurs à automatiser le déploiement, la mise à l’échelle et la gestion des applications qui s’exécutent dans des conteneurs.

Ce didacticiel vous indique comment utiliser Docker pour développer et déployer une application Spring Boot sur un hôte Linux dans [Azure Container Service].

## <a name="prerequisites"></a>Composants requis

Pour effectuer les étapes de ce didacticiel, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN] ou créer un [compte Azure gratuit].
* [Azure CLI].
* Un [JDK (Java Developer Kit)] à jour.
* L’outil de génération [Maven] (version 3) d’Apache.
* Un [client Git].
* Un [client Docker].

> [!NOTE]
>
> En raison des exigences de virtualisation de ce didacticiel, vous ne pouvez pas suivre les étapes décrites dans cet article sur une machine virtuelle. À la place, vous devez utiliser un ordinateur physique sur lequel les fonctionnalités de virtualisation sont activées.
>

## <a name="create-the-spring-boot-on-the-docker-getting-started-web-app"></a>Créer l’application web Spring Boot on the Docker Getting Started

La procédure suivante vous aide à créer une application web Spring Boot simple et à la tester localement.

1. Ouvrez une invite de commandes. Ensuite, créez un répertoire local pour y stocker votre application, puis accédez à ce répertoire. Par exemple :
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   - ou -
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

2. Clonez l’exemple de projet [Spring Boot on Docker Getting Started] dans le répertoire que vous venez de créer. Par exemple :
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

3. Accédez au répertoire du projet terminé. Par exemple :
   ```
   cd gs-spring-boot-docker/complete
   ```

4. **Étape facultative** : Si vous voulez exécuter le serveur Tomcat incorporé sur le port 80 au lieu du port par défaut 8080 (par exemple si vous voulez tester votre projet Spring Boot localement), configurez le port en effectuant les étapes suivantes :

   a. Accédez au répertoire des ressources. Par exemple :
   ```
   cd src/main/resources
   ```

   b. Ouvrez le fichier **application.yml** dans un éditeur de texte.

   c. Modifiez le paramètre **server:** pour que le serveur s’exécute sur le port 80. Par exemple :
   ```
   server:
      port: 80
   ```

   d. Enregistrez et fermez le fichier **application.yml**.

   e. Revenez au dossier racine du projet terminé. Par exemple :
   ```
   cd ../../..
   ```

5. Générez le fichier JAR en utilisant Maven. Par exemple :
   ```
   mvn package
   ```

6. Une fois l’application web créée, accédez au répertoire `target` où se trouve fichier JAR, puis démarrez l’application web. Par exemple :
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

7. Testez l’application web en y accédant localement avec un navigateur web. Par exemple, vous pouvez utiliser la commande suivante si curl est disponible et que vous avez configuré le serveur Tomcat pour qu’il s’exécute sur le port 80 :
   ```
   curl http://localhost
   ```

8. Vous devez normalement voir le message suivant : **Hello Docker World!**

   ![Parcourir l’exemple d’application en local][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Créer un registre de conteneurs Azure à utiliser comme registre Docker privé

Les étapes suivantes vous indiquent comment utiliser le portail Azure pour créer un registre de conteneurs Azure.

> [!NOTE]
> Si vous souhaitez utiliser Azure CLI au lieu du portail Azure, suivez les étapes décrites dans [Créer un registre de conteneurs Docker privé à l’aide d’Azure CLI 2.0][1].

1. Connectez-vous au [portail Azure].

    Une fois connecté à votre compte sur le portail Azure, suivez les étapes décrites dans [Créez un registre de conteneur Docker privé à l’aide du portail Azure]. Les étapes décrites dans cet article sont les suivantes :

2. Sélectionnez l’icône de menu pour **+ Nouveau**.

3. Sélectionnez **Conteneurs**, puis sélectionnez **Azure Container Registry**.

   ![Créer un registre de conteneurs Azure][AR01]

4. Quand la page d’informations pour le modèle de registre de conteneurs Azure s’affiche, cliquez sur **Créer**.

   ![Créer un registre de conteneurs Azure][AR02]

5. Quand le panneau **Créer un Registre de conteneurs** s’affiche :

   a. Entrez vos **Nom du Registre** et **Groupe de ressources**.  
   
   b. Sélectionnez **Activer** pour **l’Utilisateur administrateur**.
   
   c. Sélectionnez **Créer**.

   ![Configurer les paramètres du registre de conteneurs Azure][AR03]

6. Une fois votre registre de conteneurs créé, accédez à ce dernier dans le portail Azure. Ensuite, sélectionnez **Clés d’accès**. Notez le nom d’utilisateur et le mot de passe pour les étapes suivantes.

   ![Clés d’accès du registre de conteneurs Azure][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Configurer Maven pour utiliser les clés d’accès de votre registre de conteneurs Azure

1. Accédez au répertoire de configuration de votre installation Maven. Ensuite, ouvrez le fichier **settings.xml** avec un éditeur de texte.

2. Ajoutez les paramètres d’accès de votre registre de conteneurs Azure de la section précédente de ce didacticiel à la collection `<servers>` dans le fichier **settings.xml**. Par exemple :

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

3. Accédez au répertoire de projet terminé de votre application Spring Boot (par exemple, **C:\SpringBoot\gs-spring-boot-docker\complete** ou **/users/robert/SpringBoot/gs-spring-boot-docker/complete**). Ensuite, ouvrez le fichier **pom.xml** avec un éditeur de texte.

1. Mettez à jour la collection `<properties>` dans le fichier **pom.xml**. Utilisez la valeur du serveur de connexion pour votre registre de conteneurs Azure de la section précédente de ce didacticiel. Par exemple :

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Mettez à jour la collection `<plugins>` dans le fichier **pom.xml** de façon à ce que le `<plugin>` contienne l’adresse du serveur de connexion et le nom de registre de votre registre de conteneurs Azure de la section précédente de ce didacticiel. Par exemple :

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Accédez au répertoire du projet terminé pour votre application Spring Boot. Ensuite, exécutez la commande suivante pour regénérer l’application et envoyer (push) le conteneur au registre de conteneurs Azure :

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
> Quand vous placez votre conteneur Docker dans Azure, vous pouvez recevoir un message d’erreur similaire à un de ceux-ci, même si vous avez créé votre conteneur Docker correctement :
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> Dans ce cas, vous pouvez être amené à vous connecter à Azure à partir de la ligne de commande de Docker. Par exemple :
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Vous pouvez ensuite placer votre conteneur à partir de la ligne de commande. Par exemple :
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-by-using-your-container-image"></a>Créer une application web sur Linux sur Azure App Service en utilisant votre image conteneur

1. Connectez-vous au [portail Azure].

1. Cliquez sur l’icône de menu pour **+ Nouveau**, puis sur **Web + Mobile**.

2.  Cliquez sur **Application web sur Linux**.

   ![Créer une application web dans le portail Azure][LX01]

3. Quand le panneau **Application web sur Linux** s’affiche, effectuez les étapes suivantes :

   a. Entrez un nom unique pour le **Nom de l’application**, par exemple : *wingtiptoyslinux*.

   b. Choisissez votre **abonnement** dans la liste déroulante.

   c. Pour créer un groupe de ressources, choisissez un **Groupe de ressources** existant ou spécifiez un nom.

   d. Cliquez sur **Configurer le conteneur**, puis entrez les informations suivantes :

      * Choisissez **Registre privé**.

      * **Image et étiquette facultative** : spécifiez le nom de votre conteneur utilisé plus haut, par exemple : *wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*.

      * **URL du serveur** : spécifiez l’URL du registre déclaré plus haut, par exemple : *https://wingtiptoysregistry.azurecr.io*.

      * **Nom d’utilisateur de connexion** et **Mot de passe** : spécifiez vos informations d’identification de connexion des **clés d’accès** que vous avez utilisées dans les étapes précédentes.

   e. Après avoir entré toutes les informations précédentes, sélectionnez **OK**.

   ![Configurer les paramètres de l’application web][LX02]

1. Cliquez sur **Create**.

> [!NOTE]
> Azure mappe automatiquement les demandes Internet au serveur Tomcat incorporé qui s’exécute sur les ports standard 80 ou 8080. Cependant, si vous avez configuré votre serveur Tomcat incorporé pour qu’il s’exécute sur un port personnalisé, vous devez ajouter une variable d’environnement à votre application web qui définit le port pour votre serveur Tomcat incorporé. Pour ce faire, effectuez les étapes suivantes :
>
>1. Connectez-vous au [portail Azure].

>2. Sélectionnez l’icône pour **App Services**. (Consultez l’élément #1 dans l’image suivante.)

>3. Sélectionnez votre application web dans la liste. (Consultez l’élément #2 dans l’image suivante.)

>4. Cliquez sur **Paramètres de l’application**. (Consultez l’élément #3 dans l’image suivante.)

>5. Dans la section **Paramètres de l’application**, ajoutez une nouvelle variable d’environnement nommée **PORT**. Ensuite, en guise de valeur, entrez votre numéro de port personnalisé. (Consultez l’élément #4 dans l’image suivante.)

>6. Sélectionnez **Enregistrer**. (Consultez l’élément #5 dans l’image suivante.)

> ![Enregistrement d’un numéro de port personnalisé dans le portail Azure][LX03]
>

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’applications Spring Boot sur Azure, consultez les articles suivants :

- [Déployer une application Spring Boot sur Azure App Service][2]


- [Exécution d’une application Spring Boot sur un cluster Kubernetes dans Azure Container Service](container-service-deploy-spring-boot-app-on-kubernetes.md)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure] et les [outils Java pour Visual Studio Team Services].

Pour plus d’informations sur l’exemple de projet Spring Boot sur Docker, consultez [Spring Boot on Docker getting started].

Pour de l’aide sur la mise en route de vos propres applications Spring Boot, consultez [Spring Initializr](https://start.spring.io/).

Pour plus d’informations sur la création d’une application Spring Boot simple, consultez [Spring Initializr](https://start.spring.io/).

Pour obtenir des exemples supplémentaires illustrant l’utilisation d’images Docker personnalisées avec Azure, consultez [Comment utiliser une image Docker personnalisée pour Azure Web App sur Linux].

<!-- URL List -->

[Azure CLI]: /cli/azure/overview
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[portail Azure]: https://portal.azure.com/
[Créez un registre de conteneur Docker privé à l’aide du portail Azure]: /azure/container-registry/container-registry-get-started-portal
[Comment utiliser une image Docker personnalisée pour Azure Web App sur Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[client Docker]: https://www.docker.com/
[compte Azure gratuit]: https://azure.microsoft.com/pricing/free-trial/
[client Git]: https://github.com/
[JDK (Java Developer Kit)]: http://www.oracle.com/technetwork/java/javase/downloads/
[outils Java pour Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[avantages abonnés MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[infrastructure Spring]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png

<!--Reference links in article-->
[1]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli/
[2]: https://docs.microsoft.com/azure/app-service/app-service-deploy-spring-boot-web-app-on-azure/


---

