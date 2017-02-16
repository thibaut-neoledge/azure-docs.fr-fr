---
title: "Éléments de l’e-mail d’invitation de collaboration d’Azure Active Directory B2B | Microsoft Docs"
description: "Modèle d’e-mail d’invitation de collaboration d’Azure Active Directory B2B"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/01/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 8fdd2993e6b3f0345948f35753ebb7a20ed174e3
ms.openlocfilehash: 5a31469014b7b0c7ea8a6d6a207646694c6c0b50


---


# <a name="the-elements-of-the-b2b-collaboration-invitation-email"></a>Éléments de l’e-mail d’invitation de collaboration B2B

Les e-mails d’invitation sont un composant essentiel pour intégrer des partenaires comme utilisateurs de collaboration B2B dans Azure AD. L’objectif principal est d’augmenter le niveau de confiance dans le chef du destinataire, et d’ajouter de la légitimité et une preuve sociale dans l’e-mail, de façon à ce que le destinataire sélectionne sans crainte le bouton **Bien démarrer** pour accepter l’invitation. Il s’agit d’un composant clé pour réduire les problèmes de partage. Par ailleurs, une apparence attractive de l’e-mail est également importante !

## <a name="explaining-the-email"></a>Explication de l’e-mail
Examinons quelques-uns des éléments de l’e-mail pour savoir comment utiliser au mieux ces fonctionnalités.

### <a name="subject"></a>Objet
L’objet de l’email suit le modèle suivant : vous êtes invité à rejoindre l’organisation &lt;nom_locataire&gt;

### <a name="from-address"></a>Adresse de l’expéditeur
Nous utilisons un modèle similaire à LinkedIn pour l’adresse De. Notre objectif ici est de faire apparaître clairement qui est l’inviteur et à quelle entreprise il appartient, ainsi que d’indiquer que l’e-mail provient d’une adresse e-mail Microsoft. Le format est : &lt;nom d’affichage de l’inviteur&gt; de &lt;nom_locataire&gt; (via Microsoft)<invites@microsoft.com&gt;

### <a name="reply-to"></a>Adresse de réponse
L’adresse e-mail de réponse est définie sur l’adresse e-mail de l’inviteur quand elle est disponible : ainsi, répondre à l’e-mail envoie un e-mail en retour à l’inviteur.

### <a name="branding"></a>Personnalisation
Les e-mails d’invitation de votre locataire utilisent la personnalisation d’entreprise que vous avez éventuellement définie pour votre locataire. Si vous voulez en tirer parti, voici des informations sur la façon de le configurer. Le logo de la bannière apparaît dans l’e-mail. Suivez ces instructions relatives à la taille et à la qualité de l’image pour obtenir un résultat optimal. En outre, le nom de l’entreprise apparaît également dans l’invite à l’action.

### <a name="call-to-action"></a>Invite à l’action
L’invite à l’action se compose de deux parties : explication de la raison pour laquelle le destinataire a reçu l’e-mail et ce qu’il est demandé comme action au destinataire.
- La section « Pourquoi » peut être formulée selon le modèle suivant : Vous avez été invité à accéder à des applications dans l’organisation &lt;nom_locataire&gt;

- Et la section « Ce qu’il vous est demandé de faire » est indiquée par la présence du bouton **Bien démarrer**. Quand le destinataire a été ajouté sans que des invitations soient nécessaires, ce bouton ne s’affiche pas.

### <a name="inviters-information"></a>Informations de l’inviteur
Le nom d’affichage de l’inviteur est inclus dans l’e-mail. En outre, si vous avez configuré une image de profil pour votre compte Azure AD, l’e-mail d’invitation inclut également cette image. Ces deux éléments sont destinés à améliorer la confiance de votre destinataire dans l’e-mail.
Si l’inviteur n’a pas encore configuré son image de profil, Azure AD crée une icône avec les initiales de l’inviteur à la place de l’image, comme ceci :

  ![affichage des initiales de l’inviteur](media/active-directory-b2b-invitation-email/inviters-initials.png)

### <a name="body"></a>Corps
Il contient le message que l’inviteur a tapé dans l’interface utilisateur ou a passé via l’API d’invitation. Ceci est une simple zone de texte qui ne traite pas les balises HTML pour des raisons de sécurité.

### <a name="footer-section"></a>Section Pied de page

Le pied de page contient la marque de la société Microsoft et permet au destinataire de savoir si l’e-mail a été envoyé depuis un alias non surveillé. Cas particuliers :

- L’inviteur n’a pas d’adresse e-mail dans la location de l’inviteur

  ![image de l’inviteur sans adresse e-mail dans la location de l’inviteur](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- Le destinataire n’a pas besoin de réclamer l’invitation

  ![quand le destinataire n’a pas besoin de réclamer l’invitation](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


