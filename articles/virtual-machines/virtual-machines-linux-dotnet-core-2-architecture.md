<properties
   pageTitle="Déploiement de ressources de calcul avec des modèles Azure Resource Manager | Microsoft Azure"
   description="Didacticiel sur DotNet Core pour les machines virtuelles Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# Architecture d’application avec des modèles Azure Resource Manager

Lors du développement d’un déploiement Azure Resource Manager, les exigences de calcul doivent être mappées aux services et ressources Azure. Si une application comprend plusieurs points de terminaison http, une base de données, et un service de mise en cache de données, les ressources Azure hébergeant ces composants doivent être rationalisées. Ainsi, l’exemple d’application du Store musique comprend une application web hébergée sur une machine virtuelle, et une base de données SQL hébergée dans Azure SQL Database.

Ce document décrit en détail la manière dont les ressources de calcul du Store musique sont configurées dans l’exemple de modèle Azure Resource Manager. Toutes les dépendances et configurations uniques sont en surbrillance. Pour optimiser l’expérience, prédéployez une instance de la solution sur votre abonnement Azure et travaillez avec le modèle Azure Resource Manager. Pour le modèle complet, consultez [Déploiement du Store musique sur Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## Machine virtuelle

L’application du Store musique comprend une application web dans laquelle les clients peuvent parcourir et acheter de la musique. Bien qu’il existe plusieurs services Azure capables d’héberger des applications web, pour cet exemple, une machine virtuelle est utilisée. Avec l’exemple de modèle du Store musique, une machine virtuelle est déployée, un serveur web est installé, et le site web du Store musique est installé et configuré. Dans le cadre de cet article, seul le déploiement de machine virtuelle est détaillé. La configuration du serveur web et de l’application sera décrite en détails dans un prochain article.

Vous pouvez ajouter une machine virtuelle à un modèle à l’aide de l’Assistant Ajouter une nouvelle ressource de Visual Studio, ou en insérant un JSON valide dans le modèle de déploiement. Lors du déploiement d’une machine virtuelle, plusieurs ressources associées sont également nécessaires. Si vous utilisez Visual Studio pour créer le modèle, ces ressources sont créées pour vous. Si vous construisez manuellement le modèle, ces ressources doivent être insérées et configurées.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [JSON de machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L295).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
      ........<truncated>  
    }
```

Une fois la machine virtuelle déployée, ses propriétés sont visibles dans le portail Azure.

![Machine virtuelle](./media/virtual-machines-linux-dotnet-core/vm.png)

## Compte de stockage

Les comptes de stockage offrent de nombreuses options et fonctionnalités de stockage. Dans le contexte des machines virtuelles Azure, un compte de stockage conserve les disques durs virtuels de la machine virtuelle et tout disque de données supplémentaire. L’exemple du Store musique inclut un compte de stockage pour stocker le disque dur virtuel de chaque machine virtuelle composant le déploiement.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Compte de stockage](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L109).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
},
```

Un compte de stockage est associé à une machine virtuelle à l’intérieur de la déclaration de la machine virtuelle du modèle Resource Manager.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Association d’une machine virtuelle et d’un compte de stockage](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L341).

```none
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

Après le déploiement, le compte de stockage est visible dans le portail Azure.

![Compte de stockage](./media/virtual-machines-linux-dotnet-core/storacct.png)

Vous pouvez consulter le fichier du lecteur de disque dur virtuel de chaque machine virtuelle déployée avec le modèle en cliquant sur le conteneur d’objets blob du compte de stockage.

![Disques durs virtuels](./media/virtual-machines-linux-dotnet-core/vhd.png)

Pour plus d’informations sur Stockage Azure, voir la [documentation d’Azure Storage](https://azure.microsoft.com/documentation/services/storage/).

## Réseau virtuel

Si une machine virtuelle nécessite une mise en réseau interne offrant, par exemple, la possibilité de communiquer avec d’autres machines virtuelles et ressources Azure, un réseau virtuel Azure est requis. Un réseau virtuel ne rend pas la machine virtuelle accessible sur Internet. La connectivité publique requiert une adresse IP publique, qui est détaillée plus loin dans cette série.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Réseau et sous-réseaux virtuels](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L136).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "10.0.0.0/24",
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
          }
        }
      }
    ]
  }
}
```

Dans le portail Azure, le réseau virtuel ressemble à l’image suivante. Notez que toutes les machines virtuelles déployées avec le modèle sont attachées au réseau virtuel.

![Réseau virtuel](./media/virtual-machines-linux-dotnet-core/vnet.png)

## Interface réseau

 Une interface réseau connecte une machine virtuelle à un réseau virtuel, plus spécifiquement à un sous-réseau défini dans le réseau virtuel.
 
 Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Interface réseau](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L166).
 
```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceNamePrefix'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'SSH-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/SSH-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Chaque ressource de machine virtuelle comprend un profil réseau. L’interface réseau est associée à la machine virtuelle dans ce profil.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Profil réseau de machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L350).


```none
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceNamePrefix'), copyindex()))]"
    }
  ]
}
```

Dans le portail Azure, l’interface réseau ressemble à l’image suivante. L’association de l’adresse IP interne et de la machine virtuelle est visible sur la ressource d’interface réseau.

![Interface réseau](./media/virtual-machines-linux-dotnet-core/nic.png)

Pour plus d’informations sur les réseaux virtuels Azure, voir la [du réseau virtuel Azure](https://azure.microsoft.com/documentation/services/virtual-network/).

## Azure SQL Database

En plus d’une machine virtuelle hébergeant le site web du Store musique, une base de données Azure SQL Database est déployée pour héberger la base de données du Store musique. L’avantage de l’utilisation d’Azure SQL Database ici est qu’un deuxième ensemble de machines virtuelles n’est pas requis, et que la mise à l’échelle et la disponibilité sont intégrées au service.

Vous pouvez ajouter une base de données Azure SQL Database à l’aide de l’Assistant Ajouter une nouvelle ressource de Visual Studio, ou en insérant un JSON valide dans un modèle. La ressource SQL Server inclut un nom d’utilisateur et un mot de passe auxquels sont associés des droits d’administration sur l’instance SQL. Par ailleurs, une ressource de pare-feu SQL est ajoutée. Par défaut, les applications hébergées dans Azure sont en mesure de se connecter à l’instance SQL. Pour qu’une application externe telle que SQL Server Management Studio puisse se connecter à l’instance SQL, le pare-feu doit être configuré. Pour la démonstration du Store musique, la configuration par défaut est correcte.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L401.


```none
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicStoreSqlName')]",
  "location": "[resourceGroup().location]",

  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('sqlAdminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicStoreSqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Vue des bases de données SQL Server et du Store musique dans le portail Azure.

![SQL Server](./media/virtual-machines-linux-dotnet-core/sql.png)

Pour plus d’informations sur le déploiement d’Azure SQL Database, voir la [Documentation d’Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).

## Étape suivante

<hr>

[Étape 2 : accès et sécurité dans les modèles Azure Resource Manager](./virtual-machines-linux-dotnet-core-3-access-security.md)

<!---HONumber=AcomDC_0928_2016-->