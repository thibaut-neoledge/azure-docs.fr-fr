<properties
   pageTitle="Assistant Sécurité d’Azure Privileged Identity Management"
   description="La première fois que vous utilisez l’extension Azure Privileged Identity Management, un Assistant de sécurité s’affiche. Cet article décrit les étapes d’utilisation de l’Assistant."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2015"
   ms.author="inhenk"/>

# Assistant Sécurité d’Azure Privileged Identity Management

La première fois que vous exécutez Azure Privileged Identity Management, un Assistant s’affiche. Celui-ci vous aide à comprendre les risques de sécurité des identités privilégiées et comment utiliser Privileged Identity Management pour limiter ces risques.

Trois sections méritent votre attention : **VOS ADMINISTRATEURS PEUVENT VOUS EXPOSER À DES RISQUES, GÉRER LA SURFACE D’ATTAQUE DE VOS ADMINISTRATEURS** et **DÉFINIR DES PARAMÈTRES D’ADMINISTRATION TEMPORAIRES**. Chaque section vous donne une vue d’ensemble des concepts et une explication de certaines actions à entreprendre.

Initialement, tous vos administrateurs généraux sont permanents. Quand vous cliquez sur **VOS ADMINISTRATEURS PEUVENT VOUS EXPOSER À DES RISQUES**, vous voyez apparaître la liste des rôles d’administrateur général et combien vous en possédez actuellement.

Vous pouvez cliquer sur **GÉRER LA SURFACE D’ATTAQUE DE VOS ADMINISTRATEURS** pour rendre vos administrateurs temporaires, conserver leur caractère permanent ou les supprimer complètement du rôle.

**DÉFINIR DES PARAMÈTRES D’ADMINISTRATION TEMPORAIRES** vous permet de déterminer combien de temps un administrateur temporaire détient des privilèges, d’activer les notifications et d’imposer l’authentification multifacteur.

## Changer les rôles d’administrateur général en permanent ou temporaire

Vous avez trois options pour changer la fenêtre de temps d’un administrateur général.

1.  Cliquez sur le bouton **Les rendre tous temporaires** pour rendre tous les administrateurs généraux temporaires.

2.  Cliquez sur le bouton **Les rendre tous permanents** pour rendre tous les administrateurs généraux permanents.

3.  Sélectionnez **Conserver permanent**, **Rendre temporaire** ou **Supprimer du rôle** pour chaque administrateur général.

4.  Cliquez sur **OK**.

5.  Cliquez sur **Envoyer**.

## Modifier la période d’activation d’un rôle d’administrateur général

1.  Déplacer le curseur **Période d’activation** vers la gauche ou la droite pour augmenter ou réduire la période d’activation. La période d’activation peut aller jusqu’à 72 heures.

2.  Entrez le nombre d’heures dans le champ **Heures** à droite du curseur.

## Activer les notifications

Pour que les administrateurs puissent recevoir des messages quand les rôles deviennent actifs, activez les notifications en cliquant sur le bouton **Activer**. Vous pouvez également désactiver cette fonctionnalité.

## Imposer l’authentification multifacteur

Pour obliger les administrateurs à utiliser l’authentification multifacteur pour se connecter à leur compte et demander une extension de leur rôle, activez l’authentification multifacteur en cliquant sur le bouton **Activer**. Vous pouvez également désactiver cette fonctionnalité.

Pour plus d’informations sur l’authentification multifacteur et sur PIM, cliquez ici. ESPACE RÉSERVÉ : LIEN VERS LA DOCUMENTATION SUR L’AUTHENTIFICATION MULTIFACTEUR.

Sélectionnez les rôles auxquels ces paramètres doivent s’appliquer. Cliquez sur **OK**.

> [AZURE.WARNING]Il est important, à ce stade, que vous disposiez de plusieurs administrateurs de sécurité, car si vous n’en possédez qu’un, qu’il n’est pas défini comme permanent, que l’attribution de rôle expire et que l’authentification multifacteur n’est pas définie pour cet utilisateur, ce dernier ne peut absolument pas administrer PIM.

Cliquez sur le bouton **OK** quand vous avez terminé.

Une fois que vous avez apporté les modifications, l’Assistant ne s’affiche plus. Toutefois, vous pouvez y accéder à nouveau en cliquant sur le bouton Assistant sous Gérer les identités.

## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Sept15_HO4-->