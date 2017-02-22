---
title: "Vérification en deux étapes dans Azure MFA | Microsoft Docs"
description: "En quoi consiste Azure Multi-Factor Authentication (MFA), pourquoi l’utiliser, informations sur le client MFA, différentes méthodes et versions disponibles. "
keywords: "introduction à l&quot;authentification multifacteur, vue d&quot;ensemble de l&quot;authentification multifacteur, définition de l&quot;authentification multifacteur"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: c40d7a34-1274-4496-96b0-784850c06e9b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 78daa5a75b3414e2761333ea6ad91945596553c8
ms.openlocfilehash: 757a26ed6c39e6eb498a4f717a2eb5da3cb5c0a6


---
# <a name="what-is-azure-multi-factor-authentication"></a>Présentation d'Azure Multi-Factor Authentication
La vérification en deux étapes est une méthode d’authentification qui nécessite plusieurs méthodes de vérification et ajoute une deuxième couche critique de sécurité aux connexions et transactions des utilisateurs. Cette authentification fonctionne en nécessitant au minimum deux des méthodes de vérification suivantes :

* Un élément que vous connaissez (généralement un mot de passe)
* Un élément que vous possédez (un appareil de confiance qui n'est pas facilement dupliqué, comme un téléphone)
* Un élément vous concernant particulièrement (biométrie)

<center>![Nom d’utilisateur et mot de passe](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Certificats](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Smartphone](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Carte à puce](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Carte à puce virtuelle](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Nom d’utilisateur et mot de passe](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) est la solution de vérification en deux étapes de Microsoft. Azure MFA contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Il fournit une authentification forte au moyen de diverses méthodes de vérification, notamment les appels téléphoniques, l’envoi de SMS ou la vérification sur l’application mobile.

> [VIDÉO https://channel9.msdn.com/Blogs/Azure/WA-MFA-Overview/player]
>
>

## <a name="why-use-azure-multi-factor-authentication"></a>Pourquoi utiliser Azure Multi-Factor Authentication ?
Aujourd’hui, plus que jamais, les personnes sont de plus en plus connectées. Avec les smartphones, les tablettes, les PC portables et les PC de bureau, les utilisateurs ont plusieurs options différentes pour se connecter et rester connecté à tout moment. Ils peuvent accéder à leurs comptes et leurs applications où qu’ils soient, cela signifie qu’ils peuvent être plus productifs et mieux servir leurs clients.

Azure Multi-Factor Authentication est une solution facile à utiliser, évolutive et fiable qui fournit une deuxième méthode d'authentification pour que vos utilisateurs soient toujours protégés.

| ![Facile à utiliser](./media/multi-factor-authentication/simple.png) | ![Évolutif](./media/multi-factor-authentication/scalable.png) | ![Toujours protégé](./media/multi-factor-authentication/protected.png) | ![Fiable](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Facile à utiliser** |**Évolutif** |**Toujours protégé** |**Fiable** |

* **Facile à utiliser** : Azure Multi-Factor Authentication est simple à configurer et utiliser. La protection supplémentaire fournie par Azure Multi-Factor Authentication permet aux utilisateurs de gérer leurs propres appareils. De plus, dans de nombreux cas elle peut être configurée de manière très simple.
* **Évolutif** : Azure Multi-Factor Authentication utilise la puissance du cloud et s’intègre à votre site Active Directory local et à vos applications personnalisées. Cette protection est même étendue à vos scénarios à mission critique volumineux.
* **Toujours protégé** : Azure Multi-Factor Authentication fournit une authentification forte qui utilise les normes les plus strictes du secteur.
* **Fiable** : nous garantissons une disponibilité à 99,9 % d'Azure Multi-Factor Authentication. Le service est considéré comme non disponible quand il ne parvient pas à recevoir ou traiter des demandes de vérification pour la vérification en deux étapes.

> [VIDÉO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]
>
>

## <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication : fonctionnement
La sécurité de la vérification en deux étapes repose sur son approche en couche. Compromettre plusieurs méthodes de vérification présente un défi de taille pour les attaquants. Même si un attaquant réussit à connaître votre mot de passe, ce dernier est inutile sans posséder l’appareil de confiance. Si vous perdez votre appareil, la personne qui le trouve ne peut pas s’en servir si elle ne connaît pas votre mot de passe.

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]
>
>

## <a name="methods-available-for-multi-factor-authentication"></a>Méthodes disponibles pour Multi-Factor Authentication
Quand un utilisateur se connecte, une demande de vérification supplémentaire lui est envoyée. Voici la liste des méthodes qui peuvent être utilisées pour cette seconde vérification.

| Méthode de vérification | Description |
| --- | --- |
| appel téléphonique |Un appel est passé sur le téléphone de l’utilisateur demandant de vérifier qu’il se connecte. Appuyez sur la touche # pour finaliser le processus de vérification. Cette option est configurable et peut être remplacée par un code que vous spécifiez. |
| SMS |Un SMS est envoyé sur le smartphone de l’utilisateur avec un code à 6 chiffres. Entrez ce code pour finaliser le processus de vérification. |
| Notification sur l’application mobile |Une demande de vérification sera envoyée sur le smartphone de l’utilisateur lui indiquant de terminer la vérification en sélectionnant **Vérifier** depuis l’application mobile. Cela se produit si une notification de l’application est la méthode de vérification principale. Si l’utilisateur reçoit cette notification lorsqu’il ne se connecte pas, il peut la signaler comme fraude. |
| Code de vérification avec application mobile |L’application mobile sur l’appareil d’un utilisateur génère un code de vérification. Cela se produit si vous avez sélectionné un code de vérification comme méthode de vérification principale. |

Pour les méthodes de vérification sur l’application mobile, Azure Multi-Factor Authentication fonctionne avec des applications d’authentification tierces pour smartphones. Toutefois, nous vous recommandons d’utiliser l’application Microsoft Authenticator, disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versions disponibles d’Azure Multi-Factor Authentication
Azure Multi-Factor Authentication est disponible en trois versions.

| Version | Description |
| --- | --- |
| Authentification multifacteur pour Office 365 |Cette version fonctionne exclusivement avec les applications Office 365 et est gérée à partir du portail Office 365. De ce fait, les administrateurs peuvent désormais sécuriser leurs ressources Office 365 avec la vérification en deux étapes. Cette version est fournie dans le cadre d’un abonnement à Office 365. |
| Authentification multifacteur pour administrateurs Azure |Le même sous-ensemble de fonctionnalités de vérification en deux étapes pour Office 365 est disponible gratuitement pour l’ensemble des administrateurs Azure. Tout compte administratif dans le cadre d’un abonnement Azure peut activer cette fonctionnalité pour bénéficier d’une protection supplémentaire. Un administrateur qui souhaite accéder au portail Azure afin de créer une machine virtuelle, un site web, gérer le stockage, ou utiliser tout autre service Azure peut ajouter une authentification multifacteur à son compte d’administrateur. |
| Azure Multi-Factor Authentication | Également désignée comme version « complète », Azure Multi-Factor Authentication offre un riche éventail de fonctionnalités. Il fournit des options de configuration supplémentaires via le [Portail Azure Classic](https://manage.windowsazure.com), des fonctions de rapports avancées et la prise en charge d’une sélection d’applications locales et dans le cloud. Azure Multi-Factor Authentication, fourni avec Azure Active Directory Premium et Enterprise Mobility Suite, peut être déployé localement ou dans le cloud. Reportez-vous à la rubrique ci-dessous pour découvrir [d’autres façons d’obtenir Azure Multi-Factor Authentication](multi-factor-authentication.md#how-to-get-azure-multi-factor-authentication). |

## <a name="feature-comparison-of-versions"></a>Comparaison des fonctionnalités suivant les versions
Le tableau suivant fournit la liste des fonctionnalités qui sont disponibles dans les différentes versions d’Azure Multi-Factor Authentication.

> [!NOTE]
> Ce tableau de comparaison répertorie les fonctionnalités de chaque version de Multi-Factor Authentication. Si vous disposez du service Azure Multi-Factor Authentication complet, certaines fonctionnalités peuvent être disponibles ou non, selon que vous utilisez [MFA dans le cloud ou localement](multi-factor-authentication-get-started.md).


| Fonctionnalité | Authentification multifacteur pour Office 365 | Authentification multifacteur pour administrateurs Azure | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Protection des comptes Administrateur avec MFA |● |● (Disponible uniquement pour les comptes d’administrateur Azure) |● |
| Application mobile comme second facteur |● |● |● |
| Appel téléphonique comme second facteur |● |● |● |
| SMS comme second facteur |● |● |● |
| Mots de passe d'application pour les clients qui ne prennent pas en charge MFA |● |● |● |
| Contrôle d’administration sur les méthodes de vérification |● |● |● |
| Mode du code PIN | | |● |
| Alerte de fraude | | |● |
| Rapports MFA | | |● |
| Contournement à usage unique | | |● |
| Messages de bienvenue personnalisés pour les appels téléphoniques | | |● |
| ID d’appelant personnalisé pour les appels téléphoniques | | |● |
| Confirmation d’événement | | |● |
| Adresses IP approuvées | | |● |
| Mémoriser MFA pour les appareils fiables |● |● |● |
| SDK MFA | | |● requiert un fournisseur Multi-Factor Auth et un abonnement Azure |
| MFA pour les applications locales | | |● |

## <a name="how-to-get-azure-multi-factor-authentication"></a>Comment obtenir Azure Multi-Factor Authentication ?
Si vous souhaitez bénéficier de toutes les fonctionnalités offertes par Azure Multi-Factor Authentication, plusieurs options s’offrent à vous :

1. Achetez des licences Azure Multi-Factor Authentication et attribuez-les à vos utilisateurs.
2. Achetez des licences regroupant Azure Multi-Factor Authentication (comme Azure Active Directory Premium, Enterprise Mobility Suite ou Enterprise Cloud Suite) et attribuez-les à vos utilisateurs.
3. Créez un fournisseur Azure Multi-Factor Authentication dans un abonnement Azure. Quand vous utilisez un fournisseur Azure Multi-Factor Authentication, vous avez le choix entre deux modèles d’utilisation qui sont facturés dans le cadre de votre abonnement Azure :  
   * **Par utilisateur**. Pour les entreprises qui souhaitent activer la vérification en deux étapes pour un nombre fixe d’employés qui s’authentifient régulièrement.  
   * **Par authentification**. Pour les entreprises qui souhaitent activer la vérification en deux étapes pour un nombre important d’utilisateurs externes qui s’authentifient ponctuellement.  

Azure Multi-Factor Authentication fournit des méthodes de vérification sélectionnables pour cloud et pour serveur. Cela signifie que vous pouvez choisir les méthodes mises à la disposition de vos utilisateurs : appel téléphonique, texte, notification sur l’application ou codes d’application. Pour plus d’informations, consultez [Méthodes de vérification sélectionnables](multi-factor-authentication-whats-next.md#selectable-verification-methods).

Pour plus d’informations sur la tarification, consultez la [tarification d’Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="next-steps"></a>Étapes suivantes
Pour commencer avec l’authentification multifacteur Azure, votre première étape consiste à [choisir entre l’authentification multifacteur dans le cloud ou en local](multi-factor-authentication-get-started.md)



<!--HONumber=Jan17_HO5-->


