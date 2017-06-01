---
title: "Comprendre les identité Azure | Microsoft Docs"
description: "Acquérez une compréhension élémentaire des conditions, concepts et recommandations liés aux identités Microsoft Azure afin de pouvoir prendre une décision optimale en matière de gouvernance des identités pour votre organisation."
keywords: 
author: jeffgilb
manager: femila
ms.date: 5/12/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 8d550a9c2a6d2f28687aefc3b9db97ca3c68eb82
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="understand-azure-identity-solutions"></a>Comprendre les solutions d’identité Azure
Microsoft Azure Active Directory (Azure AD) est une solution cloud complète et hautement disponible de gestion des identités et des accès qui associe des services d’annuaire principaux, une gouvernance avancée des identités et la gestion de l’accès aux applications. Azure AD active rapidement l’[authentification unique (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) auprès de milliers d’applications commerciales et personnalisées pré-intégrées de la [Galerie d’applications Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), dont celles que vous utilisez quotidiennement, telles qu’Office 365, Salesforce.com, Box, ServiceNow et Workday.

Dans sa forme la plus simple, une identité Azure est fournie par un annuaire Azure AD qui est automatiquement associé à un abonnement Azure lors de la création de celui-ci. En tant que service d’identité dans Azure, Azure AD assure toutes les fonctions de gestion des identités et de contrôle d’accès pour des ressources cloud telles que les utilisateurs, les applications et les groupes, pour un client (ou une organisation) spécifique, comme illustré dans le diagramme suivant :

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure offre plusieurs façons de tirer parti de la gestion des identités et des accès en tant que service (IDaaS) avec divers niveaux de complexité pour répondre aux besoins de votre organisation. Le reste de cet article explique la terminologie et les concepts que vous devez comprendre, et énonce diverses recommandations pour vous aider à choisir au mieux parmi les options d’identité Azure disponibles.

## <a name="terms-to-know"></a>Termes à connaître

Avant de prendre une décision concernant la solution de service d’identité Azure appropriée pour votre organisation, vous devez avoir une compréhension élémentaire des termes couramment utilisés en relation avec les services d’identité Azure.

|Terme à connaître| Description|
|-----|-----|
|Abonnement Azure |Les abonnements sont utilisés pour payer les services cloud Azure et sont généralement liés à une carte de crédit. Vous pouvez avoir plusieurs abonnements, mais il peut être difficile de partager des ressources entre ceux-ci.|
|Client Azure | Un client Azure AD représente une organisation. Il s’agit d’une instance dédiée et approuvée d’Azure AD qu’une organisation reçoit et acquiert quand elle souscrit un abonnement à un service du Microsoft Cloud tel qu’Azure, Microsoft Intune ou Office 365. Les clients peuvent avoir accès aux services dans un environnement dédié (client unique) ou dans un environnement partagé avec d’autres organisations (plusieurs clients).|
|Annuaire Azure AD | Chaque client Azure dispose d’un annuaire Azure AD dédié et approuvé contenant ses utilisateurs, groupes et applications. Celui-ci est utilisé pour exécuter des fonctions de gestion des identités et des accès en relation avec les ressources du client. Étant donné qu’un annuaire Azure AD est automatiquement configuré pour représenter votre organisation lorsque vous souscrivez un service Microsoft Cloud tel qu’Azure, Microsoft Intune ou Office 365, les termes *Client*, *Azure AD* et *Azure Active Directory* son parfois utilisés de façon interchangeable. |
|Domaine personnalisé | Lorsque vous souscrivez pour la première fois un abonnement au service Microsoft Cloud, votre client (ou organisation) crée un nom de domaine *.onmicrosoft.com*. Toutefois, la plupart des organisations disposent d’un ou plusieurs noms de domaine utilisés pour les relations commerciales, dont les utilisateurs finaux peuvent se servir pour accéder aux ressources de l’entreprise. Vous pouvez ajouter votre nom de domaine personnalisé à Azure AD afin que vos utilisateurs puissent s’y habituer, par exemple, en spécifiant *alice@contoso.com* au lieu de *alice@contoso.onmicrosoft.com*. |
|Compte Azure AD | Il s’agit d’identités créées à l’aide d’Azure AD ou d’un autre service Microsoft Cloud tel qu’Office 365. Elles sont stockées dans l’Azure AD d’un client, et accessibles à tout abonnement au service cloud de l’organisation. |
|Administrateur d’abonnement Azure| L’administrateur de compte est la personne qui s’est inscrite ou a acheté l’abonnement Azure. Il est autorisé à accéder au [Centre des comptes](https://account.windowsazure.com/Home/Index) et à effectuer diverses tâches de gestion comme créer des abonnements, annuler des abonnements, modifier la facturation d’un abonnement et modifier l’administrateur de services fédérés. |
|Administrateur général Azure AD | Les administrateurs généraux Azure AD disposent d’un accès illimité à toutes les fonctions d’administration d’Azure AD. La personne qui souscrit un abonnement au service Microsoft Cloud devient automatiquement un administrateur général par défaut. Vous pouvez avoir plusieurs administrateurs généraux, mais seuls ceux-ci peuvent affecter les [autres rôles Administrateur](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) à des utilisateurs. |
|Compte Microsoft | Les comptes Microsoft (créés par vous pour votre usage personnel) donnent accès à l’ensemble des produits et services cloud Microsoft orientés utilisateur, tels qu’Outlook (Hotmail), Messenger, OneDrive, MSN, Xbox LIVE ou Office 365. Ces identités sont créées et stockées dans le système de comptes d’identité des consommateurs de Microsoft.|
|Comptes professionnels ou scolaires | Les comptes professionnels ou scolaires (créés par un administrateur pour une utilisation professionnelle ou scolaire) donnent accès à des services cloud Microsoft de niveau professionnel, tels qu’Azure, Intune ou Office 365.|


## <a name="concepts-to-understand"></a>Concepts à comprendre

À présent que vous connaissez la terminologie de base concernant l’identité, vous devez comprendre les concepts clés suivants pour prendre une décision éclairée concernant le service d’identité Azure.

|Concepts à comprendre |Description|
|-----|-----|
|[Association des abonnements Azure avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Chaque abonnement Azure a une relation d’approbation avec un annuaire Azure AD pour authentifier les utilisateurs, les services et les appareils. *Plusieurs abonnements peuvent approuver le même annuaire Azure AD, mais un abonnement n’approuve qu’un seul annuaire Azure AD*. Cette relation de confiance diffère de la relation qu’un abonnement a avec d’autres ressources Azure (sites web, bases de données, etc.), qui se rapprochent davantage des ressources enfants d’un abonnement. Quand un abonnement expire, l’accès aux ressources autres qu’Azure AD qui y sont associées cesse également. Toutefois, l’annuaire Azure AD restant dans Azure, vous pouvez associer un autre abonnement à cet annuaire et continuer à gérer les ressources client.|
|[Comment fonctionne la gestion des licences Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Lorsque vous achetez ou activez Enterprise Mobility Suite, Azure AD Premium ou Azure AD Standard, votre annuaire est mis à jour avec l’abonnement, y compris sa période de validité et les licences prépayées. Une fois l’abonnement actif, les fonctionnalités de service peuvent être gérées par des administrateurs généraux Azure et utilisées par des utilisateurs sous licence. Les informations relatives à votre abonnement, dont le nombre de licences attribuées ou disponibles, sont accessibles via le portail Azure, dans le panneau **Azure Active Directory** > **Licences**. Cette vignette constitue également l’emplacement idéal pour gérer vos attributions de licence.|
|[Contrôle d’accès en fonction du rôle dans le portail Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|La fonctionnalité de contrôle d’accès en fonction du rôle (RBAC) Azure permet de gérer avec précision l’accès aux ressources Azure. Un trop grand nombre d’autorisations peut exposer un compte à des attaques potentielles. Si le nombre d’autorisations est trop faible, les employés ne peuvent pas effectuer leur travail efficacement. La fonctionnalité RBAC vous permet d’octroyer précisément aux employés les autorisations dont ils ont besoin en fonction de trois rôles de base qui s’appliquent à tous les groupes de ressources : propriétaire, contributeur et lecteur. Vous pouvez également créer jusqu’à 2 000 [rôles RBAC personnalisés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) pour répondre à vos besoins spécifiques. |
|[Identité hybride](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|Vous obtenez une identité hybride en intégrant votre Windows Server Active Directory (AD DS) local avec Azure AD à l’aide d’[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Cela vous permet de fournir une identité commune à vos utilisateurs pour les applications Office 365, Azure, ainsi que les applications locales ou SaaS intégrées avec Azure AD. Avec une identité hybride, vous étendez efficacement votre environnement local au cloud pour l’identité et l’accès.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Différence entre Windows Server AD DS et Azure AD
Si vous connaissez Windows Server Active Directory domaine Services (AD DS), introduit avec Windows 2000 Server, vous comprenez probablement le concept de base d’un service d’identité. Cependant, il est également important de comprendre qu’Azure AD n’est pas seulement un contrôleur de domaine dans le cloud, mais aussi une façon entièrement nouvelle de fournir l’identité en tant que service (IDaaS) dans Azure, qui nécessite une toute autre manière de penser pour appréhender pleinement les fonctionnalités basées sur le nuage et protéger votre organisation contre les menaces modernes. Traditionnellement, les informaticiens protègent leur périmètre de sécurité à l’aide d’AD DS. Toutefois, les entreprises modernes, sans périmètre, qui prennent en charge les besoins d’identité de leurs employés, clients et partenaires nécessitent un nouveau plan de contrôle. Azure AD est ce plan de contrôle des identités.

La sécurité s’est déplacée au-delà du pare-feu d’entreprise vers le nuage où Azure AD protège les ressources de l’entreprise et l’accès en fournissant une identité commune unique pour les utilisateurs (localement ou dans le cloud). Vos utilisateurs bénéficient ainsi de la flexibilité nécessaire pour accéder en toute sécurité aux applications dont ils ont besoin pour accomplir leur travail à partir de pratiquement tout appareil, ainsi que de contrôles de protection des données en fonction du risque s’appuyant sur les fonctionnalités d’apprentissage automatique et de génération de rapports détaillés dont le service informatique à besoin pour préserver la sécurité des données d’entreprise.

### <a name="extend-office-365-management-and-security-capabilities"></a>Étendre les fonctionnalités de gestion et de sécurité d’Office 365
Vous utilisez déjà Office 365 ? Vous pouvez accélérer votre transformation numérique en étendant les fonctionnalités intégrées de gestion et de sécurité d’Office 365 avec Azure AD afin de sécuriser l’accès à l’ensemble de vos ressources, ce qui vous permet également de renforcer et de sécuriser la productivité de vos effectifs. En utilisant les fonctionnalités d’Azure AD, en plus de celles fournies par Office 365, vous pouvez sécuriser votre portefeuille d’applications tout entier en utilisant une seule identité permettant l’authentification unique de toutes les applications. Vous pouvez étendre vos capacités d’accès conditionnel en fonction, non seulement de l’état de l’appareil, mais aussi de l’utilisateur, de l’emplacement, de l’application et du risque. Les fonctionnalités d’authentification multifacteur (Multi-Factor Authentication, MFA) offrent davantage de protection lorsque vous en avez besoin. Vous allez bénéficier d’un meilleur contrôle des privilèges d’utilisateur et de la possibilité de fournir un accès administratif juste-à-temps à la demande. Vos utilisateurs seront plus productifs et créeront moins de tickets de support technique grâce aux fonctionnalités en libre-service d’Azure AD, telles que la réinitialisation des mots de passe oubliés, les demandes d’accès aux applications, ainsi que la création et la gestion des groupes.

> [!TIP]
> Vous souhaitez en savoir plus sur la gestion des identités Azure AD avec Office 365 ? [Téléchargez le livre électronique](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-identity-solutions"></a>Solutions Microsoft pour le contrôle des identités

Microsoft Azure offre plusieurs façons de gérer les identités des utilisateurs : uniquement localement, uniquement dans le cloud, ou entre les deux. Ces options sont les suivantes : AD DS à configurer en libre service dans Azure, Azure Active Directory (Azure AD), identité hybride et Azure AD Domain Services.

Les organisations qui privilégient un faible encombrement dans le cloud peuvent opter pour la solution d’**AD DS à configurer en libre service** dans Azure. Cette option prend en charge de nombreux scénarios de Windows Server AD DS bien adaptés au déploiement de machines virtuelles sur Azure. Par exemple, vous pouvez créer une machine virtuelle Azure faisant office de contrôleur de domaine opérant au sein d’un centre de données lointain connecté au réseau à distance. À partir de là, la machine virtuelle peut prendre en charge les demandes d’authentification d’utilisateurs distants et améliorer les performances de l’authentification. Cette option convient également comme alternative relativement bon marché aux sites de récupération d’urgence coûteux en hébergeant un petit nombre de contrôleurs de domaine et un seul réseau virtuel sur Azure. Enfin, vous pouvez déployer sur Azure une application telle que SharePoint, qui nécessite Windows Server AD DS mais ne dépend pas du réseau local ou du Windows Server Active Directory de l’entreprise. Dans ce cas, vous pouvez déployer une forêt isolée sur Azure pour répondre aux besoins de la batterie de serveurs SharePoint. Il est également possible de déployer des applications réseau nécessitant une connectivité au réseau et à l’instance Active Directory locaux.

Une instance **Azure Active Directory (Azure AD)** autonome est solution de gestion des accès et des identités en tant que service (IDaaS) entièrement basée sur le cloud. Azure AD offre un ensemble puissant de fonctionnalités pour gérer les utilisateurs et les groupes. Il permet de sécuriser l’accès aux applications locales et cloud, dont des services web Microsoft, tels qu’Office 365 et de nombreuses applications SaaS (Software as a service) tierces. Azure AD est proposé en 3 versions : Gratuite, De base et Premium. Azure AD améliore l’efficacité organisationnelle et étend la sécurité, au-delà du pare-feu de périmètre, à un nouveau plan de contrôle protégé par Azure Machine Learning et d’autres fonctionnalités de sécurité avancées.

Au lieu d’opter pour des solutions de gestion des identités locales ou basées sur le cloud, bon nombre de responsables de services informatiques et d’entreprises visionnaires qui ont commencé à anticiper les tendances à long terme de leur organisation étendent leurs annuaires locaux à des solutions d’**identité hybride**. Une identité hybride vous permet de bénéficier d’une solution réellement globale de gestion des identités et des accès, offrant un accès sécurisé et productif aux applications dont les utilisateurs ont besoin pour accomplir leurs tâches, tant aujourd’hui qu’à l’avenir.

> [!TIP]
> Pour en savoir plus sur la manière dont les responsables de services informatiques ont fait d’Azure Active Directory un élément central de leurs stratégies, téléchargez le [Guide Azure Active Directory pour les responsables de services informatiques](https://aka.ms/AzureADCIOGuide).

Enfin, la solution **Azure AD Domain Services** offre une option basée sur le cloud permettant de tirer parti d’AD DS pour un contrôle très léger de la configuration des machines virtuelles Azure afin de répondre aux besoins en matière de développement et de test d’applications réseau. La solution Azure AD Domain Services n’a pas pour vocation de porter votre infrastructure AD DS locale sur des machines virtuelles Azure gérées par Azure AD Domain Services. Au lieu de cela, il convient d’utiliser des machines virtuelles Azure dans des domaines gérés pour prendre en charge le développement, le test et le déplacement d’applications locales nécessitant des méthodes d’authentification AD DS vers le cloud.

## <a name="common-scenarios-and-recommendations"></a>Scénarios et recommandations courants

Voici quelques scénarios courants de gestion des identités et des accès avec des recommandations concernant l’option d’identité Azure la plus appropriée dans chaque cas.

  |Scénario d’identité| Recommandation|
  |-----|-----|
  |Je dois prendre en charge un petit nombre de machines virtuelles dans Azure, mais mon organisation a considérablement investi dans des solutions Active Directory (AD DS) locales.|Pour utiliser des machines virtuelles Azure lorsque vous devez prendre en charge un très petit nombre de machines virtuelles et avez consenti des investissements importants dans des solutions AD DS locales, optez pour [AD DS à configurer en libre service](https://msdn.microsoft.com/library/azure/jj156090.aspx). |
  |Notre activité a vu le jour dans le cloud et nous n’avons rien investi dans des solutions locales de gestion des identités.| [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) est le meilleur choix pour les entreprises opérant uniquement dans le cloud sans investissement local.|
  |Mon organisation a investi massivement dans Windows Server Active Directory en local, mais nous voulons étendre la gestion des identités au cloud.| La solution de gestion des identités Azure la plus utilisée est celle de l’[identité hybride](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Si vous avez déjà investi dans une solution AD DS locale, vous pouvez facilement étendre l’identité au cloud à l’aide d’Azure AD Connect.|
  |J’ai besoin d’une solution de configuration et de gestion de machine virtuelle Azure très légère afin de répondre aux besoins de gestion des identités pour le développement et le test d’applications.|La solution [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) est un bon choix si vous devez utiliser AD DS pour effectuer un contrôle très léger de la configuration de machines virtuelles Azure, ou si vous cherchez à développer ou à migrer vers le cloud des applications locales héritées prenant en charge les annuaires.|

## <a name="next-steps"></a>Étapes suivantes

À présent que vous comprenez les concepts liés à la gestion des identités Azure et les options disponibles, vous pouvez utiliser les ressources suivantes pour commencer à implémenter l’option de votre choix :

[Tester votre compréhension des solutions de gestion des identités Azure](https://aka.ms/aad-understand-quiz)

[En apprendre davantage dans un environnement de preuve de concept Azure](https://aka.ms/aad-poc)

[Déployer Azure AD en production](https://aka.ms/aad-onboard)

