---
title: "Accès conditionnel Azure Active Directory | Microsoft Docs"
description: "Utilisez le contrôle d’accès conditionnel dans Azure Active Directory pour vérifier des conditions spécifiques lors de l’authentification pour l’accès aux applications."
services: active-directory
keywords: "accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 89d32955506afdfb9c45c5b0ce3d38d40f012e72
ms.openlocfilehash: 570a0f0a0c0932a00bbe92b1e137951ceffcd660


---
# <a name="conditional-access-in-azure-active-directory---preview"></a>Accès conditionnel dans Azure Active Directory - version préliminaire

> [!div class="op_single_selector"]
> * [Portail Azure](active-directory-conditional-access-azure-portal.md)
> * [Portail Azure Classic](active-directory-conditional-access.md)


Le comportement décrit dans cette rubrique correspond à une [version préliminaire](active-directory-preview-explainer.md).

Tout d’abord, dans un appareil où mobilité et cloud occupent le premier plan, Azure Active Directory autorise une authentification unique sur les appareils, applications et services depuis n’importe où. Avec la prolifération des appareils (y compris des appareils BYOD), des réseaux d’entreprise externes et des applications SaaS tierces, les professionnels de l’informatique sont confrontés à deux objectifs contradictoires :

- Permettre aux utilisateurs d’être productifs où et quand ils le veulent
- Protéger les ressources de l’entreprise à tout moment

Pour améliorer la productivité, Azure Active Directory fournit à vos utilisateurs un large éventail d’options permettant d’accéder à vos ressources d’entreprise. Avec la gestion des accès aux applications, Azure Active Directory vous permet de garantir que seules *les bonnes personnes* ont accès à vos applications. Et si vous souhaitiez mieux contrôler comment les bonnes personnes accèdent à vos ressources dans certaines conditions ? Et si vous étiez en mesure de bloquer l’accès à certaines applications, même pour *les bonnes personnes* ? Par exemple, vous pouvez accepter que les bonnes personnes puissent accéder à certaines applications à partir d’un réseau approuvé, mais pas qu’elles accèdent à ces applications à partir d’un réseau non approuvé. L’accès conditionnel apporte une réponse à ces questions. 

L’accès conditionnel est une fonctionnalité d’Azure Active Directory qui vous permet d’appliquer des contrôles sur l’accès aux applications dans votre environnement en fonction de conditions spécifiques. Grâce aux contrôles, vous pouvez soit ajouter d’autres conditions requises à l’accès, soit bloquer ce dernier. L’implémentation de l’accès conditionnel repose sur des stratégies. Une approche fondée sur des stratégies simplifie votre expérience de configuration, car elle suit votre perception des modalités d’accès.  
En général, vous définissez vos modalités d’accès à l’aide d’instructions basées sur le modèle suivant :

![Contrôle](./media/active-directory-conditional-access-azure-portal/10.png)

Lorsque vous remplacez les deux occurrences de « *this* » par des informations concrètes, vous disposez d’un exemple d’instruction de stratégie qui vous est probablement familier :

*Lorsque des prestataires essaient d’accéder à nos applications cloud à partir de réseaux non approuvés, bloquer l’accès.*

L’instruction de stratégie ci-dessus met en évidence la puissance de l’accès conditionnel. Vous pouvez permettre à des prestataires d’accéder à vos applications cloud (**qui**). Mais avec l’accès conditionnel, vous pouvez également définir les conditions dans lesquelles l’accès est possible (**comment**). 

Dans le contexte de l’accès conditionnel Azure Active Directory, 

- « **When this happens** » est une **instruction de condition**
- « **Then do this** » est un **contrôle**

![Contrôle](./media/active-directory-conditional-access-azure-portal/11.png)

Une stratégie d’accès conditionnel combine une instruction de condition à des contrôles.

![Contrôle](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>Commandes

Dans une stratégie d’accès conditionnel, les contrôles définissent l’action à effectuer lorsqu’une instruction de condition est remplie.  
Grâce aux contrôles, vous pouvez bloquer ou autoriser l’accès avec des exigences supplémentaires.
Lorsque vous configurez une stratégie qui autorise l’accès, vous devez sélectionner au moins une exigence.   

L’implémentation actuelle d’Azure Active Directory vous permet de configurer les exigences suivantes : 

![Contrôle](./media/active-directory-conditional-access-azure-portal/05.png)

- **Multi-factor Authentication** - Grâce à l’authentification multifacteur, vous pouvez appliquer une authentification renforcée. En tant que fournisseur, vous pouvez combiner Azure Multi-Factor Authentication ou une authentification multifacteur locale, avec AD FS (Active Directory Federation Services). L’authentification multifacteur contribue à empêcher tout accès à vos ressources par un utilisateur non autorisé qui peut avoir obtenu l’accès aux informations d’identification d’un utilisateur valide.

- **Appareil conforme** - Vous pouvez définir des stratégies d’accès conditionnel au niveau de l’appareil. Vous pouvez configurer une stratégie permettant seulement aux ordinateurs conformes ou aux appareils mobiles inscrits dans une application de gestion des appareils mobiles d’accéder aux ressources de votre organisation. Par exemple, vous pouvez utiliser Intune pour vérifier la conformité de l’appareil, puis la signaler à Azure AD pour application lorsque l’utilisateur tente d’accéder à une application. Pour obtenir des informations détaillées sur l’utilisation d’Intune afin de protéger les applications et les données, consultez Protéger les données et les applications avec Microsoft Intune. Vous pouvez également utiliser Intune pour appliquer la protection des données pour les appareils perdus ou volés. Pour plus d’informations, consultez Protection de vos données avec effacement complet ou sélectif à l’aide de Microsoft Intune.

- **Appareil joint à un domaine** – vous pouvez exiger que l’appareil utilisé pour vous connecter à Azure Active Directory soit joint à un domaine. Cette stratégie s’applique aux tablettes de l’entreprise, ordinateurs portables et ordinateurs de bureau Windows. Pour plus d’informations sur la configuration de l’inscription automatique des appareils joints à un domaine dans Azure AD, consultez [Inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration.md).

Si vous avez sélectionné plusieurs exigences dans une stratégie d’accès conditionnel, vous pouvez également les configurer en vue de les appliquer. Vous pouvez rendre un ou l’ensemble des contrôles sélectionnés obligatoires.

![Contrôle](./media/active-directory-conditional-access-azure-portal/06.png)


## <a name="condition-statement"></a>Instruction de condition

La section précédente vous a présenté les options permettant de bloquer ou de limiter l’accès à vos ressources sous forme de contrôles. Dans une stratégie d’accès conditionnel, vous définissez les critères que vos contrôles doivent remplir, sous la forme d’une instruction de condition.  

Vous pouvez inclure les assignations suivantes dans votre instruction de condition :
    
![Contrôle](./media/active-directory-conditional-access-azure-portal/07.png)


- **Qui** - Souvent, vous voudrez appliquer vos contrôles à un ensemble donné d’utilisateurs. Dans une instruction de condition, vous pouvez définir cet ensemble en sélectionnant les utilisateurs et groupes concernés par votre stratégie. Au besoin, vous pouvez également exclure explicitement un ensemble d’utilisateurs de votre stratégie en les exemptant.  
En sélectionnant des utilisateurs et des groupes, vous définissez les utilisateurs auxquels s’applique votre stratégie.    

    ![Contrôle](./media/active-directory-conditional-access-azure-portal/08.png)



- **Quoi** - En général, certaines applications qui s’exécutent dans votre environnement requièrent une protection supérieure à d’autres. Cela concerne notamment les applications qui ont accès à des données sensibles. En sélectionnant des applications cloud, vous définissez celles auxquelles s’applique votre stratégie. Au besoin, vous pouvez également exclure explicitement un ensemble d’applications de votre stratégie. 

    ![Contrôle](./media/active-directory-conditional-access-azure-portal/09.png)


- **Comment** - Tant que l’accès à vos applications s’effectue dans des conditions que vous pouvez contrôler, il est inutile d’imposer des contrôles supplémentaires sur les modalités d’accès de vos utilisateurs à vos applications cloud. Toutefois, les choses peuvent être différentes si l’accès à vos applications cloud s’effectue notamment à partir de réseaux non approuvés ou d’appareils non conformes. Dans une instruction de condition, vous pouvez définir certaines conditions d’accès qui précisent des exigences supplémentaires concernant le mode d’accès à vos applications.

    ![Conditions](./media/active-directory-conditional-access-azure-portal/01.png)


## <a name="conditions"></a>Conditions

Dans l’implémentation actuelle d’Azure Active Directory, vous pouvez définir des conditions pour les aspects suivants :


- **Plateformes d’appareils** – La plateforme d’appareils se caractérise par le système d’exploitation qui s’exécute sur votre appareil (Android, iOS, Windows Phone ou Windows). Vous pouvez définir les plateformes d’appareils incluses et celles qui sont exclues d’une stratégie.  
Pour utiliser des plateformes d’appareils dans la stratégie, commencez par régler les options de configuration sur **Oui**, puis sélectionnez une, plusieurs ou l’ensemble des plateformes d’appareils auxquelles s’applique la stratégie. Si vous sélectionnez certaines plateformes d’appareils, la stratégie ne s’applique qu’à celles-ci. Dans ce cas, la stratégie est sans effet sur les connexions aux autres plateformes prises en charge. 

    ![Conditions](./media/active-directory-conditional-access-azure-portal/02.png)

- **Emplacements** - L’emplacement est identifié par l’adresse IP du client que vous avez utilisée pour vous connecter à Azure Active Directory. Pour cette condition, vous devez connaître les adresses IP approuvées. Les adresses IP approuvées sont une fonctionnalité de Multi-Factor Authentication, qui permet de définir les plages d’adresses IP approuvées, correspondant à l’intranet local de votre organisation. Lorsque vous configurez des conditions d’emplacement, les adresses IP approuvées vous permettent de faire la distinction entre les connexions effectuées depuis le réseau de votre organisation et celles provenant de tous les autres emplacements. Pour plus d’informations, consultez [Adresses IP approuvées](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  
Vous pouvez soit inclure tous les emplacements ou toutes les adresses IP approuvées, soit exclure toutes les adresses IP approuvées.

    ![Conditions](./media/active-directory-conditional-access-azure-portal/03.png)


- **Application cliente** - Le client peut être l’application (navigateur web, application mobile, client de bureau) que vous avez utilisée pour vous connecter à Azure Active Directory ou vous pouvez sélectionner Exchange Active Sync.  
L’authentification héritée concerne les clients qui utilisent l’authentification de base, comme les anciens clients Office qui n’exploitent pas l’authentification moderne. Pour l’instant, l’accès conditionnel ne fonctionne pas avec l’authentification héritée.

    ![Conditions](./media/active-directory-conditional-access-azure-portal/04.png)


## <a name="what-you-should-know"></a>Ce que vous devez savoir

### <a name="do-i-need-to-assign-a-user-to-my-policy"></a>Dois-je affecter un utilisateur à ma stratégie ?

Lorsque vous configurez une stratégie d’accès conditionnel, vous devez lui affecter au moins un groupe. Une stratégie d’accès conditionnel, à laquelle aucun utilisateur ou groupe n’est affecté, n’est jamais déclenchée.

Lorsque vous prévoyez d’affecter plusieurs utilisateurs et groupes à une stratégie, vous devez y aller progressivement en n’affectant qu’un utilisateur ou groupe, puis en testant votre configuration. Si votre stratégie fonctionne comme prévu, ajoutez-lui d’autres affectations.  


### <a name="how-are-assignments-evaluated"></a>Comment les affectations sont-elles évaluées ?

Toutes les attributions sont reliées par l’opérateur logique **AND**. Si vous configurez plusieurs affectations, elles doivent toutes être satisfaites pour que la stratégie soit déclenchée.  

Pour configurer une condition d’emplacement applicable à toutes les connexions non établies depuis le réseau de votre organisation, vous devez :

- inclure **tous les emplacements**,
- exclure **toutes les adresses IP approuvées**. 

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Que se passe-t-il si vous avez configuré des stratégies dans le portail Azure Classic et le portail Azure ?  
Azure Active Directory applique les deux stratégies et l’utilisateur n’obtient l’accès que si toutes les conditions requises sont remplies.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Que se passe-t-il si vous avez des stratégies dans le portail Intune Silverlight et le portail Azure ?
Azure Active Directory applique les deux stratégies et l’utilisateur n’obtient l’accès que si toutes les conditions requises sont remplies. 

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Que se passe-t-il si j’ai configuré plusieurs stratégies pour le même utilisateur ?  
À chaque connexion, Azure Active Directory évalue toutes les stratégies et vérifie que toutes les conditions requises sont remplies avant d’accorder l’accès à l’utilisateur.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>L’accès conditionnel fonctionne-t-il avec Exchange ActiveSync ?
 
Non, pour l’instant, vous ne pouvez pas utiliser Exchange ActiveSync dans une stratégie d’accès conditionnel.


### <a name="what-happens-if-i-require-multi-factor-authentication-or-a-compliant-device"></a>Que se passe-t-il si je demande une authentification multifacteur et un appareil conforme ?

Actuellement, l’utilisateur est invité à utiliser l’authentification multifacteur, quel que soit l’appareil.



## <a name="common-scenarios"></a>Scénarios courants

### <a name="requiring-multi-factor-authentication-for-apps"></a>Exiger l’authentification multifacteur pour les applications

Dans de nombreux environnements, certaines applications nécessitent un niveau de protection plus élevé que d’autres.
C’est le cas des applications qui ont accès à des données sensibles. Si vous souhaitez ajouter une couche de protection supplémentaire pour ces applications, vous pouvez configurer une stratégie d’accès conditionnel qui requiert l’authentification multifacteur lorsque les utilisateurs accèdent à ces applications.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Exiger l’authentification multifacteur pour l’accès à partir de réseaux non approuvés

Ce scénario est semblable au précédent, car il ajoute une exigence pour l’authentification multifacteur.
Toutefois, la principale différence réside dans la condition de cette exigence.  
Tandis que le scénario précédent se concentre sur les applications ayant accès à des données sensibles, ce scénario concerne les emplacements approuvés.  
En d’autres termes, l’authentification multifacteur peut être requise si un utilisateur accède à une application à partir d’un réseau que vous n’avez pas approuvé. 


### <a name="only-trusted-devices-can-access-office-365-services"></a>Seuls les appareils approuvés ont accès aux services Office 365

Si vous utilisez Intune dans votre environnement, vous pouvez utiliser d’emblée l’interface de stratégie d’accès conditionnel dans la console Azure.

De nombreux clients Intune utilisent l’accès conditionnel pour vérifier que seuls les appareils approuvés ont accès aux services Office 365. Cela signifie que les appareils mobiles sont inscrits dans Intune, qu’ils répondent aux critères de la stratégie de conformité et que des PC Windows sont joints à un domaine local. L’avantage, c’est que vous n’avez pas à définir la même stratégie pour chacun des services Office 365.  Lorsque vous créez une stratégie, configurez les applications cloud pour inclure chacune des applications Office&365; que vous souhaitez protéger avec l’accès conditionnel. 

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).



<!--HONumber=Jan17_HO3-->


