<properties
   pageTitle="Utilisation du journal d’audit | Microsoft Azure"
   description="Découvrez comment utiliser le journal d’audit dans l’extension Azure Privileged Identity Management."
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Comment utiliser le journal d’audit dans Azure AD Privileged Identity Management

Vous pouvez utiliser le journal d’audit Privileged Identity Management (PIM) pour voir toutes les activations et affectations d’utilisateur dans un laps de temps donné.

## Accéder au journal d’audit
À partir du [portail Azure](https://portal.azure.com) et du tableau de bord, sélectionnez l’application **Azure AD Privileged Identity Management**. De là, vous pouvez accéder au journal d’audit en cliquant sur **Historique d’audit** dans le tableau de bord PIM.

## Graphique du journal d’audit
Le journal d’audit indique le nombre total d’activations, le nombre maximal d’activations par jour et la moyenne d’activations par jour dans un graphique linéaire. Vous pouvez également filtrer les données par rôle s’il existe plusieurs rôles dans l’historique d’audit.

Utilisez les boutons **temps**, **action** et **rôle** boutons pour trier le journal.

## Liste du journal d’audit
Les colonnes dans la liste du journal d’audit sont les suivantes :

- **Demandeur** : personne qui a demandé l’activation de rôle ou la modification. Si la valeur est « Système Azure », consultez le journal d'audit Azure pour plus d'informations.
- **Utilisateur** : l'utilisateur qui active un rôle ou y est affecté.
- **Rôle** : le rôle affecté ou activé par l'utilisateur.
- **Action** : les mesures prises par le demandeur. Ceci peut inclure l'attribution, la non-attribution, l’activation ou la désactivation.
- **Heure** : heure à laquelle l’action s’est produite.
- **Motif** : tout texte éventuellement entré dans le champ de motif pendant l’activation.
- **Expiration** : concerne uniquement l'activation de rôles.

## Filtrer le journal d’audit

Vous pouvez filtrer les informations qui s’affichent dans le journal d’audit en cliquant sur le bouton **Filtre**. Le panneau **Mettre à jour les paramètres du graphique** s’affiche.

Après avoir défini les filtres, cliquez sur **Mettre à jour** pour filtrer les données dans le journal. Si les données ne s’affichent pas immédiatement, actualisez la page.


### Modifier la plage de dates
Utilisez les boutons **Aujourd’hui**, **Semaine passée**, **Mois Dernier** ou **Personnalisé**.

Lorsque vous choisissez le bouton **Personnalisé**, les champs de date **De** et **À** s’affichent pour que vous puissiez spécifier une plage de dates pour le journal. Vous pouvez entrer les dates au format JJ/MM/AAAA ou cliquer sur l’icône de **calendrier** et sélectionner une date dans un calendrier.

### Modifier les rôles inclus dans le journal

Cochez (ou décochez) la case **Rôle** en regard de chaque rôle à inclure dans le journal (ou à exclure de ce dernier).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->