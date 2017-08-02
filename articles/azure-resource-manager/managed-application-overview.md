---
title: "Vue d’ensemble de l’application gérée Azure | Microsoft Docs"
description: "Décrit les concepts relatifs à l’application gérée Azure"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 51732474e7983827988f950d344d36745564dfd2
ms.contentlocale: fr-fr
ms.lasthandoff: 07/13/2017

---

# <a name="azure-managed-applications-overview"></a>Vue d’ensemble des applications gérées Azure

Azure permet aux fournisseurs d’offrir des solutions à des clients du monde entier. La Place de marché Microsoft Azure est une galerie composée de centaines de modèles à plusieurs ressources complexes issus de fournisseurs tiers et internes. Les clients peuvent effectuer un déploiement et un démarrage en quelques minutes à l’aide d’applications PaaS et SaaS. Bien qu’elle offre un excellent moyen de déployer rapidement une offre, le client reste responsable de la gestion et de la mise à jour de la solution. Pour les fournisseurs, il n’existe aucun moyen de facturer les clients pour l’utilisation d’une application au-delà de la facturation d’image de machine virtuelle. En outre, les fournisseurs n’ont aucun moyen d’empêcher les clients de modifier les ressources des applications critiques ni de bloquer l’accès à la propriété intellectuelle qui compose une application. Les applications gérées Azure constituent une solution à ces problèmes. 

Une application gérée est similaire à un modèle de solution de la Place de marché à une importante différence près. Dans une application gérée, les ressources sont configurées sur un groupe de ressources géré par le fournisseur. Le groupe de ressources est présent dans l’abonnement du client, mais une identité du client du fournisseur a accès au groupe de ressources.

## <a name="advantages-of-managed-applications"></a>Avantages des applications gérées

Avec les applications gérées, les fournisseurs de services gérés, les éditeurs de logiciels indépendants et les équipes informatiques centrales des entreprises peuvent proposer des solutions via la Place de marché ou un catalogue de services. Bien que les clients déploient ces applications gérées dans leurs abonnements, ils n’ont pas à en gérer la maintenance ni à les mettre à jour. Ce sont les fournisseurs qui se chargent de gérer ces applications et d’en assurer le support. Par conséquent, les clients n’ont pas besoin d’acquérir des connaissances techniques propres aux applications pour les gérer. Les clients peuvent ainsi obtenir automatiquement les mises à jour des applications sans avoir à se soucier de la résolution et du diagnostic des problèmes liés à l’application.

Pour les fournisseurs, les applications gérées créent non seulement un canal de vente d’infrastructures et de logiciels via la Place de marché, mais elles permettent également d’attacher des services et un support opérationnel aux abonnements des clients d’Azure. Les fournisseurs peuvent alors facturer les clients à l’aide du système de facturation Azure et utiliser des modèles pour gérer le cycle de vie des applications déployées. Ces solutions sont autonomes et propres au client. Les fournisseurs peuvent donc leur fournir des services de haute qualité. Cette approche profite non seulement aux fournisseurs de services PaaS et SaaS, mais également aux équipes de plateforme centrale d’entreprise et aux intégrateurs système souhaitant rassembler et revendre leurs solutions.

## <a name="managed-application-types"></a>Types d’applications gérées
Les applications gérées Azure se présentent sous deux versions : un catalogue de services et la Place de marché.
 
### <a name="service-catalog"></a>Catalogue de services  

Le catalogue de services permet aux organisations de créer un catalogue de solutions approuvées pour Azure que les employés de cette organisation peuvent utiliser. Il est utile pour les équipes informatiques centrales des entreprises de maintenir un tel catalogue à jour. Elles peuvent ainsi garantir la conformité avec certaines normes organisationnelles tout en fournissant des solutions de qualité à leur organisation. Elles peuvent contrôler, mettre à jour et gérer ces applications. Les employés de l’organisation peuvent découvrir facilement le riche éventail d’applications recommandées et approuvées par le service informatique. Les clients voient uniquement les applications gérées du catalogue de services qu’ils ont créées, ou les applications gérées que d’autres personnes de leur organisation ont partagées avec eux.
 
Pour plus d’informations sur la publication d’une application gérée de catalogue de services, consultez l’article [Créer et publier une application gérée de catalogue de services](managed-application-publishing.md).
 
Pour plus d’informations sur l’utilisation d’une application gérée de catalogue de services, consultez l’article [Utiliser une application gérée du catalogue de services](managed-application-consumption.md).
 
### <a name="marketplace"></a>Marketplace

Les applications managées sont disponibles via la place de marché du portail Azure. Une fois publiées par le fournisseur, tout le monde peut utiliser ces applications, dans votre organisation ou en dehors. Cette approche permet aux fournisseurs de services gérés, éditeurs de logiciels indépendants et intégrateurs système d’offrir leurs solutions à tous les clients Azure. Les clients bénéficient de l’utilisation de solutions complexes sans avoir à comprendre et à maintenir ces solutions à jour. Actuellement, l’auteur de la publication peut proposer son offre sous forme d’application gérée ou sous forme de modèle de solution non géré. Les principaux composants de la publication d’une application gérée sont les suivants : les fichiers de modèle, qui décrivent les ressources approvisionnées, et le fichier de définition de l’interface utilisateur, qui décrit la façon dont les entrées requises pour l’approvisionnement de ces ressources s’affichent dans le portail. Les fichiers requis sont packagés dans un fichier .zip et chargés via le portail de publication.
 
Pour plus d’informations sur la publication d’applications gérées sur la Place de marché, consultez l’article [Applications gérées sur la Place de marché Azure](managed-application-author-marketplace.md).

Pour plus d’informations sur l’utilisation d’applications gérées de la Place de marché, consultez l’article [Utilisation des applications gérées de la Place de marché Azure](managed-application-consume-marketplace.md).

## <a name="key-concepts"></a>Concepts clés

### <a name="managed-resource-group"></a>Groupe de ressources géré
Groupe de ressources où sont créées toutes les ressources Azure en cours d’approvisionnement dans le modèle. Par exemple, si l’application crée un compte de stockage, ce groupe de ressources contient la ressource du compte de stockage. Il ne contient pas la ressource d’appliance.

### <a name="appliance-package"></a>Package d’appliance
L’éditeur crée un package qui contient les fichiers modèle et le fichier CreateUIDefinition. Il contient précisément les fichiers suivants :

- **applianceMainTemplate.json** : le fichier modèle qui définit toutes les ressources configurées par l’appliance. Ce fichier est un fichier modèle normal utilisé pour créer des ressources.

- **MainTemplate.json** : fichier modèle qui définit la ressource de l’appliance (Microsoft.Solutions/appliances). Une propriété essentielle définie dans cette ressource est ManagedResourceGroupId. Cette propriété indique le groupe de ressources utilisé pour héberger les ressources réelles définies dans le fichier applianceMainTemplate.json.

- **applianceCreateUIDefinition.json** : ce fichier décrit le mode de rendu de l’interface utilisateur pour les paramètres définis dans le modèle.

### <a name="authorization"></a>Autorisation
L’éditeur doit spécifier les autorisations requises par le fournisseur pour gérer les ressources pour le compte du client. Cette autorisation s’applique au groupe de ressources gérées. Vous définissez les valeurs suivantes :

- **PrincipalID** : identificateur Azure Active Directory de l’utilisateur, du groupe ou de l’application utilisée pour accorder l’accès au groupe de ressources gérées. Cet identificateur appartient au client de l’éditeur.

- **RoleDefinitionID** : identificateur Azure Active Directory du rôle affecté à l’ID principal précédent. Il peut s’agir d’un des rôles RBAC intégrés dans le client de l’éditeur. Pour plus d’informations, consultez la page [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la publication d’applications gérées sur la Place de marché, consultez l’article [Applications gérées sur la Place de marché Azure](managed-application-author-marketplace.md).
* Pour plus d’informations sur l’utilisation d’applications gérées de la Place de marché, consultez l’article [Utilisation des applications gérées de la Place de marché Azure](managed-application-consume-marketplace.md).
* Pour plus d’informations sur la publication d’une application gérée de catalogue de services, consultez l’article [Créer et publier une application gérée de catalogue de services](managed-application-publishing.md).
* Pour plus d’informations sur l’utilisation d’une application gérée de catalogue de services, consultez l’article [Utiliser une application gérée du catalogue de services](managed-application-consumption.md).
* Pour créer un fichier de définition de l’interface utilisateur, consultez [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
