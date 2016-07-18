<properties
   pageTitle="Démarrage d’une révision d'accès | Microsoft Azure"
   description="Découvrez comment créer une révision d'accès pour les identités privilégiées avec l’application Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# Comment démarrer une révision de l’accès dans Azure AD Privileged Identity Management ?

Les attributions de rôles deviennent « obsolètes » lorsque les utilisateurs bénéficient d’un accès privilégié dont ils n’ont plus besoin. Pour réduire les risques associés à ces affectations de rôles « obsolètes », les administrateurs de rôle privilégié doivent régulièrement réviser les rôles qui ont été donnés aux utilisateurs. Ce document décrit les étapes de démarrage d’une révision de la sécurité dans Azure AD Privileged Identity Management (PIM).

## Démarrer une révision de sécurité
> [AZURE.NOTE] Si vous n’avez pas ajouté l’application PIM à votre tableau de bord dans le portail Azure, consultez les étapes dans [Prise en main d’Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)

Dans la page principale application PIM, il y a trois manières de démarrer une révision de sécurité :

- **Révisions d’accès** > **Ajouter**
- **Rôles** > bouton **Révision**
- Sélectionnez le rôle spécifique à réviser dans la liste des rôles > bouton **Révision**

Lorsque vous cliquez sur le bouton **Révision**, le panneau **Démarrer une vérification d’accès** s’affiche. Dans ce panneau, vous allez configurer la révision avec un nom et une limite de temps, choisir un rôle à réviser, et nommer la personne qui effectuera la révision.

![Démarrage d’une vérification d’accès - capture d’écran][1]

### Configuration de la révision

Pour créer une révision d’accès, vous devez la nommer et définir une date de début et de fin.

![Configuration d’une révision - capture d’écran][2]

Prévoyez une période suffisamment longue pour permettre aux utilisateurs de terminer la révision. Si vous avez terminé avant la date de fin, vous pouvez toujours arrêter la révision plus tôt.

### Sélection d’un rôle à réviser

Chaque révision se concentre sur un seul rôle. À moins d’avoir démarré la révision d’accès à partir d’un panneau de rôle spécifique, vous devez maintenant choisir un rôle.

1. Navigation vers **Réviser une appartenance à un rôle**

    ![Réviser une appartenance à un rôle - capture d’écran][3]

2. Choisissez un rôle dans la liste.

### Désignez la personne qui effectuera la révision

Il existe deux options pour effectuer une révision. Vous pouvez la faire vous-même, en approuvant ou refusant l’accès pour tous les utilisateurs d’un rôle. Ou vous pouvez laisser chaque utilisateur réviser son propres accès.

1. Navigation vers **Sélectionner des réviseurs**

    ![Sélection des réviseurs - capture d’écran][4]

2. Choisissez l'une des options :
    - **Moi** : utile si vous souhaitez afficher un aperçu du fonctionnement des révisions d’accès ou effectuer une révision pour le compte de personnes qui ne peuvent pas le faire.
    - **Auto-révision par les membres du rôle** : utilisez cette option pour demander aux utilisateurs de réviser leurs propres affectations de rôles.

### Démarrage d’une révision

Enfin, vous pouvez obliger les utilisateurs à indiquer le motif pour lequel ils approuvent leur accès. Ajoutez une description de la révision si vous le souhaitez, puis sélectionnez **Démarrer**.

Assurez-vous d’informer vos utilisateurs qu’une révision d’accès les attend, puis montrez-leur [comment effectuer une révision d’accès](active-directory-privileged-identity-management-how-to-perform-security-review.md).

## Gestion de la révision d’accès

Vous pouvez suivre la progression des révisions des réviseurs dans le tableau de bord Azure AD PIM, dans la section des révisions d'accès. Aucun droit d'accès ne sera modifié dans le répertoire avant que [la révision ne soit terminée](active-directory-privileged-identity-management-how-to-complete-review.md).

Tant que la période de révision n’est pas terminée, vous pouvez rappeler aux utilisateurs d’effectuer leur révision, ou arrêter la révision au début de la section des révisions d’accès.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Sommaire PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png

<!---HONumber=AcomDC_0706_2016-->