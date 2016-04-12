<properties
   pageTitle="Déboguer des applications dans un conteneur Docker local avec la fonctionnalité Modifier et actualiser | Microsoft Azure"
   description="Découvrez comment modifier une application qui s’exécute dans un conteneur Docker local et actualiser le conteneur via la modification et l’actualisation"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="03/25/2016"
   ms.author="tarcher" />

# Déboguer des applications dans un conteneur Docker local avec la fonctionnalité Modifier et actualiser

## Vue d'ensemble
Visual Studio Tools pour Docker offre un moyen pratique pour développer et tester votre application localement dans un conteneur Docker, sans avoir à redémarrer le conteneur chaque fois que vous modifiez le code. Cet article illustre comment utiliser la fonctionnalité Modifier et actualiser pour démarrer une application web ASP.NET 5 dans un conteneur Docker local, apporter les modifications nécessaires, puis actualiser le navigateur pour afficher ces modifications.

## Configuration requise
Les outils suivants doivent être installés.

- [Visual Studio 2015 Mise à jour 1](https://go.microsoft.com/fwlink/?LinkId=691979)
- [Microsoft ASP .NET et Web Tools 2015 RC](https://go.microsoft.com/fwlink/?LinkId=627627)
- [Boîte à outils Docker](https://www.docker.com/products/overview#/docker_toolbox)
- [Visual Studio 2015 Tools pour Docker](https://aka.ms/DockerToolsForVS)
- [Configurer le client Docker](./vs-azure-tools-docker-setup.md)

> [AZURE.NOTE] Si vous disposez d’une version antérieure de Visual Studio 2015 Tools pour Docker, vous devez la désinstaller à partir du panneau de configuration avant de pouvoir installer la version la plus récente.

## Modification d’une application en cours d’exécution dans un conteneur Docker local
Visual Studio 2015 Tools pour Docker permet aux développeurs d’applications web ASP .NET 5 de tester et d’exécuter leur application dans un conteneur Docker, d’apporter des modifications à l’application dans Visual Studio et d’actualiser le navigateur pour voir les modifications appliquées à l’application en cours d’exécution dans le conteneur.

1. Dans le menu Visual Studio, sélectionnez **Fichier > Nouveau > Projet**. 

1. Sous la section **Modèles** de la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C# > Web**.

1. Sélectionnez **Application web ASP.NET**.

1. Donnez un nom à votre nouvelle application (ou utilisez la valeur par défaut).

1. Appuyez sur **OK**.

1. Sous **Modèles ASP.NET 5**, sélectionnez **Application web ASP.NET**.

1. Appuyez sur **OK**.

1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter > Prise en charge Docker**.

	![][0]
 
1. Les fichiers suivants sont créés sous le nœud du projet :

	![][1]

1. Définissez la Configuration de Solution sur `Debug`, puis appuyez sur **&lt;F5>** pour commencer à tester votre application localement.

1. Une fois l’image de conteneur créée et en cours d’exécution dans un conteneur Docker, une console PowerShell essaiera de lancer l’application web dans votre navigateur par défaut. Si vous utilisez le navigateur Microsoft Edge, consultez la section [Résolution des problèmes](#troubleshooting).

1. Revenez à Visual Studio et ouvrez `Views\Home\Index.cshtml`.

1. Ajouter le contenu HTML suivant à la fin du fichier et enregistrez les modifications.

		<div>
			<h1>Hello from Docker Container!</h1>
		</div>

1.	Revenez à la fenêtre de votre navigateur et actualisez-la.

1.	Faites défiler jusqu’à la fin de la page d’accueil pour vérifier que tous les changements ont bien été appliqués. La recompilation du site pouvant prendre quelques secondes, actualisez simplement le navigateur de nouveau si vous ne voyez pas vos modifications reflétées immédiatement.

##Résolution de problèmes 

- **L’exécution de l’application entraîne l’ouverture de PowerShell, qui affiche l’erreur avant de se refermer. La page du navigateur ne s’ouvre pas.**

	Cela peut être dû à une erreur au cours de `docker-compose-up`. Procédez comme suit pour afficher l’erreur :

	1. Ouvrez le fichier `Properties\launchSettings.json`.
	
	1. Recherchez l’entrée Docker.
	
	1. Notez la ligne qui commence comme suit :

			"commandLineArgs": "-ExecutionPolicy RemoteSigned …”
	
	1. Ajoutez le paramètre `-noexit` afin que la ligne soit comme suit : Cette opération permet de garder PowerShell ouvert afin que vous puissiez afficher l’erreur.

			"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”

- **Génération : échec de génération de l’image, erreur lors de la vérification de la connexion TLS : l’hôte n’est pas en cours d’exécution.**

	Vérifiez que l’hôte Docker par défaut est en cours d’exécution. Consultez l’article [Configurer le client Docker](./vs-azure-tools-docker-setup.md)

- **Impossible de trouver le mappage de volume**

	Par défaut, VirtualBox partage `C:\Users` en tant que `c:/Users`. Si le projet n’est pas sous `c:\Users`, ajoutez-le manuellement aux [dossiers partagés](https://www.virtualbox.org/manual/ch04.html#sharedfolders) VirtualBox.

- **Utiliser Microsoft Edge en tant que navigateur par défaut**

	Si vous utilisez le navigateur Microsoft Edge, le site risque de ne pas s’ouvrir, car Edge considère l’adresse IP comme non sécurisée. Pour résoudre ce problème, procédez comme suit :
	1. Dans la zone Exécuter, tapez `Internet Options`.
	2. Cliquez sur **Options Internet**. 
	2. Cliquez sur l’onglet **Sécurité**.
	3. Sélectionnez la zone **Intranet local**.
	4. Cliquez sur **Sites**. 
	5. Ajoutez l’IP de votre machine virtuelle (dans ce cas, l’hôte Docker) à la liste. 
	6. Actualisez la page dans Edge et vérifiez que le site est opérationnel. 
	7. Pour plus d’informations sur ce problème, reportez-vous au billet de blog de Scott Hanselman [Microsoft Edge can’t see or open VirtualBox-hosted local web sites (Microsoft Edge ne peut pas voir ou ouvrir des sites web locaux hébergés sur VirtualBox)](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx).

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png

<!---HONumber=AcomDC_0330_2016-->