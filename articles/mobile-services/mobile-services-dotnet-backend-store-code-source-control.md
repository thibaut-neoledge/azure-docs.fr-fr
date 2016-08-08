<properties
	pageTitle="Stocker le code de votre projet de backend .NET dans le contrôle de code source | Azure Mobile Services"
	description="Découvrez comment stocker votre projet de backend .NET dans un référentiel Git local sur votre ordinateur et à le publier depuis cet emplacement."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>

# Stocker le code de votre projet de service mobile dans le contrôle de code source

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [Backend .NET](mobile-services-dotnet-backend-store-code-source-control.md)
- [Back-end Javascript](mobile-services-store-scripts-source-control.md)

Cette rubrique montre comment utiliser le contrôle de code source fourni par Azure Mobile Services pour stocker votre projet de service de backend .NET. Vous pouvez publier votre projet en le téléchargeant simplement à partir de votre référentiel Git local dans votre service mobile de production.

Pour suivre ce didacticiel, vous devez avoir créé un service mobile au moyen du didacticiel [Prise en main de Mobile Services].

##<a name="enable-source-control"></a>Activer le contrôle de code source dans votre service mobile

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Installer Git et créer le référentiel local

1. Installez Git sur votre ordinateur local.

	La procédure requise pour installer Git diffère selon les systèmes d'exploitation. Consultez la rubrique [Installation de Git] pour accéder aux distributions et consignes d'installation propres aux différents systèmes d'exploitation.

	> [AZURE.NOTE]
	Sur certains systèmes d'exploitation, une version en ligne de commande et une version avec interface utilisateur graphique sont toutes deux disponibles. Les instructions fournies dans cet article utilisent la version en ligne de commande.

2. Ouvrez une ligne de commande, telle que **GitBash** (Windows) ou **Bash** (Unix Shell). Sur les systèmes OS X, la ligne de commande est accessible depuis l'application **Terminal**.

3. À partir de la ligne de commande, accédez au répertoire dans lequel vous allez stocker vos scripts. Par exemple, `cd SourceControl`.

4. Utilisez la commande suivante pour créer une copie locale de votre nouveau référentiel Git, en remplaçant `<your_git_URL>` par l’URL du référentiel Git pour votre service mobile :

		git clone <your_git_URL>

5. Lorsque vous y êtes invité, tapez le nom d'utilisateur et le mot de passe que vous avez définis lorsque vous avez activé le contrôle de code source dans votre service mobile. À l'issue de l'authentification, une série de réponses similaires à ce qui suit s'affiche :

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Accédez au répertoire à partir duquel vous avez exécuté la commande `git clone` et notez qu'un nouveau répertoire est créé avec le nom du service mobile. Pour un service mobile de backend .NET, le référentiel git est initialement vide.

À présent que vous avez créé votre référentiel local, vous pouvez publier votre projet de service de backend .NET à partir de ce référentiel.

##<a name="deploy-scripts"></a>Publication du projet à l'aide de Git

1. Créez un nouveau projet de service mobile de backend .NET dans Visual Studio 2013 ou déplacez un projet existant dans votre nouveau référentiel local.

	Pour effectuer un test rapide, téléchargez et enregistrez le projet de démarrage rapide Mobile Services dans ce dossier.

2. Supprimez les dossiers de package NuGet en conservant le fichier packages.config.

	Mobile Services restaure automatiquement vos packages NuGet basés sur le fichier packages.config. Vous pouvez également définir un fichier .gitignore pour empêcher l'ajout de répertoires du package.

3. Dans l'invite de commandes Git, tapez la commande suivante pour commencer le suivi du nouveau fichier de script :

		$ git add .

4. Tapez la commande suivante pour valider les modifications :

		$ git commit -m "adding the .NET backend service project"

5. Tapez la commande suivante pour télécharger les modifications vers le référentiel distant, et fournissez vos informations d'identification :

		$ git push origin master

	Vous devez voir une série de commandes qui indique que le projet a été déployé sur Mobile Services, que les packages ont été ajoutés et que le service a été redémarré.

6. Accédez à l'URL de votre service mobile de backend .NET. Vous devez voir les éléments suivants :

	![Page de démarrage de Mobile Services](./media/mobile-services-dotnet-backend-store-code-source-control/mobile-service-startup.png)

À présent, votre projet de service mobile est conservé dans le contrôle de code source et vous pouvez publier des mises à jour de service en les envoyant simplement (Push) à partir de votre référentiel local. Pour plus d'informations sur l'apport de modifications aux modèles de données dans un service mobile de backend .NET qui utilise une base de données SQL, consultez [Modifications des modèles de données pour un service mobile de backend .NET].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Git website]: http://git-scm.com
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installation de Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Modifications des modèles de données pour un service mobile de backend .NET]: mobile-services-dotnet-backend-how-to-use-code-first-migrations.md

<!---HONumber=AcomDC_0727_2016-->