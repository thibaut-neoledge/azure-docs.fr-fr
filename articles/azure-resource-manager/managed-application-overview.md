---
title: "Vue d’ensemble des applications gérées Azure | Microsoft Docs"
description: "Décrit les concepts relatifs aux applications gérées Azure"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/19/2017
ms.author: gauravbh
ms.openlocfilehash: 96b07bb3b923a5120e3d43c3fa60b3b1308010b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-managed-applications-overview"></a>Vue d’ensemble des applications gérées Azure

Les fournisseurs qui utilisent Azure peuvent offrir des solutions à des clients du monde entier. La Place de marché Azure est une galerie composée de centaines de modèles à plusieurs ressources complexes issus de fournisseurs tiers et internes. En quelques minutes, les clients peuvent déployer et commencer à utiliser des applications PaaS (plateforme en tant que service) et SaaS (logiciel en tant que service). 

Bien que la Place de marché Azure offre un excellent moyen pour les clients de déployer rapidement une offre, le client est responsable de la gestion et de la mise à jour de la solution. Pour les fournisseurs, il n’existe aucun moyen de facturer les clients pour l’utilisation d’une application au-delà de la facturation d’image de machine virtuelle. En outre, les fournisseurs ne peuvent pas empêcher les clients de modifier des ressources d’application critiques. De même, les fournisseurs ne peuvent pas bloquer l’accès à la propriété intellectuelle qui constitue une application. Les applications gérées Azure constituent une solution à ces problèmes. 

Une application gérée est similaire à un modèle de solution de la Place de marché à une importante différence près. Dans une application gérée, les ressources sont configurées sur un groupe de ressources géré par le fournisseur. Le groupe de ressources est présent dans l’abonnement du client, mais une identité du client du fournisseur a accès au groupe de ressources.

## <a name="advantages-of-managed-applications"></a>Avantages des applications gérées

Avec les applications gérées, les fournisseurs de services gérés, les éditeurs de logiciels indépendants et les équipes informatiques centrales des entreprises peuvent proposer des solutions via la Place de marché ou le Catalogue de services. Bien que les clients déploient ces applications gérées dans leurs abonnements, ils n’ont pas à en gérer la maintenance ni à les mettre à jour. Étant donné que les fournisseurs gèrent et assurent le support pour les applications, les clients n’ont pas besoin d’acquérir des connaissances techniques propres aux applications pour les gérer. Les clients peuvent ainsi obtenir automatiquement les mises à jour des applications sans avoir à se soucier de la résolution et du diagnostic des problèmes liés aux applications.

Pour les fournisseurs, les applications gérées créent un canal pour vendre de l’infrastructure et des logiciels via la Place de marché. Les applications gérées permettent également d’associer des services et un support opérationnel aux clients Azure. Les fournisseurs peuvent facturer les clients à l’aide du système de facturation Azure. Ils peuvent utiliser des modèles pour gérer le cycle de vie des applications déployées. Ces solutions sont autonomes et propres au client. Les fournisseurs peuvent donc leur fournir des services de haute qualité. Cette approche est bénéfique pour les fournisseurs de services PaaS et SaaS. Elle aide également les équipes de plateforme centrales d’entreprise et les intégrateurs système qui souhaitent packager et revendre leurs solutions.

## <a name="managed-application-types"></a>Types d’applications gérées
Les applications gérées Azure se présentent sous deux types : un catalogue de services et la Place de marché.
 
### <a name="service-catalog"></a>Catalogue de services  

Le catalogue de services permet aux clients de créer un catalogue de solutions approuvées pour Azure que les employés de leur organisation peuvent utiliser. Il est utile pour les équipes informatiques centrales des entreprises de maintenir un tel catalogue à jour. Ils peuvent utiliser le catalogue pour garantir la conformité avec certaines normes organisationnelles tout en fournissant des solutions à leur organisation. Elles peuvent contrôler, mettre à jour et gérer ces applications. Les employés peuvent utiliser le catalogue pour découvrir facilement le riche éventail d’applications recommandées et approuvées par leur service informatique. Les clients voient les applications gérées du catalogue de services qu’ils ont créées. Ils peuvent également voir les applications gérées que d’autres personnes dans leur organisation partagent avec eux.
 
Pour plus d’informations sur la publication d’une application gérée de catalogue de services, consultez l’article [Créer et publier une application gérée de catalogue de services](managed-application-publishing.md).
 
Pour plus d’informations sur l’utilisation d’une application gérée de catalogue de services, consultez l’article [Utiliser une application gérée du catalogue de services](managed-application-consumption.md).
 
### <a name="marketplace"></a>Place de marché

Les applications managées sont disponibles via la place de marché du portail Azure. Une fois publiées par le fournisseur, tout le monde peut utiliser ces applications, au sein d’une organisation ou en dehors. Cette approche permet aux fournisseurs de services gérés, éditeurs de logiciels indépendants et intégrateurs système d’offrir leurs solutions à tous les clients Azure. Les clients bénéficient de l’utilisation de ces solutions complexes sans avoir à comprendre et à maintenir ces solutions à jour. 

Actuellement, l’auteur de la publication peut proposer son offre sous forme d’application gérée ou sous forme de modèle de solution non géré. Les principaux composants de la publication d’une application gérée incluent le fichier de modèle et le fichier de définition de l’interface utilisateur. Le fichier de modèle décrit les ressources qui sont approvisionnées. Le fichier de définition de l’interface utilisateur décrit la façon dont les entrées requises pour l’approvisionnement de ces ressources sont affichées dans le portail. Les fichiers requis sont packagés dans un fichier .zip et chargés via le portail de publication.
 
Pour plus d’informations sur la publication d’une application gérée sur la Place de marché, consultez [Applications gérées sur la Place de marché Azure](managed-application-author-marketplace.md).

Pour plus d’informations sur l’utilisation d’applications gérées de la Place de marché, consultez l’article [Utilisation des applications gérées de la Place de marché Azure](managed-application-consume-marketplace.md).

## <a name="key-concepts"></a>Concepts clés

### <a name="managed-resource-group"></a>Groupe de ressources géré
Groupe de ressources géré où sont créées toutes les ressources Azure en cours d’approvisionnement dans le modèle. Par exemple, si l’application est utilisée pour créer un compte de stockage, ce groupe de ressources contient la ressource du compte de stockage. Il ne contient pas la ressource d’application.

### <a name="application-package"></a>Package d’application
L’éditeur crée un package qui contient le fichier modèle et le fichier createUIDefinition. Il contient précisément les fichiers suivants :

- **mainTemplate.json** : ce fichier modèle définit toutes les ressources approvisionnées par l’application. Ce fichier est un fichier modèle normal utilisé pour créer des ressources.

- **createUIDefinition.json** : ce fichier décrit le mode de rendu de l’interface utilisateur nécessaire pour les paramètres définis dans le modèle.

### <a name="authorization"></a>Autorisation
L’éditeur doit spécifier les autorisations requises par le fournisseur pour gérer les ressources pour le compte du client. Cette autorisation s’applique au groupe de ressources gérées. Définissez les valeurs suivantes :

- **PrincipalID** : identificateur Azure Active Directory (Azure AD) de l’utilisateur, du groupe ou de l’application utilisée pour accorder l’accès au groupe de ressources gérées. Cet identificateur appartient au client de l’éditeur.

- **RoleDefinitionID** : identificateur Azure Active Directory du rôle affecté à l’ID principal précédent. Il peut s’agir d’un des rôles Contrôle d’accès en fonction du rôle intégrés dans le client de l’éditeur. Pour plus d’informations, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la publication d’applications gérées sur la Place de marché, consultez l’article [Applications gérées sur la Place de marché Azure](managed-application-author-marketplace.md).
* Pour plus d’informations sur l’utilisation d’applications gérées de la Place de marché, consultez l’article [Utilisation des applications gérées de la Place de marché Azure](managed-application-consume-marketplace.md).
* Pour plus d’informations sur la publication d’une application gérée de catalogue de services, consultez l’article [Créer et publier une application gérée de catalogue de services](managed-application-publishing.md).
* Pour plus d’informations sur l’utilisation d’une application gérée de catalogue de services, consultez l’article [Utiliser une application gérée du catalogue de services](managed-application-consumption.md).
* Pour créer un fichier de définition de l’interface utilisateur, consultez [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
