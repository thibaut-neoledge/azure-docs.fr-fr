<properties
   pageTitle="Déployer un conteneur ASP.NET sur un hôte Docker distant | Microsoft Azure"
   description="Découvrez comment utiliser Visual Studio Tools pour Docker pour publier une application web ASP.NET 5 dans un conteneur Docker fonctionnant sur une machine hôte Azure Docker"   
   services="visual-studio-online"
   documentationCenter=".net"
   authors="allclark"
   manager="douge"
   editor=""/>

<tags
   ms.service="visual-studio-online"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="allclark"/>

# Déployer un conteneur ASP.NET sur un hôte Docker distant

## Vue d'ensemble
Docker est un moteur de conteneur léger, semblable à certains égards à une machine virtuelle, que vous pouvez utiliser pour héberger des applications et des services. Visual Studio prend en charge Docker sur Ubuntu, CoreOS et Windows. Ce didacticiel vous guide dans l’extension [Visual Studio 2015 Tools pour Docker](http://aka.ms/DockerToolsForVS) pour publier une application ASP.NET 5 sur un hôte Docker sur Azure.

## 1\. Composants requis
Les éléments suivants sont nécessaires pour suivre ce didacticiel :

- Créer une machine virtuelle hôte Azure Docker, comme le décrit la rubrique [Comment utiliser docker-machine avec Azure](./virtual-machines/virtual-machines-linux-classic-docker-machine.md)
- Installez [Visual Studio 2015](https://www.visualstudio.com/fr-FR/downloads/download-visual-studio-vs.aspx)
- Installez [Visual Studio 2015 Tools pour Docker - Version préliminaire](http://aka.ms/DockerToolsForVS)

## 2\. Créer une application web ASP.NET 5
La procédure suivante vous guidera dans la création d'une application ASP.NET 5 de base qui sera utilisée dans ce didacticiel.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 3\. Ajouter la prise en charge Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 4\. Pointer sur l'hôte Docker distant

1.  Dans l’**Explorateur de solutions** Visual Studio, localisez le dossier **Propriétés** et développez-le.
1.  Ouvrez le fichier *Docker.props*.

    ![Ouvrez le fichier Docker.props][0]

1.  Modifiez la valeur de **DockerMachineName** et attribuez-lui le nom de votre hôte Docker distant. Si vous ne connaissez pas le nom de votre hôte Docker distant, exécutez ```docker-machine ls``` dans l'invite Windows PowerShell. Utilisez la valeur listée sous la colonne **Nom** de l’hôte souhaité.

    ![Modifiez le nom de la machine Docker][1]

1.  Redémarrez Visual Studio.

## 5\. Configurer le point de terminaison de l'hôte Azure Docker
Avant de déployer votre application à partir de Visual Studio vers Azure, ajoutez à votre machine virtuelle hôte Docker le point de terminaison 80 pour pouvoir ultérieurement afficher votre application dans le navigateur. Cela est possible avec le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885) ou avec Windows PowerShell :

- **Utilisation du [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885) pour configurer le point de terminaison de l’hôte Azure Docker**

    1.  Connectez-vous au [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885). 
    
    1.  Sélectionnez **MACHINES VIRTUELLES**.
    
    1.  Sélectionnez votre machine virtuelle hôte Docker.
    
    1.  Sélectionnez l’onglet **POINTS DE TERMINAISON**.
    
    1.  Sélectionnez **AJOUTER** (en bas de la page).
    
    1.  Suivez les instructions pour exposer le port 80, utilisé par le script de déploiement par défaut.

- **Utilisation de Windows PowerShell pour configurer le point de terminaison de l'hôte Azure Docker**

    1. Ouvrez Windows PowerShell.
    1. Entrez la commande suivante dans l'invite Windows PowerShell (en modifiant les valeurs entre chevrons en fonction de votre environnement) :  

        ```PowerShell
        C:\PS>Get-AzureVM -ServiceName "<your_cloud_service_name>" -Name "<your_vm_name>" | Add-AzureEndpoint -Name "<endpoint_name>" -Protocol "tcp" -PublicPort 80 -LocalPort 80 | Update-AzureVM
        ```

## 6\. Générer et exécuter l'application
Lors du déploiement sur les hôtes distants, la fonctionnalité de mappage de volume utilisée pour le développement Modifier et actualiser ne fonctionnera pas. Par conséquent, vous devrez utiliser la *configuration de lancement* lors de la génération de votre application afin d’éviter la configuration de mappage de volume. Suivez ces instructions pour exécuter votre application.

1.  Dans la barre d’outils Visual Studio, sélectionnez la configuration **Lancement**

1.  Modifiez la cible de lancement en spécifiant **Docker**.

1.  Sélectionnez l’icône **Docker** pour générer et exécuter l’application.

![Lancez l’application][2]

Vous devriez obtenir un résultat semblable à ce qui suit.

![Affichez votre application][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0608_2016-->