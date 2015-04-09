<properties
	pageTitle="Création d'une application web - Gestion des services Azure"
	description="Apprenez à créer une application web à l'aide du portail Azure."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

#Création d'une application web

Cette rubrique explique comment créer une application web à partir de la galerie ou du portail Azure.

Pour plus d'informations sur le déploiement de vos contenus vers une application web que vous avez créée, consultez la section **Déploiement** de la page [Azure Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) (en anglais).

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d'ouvrir un compte Azure, accédez à la page d'[essai d'App Service](http://go.microsoft.com/fwlink/?LinkId=523751) (en anglais), qui vous permet de créer immédiatement une application web de départ de courte durée dans App Service. Aucune carte de crédit n'est requise, et vous ne prenez aucun engagement.

##<a name="createawebsiteportal"></a>Création d'une application web à l'aide du portail Azure

Pour créer une application web dans Azure, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

2. Cliquez sur l'icône **Nouveau** en bas à gauche du portail Azure.

3. Cliquez sur l'icône **Web + Mobile**, cliquez sur l'icône **Web App**, entrez une valeur pour l'URL, puis cliquez sur **Créer** dans l'angle inférieur droit du panneau de création.

4. Une fois l'application web créée, le message suivant s'affiche : **Déploiement vers le groupe de ressources <nom du groupe de ressources> réussi**.

5. Dans le portail, cliquez sur le nom de l'application web affichée dans la liste des applications web pour ouvrir le panneau correspondant.

6. Celui-ci inclut plusieurs options permettant d'obtenir des outils de développement d'application web et de configurer une publication pour votre application web ou un déploiement à partir d'un fournisseur de contrôle de code source tel que TFS ou Git. La publication FTP est configurée par défaut pour les applications web, et le nom d'hôte FTP est affiché dans la section **Essentials** du panneau de l'application web. Avant la publication avec FTP ou Git, choisissez l'option de réinitialisation du profil de publication**** dans le panneau de l'application web pour pouvoir vous authentifier auprès de l'hôte FTP ou du référentiel Git lors du déploiement de contenus vers votre application web.

7. Le panneau **Paramètres** présente les paramètres relatifs à votre application web, tels que :

	- version de .NET, PHP, Java ou Python pour votre application web
	- modification dans Visual Studio Online
	- Liaisons SSL
	- Noms de domaine personnalisés
	- authentification/autorisation
	- diagnostics d'application et de site
	- surveillance des points de terminaison
	- Options de journalisation
	- Paramètres d'application pour l'environnement Azure (remplacement de <appSettings> dans le fichier Web.config de votre environnement de développement, par exemple)
	- Chaînes de connexion (remplacement de <connectionStrings> dans le fichier Web.config de votre environnement de développement, par exemple)
	- Processeurs de script pour des extensions de fichier spécifiques comme *.php

##<a name="howtocreatefromgallery"></a>Procédure : création d'une application web à partir de la galerie

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Procédure : suppression d'une application web
Pour supprimer une application web, utilisez l'icône **Supprimer** du portail Azure. L'icône **Supprimer** est disponible dans la partie supérieure du panneau de l'application web.

##<a name="nextsteps"></a> Étapes suivantes

Pour plus d'informations, consultez la page [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

## Modifications
* Pour plus d'informations sur les modifications entre Sites Web et App Service, consultez la page : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714) (en anglais)
* Pour plus d'informations sur les modifications entre l'ancien portail et le nouveau, consultez la page : [Référence pour la navigation sur la version préliminaire du portail](http://go.microsoft.com/fwlink/?LinkId=529715) (en anglais)

<!--HONumber=49-->