---
title: "Modèles de mise en réseau d’Azure Service Fabric | Microsoft Docs"
description: "Décrit les modèles de mise en réseau courants de Service Fabric et explique comment créer un cluster à l’aide des fonctionnalités de mise en réseau d’Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 030114fa1ea9b76c0ed48baeffb8859260fc8e52
ms.openlocfilehash: 78012ae1552c01690b0ad5b1285173ef9faf12bc
ms.lasthandoff: 03/01/2017


---
# <a name="service-fabric-networking-patterns"></a>Modèles de mise en réseau de Service Fabric
L’une des questions que nous avons constatées le plus souvent lors de la création de clusters Service Fabric concerne l’intégration du cluster avec différentes fonctionnalités de mise en réseau Azure.  Cet article explique comment créer des clusters à l’aide des fonctionnalités suivantes :

- [Réseau virtuel / sous-réseau existant](#existingvnet)
- [Adresse IP publique statique](#staticpublicip)
- [Équilibreur de charge interne uniquement](#internallb)
- [Équilibreur de charge interne + externe](#internalexternallb)

Il est important de garder à l’esprit que Service Fabric s’exécute dans un groupe de machines virtuelles identiques standard : par conséquent, toutes les fonctionnalités que vous pouvez utiliser dans un groupe de machines virtuelles identiques peuvent également être utilisées avec un cluster Service Fabric. Les parties du modèle Resource Manager qui concernent la mise en réseau sont identiques.  Et, une fois que vous avez effectué le déploiement dans un réseau virtuel existant, il est facile d’intégrer d’autres fonctionnalités de mise en réseau, comme ExpressRoute, la passerelle VPN, le groupe de sécurité réseau (NSG) et l’homologation du réseau virtuel.

Le seul aspect spécifique de Service Fabric est le fait que le [Portail Azure](https://portal.azure.com) utilise en interne le fournisseur de ressources Service Fabric (SFRP) pour effectuer des appels dans un cluster afin d’obtenir des informations sur les nœuds et les applications.  SFRP requiert un accès entrant accessible publiquement au port de la passerelle HTTP (19080 par défaut) sur le point de terminaison de gestion, utilisé par [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pour gérer votre cluster. Ce port est également utilisé par le fournisseur de ressources Service Fabric pour demander des informations sur votre cluster à des fins d’affichage sur le Portail Azure.  Si ce port n’est pas accessible à partir de SFRP, un message du type « Nœuds introuvables » s’affiche sur le Portail de gestion et votre liste de nœuds et d’applications apparaît vide.  Si vous souhaitez avoir une certaine visibilité dans votre cluster par le biais du Portail Azure, votre équilibreur de charge doit exposer une adresse IP publique et votre groupe de sécurité réseau doit autoriser le trafic 19080 entrant.  Si vous ne respectez pas ces conditions, le Portail Azure n’affiche pas l’état actuel de votre cluster.  Dans le cas contraire, votre cluster n’est pas affecté et vous pouvez utiliser [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pour obtenir l’état actuel, ce qui peut être une limitation acceptable en fonction de vos impératifs de mise en réseau.  Il s’agit d’une limitation temporaire que nous prévoyons de supprimer dans une mise à jour ultérieure, qui prévoira que votre cluster pourra être inaccessible publiquement sans perte de fonctionnalités du Portail de gestion.

## <a name="templates"></a>Modèles

Tous les modèles se trouvent [ici](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip). Vous devriez pouvoir déployer les modèles tels quels avec les commandes PowerShell suivantes.  Si vous déployez le modèle de réseau virtuel existant ou le modèle d’adresse IP publique statique, veillez à parcourir la section [Configuration initiale](#initialsetup) au préalable.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Configuration initiale

### <a name="existing-virtual-network"></a>Réseau virtuel existant

Commençons avec un réseau virtuel existant nommé « ExistingRG-vnet » dans le groupe de ressources *ExistingRG*, avec un sous-réseau nommé « default ».  Il s’agit des ressources par défaut, créées lors de l’élaboration d’une machine virtuelle standard avec le Portail Azure.  Vous pouvez également créer le réseau virtuel et le sous-réseau sans créer de machine virtuelle. Cependant, l’objectif principal de l’ajout d’un cluster à un réseau virtuel existant est de fournir une connectivité réseau à d’autres machines virtuelles. Par conséquent, la création de la machine virtuelle donne un exemple concret d’utilisation courante.  Si votre cluster Service Fabric utilise seulement un équilibreur de charge interne sans adresse IP publique, la machine virtuelle avec son adresse IP publique peut également être utilisée comme jump box.

### <a name="static-public-ip-address"></a>Adresse IP publique statique

Une adresse IP publique statique est généralement une ressource dédiée, gérée de façon distincte de la ou des machines virtuelles auxquelles elle est affectée. Par conséquent, elle est approvisionnée dans un groupe de ressources réseau dédié (et non au sein du groupe de ressources du cluster Service Fabric proprement dit).  Créez une adresse IP publique statique nommée « staticIP1 » dans le même groupe de ressources *ExistingRG*, avec soit le Portail Azure soit PowerShell :

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Modèle Service Fabric

Utilisons le modèle Service Fabric template.json, téléchargeable sur le portail, avant de créer un cluster à l’aide de l’Assistant du portail standard. Vous pouvez également utiliser l’un des modèles de la [galerie de modèles](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric), comme le [Cluster Service Fabric à cinq nœuds](https://azure.microsoft.com/en-us/documentation/templates/service-fabric-unsecure-cluster-5-node-1-nodetype/).

<a id="existingvnet"></a>
## <a name="existing-virtual-networksubnet"></a>Réseau virtuel / sous-réseau existant

[24 janvier 2016 : il existe un autre exemple, qui sort du champ d’application de Service Fabric, à l’adresse [https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet)]

1. Modifiez le paramètre du sous-réseau et donnez-lui le nom du sous-réseau existant, puis ajoutez deux nouveaux paramètres pour référencer le réseau virtuel existant :

    ```
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```


2. Modifiez la variable *vnetID* pour qu’elle pointe vers le réseau virtuel existant :

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Supprimez *Microsoft.Network/virtualnetworks* des Ressources afin d’éviter qu’Azure ne crée un nouveau réseau virtuel :

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

4. Mettez en commentaire le réseau virtuel dans l’attribut *dependsOn* de *Microsoft.Compute/virtualMachineScaleSets* afin d’éviter de dépendre de la création d’un nouveau réseau virtuel :

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. Déployez le modèle :

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Après le déploiement, votre réseau virtuel doit inclure les nouvelles machines virtuelles du groupe identique et le type de nœud du groupe de machines virtuelles identiques doit afficher le réseau virtuel et le sous-réseau existants.  Vous pouvez également vous connecter par RDP à la machine virtuelle existante qui était déjà dans le réseau virtuel et exécuter une commande ping sur les nouvelles machines virtuelles du groupe identique :

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Adresse IP publique statique

1. Ajoutez les paramètres du groupe de ressources d’adresses IP statiques existant, du nom et du nom de domaine complet :

    ```
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Supprimez le paramètre *dnsName*, étant donné que l’adresse IP statique en a déjà un :

    ```
    /*
    "dnsName": {
        "type": "string"
    }, 
    */
    ```

3. Ajoutez une variable pour référencer l’adresse IP statique existante :

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Supprimez *Microsoft.Network/publicIPAddresses* des *Ressources* afin d’éviter qu’Azure ne crée une nouvelle adresse IP :

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Mettez en commentaire l’adresse IP dans l’attribut *dependsOn* de *Microsoft.Network/loadBalancers* afin d’éviter de dépendre de la création d’une nouvelle adresse IP :

    ```
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. Modifiez l’élément *publicIPAddress* de *frontendIPConfigurations* dans la ressource *Microsoft.Network/loadBalancers* pour référencer l’adresse IP statique existante au lieu d’une adresse IP nouvellement créée :

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. Remplacez *managementEndpoint* dans la ressource *Microsoft.ServiceFabric/clusters* par le nom de domaine complet du DNS de l’adresse IP statique.  **Si vous utilisez un cluster sécurisé, veillez à remplacer « http:// » par « https:// ».** (Remarque : Cette instruction vaut uniquement pour les clusters Service Fabric.  Si vous utilisez un groupe de machines virtuelles identiques, ignorez cette étape) :

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Déployez le modèle :

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Après le déploiement, votre équilibreur de charge est lié à l’adresse IP statique publique de l’autre groupe de ressources. Le point de terminaison de connexion du client Service Fabric et le point de terminaison [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pointent vers le nom de domaine complet du DNS de l’adresse IP statique.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Équilibreur de charge interne uniquement

Ce scénario remplace l’équilibreur de charge externe dans le modèle Service Fabric par défaut par un équilibreur de charge interne uniquement.  Consultez la section précédente pour connaître les implications pour le Portail Azure et SFRP.

1. Supprimez le paramètre *dnsName*, qui n’est pas nécessaire :

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Ajoutez éventuellement un paramètre d’adresse IP statique, si vous utilisez la méthode d’allocation statique. Si vous utilisez la méthode d’allocation dynamique, ce n’est pas nécessaire :

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Supprimez *Microsoft.Network/publicIPAddresses* des Ressources afin d’éviter qu’Azure ne crée une nouvelle adresse IP :

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Supprimez l’attribut *dependsOn* de l’adresse IP de *Microsoft.Network/loadBalancers* afin d’éviter de dépendre de la création d’une nouvelle adresse IP.  Ajoutez l’attribut *dependsOn* de réseau virtuel, étant donné que l’équilibreur de charge dépend désormais du sous-réseau du réseau virtuel :

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Modifiez les configurations *frontendIPConfigurations* de l’équilibreur de charge : remplacez l’utilisation d’une *publicIPAddress* par l’utilisation d’un sous-réseau et d’une *privateIPAddress*, qui utilise une adresse IP interne statique prédéfinie.  Vous pouvez utiliser une adresse IP dynamique en supprimant l’élément *privateIPAddress* et en donnant la valeur « Dynamic » à *privateIPAllocationMethod*.

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. Dans la ressource *Microsoft.ServiceFabric/clusters*, modifiez *managementEndpoint* pour qu’il pointe vers l’adresse de l’équilibreur de charge interne.  **Si vous utilisez un cluster sécurisé, veillez à remplacer « http:// » par « https:// ».** (Remarque : Cette instruction vaut uniquement pour les clusters Service Fabric.  Si vous utilisez un groupe de machines virtuelles identiques, ignorez cette étape) :

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Déployez le modèle :

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Après le déploiement, votre équilibreur de charge utilise l’adresse IP statique privée 10.0.0.250. Si vous disposez d’une autre machine dans ce même réseau virtuel, vous pouvez également accéder au point de terminaison [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) interne et vérifier qu’il se connecte à un des nœuds derrière l’équilibreur de charge.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Équilibreur de charge interne et externe

Ce scénario prend l’équilibreur de charge externe à type de nœud unique existant et ajoute un équilibreur de charge interne supplémentaire pour le même type de nœud.  Un port back-end associé à un pool d’adresses back-end ne peut être affecté qu’à un seul équilibreur de charge : vous devez donc décider quel équilibreur de charge doit avoir les ports de votre application et lequel doit avoir vos points de terminaison de gestion (port 19000/19080).  Gardez à l’esprit les restrictions SFRP ci-dessus si vous décidez de mettre les points de terminaison de gestion sur l’équilibreur de charge interne.  Cet exemple conserve les points de terminaison de gestion sur l’équilibreur de charge externe. Il ajoute un port 80 d’application et le place sur l’équilibreur de charge interne.

Si vous souhaitez un cluster à deux types de nœuds, l’un sur l’équilibreur de charge externe et l’autre sur l’équilibreur de charge interne, prenez simplement le modèle à deux types de nœuds créé sur le portail (fourni avec deux équilibreurs de charge) et faites du deuxième équilibreur de charge un équilibreur de charge interne suivant la section « Équilibreur de charge interne uniquement » ci-dessus.

1. Ajoutez le paramètre d’adresse IP de l’équilibreur de charge interne statique (consultez les remarques ci-dessus si vous souhaitez une adresse IP dynamique) :

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Ajoutez le paramètre port d’application 80 :

3. Ajoutez des versions internes des variables de mise en réseau existantes en effectuant un copier/coller et en ajoutant « -Int » aux noms :

    ```
    /* Add internal LB networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* internal LB networking variables end */
    ```

4. Si vous commencez avec le modèle généré par le portail et avec un port d’application 80, le modèle du portail par défaut ajoute *AppPort1* (port 80) sur l’équilibreur de charge externe.  Dans ce cas, supprimez-le des sondes et des règles *loadBalancingRules* de l’équilibreur de charge externe pour pouvoir l’ajouter à l’équilibreur de charge interne :

    ```
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe", 
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,,
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ], 
    "inboundNatPools": [
    ```

5. Ajoutez une deuxième ressource *Microsoft.Network/loadBalancers*, très similaire à l’équilibreur de charge interne créé dans la section précédente [Équilibreur de charge interne uniquement](#internallb), mais qui utilise les variables d’équilibreur de charge « -Int » et implémente uniquement le port d’application 80.  Cela supprime également *inboundNatPools* afin de conserver les points de terminaison RDP sur l’équilibreur de charge public ; pour qu’ils soient dans l’équilibreur de charge interne, déplacez *inboundNatPools* de l’équilibreur de charge externe vers cet équilibreur de charge interne.

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" LB variables */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add '-Internal' to name */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule, making sure to reference the "-Int" versions of the backendAddressPool, frontendIPConfiguration, and probe variables */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. Dans le profil *networkProfile* de la ressource *Microsoft.Compute/virtualMachineScaleSets*, ajoutez le pool d’adresses back-end interne :

    ```
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Déployez le modèle :

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Après le déploiement, deux équilibreurs de charge apparaissent dans le groupe de ressources ; si vous parcourez les équilibreurs de charge, vous voyez l’adresse IP publique et les points de terminaison de gestion (port 19000/19080) affectés à l’adresse IP publique, ainsi que l’adresse IP interne statique et le point de terminaison d’application (port 80) affectés à l’équilibreur de charge interne, de même que les deux équilibreurs de charge utilisant le même pool back-end de groupe de machines virtuelles identiques.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez intégrer des clusters Service Fabric avec les fonctionnalités de mise en réseau d’Azure, [créez un cluster](service-fabric-cluster-creation-via-arm.md). 

