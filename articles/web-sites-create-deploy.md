<properties urlDisplayName="How to create" pageTitle="Création de sites web - gestion du service Azure" metaKeywords="Azure creating website, Azure deleting website" description="Learn how to create a website using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Création d'un site web

Cette rubrique explique comment créer un site web à partir de la galerie ou du portail de gestion.

Pour plus d'informations sur le déploiement de votre contenu sur un site web Azure que vous avez créé, consultez la section **Déploiement** dans [Azure Web Sites](/fr-fr/documentation/services/web-sites/).

## Sommaire ##

- [Procédure : création d'un site web à l'aide du portail de gestion](#createawebsiteportal)
- [Procédure : création d'un site web à partir de la galerie](#howtocreatefromgallery)
- [Procédure : suppression d'un site web](#deleteawebsite)
- [Étapes suivantes](#nextsteps)

##<a name="createawebsiteportal"></a>Procédure : création d'un site web à l'aide du portail de gestion

Pour créer un site web dans Azure, procédez comme suit :
	
1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).

2. Cliquez sur l'icône **Créer un nouveau** en bas à gauche du portail de gestion.

3. Cliquez sur l'icône **Site web**, sur l'icône **Création rapide**, entrez une valeur pour l'URL, puis cliquez sur la coche à côté de **Créer un site web** en bas à droite de la page.

4. Une fois le site web créé, le texte **La création du site web <*nom du site web*> a réussi** s'affiche. Vous pouvez accéder au site web en cliquant sur **Parcourir** en bas de la page du portail.

5. Dans le portail, cliquez sur le nom du site web affiché dans la liste pour ouvrir la page de gestion **Démarrage rapide** du site web.

6. La page **Démarrage rapide** propose plusieurs options pour obtenir des outils de développement de site web, configurer une publication pour votre site web ou configurer un déploiement à partir d'un fournisseur de contrôle de code source tel que TFS or Git. La publication FTP est configurée par défaut pour les sites web. Par ailleurs, le nom d'hôte FTP est affiché dans la section **Aperçu rapide** de la page **Tableau de bord** sous **Nom de l'hôte FTP**. Avant la publication avec FTP ou Git, choisissez l'option **Réinitialiser les informations d'identification du déploiement** dans la page **Tableau de bord** pour pouvoir vous authentifier auprès de l'hôte FTP ou du référentiel Git pendant le déploiement du contenu sur votre site web.

7. La page de gestion **Configurer** affiche des paramètres pour votre site web, tels que :

	- Version du .NET Framework ou de PHP pour votre application web
	- Liaisons SSL
	- Noms de domaine personnalisés
	- Options de journalisation
	- Paramètres d'application pour l'environnement Azure (remplacement de <appSettings> dans le fichier Web.config de votre environnement de développement, par exemple)
	- Chaînes de connexion (remplacement de <connectionStrings> dans le fichier Web.config de votre environnement de développement, par exemple)
	- Processeurs de script pour des extensions de fichier spécifiques comme *.php

##<a name="howtocreatefromgallery"></a>Procédure : création d'un site web à partir de la galerie

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Procédure : suppression d'un site
Pour supprimer un site web, utilisez l'icône **Supprimer** du portail de gestion Azure. L'icône **Supprimer** est disponible dans le portail Azure quand vous cliquez sur **Sites web** pour répertorier tous vos sites web, et en bas de chaque page de gestion de site web.

##<a name="nextsteps"></a>Étapes suivantes

Pour plus d'informations, consultez [Azure Web Sites](/fr-fr/documentation/services/web-sites/).

<!--HONumber=35.1-->
