---
title: "Publication d’applications sur des réseaux et emplacements distincts à l’aide de groupes de connecteurs dans le proxy d’application Azure AD | Microsoft Docs"
description: "Explique comment créer et gérer des groupes de connecteurs dans le proxy d’Application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 53fa0876ce1e3e2b2ac47316f37c5a0de2591d41
ms.openlocfilehash: 2a050e5817ab884c9d4337cc1cc76e298299dba3
ms.lasthandoff: 02/28/2017

---

# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups---public-preview"></a>Publier des applications sur des réseaux et emplacements distincts à l’aide de groupes de connecteurs - Version préliminaire publique
> [!div class="op_single_selector"]
> * [portail Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portail Azure Classic](active-directory-application-proxy-connectors.md)
>

## <a name="azure-ad-application-proxy-and-connector-groups"></a>Proxy d’application Azure AD et groupes de connecteurs

Les clients utilisent le proxy d’application Azure AD pour obtenir toujours plus de scénarios et d’applications. C’est pourquoi nous avons rendu le proxy d’application encore plus flexible en activant davantage de topologies. Vous pouvez créer des groupes de connecteurs Proxy d’application, une nouvelle fonctionnalité permettant d’attribuer des connecteurs spécifiques pour servir des applications spécifiques. Cette fonctionnalité génère de nombreux cas d’utilisation qui n’étaient pas possibles auparavant pour le proxy d’application. Pendant la phase de la version préliminaire privée, nous avons observé que de grandes entreprises activaient des groupes de connecteurs pour améliorer leurs déploiements de proxy d’application en direct. 

Le concept de base est que chaque connecteur de proxy d’application est attribué à un groupe de connecteurs. Tous les connecteurs qui appartiennent au même groupe de connecteurs agissent comme un groupe distinct pour la haute disponibilité et l’équilibrage de charge. Tous les connecteurs appartiennent à un groupe par défaut. L’administrateur peut créer des groupes et modifier ces affectations dans le portail Azure. 

Toutes les applications sont affectées à un groupe de connecteurs par défaut. Si votre administrateur ne modifie rien, le système continue de se comporter comme avant. Si vous ne modifiez rien, toutes les applications affectées au groupe de connecteurs par défaut incluent tous les connecteurs. Mais si vous organisez vos connecteurs en groupes, vous pouvez définir chaque application de façon à ce qu’elle fonctionne avec un groupe de connecteurs spécifique. Dans ce cas, seuls les connecteurs de ce groupe traiteront l’application sur demande.


>[!NOTE] 
>Étant donné que les nouveaux connecteurs sont automatiquement attribués à un groupe de connecteurs par défaut, nous vous recommandons, pour les déploiements à grande échelle, de n’avoir aucune application affectée au groupe par défaut. Par conséquent, une fois installés, les nouveaux connecteurs ne reçoivent aucun trafic en direct. Ce n’est qu’une fois que vous affectez le connecteur à l’un des groupes actifs qu’il peut commencer à traiter le trafic en direct. Cela vous permet également de placer des connecteurs en mode inactif à des fins de maintenance.
>

## <a name="prerequisite-create-your-connector-groups"></a>Condition préalable : créez vos groupes de connecteurs
Pour regrouper vos connecteurs, vous devez vous assurer que vous avez [installé plusieurs connecteurs](active-directory-application-proxy-enable.md). Lorsque vous installez un nouveau connecteur, il est automatiquement ajouté au groupe de connecteurs **par défaut** .

## <a name="step-1-create-connector-groups"></a>Étape 1 : créer des groupes de connecteurs
Vous pouvez créer autant de groupes de connecteurs que vous le souhaitez. La création de groupes de connecteurs s’effectue dans le [portail Azure](https://portal.azure.com).

1. Sélectionnez **Azure Active Directory** pour accéder au tableau de bord de gestion de votre répertoire. À partir de là, sélectionnez **Applications d’entreprise** > **Proxy d’application**.
2. Sélectionnez le bouton **Groupes de connecteurs** . Le panneau Nouveau groupe de connecteurs s’affiche.
3. Donnez un nom à votre nouveau groupe de connecteurs, puis utilisez le menu déroulant pour sélectionner les connecteurs qui appartiennent à ce groupe.
4. Sélectionnez **Enregistrer** lorsque votre groupe de connecteurs est terminé.

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Étape 2 : affecter des applications à vos groupes de connecteurs
La dernière étape consiste à définir chaque application sur le groupe de connecteurs destiné à la servir.

1. Dans le tableau de bord de gestion de votre répertoire, sélectionnez **Applications d’entreprise** > **Toutes les applications** > l’application que vous souhaitez affecter à un groupe de connecteurs > **Proxy d’application**.
2. Sous **Groupe de connecteurs**, utilisez le menu déroulant pour sélectionner le groupe que l’application doit utiliser.
3. Sélectionnez **Enregistrer** pour appliquer la modification.

## <a name="use-cases-for-connector-groups"></a>Cas d’utilisation des groupes de connecteurs 

Les groupes de connecteurs sont utiles dans divers scénarios, notamment les suivants :

###<a name="sites-with-multiple-interconnected-datacenters"></a>Sites contenant plusieurs centres de données interconnectés

De nombreuses organisations possèdent plusieurs centres de données interconnectés. Dans ce cas, vous souhaitez conserver autant de trafic que possible au sein du centre de données, car les liaisons entre les centres de données sont coûteuses et lentes. Vous pouvez déployer des connecteurs dans chaque centre de données pour traiter uniquement les applications contenues dans le centre de données. Cette approche réduit au minimum les liaisons entre centres de données et fournit à vos utilisateurs une expérience entièrement transparente.

### <a name="applications-installed-on-isolated-networks"></a>Applications installées sur des réseaux isolés

Les applications peuvent être hébergées sur des réseaux qui ne font pas partie du réseau d’entreprise principal. Vous pouvez utiliser des groupes de connecteurs pour installer des connecteurs dédiés sur des réseaux isolés afin d’isoler les applications sur le réseau. Cela se produit généralement lorsqu’un fournisseur tiers gère une application spécifique pour votre organisation. 

Les groupes de connecteurs vous permettent d’installer des connecteurs dédiés aux réseaux qui publient uniquement des applications spécifiques, ce qui permet de sous-traiter la gestion des applications à des fournisseurs tiers plus facilement et en toute sécurité.

### <a name="applications-installed-on-iaas"></a>Applications installées sur IaaS 

Pour les applications installées sur IaaS pour l’accès au cloud, les groupes de connecteurs fournissent un service commun pour sécuriser l’accès à toutes les applications. Les groupes de connecteurs ne créent pas de dépendances supplémentaires sur votre réseau d’entreprise, et ne fragmentent pas l’expérience de l’application. Les connecteurs peuvent être installés sur chaque centre de données du cloud et servir uniquement les applications qui se trouvent sur ce réseau. Vous pouvez installer plusieurs connecteurs pour atteindre une haute disponibilité.

Dans ce cas, l’organisation dispose de plusieurs machines virtuelles connectées à son propre réseau virtuel IaaS hébergé. Pour permettre aux employés d’utiliser ces applications, ces réseaux privés sont connectés au réseau d’entreprise via un réseau VPN de site à site. Les employés qui se trouvent sur site bénéficient ainsi d’une bonne expérience. Toutefois, cela peut ne pas être idéal pour les employés à distance, car cela nécessite une infrastructure locale supplémentaire, comme vous pouvez le voir dans le schéma ci-dessous :

![Réseau IaaS Azure AD](./media/application-proxy-publish-apps-separate-networks/application-proxy-iaas-network.png)
  
Cela peut devenir un problème lorsque de nombreuses organisations utilisent plusieurs fournisseurs cloud, car leurs applications résident dans plusieurs centres de données. Avec les groupes de connecteurs de proxy d’application Azure AD, vous pouvez activer un service commun pour sécuriser l’accès à toutes les applications sans créer de dépendance supplémentaire sur votre réseau d’entreprise :

![Fournisseurs de cloud multiples IaaS Azure AD](./media/application-proxy-publish-apps-separate-networks/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Forêts multiples : groupes de connecteurs différents pour chaque forêt

La plupart des clients qui ont déployé le proxy d’application utilisent ses fonctionnalités d’authentification unique (SSO) par le biais de la délégation Kerberos contrainte (KCD). Pour ce faire, les machines du connecteur doivent être jointes à un domaine qui peut déléguer les utilisateurs vers l’application. KCD prend en charge les fonctionnalités inter-forêts. Mais pour les entreprises qui disposent d’environnements distincts à plusieurs forêts sans approbation entre eux, il est impossible d’utiliser un connecteur unique pour toutes les forêts. 

Dans ce cas, des connecteurs spécifiques peuvent être déployés par forêt et définis pour traiter les applications qui ont été publiées pour traiter uniquement les utilisateurs de cette forêt spécifique. Chaque groupe de connecteurs représente une forêt différente. Bien que le client et la majorité de l’expérience sont unifiés pour toutes les forêts, les utilisateurs peuvent être affectés à leurs applications de forêt à l’aide de groupes Azure AD.
 
### <a name="disaster-recovery-sites"></a>Sites de récupération d’urgence

Il existe deux approches différentes que vous pouvez utiliser avec un site de récupération d’urgence, en fonction de l’implémentation de vos sites :

* Si votre site de récupération d’urgence est créé en mode actif - actif où il est identique au site principal et dispose des mêmes paramètres de mise en réseau et AD, vous pouvez créer les connecteurs sur le site de récupération d’urgence dans le même groupe de connecteurs que le site principal. Cela permet à Azure AD de détecter les basculements.
* Si votre site de récupération d’urgence est distinct du site principal, vous pouvez créer un groupe de connecteurs différent dans le site de récupération d’urgence et avoir soit 1) des applications supplémentaires, soit 2) diriger manuellement l’application existante vers le groupe de connecteurs de récupération d’urgence si nécessaire.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Traiter plusieurs entreprises à partir d’un seul client

Il existe différentes manières d’implémenter un modèle dans lequel un fournisseur de services unique déploie et gère les services relatifs à Azure AD pour plusieurs entreprises. Les groupes de connecteurs aident l’administrateur à séparer les connecteurs et les applications en différents groupes. Une méthode, qui est appropriée pour les petites entreprises, est d’avoir un seul client Azure AD tandis que les différentes entreprises ont leurs propres nom de domaine et réseaux. Cela vaut également pour les scénarios et les situations M&A où une division informatique unique sert plusieurs entreprises pour des raisons réglementaires ou professionnelles. 

## <a name="sample-configurations"></a>Exemples de configurations

Certains exemples que vous pouvez implémenter incluent les groupes de connecteurs suivants.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Configuration par défaut : inutile pour les groupes de connecteurs

Si vous n’utilisez pas les groupes de connecteurs, votre configuration ressemblerait à ceci :

![AzureAD Aucun groupe de connecteurs](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-1.png)
 
Cette configuration est suffisante pour les tests et les petits déploiements. Elle est également adaptée si votre organisation dispose d’une topologie de réseau à plat.
 
### <a name="default-configuration-and-an-isolated-network"></a>Configuration par défaut et réseau isolé

Cette configuration est une évolution de la configuration par défaut, dans laquelle une application spécifique s’exécute sur un réseau isolé, comme un réseau virtuel IaaS : 

![AzureAD Aucun groupe de connecteurs](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Configuration recommandée : plusieurs groupes spécifiques et un groupe par défaut inactif

La configuration recommandée pour les organisations de grande taille et complexes consiste à disposer du groupe de connecteurs par défaut en tant que groupe qui ne traite aucune application et qui est utilisé pour les connecteurs inactifs ou nouvellement installés. Toutes les applications sont traitées à l’aide de groupes de connecteurs personnalisés. Ainsi, toute la complexité des scénarios décrits ci-dessus est permise.

Dans l’exemple ci-dessous, l’entreprise a deux centres de données, A et B, avec deux connecteurs qui servent chaque site. Des applications différentes s’exécutent sur chaque site. 

![AzureAD Aucun groupe de connecteurs](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Étapes suivantes
* [Activer le proxy d’application](active-directory-application-proxy-enable.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
* [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)


