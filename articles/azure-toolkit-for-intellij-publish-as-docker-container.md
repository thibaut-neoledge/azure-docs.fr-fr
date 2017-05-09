---
title: Publier un conteneur Docker avec le kit de ressources Azure pour IntelliJ | Microsoft Docs
description: "Découvrez comment publier une application web sur Microsoft Azure en tant que conteneur Docker à l’aide du kit de ressources Azure pour IntelliJ."
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
ms.date: 04/14/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f8ae15f4a5edb3cd84b5bb2302e1a5f709b57f82
ms.contentlocale: fr-fr
ms.lasthandoff: 04/22/2017


---

# <a name="how-to-publish-a-web-app-as-a-docker-container-using-the-azure-toolkit-for-intellij"></a>Comment publier une application web en tant que conteneur Docker à l’aide du kit de ressources Azure pour IntelliJ

Les conteneurs Docker sont une méthode largement utilisée pour déployer des applications web dans lesquelles les développeurs peuvent regrouper tous leurs fichiers de projet et dépendances dans un seul package pour effectuer un déploiement sur un serveur. Le kit de ressources Azure pour IntelliJ simplifie ce processus pour les développeurs Java en ajoutant des fonctionnalités *Publish as Docker Container* (Publier en tant que conteneur Docker) pour le déploiement dans Microsoft Azure, et les étapes décrites dans cet article vous permettront de publier vos applications dans Azure en tant que conteneurs Docker.

> [!NOTE]
>
> Vous trouverez plus d’informations sur Docker sur le [site web Docker].
>

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Publication de votre application web dans Azure avec un conteneur Docker

> [!NOTE]
>
> Pour publier votre application web, vous devrez créer un artefact prêt pour le déploiement. Pour plus d’informations, consultez [Informations supplémentaires sur la création d’artefacts](#artifacts) plus loin dans cet article.
>
> En outre, une fois que vous avez terminé l’Assistant de déploiement au moins une fois, la plupart des paramètres que vous spécifiez lors de cette procédure seront utilisés par défaut lorsque vous réexécuterez l’Assistant.
>

1. Ouvrez votre projet d’application web dans IntelliJ.

1. Utilisez l’une des méthodes suivantes pour lancer l’Assistant Publish as Docker Container (Publier en tant que conteneur Docker) :

   * Cliquez avec le bouton droit sur votre projet dans la fenêtre de l’outil **Projet**, puis cliquez sur **Azure** et **Publish as Docker Container** (Publier en tant que conteneur Docker) :  ![Publier en tant que conteneur Docker][PUB01]

   * Cliquez sur le menu **Publish Group** (Publier un groupe) dans la barre d’outils IntelliJ, puis cliquez sur **Publish as Docker Container** (Publier en tant que conteneur Docker) :  ![Publier en tant que conteneur Docker][PUB02]

1. Lorsque l’Assistant **Deploy Docker Container on Azure** (Déployer le conteneur Docker dans Azure) s’affiche, vous verrez une boîte de dialogue similaire à la suivante : ![Assistant Deploy Docker Container on Azure][PUB03] (Déployer le conteneur Docker dans Azure)

   a. Entrez un nom unique dans la zone de texte pour votre hôte Docker dans le champ **Docker image name** (Nom de l’image Docker). (L’Assistant va créer automatiquement un nom pour vous, mais vous pouvez le modifier si vous voulez).

   b. La fenêtre **Hôtes** affiche tous les hôtes Docker que vous avez créés.
      * Si vous n’avez pas créé d’hôtes Docker, cette section de la boîte de dialogue est vide.
      * Si vous avez déjà créé des hôtes Docker, vous pouvez choisir de déployer votre application web sur un hôte existant ; sinon, suivez les étapes ci-dessous pour créer un hôte Docker.

1. Pour créer un hôte Docker, cliquez sur le signe plus de couleur verte (« **+** ») ; cela va lancer la boîte de dialogue **Create Docker Host** (Créer un hôte Docker).
      ![Assistant Deploy Docker Container on Azure (Déployer un conteneur Docker dans Azure)][PUB04a]

   a. Spécifiez les options suivantes pour votre hôte Docker. (L’Assistant génère automatiquement la plupart des options pour vous, mais vous pouvez modifier les options que vous souhaitez personnaliser).

      * **Nom** : il s’agit d’un nom unique pour l’hôte Docker. (Ce n’est pas le même que le nom de l’image Docker que vous avez spécifié précédemment.)

      * **Abonnement** : spécifie quel abonnement Azure vous utiliserez pour votre hôte.
      
      * **Région** : spécifie la région géographique dans laquelle sera votre hôte.
      
      * Dans l’onglet **OS and Size** (Système d’exploitation et taille) :
         * **Host OS** (Système d’exploitation de l’hôte) : spécifie le système d’exploitation de la machine virtuelle qui contiendra votre hôte.
         * **Taille** : spécifie la taille de la machine virtuelle de votre hôte.

      * Dans l’onglet **Groupe de ressources** :
         * **Nouveau groupe de ressources** : vous permet de créer un groupe de ressources pour votre hôte.
         * **Groupe de ressources existant** : vous permet de spécifier un groupe de ressources existant à partir de votre compte Azure.

      * Dans l’onglet **Réseau** :
         * **Nouveau réseau virtuel** : vous permet de créer un réseau virtuel pour votre hôte.
         * **Réseau virtuel existant** : vous permet de spécifier un réseau virtuel existant à partir de votre compte Azure.

      * Dans l’onglet **Stockage** :
         * **Nouveau compte de stockage** : vous permet de créer un compte de stockage pour votre hôte.
         * **Existing storage account** (Compte de stockage existant) : vous permet de spécifier un compte de stockage existant à partir de votre compte Azure.

   b. Après avoir indiqué les options ci-dessus, cliquez sur **Suivant**.

   c. Choisissez l’une des options suivantes pour la connexion de machine virtuelle de votre hôte Docker :    ![Create Docker Host][PUB05] (Créer un hôte Docker)

      * **Import credentials from Azure Key Vault** (Importer des informations d’identification Azure Key Vault) : vous permet de spécifier un jeu d’informations d’identification précédemment enregistré et stocké dans votre abonnement Azure.

      > [!NOTE]
      > Un coffre Azure Key Vault créé avec un compte ou un principal de service spécifique ne sera pas automatiquement accessible par un autre compte ou principal de service qui partage le même abonnement. Pour permettre à un autre compte ou principal de service d’utiliser le coffre Key Vault, vous devrez utiliser le portail Azure pour ajouter le compte ou le principal de service.

      * **New log in credentials** (Nouvelles informations d’identification) : vous permet de créer un jeu d’informations d’identification de connexion, ce qui vous obligera à spécifier les options suivantes dans l’onglet **VM Credentials** (Informations d’identification de machine virtuelle) :
         * **Nom d’utilisateur** : spécifie le nom d’utilisateur pour la connexion de votre machine virtuelle.
         * **Mot de passe** et **Confirmer** : spécifient le mot de passe pour vous connecter à votre machine virtuelle.
         * **SSH** : spécifie les paramètres SSH (Secure Shell) pour votre hôte Docker ; vous pouvez choisir parmi les options suivantes.
            * **None (Aucun)** : spécifie que votre machine virtuelle n’autorisera pas les connexions SSH.
            * **Générer automatiquement** : cette option permet de créer automatiquement les paramètres requis pour la connexion via le protocole SSH.
            * **Import from directory** (Importer à partir du répertoire) : vous permet de spécifier un répertoire qui contient un jeu de paramètres SSH précédemment enregistrés. Plus précisément, le répertoire doit contenir les deux fichiers suivants :
               * *id_rsa* : ce fichier contient l’identification RSA d’un utilisateur.
               * *id_rsa.pub*: ce fichier contient la clé publique RSA qui sera utilisée pour l’authentification.
        
      * Dans l’onglet **Docker Daemon Access** (Accès au démon Docker), spécifiez les options suivantes : ![Create Docker Host][PUB06] (Créer un hôte Docker)

         * **Docker Daemon port** (Port du démon Docker) : spécifie le port TCP unique pour votre hôte Docker.
         * **TLS Security** (Sécurité TLS) : spécifie les paramètres TLS (Transport Layer Security) de votre hôte Docker ; vous pouvez choisir parmi les options suivantes.
            * **None (Aucun)** : spécifie que votre machine virtuelle n’autorisera pas les connexions TLS.
            * **Générer automatiquement** : cette option permet de créer automatiquement les paramètres requis pour la connexion via le protocole TLS.
            * **Import from directory** (Importer à partir du répertoire) : vous permet de spécifier un répertoire qui contient un jeu de paramètres TLS précédemment enregistrés. Plus précisément, le répertoire doit contenir les six fichiers suivants :
               * *ca.pem* et *ca-key.pem* : ces fichiers contiennent le certificat et la clé publique de l’autorité de certificat TLS.
               * *cert.pem* et *key.pem* : ces fichiers contiennent le certificat client et la clé publique qui seront utilisés pour l’authentification TLS.
               * *server.pem* et *server-key.pem* : ces fichiers contiennent la clé publique et le certificat de serveur pour l’hôte.

   d. Après avoir indiqué toutes les options ci-dessus, cliquez sur **Terminer**.

1. Lorsque l’Assistant **Deploy Docker Container on Azure** (Déployer un conteneur Docker dans Azure) s’affiche à nouveau, cliquez sur **suivant**.
   ![Assistant Deploy Docker Container on Azure (Déployer un conteneur Docker dans Azure)][PUB07]

1. Sur la dernière page de l’Assistant, spécifiez les options suivantes :

   * **Docker container name** (Nom du conteneur Docker) : il s’agit du nom unique pour votre conteneur Docker.

   * Choisissez l’une des images Docker suivantes :

      * **Predefined Docker image** (Image Docker prédéfinie) : vous permet de spécifier une image Docker existante à partir d’Azure.

      > [!NOTE]
      > La liste d’images Docker dans ce menu déroulant se compose de plusieurs images que le kit de ressources Azure a configurées pour appliquer des correctifs afin que votre artefact soit automatiquement déployé.

      * **Custom Dockerfile** (Fichier Docker personnalisé) : vous permet de spécifier un fichier Docker précédemment enregistré à partir de votre ordinateur local.

      > [!NOTE]
      > Il s’agit d’une fonctionnalité plus avancée destinée aux développeurs qui souhaitent déployer leur propre fichier Docker. Toutefois, les développeurs qui utilisent cette option doivent s’assurer que leur fichier Docker est construit correctement. Puisque le kit de ressources Azure ne valide pas le contenu d’un fichier Docker personnalisé, le déploiement peut échouer si le fichier Docker présente des problèmes. En outre, le kit de ressource Azure s’attend à ce que le fichier Docker personnalisé contienne un artefact d’application web et tentera d’ouvrir une connexion HTTP. Si les développeurs publient un autre type d’artefact, ils peuvent recevoir des erreurs inoffensives après le déploiement.

   * **Paramètres du port** : spécifie la liaison de port TCP unique pour votre conteneur Docker.
   ![Assistant Deploy Docker Container on Azure (Déployer un conteneur Docker dans Azure)][PUB08]

1. Après avoir réalisé toutes les étapes ci-dessus, cliquez sur **Terminer**.

Le kit de ressources Azure commence le déploiement de votre application web dans Azure dans un conteneur Docker, et sauf si vous avez configuré IntelliJ pour effectuer un déploiement en arrière-plan, une boîte de dialogue apparaît, indiquant la progression du déploiement. 
![Progression du déploiement][PUB09]

<a name="artifacts"></a>
## <a name="additional-information-about-creating-artifacts"></a>Informations supplémentaires sur la création d’artefacts

Pour créer un artefact prêt pour le déploiement, procédez comme suit :

1. Ouvrez votre projet d’application web dans IntelliJ.

1. Cliquez sur **Fichier**, puis sur **Structure de projet**.
   ![Menu Structure de projet][ART01]

1. Cliquez sur le signe plus de couleur verte (« **+** ») pour ajouter un artefact, puis cliquez sur **Web Application: Artifact** (Application web : artefact).
   ![Ajouter un artefact][ART02]

1. Nommez votre artefact en veillant à ne pas ajouter l’extension « .war », puis cliquez sur **OK**.
   ![Propriétés de l’artefact][ART03]

Pour plus d’informations sur la création d’artefacts dans IntelliJ, consultez [Configuring Artifacts] (Configuration des artefacts) sur le site web JetBrains.

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur les boîtes à outils Azure pour les environnements de développement Java, consultez les liens suivants :

* [Kit de ressources Azure pour Eclipse]
  * [Nouveautés du kit de ressources Azure pour Eclipse]
  * [Installation du kit de ressources Azure pour Eclipse]
  * [Azure Sign In Instructions for the Azure Toolkit for Eclipse] (Instructions de connexion à Azure pour le kit de ressources Azure pour Eclipse)
  * [Créer une application web « Hello World » pour Azure dans Eclipse]
* [Kit de ressources Azure pour IntelliJ]
  * [Nouveautés du Kit de ressources Azure pour IntelliJ]
  * [Installation du kit de ressources Azure pour IntelliJ]
  * [Azure Sign In Instructions for the Azure Toolkit for IntelliJ] (Instructions de connexion à Azure pour le kit de ressources Azure pour IntelliJ)
  * [Créer une application web « Hello World » pour Azure dans IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure] et les [outils Java pour Visual Studio Team Services].

Pour obtenir des ressources supplémentaires pour Docker, consultez le [site web Docker].

<!-- URL List -->

[Kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application web « Hello World » pour Azure dans Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application web « Hello World » pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installation du kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Azure Sign In Instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md (Instructions de connexion à Azure pour le kit de ressources Azure pour Eclipse)
[Azure Sign In Instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md (Instructions de connexion à Azure pour le kit de ressources Azure pour IntelliJ)
[Nouveautés du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Nouveautés du Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[outils Java pour Visual Studio Team Services]: https://java.visualstudio.com/

[site web Docker]: https://www.docker.com/
[Configuring Artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html (Configuration des artefacts)

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB08.png
[PUB09]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB09.png

[ART01]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART01.png
[ART02]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART02.png
[ART03]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART03.png

