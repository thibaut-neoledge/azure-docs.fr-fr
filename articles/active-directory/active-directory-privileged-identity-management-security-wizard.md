<properties
   pageTitle="Assistant Sécurité d’Azure AD Privileged Identity Management"
   description="La première fois que vous utilisez l’extension Azure Active Directory Privileged Identity Management, un Assistant Sécurité s’affiche. Cet article décrit les étapes d’utilisation de l’Assistant."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/08/2016"
   ms.author="kgremban"/>

# Assistant Sécurité d’Azure AD Privileged Identity Management

La première fois que vous exécutez Azure Privileged Identity Management (PIM), un Assistant s’affiche. Celui-ci vous aide à comprendre les risques de sécurité des identités privilégiées et comment utiliser Privileged Identity Management pour limiter ces risques.

Trois sections méritent votre attention : **VOS ADMINISTRATEURS PEUVENT VOUS EXPOSER À DES RISQUES, GÉRER LA SURFACE D’ATTAQUE DE VOS ADMINISTRATEURS** et **DÉFINIR DES PARAMÈTRES D’ADMINISTRATION TEMPORAIRES**. Chaque section vous donne une vue d’ensemble des concepts et une explication de certaines actions à entreprendre.

Initialement, tous vos administrateurs généraux sont permanents. Quand vous cliquez sur **VOS ADMINISTRATEURS PEUVENT VOUS EXPOSER À DES RISQUES**, vous voyez apparaître la liste des rôles d’administrateur général et combien vous en possédez actuellement.

Vous pouvez cliquer sur **GÉRER LA SURFACE D’ATTAQUE DE VOS ADMINISTRATEURS** pour rendre vos administrateurs temporaires, conserver leur caractère permanent ou les supprimer complètement du rôle.

**DÉFINIR DES PARAMÈTRES D’ADMINISTRATION TEMPORAIRES** vous permet d’imposer l’authentification multifacteur, d’activer les notifications et de déterminer combien de temps un administrateur temporaire détient des privilèges.

## Modifier et rendre les rôles d’administrateur général permanents ou temporaires

Trois options permettent de changer la fenêtre de temps d’un administrateur général :

1.  Cliquez sur le bouton **Les rendre tous temporaires** pour rendre tous les administrateurs généraux temporaires. Ne choisissez cette option que si tous les administrateurs disposent de comptes professionnels et se sont enregistrés pour l’authentification multifacteur Azure.

2.  Cliquez sur le bouton **Les rendre tous permanents** pour rendre tous les administrateurs généraux permanents.

3.  Sélectionnez **Conserver permanent**, **Rendre temporaire** ou **Supprimer du rôle** pour chaque administrateur général.

## Modifier la période d’activation d’un rôle d’administrateur général

Deux méthodes permettent de définir la période d’activation d’un administrateur général :

1.  Déplacer le curseur **Période d’activation** vers la gauche ou la droite pour augmenter ou réduire la période d’activation. La période d’activation peut aller jusqu’à 72 heures.

2.  Entrez le nombre d’heures dans le champ **Heures** à droite du curseur.

## Activer les notifications

Pour que les administrateurs puissent recevoir des messages quand les rôles deviennent actifs, activez les notifications en cliquant sur le bouton **Activer**. Vous pouvez également désactiver cette fonctionnalité ultérieurement.

## Imposer l’authentification multifacteur

Pour obliger les administrateurs à utiliser MFA pour se connecter à leur compte et demander une extension de leur rôle, activez MFA en cliquant sur le bouton **Activer**.

<!--For more information about MFA and PIM, click here. PLACEHOLDER: NEED LINK TO MFA DOC.-->

Sélectionnez les rôles auxquels ces paramètres doivent s’appliquer. Cliquez sur **OK**.

> [AZURE.WARNING] Il est important à ce stade que vous disposiez de plusieurs administrateurs de sécurité avec un compte professionnel (et non un compte Microsoft). S’il n’existe qu’un seul administrateur de sécurité qui n’est pas rendu permanent et pour lequel l’authentification MFA n’est pas configurée, l’utilisateur ne sera pas du tout en mesure d’administrer PIM si le compte est supprimé.

Cliquez sur le bouton **OK** lorsque vous avez terminé.

Une fois que vous avez apporté les modifications, l’Assistant ne s’affiche plus. Toutefois, vous pouvez y accéder à nouveau en cliquant sur le bouton **Assistant** sous **Gérer les identités**.

## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0316_2016-->