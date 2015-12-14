<properties
   pageTitle="Événement « Acteur inconnu»  de la création de rapports Azure Active Directory | Microsoft Azure"
   description="Description de l’événement « Acteur inconnu » des rapports Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/30/2015"
   ms.author="kenhoff"/>

# Événement « Acteur inconnu » lors de la création de rapports Azure Active Directory

## Articles de la documentation sur la création de rapports

 - [API de création de rapports](active-directory-reporting-api-getting-started.md)
 - [Événements d'audit](active-directory-reporting-audit-events.md)
 - [Rétention](active-directory-reporting-retention.md)
 - [Versions préliminaires](active-directory-reporting-previews.md)
 - [action](active-directory-reporting-search.md)
 - [Renvoi](active-directory-reporting-backfill.md)
 - [Latences](active-directory-reporting-latencies.md)
 - [Événement « Acteur inconnu »](active-directory-reporting-unknown-actor.md)

Dans de rares occasions, les champs « Acteur » ou « Utilisateur » des rapports Azure AD peuvent contenir des valeurs inhabituelles. Ce comportement est normal. Il est dû à l’un des deux événements suivants :

## Un principal de service agit sur l’annuaire, sans contexte utilisateur

Dans ce cas, un principal de service (application) effectue les mises à jour sans réellement ouvrir de session en tant qu’utilisateur. L’ID du principal de service s’affiche en tant qu’acteur et non pas comme nom UPN. Voici un exemple :

![](./media/active-directory-reporting-unknown-actor/spd-actor.png)

Il s'agit d'un bogue connu et nous travaillons assidûment pour le résoudre.

## Un utilisateur a été supprimé de l’annuaire avant que l'événement ait été traité.

Dans ce cas, un utilisateur a été supprimé de l’annuaire avant que nous ayons traité l'événement et l’ayons associé à un nom d'utilisateur. Voici un exemple :

![](./media/active-directory-reporting-unknown-actor/unknown-actor.png)

Il s'agit d'un bogue connu et nous travaillons assidûment pour le résoudre.

<!-- ![](./media/active-directory-reporting-unknown-actor/uid-actor.png) -->

<!---HONumber=AcomDC_1203_2015-->