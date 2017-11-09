---
title: 'Azure AD Connect : authentification directe - Forum aux questions | Documents Microsoft'
description: "Réponses au forum aux questions sur l’authentification directe d’Azure Active Directory."
services: active-directory
keywords: Authentification directe Azure AD Connect, installer Active Directory, composants requis pour Azure AD, SSO, Authentification unique
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: e1bd58797124210f7c31e90fb20d728289a04ba2
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Authentification directe Azure Active Directory : forum aux questions

Dans cet article, nous répondons au forum aux questions sur l’authentification directe d’Azure Active Directory (Azure AD). N’hésitez pas à le consulter régulièrement, du contenu nouveau y est fréquemment ajouté.

## <a name="which-of-the-azure-ad-sign-in-methods---pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs---should-i-choose"></a>Parmi les nouvelles méthodes de connexion Azure AD - authentification directe, synchronisation de hachage de mot de passe et services de fédération Active Directory (AD FS) - laquelle dois-je choisir ?

Cela dépend de votre environnement local et des exigences de votre organisation. Lisez cet article pour une [comparaison entre les différentes méthodes de connexion Azure AD](active-directory-aadconnect-user-signin.md).

## <a name="is-pass-through-authentication-a-free-feature"></a>L’authentification directe est-elle une fonctionnalité gratuite ?

L’authentification directe est gratuite et aucune édition payante d’Azure AD n’est nécessaire pour l’utiliser.

## <a name="is-pass-through-authentication-available-in-microsoft-cloud-germanyhttpwwwmicrosoftdecloud-deutschland-and-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>L’authentification directe est-elle disponible dans [Microsoft Cloud Allemagne](http://www.microsoft.de/cloud-deutschland) et [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/) ?

Non, l’authentification directe est uniquement disponible dans l’instance à l’échelle mondiale d’Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-accessmd-work-with-pass-through-authentication"></a>[L’accès conditionnel](../active-directory-conditional-access.md) fonctionne-t-il avec l’authentification directe ?

Oui, toutes les fonctionnalités, y compris l’authentification multifacteur Azure, fonctionnent avec l’authentification directe.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>L’authentification directe prend-elle en charge « l’ID alternatif » comme le nom d’utilisateur, plutôt que « userPrincipalName » ?

Oui. L’authentification directe prend en charge `Alternate ID` comme nom d’utilisateur lorsqu’elle est configurée dans Azure AD Connect comme indiqué [ici](active-directory-aadconnect-get-started-custom.md). Toutes les applications Office 365 ne prennent pas en charge `Alternate ID`. Reportez-vous à la documentation de l’application qui vous intéresse pour avoir des précisions sur sa prise en charge.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>La synchronisation du hachage de mot de passe agit-elle comme solution de secours pour l’authentification directe ?

Non, l’authentification directe _ne bascule pas_ automatiquement vers la synchronisation de hachage de mot de passe. Elle agit uniquement comme solution de secours pour les [scénarios que l’authentification directe ne prend pas en charge aujourd'hui](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Pour éviter les échecs de connexion de l’utilisateur, vous devez configurer l’authentification directe pour une [haute disponibilité](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Puis-je installer un connecteur de [proxy d’application Azure AD](../active-directory-application-proxy-get-started.md) sur le même serveur qu’un agent d’authentification directe ?

Oui, les versions de l’agent d’authentification directe (versions 1.5.193.0 ou versions ultérieures) prennent en charge cette configuration.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>De quelles versions d’Azure AD Connect et de quel agent d’authentification directe avez-vous besoin ?

Vous devez utiliser la version 1.1.486.0 ou une version ultérieure pour Azure AD Connect et 1.5.58.0 ou version ultérieure pour l’agent d’authentification directe pour que cette fonctionnalité puisse fonctionner. Tous les logiciels doivent être installés sur des serveurs avec Windows Server 2012 R2 ou version ultérieure.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-using-pass-through-authentication"></a>Que se passe-t-il si mon mot de passe utilisateur a expiré et qu’ils essaient de se connecter à l’aide de l’authentification directe ?

Dans le cas où vous avez configuré [l’écriture différée du mot de passe](../active-directory-passwords-update-your-own-password.md) pour un utilisateur spécifique et que cet utilisateur se connecte à l’aide de l’authentification directe, leurs mots de passe peuvent être modifiés ou réinitialisés. Les mots de passe seront réécrits dans l’annuaire Active Directory local comme prévu.

Toutefois, si l’écriture différée du mot de passe n’est pas configurée pour un utilisateur spécifique ou si l’utilisateur n’a aucune licence Azure AD valide attribuée, il ne peut pas mettre à jour son mot de passe dans le cloud. Il ne peut pas mettre à jour son mot de passe même si le mot de passe a expiré. À la place, l’utilisateur voit le message : « Votre organisation ne vous autorise pas à mettre à jour votre mot de passe sur ce site. Veuillez le mettre à jour en fonction de la méthode recommandée par votre organisation, ou contactez votre administrateur si vous avez besoin d’aide ». L’utilisateur ou l’administrateur doit réinitialiser son mot de passe dans Active Directory local.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Comment l’authentification directe vous protège-t-elle contre les attaques par recherche exhaustive de mot de passe ?

Pour plus d’informations, consultez [cet article](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Qu’est-ce que les agents de l’authentification directe communiquent via les ports 80 et 443 ?

- Les agents d’authentification établissent les requêtes HTTPS sur le port 443 pour toutes les opérations de fonctionnalité.
- Les agents d’authentification établissent des requêtes HTTP sur le port 80 pour télécharger des listes de révocations de certificats SSL.

     >[!NOTE]
     >Dans les mises à jour récentes, nous avons réduit le nombre de ports requis par la fonctionnalité. Si vous disposez de versions antérieures d’Azure AD Connect ou de l’agent d’authentification, laissez ces ports également ouverts : 5671, 8080, 9090, 9091, 9350, 9352 et 10100 à 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Les agents d’authentification directe peuvent-ils communiquer sur un serveur proxy web sortant ?

Oui. Si WPAD (Web Proxy Auto-Discovery) est activé dans votre environnement local, les agents d’authentification essayent automatiquement de localiser et d’utiliser un serveur proxy web sur le réseau.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Puis-je installer deux ou plusieurs agents d’authentification directe sur le même serveur ?

Non, vous ne pouvez installer qu’un seul agent d’authentification directe sur un serveur unique. Si vous souhaitez configurer l’authentification directe pour la haute disponibilité, suivez plutôt les instructions dans cet [article](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="i-already-use-active-directory-federation-services-ad-fs-for-azure-ad-sign-in-how-do-i-switch-it-to-pass-through-authentication"></a>J’utilise déjà Active Directory Federation Services (AD FS) pour la connexion d’Azure AD. Comment basculer vers l’authentification directe ?

Si vous avez configuré AD FS en tant que méthode de connexion à l’aide de l’Assistant Azure AD Connect, définissez la méthode de connexion utilisateur sur Authentification directe. Cette modification permet l’authentification directe sur le client et convertit _tous_ vos domaines fédérés en domaines gérés. Toutes les requêtes de connexion suivantes sur votre client sont gérées par l’authentification directe. Actuellement, il n’existe aucun moyen pris en charge dans Azure AD Connect pour utiliser une combinaison d’AD FS et d’authentification directe sur différents domaines.

Si les services AD FS ont été configurés en tant que méthode de connexion _hors_ de l’assistant Azure AD Connect, définissez la méthode de connexion utilisateur sur Authentification directe (à partir de l’option « Ne pas configurer »). Cette modification permet une authentification directe sur le client. Toutefois, tous vos domaines fédérés continuent à utiliser des services AD FS pour la connexion. Utilisez PowerShell pour convertir manuellement certains ou l’ensemble de ces domaines fédérés en domaines gérés. Après cela, toutes les requêtes de connexion sur des domaines gérés (_uniquement_) sont gérées par l’authentification directe.

>[!IMPORTANT]
>L’authentification directe ne gère pas la connexion pour les utilisateurs Active Directory dans le cloud uniquement.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-ad-environment"></a>Puis-je utiliser l’authentification directe dans un environnement à plusieurs forêts AD ?

Oui. Les environnements à plusieurs forêts sont pris en charge s’il existe des approbations de forêts entre les forêts AD et si le routage du suffixe de leurs noms est configuré correctement.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Combien d’agents d’authentification directe dois-je installer ?

L’installation de plusieurs agents d’authentification directe assure une [haute disponibilité](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Toutefois, cela ne fournit pas un équilibrage de charge déterministe entre les agents d’authentification.

Envisagez la charge moyenne et les pics de charge lors des demandes de connexion que vous attendez de la part de votre locataire. À titre de référence, un seul agent d’authentification peut gérer entre 300 000 et 400 000 authentifications par seconde sur un serveur doté d’un CPU à 4 cœurs et de 16 Go de RAM. Pour la plupart des clients, deux ou trois agents d’authentification au total suffisent à offrir la haute disponibilité et suffisamment de capacité.

Nous vous recommandons d’installer des agents d’authentification près de vos contrôleurs de domaine pour améliorer la latence de connexion.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Puis-je installer le premier agent d’authentification directe sur un serveur autre que celui qui exécute Azure AD Connect ?

Non, ce scénario n’est _pas_ pris en charge.

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>Combien d’agents d’authentification directe dois-je installer ?

Nous vous recommandons :

- d’installer deux ou trois agents d’authentification au total. Cette configuration est suffisante pour la plupart des clients.
- Installez des agents d’authentification sur vos contrôleurs de domaine (ou aussi près que possible) pour améliorer la latence de connexion.
- Assurez-vous que les serveurs (où sont installés les agents d’authentification) sont ajoutés à la même forêt AD que les utilisateurs dont les mots de passe doivent être validés.

>[!NOTE]
>Il existe une limite système de 12 agents d’authentification par client.

## <a name="how-can-i-disable-pass-through-authentication"></a>Comment désactiver l’authentification directe ?

Réexécutez l’assistant Azure AD Connect et modifiez la méthode de connexion utilisateur de l’authentification directe à une autre méthode. Cette modification désactive l’authentification directe sur le client et désinstalle l’agent d’authentification du serveur. Vous devez désinstaller manuellement les agents d’authentification à partir d’autres serveurs.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Que se passe-t-il lorsque je désinstalle un agent d’authentification directe ?

La désinstallation d’un agent d’authentification directe à partir d’un serveur provoque l’interruption de l’acceptation des requêtes de connexion. Assurez-vous d’avoir un autre agent d’authentification en cours d’exécution avant de procéder à cette opération, pour éviter de rompre la connexion utilisateur sur votre client.

## <a name="next-steps"></a>Étapes suivantes
- [**Limitations actuelles**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) : découvrez les scénarios pris en charge et ceux qui ne le sont pas.
- [**Démarrage rapide**](active-directory-aadconnect-pass-through-authentication-quick-start.md) : soyez opérationnel avec l’authentification directe Azure AD.
- [**Verrouillage intelligent**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) : configurez la fonctionnalité Verrouillage intelligent sur votre locataire pour protéger les comptes d’utilisateur.
- [**Immersion technique**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) : découvrez comment fonctionne cette fonctionnalité.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants susceptibles de se produire avec cette fonctionnalité.
- [**Immersion dans la sécurité**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) : informations techniques supplémentaires sur la fonctionnalité.
- [**Authentification unique transparente Azure AD**](active-directory-aadconnect-sso.md) : explorez en détail cette fonctionnalité complémentaire.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour formuler des demandes de nouvelles fonctionnalités.
