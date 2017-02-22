---
title: "Sécurisation des applications mobiles et web PaaS à l’aide d’Azure App Services | Microsoft Docs"
description: " Découvrir les bonnes pratiques en matière de sécurité d’Azure App Services pour protéger vos applications mobiles et web PaaS. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b3db1281bbd37fcfbec54bdeb998e6338bb08369
ms.openlocfilehash: 50421a70ede62250a0ab17a38200995c90fdcd55


---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-services"></a>Sécurisation des applications mobiles et web PaaS à l’aide d’Azure App Services

Dans cet article, nous abordons un ensemble de bonnes pratiques de sécurité [Azure App Services](https://azure.microsoft.com/services/app-service/) pour protéger vos applications mobiles et web PaaS. Ces bonnes pratiques sont issues de notre expérience d’Azure, mais également de celle des clients, comme vous.

## <a name="azure-app-services"></a>Azure App Services
[Azure App Services](../app-service/app-service-value-prop-what-is.md) est une offre PaaS qui permet de créer des applications mobiles et web pour n’importe quel appareil ou plateforme et de se connecter à des données en tout lieu, dans le cloud ou localement. App Services inclut des fonctionnalités web et mobiles qui étaient précédemment fournies séparément dans Sites Web Azure et Azure Mobile Services. Il inclut également de nouvelles fonctionnalités d’automatisation des processus d’entreprise et d’hébergement d’API cloud. App Services est un service intégré unique qui apporte un ensemble riche de fonctionnalités pour les scénarios web, mobiles et les scénarios d’intégration.

Pour en savoir plus, consultez les vues d’ensemble de [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) et de [Web Apps](../app-service-web/app-service-web-overview.md).

## <a name="best-practices"></a>Meilleures pratiques

Quand vous utilisez App Services, appliquez les bonnes pratiques suivantes :

- [S’authentifier par le biais d’Azure Active Directory (AD)](../app-service-web/web-sites-authentication-authorization.md#authenticate-through-azure-active-directory). App Services fournit un service OAuth 2.0 pour votre fournisseur d’identité. OAuth 2.0 privilégie la simplicité du développement client tout en fournissant des flux d’autorisation spécifiques pour les applications web, les applications de bureau et les téléphones mobiles. Azure AD utilise OAuth 2.0 pour vous permettre d’autoriser l’accès aux applications mobiles et web.
- Restreindre l’accès en fonction des principes du besoin de connaître et du privilège minimum. La restriction de l’accès est indispensable pour les organisations qui veulent appliquer des stratégies de sécurité portant sur l’accès aux données. La fonction de contrôle d’accès en fonction du rôle (RBAC) peut être utilisée pour affecter des autorisations aux utilisateurs, groupes et applications à une certaine étendue. Consultez [Bien démarrer avec la gestion de l’accès](../active-directory/role-based-access-control-what-is.md) pour en savoir plus sur l’octroi aux utilisateurs du droit d’accès aux applications.
- Protégez vos clés. Si vous perdez vos clés d’abonnement, qu’importe la qualité de votre stratégie de sécurité ! Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. En utilisant Key Vault, vous pouvez chiffrer les clés et les secrets (tels que les clés d’authentification, les clés de compte de stockage, les clés de chiffrement de données, les fichiers PFX et les mots de passe) à l’aide de clés protégées par des modules de sécurité matériels (HSM). Pour une meilleure garantie, vous pouvez importer ou générer des clés HSM. Pour en savoir plus, consultez [Azure Key Vault](../key-vault/key-vault-whatis.md). Vous pouvez également utiliser Key Vault pour gérer vos certificats TLS avec renouvellement automatique.
- Limitez les adresses IP source entrantes. App Services propose une fonctionnalité d’intégration de réseau virtuel qui vous permet de limiter les adresses IP source entrantes par le biais de groupes de sécurité réseau (NSG). Si vous n’êtes pas familiarisé avec les réseaux Azure Virtual Network, cette fonctionnalité vous permet de placer la plupart de vos ressources Azure dans un réseau routable non-Internet dont vous contrôlez l’accès. Pour en savoir plus, consultez [Intégrer une application à un réseau virtuel Azure](../app-service-web/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté un ensemble de bonnes pratiques de sécurité App Services pour protéger vos applications mobiles et web PaaS. Pour en savoir plus sur la sécurisation de vos déploiements PaaS, consultez :

- [Sécurisation des déploiements PaaS](security-paas-deployments.md)
- [Sécurisation des applications mobiles et web PaaS à l’aide d’Azure SQL Database et de SQL Data Warehouse](security-paas-applications-using-sql.md)



<!--HONumber=Feb17_HO1-->


