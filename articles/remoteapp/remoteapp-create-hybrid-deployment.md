<properties 
	pageTitle="Création d'une collection hybride pour Azure RemoteApp" 
	description="Découvrez comment créer un déploiement de RemoteApp qui se connecte à votre réseau interne." 
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
	ms.date="05/28/2015" 
	ms.author="elizapo"/>

# Création d'une collection hybride pour Azure RemoteApp

Il existe deux types de collection RemoteApp :

- Cloud : réside complètement dans Azure et est créé à l'aide de l'option **Création rapide** du portail de gestion Azure.  
- Hybride : comprend un réseau virtuel pour un accès local et est créé à l'aide de l'option **Créer avec VNET** du portail de gestion.

Ce didacticiel vous familiarise avec la procédure de création d'une collection hybride. Elle comprend huit étapes :

1.	Choix de l’[image](remoteapp-imageoptions.md) à utiliser pour votre collection. Vous pouvez créer une image personnalisée ou utiliser l’une des images Microsoft incluses dans votre abonnement.
2. Configuration de votre réseau virtuel.
2.	Création d'une collection RemoteApp.
2.	Liaison de votre collection à votre réseau virtuel.
3.	Ajout d'une image de modèle à votre collection.
4.	Configuration d'une synchronisation d'annuaires. RemoteApp exige que vous intégriez Azure Active Directory soit 1) en configurant la synchronisation Azure Active Directory avec l'option de synchronisation de mot de passe ou 2) en configurant Azure Active Directory sans option de synchronisation de mot de passe, mais à l'aide d'un domaine fédéré à AD FS. Consultez les [informations de configuration d'Active Directory avec RemoteApp](remoteapp-ad.md).
5.	Publication d'applications RemoteApp.
6.	Configuration de l'accès utilisateur

**Avant de commencer**

Avant de créer la collection, vous devez effectuer les étapes suivantes :

- [S'inscrire](http://azure.microsoft.com/services/remoteapp/) à RemoteApp. 
- Créer un compte d'utilisateur dans Active Directory à utiliser comme compte de service RemoteApp. Limiter les autorisations pour ce compte, de telle sorte qu'il puisse uniquement joindre des ordinateurs au domaine.
- Collecter des informations sur votre réseau local  : adresse IP et périphérique VPN.
- Installer le module [Azure PowerShell](../install-configure-powershell.md).
- Collecter des informations sur les utilisateurs auxquels vous souhaitez accorder l'accès. Vous aurez besoin du nom d'utilisateur principal Azure Active Directory (par exemple, name@contoso.com) pour chaque utilisateur.
- Choisir votre image de modèle. Une image de modèle RemoteApp contient les applications et les programmes que vous souhaitez publier pour les utilisateurs. Consultez les [options d’images RemoteApp](remoteapp-imageoptions.md) pour plus d'informations. 
- [Configuration d'Active Directory pour RemoteApp](remoteapp-ad.md).



## Étape 1 : configuration de votre réseau virtuel
Vous pouvez déployer une collection RemoteApp hybride qui utilise un réseau virtuel Azure existant ou vous pouvez créer un réseau virtuel. Un réseau virtuel permet aux utilisateurs d'accéder aux données de votre réseau local via des ressources distantes de RemoteApp. L'utilisation d'un réseau virtuel Azure offre à votre collection un accès réseau direct aux autres services Azure et aux machines virtuelles déployées sur ce réseau virtuel.

### Création d'un réseau virtuel Azure et jonction à votre déploiement Active Directory

Commencez par créer un [réseau virtuel](https://msdn.microsoft.com/library/azure/dn631643.aspx). Cette opération s'effectue sous l'onglet **Réseau** dans le portail de gestion Azure. Vous devez connecter votre réseau virtuel au déploiement Active Directory qui est synchronisé avec votre locataire Azure Active Directory.

Consultez [À propos des paramètres de réseau virtuel dans le portail de gestion](https://msdn.microsoft.com/library/azure/jj156074.aspx) pour plus d'informations.

### Vérification que votre réseau virtuel est prêt pour RemoteApp
Avant de créer votre collection RemoteApp, assurez-vous que votre nouveau réseau virtuel est prêt. Vous pouvez le confirmer en procédant comme suit :

1. Créez une machine virtuelle Azure dans le sous-réseau du réseau virtuel que vous venez de créer pour RemoteApp.
2. Utilisez le Bureau à distance pour se connecter à la machine virtuelle. (Cliquez sur **Connecter**.)
3. Joignez-la au même déploiement Active Directory que celui que vous voulez utiliser pour RemoteApp.

Tout s'est bien passé ? Votre réseau virtuel et le sous-réseau sont prêts pour RemoteApp !

Vous trouverez plus d'informations sur la création de machines virtuelles Azure et leur connexion au Bureau à distance [ici](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## Étape 2 : création d'une collection RemoteApp ##



1. Accédez à la page RemoteApp du [portail de gestion Azure](http://manage.windowsazure.com).
2. Cliquez sur **Nouveau > Créer avec VNET**.
3. Entrez un nom pour votre collection.
4. Choisissez le plan que vous souhaitez utiliser (de base ou standard).
5. Cliquez sur **Créer une collection RemoteApp**.

Une fois votre collection RemoteApp créée, accédez à la page **Démarrage rapide** de RemoteApp pour poursuivre la procédure de configuration.

## Étape 3 : liaison de votre collection au réseau virtuel ##

 
1. Sur la page **Démarrage rapide**, cliquez sur **Lier un réseau virtuel**.
2. Choisissez le réseau virtuel que vous voulez utiliser dans la liste déroulante.
3. Choisissez la région à utiliser et assurez-vous que l'abonnement approprié s'affiche dans le champ. 
5. Revenez à la page **Démarrage rapide**, cliquez sur **joindre le domaine local**. Ajoutez le compte de service RemoteApp à votre domaine Active Directory local. Vous aurez besoin du nom de domaine, de l'unité d'organisation, ainsi que du nom d'utilisateur et du mot de passe du compte de service. 

	Voici les informations collectées si vous avez suivi les étapes de la procédure [Configuration d'Active Directory pour Azure RemoteApp](remoteapp-ad.md).


## Étape 4 : liaison à une image RemoteApp ##

Une image de modèle RemoteApp contient les programmes que vous souhaitez partager avec les utilisateurs. Vous pouvez créer une [image de modèle](remoteapp-imageoptions.md) ou un lien vers une image existante (une image déjà importée ou téléchargée vers Azure RemoteApp). Vous pouvez également établir une liaison à l'une des [images de modèle](remoteapp-images.md) RemoteApp contenant des programmes Office 365 ou Office 2013 (à des fins d'évaluation).

Si vous optez pour le téléchargement de la nouvelle image, vous devez entrer un nom et choisir son emplacement. Plusieurs cmdlets PowerShell sont affichés sur la page suivante de l'Assistant. Copiez-les et exécutez-les à partir d'une invite de commandes Windows PowerShell avec élévation de privilèges afin de télécharger l'image spécifiée.

En cas d'association d'une image de modèle existante, il vous suffit de spécifier le nom de l'image, son emplacement et l'abonnement Azure associé.



## Étape 5 : configuration de la synchronisation d'annuaires Active Directory ##

RemoteApp exige que vous intégriez Azure Active Directory soit 1) en configurant la synchronisation Azure Active Directory avec l'option de synchronisation de mot de passe ou 2) en configurant Azure Active Directory sans option de synchronisation de mot de passe, mais à l'aide d'un domaine fédéré à AD FS. Pour plus d'informations sur la planification, consultez la rubrique [Programme de synchronisation d'annuaires](http://msdn.microsoft.com//library/azure/hh967642.aspx).

## Étape 6 : publication d'applications RemoteApp ##

Une application RemoteApp est l'application ou le programme que vous fournissez à vos utilisateurs. Elle se trouve dans l'image de modèle que vous avez téléchargée pour la collection. Quand un utilisateur accède à une application, celle-ci semble s'exécuter dans son environnement local. En réalité, elle s'exécute dans Azure.

Avant que vos utilisateurs puissent accéder à des applications RemoteApp, vous devez les publier dans le flux de l'utilisateur final. Il s'agit de la liste des applications disponibles auxquelles vos utilisateurs peuvent accéder via le client Bureau à distance.
 
Vous pouvez publier plusieurs applications dans votre collection RemoteApp. Sur la page de publication de RemoteApp, cliquez sur **Publier** pour ajouter une application. Vous pouvez publier l'application à partir du menu Démarrer de l'image de modèle ou en indiquant le chemin d'accès dans l'image de modèle de l'application. Si vous choisissez la première option, sélectionnez le programme à publier. Si vous choisissez la deuxième option, indiquez un nom pour l'application ainsi que le chemin d'accès à son répertoire d'installation dans l'image de modèle.

## Étape 7 : configuration de l'accès utilisateur ##

Maintenant que vous avez créé votre collection RemoteApp, vous devez ajouter les utilisateurs qui seront autorisés à utiliser vos ressources distantes. Ceux-ci doivent se trouver dans le locataire Active Directory associé à l'abonnement que vous avez utilisé pour créer cette collection RemoteApp.

1.	Sur la page Démarrage rapide, cliquez sur **Configurer l'accès utilisateur**. 
2.	Entrez le compte professionnel (à partir d'Active Directory) ou le compte Microsoft auquel vous souhaitez accorder l'accès.

	**Remarques :**

	assurez-vous d'utiliser le format « utilisateur@domaine.com ».

	Si vous utilisez Office 365 ProPlus dans votre collection, vous devez utiliser les identités Active Directory de vos utilisateurs. Cela permet de valider la licence.


3.	Une fois les utilisateurs validés, cliquez sur **Enregistrer**.


## Étapes suivantes ##
Félicitations ! Vous avez créé et déployé correctement votre collection hybride RemoteApp. L'étape suivante consistera à faire en sorte que vos utilisateurs téléchargent et installent le client Bureau à distance. L'URL du client est disponible sur la page Démarrage rapide de RemoteApp. Les utilisateurs peuvent à présent se connecter au client et accéder aux applications que vous avez publiées.


 

<!---HONumber=July15_HO4-->