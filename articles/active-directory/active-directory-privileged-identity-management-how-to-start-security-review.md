<properties
   pageTitle="Azure Privileged Identity Management : comment démarrer une révision de la sécurité"
   description="Découvrez comment créer une révision de la sécurité pour les identités privilégiées avec l’extension Azure Privileged Identity Management."
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
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management : comment démarrer une révision de la sécurité

## Démarrage d’une révision de la sécurité
Au terme de cette procédure, vous serez en mesure d’effectuer des révisions de la sécurité à d’autres emplacements dans le portail Azure. Ce document décrit les étapes de démarrage d’une révision de la sécurité au sein de l’interface **Privileged Identity Management (PIM)**.

Il y a peut-être des utilisateurs que vous ne reconnaissez pas ou des utilisateurs qui ont changé de tâches ou de projets et n’ont donc plus besoin d’un accès privilégié dans le cadre de leur nouveau poste. Afin de réduire les risques associés à ces affectations de rôles « obsolètes », vous et d’autres administrateurs pouvez réviser les rôles qui ont été donnés aux utilisateurs en démarrant une révision de la sécurité.

## Étapes pour démarrer une révision de la sécurité
> [AZURE.NOTE]Si vous n’avez pas encore créé de tableau de bord PIM dans le portail Azure, consultez les étapes dans [Mise en route avec Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)

Dans le tableau de bord Azure PIM, vous pouvez démarrer une révision grâce aux étapes suivantes :

- Tableau de bord > Révisions de la sécurité > Révision > bouton Révision
- Tableau de bord > Rôles > bouton Révision
- Tableau de bord > cliquez sur le rôle à réviser dans la liste des rôles > bouton Révision

## Démarrage d’une révision de la sécurité

Lorsque vous cliquez sur le bouton **Révision**, les volets **Démarrer la révision d’un rôle** et **Sélectionner un rôle à réviser** s’affichent et l’élément **Sélectionner un rôle à réviser** est sélectionné.

### Sélection du rôle à réviser

1. Sélectionnez le rôle dans la liste des rôles dans le volet **Sélectionner un rôle à réviser**. Vous ne pouvez choisir qu’un seul rôle à la fois. Le volet **Sélectionner un rôle à réviser** sera remplacé par le volet **Sélectionner des réviseurs**. Deux options s’offrent à vous lors de la sélection des réviseurs :
  - Moi : utilisez cette fonctionnalité si vous souhaitez afficher un aperçu du fonctionnement des révisions de la sécurité sans impliquer d’autres administrateurs.
  - Auto-révision par les membres du rôle : utilisez cette fonctionnalité pour demander aux utilisateurs de réviser leurs propres affectations de rôle eux-mêmes.
2. Sélectionnez l’une de ces options pour commencer à travailler avec les détails de la révision. Le volet **Modifier les paramètres par défaut** s’affiche.

### Révision par auto-révision

1. Nommez la révision en entrant son nom dans le champ **Nom**. Il est recommandé de choisir un nom unique qui décrit la révision et qui permet de facilement en effectuer le suivi.
2. Entrez une date de début de la révision dans le champ **Date de début**.
3. Entrez une date de fin de la révision dans le champ **Date de fin**. Éléments à prendre en considération lorsque vous définissez la date de fin de la révision :
  - Nombre de personnes révisées ?
  - Délai selon lequel les utilisateurs seront en mesure d’ajouter l’extension et d’effectuer la révision ?
4. Cliquez sur le bouton OK dans le volet **Modifier les paramètres par défaut**. Le volet se ferme.
5. Cliquez sur le bouton OK dans le volet **Démarrer une révision d’un rôle**. Le volet se ferme. Une notification s’affiche dans le menu principal du portail. Actualisez le tableau de bord en cliquant sur le bouton **Actualiser** ; la révision de la sécurité s’affiche dans la section **Révisions de la sécurité**.
6. Notifiez les individus du rôle qu’ils devront ajouter l’extension, puis réviser leur propre accès administratif. Voir l’étape suivante.
6. [Réviser l’accès administratif](active-directory-privileged-identity-management-how-to-perform-security-review.md)

### Révision par Moi

Si vous avez sélectionné l’option « Moi » pour le réviseur, passez à la révision de la sécurité. Pour plus d’informations sur l’exécution de la révision, consultez [Azure Privileged Identity Management : comment effectuer une révision de la sécurité](active-directory-privileged-identity-management-how-to-perform-security-review.md)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Sommaire PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->