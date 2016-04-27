<properties
	pageTitle="Gestion du contrôle d’accès basé sur les rôles à l’aide de l’API REST"
	description="Gestion du contrôle d’accès basé sur les rôles à l’aide de l’API REST"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="rest-api"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/12/2016"
	ms.author="kgremban"/>

# Gestion du contrôle d’accès basé sur les rôles à l’aide de l’API REST

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

Le contrôle d’accès en fonction du rôle (RBAC) disponible dans le portail Azure et l’API Azure Resource Manager permet une gestion très fine de l’accès à votre abonnement et à vos ressources. Cette fonctionnalité vous permet d’accorder l’accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.


## Répertorie toutes les affectations de rôle

Répertorie toutes les affectations de rôle de la portée spécifiée et des étendues secondaires.

Pour répertorier les attributions de rôle, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/read` dans la portée. Tous les rôles intégrés se voient octroyer l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### Demande

Utilisez la méthode **GET** avec l’URI suivant :

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&filter={filter}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

Remplacez *{scope}* par la portée dont vous souhaitez répertorier les attributions de rôle. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

| Level | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Groupe de ressources | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Remplacez *{api-version}* par 2015-07-01.

Remplacez *{filter}* par la condition que vous souhaitez appliquer pour filtrer la liste des attributions de rôle. Les conditions suivantes sont prises en charge :


| Condition | *{Filter}* | Replace |
|-----------|------------|---------|
| Pour répertorier les affectations de rôle pour la portée spécifiée seulement, sans y inclure les affectations de rôles à des étendues secondaires. | `atScope()` | |
| Pour répertorier les affectations de rôle pour un utilisateur, un groupe ou une application spécifiques | `principalId%20eq%20'{objectId}'` | Remplacez *{objectId}* par l’objectId Azure AD de l’utilisateur, du groupe ou du service principal. Exemple : `&filter=principalId%20eq%20'3a477f6a-6739-4b93-84aa-3be3f8c8e7c2'` |
| Pour répertorier les affectations de rôle pour un utilisateur spécifique, y compris celles affectées à des groupes dont l’utilisateur est membre | `assignedTo('{objectId}')` | Remplacez *{objectId}* par l’objectId Azure AD de l’utilisateur. Exemple : `&filter=assignedTo('3a477f6a-6739-4b93-84aa-3be3f8c8e7c2')` |



### Réponse

Code d’état : 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## Obtention d’informations sur une affectation de rôle

Obtient des informations sur une affectation de rôle unique spécifiée par l’identificateur d’affectation de rôle.

Pour obtenir des informations sur une attribution de rôle, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/read`. Tous les rôles intégrés se voient octroyer l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### Demande

Utilisez la méthode **GET** avec l’URI suivant :

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

Remplacez *{scope}* par la portée dont vous souhaitez répertorier les attributions de rôle. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

| Level | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Groupe de ressources | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Remplacez *{role-assignment-id}* par l’identificateur GUID de l’attribution de rôle.

Remplacez *{api-version}* par 2015-07-01.

### Réponse

Code d’état : 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## Créer une affectation de rôle

Créer une affectation de rôle dans la portée spécifiée pour le principal qui octroie le rôle spécifié.

Pour créer une attribution de rôle, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/write`. Parmi les rôles intégrés, seuls ceux du *propriétaire* et de l’*administrateur des accès utilisateur* se voient accorder l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### Demande

Utilisez la méthode **PUT** avec l’URI suivant :

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

Remplacez *{scope}* par la portée sur laquelle vous souhaitez créer les attributions de rôle. Lorsque vous créez une affectation de rôle pour une portée parent, toutes les portées enfants en héritent. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

| Level | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Groupe de ressources | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Remplacez *{role-assignment-id}* par un nouveau GUID. Ils sera utilisé comme identificateur GUID de la nouvelle attribution de rôle.

Remplacez *{api-version}* par 2015-07-01.

Pour le corps de la requête, saisissez les valeurs au format suivant :

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nom de l’élément | Requis | Type | Description |
|------------------|----------|--------|-------------|
| roleDefinitionId | Oui | Chaîne | Identificateur du rôle à affecter. Le format de l’identificateur est : `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId | Oui | Chaîne | objectId du principal Azure AD (utilisateur, groupe ou principal de service) auquel le rôle doit être affecté. |

### Response

Code d’état : 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## Supprimer une affectation de rôle

Supprimez une affectation de rôle au niveau de la portée spécifiée.

Pour supprimer une attribution de rôle, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/delete`. Parmi les rôles intégrés, seuls ceux du *propriétaire* et de l’*administrateur des accès utilisateur* se voient accorder l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### Demande

Utilisez la méthode **DELETE** avec l’URI suivant :

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

Remplacez *{scope}* par la portée sur laquelle vous souhaitez créer les attributions de rôle. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

| Level | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Groupe de ressources | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Remplacez *{role-assignment-id}* par le GUID de l’ID d’attribution de rôle.

Remplacez *{api-version}* par 2015-07-01.

### Réponse

Code d’état : 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## Répertorier tous les rôles

Répertorie tous les rôles disponibles à l’attribution sur la portée spécifiée.

Pour répertorier les rôles, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/read` dans la portée. Tous les rôles intégrés se voient octroyer l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### Demande

Utilisez la méthode **GET** avec l’URI suivant :

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&filter={filter}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

Remplacez *{scope}* par la portée dont vous souhaitez répertorier les rôles. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

| Level | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Groupe de ressources | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Remplacez *{api-version}* par 2015-07-01.

Remplacez *{filter}* par la condition que vous souhaitez appliquer pour filtrer la liste des rôles. Les conditions suivantes sont prises en charge :

| Condition | *{Filter}* | Replace |
|-----------|------------|---------|
| Pour répertorier les rôles disponibles à l’affectation à la portée spécifiée et chacune de ses portées enfants. | `atScopeAndBelow()` | |
| Pour rechercher un rôle utilisant le nom complet exact. | `roleName%20eq%20'{role-display-name}'` | Remplacez *{role-display-name}* par la forme codée de l’URL du nom d’affichage exact du rôle. Exemple : `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |



### Réponse

Code d’état : 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## Obtention des informations sur un rôle

Obtient des informations sur un rôle unique spécifié par l’identificateur de définition de rôle. Pour obtenir des informations sur un rôle unique en utilisant son nom complet, consultez la liste de tous les rôles et du filtre roleName.

Pour obtenir des informations sur un rôle, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/read`. Tous les rôles intégrés se voient octroyer l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### Demande

Utilisez la méthode **GET** avec l’URI suivant :

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

Remplacez *{scope}* par la portée dont vous souhaitez répertorier les attributions de rôle. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

| Level | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Groupe de ressources | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Remplacez *{role-definition-id}* par l’identificateur de définition GUID de la définition du rôle. Remplacez *{api-version}* par 2015-07-01.

### Réponse

Code d’état : 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## Créer un rôle personnalisé
Créez un rôle personnalisé.

Pour créer un rôle personnalisé, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/write` sur l’ensemble de ses `AssignableScopes`. Parmi les rôles intégrés, seuls ceux du *propriétaire* et de l’*administrateur des accès utilisateur* se voient accorder l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### Demande

Utilisez la méthode **PUT** avec l’URI suivant :

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

Remplacez *{scope}* par le premier élément *AssignableScope* du rôle personnalisé. Les exemples qui suivent montrent comment spécifier la portée sur différents niveaux :

| Level | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Groupe de ressources | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Remplacez *{role-definition-id}* par un nouveau GUID. Il sera utilisé comme identificateur GUID du nouveau rôle personnalisé.

Remplacez *{api-version}* par 2015-07-01.

Pour le corps de la requête, saisissez les valeurs au format suivant :

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nom de l’élément | Requis | Type | Description |
|--------------|----------|------|-------------|
| name | Oui | Chaîne | Identificateur GUID du rôle personnalisé. |
| properties.roleName | Oui | Chaîne | Afficher le nom complet du rôle personnalisé. Taille maximale de 128 caractères. |
| properties.description | Non | Chaîne | Description du rôle personnalisé. Taille maximale de 1024 caractères. |
| properties.type | Oui | Chaîne | Affectez la valeur à « CustomRole ». |
| properties.permissions.actions | Oui | Chaîne | Tableau de chaînes d’action spécifiant les opérations auxquelles le rôle personnalisé octroie l’accès. |
| properties.permissions.notActions | Non | Chaîne | Tableau de chaînes d’action spécifiant les opérations à exclure des opérations auxquelles le rôle personnalisé octroie l’accès. |
| properties.assignableScopes | Oui | Chaîne | Tableau des portées dans lequel le rôle personnalisé peut être utilisé pour la gestion des accès. |

### Response

Code d’état : 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## Mettre à jour un rôle personnalisé

Modifiez un rôle personnalisé.

Pour modifier un rôle personnalisé, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/write` sur l’ensemble de ses `AssignableScopes`. Parmi les rôles intégrés, seuls ceux du *propriétaire* et de l’*administrateur des accès utilisateur* se voient accorder l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### Demande

Utilisez la méthode **PUT** avec l’URI suivant :

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

Remplacez *{scope}* par le premier élément *AssignableScope* du rôle personnalisé. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

| Level | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Groupe de ressources | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Remplacez *{role-definition-id}* par l’identificateur de définition GUID du rôle personnalisé qui doit être mis à jour.

Remplacez *{api-version}* par 2015-07-01.

Pour le corps de la requête, saisissez les valeurs au format suivant :

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nom de l’élément | Requis | Type | Description |
|--------------|----------|------|-------------|
| name | Oui | Chaîne | Identificateur GUID du rôle personnalisé à mettre à jour. |
| properties.roleName | Oui | Chaîne | Nom complet du rôle personnalisé mis à jour. |
| properties.description | Non | Chaîne | Description du rôle personnalisé mis à jour. |
| properties.type | Oui | Chaîne | Affectez la valeur à « CustomRole ». |
| properties.permissions.actions | Oui | Chaîne | Tableau de chaînes d’action spécifiant les opérations auxquelles le rôle personnalisé mis à jour octroie l’accès. |
| properties.permissions.notActions | Non | Chaîne | Tableau de chaînes d’action spécifiant les opérations à exclure des opérations auxquelles le rôle personnalisé mis à jour octroie l’accès. |
| properties.assignableScopes | Oui | Chaîne | Tableau des portées dans lesquelles le rôle personnalisé mis à jour peut être utilisé pour la gestion des accès. |

### Response

Code d’état : 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## Supprimer un rôle personnalisé

Supprimez un rôle personnalisé.

Pour supprimer un rôle personnalisé, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/delete` sur l’ensemble de ses `AssignableScopes`. Parmi les rôles intégrés, seuls ceux du *propriétaire* et de l’*administrateur des accès utilisateur* se voient accorder l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### Demande

Utilisez la méthode **DELETE** avec l’URI suivant :

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

Remplacez *{scope}* par la portée dont vous souhaitez supprimer la définition de rôle. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

| Level | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Groupe de ressources | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Remplacez *{role-definition-id}* par l’id de définition de rôle GUID du rôle personnalisé qui doit être supprimé.

Remplacez *{api-version}* par 2015-07-01.

### Réponse

Code d’état : 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

<!---HONumber=AcomDC_0413_2016-->