<properties
	pageTitle="Configuration de l’accès WinRM pour les machines virtuelles dans Azure Resource Manager | Microsoft Azure"
	description="Comment configurer un accès WinRM pour une utilisation avec une machine virtuelle Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="singhkay"/>

# Configuration de l’accès WinRM pour les machines virtuelles dans Azure Resource Manager

## WinRM dans Azure Service Management par rapport à Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modèle de déploiement classique

* Pour obtenir une vue d’ensemble d’Azure Resource Manager, consultez cet [article](../resource-group-overview.md)
* Pour connaître les différences entre Azure Service Management et Azure Resource Manager, consultez cet [article](../resource-manager-deployment-model.md)

La principale différence dans la configuration de WinRM dans les deux piles réside dans la manière dont le certificat est installé sur la machine virtuelle. Dans la pile Azure Resource Manager, les certificats sont modélisés en tant que ressources gérées par le fournisseur de ressources Key Vault. Par conséquent, l’utilisateur doit fournir leur propre certificat et le charger vers un coffre de clés avant de l’utiliser dans une machine virtuelle.

Voici les étapes à suivre pour configurer une machine virtuelle avec une connectivité WinRM

1. Créer un coffre de clés
2. Créer un certificat auto-signé
3. Charger votre certificat auto-signé dans Key Vault
4. Obtenir l’URL de votre certificat auto-signé dans le coffre de clés
5. Référencer les URL de vos certificats auto-signés lors de la création d’une machine virtuelle

## Étape 1 : créer un coffre de clés

Vous pouvez utiliser la commande ci-dessous pour créer le coffre de clés

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## Étape 2 : créer un certificat auto-signé
Vous pouvez créer un certificat auto-signé à l’aide de ce script PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## Étape 3 : charger votre certificat auto-signé dans Key Vault

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

## Étape 4 : obtenir l’URL de votre certificat auto-signé dans le coffre de clés

Le fournisseur de ressources Microsoft.Compute a besoin de l’URL de la clé secrète dans le coffre de clés lors de l’approvisionnement de la machine virtuelle. Ainsi, le fournisseur de ressources Microsoft.Compute peut télécharger la clé secrète et créer le certificat équivalent sur la machine virtuelle.

>[AZURE.NOTE]L’URL de la clé secrète doit également inclure la version. L’URL ci-dessous est un exemple https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### Modèles

Vous pouvez obtenir le lien vers l’URL dans le modèle à l’aide du code ci-dessous

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### PowerShell

Vous pouvez obtenir cette URL à l’aide de la commande PowerShell ci-dessous

	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## Étape 5 : référencer les URL de vos certificats auto-signés lors de la création d’une machine virtuelle

#### Modèles Azure Resource Manager

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

#### PowerShell

	$vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
	$credential = Get-Credential
	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
	$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
	$sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
	$CertificateStore = "My"
	$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## Étape 6 : se connecter à la machine virtuelle
Avant de vous connecter à la machine virtuelle, vous devrez vous assurer que votre machine est configurée pour la gestion à distance WinRM. Démarrez PowerShell en tant qu’administrateur et exécutez la commande ci-dessous pour vérifier que l’installation est terminée.

    Enable-PSRemoting -Force

>[AZURE.NOTE] Vous devrez peut-être vous assurer que le service WinRM est en cours d’exécution si la commande ci-dessus ne fonctionne pas. Vous pouvez le faire à l’aide de `Get-Service WinRM`

Une fois l’installation terminée, vous pouvez vous connecter à la machine virtuelle à l’aide de la commande ci-dessous

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate

<!---HONumber=AcomDC_0629_2016-->
