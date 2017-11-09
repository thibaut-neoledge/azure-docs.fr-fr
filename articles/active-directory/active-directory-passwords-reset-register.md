---
title: "Azure AD : inscription SSPR | Microsoft Docs"
description: "Inscrire les données d’authentification en vue de la réinitialisation de mot de passe en libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: b884f8bc3a20052fa0cb40772deef591b69d7b10
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="register-for-self-service-password-reset"></a>S’inscrire à la réinitialisation de mot de passe en libre-service

> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

En tant qu’utilisateur final, vous pouvez réinitialiser votre mot de passe ou déverrouiller votre compte, sans avoir à contacter qui que ce soit au moyen de la réinitialisation de mot de passe en libre-service (SSPR). Pour pouvoir utiliser cette fonctionnalité, il vous faut inscrire les méthodes d’authentification ou confirmer les méthodes d’authentification prédéfinies et remplies par votre administrateur.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Inscrire ou confirmer les données d’authentification avec SSPR

1. Ouvrez le navigateur web de votre appareil et accédez à la [page d’inscription à la réinitialisation de mot de passe](http://aka.ms/ssprsetup).
2. Entrez le nom d’utilisateur et le mot de passe qui vous ont été fournis par votre administrateur.
3. Selon la façon dont votre personnel informatique a effectué la configuration, une ou plusieurs des options suivantes peuvent être configurées et vérifiées par vous. Votre administrateur peut remplir une partie pour vous s’il a l’autorisation d’utiliser les informations.
    * Le téléphone de bureau ne peut être défini que par l’administrateur.
    * Le téléphone d’authentification doit être défini sur un autre numéro de téléphone auquel vous avez accès, comme un téléphone portable capable de recevoir un appel ou un SMS.
    * L’e-mail d’authentification doit être défini sur une autre adresse de messagerie à laquelle vous pouvez accéder sans le mot de passe à réinitialiser.
    * Les questions de sécurité vous donne une liste de questions fournies par votre administrateur, auxquelles vous devez répondre. Vous ne pouvez pas utiliser la même question ou réponse plusieurs fois.
4. Fournissez et vérifiez les informations demandées par votre administrateur. Si plusieurs options sont disponibles, nous vous proposons d’inscrire plusieurs méthodes afin d’assurer une certaine souplesse en cas d’indisponibilité d’une méthode (exemple : impossibilité d’accéder au téléphone de bureau en déplacement).

    ![Inscrire des méthodes d’authentification et cliquer sur Terminer][Register]

5. Lorsque vous avez terminé l’étape 4, sélectionnez **Terminer** ; vous pourrez à l’avenir utiliser la réinitialisation de mot de passe en libre-service en cas de besoin.

Si vous entrez des données dans le téléphone d’authentification ou l’adresse électronique d’authentification, elles ne sont pas visibles dans le répertoire global. Les seules personnes qui peuvent voir ces données sont vos administrateurs et vous-même. Vous êtes la seule personne à pouvoir voir les réponses à vos questions de sécurité.

Les administrateurs peuvent vous demander de confirmer vos méthodes d’authentification après quelque temps pour vérifier que les méthodes inscrites sont toujours les bonnes.

## <a name="common-problems-and-their-solutions"></a>Problèmes courants et leurs solutions

 Voici quelques cas d'erreur courants et leurs solutions :

| Cas d’erreur| Quelle erreur voyez-vous ?| Solution |
| --- | --- | --- |
| J’obtiens une page « Veuillez contacter votre administrateur » après avoir entré mon identifiant utilisateur. | Veuillez contacter votre administrateur. <br> <br> Nous avons détecté que votre mot de passe de compte d'utilisateur n'est pas géré par Microsoft. Par conséquent, nous ne pouvons pas réinitialiser automatiquement votre mot de passe. <br> <br> Vous devez contacter votre équipe informatique pour une aide supplémentaire. | Ce message s’affiche parce que votre service informatique gère votre mot de passe dans votre environnement local et ne vous autorise pas à réinitialiser votre mot de passe à partir du lien Votre compte n’est pas accessible. <br> <br> Pour réinitialiser votre mot de passe, contactez directement votre service informatique pour obtenir de l’aide et informez-le que vous voulez réinitialiser votre mot de passe pour qu’il puisse activer cette fonctionnalité pour vous.|
| J'obtiens une erreur « Votre compte n'est pas activé pour la réinitialisation de mot de passe » après avoir entré mon identifiant utilisateur. | Votre compte n’est pas activé pour la réinitialisation du mot de passe <br> <br> Nous sommes désolés, mais votre service informatique n’a pas configuré votre compte pour utiliser ce service. <br> <br> Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place. | Vous voyez ce message parce que votre service informatique n’a pas activé la réinitialisation de mot de passe pour votre organisation à partir du lien Votre compte n’est pas accessible ou ne vous a pas octroyé de licence vous permettant d’utiliser la fonctionnalité. <br> <br> Pour réinitialiser votre mot de passe, cliquez sur le lien Contacter un administrateur afin d’envoyer un courrier au service informatique de votre société : informez-le que vous voulez réinitialiser votre mot de passe pour qu’il puisse activer cette fonctionnalité pour vous. |
| J'obtiens une erreur « Impossible de vérifier votre compte » après avoir entré mon identifiant utilisateur. | Impossible de vérifier votre compte <br> <br> Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place. | Vous voyez ce message parce que vous êtes autorisé à réinitialiser le mot de passe, mais que vous ne vous êtes pas inscrit pour utiliser le service. Pour demander une réinitialisation du mot de passe, accédez à http://aka.ms/ssprsetup une fois que vous avez à nouveau accès à votre compte. <br> <br> Pour réinitialiser votre mot de passe, cliquez sur le lien Contacter un administrateur pour envoyer un courrier au service informatique de votre société. |

## <a name="next-steps"></a>Étapes suivantes

* [Guide pratique : modifier votre mot de passe avec la réinitialisation de mot de passe en libre-service](active-directory-passwords-update-your-own-password.md)
* [Page relative à l’inscription à la réinitialisation de mot de passe](http://aka.ms/ssprsetup)
* [Portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com/)
* [Connexion impossible à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Page relative à l’inscription à la réinitialisation de mot de passe, montrant les méthodes inscrites et le bouton Terminer"
