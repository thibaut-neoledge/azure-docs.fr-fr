<properties 
	pageTitle="Création de sites Web - gestion du service Azure" 
	description="Découvrez comment créer un site Web à l'aide du portail de gestion Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

#Création d'un site Web

Cette rubrique explique comment créer un site Web à partir de la galerie ou du portail de gestion.

Pour plus d'informations sur le déploiement de votre contenu sur un site Web Azure que vous avez créé, consultez la section **Déploiement** dans [Sites Web Azure](/fr-fr/documentation/services/web-sites/).

## Sommaire ##

- [Procédure : création d'un site Web à l'aide du portail de gestion](#createawebsiteportal)
- [Procédure : création d'un site Web à partir de la galerie](#howtocreatefromgallery)
- [Procédure : suppression d'un site Web](#deleteawebsite)
- [Étapes suivantes](#nextsteps)

##<a name="createawebsiteportal"></a>Procédure : création d'un site Web à l'aide du portail de gestion

Pour créer un site Web dans Azure, procédez comme suit :
	
1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).

2. Cliquez sur l'icône **Créer un nouveau** dans le coin inférieur gauche du portail de gestion.

3. Cliquez sur l'icône **Site Web**, puis sur l'icône **Création rapide**, entrez une valeur pour l'URL, puis cliquez sur la coche en regard de **Créer un site Web** dans le coin inférieur droit de la page.

4. Une fois le site Web créé, le texte **La création du site Web <*nom du site Web*> a réussi** s'affiche. Vous pouvez accéder au site Web en cliquant sur **Parcourir** en bas de la page du portail.

5. Dans le portail, cliquez sur le nom du site Web affiché dans la liste des sites Web pour ouvrir la page de gestion **Démarrage rapide** du site Web.

6. La page **Démarrage rapide** inclut plusieurs options permettant d'obtenir des outils de développement de site Web, de configurer une publication pour votre site Web ou un déploiement à partir d'un fournisseur de contrôle de code source tel que TFS ou Git. La publication FTP est configurée par défaut pour les sites Web. Par ailleurs, le nom d'hôte FTP est affiché dans la section **Aperçu rapide** de la page **Tableau de bord** sous **Nom de l'hôte FTP**. Avant la publication avec FTP ou Git, choisissez l'option **Réinitialiser les informations d'identification du déploiement** dans la page **Tableau de bord** pour pouvoir vous authentifier auprès de l'hôte FTP ou du référentiel Git durant le déploiement de contenus vers votre site Web.

7. La page de gestion **Configurer** affiche des paramètres pour votre site Web, tels que :

	- Version du .NET Framework ou de PHP pour votre application Web
	- Liaisons SSL
	- Noms de domaine personnalisés
	- Options de journalisation
	- Paramètres de l'application pour l'environnement Windows Azure (remplacement de <appSettings> dans le fichier Web.config de votre environnement de développement, par exemple)
	- Chaînes de connexion (remplacement de <connectionStrings> dans le fichier Web.config de votre environnement de développement, par exemple)
	- Processeurs de script pour des extensions de fichier spécifiques comme *.php

##<a name="howtocreatefromgallery"></a>Procédure : création d'un site Web à partir de la galerie

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Procédure : suppression d'un site
Pour supprimer un site Web, utilisez l'icône **Supprimer** du portail de gestion Azure. L'icône **Supprimer** est disponible sur le portail Azure quand vous cliquez sur **Sites Web** pour répertorier tous vos sites Web, ainsi qu'en bas de chaque page de gestion de site Web.

##<a name="nextsteps"></a>Étapes suivantes

Pour plus d'informations, consultez la page [Sites Web Azure](/fr-fr/documentation/services/web-sites/).


<!--HONumber=42-->
