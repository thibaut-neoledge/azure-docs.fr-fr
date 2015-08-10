<properties
	pageTitle="Azure AD Connect Sync : implémenter la synchronisation de mot de passe"
	description="Fournit les informations nécessaires pour comprendre le fonctionnement de la synchronisation de mot de passe et comment l’activer dans votre environnement."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync : implémenter la synchronisation de mot de passe 

Avec la synchronisation de mot de passe, les utilisateurs peuvent utiliser le mot de passe qu’ils utilisent pour se connecter à votre annuaire Active Directory local pour se connecter à Azure Active Directory.

L’objectif de cette rubrique est de vous fournir les informations nécessaires pour comprendre le fonctionnement de la synchronisation de mot de passe et comment l’activer dans votre environnement.

## Qu’est-ce que la synchronisation de mot de passe ?

La synchronisation de mot de passe est une fonctionnalité d’Azure Active Directory Connect Synchronization Services (Azure AD Connect Sync) qui synchronise les mots de passe des utilisateurs de votre annuaire Active Directory local vers Azure Active Directory (Azure AD). Cette fonctionnalité permet à vos utilisateurs de se connecter à leurs services Azure Active Directory (comme Office 365, Microsoft Intune, CRM Online, etc.) à l’aide du mot de passe qu’ils utilisent pour se connecter à votre réseau local. Il est important de noter que cette fonctionnalité ne constitue pas une solution d’authentification unique, car il n’y a aucun partage ou échange de jeton dans le processus de synchronisation de mot de passe.

> [AZURE.NOTE]Pour plus d’informations sur les services de domaine Active Directory qui sont configurés pour la synchronisation de mot de passe et FIPS, consultez Échec de la synchronisation de mot de passe dans les systèmes compatibles FIPS.
 
## Disponibilité de la synchronisation de mot de passe

Tout client Azure Active Directory est autorisé à exécuter la synchronisation de mot de passe. Pour plus d’informations sur la compatibilité de la synchronisation de mot de passe et d’autres fonctionnalités telles que l’authentification fédérée, voir ci-dessous.

## Fonctionnement de la synchronisation de mot de passe

La synchronisation de mot de passe est une extension de la fonctionnalité de synchronisation d’annuaire implémentée par Azure AD Connect Sync. Ainsi, elle nécessite la configuration de la synchronisation d’annuaire entre votre annuaire local et votre annuaire Azure Active Directory.

Les services de domaine Active Directory stockent les mots de passe sous forme de valeur de hachage du mot de passe réel de l’utilisateur. Vous ne pouvez pas utiliser le hachage de mot de passe pour vous connecter à votre réseau local. De par sa conception, vous ne pouvez pas non plus l’inverser pour accéder au mot de passe en texte brut de l’utilisateur. Pour synchroniser un mot de passe, Azure AD Connect Sync extrait le hachage de mot de passe de l’utilisateur à partir de l’annuaire Active Directory local. Un traitement de sécurité supplémentaire est appliqué au hachage du mot de passe avant sa synchronisation avec le service d’authentification Azure Active Directory. Le flux de données du processus de synchronisation de mot de passe est similaire à la synchronisation des données utilisateur telles que le nom d’affichage ou les adresses de messagerie.

Les mots de passe sont synchronisés plus fréquemment que la fenêtre de synchronisation d’annuaire standard pour d’autres attributs. Les mots de passe sont synchronisés pour chaque utilisateur, généralement par ordre chronologique. Quand un mot de passe est synchronisé de l’annuaire Active Directory local vers le cloud, le mot de passe cloud existant est remplacé.

Quand vous activez pour la première fois la fonctionnalité de synchronisation de mot de passe, elle effectue une synchronisation initiale des mots de passe de tous les utilisateurs dans la portée de votre annuaire Active Directory local vers Azure Active Directory. Vous ne pouvez pas définir explicitement l’ensemble des utilisateurs dont les mots de passe seront synchronisés vers le cloud. Par la suite, quand un mot de passe a été modifié par un utilisateur local, la fonctionnalité de synchronisation de mot de passe détecte et synchronise le mot de passe modifié, le plus souvent en quelques minutes. La fonctionnalité de synchronisation de mot de passe tente automatiquement de réeffectuer une synchronisation de mot de passe utilisateur ayant échoué. Si une erreur se produit lors d’une tentative de synchronisation de mot de passe, l’erreur est enregistrée dans l’Observateur d’événements.

La synchronisation d’un mot de passe n’a aucun impact sur les utilisateurs actuellement connectés. Si un utilisateur connecté à un service cloud modifie également le mot de passe local, la session de service cloud se poursuit sans interruption. Toutefois, dès que le service cloud exige de l’utilisateur qu’il se réauthentifie, le nouveau mot de passe doit être fourni. À ce stade, l’utilisateur doit fournir le nouveau mot de passe (celui qui a récemment été synchronisé de l’annuaire Active Directory local vers le cloud).

## Sécurité

Lors de la synchronisation des mots de passe, la version en texte brut du mot de passe d’un utilisateur n’est exposée ni à la fonctionnalité de synchronisation de mot de passe, ni à Azure AD ou l’un des services associés.

En outre, il n’existe aucune spécification obligeant l’annuaire Active Directory local à stocker le mot de passe dans un format chiffré réversible. Un condensé du hachage de mot de passe Windows Active Directory est utilisé pour la transmission entre l’annuaire Active Directory local et Azure Active Directory. Vous ne pouvez pas utiliser le condensé du hachage de mot de passe pour accéder aux ressources dans l’environnement local du client.

## Remarques sur les stratégies de mot de passe

Deux types de stratégies de mot de passe sont affectés par l’activation de la synchronisation de mot de passe :

1. la stratégie de complexité de mot de passe ;
2. la stratégie d’expiration de mot de passe.

### Stratégie de complexité de mot de passe

Quand vous activez la synchronisation de mot de passe, les stratégies de complexité de mot de passe configurées dans l’annuaire Active Directory local remplacent les stratégies de complexité qui peuvent être définies dans le cloud pour les utilisateurs synchronisés. Cela signifie que tout mot de passe valide dans l’environnement Active Directory local du client peut être utilisé pour accéder aux services Azure AD.


> [AZURE.NOTE]Les mots de passe des utilisateurs créés directement dans le cloud sont toujours soumis aux stratégies de mot de passe définies dans le cloud.
 
### la stratégie d’expiration de mot de passe.

Si un utilisateur est dans la portée de la synchronisation de mot de passe, le mot de passe de compte cloud a la valeur « \*Ne jamais expirer\* ». Cela signifie que le mot de passe d’un utilisateur peut expirer dans l’environnement local, mais que l’utilisateur peut continuer à se connecter aux services cloud à l’aide de ce mot de passe expiré.

Le mot de passe cloud sera mis à jour la prochaine fois que l’utilisateur modifiera le mot de passe dans l’environnement local.


## Remplacement des mots de passe synchronisés

Un administrateur peut réinitialiser manuellement le mot de passe d’un utilisateur à l’aide d’Azure Active Directory PowerShell.

Dans ce cas, le nouveau mot de passe remplace le mot de passe synchronisé de l’utilisateur et toutes les stratégies de mot de passe définies dans le cloud s’appliquent au nouveau mot de passe.

Si l’utilisateur modifie de nouveau le mot de passe local, le nouveau mot de passe est synchronisé avec le cloud et il remplace le mot de passe mis à jour manuellement.


## Préparation à la synchronisation de mot de passe

Pour que votre client Azure Active Directory puisse être activé pour la synchronisation de mot de passe, il doit être activé pour la synchronisation d’annuaire.


## Activation de la synchronisation de mot de passe

Vous activez la synchronisation de mot de passe lors de l’exécution de l’Assistant Configuration Azure AD Connect.

Dans la page de boîte de dialogue **Fonctionnalités facultatives**, sélectionnez « \*\*Synchronisation de mot de passe\*\* ».
 
![Fonctionnalités facultatives][1]


> [AZURE.NOTE]Ce processus déclenche une synchronisation complète. Les cycles de synchronisation complète durent généralement plus longtemps que les autres cycles de synchronisation.
 

## Gestion de la synchronisation de mot de passe

Vous pouvez surveiller la progression de la synchronisation de mot de passe via le journal des événements de l’ordinateur qui exécute Azure AD Connect.


### Détermination de l’état de la synchronisation de mot de passe

Vous pouvez identifier les utilisateurs dont les mots de passe ont été synchronisés en examinant les événements qui correspondent aux critères suivants :

| Source| ID de l’événement |
| --- | --- |
| Synchronisation d’annuaire| 656|
| Synchronisation d’annuaire| 657|
 
Les événements avec l’ID 656 fournissent des informations sur les demandes de modification de mot de passe traitées :

![ID d’événement 656][2]

Les événements correspondants avec l’ID 657 indiquent les résultats de ces demandes :

![ID d’événement 657][3]

Dans les événements, les objets affectés sont identifiés par leur ancre et la valeur de nom de domaine. La valeur d’ancre correspond à la valeur **ImmutableId** retournée pour un utilisateur par l’applet de commande Get-MsoUser.

Outre les identificateurs d’objets, l’**ID d’événement 656** fournit la date de modification du mot de passe dans l’annuaire Active Directory local :

![Demande de modification de mot de passe][4]

L’ID d’événement 657 comporte un champ de résultat en plus des identificateurs d’objets sources pour indiquer l’état de la synchronisation pour cet objet utilisateur.

Un mot de passe correctement synchronisé figure dans un événement portant l’ID 657, indiqué par une valeur Success pour l’attribut Result. Quand une tentative de synchronisation de mot de passe échoue, la valeur de l’attribut Result est Failure :

![Résultat de modification de mot de passe][5]

 
## Désactivation de la synchronisation de mot de passe

Vous pouvez désactiver la synchronisation de mot de passe en réexécutant l’Assistant Configuration Azure AD Connect. À l’invite de l’Assistant, décochez la case « Synchronisation de mot de passe ».


> [AZURE.NOTE]Ce processus déclenche une synchronisation complète. Les cycles de synchronisation complète durent généralement plus longtemps que les autres cycles de synchronisation.
 
Une fois l’Assistant Configuration exécuté, votre client ne synchronisera plus les mots de passe. Les nouvelles modifications de mots de passe ne seront pas synchronisées avec le cloud. Les utilisateurs dont les mots de passe étaient auparavant synchronisés pourront continuer à se connecter avec ces mots de passe jusqu’à ce qu’ils les modifient manuellement dans le cloud.



## Ressources supplémentaires

* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->
[1]: ./media/active-directory-aadsync-implement-password-synchronization/IC759788.png
[2]: ./media/active-directory-aadsync-implement-password-synchronization/IC662504.png
[3]: ./media/active-directory-aadsync-implement-password-synchronization/IC662505.png
[4]: ./media/active-directory-aadsync-implement-password-synchronization/IC662506.png
[5]: ./media/active-directory-aadsync-implement-password-synchronization/IC662507.png

<!---HONumber=July15_HO5-->