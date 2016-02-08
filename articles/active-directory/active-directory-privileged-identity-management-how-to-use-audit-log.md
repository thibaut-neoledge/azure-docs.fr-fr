<properties
   pageTitle="Azure Privileged Identity Management : comment utiliser le journal d’audit"
   description="Découvrez comment utiliser le journal d’audit dans l’extension Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management : Comment utiliser le journal d’audit

Vous pouvez utiliser le journal d’audit Privileged Identity Management (PIM) pour voir toutes les activations et affectations d’utilisateur dans un laps de temps donné.

## Accès au journal d’audit
Vous pouvez accéder au journal d’audit en cliquant sur **Historique d’audit** dans le tableau de bord PIM.

## Graphique du journal d’audit
Le journal d’audit indique le nombre total d’activations, le nombre maximal d’activations par jour et la moyenne d’activations par jour dans un graphique linéaire. Vous pouvez également filtrer les données par rôle s’il existe plusieurs rôles dans l’historique d’audit.

Triez par heure, action ou rôle à l’aide des boutons **Heure**, **Action** ou **Rôle**.

## Liste du journal d’audit
Les colonnes dans la liste du journal d’audit sont les suivantes :

- **Demandeur** : personne qui a demandé l’activation de rôle.
- **Utilisateur** : utilisateur de l’activation de rôle.
- **Rôle** : rôle affecté à l’utilisateur.
- **Action** : actions effectuées avec le rôle ou l’utilisateur.
- **Heure** : heure à laquelle l’action s’est produite.
- **Motif** : tout texte éventuellement entré dans le champ de motif pendant l’activation.
- **Expiration** : si l’année d’expiration est 9999, l’utilisateur dispose du rôle définitivement.

## Filtrage du journal d’audit

Vous pouvez également filtrer les informations qui s’affichent dans le journal d’audit en cliquant sur le bouton **Filtrer**. Le panneau **Mettre à jour les paramètres du graphique** s’affiche.

### Modifier la plage de dates
Modifiez la période du journal d’audit en sélectionnant un des boutons **Aujourd’hui**, **Semaine passée**, **Mois passé** ou **Personnalisé**. Si vous choisissez le bouton **Personnalisé**, les champs de date **De** et **À** s’affichent pour que vous puissiez spécifier la plage de dates du journal. Vous pouvez entrer les dates au format JJ/MM/AAAA ou cliquer sur l’icône de **calendrier** et sélectionner une date dans un calendrier.

### Modifier les rôles inclus dans le journal

Cochez (ou décochez) la case **Rôle** en regard de chaque rôle à inclure dans le journal (ou à exclure de ce dernier).

Une fois définis tous les filtres du journal d’audit, cliquez sur Mettre à jour pour filtrer les données du journal. Si les données ne s’affichent pas immédiatement, actualisez la page.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->