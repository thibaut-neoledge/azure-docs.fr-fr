<properties
   pageTitle="À propos de l’application Tailspin Surveys | Microsoft Azure"
   description="Présentation de l’application Tailspin Surveys"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# À propos de l’application Tailspin Surveys

Cet article fait [partie d’une série] qui s’accompagne d’un [exemple d’application].

Tailspin est une société fictive qui développe une application SaaS nommée Surveys. Cette application permet aux organisations de créer et de publier des enquêtes en ligne.

- Une organisation peut s’inscrire auprès de l’application.
- Une fois que l’organisation est inscrite, les utilisateurs peuvent se connecter à l’application avec leurs informations d’identification professionnelles.
- Les utilisateurs peuvent créer, modifier et publier des enquêtes.

> [AZURE.NOTE] Pour vous familiariser avec l’application, consultez [Exécution de l’application Surveys].

Cette capture d’écran illustre la page Edit Survey :

![Modifier l’enquête](media/guidance-multitenant-identity/edit-survey.png)

Notez que l’utilisateur est connecté à l’aide de son identité professionnelle, `bob@contoso.com`.

Les utilisateurs peuvent consulter les enquêtes créées par d’autres utilisateurs au sein du même client.

![Enquêtes client](media/guidance-multitenant-identity/tenant-surveys.png)

Lorsqu’un utilisateur crée une enquête, il peut inviter d’autres personnes à devenir des collaborateurs sur l’enquête. Les collaborateurs peuvent modifier l’enquête, mais ils ne peuvent pas la supprimer, ni la publier.

![Ajouter un collaborateur](media/guidance-multitenant-identity/add-contributor.png)

Un utilisateur peut ajouter des collaborateurs à partir d’autres clients, ce qui permet le partage des ressources entre locataires. Dans cette capture d’écran, Bob (`bob@contoso.com`) ajoute Alice (`alice@fabrikam.com`) en tant que collaboratrice sur un questionnaire qu’il a créé.

Lorsqu’Alice se connecte, elle voit l’enquête répertoriée sous « Surveys I can contribute to » (Enquêtes auxquelles je peux contribuer).

![Collaborateur de l’enquête](media/guidance-multitenant-identity/contributor.png)

Notez qu’Alice se connecte à son propre client et non en tant qu’invitée du client Contoso. Alice dispose des autorisations propres au collaborateur uniquement pour cette enquête. Elle ne peut pas afficher les autres enquêtes du client Contoso.

## Architecture

L’application Surveys se compose d’un serveur web frontal et d’un serveur principal d’API web. Les deux sont implémentés à l’aide d’[ASP.NET Core 1.0].

L’application web utilise Azure Active Directory (Azure AD) pour authentifier les utilisateurs. L’application web appelle également Azure AD pour obtenir des jetons d’accès OAuth 2 pour l’API web. Les jetons d’accès sont mis en cache dans le Cache Redis Azure. Le cache permet à plusieurs instances de partager le même cache de jeton (par exemple, dans une batterie de serveurs).

![Architecture](media/guidance-multitenant-identity/architecture.png)

<!-- Links -->
[partie d’une série]: guidance-multitenant-identity.md
[Exécution de l’application Surveys]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[ASP.NET Core 1.0]: https://docs.asp.net/en/latest/
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->