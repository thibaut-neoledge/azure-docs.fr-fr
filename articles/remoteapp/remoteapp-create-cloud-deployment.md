<properties 
	pageTitle="Création d'une collection cloud d’Azure RemoteApp"
	description="Découvrez comment créer un déploiement d'Azure RemoteApp qui enregistre les données dans le cloud Azure."
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
	ms.date="09/02/2015"
	ms.author="elizapo"/>

# Création d'une collection cloud d’Azure RemoteApp

Il existe deux types de collection Azure RemoteApp :

- Cloud : réside complètement dans Azure . Vous pouvez soit enregistrer toutes les données dans le cloud (la collection se trouve uniquement dans le cloud), soit connecter votre collection à un réseau virtuel et enregistrer vos données dans celui-ci.   
- Hybride : inclut un réseau virtuel pour l’accès local (nécessite Azure AD et un environnement Active Directory local).

Ce didacticiel vous guide dans la procédure de création d'une collection cloud. Il se compose de quatre étapes :

1.	Création d'une collection RemoteApp.
2.	Vous pouvez éventuellement configurer une synchronisation d'annuaires. Si vous utilisez Azure AD et Active Directory, vous devez synchroniser les utilisateurs, contacts et mots de passe de votre domaine Active Directory local avec votre client Azure AD.
5.	Publication d'applications RemoteApp.
6.	Configuration de l'accès utilisateur

**Remarque** *Cette rubrique est en cours de reconstruction. J’ai mis à jour les étapes pour refléter la nouvelle interface utilisateur, mais je ne peux pas encore republier l’intégralité de la rubrique. Je rédige actuellement de nouveaux articles qui vous permettront de comprendre beaucoup plus facilement les options d’authentification et de collection à votre disposition. Par conséquent, si vous êtes un peu perdu à ce stade, sachez que je travaille le plus rapidement possible pour mieux vous informer. Merci.*

**Avant de commencer**

Avant de créer la collection, vous devez effectuer les étapes suivantes :

- [S’inscrire](http://azure.microsoft.com/services/remoteapp/) à Azure RemoteApp. 
- Collecter des informations sur les utilisateurs auxquels vous souhaitez accorder l'accès. Il peut s'agir d'informations sur le compte Microsoft ou sur le compte professionnel Active Directory pour les utilisateurs.
- Cette procédure part du principe que vous allez utiliser les images de modèle fournies dans le cadre de votre abonnement ou que vous avez déjà téléchargé l'image de modèle à utiliser. Si vous devez télécharger une autre image de modèle, rendez-vous sur la page Images de modèle. Il vous suffit de cliquer sur **télécharger une image de modèle** et de suivre les étapes de l'Assistant. 
- Vous souhaitez utiliser l’image d’Office 365 ProPlus ? Pour plus d’informations, cliquez [ici](remoteapp-officesubscription.md).
- Vous voulez fournir des applications personnalisées ou des programmes métier ? Créez une [image](remoteapp-imageoptions.md) et utilisez-la dans votre collection cloud.
- Déterminez si vous devez vous connecter à un réseau virtuel. Si vous choisissez de vous connecter à un réseau virtuel, vérifiez qu’il respecte les instructions de dimensionnement et qu’il peut se connecter à RemoteApp.
- Si vous utilisez un réseau virtuel, déterminez si vous souhaitez le joindre à votre domaine Active Directory local.

## Étape 1 : créer une collection cloud avec ou sans réseau virtuel##


Pour **créer une collection uniquement dans le cloud**, procédez comme suit :

1. Accédez à la page RemoteApp du portail de gestion Azure.
2. Cliquez sur **Nouveau > Création rapide**.
3. Entrez le nom de votre collection et sélectionnez votre région.
4. Choisissez le plan que vous souhaitez utiliser (de base ou standard).
5. Choisissez le modèle à utiliser pour cette collection. 

	**Conseil :** votre abonnement à RemoteApp s'accompagne d'[images de modèle](remoteapp-images.md) qui contiennent des programmes Office 365 ou Office 2013 (à des fins d'évaluation), certains étant publiés (tels que Word) et d'autres étant prêts pour la publication. Vous pouvez également créer une [image](remoteapp-imageoptions.md) et l'utiliser dans votre collection cloud.


1. Cliquez sur **Créer une collection RemoteApp**.
	
	**Important :** la configuration de votre collection peut prendre jusqu'à 30 minutes.

Après avoir créé votre collection RemoteApp, double-cliquez sur son nom. Ceci affiche la page **Démarrage rapide** qui vous permet de terminer la configuration de la collection.

Pour **créer une collection dans le cloud et dans un réseau virtuel**, procédez comme suit :

1. Accédez à la page RemoteApp du portail de gestion Azure.
2. Cliquez sur **Nouveau** > **Créer avec un réseau virtuel**.
3. Entrez un nom pour votre collection.
4. Choisissez le plan que vous souhaitez utiliser (de base ou standard).
5. Choisissez le réseau virtuel que vous avez déjà créé. Vous ne savez pas comment faire ? Pour l’instant, les étapes se trouvent dans la rubrique [hybride](remoteapp-create-hybrid-deployment.md).
6. Décidez si vous souhaitez joindre votre collection à votre domaine. Dans l’affirmative, vous devez utiliser AD Connect pour intégrer Azure AD et votre environnement Active Directory. La procédure à suivre est traitée à l’**étape 2**.
6. Cliquez sur **Créer une collection RemoteApp**.


## Étape 2 : configuration de la synchronisation d'annuaires Active Directory (facultatif) ##

Si vous souhaitez utiliser Active Directory, RemoteApp requiert une synchronisation d'annuaires entre Azure Active Directory et votre domaine Active Directory local afin de synchroniser les utilisateurs, contacts et mots de passe dans votre locataire Azure Active Directory. Consultez [Configuration d'Active Directory pour Azure RemoteApp](remoteapp-ad.md) pour obtenir des informations sur la planification. Vous pouvez également accéder directement à [AD Connect](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx) pour obtenir des informations.

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

 

<!---HONumber=September15_HO1-->