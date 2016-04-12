<properties
	pageTitle="Vérifier l’utilisation des applications dans PowerApps Enterprise | Microsoft Azure"
	description="Afficher l’ensemble des applications, des API, des utilisateurs, des demandes d’applications et des autorisations de mise à jour dans le portail Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="litran"/>


# Gérer et sécuriser PowerApps
Quand vous créez un environnement App Service et ajoutez des API avec leurs connexions, vous permettez aux utilisateurs de votre organisation d’utiliser ces API et ces connexions. Vous pouvez également gérer toutes les applications créées dans votre organisation. Ces options sont les suivantes :

- Afficher les différentes applications de votre environnement App Service, notamment PowerApps, les applications web, les applications logiques, les applications mobiles et bien plus encore.
- Afficher toutes les API utilisées par certaines applications.
- Afficher et gérer l’accès utilisateur aux applications de l’environnement App Service. 
- Afficher et gérer l’accès aux API et à leurs connexions. 

Vous pouvez ajouter d’autres applications à votre environnement App Service, telles que des applications web et logiques. Vous pouvez ensuite ouvrir PowerApps Enterprise pour afficher et gérer ces applications.


## Ajouter des administrateurs PowerApps
Une fois PowerApps Enterprise activé et prêt à être utilisé, vous pouvez ajouter des administrateurs et surveiller les autres applications de votre environnement App Service.

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez **PowerApps**.
2. Sélectionnez **Paramètres**.
3. Dans **Paramètres**, sélectionnez **Administrateur** : ![][1]  
4. Dans **Utilisateurs**, sélectionnez **Ajouter**.
5. Sélectionnez le rôle **Propriétaire** : ![][2]  

	> [AZURE.IMPORTANT] Quand vous désignez un utilisateur comme administrateur PowerApps, attribuez-lui le rôle de **Propriétaire**. Les autres rôles ne donnent pas un accès complet pour la gestion de PowerApps.

6. Sélectionnez des utilisateurs ou des groupes.
7. Sélectionnez **OK** pour terminer les étapes.

Quand vous ajoutez des administrateurs à PowerApps Enterprise, les utilisateurs et les groupes que vous ajoutez en tant qu’administrateurs :

- peuvent ajouter d’autres utilisateurs en tant qu’administrateurs PowerApps ;
- peuvent gérer toutes les applications, ainsi que leur accès utilisateur ;
- ne peuvent pas modifier la facturation.

> [AZURE.IMPORTANT] Les administrateurs PowerApps ne peuvent pas modifier l’environnement App Service si le rôle Propriétaire ne leur est pas attribué dans le groupe de ressources de l’environnement App Service. Pour ce faire, consultez [Qu’est-ce que Microsoft PowerApps Enterprise ?](powerapps-get-started-azure-portal.md).

Une fois que le rôle Propriétaire leur est attribué dans le groupe de ressources de l’environnement App Service, les administrateurs PowerApps peuvent également :

- créer et configurer des API et leurs connexions ;
- modifier les paramètres de PowerApps, notamment l’environnement App Service ;
- ajouter d’autres utilisateurs et groupes, et leur attribuer des rôles et des autorisations pour les API, leurs connexions et l’environnement App Service. 


## Gérer PowerApps et les autres types d’applications
Une fois que vous activez PowerApps et votre environnement App Service, vous pouvez ajouter d’autres applications, telles que des applications web et des applications logiques, au même environnement App Service. Une fois ajoutées, les applications sont répertoriées sous *Toutes les applications* avec les applications créées dans PowerApps. Vous pouvez cliquer sur chaque type d’application pour parcourir les différentes applications.

### Afficher et gérer PowerApps

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez **PowerApps**.
2. Dans la mosaïque **Toutes les applications**, sélectionnez **PowerApps** : ![][3]  
3. Sélectionnez une application pour afficher les détails la concernant, notamment :  
	- Les API qu’utilise l’application
	- Les utilisateurs et les groupes qui ont accès à l’application 
	- Les données d’analyse sur l’application (bientôt disponible)

#### Ajouter une application

Vous ne pouvez pas ajouter d’applications dans le portail Azure. Vous devez les ajouter dans le [portail PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715583).

#### Supprimer des applications créées dans PowerApps
En tant qu’administrateur PowerApps, vous pouvez supprimer n’importe quelle application, y compris celles créées dans PowerApps et les autres types d’applications de votre environnement App Service. Pour supprimer une application, sélectionnez la mosaïque **Toutes les applications**, sélectionnez l’application à supprimer, puis sélectionnez **Supprimer** : ![][4]


#### Attribuer aux utilisateurs et aux groupes l’accès à une application
En tant qu’administrateur PowerApps, vous pouvez ajouter et supprimer des utilisateurs et des groupes dans PowerApps.

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez **PowerApps**.
2. Dans la mosaïque **Toutes les applications**, sélectionnez **PowerApps** : ![][3]  
3. Sélectionnez une application, telle que **Service Desk**. 
4. Dans **Paramètres**, sélectionnez **Accès utilisateur à l’application** : ![][5]  
5. Pour ajouter un utilisateur ou un groupe, sélectionnez **Ajouter**. 
6. Sélectionnez un rôle :  
	- Modifier
	- Afficher
7. Sélectionnez les utilisateurs ou les groupes.
8. Sélectionnez **OK** pour terminer les étapes.

### Afficher et gérer des applications logiques

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez **PowerApps**.
2. Dans la mosaïque **Toutes les applications**, sélectionnez **Applications logiques** : ![][8]  
3. Sélectionnez une application logique pour afficher les détails la concernant. Veillez à sélectionner le bon abonnement PowerApps pour afficher les applications logiques appropriées : ![][7]  

	> [AZURE.IMPORTANT] Dans la version préliminaire publique, vous pourrez peut-être remarquer une incohérence entre le nombre d’applications logiques qui s’affiche dans le panneau de navigation et celui qui s’affiche dans le panneau PowerApps principal. Ceci est normal. En effet, le portail affiche l’ensemble des applications logiques des plans d’hébergement, sans filtrer les applications logiques de l’environnement App Service déployées pour PowerApps. Ce problème sera résolu dans une prochaine mise à jour.

	**Pour en savoir plus sur les applications logiques et leur gestion, consultez [ces instructions](https://azure.microsoft.com/documentation/services/app-service/logic/).**

### Afficher et gérer des applications web

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez **PowerApps**.
2. Dans la mosaïque **Toutes les applications**, sélectionnez **Applications web** : ![][9]  

	**Pour plus d’informations sur les applications web et leur gestion, consultez [ces instructions](https://azure.microsoft.com/documentation/services/app-service/web/).**

### Afficher et gérer des applications mobiles

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez **PowerApps**.
2. Dans la mosaïque **Toutes les applications**, sélectionnez **Applications mobiles** : ![][10]  

	**Pour plus d’informations sur les applications mobiles et leur gestion, consultez [ces instructions](https://azure.microsoft.com/documentation/services/app-service/mobile/).**


## Passer en revue les options de sécurité
Différentes méthodes de sécurité peuvent être utilisées selon ce que vous faites. Voici ce que vous devez savoir :

- **Administrateurs d’abonnement**. Ces administrateurs contrôlent la facturation et sont chargés de l’inscription de votre société à PowerApps Enterprise. Seuls les administrateurs d’abonnement peuvent demander à activer PowerApps dans le cadre de l’abonnement Azure de votre société. 

- **Accès utilisateur au runtime**. Il existe trois types d’accès utilisateur au runtime :
	- **Accès utilisateur à l’application** : cette autorisation permet de décider si l’utilisateur peut *modifier* l’application ou *afficher* l’application.
	- **Accès utilisateur à l’API** : cette autorisation permet de contrôler l’accès au runtime. Si les utilisateurs disposent de cette autorisation, ils peuvent utiliser l’API dans leur application. Les utilisateurs disposent ou ne disposent pas de l’autorisation d’utiliser l’API lors de l’exécution. 
	- **Accès utilisateur à la connexion** : les autorisations de runtime disponibles pour les connexions sont *Afficher* et *Modifier*. Quand vous ajoutez une API (ou un profil de connexion) et créez sa connexion, vous accordez aux utilisateurs et aux groupes les autorisations suivantes : ![][6]  

		Par exemple, vous pouvez accorder au groupe Ventes de votre société l’autorisation *Modifier* pour une connexion d’API Connecteur SQL. Les utilisateurs disposant de l’autorisation *Modifier* peuvent utiliser la connexion dans leurs applications et modifier la configuration de la connexion. Les utilisateurs disposant de l’autorisation *Afficher* peuvent utiliser la connexion dans leurs applications, mais ne peuvent pas modifier la configuration de la connexion, comme, par exemple, la chaîne de connexion.

- **Contrôle d’accès en fonction du rôle** (RBAC) : de nombreuses offres Azure utilisent le contrôle d’accès en fonction du rôle pour déterminer les actions autorisées à chaque utilisateur. Dans PowerApps, le contrôle d’accès en fonction du rôle est utilisé à deux endroits différents :
	- Quand vous ouvrez le portail PowerApps pour la première fois, vous pouvez ajouter et gérer les utilisateurs qui ont un rôle d’administrateur PowerApps. 
	- Quand vous créez l’environnement App Service, vous pouvez ajouter et supprimer des utilisateurs et des groupes dans PowerApps. Par exemple, vous pouvez attribuer à certains groupes d’administrateurs de votre société le rôle de *Propriétaires*, ce qui leur permet de créer des API et des connexions. Ces API et ces connexions sont alors ajoutées aux applications créées dans PowerApps.
	- Quand vous ajoutez des utilisateurs aux applications telles que les applications web, logiques ou mobiles. Vous pouvez choisir le rôle de ces utilisateurs.  
		
		L’ajout d’utilisateurs et l’attribution de rôles reviennent à utiliser le [contrôle d’accès en fonction du rôle](../role-based-access-control-configure.md) dans Azure. Voici quelques exemples de rôles :

		Rôle | Description
		--- | ---
		Collaborateur | Peut tout gérer à l’exception des autorisations d’accès utilisateur.
		Lecteur | Peut tout afficher, mais ne peut pas effectuer de modifications.
		Propriétaire | Peut tout gérer et accorder des autorisations d’accès aux utilisateurs.

Grâce à ces rôles, vous pouvez attribuer à l’utilisateurA une autorisation **Afficher** pour une application Twitter, et attribuer à l’utilisateurB une autorisation **Modifier** pour l’application ShuttleBus. Vous pouvez accorder à l’utilisateurB l’accès à toutes les API. Vous pouvez accorder à chacun des utilisateurs un rôle spécifique. Cela dépend des besoins de votre entreprise.


## Résumé et étapes suivantes
Dans cette rubrique, vous avez vu les différentes options permettant de gérer PowerApps, ainsi que les méthodes de sécurité implémentées dans PowerApps.

Maintenant que votre portail Azure est configuré, passons à la création d’applications. Les rubriques suivantes constituent un bon point de départ :

- [Créer une application à partir d’un modèle dans PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715536) 
- [Créer une application à partir de données dans PowerApps](http://go.microsoft.com/fwlink/?LinkId=715539) 
- [Créer une application à partir de zéro dans PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715538)


[1]: ./media/powerapps-manage-monitor-usage/addadmin.png
[2]: ./media/powerapps-manage-monitor-usage/selectrole.png
[3]: ./media/powerapps-manage-monitor-usage/PowerApps.png
[4]: ./media/powerapps-manage-monitor-usage/deleteapp.png
[5]: ./media/powerapps-manage-monitor-usage/appuseraccess.png
[6]: ./media/powerapps-manage-monitor-usage/selectpermission.png
[7]: ./media/powerapps-manage-monitor-usage/alllogicapps.png
[8]: ./media/powerapps-manage-monitor-usage/logicapps.png
[9]: ./media/powerapps-manage-monitor-usage/webapps.png
[10]: ./media/powerapps-manage-monitor-usage/mobileapps.png

<!---------HONumber=AcomDC_0309_2016-->