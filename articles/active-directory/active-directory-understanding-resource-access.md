<properties
    pageTitle="Présentation de l’accès aux ressources dans Azure | Microsoft Azure" 
    description="Cette rubrique explique les concepts relatifs à l'utilisation des administrateurs des abonnements pour contrôler l'accès aux ressources dans l’ensemble du portail Azure."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/11/2016"
    ms.author="markusvi"/>


# Présentation de l'accès aux ressources dans Azure


> [AZURE.NOTE] Cette rubrique explique les concepts relatifs à l'utilisation des administrateurs des abonnements pour contrôler l'accès aux ressources dans l’ensemble du portail Azure. Le portail Azure en version préliminaire fournit également un [contrôle des accès par rôle](role-based-access-control-configure.md) afin de permettre une gestion plus précise des ressources Microsoft Azure.

En octobre 2013, le portail Azure Classic et les API Service Management ont été intégrés à Azure Active Directory afin de préparer le terrain pour améliorer l’expérience utilisateur en matière de gestion de l’accès aux ressources Microsoft Azure. Azure Active Directory fournit déjà des fonctionnalités importantes telles que la gestion des utilisateurs, la synchronisation locale de répertoires, l'authentification multifacteur et le contrôle de l’accès aux applications. Naturellement, elles doivent également être mises à disposition pour la gestion générale des ressources Azure.

Le contrôle des accès dans Azure s’envisage d’abord dans une perspective de facturation. Le propriétaire d'un compte Azure, accessible via le [Centre des comptes Azure](https://account.windowsazure.com/subscriptions), est l'administrateur de compte. Les abonnements sont un conteneur de facturation, mais ils constituent également une limite de sécurité : chaque abonnement a un administrateur de service qui peut ajouter, supprimer et modifier des ressources Azure dans cet abonnement à l’aide du [portail Azure Classic](https://manage.windowsazure.com/). L'administrateur de sécurité par défaut d'un nouvel abonnement est l'administrateur de compte, mais ce dernier peut modifier l'administrateur de sécurité dans le centre des comptes Azure.

<br><br>![Comptes Azure][1]

Les abonnements sont également associés à un répertoire. Le répertoire définit un ensemble d'utilisateurs. Ceux-ci peuvent être des utilisateurs professionnels ou scolaires qui ont créé le répertoire, ou bien des utilisateurs externes (autrement dit, des comptes Microsoft). Les abonnements sont accessibles par un sous-ensemble des utilisateurs du répertoire qui ont été affectés comme administrateur de service ou comme coadministrateur ; la seule exception est que, pour des raisons d'héritage, les comptes Microsoft (anciennement Windows Live ID) peuvent être affectés comme administrateur de service ou comme co-admistrateur sans être présents dans le répertoire.

<br><br>![Contrôle des accès par rôle dans Azure][2]


Le portail Azure Classic comprend une fonctionnalité qui permet aux administrateurs de service connectés avec un compte Microsoft de modifier le répertoire auquel est associé un abonnement à l’aide de la commande **Modifier le répertoire** sur la page **Abonnements** dans **Paramètres**. Notez que cette opération a des incidences sur le contrôle des accès de cet abonnement.



> [AZURE.NOTE] La commande **Modifier le répertoire** du portail de gestion Azure n’est pas disponible pour les utilisateurs connectés avec un compte professionnel ou scolaire parce que ces comptes peuvent se connecter uniquement au répertoire auquel ils appartiennent.

<br><br>![Flux de connexion utilisateur simple][3]

Dans le cas le plus simple, une organisation (par exemple Contoso) applique la facturation et le contrôle des accès au même ensemble d'abonnements. Autrement dit, le répertoire est associé à des abonnements appartenant à un seul compte Azure. Une fois correctement connectés au portail Azure Classic, les utilisateurs voient deux collections de ressources (représentées en orange dans l’illustration précédente) :


- Répertoires où leur compte d'utilisateur existe (source ou ajouté comme principal étranger). Notez que le répertoire utilisé pour la connexion n'est pas approprié pour ce calcul. Ainsi, vos répertoires seront toujours affichés, où que vous soyez connecté.

- Ressources qui font partie des abonnements associés au répertoire utilisé pour la connexion ET auxquelles l'utilisateur peut accéder (qu’ils soient administrateurs de service ou co-administrateurs).


<br><br>![Utilisateur avec plusieurs abonnements et répertoires][4]


Les utilisateurs disposant d’abonnements sur plusieurs répertoires ont la possibilité de basculer le contexte actuel du portail Azure Classic à l’aide du filtre d’abonnement. En arrière-plan, il en résulte une connexion distincte à un répertoire différent, mais cela est effectué en toute transparence à l'aide de l’authentification unique.

Les opérations telles que le déplacement de ressources entre des abonnements peuvent être plus difficiles en raison de cet affichage unique des répertoires des abonnements. Pour effectuer le transfert de ressources, il peut être nécessaire de commencer par utiliser la commande **Modifier le répertoire** sur la page Abonnements dans **Paramètres** pour associer les abonnements au même répertoire.

## Étapes suivantes

- Pour plus d’informations sur la façon de modifier les administrateurs pour un abonnement Azure, voir [Comment ajouter ou modifier les rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md)

- Pour plus d’informations sur la façon dont le service Azure Active Directory est lié à votre abonnement Azure, voir [Association des abonnements Azure avec Azure Active Directory] (active-directory-how-subscriptions-associated directory.md)

- Pour plus d’informations sur l’attribution des rôles dans Azure AD, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md)



<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png

<!---HONumber=AcomDC_0218_2016-->