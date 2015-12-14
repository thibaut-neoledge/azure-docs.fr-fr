<properties
	pageTitle="Qu’est-ce que PowerApps Enterprise et comment démarrer | Microsoft Azure"
	description="Prise en main de PowerApps Enterprise et création d’un App Service Environment"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>

# Qu’est-ce que Microsoft PowerApps Enterprise ?

Microsoft PowerApps Enterprise est un nouveau service Microsoft Azure. PowerApps Enterprise étend la création d’applications mobiles aux utilisateurs professionnels au sein de votre société et permet aux administrateurs informatiques de gérer étroitement ces applications.

À l’aide d’une interface de type Office avec des rubans et des formules Excel, les utilisateurs professionnels peuvent créer des applications qui permettent d’effectuer les actions suivantes :

- Afficher les données à l’aide de graphiques en courbes, en secteurs et à barres, tout comme vous pouvez le faire dans Excel.
- Créer des interfaces utilisateur qui incluent des boutons, insérer du texte et mettre en forme une date.
- Ajouter des contrôles à choix multiple, notamment des zones de liste, des listes déroulantes et des cases d’option.
- Charger des images, prendre des photos et lire des fichiers audio et vidéo.
- Utiliser des systèmes « principaux », comme Excel et SQL Server pour afficher et mettre à jour les informations.
- Ajouter des connecteurs App Service préintégrés à vos applications qui se connectent à des programmes PaaS comme Twitter et SharePoint.

Les administrateurs informatiques peuvent gérer les applications créées par les utilisateurs professionnels dans leur entreprise, notamment :

- Gérer ces applications et gérer l’accès des utilisateurs à ces applications.
- Créer des API et des connexions aux sources de données différentes. 
- Gérer l’accès des utilisateurs pour les API et les connexions à ces sources de données. 

## Comment faire pour démarrer ?

Tout d’abord, déterminez si vous devez créer un client Azure Active Directory (Azure AD). Si vous disposez déjà d’un client AD, activez simplement PowerApps Enterprise dans le portail Azure, ajoutez vos API et connexions, et commencez la gestion (commencez à l’*étape 2* de cette rubrique).

Si vous ne disposez pas d’un client AD, créez-en un, activez PowerApps Enterprise dans le portail Azure, ajoutez vos API et connexions, et commencez la gestion.

Cette rubrique présente les éléments spécifiques.

## Étape 1 : Créer un client Azure AD ou utiliser un client Azure AD existant

Pour commencer avec PowerApps Enterprise, vous avez besoin d’un client Azure Active Directory (Azure AD). Un client est une instance dédiée du service Azure AD.

Lorsque votre organisation ou votre société s’inscrit à un service cloud Microsoft Azure comme Microsoft Intune ou Office 365, automatiquement, elle reçoit et possède un client AD. Chaque client AD est distinct et indépendant des autres clients Azure AD.

Utilisez la procédure suivante pour déterminer si vous disposez déjà d’un client ou pour savoir comment en créer un.

#### Vous avez un abonnement Office 365 en cours
Si vous possédez un abonnement Office 365 en cours (ou Microsoft Dynamic CRM Online, Enterprise Mobility Suite ou autres services Microsoft), vous disposez d’un abonnement gratuit à Azure Active Directory. Vous pouvez utiliser Azure AD pour créer et gérer des comptes d’utilisateur et de groupe. Si vous ne pouvez pas vous connecter au portail Azure, il faut sans doute que vous activiez l’abonnement. Pour ce faire, accédez au [Portail Azure Classic](https://manage.windowsazure.com/) et procédez à l’inscription unique. Utilisez cette [procédure](https://technet.microsoft.com/library/dn832618.aspx) pour accéder à votre client Azure AD.

#### Vous avez un abonnement Azure en cours associé à un compte Microsoft
Si vous vous êtes déjà inscrit à un abonnement Azure avec votre compte Microsoft individuel (hotmail ou live), vous avez déjà un client ! Dans le [Portail Azure Classic](https://manage.windowsazure.com/), **Client par défaut** est répertorié sous **Tous les éléments** et sous **Active Directory**. Vous êtes libre d'utiliser ce client selon vos besoins, mais vous avez la possibilité de créer un compte d'administrateur professionnel.

Pour ce faire, procédez comme suit. Autrement, vous pouvez créer un nouveau client et créer un administrateur dans ce client suivant un processus similaire.

1.	Connectez-vous au [Portail Azure Classic ](https://manage.windowsazure.com/) avec votre compte individuel.
2.	Dans la barre de menu de gauche, sélectionnez **Active Directory**. 
3.	Sélectionnez **Répertoire par défaut** dans la liste des répertoires disponibles.
4.	Sélectionnez l’onglet **Utilisateurs** en haut. Un seul utilisateur apparaît avec la mention « Compte Microsoft » dans la colonne Provenance.
5.	Sélectionnez **Ajouter un utilisateur** en bas. 
6.	Dans le **formulaire Ajouter un utilisateur**, fournissez les informations suivantes :  
	
	Propriété | Description
--- | ---
Type d’utilisateur | Nouvel utilisateur dans votre organisation
Nom d'utilisateur | Choisissez un nom d’utilisateur pour cet administrateur
Prénom/Nom/Nom d’affichage | Entrez vos valeurs
Rôle | Administrateur général
Autre adresse de messagerie | Entrez votre valeur
Facultatif | Activez Multi-Factor Authentication  
	
	Sélectionnez le bouton **CRÉER** pour terminer et pour afficher le mot de passe temporaire.

Lorsque vous avez terminé, notez ce mot de passe temporaire pour le nouvel utilisateur administratif. Pour modifier le mot de passe temporaire, connectez-vous à [https://login.microsoftonline.com](https://login.microsoftonline.com) avec ce nouveau compte d’utilisateur et modifiez le mot de passe. Vous pouvez également envoyer le mot de passe directement à l'utilisateur, à l'aide d'un autre e-mail.


#### Vous avez un abonnement Azure en cours associé à un compte professionnel
Si vous vous êtes déjà inscrit à un abonnement Azure avec votre compte professionnel, vous avez déjà un client. Dans le [Portail Azure Classic](https://manage.windowsazure.com/), le client est répertorié sous **Tous les éléments** et également sous **Active Directory**. Vous êtes libre d'utiliser ce client selon vos besoins. Vous pouvez également créer un client à l’aide du menu **Nouveau** dans la barre des tâches en bas.

#### Vous n’avez aucun des éléments ci-dessus et vous souhaitez commencer à partir de zéro
Si aucune des situations ci-dessus ne s’applique à vous, accédez à la page [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) pour vous inscrire à Azure avec une nouvelle organisation. Une fois inscrit, vous disposez de votre propre client Azure AD avec le nom de domaine que vous avez choisi. Dans le [Portail Azure Classic](https://manage.windowsazure.com/), vous pouvez voir le client dans **Active Directory** dans le menu de gauche.

## Étape 2 : Créer un abonnement Azure ou utiliser un abonnement Azure en cours
Maintenant que vous avez votre client AD, vous pouvez créer un abonnement Azure ou en utiliser un en cours. L’abonnement Azure AD comprend plusieurs éditions. Pour PowerApps Enterprise, vous pouvez utiliser l’édition gratuite. Toutefois, si vous devez utiliser le proxy AAD pour créer une connexion hybride aux données locales, vous avez besoin de l’édition De base ou Premium.

La page [Éditions d’Azure Active Directory](../active-directory/active-directory-editions.md) répertorie plus de fonctionnalités.


## Étape 3 : S’inscrire à PowerApps Enterprise dans votre abonnement Azure professionnel
> [AZURE.NOTE]Les étapes suivantes requièrent que l’administrateur d’abonnements se connecte au portail Azure et envoie une demande.

Maintenant que vous disposez de votre client AD et d’un abonnement Azure, l’administrateur de votre abonnement professionnel peut s’inscrire à PowerApps Enterprise. L’administrateur peut également ajouter des utilisateurs dans votre société pour administrer PowerApps, notamment pour accorder des autorisations aux utilisateurs et gérer les éléments PowerApps publiés dans votre abonnement Azure.

Sans inscription à PowerApps Enterprise, un panneau d’accès refusé s’affiche lorsque vous accédez au [portail Azure](https://portal.azure.com/) et que vous recherchez PowerApps. Pour inscrire votre société, l’**administrateur d’abonnements** peut accéder à [PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=716848) pour nous contacter afin d’en savoir plus sur la tarification et le processus d’inscription.

![][4]

Une fois que vous avez terminé le processus d’inscription et que vous êtes prêt à utiliser PowerApps Enterprise, vous pouvez :

- Ajouter des utilisateurs au sein de votre société et, à l’aide du [contrôle d’accès en fonction du rôle](../role-based-access-control-configure.md), attribuer à ces utilisateurs des rôles d’administrateurs PowerApps pour l’accès au portail PowerApps Enterprise.
- Créer un App Service Environment dédié pour héberger vos PowerApps.
- Créer des API et connexions s’exécutant au sein de votre App Service Environment dédié.
- Outre les applications créées dans PowerApps, vous pouvez ajouter des applications supplémentaires à votre App Service Environment, notamment des applications web, des applications mobiles, des API Apps et des Logic Apps.

Dans l’exemple suivant, la société Contoso s’est inscrite à PowerApps. Dans ce nouveau panneau **PowerApps**, vous pouvez afficher un résumé des différents types d’applications créés à l’aide de cet App Service Environment. Dans **Gérer les API**, vous pouvez afficher un résumé des API créées par Microsoft (gérées par Microsoft) et voir les API créées par Contoso (gérées par le service informatique) :

![Exemple de panneau d’entreprise PowerApps][3]


## Étape 4 : Créer un App Service Environment
Créez un App Service Environment pour héberger vos API et connexions PowerApps, ainsi que les applications mobiles, applications web, API Apps et Logic Apps.

Un App Service Environment est un environnement isolé et dédié qui exécute l’ensemble de vos applications en toute sécurité. Les ressources de calcul sont par App Service Environment et sont exclusivement dédiées à l’exécution de vos applications uniquement. Lorsque vous vous inscrivez à PowerApps Enterprise, un App Service Environment dédié est utilisé pour héberger les API et connexions utilisées par vos applications. Cet App Service Environment est un type spécial d’App Service Environment. Plus précisément :

- Vous pouvez utiliser cet App Service Environment comme bon vous semble. Il est lié à votre société, et non à l’abonnement.
- Vous configurez les API et connexions à utiliser par vos applications créées dans PowerApps. Toutefois, vous pouvez également ajouter des applications web, des applications mobiles, des Logic Apps et des API Apps à ce même App Service Environment. 
- La facturation est fixe et incluse avec PowerApps Enterprise.  
- La mise à l’échelle est automatiquement gérée pour vous. Vous n’avez pas à surveiller l’environnement pour déterminer si des ressources de calcul supplémentaires sont nécessaires.

L’environnement normal Azure App Service Environment présente des fonctionnalités différentes. Consultez la page [Présentation de l’environnement App Service](../app-service-app-service-environment-intro.md) pour découvrir ces détails.

#### Conditions préalables requises

- Abonnement d’entreprise Azure
- L’administrateur d’abonnements de votre société [a inscrit votre société pour PowerApps](powerapps-get-started-azure-portal.md) Enterprise.
- Vous êtes connecté au portail Azure en tant qu’administrateur PowerApps (« propriétaire » de PowerApps) ou en tant qu’administrateur d’abonnements.

### Créer un App Service Environment
> [AZURE.NOTE]Si vous ne voyez pas l’option de création de l’App Service Environment, il est déjà créé pour votre client. Pour afficher les détails, sélectionnez **Paramètres** pour ouvrir l’App Service Environment.

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide d’une adresse *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Dans la barre des tâches, sélectionnez **Parcourir** : ![Accéder à PowerApps][1]
  
3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou saisir *powerapps* : ![Rechercher PowerApps][2]

4. Dans le panneau **PowerApps**, sélectionnez **Créer un App Service Environment pour commencer** ou sélectionnez **App Service Environment** sous *Paramètres* : ![][5]

	> [AZURE.NOTE]Si vous cliquez sur **Créer un App Service Environment pour commencer**, un panneau supplémentaire s’affiche avec des détails sur l’App Service Environment. Cliquez simplement sur Créer un lien dans ce panneau pour lancer le panneau de création.

5. Ensuite, entrez le nom, sélectionnez l’abonnement que vous souhaitez utiliser, sélectionnez ou créez un groupe de ressources et sélectionnez un réseau virtuel. **Veuillez noter** qu’après avoir choisi un réseau virtuel, vous ne pourrez pas le modifier : ![][6] Pour savoir comment les réseaux virtuels fonctionnent avec un App Service Environment, consultez [Comment créer un environnement App Service](../app-service-web-how-to-create-an-app-service-environment.md).

6. Sélectionnez **Ajouter** pour terminer la création de l’App Service Environment.

> [AZURE.TIP]Lors de la création de l’App Service Environment à l’aide de PowerApps, vous n’êtes pas invité à configurer des pools de ressources de calcul. Cette étape est gérée automatiquement.

N’oubliez pas : vous pouvez également ajouter des applications web, des applications mobiles et des API Apps à cet App Service Environment. En fait, il s’agit de votre environnement pour ajouter tout ce qu’App Service Environment prend en charge.

### Ajouter des administrateurs pour gérer l’App Service Environment

Pour obtenir l’accès à l’App Service Environment, créer des API, des connexions et d’autres ressources, les utilisateurs doivent être ajoutés avec le rôle de propriétaire.

1. Sélectionnez l’App Service Environment que vous venez de créer.
2. Dans Essentials, sélectionnez la propriété **Groupe de ressources**. Cette action ouvre le groupe de ressources qui contient l’App Service Environment : ![][7]
3. Sélectionnez l’icône RBAC pour gérer les autorisations : ![][8] L’ajout d’utilisateurs et l’attribution de rôles reviennent à utiliser le [contrôle d’accès en fonction du rôle](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure) dans Azure.

> [AZURE.NOTE]Actuellement, vous ne pouvez pas accorder des autorisations RBAC à l’App Service Environment. Vous pouvez accorder des autorisations RBAC au niveau du groupe de ressources parent.

## Résumé et étapes suivantes
Votre société est désormais inscrite à PowerApps et possède un App Service Environment. Ensuite, vous pouvez ajouter des API et des connexions qui peuvent être utilisées par vos applications.

- [Surveiller vos applications PowerApps](powerapps-manage-monitor-usage.md)
- [Développer une API pour PowerApps](powerapps-develop-api.md)
- [Ajouter une nouvelle API, ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)
- [Mettre à jour une API existante et ses propriétés](powerapps-configure-apis.md)


[1]: ./media/powerapps-get-started-azure-portal/browseall.png
[2]: ./media/powerapps-get-started-azure-portal/allresources.png
[3]: ./media/powerapps-get-started-azure-portal/powerappsblade.png
[4]: ./media/powerapps-get-started-azure-portal/noaccess.png
[5]: ./media/powerapps-get-started-azure-portal/createase.png
[6]: ./media/powerapps-get-started-azure-portal/aseproperties.png
[7]: ./media/powerapps-get-started-azure-portal/aseessentials.png
[8]: ./media/powerapps-get-started-azure-portal/resourcegrouprbac.png

<!---HONumber=AcomDC_1203_2015-->