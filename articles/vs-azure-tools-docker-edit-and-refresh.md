<properties
   pageTitle="Débogage d’applications dans un conteneur Docker local | Microsoft Azure"
   description="Découvrez comment modifier une application qui s’exécute dans un conteneur Docker local et actualiser le conteneur via la modification et l’actualisation ainsi que la définition de points d’arrêt pour le débogage"
   services="visual-studio-online"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# Débogage d’applications dans un conteneur Docker local

## Vue d'ensemble
Visual Studio Tools pour Docker fournit un moyen cohérent de développer et valider votre application localement sur un conteneur de Docker Linux. Vous n’avez pas besoin de redémarrer le conteneur chaque fois que vous modifiez le code. Cet article illustre comment utiliser la fonctionnalité Modifier et actualiser pour démarrer une application web ASP.NET Core dans un conteneur Docker local, apporter les modifications nécessaires, puis actualiser le navigateur pour afficher ces modifications. Vous verrez également comment définir des points d’arrêt pour le débogage.

> [AZURE.NOTE] La prise en charge des conteneurs Windows sera proposée dans une version ultérieure

## Composants requis
Les outils suivants doivent être installés.

- [Visual Studio 2015 Mise à jour 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- [Microsoft ASP .NET Core RC 2](http://go.microsoft.com/fwlink/?LinkId=798481)
- [Visual Studio 2015 Tools pour Docker](https://aka.ms/DockerToolsForVS)

Pour exécuter des conteneurs de Docker localement, vous aurez besoin d’un client Docker local. Vous pouvez utiliser la version [Boîte à outils Docker](https://www.docker.com/products/overview#/docker_toolbox), qui nécessite la désactivation d’Hyper-V, ou la [version bêta de Docker pour Windows](https://beta.docker.com) qui utilise Hyper-V et nécessite Windows 10.

Si vous utilisez la Boîte à outils Docker, vous devrez [Configurer le client Docker](./vs-azure-tools-docker-setup.md)

## Modification d’une application en cours d’exécution dans un conteneur Docker local
Visual Studio 2015 Tools pour Docker permet aux développeurs d’applications web ASP .NET Core RC2 de tester et d’exécuter leur application dans un conteneur Docker, d’apporter des modifications à l’application dans Visual Studio et d’actualiser le navigateur pour voir les modifications appliquées à l’application en cours d’exécution dans le conteneur. Avec .NET Core et Visual Studio Tools pour Docker version 0.20, vous pouvez également définir des points d’arrêt pour le code en cours d’exécution avec le conteneur Docker.

1. Dans le menu Visual Studio, sélectionnez **Fichier > Nouveau > Projet**.

1. Sous la section **Modèles** de la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C# > Web**.

1. Sélectionnez **ASP.NET Core Web Application (.NET Core)**.

1. Donnez un nom à votre nouvelle application (ou utilisez la valeur par défaut) et cliquez sur **OK**.

1. Sous **Modèles ASP.NET Core**, sélectionnez **Application web** et cliquez sur **OK**.

1. Désélectionnez **Héberger dans le cloud**, car vous allez utiliser Docker comme solution de déploiement.

1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter > Prise en charge Docker**.

	![][0]

1. Les fichiers suivants sont créés sous le nœud du projet :

	![][1]

> [AZURE.NOTE] Si vous utilisez la [version bêta de Docker pour Windows](https://beta.docker.com), ouvrez Properties\\Docker.props et supprimez la valeur par défaut, puis redémarrez Visual Studio pour que la valeur prenne effet.
>
> ![][2]

##Modifier et actualiser
Pour effectuer une itération rapide des modifications, vous pouvez lancer votre application au sein d’un conteneur et continuer à apporter des modifications, en les observant comme vous le feriez avec IIS Express.

1. Définissez la configuration de solution sur `Debug`, puis appuyez sur **&lt;CTRL + F5 >** pour créer votre image de Docker et l’exécuter localement.

    Une fois l’image de conteneur créée et en cours d’exécution dans un conteneur Docker, Visual Studio lancera l’application web dans votre navigateur par défaut. Si vous utilisez le navigateur Microsoft Edge ou rencontrez des erreurs, consultez la section [Résolution des problèmes](vs-azure-tools-docker-troubleshooting-docker-errors.md).

1. Accédez à la page À propos, à laquelle nous allons apporter nos modifications.

1. Revenez à Visual Studio et ouvrez `Views\Home\About.cshtml`.

1. Ajoutez le contenu HTML suivant à la fin du fichier et enregistrez les modifications.

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	Dans la fenêtre de sortie, lorsque le build .NET est terminé et que vous voyez ces lignes, revenez sur votre navigateur et actualisez la page À propos.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.	Vos modifications ont été appliquées !

##Points d’arrêt pour débogage
Les modifications nécessitent souvent une inspection plus poussée, en exploitant les fonctionnalités de débogage de Visual Studio

1.	Revenez à Visual Studio et ouvrez `Controllers\HomeController.cs`

1.  Remplacez le contenu de la méthode About() par ce qui suit :

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  Définissez un point d’arrêt à gauche de la ligne `string message`....

1.  Appuyez sur **&lt;F5>** pour démarrer le débogage.

1.  Accédez à la page À propos de pour atteindre votre point d’arrêt.

1.  Basculez sur Visual Studio pour afficher le point d’arrêt, puis inspectez la valeur du message.

	![][3]

##Résumé
Avec [Visual Studio 2015 Tools pour Docker](https://aka.ms/DockerToolsForVS), vous pouvez obtenir la productivité du travail local tout en conservant le réalisme de production du développement au sein d’un conteneur Docker.

## Résolution de problèmes
[Résolution des problèmes de développement avec Docker pour Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## En savoir plus sur Docker avec Visual Studio, Windows et Azure

- [Outils Docker pour Visual Studio](http://aka.ms/dockertoolsforvs) : développer votre code .NET Core dans un conteneur
- [Outils Docker pour Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) : créez et déployez des conteneurs Docker
- [Outils Docker pour Visual Studio Code](http://aka.ms/dockertoolsforvscode) : services linguistiques pour la modification de fichiers Docker, avec plus de scénarios E2E à venir
- [Informations sur le conteneur Windows](http://aka.ms/containers) : informations sur Windows Server et Nano Server
- [Service de conteneur Azure](https://azure.microsoft.com/services/container-service/) - [Contenu du service de conteneur Azure](http://aka.ms/AzureContainerService)

## Divers outils Docker

[D’excellents outils Docker (blog de Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## Articles intéressants

[Introduction aux microservices de NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## Présentations

- [Steve Lasker : Visual Studio Live Las Vegas 2016 - E2E Docker](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduction à ASP.NET Core @ build 2016 - Démonstration Where You At](https://channel9.msdn.com/Events/Build/2016/B810)
- [Développement d’applications .NET dans des conteneurs, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png
[2]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-props.png
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0608_2016-->