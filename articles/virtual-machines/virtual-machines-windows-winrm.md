---
title: "Configurer l’accès WinRM pour une machine virtuelle Azure | Microsoft Docs"
description: "Configurez l’accès WinRM à utiliser avec une machine virtuelle Azure créée à l’aide du modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: ebc2195bbb3526171359cc126ea6b59d0089f4a0
ms.openlocfilehash: 6559b8605a311bb774f6c8b38a9298875d3a5362
ms.lasthandoff: 02/14/2017


---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configuration de l’accès WinRM pour les machines virtuelles dans Azure Resource Manager
## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM dans Azure Service Management par rapport à Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

* Pour obtenir une vue d’ensemble d’Azure Resource Manager, consultez cet [article](../azure-resource-manager/resource-group-overview.md)
* Pour connaître les différences entre Azure Service Management et Azure Resource Manager, consultez cet [article](../azure-resource-manager/resource-manager-deployment-model.md)

La principale différence dans la configuration de WinRM dans les deux piles réside dans la manière dont le certificat est installé sur la machine virtuelle. Dans la pile Azure Resource Manager, les certificats sont modélisés en tant que ressources gérées par le fournisseur de ressources Key Vault. Par conséquent, l’utilisateur doit fournir leur propre certificat et le charger vers un coffre de clés avant de l’utiliser dans une machine virtuelle.

Voici les étapes à suivre pour configurer une machine virtuelle avec une connectivité WinRM

1. Créer un coffre de clés
2. Créer un certificat auto-signé
3. Charger votre certificat auto-signé dans Key Vault
4. Obtenir l’URL de votre certificat auto-signé dans le coffre de clés
5. Référencer les URL de vos certificats auto-signés lors de la création d’une machine virtuelle

## <a name="step-1-create-a-key-vault"></a>Étape 1 : créer un coffre de clés
Vous pouvez utiliser la commande ci-dessous pour créer le coffre de clés

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Étape 2 : créer un certificat auto-signé
Vous pouvez créer un certificat auto-signé à l’aide de ce script PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Étape 3 : charger votre certificat auto-signé dans Key Vault
Avant de charger le certificat dans le coffre de clés créé à l’étape 1, vous devez le convertir dans un format que le fournisseur de ressources Microsoft.Compute comprend. Le script PowerShell ci-dessous vous permettra de le faire

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Étape 4 : obtenir l’URL de votre certificat auto-signé dans le coffre de clés
Le fournisseur de ressources Microsoft.Compute a besoin de l’URL de la clé secrète dans le coffre de clés lors de l’approvisionnement de la machine virtuelle. Ainsi, le fournisseur de ressources Microsoft.Compute peut télécharger la clé secrète et créer le certificat équivalent sur la machine virtuelle.

> [!NOTE]
> L’URL de la clé secrète doit également inclure la version. Un exemple d’URL se présente comme suit https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve
> 
> 

#### <a name="templates"></a>Modèles
Vous pouvez obtenir le lien vers l’URL dans le modèle à l’aide du code ci-dessous

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Vous pouvez obtenir cette URL à l’aide de la commande PowerShell ci-dessous

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Étape 5 : référencer les URL de vos certificats auto-signés lors de la création d’une machine virtuelle
#### <a name="azure-resource-manager-templates"></a>Modèles Azure Resource Manager
Lorsque vous créez une machine virtuelle par le biais de modèles, le certificat est référencé dans la section des clés secrètes et la section winRM comme suit :

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Vous trouverez un exemple de modèle pour machine virtuelle dans [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Pour obtenir le code source pour ce modèle, consultez [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Étape 6 : se connecter à la machine virtuelle
Avant de vous connecter à la machine virtuelle, vous devrez vous assurer que votre machine est configurée pour la gestion à distance WinRM. Démarrez PowerShell en tant qu’administrateur et exécutez la commande ci-dessous pour vérifier que l’installation est terminée.

    Enable-PSRemoting -Force

> [!NOTE]
> Vous devrez peut-être vous assurer que le service WinRM est en cours d’exécution si la commande ci-dessus ne fonctionne pas. Vous pouvez le faire à l’aide de `Get-Service WinRM`
> 
> 

Une fois l’installation terminée, vous pouvez vous connecter à la machine virtuelle à l’aide de la commande ci-dessous

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate

