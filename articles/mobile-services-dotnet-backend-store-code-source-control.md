<properties 
	pageTitle="Stockage du code d'un projet dans le contrôle du code source - Azure Mobile Services" 
	description="Découvrez comment stocker votre projet .NET principal dans un référentiel Git local sur votre ordinateur et à le publier depuis cet emplacement." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/5/2014" 
	ms.author="glenga"/>

<div class="dev-center-tutorial-subselector">
	<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/" title=".NET backend" class="current">.NET principal</a> | <a href="/fr-fr/documentation/articles/mobile-services-store-scripts-source-control/"  title="JavaScript backend">JavaScript principal</a>
</div>

# Stockage du code d'un projet dans le contrôle du code source

Cette rubrique montre comment utiliser le contrôle de code source fourni par Azure Mobile Services pour stocker votre projet de service principal .NET. Vous pouvez publier votre projet en le téléchargeant simplement à partir de votre référentiel Git local dans votre service mobile de production. 

Ce didacticiel vous accompagne tout au long des étapes suivantes :

1. [Activation du contrôle de code source dans votre service mobile]
2. [Installation de Git et création du référentiel local]
3. [Publication du projet à l'aide de Git]

Pour suivre ce didacticiel, vous devez avoir créé un service mobile en suivant le didacticiel [Prise en main de Mobile Services] ou [Ajout de Mobile Services à une application existante].

##<a name="enable-source-control"></a>Activation du contrôle de code source dans votre service mobile

[AZURE.INCLUDE [mobile-services-enable-source-control](../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Installation de Git et création du référentiel local

1. Installez Git sur votre ordinateur local. 

	La procédure requise pour installer Git diffère selon les systèmes d'exploitation. Consultez la rubrique [Installation de Git] pour accéder aux distributions et consignes d'installation propres aux différents systèmes d'exploitation.

	> [AZURE.NOTE]
	> Sur certains systèmes d'exploitation, une version en ligne de commande et une version avec interface utilisateur graphique sont toutes deux disponibles. Les instructions fournies dans cet article utilisent la version en ligne de commande.

2. Ouvrez une ligne de commande, telle que **GitBash** (Windows) ou **Bash** (Unix Shell). Sur les systèmes OS X, vous pouvez accéder à la ligne de commande via l'application **Terminal**.

3. À partir de la ligne de commande, accédez au répertoire dans lequel vous allez stocker vos scripts. Par exemple, `cd SourceControl`.

4. Utilisez la commande suivante pour créer une copie locale de votre nouveau référentiel Git, en remplaçant `<your_git_URL>` par l'URL du référentiel Git pour votre service mobile :

		git clone <your_git_URL>

5. Lorsque vous y êtes invité, tapez le nom d'utilisateur et le mot de passe que vous avez définis lorsque vous avez activé le contrôle de code source dans votre service mobile. À l'issue de l'authentification, une série de réponses similaires à ce qui suit s'affiche :

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Accédez au répertoire à partir duquel vous avez exécuté la commande  `git clone` et notez qu'un nouveau répertoire est créé avec le nom du service mobile. Pour un service mobile principal .NET, le référentiel git est initialement vide. 

À présent que vous avez créé votre référentiel local, vous pouvez publier votre projet de service principal .NET à partir de ce référentiel.

##<a name="deploy-scripts"></a>Publication du projet à l'aide de Git

1. Créez un nouveau projet de service mobile principal .NET dans Visual Studio 2013 ou déplacez un projet existant dans votre nouveau référentiel local.  

	Pour effectuer un test rapide, téléchargez et enregistrez le projet de démarrage rapide Mobile Services dans ce dossier.

2. Supprimez les dossiers de package NuGet en conservant le fichier packages.config.

	Mobile Services restaure automatiquement vos packages NuGet basés sur le fichier packages.config. Vous pouvez également définir un fichier .gitignore pour empêcher l'ajout de répertoires du package. 
 
3. Dans l'invite de commandes Git, tapez la commande suivante pour commencer le suivi du nouveau fichier de script :

		$ git add .
	
4. Tapez la commande suivante pour valider les modifications :

		$ git commit -m "adding the .NET backend service project"

5. Tapez la commande suivante pour télécharger les modifications vers le référentiel distant, et fournissez vos informations d'identification :

		$ git push origin master
	
	Vous devez voir une série de commandes qui indique que le projet a été déployé sur Mobile Services, que les packages ont été ajoutés et que le service a été redémarré.

6. Accédez à l'URL de votre service mobile principal .NET. Vous devez voir les éléments suivants :

	![Mobile Services startup page](./media/mobile-services-dotnet-backend-store-code-source-control/mobile-service-startup.png)

À présent, votre projet de service mobile est conservé dans le contrôle de code source et vous pouvez publier des mises à jour de service en les envoyant simplement (Push) à partir de votre référentiel local. Pour plus d'informations sur l'apport de modifications aux modèles de données dans un service mobile principal .NET qui utilise une base de données SQL, consultez [Modifications des modèles de données pour un service mobile principal .NET].

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, vous savez comment stocker vos scripts dans un contrôle de code source. Envisagez d'en savoir plus sur les services mobiles : 

+ [Modifications des modèles de données pour un service mobile principal .NET]
	<br/> Montre comment utiliser Entity Framework Code First Migrations pour apporter des modifications de modèles de données à une base de données SQL existante, afin d'éviter de perdre les données existantes. 	

+ [Appel d'une API personnalisée à partir du client] 
	<br/> Présente la création d'API personnalisées pouvant être appelées à partir du client.

<!-- Anchors. -->
[Activation du contrôle de code source dans votre service mobile]: #enable-source-control
[Installation de Git et création du référentiel local]: #clone-repo
[Publication du projet à l'aide de Git]: #deploy-scripts

<!-- Images. -->

<!-- URLs. -->
[Site web Git]: http://git-scm.com
[Contrôle de code source]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installation de Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Ajout de Mobile Services à une application existante]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Appel d'une API personnalisée à partir du client]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api/
[Modifications des modèles de données pour un service mobile principal .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations



<!--HONumber=42-->
