<properties 
	pageTitle="Créer une application web « Hello World » pour Azure dans Eclipse" 
	description="Ce didacticiel vous montre comment utiliser le Kit de ressources Azure pour Eclipse pour créer une application Web Hello World pour Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="05/04/2016" 
	ms.author="robmcm"/>

# Créer une application web « Hello World » pour Azure dans Eclipse

Ce didacticiel explique comment créer une application Hello World de base et la déployer sur Azure en tant qu’application web à l’aide du [Kit de ressources Azure pour Eclipse]. Un exemple JSP de base est présenté par souci de simplicité, mais des étapes très similaires conviennent également pour un servlet Java en ce qui concerne le déploiement d’Azure.

À la fin de ce didacticiel, votre application ressemble à l’illustration suivante quand vous l’affichez dans un navigateur web :

![][01]
 
## Composants requis

* JDK (Java Development Kit) version 1.7 ou ultérieure.
* IDE (environnement de développement intégré) Eclipse pour développeurs Java EE, Indigo ou ultérieur, Vous pouvez le télécharger à partir de <http://www.eclipse.org/downloads/>.
* Une distribution d’un serveur web ou d’un serveur d’applications basé sur Java, comme Apache Tomcat ou Jetty.
* Un abonnement à Azure, pouvant être souscrit à l’adresse <https://azure.microsoft.com/fr-FR/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* Kit de ressources Azure pour Eclipse. Pour plus d’informations, consultez [Installation du kit de ressources Azure pour Eclipse].

## Pour créer une application Hello World

Tout d’abord, nous allons commencer par créer un projet Java.

1. Démarrez Eclipse, puis, dans le menu, cliquez successivement sur **Fichier**, **Nouveau** et **Projet web dynamique**. (Si vous ne voyez pas **Dynamic Web Project** répertorié en tant que projet disponible après avoir cliqué sur **File** et **New**, procédez comme suit : cliquez sur **File**, cliquez sur **New**, sur **Project...**, développez **Web**, puis cliquez sur **Dynamic Web Project**, puis sur **Next**.)
1. Pour l’exemple de ce didacticiel, nommez le projet **MyHelloWorld**. Votre écran se présente comme suit : ![][02]
1. Cliquez sur **Terminer**.
1. Dans la vue Explorateur de projets d’Eclipse, développez **MyHelloWorld**. Cliquez avec le bouton droit sur **WebContent**, cliquez sur **New**, puis sur **JSP File**.
1. Dans la boîte de dialogue **New JSP File** (Nouveau fichier JSP), nommez le fichier **index.jsp**. Conservez **MyHelloWorld/WebContent** comme dossier parent.
1. Pour les besoins de ce didacticiel, dans la boîte de dialogue **Select JSP Template** (Sélectionner le modèle JSP), sélectionnez **New JSP File (html)** (Nouveau fichier JSP (html)) et cliquez sur **Finish** (Terminer).
1. Quand votre fichier index.jsp s’ouvre dans Eclipse, ajoutez un texte pour afficher dynamiquement **Hello World!** dans l’élément `<body>` existant. Le contenu `<body>` mis à jour doit ressembler à l’exemple suivant : `<body><b><% out.println("Hello World!"); %></b></body>` 
1. Enregistrez index.jsp.

## Pour déployer votre application sur un conteneur d’application web Azure

Vous pouvez déployer une application web Java sur Azure de plusieurs façons. Ce didacticiel décrit l’une des plus simples : votre application est déployée sur un conteneur d’application web Azure ; ainsi, aucun type de projet spécifique ni outil supplémentaire n’est nécessaire. Le JDK et le logiciel du conteneur web vous étant fournis par Azure, vous n’avez pas besoin de charger les vôtres ; vous devez uniquement être en possession de votre application web Java. Ainsi, le processus de publication de votre application ne prend que quelques secondes.

1. Dans l’Explorateur de projets d’Eclipse, cliquez avec le bouton droit sur **MyHelloWorld**.

1. Dans le menu contextuel, sélectionnez **Azure**, puis cliquez sur **Publish as Azure Web App...**

   ![][03]
   
   Ou bien, pendant que votre projet d’application web est sélectionné dans l’Explorateur de projets, vous pouvez cliquer sur le bouton de liste déroulante **Publier** sur la barre d’outils et sélectionner **Publish as Azure Web App** à partir cet emplacement :
   
   ![][publishDropdownButton]
   
1. Si vous n’êtes pas encore connecté à Azure à partir d’Eclipse, vous serez invité à vous connecter à votre compte Azure :

   ![][04]
   
   Remarque : si vous avez plusieurs comptes Azure, certaines des invites du processus de connexion peuvent s’afficher plusieurs fois, même si elles semblent être identiques. Dans ce cas, continuez à suivre les instructions de connexion.
1. Une fois que vous êtes connecté à votre compte Azure, la boîte de dialogue **Gérer les abonnements** affiche la liste des abonnements associés à vos informations d’identification. Si plusieurs abonnements sont répertoriés et que vous ne souhaitez utiliser qu’une partie d’entre eux, vous pouvez éventuellement désélectionner ceux qui ne vous intéressent pas. Quand vous avez sélectionné vos abonnements, cliquez sur **Fermer**.

   ![][05]
   
1. Quand la boîte de dialogue **Deploy to Azure Web App Container** s’affiche, elle présente tous les conteneurs d’application web déjà créés ; si vous n’avez pas créé de conteneur, la liste est vide.

   ![][06]
   
1. Si vous n’avez pas déjà créé de conteneur d’application web Azure ou que vous souhaitez publier votre application dans un nouveau conteneur, procédez comme suit. Sinon, sélectionnez un conteneur d’application web existant et passez à l’étape 7 ci-dessous.

  1. Cliquez sur **New...**

  1. La boîte de dialogue **New Web App Container** s’affiche :

        ![][07]

  1. Entrez un **nom DNS** pour votre conteneur d’application web ; celui-ci constitue le nom DNS feuille de l’URL hôte de votre application web dans Azure. Remarque : Le nom doit être disponible et conforme aux exigences d’affectation de noms pour les applications web Azure.

  1. Dans le menu déroulant **Web Container**, sélectionnez le logiciel approprié pour votre application.

        Currently, you can choose from Tomcat 8, Tomcat 7 or Jetty 9. A recent distribution of the selected software will be provided by Azure, and it will run on a recent distribution of JDK 8 created by Oracle and provided by Azure.

  1. Dans le menu déroulant **Subscription**, sélectionnez l’abonnement à utiliser pour ce déploiement.

  1. Dans le menu déroulant **Resource Group**, sélectionnez le groupe de ressources auquel vous souhaitez associer votre application web.

        Note: Azure Resource Groups allow you to group related resources together so that, for example, they can be deleted together.

        You can select an existing Resource Group (if you have any) and skip to step g below, or use the following these steps to create a new Resource Group:

      * Cliquez sur **New...**

      * La boîte de dialogue **New Resource Group** s’affiche :

            ![][08]

      * Dans la zone de texte **Name**, spécifiez un nom pour votre nouveau groupe de ressources.

      * Dans le menu déroulant **Region**, sélectionnez l’emplacement de centre de données Azure approprié pour votre groupe de ressources.

      * Cliquez sur **OK**.

  1. Le menu déroulant **App Service Plan** répertorie les plans de service d’application qui sont associés au groupe de ressources que vous avez sélectionné.

        Note: An App Service Plan specifies information such as the location of your Web App, the pricing tier and the compute instance size. A single App Service Plan can be used for multiple Web Apps, which is why it is maintained separately from a specific Web App deployment.

        You can select an existing App Service Plan (if you have any) and skip to step h below, or use the following these steps to create a new App Service Plan:

      * Cliquez sur **New...**

      * La boîte de dialogue **New App Service Plan** s’affiche :

            ![][09]

      * Dans la zone de texte **Name**, spécifiez un nom pour votre nouveau plan de service d’application.

      * Dans le menu déroulant **Location**, sélectionnez l’emplacement de centre de données Azure approprié pour le plan.

      * Dans le menu déroulant **Pricing Tier**, sélectionnez la tarification appropriée pour le plan. À des fins de test, vous pouvez choisir **Free**.

      * Dans le menu déroulant **Instance Size**, sélectionnez la taille d’instance appropriée pour le plan. À des fins de test, vous pouvez choisir **Small**.

  1. Une fois effectuées toutes les étapes ci-dessus, la boîte de dialogue New Web App Container doit ressembler à ceci :

        ![][10]

  1. Cliquez sur **OK** pour terminer la création de votre conteneur d’application web.

        Wait a few seconds for the list of the Web App containers to be refreshed, and your newly-created web app container should now be selected in the list.

1. Vous êtes maintenant prêt à lancer le déploiement initial de votre application web sur Azure :

    ![][11]

    Cliquez sur **OK** pour déployer votre application Java sur le conteneur d’application web sélectionné.

    Remarque : Par défaut, votre application est déployée en tant que sous-répertoire du serveur d’applications. Si vous voulez qu’elle soit déployée en tant qu’application racine, cochez la case **Deploy to root** avant de cliquer sur **OK**.

1. Ensuite, la vue **Azure Activity Log** doit apparaître, indiquant l’état du déploiement de votre application web.

    ![][12]

    Le processus de déploiement de votre application web sur Azure doit prendre seulement quelques secondes. Quand votre application est prête, un lien nommé **Published** apparaît dans la colonne **Status**. Quand vous cliquez sur le lien, vous êtes redirigé vers la page d’accueil de votre application web déployée.

## Mise à jour de votre application web

La mise à jour d’une application web Azure existante en cours d’exécution est un processus simple et rapide, que vous pouvez effectuer de deux façons :

* Vous pouvez mettre à jour le déploiement d’une application web Java existante.
* Vous pouvez publier une application Java supplémentaire dans le même conteneur d’application web.

Dans les deux cas, le processus est identique et ne prend que quelques secondes :

1. Dans l’Explorateur de projets Eclipse, cliquez avec le bouton droit sur l’application Java que vous souhaitez mettre à jour ou ajouter à un conteneur d’application web existant.

2. Quand le menu contextuel apparaît, sélectionnez **Azure**, puis **Publish as Azure Web App...**.

3. Comme vous vous êtes déjà connecté, la liste de vos conteneurs d’application web existants apparaît. Sélectionnez celui dans lequel vous souhaitez publier ou republier votre application Java, puis cliquez sur **OK**.

Quelques secondes plus tard, la vue **Azure Activity Log** associe l’état **Published** à votre déploiement mis à jour, et vous pouvez vérifier dans un navigateur web que votre application a été correctement mise à jour.

## Arrêt d’une application web existante

Pour arrêter un conteneur d’application web Azure existant, (y compris toutes les applications Java déployées dans celui-ci), vous pouvez utiliser la vue **Azure Explorer**.

Si la vue **Azure Explorer** n’est pas déjà ouverte, procédez comme suit : cliquez sur **Window** dans le menu d’Eclipse, puis cliquez successivement sur **Show View**, **Other...**, **Azure** et **Azure Explorer**. Si vous ne vous êtes pas déjà connecté, vous êtes invité à le faire.

Quand **Azure Explorer** s’affiche, procédez comme suit pour arrêter votre application web :

1. Développez le nœud **Azure**.
2. Développez le nœud **Web Apps**. 
3. Cliquez avec le bouton droit sur l’application web souhaitée.
5. Quand le menu contextuel s’affiche, cliquez sur **Stop**. ![][13]

## Étapes suivantes

Pour plus d’informations, consultez les liens suivants :

* [Centre de développement Java](/develop/java/).
* [Vue d’ensemble de Web Apps](app-service-web-overview.md)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Installation du kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[01]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/01-Web-Page.png
[02]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/02-Dynamic-Web-Project.png
[03]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/03-Context-Menu.png
[04]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/04-Log-In-Dialog.png
[05]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/05-Manage-Subscriptions-Dialog.png
[06]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/07-New-Web-App-Container-Dialog.png
[08]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/08-New-Resource-Group-Dialog.png
[09]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/09-New-Service-Plan-Dialog.png
[10]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/11-Completed-Deploy-Dialog.png
[12]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/12-Activity-Log-View.png
[13]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/13-Azure-Explorer-Web-App.png
[publishDropdownButton]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/publishDropdownButton.png

<!---HONumber=AcomDC_0504_2016-->