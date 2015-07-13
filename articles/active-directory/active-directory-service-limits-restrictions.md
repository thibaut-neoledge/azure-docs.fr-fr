<properties 
	pageTitle="Restrictions et limites du service Azure Active Directory" 
	description="Contraintes d’utilisation et autres limites de service pour le service Azure Active Directory" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Restrictions et limites du service Azure Active Directory

Présentation des contraintes d'utilisation et autres limites de service pour le service Azure Active Directory. Pour connaître l'ensemble complet des limites du service Microsoft Azure, consultez [Limites, quotas et contraintes de service de l’abonnement Azure](../azure-subscription-service-limits.md).

## Répertoires

Un utilisateur ne peut être associé qu’à un maximum de 20 répertoires Azure Active Directory. Cette limite peut être atteinte dans un des exemples suivants :

- Un seul utilisateur crée 20 répertoires.
- Un seul utilisateur est ajouté à 20 répertoires en tant que membre.
- Un seul utilisateur crée 10 répertoires et est ajouté ultérieurement par d'autres à 10 autres répertoires.

## Objets

- Il n’existe pas de limite pour les abonnés d’Azure Active Directory Premium ou Azure Active Directory de base, Enterprise Mobility Suite, Office 365, Microsoft Intune ou tout autre service en ligne Microsoft associé à Azure Active Directory pour les services de répertoire.
- 500 000 objets au maximum peuvent être utilisés dans un répertoire unique avec l'édition gratuite d'Azure Active Directory.
- Un utilisateur non administrateur peut créer jusqu'à 250 objets.

##Extensions de schéma

Actuellement, les entités « Utilisateur », « Groupe », « TenantDetail », « Périphérique », « Application » et « ServicePrincipal » peuvent être étendues avec des attributs à valeur unique de type « binaire » ou « chaîne ». Ces entités incluent les limitations suivantes :

- Les extensions de type chaîne peuvent avoir jusqu'à 256 caractères.
- Les extensions de type binaire sont limitées à 256 octets.
- 100 valeurs d'extension (pour TOUT type et TOUTE application) peuvent être écrites pour un objet unique.
- Les extensions de schéma sont disponibles uniquement dans l’aperçu de la version 1.21 de l’API Graph. L’accès en écriture pour enregistrer une extension doit être accordé à l'application.

## Applications

10 utilisateurs au maximum peuvent être propriétaires d'une seule application.

## Groupes 

- 10 utilisateurs au maximum peuvent être propriétaires d'un seul groupe.
- Un nombre quelconque d'objets peuvent être membre d'un groupe unique dans Azure Active Directory.


> [AZURE.NOTE]
> 
Il existe une limite au nombre d'objets que vous pouvez synchroniser à partir de votre sur Active Directory local vers Azure Active Directory. Si vous utilisez DirSync, la limite est de 15 000 utilisateurs. Si vous utilisez Connect d’Azure AD, la limite est de 50 000 utilisateurs.

## Panneau d'accès

- Il n’existe pas de limite au nombre d’applications par utilisateur final qui peuvent être affichées dans le Panneau d’accès pour les abonnés à Azure Active Directory Premium, Azure Active Directory de base ou Enterprise Mobility Suite.
- Un maximum de 10 applications SaaS préintégrées (exemples : Box, Salesforce, ou Dropbox) peuvent être consultées dans le Panneau d'accès pour chaque utilisateur final avec l'édition gratuite d'Azure Active Directory. Les utilisateurs finaux peuvent afficher plus de 10 applications si votre organisation a développé des applications qui ont été intégrées par la suite avec Azure Active Directory. Cette limite ne s'applique pas aux comptes d'administrateur.

## Rapports

1 000 lignes au maximum peuvent être affichées ou téléchargées dans un rapport. Toutes les données supplémentaires sont tronquées.

## Étapes suivantes
- [Inscription à Azure en tant qu’organisation](sign-up-organization.md)
- [Association des abonnements Azure avec Azure AD](active-directory-how-subscriptions-associated-directory.md)
- [Terminologie Azure AD](active-directory-terminology.md)


 

<!---HONumber=62-->