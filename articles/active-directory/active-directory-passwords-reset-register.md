---
title: "Azure AD : inscription à la réinitialisation de mot de passe en libre-service | Microsoft Docs"
description: "Inscrire les données d’authentification en vue de la réinitialisation de mot de passe en libre-service"
services: active-directory
keywords: "Gestion des mots de passe Active Directory, gestion des mots de passe, réinitialisation de mot de passe en libre-service Azure AD, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 0f10ad4904b10a9554e949e77f0192edcb8f624f
ms.lasthandoff: 04/12/2017


---
# <a name="register-for-self-service-password-reset"></a>S’inscrire à la réinitialisation de mot de passe en libre-service

Si votre administrateur a activé la réinitialisation de mot de passe en libre-service (SSPR), vous pouvez réinitialiser votre mot de passe ou déverrouiller votre compte en tant qu’utilisateur final, sans avoir à contacter qui que ce soit. Pour pouvoir utiliser cette fonctionnalité, il vous faut inscrire les méthodes d’authentification ou confirmer les méthodes d’authentification prédéfinies et remplies par votre administrateur.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Inscrire ou confirmer les données d’authentification avec SSPR

1. Ouvrez le navigateur web de votre appareil et accédez à la [page d’inscription à la réinitialisation de mot de passe](http://aka.ms/ssprsetup).
2. Entrez le nom d’utilisateur et le mot de passe qui vous ont été fournis par votre administrateur.
3. Selon les options approuvées par votre administrateur, différents éléments de la liste suivante apparaîtront ; vous devrez les configurer ou les vérifier pour les utiliser en cas de réinitialisation de mot de passe :
    * Téléphone de bureau : cette option ne peut être définie que par l’administrateur.
    * Téléphone d’authentification : cette option doit être définie sur un autre numéro de téléphone auquel vous avez accès, comme un téléphone portable en mesure de recevoir un SMS ou un appel (votre administrateur peut la remplir pour vous avec votre numéro de téléphone portable s’il a déjà votre autorisation d’utiliser ces informations).
    * Adresse électronique d’authentification : cette option doit être définie sur une autre adresse de messagerie à laquelle vous pouvez accéder sans le mot de passe à réinitialiser.
    * Questions de sécurité : cette option vous donne une liste de questions fournies par votre administrateur, auxquelles vous devez répondre. Vous ne pouvez pas utiliser la même réponse pour plusieurs questions.
4. Fournissez et vérifiez les informations demandées par votre administrateur. Si plusieurs options sont disponibles, nous vous proposons d’inscrire plusieurs méthodes afin d’assurer une certaine souplesse en cas d’indisponibilité d’une méthode (exemple : impossibilité d’accéder au téléphone de bureau en déplacement).

    ![Inscrire des méthodes d’authentification et cliquer sur Terminer][Register]

5. Lorsque vous avez terminé l’étape 4, sélectionnez **Terminer** ; vous pourrez à l’avenir utiliser la réinitialisation de mot de passe en libre-service en cas de besoin.

Si vous entrez des données dans le téléphone d’authentification ou l’adresse électronique d’authentification, elles ne sont pas visibles dans le répertoire global. Les seules personnes qui peuvent voir ces données sont vos administrateurs et vous-même. Vous êtes la seule personne à pouvoir voir les réponses à vos questions de sécurité.

Les administrateurs peuvent vous demander de confirmer vos méthodes d’authentification après quelque temps pour vérifier que les méthodes inscrites sont toujours les bonnes.

## <a name="next-steps"></a>Étapes suivantes

* [Guide pratique : modifier votre mot de passe avec la réinitialisation de mot de passe en libre-service](active-directory-passwords-update-your-own-password.md)
* [Page relative à l’inscription à la réinitialisation de mot de passe](http://aka.ms/ssprsetup)
* [Portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Page relative à l’inscription à la réinitialisation de mot de passe, montrant les méthodes inscrites et le bouton Terminer"

