<properties
   pageTitle="Démarrage d’une révision de la sécurité | Microsoft Azure"
   description="Découvrez comment créer une révision de la sécurité pour les identités privilégiées avec l’application Azure Privileged Identity Management."
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Comment démarrer une révision de sécurité dans Azure AD Privileged Identity Management

Les attributions de rôles deviennent « obsolètes » lorsque les utilisateurs bénéficient d’un accès privilégié dont ils n’ont plus besoin. Pour réduire les risques associés à ces affectations de rôles « obsolètes », les administrateurs de rôle privilégié doivent régulièrement réviser les rôles qui ont été donnés aux utilisateurs. Ce document décrit les étapes de démarrage d’une révision de la sécurité dans Azure AD Privileged Identity Management (PIM).

## Démarrer une révision de sécurité
> [AZURE.NOTE] Si vous n’avez pas ajouté l’application PIM à votre tableau de bord dans le portail Azure, consultez les étapes dans [Prise en main d’Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)

Dans la page principale application PIM, il y a trois manières de démarrer une révision de sécurité :

- **Révisions de sécurité** > **Révision** > bouton **Révision**
- **Rôles** > bouton **Révision**
- Sélectionnez le rôle à réviser dans la liste des rôles > bouton **Révision**

Lorsque vous cliquez sur le bouton **Révision**, les panneaux **Démarrer la révision d’un rôle** et **Sélectionner un rôle à réviser** s’affichent. L’élément **Sélectionner un rôle à réviser** sélectionné pour vous.

### Sélectionner le rôle à réviser

1. Sélectionnez le rôle dans la liste des rôles dans panneau **Sélectionner un rôle à réviser**. Vous ne pouvez choisir qu’un seul rôle à la fois. Le volet **Sélectionner un rôle à réviser** sera remplacé par le volet **Sélectionner des réviseurs**. Deux options s’offrent à vous lors de la sélection des réviseurs :
  - Moi : utilisez cette option si vous voulez afficher un aperçu du fonctionnement des révisions de la sécurité sans impliquer d’autres administrateurs.
  - Auto-révision par les membres du rôle : utilisez cette option pour demander aux utilisateurs de réviser leurs propres affectations de rôles.
2. Sélectionnez l’une de ces options pour commencer à travailler avec les détails de la révision. Le volet **Modifier les paramètres par défaut** s’affiche.

### Révision par Moi

Si vous avez sélectionné l’option « Moi » pour le réviseur, passez à la révision de la sécurité. Pour plus d’informations sur l’exécution de la révision, consultez [Azure Privileged Identity Management : comment effectuer une révision de la sécurité](active-directory-privileged-identity-management-how-to-perform-security-review.md)

### Auto-révision par les membres du rôle

Si vous choisissez de demander aux utilisateurs de réviser leurs propres affectations de rôles, procédez comme suit pour configurer la révision et envoyer des notifications.

1. Nommez la révision en entrant son nom dans le champ **Nom**. Donnez à la révision un nom unique qui la décrit et facilite son suivi.
2. Entrez une date de début de la révision dans le champ **Date de début**.
3. Entrez une date de fin de la révision dans le champ **Date de fin**. Éléments à prendre en considération lorsque vous définissez la date de fin de la révision :
  - Nombre de personnes révisées ?
  - Dans quel délai les utilisateurs seront-ils en mesure d’ajouter l’application PIM au portail Azure et d’effectuer la révision ?
4. Cliquez sur le bouton **OK** dans le panneau **Modifier les valeurs par défaut**. Le volet se ferme.
5. Cliquez sur le bouton **OK** dans le panneau **Démarrer une révision d’un rôle**. Le volet se ferme. Une notification s’affiche dans le menu principal du portail Azure. Actualisez le tableau de bord en cliquant sur le bouton **Actualiser** ; la révision de sécurité s’affiche dans la section **Révisions de sécurité**.
6. Informez les membres du rôle qu’ils devront ajouter l’application PIM, puis [réviser leur propre accès administrateur](active-directory-privileged-identity-management-how-to-perform-security-review.md).  

## Gérer la révision de sécurité

Vous pouvez suivre la progression des révisions des réviseurs dans le tableau de bord Azure AD PIM, dans la section des révisions de sécurité. Aucun droit d'accès ne sera modifié dans le répertoire avant que [la révision ne soit terminée](active-directory-privileged-identity-management-how-to-complete-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Sommaire PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->