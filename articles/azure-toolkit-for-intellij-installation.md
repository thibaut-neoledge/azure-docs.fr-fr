<properties
	pageTitle="Installation du kit de ressources Azure pour IntelliJ | azure.microsoft.com/ Azure"
	description="Apprenez à installer le Kit de ressources Azure pour IntelliJ IDEA."
	services=""
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/11/2016" 
	ms.author="robmcm"/>

# Installation du kit de ressources Azure pour IntelliJ

Le kit de ressources Azure pour IntelliJ contient des modèles et des fonctionnalités qui vous permettent de créer, de développer, de tester et de déployer des applications Azure avec l’environnement de développement IntelliJ IDEA. Le kit de ressources Azure pour IntelliJ est un projet Open Source, dont le code source est disponible sous licence MIT sur le site du projet sur GitHub à l’adresse suivante :

<https://github.com/azure.microsoft.com//azure-tools-for-java>

Il existe deux méthodes d’installation du kit de ressources Azure pour IntelliJ : à partir de la boîte de dialogue Settings (Paramètres) et à partir du menu Configure (Configurer) de l’écran d’accueil ; les deux méthodes d’installation sont présentées dans les étapes suivantes.

[AZURE.INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## Pour installer le Kit de ressources Azure pour IntelliJ à partir de la boîte de dialogue Settings (Paramètres)

1. Démarrez IntelliJ IDEA.

1. Une fois IntelliJ IDEA ouvert, cliquez sur **File** (Fichier), puis sur **Settings** (Paramètres).

    ![Ouvrir la boîte de dialogue Settings (Paramètres) d’IntelliJ IDEA][01a]

1. Dans la boîte de dialogue Settings (Paramètres), cliquez sur **Plugins** (Plug-ins), puis sur **Browse repositories** (Parcourir les référentiels).

    ![Boîte de dialogue Settings (Paramètres) d’IntelliJ IDEA][02a]

1. Dans la boîte de dialogue **Browse Repositories** (Parcourir les référentiels) , tapez « Azure » dans la zone de recherche. Mettez en surbrillance **Azure Toolkit for IntelliJ** (Kit de ressources Azure pour IntelliJ), puis cliquez sur **Install** (Installer).

    ![Rechercher le kit de ressources Azure pour IntelliJ][03]

    IntelliJ IDEA affiche la progression de l’installation dans une boîte de dialogue.

    ![Progression de l’installation][04]

1. Une fois l’installation terminée, cliquez sur **Restart IntelliJ IDEA** (Redémarrer IntelliJ IDEA).

    ![Redémarrer IntelliJ IDEA][05]

1. Cliquez sur **OK** pour fermer la boîte de dialogue Settings (Paramètres).

    ![Fermer la boîte de dialogue Settings (Paramètres) d’IntelliJ IDEA][06]

1. Quand vous êtes invité à redémarrer IntelliJ IDEA ou à reporter l’opération, cliquez sur **Restart** (Redémarrer).

    ![Redémarrer IntelliJ IDEA][07]

## Pour installer le Kit de ressources Azure pour IntelliJ à partir de l’écran d’accueil

1. Démarrez IntelliJ IDEA.

1. Quand l’écran d’accueil d’IntelliJ IDEA s’affiche, cliquez sur **Configure** (Configurer), puis sur **Plugins** (Plug-ins).

    ![Installer les plug-ins IntelliJ IDEA][01b]

1. Dans la boîte de dialogue **Plugins** (Plug-ins), cliquez sur **Browse repositories** (Parcourir les référentiels).

    ![Parcourir les référentiels de plug-ins IntelliJ IDEA][02b]

1. Dans la boîte de dialogue **Browse Repositories** (Parcourir les référentiels) , tapez « Azure » dans la zone de recherche. Mettez en surbrillance **Azure Toolkit for IntelliJ** (Kit de ressources Azure pour IntelliJ), puis cliquez sur **Install** (Installer).

    ![Rechercher le kit de ressources Azure pour IntelliJ][03]

    IntelliJ IDEA affiche la progression de l’installation dans une boîte de dialogue.

    ![Progression de l’installation][04]

1. Une fois l’installation terminée, cliquez sur **Restart IntelliJ IDEA** (Redémarrer IntelliJ IDEA).

    ![Redémarrer IntelliJ IDEA][05]

1. Quand vous êtes invité à redémarrer IntelliJ IDEA ou à reporter l’opération, cliquez sur **Restart** (Redémarrer).

    ![Redémarrer IntelliJ IDEA][07]

## Voir aussi

Pour plus d’informations sur les boîtes à outils Azure pour les environnements de développement Java, consultez les liens suivants :

- [Kit de ressources Azure pour Eclipse]
  - [Installation du kit de ressources Azure pour Eclipse]
  - [Créer une application web « Hello World » pour Azure dans Eclipse]
  - [Nouveautés du kit de ressources Azure pour Eclipse]
- [Kit de ressources Azure pour IntelliJ]
  - *Installation du kit de ressources Azure pour IntelliJ (cet article)*
  - [Créer une application web « Hello World » pour Azure dans IntelliJ]
  - [Nouveautés du Kit de ressources Azure pour IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

<!-- URL List -->

[Kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application web « Hello World » pour Azure dans Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application web « Hello World » pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Nouveautés du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Nouveautés du Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java pour Azure]: https://azure.azure.microsoft.com/.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png

<!---HONumber=AcomDC_0817_2016-->