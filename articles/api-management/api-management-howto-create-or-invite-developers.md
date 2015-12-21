<properties 
	pageTitle="Gestion des comptes d’utilisateur dans Gestion des API Azure" 
	description="Apprenez à créer ou à inviter des utilisateurs dans Gestion des API Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="sdanie"/>

# Gestion des comptes d’utilisateur dans Gestion des API Azure

Dans Gestion des API Azure, les développeurs sont les utilisateurs des API que vous exposez via Gestion des API. Ce guide vous montre comment créer et inviter des développeurs à utiliser les API et les produits que vous mettez à leur disposition dans votre instance Gestion des API.

## <a name="create-developer"> </a>Création d’un développeur

Pour créer un développeur, cliquez sur **Gérer** dans le portail Azure Classic de votre service Gestion des API. Vous accédez au portail des éditeurs Gestion des API. Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

![Portail des éditeurs][api-management-management-console]

Cliquez sur **Utilisateurs** dans le menu **Gestion des API** à gauche, puis sur **Ajouter un utilisateur**.

![Create developer][api-management-create-developer]

Entrez l'**adresse électronique**, le **mot de passe** et le **nom** du nouveau développeur et cliquez sur **Enregistrer**.

![Create developer][api-management-add-new-user]

Par défaut, les comptes de développeurs nouvellement créés sont **actifs**. Ils sont associés au groupe **Développeurs**.

![New developer][api-management-new-developer]

Les comptes de développeurs dont l'état est **actif** peuvent être utilisés pour accéder à toutes les API auxquelles ils sont abonnés. Pour associer les développeurs nouvellement créés à d'autres groupes, consultez la rubrique [Association de groupes à des développeurs][].

## <a name="invite-developer"> </a>Invitation d’un développeur

Pour inviter un développeur, cliquez sur **Utilisateurs** dans le menu **Gestion des API** à gauche, puis sur **Inviter un utilisateur**.

![Invite developer][api-management-invite-developer]

Entrez le nom et l'adresse électronique du développeur, puis cliquez sur **Inviter**.

![Invite developer][api-management-invite-developer-window]

Un message de confirmation s’affiche, mais le développeur qui vient d’être invité n’apparaît pas dans la liste tant qu’il n’a pas accepté l’invitation.

![Invite confirmation][api-management-invite-developer-confirmation]

Quand un développeur est invité, un message lui est envoyé. Ce message est généré à partir d'un modèle. Il est personnalisable. Pour plus d'informations, consultez la page [Configuration des modèles de courrier électronique][].

Une fois l'invitation acceptée, le compte est activé.

## <a name="block-developer"> </a>Désactivation ou réactivation d’un compte de développeur

Par défaut, les comptes de développeur nouvellement créés ou invités sont **actifs**. Pour désactiver un compte de développeur, cliquez sur **Bloquer**. Pour réactiver un compte de développeur bloqué, cliquez sur **Activer**. Les comptes de développeurs bloqués ne peuvent pas accéder au portail des développeurs, ni appeler les API.

![Block developer][api-management-new-developer]

## <a name="next-steps"> </a>Étapes suivantes

Une fois le compte de développeur créé, vous pouvez l'associer à des rôles et l'abonner à des produits et des API. Pour plus d'informations, consultez la page [Création et utilisation de groupes][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-]: ./media/api-management-howto-create-or-invite-developers/api-management-.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Création et utilisation de groupes]: api-management-howto-create-groups.md
[Association de groupes à des développeurs]: api-management-howto-create-groups.md#associate-group-developer

[Prise en main de Gestion des API Azure]: api-management-get-started.md
[Création d'une instance de service Gestion des API]: api-management-get-started.md#create-service-instance
[Configuration des modèles de courrier électronique]: api-management-howto-configure-notifications.md#email-templates

<!---HONumber=AcomDC_1210_2015-->