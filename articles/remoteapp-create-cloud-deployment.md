<properties 
	pageTitle="Création d'une collection cloud de RemoteApp" 
	description="Découvrez comment créer un déploiement de RemoteApp qui enregistre les données dans le cloud Azure." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="elizapo"/>

# Création d'une collection cloud de RemoteApp

Il existe deux types de collection RemoteApp :

- Cloud : réside complètement dans Azure et est créé à l'aide de l'option **Création rapide** du portail de gestion Azure.  
- Hybride : comprend un réseau virtuel pour un accès local et est créé à l'aide de l'option **Créer avec VPN** du portail de gestion.

Ce didacticiel vous guide dans la procédure de création d'une collection cloud. Il se compose de quatre étapes :

1.	Création d'une collection RemoteApp.
2.	Vous pouvez éventuellement configurer une synchronisation d'annuaires. RemoteApp en a besoin pour synchroniser les utilisateurs, contacts et mots de passe de votre domaine Active Directory local dans votre locataire Azure Active Directory.
5.	Publication d'applications RemoteApp.
6.	Configuration de l'accès utilisateur

**Avant de commencer**

Avant de créer la collection, vous devez effectuer les étapes suivantes :

- [S'inscrire](http://azure.microsoft.com/services/remoteapp/) à RemoteApp. 
- Collecter des informations sur les utilisateurs auxquels vous souhaitez accorder l'accès. Il peut s'agir d'informations sur le compte Microsoft ou sur le compte professionnel Active Directory pour les utilisateurs.
- Cette procédure part du principe que vous allez utiliser les images de modèle fournies dans le cadre de votre abonnement ou que vous avez déjà téléchargé l'image de modèle à utiliser. Si vous devez télécharger une autre image de modèle, rendez-vous sur la page Images de modèle. Il vous suffit de cliquer sur **télécharger une image de modèle** et de suivre les étapes de l'Assistant. 
- Vous voulez fournir des applications personnalisées ou des programmes métier ? Créez une [image](remoteapp-imageoptions.md) et utilisez-la dans votre collection cloud.

## Étape 1 : création d'une collection RemoteApp ##



1. Accédez à la page RemoteApp du portail de gestion Azure.
2. Cliquez sur **Nouveau > Création rapide**.
3. Entrez le nom de votre collection et sélectionnez votre région.
4. Choisissez le plan que vous souhaitez utiliser (de base ou standard).
5. Choisissez le modèle à utiliser pour cette collection. 

	**Conseil :** votre abonnement à RemoteApp s'accompagne d'[images de modèle](remoteapp-images.md) qui contiennent des programmes Office 365 ou Office 2013 (à des fins d'évaluation), certains étant publiés (tels que Word) et d'autres étant prêts pour la publication. Vous pouvez également créer une [image](remoteapp-imageoptions.md) et l'utiliser dans votre collection cloud.


1. Cliquez sur **Créer une collection RemoteApp**.
	
	**Important :** la configuration de votre collection peut prendre jusqu'à 30 minutes.

Une fois votre collection RemoteApp créée, accédez à la page **Démarrage rapide** de RemoteApp pour continuer avec la procédure de configuration.


## Étape 2 : configuration de la synchronisation d'annuaires Active Directory (facultatif) ##

Si vous souhaitez utiliser Active Directory, RemoteApp requiert une synchronisation d'annuaires entre Azure Active Directory et votre domaine Active Directory local afin de synchroniser les utilisateurs, contacts et mots de passe dans votre locataire Azure Active Directory. Consultez [Configuration d'Active Directory pour Azure RemoteApp](remoteapp-ad.md) pour obtenir des informations sur la planification.

## Étape 3 : publication d'applications RemoteApp ##

Une application RemoteApp est l'application ou le programme que vous fournissez à vos utilisateurs. Elle se trouve dans l'image de modèle que vous avez téléchargée pour la collection. Quand un utilisateur accède à une application RemoteApp, Celle-ci semble s'exécuter dans son environnement local. En réalité, elle s'exécute dans Azure.

Avant que vos utilisateurs puissent accéder à des applications, vous devez les publier dans le flux de l'utilisateur final. Il s'agit d'une liste des applications disponibles auxquelles vos utilisateurs peuvent accéder via le client Bureau à distance.
 
Vous pouvez publier plusieurs applications dans votre collection RemoteApp. Dans la page de publication de RemoteApp, cliquez sur **Publier** pour ajouter un programme. Vous pouvez publier l'application à partir du menu Démarrer de l'image de modèle ou en indiquant le chemin d'accès dans l'image de modèle de l'application. Si vous choisissez la première option, sélectionnez l'application à publier. Si vous choisissez la deuxième option, indiquez un nom pour l'application ainsi que le chemin d'accès à son répertoire d'installation dans l'image de modèle.

## Étape 4 : configuration de l'accès utilisateur ##

Maintenant que vous avez créé votre collection RemoteApp, vous devez ajouter les utilisateurs qui seront autorisés à utiliser vos ressources distantes. Si vous utilisez Active Directory, les utilisateurs auxquels vous accordez l'accès doivent exister dans le locataire Active Directory associé à l'abonnement utilisé pour créer cette collection RemoteApp.

1.	Sur la page Démarrage rapide, cliquez sur **Configurer l'accès utilisateur**. 
2.	Entrez le compte professionnel (à partir d'Active Directory) ou le compte Microsoft auquel vous souhaitez accorder l'accès.

	**Remarques :**

	assurez-vous d'utiliser le format « utilisateur@domaine.com ».

	Si vous utilisez Office 365 ProPlus dans votre collection, vous devez utiliser les identités Active Directory de vos utilisateurs. Cela permet de valider la licence.

3.	Une fois les utilisateurs validés, cliquez sur **Enregistrer**.


## Étapes suivantes ##

Félicitations ! Vous avez créé et déployé correctement votre collection cloud RemoteApp. L'étape suivante consistera à faire en sorte que vos utilisateurs téléchargent et installent le client Bureau à distance. L'URL du client est disponible sur la page Démarrage rapide de RemoteApp. Les utilisateurs peuvent à présent se connecter au client et accéder aux applications que vous avez publiées.


<!--HONumber=54-->