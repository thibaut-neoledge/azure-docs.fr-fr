---
title: "Modèles Azure Resource Manager - mettre à jour une ressource | Microsoft Docs"
description: "Explique comment étendre les fonctionnalités des modèles Azure Resource Manager afin de mettre à jour une ressource"
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/02/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7807b3f6135ede80cd7e7e6486364482cde72e29
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---updating-a-resource"></a>Modèles pour étendre les fonctionnalités des modèles Azure Resource Manager - mise à jour d’une ressource

Il existe certains scénarios dans lesquels vous devez mettre à jour une ressource pendant un déploiement. Vous rencontrez ce scénario lorsque vous ne pouvez pas spécifier toutes les propriétés d’une ressource jusqu’à ce que les autres ressources dépendantes aient été créées. Par exemple, si vous créez un pool principal pour un équilibrage de charge, vous pouvez mettre à jour les interfaces réseau (NIC) sur vos machines virtuelles pour les inclure dans le pool principal. Resource Manager prend en charge la mise à jour des ressources lors du déploiement, mais vous devez concevoir votre modèle correctement pour éviter les erreurs et garantir que le déploiement est traité comme une mise à jour.

## <a name="understand-the-pattern"></a>Comprendre le modèle

Tout d’abord, vous devez référencer la ressource une fois dans le modèle pour la créer, mais ensuite vous devez référencer la ressource à l’aide du même nom pour la mettre à jour ultérieurement. Toutefois, si deux ressources ont le même nom dans un modèle, Resource Manager lève une exception. Pour éviter cette erreur, spécifiez la ressource mise à jour dans un second modèle qui est lié ou inclus en tant que sous-modèle à l’aide du type de ressource `Microsoft.Resources/deployments`.

Ensuite, dans le deuxième modèle, vous devez spécifier le nom de la propriété existante à modifier ou un nouveau nom de propriété à ajouter. Ensuite, vous devez également spécifier les propriétés et leurs valeurs d’origine. Si vous ne fournissez pas les propriétés et les valeurs d’origine, Resource Manager part du principe que vous souhaitez créer une nouvelle ressource et supprime la ressource d’origine. Il remplace la ressource d’origine par une nouvelle ressource qui inclut uniquement les nouvelles propriétés que vous avez spécifiées.

Enfin, vous devez rendre la ressource dépendante de toutes les ressources associées que vous souhaitez déployer. Cette dépendance garantit que les ressources sont créées dans l’ordre correct. L’ordre est le suivant :

1. Ressource créée
2. Ressources dépendantes créées
3. Ressource (de l’étape 1) mise à jour avec les valeurs des ressources dépendantes (étape 2)

## <a name="example-template"></a>Exemple de modèle

L’exemple suivant illustre ce modèle. Il déploie un réseau virtuel nommé `firstVNet` qui a un sous-réseau nommé `firstSubnet`. Il déploie ensuite une interface réseau virtuelle nommée `nic1` et l’associe au sous-réseau. Ensuite, une ressource de déploiement nommée `updateVNet` inclut un modèle imbriqué qui fait référence au nom de la ressource `firstVNet`. 

Examinez la propriété `addressSpace` et la propriété `subnets` dans cette ressource. Notez que la valeur `addressSpace` est définie sur la même valeur de propriété que sur l’objet de déploiement de ressource `firstVNet`. Dans le tableau `subnets`, la valeur de `firstSubnet` est définie de la même façon. Étant donné que toutes les  propriétés `firstVNet` d’origine ont été spécifiées, Resource Manager met à jour la ressource dans Azure. Dans ce cas, la mise à jour correspond à l’ajout d’un sous-réseau nommé `secondSubnet`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[              
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

## <a name="try-the-template"></a>Essayer le modèle

Si vous souhaitez effectuer des tests avec ce modèle, procédez comme suit :

1.    Accédez au portail Azure, sélectionnez l’icône « + » puis recherchez le type de ressource « déploiement de modèle ». Lorsque cette ressource apparaît dans les résultats de la recherche, sélectionnez-la.
2.    Lorsque vous accédez à la page « déploiement de modèle », sélectionnez le bouton **créer**, qui ouvre le volet « déploiement personnalisé ».
3.    Sélectionnez l’icône **éditer**.
4.    Supprimez le modèle vide.
5.    Copiez et collez l’exemple de modèle précédent dans le volet de droite.
6.    Sélectionnez le bouton **enregistrer**.
7.    Vous revenez au panneau « déploiement personnalisé », mais cette fois-ci, quelques menus déroulants apparaissent. Sélectionnez votre abonnement, créez un nouveau groupe de ressources ou utilisez un groupe de ressources existant, puis sélectionnez un emplacement. Passez en revue les Conditions générales, puis sélectionnez le bouton **J’accepte**.
8.    Sélectionnez le bouton **acheter**.

Une fois le déploiement terminé, ouvrez le groupe de ressources que vous avez spécifié dans le portail. Un réseau virtuel nommé `firstVNet` et une carte réseau nommée `nic1` s’affichent. Cliquez sur `firstVNet`, puis sur `subnets`. Vous voyez `firstSubnet` qui a été créé à l’origine et vous voyez `secondSubnet` qui a été ajouté dans la ressource `updateVNet`. 

![Sous-réseau d’origine et sous-réseau mis à jour](./media/resource-manager-update/firstVNet-subnets.png)

Ensuite, revenez au groupe de ressources, cliquez sur `nic1`, puis cliquez sur `IP configurations`. Dans la section `IP configurations`, `Subnet` est défini sur `firstSubnet (10.0.0.0/24)`. 

![Paramètres de configuration IP nic1](./media/resource-manager-update/nic1-ipconfigurations.png)

Le `firstVNet` d’origine a été mis à jour au lieu d’être recréé. Si `firstVNet` avait été recréé, `nic1` ne serait pas être associé à `firstVNet`.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez utiliser ce modèle dans vos modèles pour spécifier à nouveau les propriétés d’origine de la ressource que vous souhaitez mettre à jour. Spécifiez la ressource de mise à jour dans un modèle lié ou imbriqué avec le type de ressource `Microsoft.Resources/deployments`.

* Pour une introduction à la fonction `reference()`, consultez [Fonctions du modèle Azure Resource Manager](resource-group-template-functions.md).
* Ce modèle est également implémenté dans le [modèle de projet de blocs de construction](https://github.com/mspnp/template-building-blocks) et les [architectures de référence Azure](/azure/architecture/reference-architectures/).
