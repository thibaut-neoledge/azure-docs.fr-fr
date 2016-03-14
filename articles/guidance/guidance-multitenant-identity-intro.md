<properties
   pageTitle="Gestion des identités pour les applications multi-locataires | Microsoft Azure"
   description="Présentation de la gestion des identités pour les applications multi-locataires"
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

# Gestion des identités pour les applications multi-locataires : Introduction

Cet article fait [partie d’une série]. Un [exemple d’application] accompagne également cette série.

Supposons que vous écriviez une application SaaS d’entreprise pour qu’elle soit hébergée dans le cloud. Bien sûr, cette application aura des utilisateurs :

![Utilisateurs](media/guidance-multitenant-identity/users.png)

Mais ces utilisateurs appartiennent à des organisations :

![Utilisateurs d’organisation](media/guidance-multitenant-identity/org-users.png)

Exemple : Tailspin vend des abonnements à son application SaaS. Contoso et Fabrikam s’inscrivent à l’application. Quand Alice (`alice@contoso`) s’inscrit, l’application doit savoir qu’Alice fait partie de Contoso.

- Alice _doit_ avoir accès aux données Contoso.
- Alice _ne doit pas_ avoir accès aux données Fabrikam.

Ce guide vous explique comment gérer des identités d’utilisateurs dans une application multi-locataire, en utilisant [Azure Active Directory][AzureAD] (Azure AD) pour gérer la connexion et l’authentification.

## Qu’est-ce que l’architecture mutualisée ?

Un _locataire_ est un groupe d’utilisateurs. Dans une application SaaS, le locataire est un abonné ou un client de l’application. L’_architecture mutualisée_ est une architecture où plusieurs locataires partagent la même instance physique de l’application. Bien que les locataires partagent des ressources physiques (par exemple, les machines virtuelles ou le stockage), chaque locataire obtient sa propre instance logique de l’application.

Normalement, les données d’application sont partagées entre les utilisateurs d’un locataire, mais pas avec d’autres locataires.

![Multi-locataire](media/guidance-multitenant-identity/multitenant.png)

Comparez cette architecture avec une architecture à locataire unique, où chaque locataire a une instance physique dédiée. Dans une architecture à locataire unique, vous ajoutez des locataires mettant en place de nouvelles instances de l’application.

![Locataire unique](media/guidance-multitenant-identity/single-tenant.png)

### Architecture mutualisée et mise à l’échelle horizontale

Pour atteindre une mise à l’échelle dans le cloud, il est courant d’ajouter des instances physiques supplémentaires. C’est ce qu’on appelle la _mise à l’échelle horizontale_ ou la _montée en charge_. Prenons l’exemple d’une application web. Pour gérer davantage de trafic, vous pouvez ajouter des machines virtuelles serveurs supplémentaires et les placer derrière un équilibreur de charge. Chaque machine virtuelle exécute une instance physique distincte de l’application web.

![Équilibrage de la charge d’un site web](media/guidance-multitenant-identity/load-balancing.png)

Toute requête peut être acheminée vers n’importe quelle instance. Globalement, le système fonctionne comme une instance logique unique. Vous pouvez supprimer une machine virtuelle ou en ajouter une nouvelle, sans affecter les utilisateurs. Dans cette architecture, chaque instance physique est mutualisée, et vous effectuez la mise à l’échelle en ajoutant des instances supplémentaires. Si une instance subit une défaillance, cela ne doit affecter aucun locataire.

## Identité dans une application multi-locataire

Dans une application multi-locataire, vous devez considérer les utilisateurs dans le contexte des locataires.

**Authentification**

- Les utilisateurs se connectent à l’application avec les informations d’identification de leur organisation. Ils n’ont pas à créer de nouveaux profils utilisateur pour l’application.
- Les utilisateurs appartenant à la même organisation font partie du même locataire.
- Quand un utilisateur se connecte, l’application sait à quel locataire l’utilisateur appartient.

**Autorisation**

- Lors de l’autorisation d’actions d’un utilisateur (par exemple, l’affichage d’une ressource), l’application doit prendre en compte le locataire de l’utilisateur.
- Des rôles peuvent être affectés aux utilisateurs de dans l’application, comme « Admin » ou « Utilisateur Standard ». Les attributions de rôles doivent être gérées par le client, et non par le fournisseur SaaS.

**Exemple**. Alice, une employée de Contoso, accède à l’application dans son navigateur et clique sur le bouton « Se connecter ». Elle est redirigée vers un écran de connexion où elle entre ses informations d’identification d’entreprise (nom d’utilisateur et mot de passe). À ce stade, elle est connectée à l’application en tant que `alice@contoso.com`. L’application sait également qu’Alice est un utilisateur administrateur pour cette application. En tant qu’administrateur, elle peut voir la liste de toutes les ressources qui appartiennent à Contoso. Toutefois, elle ne peut pas afficher les ressources de Fabrikam, car elle est un administrateur uniquement au sein de son locataire.

Dans ce guide, nous examinerons plus particulièrement l’utilisation d’Azure AD pour la gestion des identités.

- Nous supposons que le client stocke ses profils utilisateur dans Azure AD (notamment les locataires Office 365 et Dynamics CRM).
- Les clients disposant d’Active Directory (AD) local peuvent utiliser [Azure AD Connect][ADConnect] pour synchroniser leur AD local avec Azure AD.

Si un client disposant d’AD local ne peut pas utiliser Azure AD Connect (en raison d’une stratégie informatique d’entreprise ou pour d’autres raisons), le fournisseur SaaS peut fédérer avec l’AD du client via les services ADFS (Active Directory Federation Services). Cette option est décrite dans [Fédération avec les services ADFS d’un client].

Ce guide ne prend pas en considération les autres aspects d’une architecture mutualisée, comme le partitionnement des données, la configuration par locataire, etc.

<!-- Links -->
[ADConnect]: ../active-directory/active-directory-aadconnect.md
[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/
[partie d’une série]: guidance-multitenant-identity.md
[Fédération avec les services ADFS d’un client]: guidance-multitenant-identity-adfs.md
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->