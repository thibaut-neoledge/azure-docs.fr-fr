<properties
   pageTitle="Déployer une machine virtuelle avec une adresse IP publique statique à l’aide d’un modèle dans Resource Manager | Microsoft Azure"
   description="Découvrir comment déployer des machines virtuelles avec une adresse IP publique statique à l’aide d’un modèle dans Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/08/2016"
   ms.author="telmos" />

# Déployer une machine virtuelle avec une adresse IP publique statique à l’aide d’un modèle

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Ressources IP publiques dans un fichier de modèle

Vous pouvez afficher et télécharger les [exemples de modèles](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

La section ci-dessous illustre la définition de la ressource IP publique d’après le scénario ci-dessus.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Notez la propriété **publicIPAllocationMethod** qui a la valeur *Static*. Cette propriété peut avoir la valeur *Dynamic* (par défaut) ou *Static*. La valeur Static garantit que l’adresse IP pour cette adresse IP publique ne changera jamais.

La section suivante illustre l’association de l’adresse IP publique ci-dessus à une carte réseau.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Notez la présence de la propriété **publicIPAddress** qui pointe vers l’**Id** d’une ressource nommée **variables('webVMSetting').pipName**. Il s’agit du nom de la ressource IP publique indiquée ci-dessus.

Enfin, la carte réseau ci-dessus est répertoriée dans la propriété **networkProfile** de la machine virtuelle en cours de création.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## Déployer le modèle en un clic

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle en un clic, suivez [ce lien](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP), cliquez sur **Déployer dans Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, puis suivez les instructions dans le portail.

## Déployer le modèle à l’aide de PowerShell

Pour déployer le modèle téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) et suivez les instructions contenues dans les étapes 1 à 3.

2. Dans une console PowerShell, exécutez l’applet de commande **New-AzureRmResourceGroup** pour créer un groupe de ressources si nécessaire. Si vous avez déjà créé un groupe de ressources, passez à l’étape 3.

		New-AzureRmResourceGroup -Name StaticPublicIP -Location westus

	Sortie attendue :

		ResourceGroupName : StaticPublicIP
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. Dans une console PowerShell, exécutez l’applet de commande **New-AzureRmResourceGroupDeployment** pour déployer le modèle.

		New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
		    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
		    -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

	Sortie attendue :

		DeploymentName    : DeployVM
		ResourceGroupName : PIPTEST
		ProvisioningState : Succeeded
		Timestamp         : 1/8/2016 7:04:44 PM
		Mode              : Incremental
		TemplateLink      :
		                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
		                    ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
		                    ContentVersion : 1.0.0.0

		Parameters        :
		                    Name                      Type                       Value     
		                    ========================  =========================  ==========
		                    vnetName                  String                     WTestVNet
		                    vnetPrefix                String                     192.168.0.0/16
		                    frontEndSubnetName        String                     FrontEnd  
		                    frontEndSubnetPrefix      String                     192.168.1.0/24
		                    storageAccountNamePrefix  String                     iaasestd  
		                    stdStorageType            String                     Standard_LRS
		                    osType                    String                     Windows   
		                    adminUsername             String                     adminUser
		                    adminPassword             SecureString                         

		Outputs           :

## Déployer le modèle à l’aide de l’interface de ligne de commande Azure

Pour déployer le modèle à l’aide de l’interface de ligne de commande Azure, procédez comme suit.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installation et configuration de l’interface de ligne de commande Azure](../xplat-cli-install.md) et suivez les instructions jusqu’à l’étape vous invitant à sélectionner votre compte et votre abonnement Azure.
2. Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.

		azure config mode arm

	Voici le résultat attendu pour la commande ci-dessus :

		info:    New mode is arm

3. Ouvrez le [fichier de paramètres](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), sélectionnez son contenu et enregistrez-le dans un fichier sur votre ordinateur. Pour cet exemple, nous avons enregistré le fichier de paramètres sous le nom *parameters.json*.

4. Exécutez l’applet de commande **azure group deployment create** pour déployer le nouveau réseau virtuel à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus haut. La liste affichée après le résultat présente les différents paramètres utilisés.

		azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e parameters.json

	Sortie attendue :

		info:    Executing command group create
		+ Getting resource group PIPTEST2
		+ Creating resource group PIPTEST2
		info:    Created resource group PIPTEST2
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/PIPTEST2
		data:    Name:                PIPTEST2
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_0413_2016-->