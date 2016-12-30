---
title: "Accès conditionnel Azure Active Directory | Microsoft Docs"
description: "Utilisez le contrôle d’accès conditionnel dans Azure Active Directory pour vérifier des conditions spécifiques lors de l’authentification pour l’accès aux applications."
services: active-directory
keywords: "accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: da3f0a44-1399-4e0b-aefb-03a826ae4ead
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/16/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: af104d47d316b3e46108e7e2ed0771bd06f360af
ms.openlocfilehash: 6c3b547b052bca6999e47ddc10fcce5e1e56fe09


---
# <a name="conditional-access-in-azure-active-directory"></a>Accès conditionnel dans Azure Active Directory

> [!div class="op_single_selector"]
> * [Portail Azure Classic](active-directory-conditional-access.md)
> * [Portail Azure](active-directory-conditional-access-azure-portal.md)

Les fonctionnalités de contrôle d’accès conditionnel d’Azure Active Directory (Azure AD) offrent des méthodes simples de sécurisation des ressources dans le cloud et en local. Les stratégies d’accès conditionnel telles que l’authentification multifacteur peuvent vous protéger contre le risque de vol et de piratage des informations d’identification. D’autres stratégies d’accès conditionnel peuvent vous aider à protéger les données de votre organisation. Par exemple, en plus des informations d’identification, vous pouvez avoir une stratégie permettant que seuls les appareils inscrits dans un système de gestion des appareils mobiles tels que Microsoft Intune puissent accéder aux services sensibles de votre organisation.

## <a name="prerequisites"></a>Composants requis
L’accès conditionnel Azure AD est une fonctionnalité [Azure Active Directory Premium](http://www.microsoft.com/identity). Tous les utilisateurs accédant à une application limitée par des stratégies d’accès conditionnel doivent disposer d’une licence Azure AD Premium. Pour en savoir plus sur les conditions requises de licence, consultez l’article [Rapport d’utilisation sans licence](https://aka.ms/utc5ix).

## <a name="how-is-conditional-access-control-enforced"></a>Comment est appliqué le contrôle d’accès conditionnel ?
Une fois le contrôle d’accès conditionnel en place, Azure AD vérifie les conditions spécifiques que vous avez définies pour qu’un utilisateur puisse accéder à une application. Une fois que ces conditions d’accès sont remplies, l’utilisateur est authentifié et peut accéder à l’application.  

![Présentation de l’accès conditionnel](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Conditions
Voici les conditions que vous pouvez inclure dans une stratégie d’accès conditionnel :

* **Appartenance au groupe**. Contrôlez l’accès d’un utilisateur selon l’appartenance à un groupe.
* **Emplacement**. Utilisez l’emplacement de l’utilisateur pour déclencher l’authentification multifacteur et bloquer les commandes lorsqu’un utilisateur n’est pas sur un réseau approuvé.
* **Plate-forme d’appareil**. Utilisez la plateforme d’un appareil (par exemple iOS, Android, Windows Mobile ou Windows) comme condition d’application d’une stratégie.
* **Appareil activé**. Le statut de l’appareil, activé ou désactivé, est validé au cours de l’évaluation de la stratégie d’appareil. Si vous désactivez un appareil perdu ou volé dans le répertoire, celui-ci n’est plus utilisable conformément à la stratégie.
* **Risque utilisateur et à la connexion**. Vous pouvez utiliser [Azure AD Identity Protection](active-directory-identityprotection.md) pour les stratégies de risque d’accès conditionnel. Les stratégies de risque d’accès conditionnel permettent d’offrir à votre organisation une protection avancée, en fonction des évènements à risque et des activités de connexion inhabituelles.

## <a name="controls"></a>Commandes
Voici les contrôles que vous pouvez utiliser dans une stratégie d’accès conditionnel :

* **Authentification multifacteur**. Grâce à l’authentification multifacteur, vous pouvez appliquer une authentification renforcée. Vous pouvez utiliser l’authentification multifacteur avec Azure Multi-Factor Authentication ou via un fournisseur d’authentification multifacteur local, combiné aux services de fédération Active Directory (AD FS). L’authentification multifacteur contribue à empêcher tout accès à vos ressources par un utilisateur non autorisé qui peut avoir obtenu l’accès aux informations d’identification d’un utilisateur valide.
* **Bloquer**. Vous pouvez appliquer des conditions comme l’emplacement de l’utilisateur pour bloquer l’accès de l’utilisateur. Vous pouvez, par exemple, bloquer l’accès à un utilisateur qui ne se trouve pas sur un réseau approuvé.
* **Appareils conformes**. Vous pouvez définir des stratégies d’accès conditionnel au niveau de l’appareil. Vous pouvez définir une stratégie de façon que seuls les ordinateurs joints à un domaine ou seuls les appareils mobiles inscrits dans une application de gestion des appareils mobiles puissent accéder aux ressources de votre organisation. Par exemple, vous pouvez utiliser Intune pour vérifier la conformité de l’appareil, puis la signaler à Azure AD pour application lorsque l’utilisateur tente d’accéder à une application. Pour obtenir des informations détaillées sur l’utilisation d’Intune afin de protéger les applications et les données, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). Vous pouvez également utiliser Intune pour appliquer la protection des données pour les appareils perdus ou volés. Pour plus d’informations, consultez [Protection de vos données avec effacement complet ou sélectif à l’aide de Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## <a name="applications"></a>Applications
Vous pouvez appliquer une stratégie d’accès conditionnel au niveau de l’application. Définissez des niveaux d’accès pour les applications et les services dans le cloud ou en local. La stratégie est appliquée directement au site web ou au service. La stratégie est appliquée pour l’accès au navigateur et aux applications qui accèdent au service.

## <a name="device-based-conditional-access"></a>Accès conditionnel basé sur les appareils
Vous pouvez restreindre l’accès aux applications à partir des appareils qui sont inscrits auprès d’Azure AD et qui répondent à des conditions spécifiques. L’accès conditionnel en fonction de l’appareil protège les ressources d’une organisation contre tout accès par des utilisateurs à partir des appareils suivants :

* Appareils inconnus ou non gérés.
* Appareils non conformes aux stratégies de sécurité définies par votre organisation.

Vous pouvez définir des stratégies en fonction des exigences suivantes :

* **Appareils joints à un domaine**. Définissez une stratégie pour limiter l’accès aux appareils qui sont joints à un domaine Active Directory local et également inscrits auprès d’Azure AD. Cette stratégie s’applique aux tablettes de l’entreprise, ordinateurs portables et ordinateurs de bureau Windows.
  Pour plus d’informations sur la configuration de l’inscription automatique des appareils joints à un domaine dans Azure AD, consultez [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration-setup.md).
* **Appareils conformes**. Définissez une stratégie pour limiter l’accès aux appareils qui sont marqués comme étant **conformes** dans le répertoire du système de gestion. Cette stratégie garantit que seuls les appareils qui répondent aux stratégies de sécurité, comme le chiffrement des fichiers sur l’appareil, ont droit à l’accès. Vous pouvez utiliser cette stratégie pour limiter l’accès à partir des appareils suivants :
  
  * **Appareils Windows joints à un domaine**. Appareils gérés par System Center Configuration Manager (dans la branche actuelle) et déployés dans une configuration hybride.
  * **Appareils personnels ou professionnels Windows 10 Mobile**. Appareils gérés par Intune ou par un système de gestion d’appareils mobiles tiers pris en charge.
  * **Appareils iOS et Android**. Appareils gérés par Intune.

Les utilisateurs qui accèdent aux applications qui sont protégées par une stratégie d’autorité de certification en fonction de l’appareil doivent accéder à l’application à partir d’un appareil qui respecte les conditions requises de cette stratégie. L’accès est refusé s’il a été effectué sur un appareil qui ne respecte pas les conditions requises de la stratégie.

Pour plus d’informations sur la façon de configurer une stratégie d’autorité de certification en fonction de l’appareil dans Azure AD, consultez [Définir la stratégie d’accès conditionnel en fonction de l’appareil pour les applications connectées à Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Ressources
Consultez les articles et catégories de ressources suivants pour en savoir plus sur la définition de l’accès conditionnel pour votre organisation.

### <a name="multi-factor-authentication-and-location-policies"></a>Stratégies basées sur l’emplacement et l’authentification multifacteur
* [Getting started with conditional access to Azure AD-connected apps based on group, location, and multi-factor authentication policies (Prise en main de l’accès conditionnel aux applications connectées à Azure AD en fonction de stratégies basées sur le groupe, l’emplacement et l’authentifiaction multifacteur)](active-directory-conditional-access-azuread-connected-apps.md)
* [Prise en charge de l’accès conditionnel des applications](active-directory-conditional-access-supported-apps.md)

### <a name="device-based-conditional-access"></a>Accès conditionnel basé sur les appareils
* [Définir la stratégie d’accès conditionnel en fonction de l’appareil pour contrôler l’accès aux applications connectées à Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)
* [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration-setup.md)
* [Résolution des problèmes d’accès Azure Active Directory](active-directory-conditional-access-device-remediation.md)
* [Protégez vos données avec la réinitialisation complète ou sélective à l’aide de Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### <a name="protect-resources-based-on-sign-in-risk"></a>Protection des ressources en fonction d’un risque à la connexion
* [Azure AD Identity Protection](active-directory-identityprotection.md)

### <a name="next-steps"></a>Étapes suivantes
* [Conditional access FAQs (Forums Aux Questions sur l’accès conditionnel)](active-directory-conditional-faqs.md)
* [Référence technique](active-directory-conditional-access-technical-reference.md)




<!--HONumber=Dec16_HO4-->


