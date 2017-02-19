<properties
  pageTitle="Créer une machine virtuelle avec un modèle Resource Manager | Microsoft Docs"
  description="Utilisez un modèle Resource Manager et PowerShell pour créer facilement une machine virtuelle Windows."
  services="virtual-machines-windows" 
  documentationcenter=""
  author="davidmu1"
  manager="timlt"
  editor=""
  tags="azure-resource-manager"/>

<tags
  ms.assetid="19129d61-8c04-4aa9-a01f-361a09466805"
  ms.service="virtual-machines-windows"
  ms.workload="na"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="01/06/2017"
  ms.author="davidmu"/>


# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources

Cet article explique la procédure de déploiement d’un modèle Azure Resource Manager à l’aide de PowerShell. Le [modèle](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) déploie une machine virtuelle unique exécutant Windows Server dans un nouveau réseau virtuel avec un seul sous-réseau.

Pour obtenir une description détaillée de la ressource de machine virtuelle, consultez [Virtual machines in an Azure Resource Manager template (Machines virtuelles dans un modèle Azure Resource Manager)](virtual-machines-windows-template-description.md). Pour plus d’informations sur toutes les ressources d’un modèle, consultez [Guide de création d’un modèle Resource Manager](../resource-manager-template-walkthrough.md).

Il vous faudra environ&5; minutes pour effectuer les étapes décrites dans cet article.

## <a name="step-1-install-azure-powershell"></a>Étape 1 : installer Azure PowerShell
Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## <a name="step-2-create-a-resource-group"></a>Étape 2 : création d'un groupe de ressources
Toutes les ressources doivent être déployées dans un [groupe de ressources](../azure-resource-manager/resource-group-overview.md).

1. Obtenez la liste des emplacements disponibles où créer des ressources.
   
  ```powershell   
  Get-AzureRmLocation | sort DisplayName | Select DisplayName
  ```

2. Créez le groupe de ressources à l’emplacement sélectionné. Cet exemple illustre la création d’un groupe de ressources nommé **MyResourceGroup** dans la région **États-Unis du Centre**.

  ```powershell   
  New-AzureRmResourceGroup -Name "myResourceGroup" -Location "Central US"
  ```
   
  Un résultat comme l’exemple suivant devrait s’afficher :

  ```
  ResourceGroupName : myResourceGroup
  Location          : centralus
  ProvisioningState : Succeeded
  Tags              :
  ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup
  ```

## <a name="step-3-create-the-resources"></a>Étape 3 : création des ressources
Déployez le modèle et indiquez des valeurs de paramètre à l’invite. Cet exemple déploie le modèle 101-vm-simple-windows template dans le groupe de ressources que vous avez créé :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" 
```
Vous êtes invité à fournir le nom du compte d’administrateur sur la machine virtuelle, le mot de passe du compte et le préfixe DNS.

Un résultat comme l’exemple suivant devrait s’afficher :

```
DeploymentName    : azuredeploy
ResourceGroupName : myResourceGroup
ProvisioningState : Succeeded
Timestamp         : 12/29/2016 8:11:37 PM
Mode              : Incremental
TemplateLink      :
                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/
                    101-vm-simple-windows/azuredeploy.json
                    ContentVersion : 1.0.0.0
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myAdminUser
                    adminPassword    SecureString
                    dnsLabelPrefix   String                     myDomain
                    windowsOSVersion String                     2016-Datacenter
Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ===========
                    hostname         String                     myDomain.centralus.cloudapp.azure.com
DeploymentDebugLogLevel :
```

> [!NOTE]
> Vous pouvez également déployer des modèles et des paramètres à partir de fichiers locaux. Pour en savoir plus, consultez la rubrique [Utilisation d’Azure PowerShell avec Azure Storage](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Étapes suivantes
* Si vous rencontrez des problèmes lors du déploiement, nous vous conseillons de consulter la section [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).
* Pour apprendre à gérer la machine virtuelle que vous avez créée, consultez [Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Jan17_HO2-->


