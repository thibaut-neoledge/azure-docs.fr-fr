<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="Création d'un déploiement hybride de RemoteApp" description="Découvrez comment créer un déploiement de RemoteApp qui se connecte à votre réseau interne." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="elizapo" ms.manager="kathyw" />

#Création d'un déploiement hybride de RemoteApp

Il existe deux types de déploiement RemoteApp : 

- Cloud : réside complètement dans Azure et est créé à l'aide de l'option **Création rapide** du portail de gestion Azure.  
- Hybride : comprend un réseau virtuel pour un accès local et est créé à l'aide de l'option **Créer avec VPN** du portail de gestion.

Ce didacticiel vous familiarise avec la procédure de création d'un déploiement hybride. Il se compose de sept étapes : 

1.	Création d'une [image de modèle pour RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-custom-image/).
2.	Création d'un service RemoteApp
2.	Liaison avec un réseau virtuel
3.	Association d'une image de modèle
4.	Configuration d'une synchronisation d'annuaires. RemoteApp exige que vous intégriez Azure Active Directory soit 1) en configurant la synchronisation d'annuaire avec l'option de synchronisation de mot de passe ou 2) en configurant la synchronisation d'annuaire sans synchronisation de mot de passe, mais à l'aide d'un domaine est AD fédéré.
5.	Publication de programmes RemoteApp
6.	Configuration de l'accès utilisateur

**Avant de commencer**

Avant de créer le service, vous devez effectuer les étapes suivantes :

- Vous inscrire pour bénéficier de la version préliminaire de RemoteApp. Pour ce faire, rendez-vous à l'adresse suivante [http://azure.microsoft.com/fr-fr/services/remoteapp/](http://azure.microsoft.com/fr-fr/services/remoteapp/).
- Créer un compte d'utilisateur dans Active Directory à utiliser comme compte de service RemoteApp. Limiter les autorisations pour ce compte, de telle sorte qu'il puisse uniquement joindre des ordinateurs au domaine.
- Collecter des informations sur votre réseau local : informations sur l'adresse IP et détails du périphérique VPN.
- Installer le module [Azure PowerShell](http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/).
- Collecter des informations sur les utilisateurs et groupes auxquels vous souhaitez accorder l'accès. Il peut s'agir d'informations sur le compte Microsoft ou sur le compte professionnel Active Directory pour les utilisateurs ou les groupes.
- Création de votre image de modèle Une image de modèle RemoteApp contient les applications et les programmes que vous souhaitez publier pour les utilisateurs. Consultez la page [Création d'une image de modèle personnalisée pour RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-custom-image/) pour des instructions détaillées. 






## **Étape 1 : Création d'un service RemoteApp** ##



1. Accédez à la page RemoteApp du [portail de gestion Azure](http://manage.windowsazure.com).
2. Cliquez sur **Nouveau > Créer avec VPN**.
3. Entrez le nom de votre service, puis cliquez sur **Créer un service RemoteApp**.

Après avoir créé votre service RemoteApp, accédez à la page **Démarrage rapide** de RemoteApp pour passer aux étapes de configuration.

## **Étape 2 : Liaison avec un réseau virtuel**

Un réseau virtuel permet aux utilisateurs d'accéder aux données de votre réseau local via des ressources distantes de RemoteApp. La configuration de votre liaison au réseau virtuel se décompose en quatre étapes :

1. Sur la page Démarrage rapide, cliquez sur **Lier à un réseau virtuel RemoteApp**. 
2. Indiquez si vous souhaitez créer un réseau virtuel ou utiliser un réseau existant. Pour les besoins de ce didacticiel, nous allons créer un réseau.
3. Indiquez les informations suivantes pour votre nouveau réseau :  
      - Nom
      - Espace d'adressage du réseau virtuel
      - Espace d'adressage local
      - Adresse IP du serveur DNS
      - Adresse IP du réseau privé virtuel (VPN)

	Pour plus d'informations, consultez la page [Configurer un VPN de site à site dans le portail de gestion](http://msdn.microsoft.com/library/azure/dn133795.aspx).

4. De retour sur la page Démarrage rapide, cliquez sur **get script** afin de télécharger un script permettant de configurer votre périphérique VPN pour qu'il se connecte au réseau virtuel que vous venez de créer. Vous aurez besoin des informations suivantes sur le périphérique VPN : 
	- Fournisseur
	- Plateforme
	- Système d'exploitation

	Enregistrez le script et exécutez-le sur le périphérique VPN. 

	**Remarque :** une fois ce script exécuté, le réseau virtuel passe à l'état " Prêt ". Cette opération peut prendre entre 5 et 7 minutes. Vous ne pourrez pas utiliser le réseau tant qu'il ne sera pas prêt.

5. Enfin, de retour sur la page Démarrage rapide, cliquez sur **joindre le domaine local**. Ajoutez le compte de service RemoteApp à votre domaine Active Directory local. Vous aurez besoin du nom de domaine, de l'unité d'organisation, ainsi que du nom d'utilisateur et du mot de passe du compte de service.


## **Étape 3 : Liaison avec une image de modèle RemoteApp**

Une image de modèle RemoteApp contient les programmes que vous souhaitez partager avec les utilisateurs. Vous pouvez télécharger la nouvelle image de modèle que vous avez créée ([Création d'une image de modèle personnalisée pour RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-custom-image/)) ou créer un lien vers une image existante (déjà téléchargée sur Azure).

Si vous optez pour le téléchargement de la nouvelle image, vous devez entrer un nom et choisir son emplacement. Plusieurs cmdlets PowerShell sont affichés sur la page suivante de l'Assistant. Copiez-les et exécutez-les à partir d'une invite de commandes Windows PowerShell avec élévation de privilèges afin de télécharger l'image spécifiée.

En cas d'association d'une image de modèle existante, il vous suffit de spécifier le nom de l'image, son emplacement et l'abonnement Azure associé.



## **Étape 4 : Configuration de la synchronisation d'annuaires Active Directory**

RemoteApp exige que vous intégriez Azure Active Directory soit 1) en configurant la synchronisation d'annuaire avec l'option de synchronisation de mot de passe ou 2) en configurant la synchronisation d'annuaire sans synchronisation de mot de passe, mais à l'aide d'un domaine est AD fédéré. Pour plus d'informations sur la planification, consultez la rubrique [Programme de synchronisation d'annuaires](http://msdn.microsoft.com//library/azure/hh967642.aspx).

## **Étape 5 : Publication de programmes RemoteApp** ##

Un programme RemoteApp est l'application ou le programme mis à la disposition de vos utilisateurs. Il se situe dans l'image de modèle que vous avez téléchargée pour le service. Lorsqu'un utilisateur accède à un programme RemoteApp, celui-ci semble s'exécuter dans son environnement local. En réalité, il s'exécute dans Azure. 

Avant que vos utilisateurs puissent accéder à des programmes RemoteApp, vous devez les publier sur le flux de l'utilisateur final ; il s'agit d'une liste des programmes disponibles auxquels vos utilisateurs peuvent accéder via le portail Azure.
 
Vous pouvez publier plusieurs programmes sur votre service RemoteApp. Sur la page des programmes RemoteApp, cliquez sur **Publier** afin d'ajouter un programme. Vous pouvez publier le programme à partir du menu Démarrer de l'image de modèle ou en indiquant le chemin sur l'image de modèle du programme. Si vous choisissez la première option, sélectionnez le programme à publier. Si vous optez pour la deuxième solution, indiquez le nom du programme, ainsi que le chemin d'accès au répertoire d'installation sur l'image de modèle.

## **Étape 6 : Configuration de l'accès utilisateur** ##

Maintenant que vous avez créé votre service RemoteApp, vous devez ajouter les utilisateurs et groupes qui seront autorisés à utiliser vos ressources distantes. Ceux-ci doivent se trouver dans le client Active Directory associé à l'abonnement que vous avez utilisé pour créer ce service RemoteApp.

1.	Sur la page Démarrage rapide, cliquez sur **Configurer l'accès utilisateur**. 
2.	Entrez le nom du groupe ou du compte professionnel (à partir d'Active Directory) ou le compte Microsoft auquel vous souhaitez accorder l'accès.

	Pour les utilisateurs, veillez à utiliser le format " utilisateur@domaine.com ". Pour les groupes, entrez le nom de groupe.

3.	Une fois les utilisateurs ou groupes validés, cliquez sur **Enregistrer**.


## Étapes suivantes ##
Félicitations ! Vous avez créé et déployé correctement votre déploiement hybride RemoteApp. L'étape suivante consistera à faire en sorte que vos utilisateurs téléchargent et installent le client Bureau à distance. L'URL du client est disponible sur la page Démarrage rapide de RemoteApp. Les utilisateurs devront ensuite se connecter au client et accéder aux programmes RemoteApp que vous avez publiés.


