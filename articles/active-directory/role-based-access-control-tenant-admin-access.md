---
title: "Élever l’accès d’un administrateur client - Azure AD | Microsoft Docs"
description: "Cette rubrique décrit les rôles intégrés pour le contrôle d’accès en fonction du rôle (RBAC)."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andredm
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 0fa44799a0bd49d3d96a1916f32e6452405abce8
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017

---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Élever l’accès en tant qu’administrateur client avec le contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle permet aux administrateurs clients d’obtenir des élévations temporaires d’accès grâce auxquelles ils peuvent accorder des autorisations plus élevées que la normale. Un administrateur client peut s’élever lui-même au rôle d’administrateur des accès utilisateur si nécessaire. Ce rôle donne à l’administrateur client les autorisations nécessaires pour accorder à lui-même ou à d’autres des rôles au niveau de l’étendue « / ».

Cette fonctionnalité est importante, car elle permet à l’administrateur client d’afficher tous les abonnements qui existent dans une organisation. Elle permet également aux applications d’automatisation (comme la facturation et les audits) d’accéder à tous les abonnements et de fournir une vue précise de l’état de l’organisation pour la facturation et la gestion des actifs.  

## <a name="how-to-use-elevateaccess-to-give-tenant-access"></a>Utiliser elevateAccess pour donner l’accès client

Le processus de base comprend les étapes suivantes :

1. Avec REST, appelez *elevateAccess*, qui vous accorde le rôle d’administrateur des accès utilisateur au niveau de l’étendue « / ».

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Créez une [attribution de rôle](/rest/api/authorization/roleassignments) pour attribuer le rôle de votre choix quelle que soit l’étendue. L’exemple suivant montre les propriétés permettant d’attribuer le rôle de lecteur au niveau de l’étendue « / » :

    ```
    { "properties":{
    "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
    "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
    "scope": "/"
    },
    "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. En tant qu’administrateur des accès utilisateur, vous pouvez également supprimer des attributions de rôles au niveau de l’étendue « / ».

4. Révoquez vos privilèges d’administrateur des accès utilisateur jusqu’à ce que vous en ayez à nouveau besoin.


## <a name="how-to-undo-the-elevateaccess-action"></a>Annuler l’action elevateAccess

Lorsque vous appelez *elevateAccess*, vous créez une attribution de rôle pour vous-même : pour révoquer ces privilèges, vous devez donc supprimer l’attribution.

1.  Appelez [GET roleDefinitions](/rest/api/authorization/roledefinitions#RoleDefinitions_Get), où roleName = administrateur des accès utilisateur, pour déterminer le GUID de nom du rôle d’administrateur des accès utilisateur. La réponse est de ce type :

    ```
    {"value":[{"properties":{
    "roleName":"User Access Administrator",
    "type":"BuiltInRole",
    "description":"Lets you manage user access to Azure resources.",
    "assignableScopes":["/"],
    "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
    "createdOn":"0001-01-01T08:00:00.0000000Z",
    "updatedOn":"2016-05-31T23:14:04.6964687Z",
    "createdBy":null,
    "updatedBy":null},
    "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "type":"Microsoft.Authorization/roleDefinitions",
    "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
    "nextLink":null}
    ```

    Enregistrez le GUID du paramètre *name*, dans ce cas **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Appelez [GET roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_Get), où principalId = votre propre ObjectId. Cette action liste toutes vos attributions dans le client. Recherchez celui dont l’étendue est « / » et le RoleDefinitionId se termine par le GUID du nom de rôle que vous avez trouvé à l’étape 1. L’attribution de rôle doit ressembler à ceci :

    ```
    {"value":[{"properties":{
    "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "principalId":"{objectID}",
    "scope":"/",
    "createdOn":"2016-08-17T19:21:16.3422480Z",
    "updatedOn":"2016-08-17T19:21:16.3422480Z",
    "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
    "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
    "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
    "type":"Microsoft.Authorization/roleAssignments",
    "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
    "nextLink":null}
    ```

    Ici encore, enregistrez le GUID du paramètre *name*, dans ce cas **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

3. Enfin, appelez [DELETE roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById), où roleAssignmentId = le GUID de nom que vous avez trouvé à l’étape 2.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [gestion du contrôle d’accès en fonction du rôle à l’aide de REST](role-based-access-control-manage-access-rest.md)

- [Gérer les attributions d’accès](role-based-access-control-manage-assignments.md) dans le Portail Azure

