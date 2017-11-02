---
title: "Connecteurs de proxy d’application Azure AD du portail Classic | Microsoft Docs"
description: "Explique comment créer et gérer des groupes de connecteurs dans le proxy d’Application Azure AD."
services: active-directory
documentationcenter: 
author: LizCasey
manager: femila
ms.assetid: b283796a-9679-4c79-b703-802bb850f65d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: 09e161effb595b993cc87b3a1c3773bdb5a6ca5a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publier des applications sur des réseaux et emplacements distincts à l’aide de groupes de connecteurs
> [!div class="op_single_selector"]
> * [Portail Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portail Azure Classic](active-directory-application-proxy-connectors.md)
>
>

Les groupes de connecteurs sont utiles dans divers scénarios, notamment les suivants :

* Sites contenant plusieurs centres de données interconnectés. Dans ce cas, vous souhaitez conserver autant de trafic que possible au sein du centre de données, car les liaisons entre les centres de données sont coûteuses et lentes. Vous pouvez déployer des connecteurs dans chaque centre de données pour traiter uniquement les applications contenues dans le centre de données. Cette approche réduit au minimum les liaisons entre centres de données et fournit à vos utilisateurs une expérience entièrement transparente.
* Gestion d’applications installées sur des réseaux isolés qui ne font pas partie du réseau d’entreprise principal. Vous pouvez utiliser des groupes de connecteurs pour installer des connecteurs dédiés sur des réseaux isolés afin d’isoler les applications sur le réseau.
* Pour les applications installées sur IaaS pour l’accès au cloud, les groupes de connecteurs fournissent un service commun pour sécuriser l’accès à toutes les applications. Les groupes de connecteurs ne créent pas de dépendances supplémentaires sur votre réseau d’entreprise, et ne fragmentent pas l’expérience de l’application. Les connecteurs peuvent être installés sur chaque centre de données du cloud et servir uniquement les applications qui se trouvent sur ce réseau. Vous pouvez installer plusieurs connecteurs pour atteindre une haute disponibilité.
* Prise en charge des environnements à plusieurs forêts dans lesquels des connecteurs spécifiques peuvent être déployés par forêt et définis pour servir des applications spécifiques.
* Des groupes de connecteurs peuvent être utilisés dans les sites de récupération d’urgence pour détecter le basculement ou faire office de sauvegarde pour le site principal.
* Des groupes de connecteurs peuvent également être utilisés pour servir plusieurs sociétés à partir d’un client unique.

## <a name="prerequisite-create-your-connectors"></a>Condition préalable : créez vos connecteurs
Pour regrouper vos connecteurs, [installez plusieurs connecteurs](active-directory-application-proxy-enable.md), puis nommez-les et regroupez-les. Enfin, vous devez les affecter à des applications spécifiques.

## <a name="step-1-create-connector-groups"></a>Étape 1 : créer des groupes de connecteurs
Vous pouvez créer autant de groupes de connecteurs que vous le souhaitez. La création de groupes de connecteurs s’effectue dans le portail Azure Classic.

1. Sélectionnez votre annuaire, puis cliquez sur **Configurer**.  
    ![Capture d’écran de la configuration du proxy d’application - cliquez sur Gérer les groupes de connecteurs](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)
2. Sous Proxy d’application, cliquez sur **Gérer les groupes de connecteurs** et créez un groupe de connecteurs en attribuant un nom au groupe.  
    ![Capture d’écran de groupes de connecteurs du proxy d’application - nommez le nouveau groupe](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Étape 2 : affecter des connecteurs à vos groupes
Une fois les groupes de connecteurs créés, déplacez les connecteurs vers le groupe approprié.

1. Sous **Proxy d’application**, cliquez sur **Gérer les connecteurs**.
2. Sous **Groupe**, sélectionnez le groupe souhaité pour chaque connecteur. L’activation des connecteurs dans le nouveau groupe peut prendre jusqu’à 10 minutes.  
    ![Capture d’écran de connecteurs du proxy d’application - sélectionnez un groupe dans le menu déroulant](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Étape 3 : affecter des applications à vos groupes de connecteurs
La dernière étape consiste à définir chaque application sur le groupe de connecteurs qui le sert.

1. Dans le portail Azure Classic, dans votre annuaire, sélectionnez l’application que vous voulez affecter au groupe, puis cliquez sur **Configurer**.
2. Sous **Groupe de connecteurs**, sélectionnez le groupe que l’application doit utiliser. Cette modification est appliquée immédiatement.  
    ![Capture d’écran de groupe de connecteurs du proxy d’application - sélectionnez un groupe dans le menu déroulant](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)

## <a name="see-also"></a>Voir aussi
* [Activer le proxy d’application](active-directory-application-proxy-enable.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
* [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)

Pour les dernières nouvelles et mises à jour, visitez [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)
