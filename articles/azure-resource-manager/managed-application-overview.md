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
ms.date: 05/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a4348a4d2348d744c03ad3a89d0548526fa2f9f8
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="azure-managed-applications-overview"></a>Vue d’ensemble des applications gérées Azure

Aujourd’hui, Azure offre une place de marché fiable où les éditeurs de logiciels indépendants et les start ups peuvent proposer leurs solutions aux clients du monde entier. Azure Marketplace est une galerie composée de centaines de modèles à plusieurs ressources complexes issus de fournisseurs tiers et internes. Les clients peuvent effectuer un déploiement et un démarrage en quelques minutes à l’aide d’applications PaaS et SaaS. Bien qu’elle offre un excellent moyen de déployer rapidement une offre, le client reste responsable de la gestion et de la mise à jour de la solution. Pour les fournisseurs, il n’existe aucun moyen de facturer les clients pour l’utilisation d’une application au-delà de la facturation d’image de machine virtuelle. En outre, les fournisseurs n’ont aucun moyen d’empêcher les clients de modifier les ressources des applications critiques ni de bloquer l’accès à la propriété intellectuelle qui compose une application. Les applications gérées Azure constituent une solution à ces problèmes. 

## <a name="advantages-of-managed-applications"></a>Avantages des applications gérées

Les applications gérées Azure offrent un écosystème qui permet aux fournisseurs de proposer des services service PaaS ou SaaS sous forme d’applications autonomes. Les clients déploient des applications gérées dans leurs abonnements, mais les fournisseurs peuvent les gérer. Cela permet aux fournisseurs de facturer les clients à l’aide du système de facturation Azure et d’utiliser des modèles pour gérer le cycle de vie des applications déployées. D’un autre côté, cela permet aux clients d’acquérir des mises à jour automatiquement et de payer pour le support et la maintenance. Ils n’ont pas à gérer ou mettre à jour l’application eux-mêmes ni à diagnostiquer et résoudre les problèmes en cas d’échec de l’application.

Un tel écosystème dans Azure profiterait non seulement aux fournisseurs de services PaaS et SaaS, mais également aux équipes de plate-forme centrale d’entreprise et aux intégrateurs système souhaitant rassembler et revendre leurs solutions.

## <a name="how-managed-applications-work"></a>Fonctionnement des applications gérées
Deux situations se présentent :

1. Le fournisseur ou l’éditeur de logiciels indépendants créant une application gérée et qui la rend disponible pour une utilisation plus large. 
2. Le client ou le consommateur souhaitant créer et utiliser l’application publiée. 

Cet article aborde ces deux situations. Tout d’abord, essayons de comprendre le fonctionnement d’une application gérée. 

Une application gérée est similaire à un modèle de solution marketplace à une importante différence près. Dans une application gérée, les ressources sont configurées sur un groupe de ressources géré par le fournisseur/l’éditeur de logiciels indépendants. Le groupe de ressources est présent dans l’abonnement du client, mais un utilisateur, un groupe d’utilisateurs ou une application du client de l’éditeur de logiciels indépendants a accès au groupe de ressources. Pour gérer et assurer la maintenance de l’application, l’identité du fournisseur est ajoutée à un rôle de lecteur, de collaborateur, de propriétaire Active Directory ou à tout autre rôle intégré. 

## <a name="key-concepts"></a>Concepts clés

### <a name="managed-resource-group"></a>Groupe de ressources géré
Groupe de ressources où sont créées toutes les ressources Azure en cours d’approvisionnement dans le modèle. Par exemple, si l’application crée un compte de stockage, ce groupe de ressources contient la ressource du compte de stockage. Il ne contient pas la ressource d’appliance.

### <a name="appliance-package"></a>Package d’appliance
L’éditeur crée un package qui contient les fichiers modèle et le fichier CreateUIDefinition. Il contient précisément les fichiers suivants :

- **applianceMainTemplate.json** : le fichier modèle qui définit toutes les ressources configurées par l’appliance. Ce fichier est un fichier modèle normal utilisé pour créer des ressources.

- **MainTemplate.json** : fichier modèle qui définit la ressource de l’appliance (Microsoft.Solutions/appliances). Une propriété essentielle définie dans cette ressource est ManagedResourceGroupId. Cette propriété indique le groupe de ressources utilisé pour héberger les ressources réelles définies dans le fichier applianceMainTemplate.json.

- **createUIDefinition.json** : ce fichier décrit le mode de rendu de l’interface utilisateur pour les paramètres définis dans le modèle.

### <a name="authorization"></a>Autorisation
L’éditeur doit spécifier les autorisations requises par le fournisseur pour gérer les ressources pour le compte du client. Cette autorisation s’applique au groupe de ressources gérées. Vous définissez les valeurs suivantes :

- **PrincipalID** : identificateur Azure Active Directory de l’utilisateur, du groupe ou de l’application utilisée pour accorder l’accès au groupe de ressources gérées. Cet identificateur appartient au client de l’éditeur.

- **RoleDefinitionID** : identificateur Azure Active Directory du rôle affecté à l’ID principal précédent. Il peut s’agir d’un des rôles RBAC intégrés dans le client de l’éditeur.

## <a name="next-steps"></a>Étapes suivantes

* Pour comprendre l’expérience client, consultez [Créer et publier une application gérée Azure](managed-application-publishing.md).
* Pour comprendre l’expérience client, consultez [Utiliser une application gérée Azure](managed-application-consumption.md).
* Pour créer un fichier de définition de l’interface utilisateur, consultez [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
