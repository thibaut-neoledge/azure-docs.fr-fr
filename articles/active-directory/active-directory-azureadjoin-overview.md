---
title: "Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join | Microsoft Docs"
description: "Fournit une présentation détaillée de la manière dont les appareils Windows 10 peuvent utiliser Azure AD Join pour s’inscrire auprès d’Azure Active Directory."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 0cd4942f-7d54-474e-bd12-8e6764b0d42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 31574a82d190b9b157f8df3308fac298924eada5


---
# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join
## <a name="what-is-azure-active-directory-join"></a>Qu’est-ce qu’Azure Active Directory Join ?
Azure Active Directory (Azure AD) Join est la fonctionnalité permettant d’inscrire un appareil appartenant à l’entreprise dans Azure Active Directory afin de le gérer de façon centralisée. Cette fonctionnalité permet aux utilisateurs (par exemple, des employés ou étudiants) de se connecter au cloud de l’entreprise via Azure Active Directory. Elle contribue à simplifier les déploiements Windows ainsi que l’accès aux applications et ressources de l’entreprise à partir de n’importe quel appareil Windows appartenant à l’entreprise ou à l’utilisateur (BYOD).

Azure AD Join s’adresse aux entreprises qui adoptent une stratégie « cloud-first/cloud-only », c’est-à-dire généralement des petites et moyennes entreprises qui ne possèdent pas d’infrastructure Windows Server Active Directory en local. Pour autant, Azure AD Join peut parfaitement être utilisé par de grandes organisations, sur des appareils incapables d’exécuter une jonction de domaine traditionnelle (appareils mobiles, par exemple), ou encore pour les utilisateurs qui ont essentiellement besoin d’accéder à Office 365 ou à d’autres applications SaaS Azure AD.

Bien que la jonction de domaine traditionnelle continue d’offrir la meilleure expérience locale sur les appareils prenant en charge la jonction de domaine, Azure AD Join est également adapté aux appareils dépourvus de cette fonction. Azure AD Join peut également être utile pour gérer des utilisateurs dans le cloud. Pour ce faire, il utilise des fonctionnalités de gestion de périphériques mobiles, sans recourir aux outils de gestion de domaine classiques tels que les stratégies de groupe ou System Center Configuration Manager (SCCM).

![Vue d’ensemble des appareils professionnels et personnels utilisant Active Directory en local et Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)

## <a name="why-should-enterprises-adopt-azure-ad-join"></a>Pourquoi les entreprises doivent-elles adopter Azure AD Join ?
* **Entreprises ayant massivement migré dans le cloud**: si vous avez migré ou êtes en passe de migrer vers un modèle permettant de réduire votre empreinte locale et que vous souhaitez exploiter davantage le cloud, Azure AD Join peut constituer un avantage certain. Vous avez peut-être créé des comptes Azure AD manuellement ou via la synchronisation de votre annuaire Active Directory local. Dans tous les cas, vous disposez d’un compte dans Azure AD que vous pouvez utiliser pour vous connecter à Windows 10. Vos utilisateurs peuvent joindre leurs ordinateurs à Azure AD via le processus OOBE (Out-Of-the-Box Experience) ou via le menu Paramètres. Une fois la jonction effectuée, ils bénéficient d’un accès par authentification unique à des ressources cloud comme Office 365 soit dans le navigateur, soit dans les applications Office.
* **Établissements d’enseignement**: l’un des principaux scénarios connus est celui des établissements d’enseignement qui gèrent deux types d’utilisateurs : les enseignants et les étudiants. Les enseignants étant des membres à plus long terme de l’organisation, il est judicieux de leur créer des comptes en local. Les étudiants, quant à eux, sont rattachés à l’organisation sur une plus courte durée ; il est donc possible de les gérer dans Azure AD. Cela signifie que la mise à l’échelle de l’annuaire peut être transmise au cloud au lieu d’être stockée en local. Les étudiants pourront également se connecter à Windows avec leur compte Azure AD et accéder aux ressources Office 365 depuis leur navigateur ou leurs applications Office.
* **Entreprises de vente au détail** : nos clients nous ont également fait part de leur volonté de gérer plus facilement les travailleurs saisonniers.  Comme indiqué plus haut, les comptes d’employés à plein temps qui restent plus longtemps au sein de l’entreprise sont généralement créés en local sur des ordinateurs joints au domaine. À l’inverse, les saisonniers étant présents pour une période plus courte, il est préférable de les gérer là où il est plus facile de déplacer les licences utilisateur. La création de ces comptes utilisateurs dans le cloud avec des licences Office 365 leur permet de bénéficier des avantages de la connexion à Windows et aux applications Office avec un compte Azure AD, tout en conservant la mobilité de leurs licences une fois qu’ils ont quitté l’établissement.
* **Autres entreprises** : même si vous gérez vos utilisateurs dans votre instance Active Directory locale, il peut être avantageux de les joindre à Azure AD. Pourquoi ? Tout simplement parce qu’Azure AD simplifie l’expérience de jonction, assure une gestion efficace des appareils, inscrit automatiquement les appareils dans le système de gestion des périphériques mobiles et offre une fonctionnalité d’authentification unique pour l’accès à Azure AD et aux ressources locales.  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>Quelles sont les fonctionnalités offertes par Azure AD Join ?
Azure AD Join a les caractéristiques suivantes :

* **Approvisionnement autonome des appareils appartenant à l’entreprise**: avec Windows 10, les utilisateurs peuvent configurer un nouvel appareil via la fonctionnalité OOBE (introduction de l’interface logicielle lors de la première utilisation ) sans faire appel au service informatique.
* **Prise en charge des formats modernes**: Azure AD Join est compatible avec les appareils qui n’intègrent pas les fonctionnalités classiques de jonction de domaine.  
* **Prise en charge des comptes professionnels existants**: les utilisateurs n’ont plus besoin de créer et gérer un compte personnel Microsoft pour bénéficier d’une expérience optimale sur les appareils fournis par l’entreprise, comme c’était le cas sous Windows 8. Ils peuvent utiliser leurs comptes professionnels existants dans Azure AD. Pour de nombreuses organisations, les utilisateurs peuvent donc configurer leur compte et se connecter à Windows à l’aide des informations d’identification qu’ils utilisent pour accéder à Office 365.
* **Inscription automatique au système de gestion des appareils mobiles** : les appareils peuvent être inscrits automatiquement dans le système de gestion lors de leur connexion à Azure AD. Ce processus fonctionne aussi bien avec Microsoft Intune qu’avec les solutions de gestion des périphériques mobiles de fournisseurs partenaires. Dans le cas d’une gestion avec Intune, les administrateurs informatiques peuvent contrôler et gérer à la fois les appareils joints à Azure AD et ceux joints à un autre domaine dans la console de gestion SCCM.
* **Authentification unique aux ressources de l’entreprise**: les utilisateurs apprécient de pouvoir se connecter via l’authentification unique depuis leur bureau Windows aux applications et ressources dans le cloud, comme Office 365 et des milliers d’applications métier qui s’appuient sur Azure AD pour une authentification via [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Les appareils appartenant à l’entreprise qui sont joints à Azure AD bénéficient également de l’authentification unique aux ressources locales lorsque l’appareil est connecté au réseau de l’entreprise ; l’accès se fait également en tout lieu lorsque ces ressources sont exposées via le [proxy d’application Azure AD](https://msdn.microsoft.com/library/azure/Dn768219.aspx).
* **Itinérance de l’état du système d’exploitation**: les paramètres d’accessibilité, les sites web, les mots de passe Wi-Fi et d’autres paramètres sont synchronisés entre les appareils appartenant à l’entreprise sans nécessiter de compte Microsoft personnel.
* **Windows Store pour les entreprises**: Windows Store prend en charge l’acquisition d’applications et la gestion des licences associées avec des comptes Azure AD. Les organisations sont en mesure d’obtenir des licences en volume pour leurs applications et de les mettre à la disposition de leurs utilisateurs.

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>Comment fonctionnent les différents appareils avec Azure AD Join ?
| Appareil d’entreprise (joint au domaine local) | Appareil d’entreprise (joint au cloud) | Appareil personnel |
| --- | --- | --- |
| Les utilisateurs peuvent se connecter à Windows à l’aide des informations d’identification de leur compte professionnel (comme ils le font déjà). |Les utilisateurs peuvent se connecter à Windows avec les informations d'identification de leur compte professionnel gérées dans Azure AD. Cette approche est pertinente pour les appareils d’entreprise dans les trois situations suivantes : 1) l’organisation ne dispose pas d’Active Directory en local (cas, par exemple, d’une petite entreprise) ; 2) l’organisation ne crée pas la totalité de ses comptes utilisateur dans Active Directory (par exemple, les comptes d’étudiants, de consultants ou d’employés saisonniers ne sont pas créés dans Active Directory) ; 3) l’organisation dispose d’appareils d’entreprise qui ne peuvent pas être joints à un domaine (local), comme des téléphones ou tablettes exécutant une version mobile (par exemple, un appareil secondaire emporté à l’atelier ou dans la surface de vente). Azure AD Join prend en charge la jonction d’appareils d’entreprise pour les organisations gérées et fédérées. |Les utilisateurs se connectent à Windows à l’aide des informations d’identification de leur compte Microsoft personnel (comme ils le font déjà). |
| Les utilisateurs ont accès aux paramètres d’itinérance et au Windows Store d’entreprise. Ces services fonctionnent avec les comptes professionnels et ne nécessitent pas l’utilisation d’un compte Microsoft personnel. Les organisations doivent connecter leur instance Active Directory locale à Azure AD. |Les utilisateurs peuvent procéder eux-mêmes à la configuration. Ils peuvent lancer leur première exécution via leur compte professionnel plutôt que de faire appel au service informatique pour l’approvisionnement de leurs appareils (mais les deux méthodes sont prises en charge). |Les utilisateurs peuvent ajouter facilement un compte professionnel géré dans Active Directory ou Azure AD. |
| Les utilisateurs ont la possibilité d’utiliser la fonctionnalité d’authentification unique pour accéder, à partir du bureau, à leurs applications, sites Web et ressources de travail, notamment les ressources locales et les applications cloud qui utilisent Azure AD pour l’authentification. |Les appareils sont automatiquement inscrits dans l’annuaire d’entreprise (Azure AD) ainsi que dans le système de gestion des périphériques mobiles (fonctionnalité Azure AD Premium). |Les utilisateurs peuvent utiliser ce compte professionnel pour accéder, via une authentification unique à l’ensemble des applications et sites web ou ressources. |
| Les utilisateurs peuvent ajouter leurs comptes Microsoft personnels pour accéder à leurs fichiers et images personnels sans affecter les données de l’entreprise (les paramètres d’itinérance restent rattachés à leurs comptes professionnels). Le compte Microsoft prend en charge l’authentification unique et n’active plus l’itinérance des paramètres. |Les utilisateurs peuvent réinitialiser eux-mêmes leur mot de passe sur winlogon en cas d’oubli du mot de passe (fonctionnalité Azure AD Premium). |Les utilisateurs ont accès au Windows Store d’entreprise et peuvent ainsi charger et utiliser les applications métiers sur leurs appareils personnels. |

## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe avec Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)




<!--HONumber=Feb17_HO2-->


