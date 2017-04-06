---
title: "Personnalisation : gestion des mots de passe Azure Active Directory | Microsoft Docs"
description: "Comment personnaliser l’apparence, le comportement et les notifications de gestion de mot de passe dans Azure AD en fonction de vos besoins."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 2cddd150-8747-447a-a7cf-1d7d5775c0b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 06d94b4f0f2c5cc979fe7ecb061fee7d8dde8972
ms.lasthandoff: 03/28/2017


---
# <a name="customizing-password-management-to-fit-your-organizations-needs"></a>Personnalisation de la gestion des mots de passe en fonction des besoins de votre organisation
> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#reset-your-password).
>
>

Pour offrir la meilleure expérience possible à vos utilisateurs, nous vous recommandons d’explorer et de tester les options de configuration de la gestion de mot de passe qui sont à votre disposition. De fait, vous pouvez de suite commencer cette exploration en ouvrant l’onglet de configuration de **l’extension Active Directory** dans le [portail Azure Classic](https://manage.windowsazure.com). Cette rubrique vous présente toutes les possibilités de personnalisation de la gestion des mots de passe que vous appliquez en tant qu’administrateur depuis l’onglet **Configurer** de votre répertoire sur le [portail Azure Classic](https://manage.windowsazure.com).

## <a name="what-customization-options-are-available"></a>Quelles sont les options de personnalisation disponibles ?
Le tableau ci-dessous répertorie toutes les options de personnalisation disponibles avec la fonction Réinitialisation du mot de passe d’Azure Active Directory.

| Rubrique | Paramètre | Licences requises |
| --- | --- | --- |
| Comment activer ou désactiver la réinitialisation des mots de passe ? |[Paramétrage : utilisateurs autorisés à réinitialiser leur mot de passe](#users-enabled-for-password-reset) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment appliquer la réinitialisation de mot de passe à un ensemble spécifique d’utilisateurs ? |[Restreindre la réinitialisation de mot de passe à certains utilisateurs](#restrict-access-to-password-reset) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment modifier la liste des méthodes d’authentification prises en charge ? |[Paramètre : méthodes d'authentification accessibles aux utilisateurs](#authentication-methods-available-to-users) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment modifier le nombre de méthodes d’authentification requis ? |[Paramètre : nombre de méthodes d’authentification requis](#number-of-authentication-methods-required) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment configurer les questions de sécurité personnalisées ? |[Paramétrage : questions de sécurité personnalisées](#custom-security-questions) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment définir des questions de sécurité localisées prédéfinies ? |[Paramètre : questions de sécurité basées sur des informations](#knowledge-based-security-questions) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment modifier le nombre de questions de sécurité requis ? |[Paramétrage : nombre de questions de sécurité pour l’inscription ou la réinitialisation](#number-of-questions-required-to-register) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment obliger les utilisateurs à s’inscrire lorsqu’ils se connectent ? |[Inscription obligatoire lors de la réinitialisation de mot de passe](#require-users-to-register-when-signing-in) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment obliger mes utilisateurs à confirmer périodiquement qu'ils sont bien inscrits ? |[Paramétrage : nombre de jours avant que les utilisateurs ne doivent confirmer leurs données d’authentification](#number-of-days-before-users-must-confirm-their-contact-data) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment personnaliser la façon dont un utilisateur contacte un administrateur ? |[Paramètre : personnaliser le lien « Contactez votre administrateur »](#customize-the-contact-your-administrator-link) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment activer ou désactiver l’écriture différée du mot de passe depuis l’expérience administrateur de cloud ? |[Paramètre : activer ou désactiver l’écriture différée du mot de passe](#write-back-passwords-to-on-premises-directory) | <ul><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment autoriser les utilisateurs à déverrouiller des comptes Active Directory locaux sans réinitialiser un mot de passe ? |[Paramètre : autoriser les utilisateurs à déverrouiller leurs comptes AD sans réinitialiser un mot de passe](#allow-users-to-unlock-accounts-without-resetting-their-password) | <ul><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment activer les notifications de réinitialisation des mots de passe pour les utilisateurs ? |[Paramètre : avertir les utilisateurs quand leurs mots de passe ont été réinitialisés](#notify-users-and-admins-when-their-own-password-has-been-reset) |  <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment activer les notifications de réinitialisation de mot de passe pour les administrateurs ? |[Paramètre : avertir les autres administrateurs quand un administrateur réinitialise leur mot de passe](#notify-admins-when-other-admins-reset-their-own-passwords) | <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |
| Comment personnaliser l’apparence de la réinitialisation de mot de passe ? |[Paramètre : nom, marque et logo de la société ](#password-management-look-and-feel) |  <ul><li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li><li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li><li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Mobility Suite [utilisateurs sur le cloud ou locaux]</li><li>Enterprise Cloud Suite [utilisateurs sur le cloud ou locaux]</li></ul> |

## <a name="password-management-look-and-feel"></a>Interface de gestion des mots de passe
Le tableau suivant décrit comment chaque contrôle affecte l’expérience des utilisateurs qui s’inscrivent pour réinitialiser leur mot de passe.  Vous pouvez configurer ces options dans la section **Propriétés de l’annuaire** de l’onglet **Configurer**, sur le [portail de gestion Azure](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Contrôle de stratégie</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Description</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Éléments affectés</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="directory-name">
                  <p>Nom de l’annuaire</p>
                </div>
              </td>
              <td>
                <p>Détermine le nom que voient les utilisateurs et les administrateurs dans les différentes communications relatives à la réinitialisation du mot de passe.</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Messages « Contactez votre administrateur » :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine le nom convivial de l’adresse d’expédition, par exemple « Microsoft, de la part de <strong>Wingtip Toys</strong> »<br><br></li>
                  <li class="unordered">
Détermine l’objet du message, par exemple : « Code de vérification du compte <strong>Wingtip Toys</strong> »<br><br></li>
                </ul>
                <p>
                  <strong>Messages de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine le nom convivial de l’adresse d’expédition, par exemple « Microsoft, de la part de <strong>Wingtip Toys</strong> »<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="sign-in-and-access-panel-page-appearance">
                  <p>Apparence de la page de connexion et de la page du panneau d’accès</p>
                </div>
              </td>
              <td>
                <p>Détermine si les utilisateurs qui accèdent à la page de réinitialisation du mot de passe voient le logo Microsoft ou votre propre logo.  Cet élément de configuration ajoute également votre marque au panneau d’accès et à la page de connexion.</p>
                <p>Pour plus d’informations sur la fonctionnalité de personnalisation et sur l’ajout d’une marque, consultez <a href="https://technet.microsoft.com/library/dn532270.aspx">Ajout d’une marque de société aux pages de connexion et aux pages du panneau d’accès</a>.</p>
                                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine si votre logo apparaît en haut du portail de réinitialisation du mot de passe, à la place du logo Microsoft par défaut.<br><br>
                    <strong>Remarque :</strong> il est possible que vous ne voyiez pas votre logo sur la première page du portail de réinitialisation du mot de passe si vous arrivez directement de la page de réinitialisation du mot de passe. Une fois que l’utilisateur entre son nom d’utilisateur et clique sur Suivant, votre logo apparaît.<br><br>
Vous pouvez forcer l’affichage de votre logo au chargement de la page en transmettant le paramètre <code>whr</code> à la page de réinitialisation du mot de passe, comme suit : <code><a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a></code><br><br>
Vous pouvez générer un lien qui pré-remplit le champ du nom d’utilisateur, en transmettant le paramètre <code>username</code>. Le logo de votre organisation (si configuré) est également chargé :<code><a href="https://passwordreset.microsoftonline.com?username=user%40wingtiptoysonline.com">https://passwordreset.microsoftonline.com?username=user%40wingtiptoysonline.com</a></code></li>
                </ul>
                <p>
                  <strong>Messages « Contactez votre administrateur » :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine si votre logo apparaît en bas des messages envoyés aux administrateurs lorsque les utilisateurs choisissent de vous contacter en cliquant sur le lien « Contactez votre administrateur » dans l’interface de réinitialisation du mot de passe.<br><br></li>
                </ul>
                <p>
                  <strong>Messages de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine si votre logo apparaît en bas des messages envoyés aux utilisateurs lorsqu’ils réinitialisent leur mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## <a name="password-management-behavior"></a>Comportement de la gestion des mots de passe
Le tableau suivant décrit comment chaque contrôle affecte l’expérience des utilisateurs qui s’inscrivent pour réinitialiser leur mot de passe.  Vous pouvez configurer ces options dans la section **Stratégie de réinitialisation du mot de passe utilisateur** de l’onglet **Configurer** du [portail de gestion Azure](https://manage.windowsazure.com).

> [!NOTE]
> Le compte d’administrateur que vous utilisez doit posséder une licence AAD Premium pour voir ces contrôles de stratégie.<br><br>Ces contrôles ne s’appliquent qu’aux utilisateurs qui réinitialisent leur mot de passe, et non aux administrateurs.  **Les administrateurs ont une stratégie de messagerie et/ou téléphone portable spécifiée par défaut par Microsoft et qui ne peut pas être modifiée.**
>
>

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Contrôle de stratégie</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Description</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Éléments affectés</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="users-enabled-for-password-reset">
                  <p>Utilisateurs autorisés à réinitialiser leur mot de passe</p>
                </div>
              </td>
              <td>
                <p>Détermine si la réinitialisation du mot de passe est activée pour les utilisateurs de cet annuaire. </p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si la valeur est Non, les utilisateurs ne peuvent pas inscrire leurs propres données de vérification.<br><br></li>
                  <li class="unordered">
Si la valeur est Oui, n’importe quel utilisateur de l’annuaire peut inscrire des données via le portail d’inscription à l’adresse <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque :</strong> les utilisateurs doivent disposer d’une licence Azure AD Premium ou Basic avant de pouvoir s’inscrire pour réinitialiser leur mot de passe.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si la valeur est Non, les utilisateurs voient un message indiquant qu’ils doivent contacter l’administrateur pour réinitialiser leur mot de passe.<br><br></li>
                  <li class="unordered">
Si la valeur est Oui, les utilisateurs peuvent réinitialiser leur mot de passe automatiquement à l’adresse <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a>, ou en cliquant sur le lien <strong>Vous ne pouvez pas accéder à votre compte ?</strong> sur la page de connexion de l’organisation.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque :</strong> les utilisateurs doivent disposer d’une licence Azure AD Premium ou Basic avant de pouvoir réinitialiser leur mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="restrict-access-to-password-reset">
                  <p>Limiter l’accès à la réinitialisation des mots de passe</p>
                </div>
              </td>
              <td>
                <p>Détermine si seul un groupe d’utilisateurs en particulier est autorisé à utiliser la réinitialisation du mot de passe. (Visible uniquement si <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est défini sur <strong>Oui</strong>).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Ce paramètre n’affecte pas l’accès des utilisateurs au portail d’inscription de réinitialisation du mot de passe. Si l’option <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est définie sur <strong>oui</strong>, tous les utilisateurs de votre annuaire peuvent s’inscrire à la réinitialisation du mot de passe à l’adresse <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.
                  </li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si la valeur est Non, tous les utilisateurs de votre annuaire peuvent réinitialiser leur mot de passe.<br><br></li>
                  <li class="unordered">
Si la valeur est Oui, seuls les utilisateurs spécifiés dans le contrôle <strong>Groupe pouvant effectuer la réinitialisation des mots de passe</strong> peuvent réinitialiser leur mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="group-that-can-perform-password-reset">
                  <p>Groupe pouvant effectuer la réinitialisation des mots de passe</p>
                </div>
              </td>
              <td>
                <p>Détermine quel groupe d’utilisateurs est autorisé à utiliser la réinitialisation du mot de passe. </p>
                <p>(Visible uniquement si <strong>Limiter l’accès à la réinitialisation des mots de passe</strong> est défini sur <strong>Oui</strong>).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si aucun groupe n’est spécifié et que vous cliquez sur <strong>Enregistrer</strong>, un groupe vide <strong>SSPRSecurityGroupUsers</strong> est créé pour vous.<br><br></li>
                  <li class="unordered">
Si vous voulez spécifier votre propre groupe, vous pouvez indiquer votre propre nom d’affichage.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Ce paramètre n’affecte pas l’accès des utilisateurs au portail d’inscription de réinitialisation du mot de passe. Si l’option <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est définie sur <strong>oui</strong>, tous les utilisateurs de votre annuaire peuvent s’inscrire à la réinitialisation du mot de passe à l’adresse <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.
                  </li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si <strong>Limiter l’accès à la réinitialisation des mots de passe</strong> est défini sur <strong>Oui</strong>, seuls les utilisateurs de ce groupe peuvent réinitialiser leur mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="authentication-methods-available-to-users">
                  <p>Méthodes d’authentification accessibles aux utilisateurs</p>
                </div>
              </td>
              <td>
                <p>Détermine les vérifications que les utilisateurs sont autorisés à utiliser pour réinitialiser leur mot de passe.</p>
                <p>(Visible uniquement si <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est défini sur <strong>Oui</strong>).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Vous devez sélectionner au moins une option.<br><br></li>
                  <li class="unordered">
Nous vous recommandons vivement d’activer au moins deux options pour offrir à vos utilisateurs une plus grande flexibilité lors de la réinitialisation de leur mot de passe.<br><br></li>
                  <li class="unordered">
Si vous utilisez des questions de sécurité, nous vous recommandons vivement de les utiliser avec une autre méthode d’authentification. Les questions de sécurité peuvent être moins sûres que les méthodes de réinitialisation par téléphone ou par courrier électronique.<br><br></li>
                </ul>
                <p>
                  <strong>Quels sont les champs de l’annuaire qui sont utilisés ?</strong>
                </p>
                <ul>
                  <li class="unordered">
Téléphone professionnel correspond à l’attribut <strong>Téléphone professionnel</strong> de l’objet utilisateur dans l’annuaire.<br><br></li>
                  <li class="unordered">
Téléphone mobile correspond à l’attribut <strong>Portable d’authentification</strong> (qui n’est pas public) ou à l’attribut <strong>Téléphone mobile</strong> (qui est public) de l’objet utilisateur dans l’annuaire.  Le service vérifie d’abord les données de <strong>Téléphone d’authentification</strong>. Si elles n’existent pas, il revient à l’attribut <strong>Téléphone mobile</strong>.<br><br></li>
                  <li class="unordered">
L’adresse électronique alternative correspond à l’attribut <strong>Adresse électronique d'authentification</strong> (qui n’est pas public) ou à l’attribut <strong>Adresse de messagerie de secours</strong> (qui est public) de l’objet utilisateur dans l’annuaire.  Le service vérifie d’abord les données de <strong>Adresse électronique d’authentification</strong>. Si elles n’existent pas, il revient à l’attribut <strong>Adresse de messagerie de secours</strong>.<br><br></li>
                  <li class="unordered">
Les questions de sécurité sont stockées de façon privée et sécurisée dans un objet utilisateur dans l’annuaire et ne peuvent être posées qu’aux utilisateurs lors de l’inscription.  Pour des raisons de sécurité, il n’existe actuellement aucun moyen pour l’administrateur de modifier ou de voir ces réponses.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque :</strong> par défaut, seuls les attributs cloud Téléphone professionnel et Téléphone mobile sont synchronisés dans votre annuaire cloud à partir de votre annuaire local.  Pour en savoir plus sur les attributs locaux qui sont synchronisés avec le cloud, consultez la page <a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Attributs synchronisés avec Azure AD.</a><br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Affecte les méthodes d’authentification affichées lors de l’inscription des utilisateurs.  Si vous n’activez pas une méthode d’authentification donnée, les utilisateurs ne pourront pas s’inscrire automatiquement à cette méthode d’authentification.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque :</strong> les utilisateurs ne peuvent actuellement pas inscrire leur propre numéro de téléphone professionnel. Cette méthode d’authentification doit être définie par leur administrateur.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine quelles méthodes d’authentification l’utilisateur peut utiliser comme vérifications pour une étape de vérification donnée.  Par exemple, si un utilisateur possède à la fois des données dans les champs <strong>Téléphone professionnel</strong> et <strong>Téléphone d’authentification</strong> dans Azure Active Directory, il peut utiliser une de ces méthodes d’authentification pour récupérer son mot de passe.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque :</strong> les utilisateurs ne peuvent réinitialiser leur mot de passe que si et seulement s’ils ont des données présentes dans les méthodes d’authentification que vous avez activées en tant qu’administrateur.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-authentication-methods-required">
                  <p>Nombre de méthodes d’authentification requises</p>
                </div>
              </td>
              <td>
                <p>Détermine le nombre minimal de méthodes d’authentification disponibles que l’utilisateur doit appliquer pour réinitialiser son mot de passe.</p>
                <p>(Visible uniquement si <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est défini sur <strong>Oui</strong>).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Peut être défini sur 1 ou 2 méthodes d’authentification.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine le nombre minimal de méthodes d’authentification auxquelles l’utilisateur doit s’inscrire avant de pouvoir terminer l’inscription.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Affecte le nombre d’étapes de vérification par lesquelles doit passer l’utilisateur avant de pouvoir réinitialiser son mot de passe.  Une étape de vérification est une procédure dans laquelle l’utilisateur recourt à une information d’authentification (un appel à son téléphone professionnel ou un courrier électronique à son adresse de messagerie de secours) pour vérifier son compte.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque :</strong> si l’utilisateur ne dispose pas de suffisamment d’informations d’authentification définies sur son compte pour que la réinitialisation de son mot de passe réussisse conformément à la stratégie que vous avez définie, il verra s’afficher une page d’erreur lui demandant de contacter un administrateur pour réinitialiser son mot de passe.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-register">
                  <p>Nombre de questions requises pour l’inscription</p>
                </div>
              </td>
              <td>
                <p>Détermine le nombre minimal de questions auxquelles un utilisateur doit répondre lors de l’inscription pour l’option des questions de sécurité.</p>
                <p>(Visible uniquement si la case à cocher <strong>Questions de sécurité</strong> est activée).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Il est possible de définir de 3 à 5 questions pour l’inscription.<br><br></li>
                  <li class="unordered">
Le nombre de questions requises pour l’inscription doit être supérieur ou égal au nombre de questions requises pour la réinitialisation.<br><br></li>
                  <li class="unordered">
Nous vous recommandons de définir un nombre de questions pour l’inscription supérieur au nombre de questions requises pour la réinitialisation afin que les utilisateurs bénéficient de plus de souplesse lors de la réinitialisation de leur mot de passe.  Il s’agit également d’une configuration plus sécurisée, les questions sont sélectionnées au hasard dans toutes les questions qu’ils ont inscrites.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine le nombre minimal de questions auxquelles un utilisateur doit répondre avant qu’il ne soit considéré comme entièrement inscrit à la réinitialisation du mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-reset">
                  <p>Nombre de questions requises pour la réinitialisation </p>
                </div>
              </td>
              <td>
                <p>Détermine le nombre minimal de questions auxquelles un utilisateur doit répondre lors de la réinitialisation du mot de passe.</p>
                <p>(Visible uniquement si la case à cocher <strong>Questions de sécurité</strong> est activée).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Il est possible de définir de 3 à 5 questions pour la réinitialisation.<br><br></li>
                  <li class="unordered">
Le nombre de questions requises pour la réinitialisation doit être inférieur ou égal au nombre de questions requises pour l’inscription.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine le nombre minimal de questions auxquelles un utilisateur doit répondre avant que son mot de passe ne puisse être réinitialisé.<br><br></li>
                  <li class="unordered">
Au moment de la réinitialisation du mot de passe, ce nombre de questions est sélectionné au hasard dans la liste des questions inscrites par l’utilisateur.  Par exemple, si l’utilisateur a inscrit 5 questions, 3 de ces 5 questions sont sélectionnées au hasard lorsque l’utilisateur demande à réinitialiser son mot de passe.  L’utilisateur doit ensuite répondre à toutes ces questions correctement avant que le mot de passe ne puisse être réinitialisé.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="knowledge-based-security-questions">
                  <p>Questions de sécurité basées sur des informations</p>
                </div>
              </td>
              <td>
                <p>Définit les questions préconçues de sécurité que vos utilisateurs peuvent choisir au moment de l’inscription à la réinitialisation du mot de passe et au moment de la réinitialisation de leur mot de passe.</p>
                <p>(Visible uniquement si la case à cocher <strong>Questions de sécurité</strong> est activée).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Toutes les questions basées sur des informations seront localisées dans l’ensemble des versions linguistiques d’Office 365 en fonction des paramètres régionaux du navigateur de l’utilisateur.<br><br></li>
                  <li class="unordered">
Jusqu’à 20 questions peuvent être définies (soit la somme de vos questions personnalisées et des questions basées sur des informations).<br><br></li>
                 <li class="unordered">
La limite minimale du nombre de caractères par question est de 3 caractères.<br><br></li>
                  <li class="unordered">
La limite maximale du nombre de caractères par réponse est de 40 caractères.<br><br></li>
                  <li class="unordered">
L’utilisateur ne peut pas répondre deux fois à la même question.<br><br></li>
                  <li class="unordered">
L’utilisateur ne peut pas fournir la même réponse à deux questions différentes.<br><br></li>
                  <li class="unordered">
N’importe quel jeu de caractères peut être utilisé pour définir les réponses (y compris les caractères Unicode).<br><br></li>
                  <li class="unordered">
Le nombre de questions définies doit être supérieur ou égal au nombre de questions requises pour l’inscription.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine les questions auxquelles l’utilisateur est en mesure de répondre lors de l’inscription pour la réinitialisation du mot de passe.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine les questions que l’utilisateur peut utiliser pour réinitialiser son mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-security-questions">
                  <p>Questions de sécurité personnalisées</p>
                </div>
              </td>
              <td>
                <p>Définit les questions de sécurité que vos utilisateurs peuvent choisir lors de l’inscription à la réinitialisation du mot de passe et lors de la réinitialisation de leur mot de passe.</p>
                <p>(Visible uniquement si la case à cocher <strong>Questions de sécurité</strong> est activée).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Jusqu’à 20 questions peuvent être définies (soit la somme de vos questions personnalisées et des questions basées sur des informations).<br><br></li>
                  <li class="unordered">
La limite maximale du nombre de caractères par question est de 200 caractères.<br><br></li>
                  <li class="unordered">
La limite minimale du nombre de caractères par question est de 3 caractères.<br><br></li>
                  <li class="unordered">
La limite maximale du nombre de caractères par réponse est de 40 caractères.<br><br></li>
                  <li class="unordered">
L’utilisateur ne peut pas répondre deux fois à la même question.<br><br></li>
                  <li class="unordered">
L’utilisateur ne peut pas fournir la même réponse à deux questions différentes.<br><br></li>
                  <li class="unordered">
N’importe quel jeu de caractères peut être utilisé pour définir les questions et les réponses (y compris les caractères Unicode).<br><br></li>
                  <li class="unordered">
Le nombre de questions définies doit être supérieur ou égal au nombre de questions requises pour l’inscription.<br><br></li>
                  <li class="unordered">
Il n’est pas possible de définir différentes questions personnalisées en fonction des paramètres régionaux.  Toutes les questions personnalisées s’affichent dans la langue dans laquelle vous les entrez dans l’interface utilisateur d’administration, même si les paramètres régionaux du navigateur de l’utilisateur sont différents.  Si vous avez besoin que ces questions soient localisées, utilisez à place les questions « basées sur des informations ».<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine les questions auxquelles l’utilisateur est en mesure de répondre lors de l’inscription pour la réinitialisation du mot de passe.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Détermine les questions que l’utilisateur peut utiliser pour réinitialiser son mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="require-users-to-register-when-signing-in">
                  <p>Obliger les utilisateurs à s’inscrire durant la connexion ?</p>
                </div>
              </td>
              <td>
                <p>Détermine si les utilisateurs doivent fournir leurs coordonnées pour la réinitialisation du mot de passe la prochaine fois qu’ils se connecteront.  
                </p>
                <p>Cette fonctionnalité fonctionne sur n’importe quelle page de connexion qui utilise un compte professionnel ou scolaire.  Ces pages incluent toutes les applications Office 365, le portail de gestion Azure, le panneau d’accès et toutes les applications fédérées ou personnalisées qui utilisent Azure AD pour se connecter.
                </p>
                <p>L’inscription obligatoire s’applique uniquement aux utilisateurs qui sont autorisés à réinitialiser le mot de passe, ce qui signifie que vous avez utilisé la fonctionnalité « Limiter l’accès à la réinitialisation des mots de passe » et étendu la réinitialisation des mots de passe à certains utilisateurs, qui devront donc s’inscrire à la réinitialisation de mot de passe lors de la connexion.</p>
                <p>(Visible uniquement si <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est défini sur <strong>Oui</strong>).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si vous désactivez cette fonctionnalité, vous pouvez également envoyer manuellement les utilisateurs vers <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> pour qu’ils inscrivent leurs coordonnées.  <br><br></li>
                  <li class="unordered">
Les utilisateurs peuvent accéder au portail d’inscription en cliquant sur le lien <strong>Inscription pour réinitialiser leur mot de passe</strong> sous l’onglet de profil du panneau d’accès.<br><br></li>
                  <li class="unordered">
L’inscription à l’aide de cette méthode peut être désactivée en cliquant sur le bouton Annuler ou en fermant la fenêtre, mais les utilisateurs recevront un rappel à chaque connexion s’ils ne se sont pas inscrits.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Ce paramètre n’affecte pas le comportement du portail d’inscription, mais détermine si ce dernier est présenté aux utilisateurs lorsqu’ils se connectent au panneau d’accès.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-days-before-users-must-confirm-their-contact-data">
                  <p>Nombre de jours avant que les utilisateurs ne doivent confirmer leurs coordonnées</p>
                </div>
              </td>
              <td>
                <p>Lorsque <strong>Demander aux utilisateurs de s’inscrire</strong> est activé, ce paramètre détermine le temps qui peut s’écouler avant que l’utilisateur ne doive reconfirmer ses coordonnées. </p>
                <p>(Visible uniquement si <strong>Demander aux utilisateurs de s’inscrire lorsqu’ils se connectent au panneau d’accès</strong> est défini sur <strong>Oui</strong>).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque : </strong>
                </p>
                <ul>
                  <li class="unordered">
Les valeurs acceptées vont de 0 à 730 jours, 0 voulant dire que l’utilisateur ne devra jamais reconfirmer ses coordonnées.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Ce paramètre n’affecte pas le comportement du portail d’inscription, mais détermine si le portail d’inscription est présenté aux utilisateurs lorsque leurs coordonnées doivent être reconfirmées. <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="customize-the-contact-your-administrator-link">
                  <p>Personnaliser le lien « Contactez votre administrateur »</p>
                </div>
              </td>
              <td>
                <p>Contrôle si le lien Contactez votre administrateur (illustré à gauche) apparaît sur le portail de réinitialisation du mot de passe lorsqu’une erreur se produit, lorsque l’utilisateur attend trop longtemps ou lorsqu’une opération pointe vers une URL ou une adresse électronique personnalisée.</p>
                <p>(Visible uniquement si <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est défini sur <strong>Oui</strong>).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque : </strong>
                </p>
                <ul>
                  <li class="unordered">
Si vous activez ce paramètre, vous devez choisir une adresse URL ou une adresse électronique personnalisée en remplissant le champ <strong>Adresse de messagerie ou URL personnalisée</strong> situé en dessous.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si la valeur est Non, si l’utilisateur clique sur le lien en surbrillance, un message est envoyé à l’adresse électronique principale de tous les administrateurs de ce client, demandant que son mot de passe soit réinitialisé.  Ce message est envoyé selon la logique suivante :<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
S’ils existent, le message est envoyé à tous les administrateurs de mot de passe, avec un maximum de 100 destinataires au total.<br><br></li>
                      <li class="unordered">
S’il n’y a pas d’administrateurs de mot de passe, le message est envoyé à tous les administrateurs d’utilisateurs, avec un maximum de 100 destinataires au total.<br><br></li>
                      <li class="unordered">
S’il n’y a pas d’administrateurs d’utilisateurs, le message est envoyé à tous les administrateurs généraux, avec un maximum de 100 destinataires au total.<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
Si la valeur est Oui, ce paramètre personnalise le comportement du lien mis en surbrillance pour qu’il pointe vers une URL ou une adresse électronique personnalisée via laquelle vos utilisateurs peuvent obtenir une aide sur la réinitialisation du mot de passe.<br><br></li>
                  <li class="unordered">
Si vous spécifiez une URL, elle s’ouvre dans un nouvel onglet.<br><br></li>
                  <li class="unordered">
Si vous spécifiez une adresse électronique, un lien mailto vers cette adresse est créé.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-email-address-or-URL">
                  <p>Adresse électronique ou URL personnalisée</p>
                </div>
              </td>
              <td>
                <p>Contrôle l’adresse électronique ou l’URL à laquelle renvoie le lien <strong>Contactez votre administrateur</strong>. </p>
                <p>(Visible uniquement si l’option <strong>Personnaliser le lien « Contactez votre administrateur »</strong> est défini sur <strong>Oui</strong>).</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Il doit s’agir d’une URL d’intranet ou d’extranet valide ou d’une adresse électronique valide.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Modifie l’emplacement sur lequel pointe le lien <strong>Contactez votre administrateur</strong>.<br><br></li>
                  <li class="unordered">
Si vous fournissez une adresse électronique, le lien devient un lien « mailto » vers cette adresse.<br><br></li>
                  <li class="unordered">
Si vous fournissez une URL, le lien devient un lien href standard pointant vers cette URL, qui s’ouvre dans un nouvel onglet.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="write-back-passwords-to-on-premises-directory">
                  <p>Écriture différée des mots de passe dans l’annuaire local</p>
                </div>
              </td>
              <td>
                <p>Contrôle si l’écriture différée de mot de passe est activée ou non pour cet annuaire. Si elle l’est, indique l’état du service d’écriture différée local.</p>
                <p>C’est utile si vous voulez désactiver temporairement le service sans reconfigurer Azure AD Connect.</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Ce contrôle apparaît uniquement si vous avez installé l’écriture différée du mot de passe en téléchargeant la dernière version d’Azure AD Connect et activé l’option <strong>Écriture différée de mot de passe</strong> dans l’écran de sélection des <strong>fonctionnalités facultatives</strong>.<br><br></li>
                  <li class="unordered">
Si vous avez activé l’écriture différée du mot de passe et que vous pensez que le service présente un problème de configuration, vous pouvez ouvrir cet onglet et consulter l’étiquette <strong>État du service d’écriture en différé des mots de passe</strong> pour voir si quelque chose ne fonctionne pas.<br><br></li>
                  <li class="unordered">
Les états possibles sont :<br><br><ul><li class="unordered"><strong>Configuré</strong> : tout fonctionne comme prévu<br><br></li><li class="unordered"><strong>Non configuré</strong> : l’écriture différée du mot de passe est installée, mais le service est injoignable. Vérifiez si vous ne bloquez pas les connexions sortantes sur le port 443 et essayez de réinstaller le service si les problèmes persistent.<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>Portail d’inscription :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si l’écriture différée du mot de passe est déployée et configurée et que ce commutateur est défini sur <strong>Non</strong>, l’écriture différée est désactivée et les utilisateurs fédérés et synchronisés par hachage du mot de passe ne pourront pas s’inscrire pour réinitialiser leur mot de passe.<br><br></li>
                  <li class="unordered">
Si le commutateur est défini sur <strong>Oui</strong>, l’écriture différée est activée et les utilisateurs fédérés et synchronisés par hachage du mot de passe pourront s’inscrire pour réinitialiser leur mot de passe.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si l’écriture différée du mot de passe est déployée et configurée et que ce commutateur est défini sur <strong>Non</strong>, l’écriture différée est désactivée et les utilisateurs fédérés et synchronisés par hachage du mot de passe ne pourront pas réinitialiser leur mot de passe.<br><br></li>
                  <li class="unordered">
Si le commutateur est défini sur <strong>Oui</strong>, l’écriture différée est activée et les utilisateurs fédérés et synchronisés par hachage du mot de passe pourront s’inscrire pour réinitialiser leur mot de passe.<br><br></li>
                </ul>
              </td
            </tr>
             <tr>
              <td>
                <div id="allow-users-to-unlock-accounts-without-resetting-their-password">
                  <p>Autoriser les utilisateurs à déverrouiller les comptes Active Directory locaux sans réinitialiser leur mot de passe</p>
                </div>
              </td>
              <td>
              <p>Indique si les utilisateurs qui visitent le portail de réinitialisation de mot de passe doivent ou non avoir la possibilité de déverrouiller leurs comptes Active Directory locaux sans réinitialiser leur mot de passe. Par défaut, Azure AD déverrouille toujours les comptes sans procéder à la réinitialisation de mot de passe. Ce paramétrage permet donc de différencier ces deux opérations.</p>
              <p>Si la valeur est « Oui », les utilisateurs auront la possibilité de réinitialiser leur mot de passe et de déverrouiller le compte ou de déverrouiller celui-ci sans réinitialiser le mot de passe. </p>
              <p>Si la valeur est « Non », les utilisateurs devront réinitialiser leur mot de passe lorsqu’ils déverrouilleront leur compte.</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Remarque :</strong>
                </p>
                <ul>
                  <li class="unordered">
Pour utiliser cette fonctionnalité, vous devez installer la version d’août 2015 ou une version postérieure d’Azure AD Connect (version 1.0.8667.0.x ou supérieure).<br><br><a href="http://www.microsoft.com/download/details.aspx?id=47594">Cliquez ici pour télécharger la dernière version d’Azure AD Connect.</a></li>
                  <li class="unordered">
                    <strong>Remarque :</strong> pour tester cette fonctionnalité, vous devez activer l’écriture différée du mot de passe et utiliser un compte provenant d’un utilisateur du site local (comme un utilisateur fédéré ou un utilisateur à mot de passe synchronisé) et possédant un compte verrouillé.  Les utilisateurs qui ne proviennent pas du site local et qui n’ont pas de compte verrouillé ne verront pas l’option de déverrouillage de leurs comptes.</li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Une fois cette option activée, lorsqu’un utilisateur avec un compte local verrouillé accède au portail de réinitialisation du mot de passe, il a la possibilité de déverrouiller son compte sans réinitialiser son mot de passe.<br><br>Notez que si vous utilisez l’écriture différée du mot de passe, les comptes sont déjà déverrouillés automatiquement lorsque le mot de passe est réinitialisé et que cette option dissocie simplement ces opérations.<br><br>Il s’agit d’une option particulièrement utile à activer si vous constatez que beaucoup de vos appels au support technique sont générés par des demandes de déverrouillage de compte.</li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## <a name="password-management-notifications"></a>Notifications de gestion des mots de passe
Le tableau suivant décrit comment chaque contrôle affecte l’expérience des utilisateurs et des administrateurs qui reçoivent les notifications de réinitialisation des mots de passe.  Vous pouvez configurer ces options dans la section **Notifications** de l’onglet **Configurer** du [portail de gestion Azure](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Contrôle de stratégie</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Description</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Éléments affectés</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-admins-when-other-admins-reset-their-own-passwords">
                  <p>Avertir les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe</p>
                </div>
              </td>
              <td>
                <p>Détermine si tous les généraux sont informés par courrier électronique à leur adresse électronique principale lorsqu’un autre administrateur de n’importe quel type réinitialise son mot de passe.</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si la valeur est définie sur Non, aucune notification n’est envoyée.<br><br></li>
                  <li class="unordered">
Si la valeur est définie sur Oui, tous les autres administrateurs généraux sont avertis si un administrateur réinitialise son mot de passe.<br><br></li>
                  <li class="unordered">
Cette notification est envoyée via un courrier électronique à l’adresse électronique principale de tous les autres administrateurs généraux de l’organisation.<br><br></li>
                </ul>
                <p>
                  <strong>Exemple :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si cette option est activée quand l’administrateur A réinitialise son mot de passe et qu’il existe trois autres administrateurs, B, C et D, les administrateurs B, C et D reçoivent un message les informant de la réinitialisation du mot de passe de l’administrateur A.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-users-and-admins-when-their-own-password-has-been-reset">
                  <p>Avertir les utilisateurs et les administrateurs quand leur mot de passe a été réinitialisé</p>
                </div>
              </td>
              <td>
                <p>Détermine si les utilisateurs et les administrateurs qui ont réinitialisé leur mot de passe doivent recevoir une notification les informant que leur mot de passe a été réinitialisé.</p>
                <br>
                <p><b><u>Requiert une des licences suivantes <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">pour en savoir plus</a></u></b></p>
                 <ul>
                   <li>O365 (toute référence payante) [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Standard [utilisateurs sur le cloud uniquement]</li>
                   <li>Azure AD Premium P1 ou P2 [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Mobility Suite [utilisateurs sur le cloud et locaux]</li>
                   <li>Enterprise Cloud Suite [utilisateurs sur le cloud et locaux]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>Portail de réinitialisation du mot de passe :</strong>
                </p>
                <ul>
                  <li class="unordered">
Si la valeur est définie sur Non, aucune notification n’est envoyée.<br><br></li>
                  <li class="unordered">
Si la valeur est définie sur Oui, chaque fois qu’un utilisateur ou qu’un administrateur réinitialise son propre mot de passe, il reçoit une notification indiquant que son mot de passe a été réinitialisé.<br><br></li>
                  <li class="unordered">
Cette notification est envoyée via un courrier électronique au nom d’utilisateur principal et à l’autre adresse électronique (ou adresse électronique d’authentification) de l’utilisateur qui réinitialise son mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/>
<br/>
<br/>

## <a name="next-steps"></a>Étapes suivantes
Voici les liens vers toutes les pages de la documentation sur la réinitialisation de mot de passe Azure AD :

* **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#reset-your-password).
* [**Fonctionnement**](active-directory-passwords-how-it-works.md) : découvrez les six différents composants du service et la fonction de chacun d’eux
* [**Prise en main**](active-directory-passwords-getting-started.md) : découvrez comment permettre à vos utilisateurs de réinitialiser et de modifier leurs mots de passe dans le cloud et localement
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) : découvrez comment déployer et gérer rapidement et efficacement les mots de passe de votre organisation
* [**Obtention d’informations**](active-directory-passwords-get-insights.md) : découvrez nos fonctionnalités intégrées de création de rapports.
* [**Forum Aux Questions**](active-directory-passwords-faq.md) : obtenez des réponses aux questions fréquemment posées.
* [**Dépannage**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre rapidement les problèmes liés au service.
* [**En savoir plus**](active-directory-passwords-learn-more.md) : découvrez les détails techniques sur le fonctionnement du service.

[001]: ./media/active-directory-passwords-customize/001.jpg "Image_001.jpg"

