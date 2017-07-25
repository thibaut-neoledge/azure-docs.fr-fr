---
title: "Problèmes lors de l’installation de l’extension de navigateur du volet d’accès aux applications | Microsoft Docs"
description: "Comment corriger les erreurs courantes rencontrées lors de l’installation de l’extension de navigateur du volet d’accès"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 759631bca9b29134098dba55ef07513d0ee42549
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017

---

<a id="problem-installing-the-application-access-panel-browser-extension" class="xliff"></a>

# Problèmes lors de l’installation de l’extension de navigateur du volet d’accès aux applications

Le volet d’accès est un portail web qui permet à un utilisateur disposant d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD) d’afficher et de lancer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. Un utilisateur disposant d’éditions Azure AD peut également utiliser des fonctionnalités de gestion de groupes et d’applications en libre-service par le biais du volet d’accès. Le volet d’accès est distinct du portail Azure et n’exige pas des utilisateurs qu’ils aient un abonnement Azure.

Pour utiliser l’authentification unique (SSO) par mot de passe dans le volet d’accès, l’extension du volet d’accès doit être installée dans le navigateur de l’utilisateur. Cette extension est téléchargée automatiquement lorsqu’un utilisateur sélectionne une application configurée pour l’authentification unique (SSO) avec mot de passe.

<a id="meeting-browser-requirements-for-the-access-panel" class="xliff"></a>

## Configuration requise du navigateur pour le volet d’accès

Le volet d’accès nécessite un navigateur qui prend en charge JavaScript et dans lequel le CSS est activé. Pour utiliser l’authentification unique (SSO) par mot de passe dans le volet d’accès, l’extension du volet d’accès doit être installée dans le navigateur de l’utilisateur. Cette extension est téléchargée automatiquement lorsqu’un utilisateur sélectionne une application configurée pour l’authentification unique (SSO) avec mot de passe.

Pour l’authentification unique par mot de passe, les navigateurs de l’utilisateur final peuvent être :

-   Internet Explorer 8, 9, 10, 11 -- sur Windows 7 ou version ultérieure

-   Chrome -- sur Windows 7 ou ultérieur, et sur Mac OS X ou ultérieur

-   Firefox 26.0 ou ultérieur -- sur Windows XP SP2 ou ultérieur, et sur Mac OS X 10.6 ou ultérieur

**Remarque** : l’extension de l’authentification unique par mot de passe sera disponible pour Edge dans Windows 10 quand les extensions de navigateur seront prises en charge pour Edge.

<a id="how-to-install-the-access-panel-browser-extension" class="xliff"></a>

## Comment installer l’extension de navigateur du volet d’accès

Pour installer l’extension de navigateur du volet d’accès, effectuez les étapes suivantes :

1.  Ouvrez le [volet d’accès](https://myapps.microsoft.com) dans l’un des navigateurs pris en charge et connectez-vous en tant **qu’utilisateur** dans Azure AD.

2.  Cliquez sur une **application avec authentification unique par mot de passe** dans le volet d’accès.

3.  Dans l’invite vous demandant d’installer le logiciel, sélectionnez **Installer maintenant**.

4.  Vous êtes redirigé vers le lien de téléchargement selon votre navigateur. **Ajoutez** l’extension à votre navigateur.

5.  Si votre navigateur vous le demande, sélectionnez l’option **Activer** ou **Autoriser** pour l’extension.

6.  Une fois l’extension installée, **redémarrez** votre session de navigateur.

7.  Connectez-vous au volet d’accès et essayez de **lancer** vos applications à authentification unique par mot de passe.

Vous pouvez également télécharger l’extension pour Chrome et Firefox à partir des liens directs ci-dessous :

-   [Extension du volet d’accès pour Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extension du volet d’accès pour Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

<a id="setting-up-a-group-policy-for-internet-explorer" class="xliff"></a>

## Configuration d’une stratégie de groupe pour Internet Explorer

Vous pouvez configurer une stratégie de groupe pour installer à distance l’extension du volet d’accès pour Internet Explorer sur les ordinateurs de vos utilisateurs.

Vous devez respecter certaines conditions préalables, notamment les suivantes :

-   Vous avez configuré les [services de domaine Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)et vous avez joint les ordinateurs de vos utilisateurs à votre domaine.

-   Vous devez disposer de l’autorisation « Modifier les paramètres » pour modifier l’objet de stratégie de groupe (GPO). Par défaut, les membres des groupes de sécurité suivants jouissent de cette autorisation : administrateurs de domaine, administrateurs d’entreprise et propriétaires créateurs de la stratégie de groupe. [En savoir plus](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Suivez le didacticiel [Déploiement de l’extension du volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe](active-directory-saas-ie-group-policy.md) pour obtenir des instructions pas à pas sur la configuration et le déploiement d’une stratégie de groupe.

<a id="troubleshoot-the-access-panel-in-internet-explorer" class="xliff"></a>

## Résolution des problèmes liés au volet d’accès dans Internet Explorer

Consultez le guide [Troubleshoot the Access Panel Extension for Internet Explorer](active-directory-saas-ie-troubleshooting.md) (Résolution des problèmes liés à l’extension du volet d’accès pour Internet Explorer) pour accéder à un outil de diagnostic et pour obtenir des instructions pas à pas sur la configuration de l’extension pour Internet Explorer.

<a id="if-these-troubleshooting-steps-do-not-resolve-the-issue" class="xliff"></a>

## Si ces étapes de dépannage ne résolvent pas le problème

Ouvrez un ticket de support en fournissant les informations suivantes, dans la mesure du possible :

-   ID d’erreur de corrélation

-   UPN (adresse e-mail de l’utilisateur)

-   ID de locataire

-   Type de navigateur

-   Fuseau horaire et heure/période à laquelle l’erreur se produit

-   Traces Fiddler

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

