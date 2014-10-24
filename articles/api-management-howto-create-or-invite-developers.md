<properties pageTitle="How manage developer accounts in Azure API Management" metaKeywords="" description="Learn how to create or invite developers in Azure API Management" metaCanonical="" services="" documentationCenter="API Management" title="How manage developer accounts in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Gestion des comptes des développeurs dans Gestion des API Azure

Dans Gestion des API Azure (version préliminaire), les développeurs sont les utilisateurs des API que vous exposez via Gestion des API. Ce guide vous montre comment créer et inviter des développeurs à utiliser les API et les produits que vous mettez à leur disposition dans votre instance Gestion des API.

## Dans cette rubrique

-   [Création d'un développeur][]
-   [Invitation d'un développeur][]
-   [Désactivation ou réactivation d'un compte de développeur][]
-   [Étapes suivantes][]

## <a name="create-developer"> </a>Création d'un développeur

Pour créer un développeur, cliquez sur **Console de gestion** dans le portail Azure de votre service Gestion des API. Vous accéderez au portail d'administration Gestion des API.

> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

![API Management console][]

Cliquez sur **Développeurs** dans le menu **Gestion des API** à gauche, puis sur **Ajouter un utilisateur**.

![Create developer][]

Entrez l'**adresse électronique**, le **mot de passe** et le **nom** du nouveau développeur et cliquez sur **Enregistrer**.

![Create developer][1]

Par défaut, les comptes de développeurs nouvellement créés sont **actifs**. Ils sont associés au groupe **Développeurs**.

![New developer][]

Les comptes de développeurs dont l'état est **actif** peuvent être utilisés pour accéder à toutes les API auxquelles ils sont abonnés. Pour associer les développeurs nouvellement créés à d'autres groupes, consultez la rubrique [Association de groupes à des développeurs][].

## <a name="invite-developer"> </a>Invitation d'un développeur

Pour inviter un développeur, cliquez sur **Développeurs** dans le menu **Gestion des API** à gauche, puis sur **Inviter un utilisateur**.

![Invite developer][]

Entrez le nom et l'adresse électronique du développeur, puis cliquez sur **Inviter**.

![Invite developer][2]

Un message de confirmation s'affiche, mais le développeur qui vient d'être invité n'apparaît pas dans la liste tant qu'il n'a pas accepté l'invitation.

![Invite confirmation][]

> Quand un développeur est invité, un message lui est envoyé. Ce message est généré à partir d'un modèle. Il est personnalisable. Pour plus d'informations, consultez la page [Configuration des modèles de courrier électronique][].

Une fois l'invitation acceptée, le compte est activé.

## <a name="block-developer"> </a>Désactivation ou réactivation d'un compte de développeur

Par défaut, les comptes de développeur nouvellement créés ou invités sont **actifs**. Pour désactiver un compte de développeur, cliquez sur **Bloquer**. Pour réactiver un compte de développeur bloqué, cliquez sur **Activer**. Les comptes de développeurs bloqués ne peuvent pas accéder au portail des développeurs, ni appeler les API.

![Block developer][New developer]

## <a name="next-steps"> </a>Étapes suivantes

Une fois le compte de développeur créé, vous pouvez l'associer à des rôles et l'abonner à des produits et des API. Pour plus d'informations, consultez la page [Création et utilisation de groupes][].

  [Création d'un développeur]: #create-developer
  [Invitation d'un développeur]: #invite-developer
  [Désactivation ou réactivation d'un compte de développeur]: #block-developer
  [Étapes suivantes]: #next-steps
  [Création d'une instance de service Gestion des API]: ../api-management-get-started/#create-service-instance
  [Prise en main de Gestion des API Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
  [Create developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
  [1]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
  [New developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
  [Association de groupes à des développeurs]: ../api-management-howto-create-groups/#associate-group-developer
  [Invite developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
  [2]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
  [Invite confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
  [Configuration des modèles de courrier électronique]: ../api-management-howto-configure-notifications/#email-templates
  [Création et utilisation de groupes]: ../api-management-howto-create-groups
