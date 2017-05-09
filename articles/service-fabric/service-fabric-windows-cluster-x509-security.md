---
title: "Sécuriser un cluster Azure Service Fabric sur Windows à l’aide de certificats | Microsoft Docs"
description: "Cet article explique comment sécuriser les communications au sein du cluster autonome ou privé, ainsi qu’entre les clients et le cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 51ed17ab8f036f00b285232500dc9f606f2a7e2f
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>Sécuriser un cluster autonome sur Windows à l’aide de certificats X.509
Cet article explique comment sécuriser les communications entre les différents nœuds de votre cluster Windows autonome, et comment authentifier des clients se connectant à ce cluster à l’aide de certificats X.509. Cela garantit que seuls les utilisateurs autorisés puissent accéder au cluster et aux applications déployées, et effectuer des tâches de gestion.  La sécurité par certificat doit être activée sur le cluster lors de sa création.  

Pour en savoir plus sur la sécurité de cluster, telle que la sécurité nœud à nœud, la sécurité client à nœud et le contrôle d’accès en fonction du rôle, consultez [Scénarios de sécurité du cluster](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need"></a>Quels sont les certificats requis ?
Pour commencer, [téléchargez le package de cluster autonome](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) sur l’un des nœuds de votre cluster. Dans le package téléchargé, vous trouverez un fichier **ClusterConfig.X509.MultiMachine.json** . Ouvrez le fichier et consultez la section relative à la **sécurité** dans la section **Propriétés** :

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, 
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }
        ]
        "ReverseProxyCertificate":{
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        }
    }
}
```

Cette section décrit les certificats nécessaires pour la sécurisation de votre cluster Windows autonome. Si vous spécifiez un certificat de cluster, affectez à **ClusterCredentialType** la valeur _**X509**_. Si vous spécifiez un certificat de serveur pour des connexions externes, donnez à **ServerCredentialType** la valeur _**X509**_. Bien que ce ne soit pas obligatoire, nous vous recommandons d’avoir ces deux certificats pour que votre cluster soit correctement sécurisé. Si vous définissez ces valeurs sur *X509*, vous devez également spécifier les certificats correspondants ; sinon, Service Fabric lèvera une exception. Dans certains scénarios, vous pouvez ne spécifier que _ClientCertificateThumbprints_ ou _ReverseProxyCertificate_. Dans ces scénarios, vous n’avez pas besoin de définir _ClusterCredentialType_ ou _ServerCredentialType_ sur _X509_.


> [!NOTE]
> Un [Thumbprint](https://en.wikipedia.org/wiki/Public_key_fingerprint) est l’identité principale d’un certificat. Lisez la section [Comment récupérer l’empreinte numérique d’un certificat](https://msdn.microsoft.com/library/ms734695.aspx) pour déterminer le Thumbprint des certificats que vous créez.
> 
> 

Le tableau suivant répertorie les certificats dont vous aurez besoin pour la configuration de votre cluster :

| **Paramètre CertificateInformation** | **Description** |
| --- | --- |
| ClusterCertificate |Ce certificat est requis pour sécuriser les communications entre les nœuds sur un cluster. Vous pouvez utiliser deux certificats différents : un certificat principal et un certificat secondaire pour la mise à niveau. Définissez le Thumbprint du certificat principal dans la section **Thumbprint**, et celui du certificat secondaire dans les variables **ThumbprintSecondary**. |
| ServerCertificate |Ce certificat est présenté au client lorsqu’il tente de se connecter à ce cluster. Pour plus de commodité, vous pouvez choisir d’utiliser le même certificat pour les éléments *ClusterCertificate* et *ServerCertificate*. Vous pouvez utiliser deux certificats de serveur différents : un certificat principal et un certificat secondaire pour la mise à niveau. Définissez le Thumbprint du certificat principal dans la section **Thumbprint**, et celui du certificat secondaire dans les variables **ThumbprintSecondary**. |
| ClientCertificateThumbprints |Il s’agit du jeu de certificats que vous souhaitez installer sur les clients authentifiés. Il peut y avoir plusieurs certificats clients installés sur les machines qui seront autorisées à accéder au cluster. Définissez le Thumbprint de chaque certificat dans la variable **CertificateThumbprint** . Si vous affectez la valeur **true** au paramètre *IsAdmin*, le client sur lequel ce certificat est installé peut effectuer des activités de gestion d’administrateur sur le cluster. Si **IsAdmin** est *false*, le client ayant ce certificat peut effectuer uniquement les actions autorisées pour les droits d’accès utilisateur, généralement en lecture seule. Pour plus d’informations sur les rôles, consultez [Contrôle d’accès en fonction du rôle (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac) |
| ClientCertificateCommonNames |Définissez le nom commun du premier certificat client pour **CertificateCommonName**. L’élément **CertificateIssuerThumbprint** est l’empreinte numérique relative à l’émetteur de ce certificat. Pour en savoir plus sur les noms communs et l’émetteur, consultez la section [Utilisation des certificats](https://msdn.microsoft.com/library/ms731899.aspx) . |
| ReverseProxyCertificate |Il s’agit d’un certificat facultatif à spécifier si vous souhaitez sécuriser votre [Proxy inverse](service-fabric-reverseproxy.md). Assurez-vous que reverseProxyEndpointPort est défini dans nodeTypes, si vous utilisez ce certificat. |

Voici un exemple de configuration de cluster dans lequel les certificats de client, de serveur et de cluster ont été fournis.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="acquire-the-x509-certificates"></a>Acquérir des certificats X.509
Pour sécuriser les communications à l’intérieur du cluster, vous devez d’abord obtenir des certificats X.509 pour vos nœuds de cluster. En outre, pour limiter les connexions à ce cluster aux ordinateurs/utilisateurs autorisés, vous devez obtenir et installer des certificats pour les ordinateurs clients.

Vous devez utiliser un certificat X.509 signé par une [autorité de certification](https://en.wikipedia.org/wiki/Certificate_authority) pour sécuriser les clusters exécutant des charges de travail de production. Pour plus d’informations sur l’obtention de ces certificats, accédez à [Comment : obtenir un certificat (WCF)](http://msdn.microsoft.com/library/aa702761.aspx).

Pour les clusters que vous utilisez à des fins de test, vous pouvez choisir d’utiliser un certificat auto-signé.

## <a name="optional-create-a-self-signed-certificate"></a>Facultatif : Créer un certificat auto-signé
Pour créer un certificat auto-signé qui peut être sécurisé correctement, l’une des solutions consiste à utiliser le script *CertSetup.ps1* contenu dans le dossier du Kit de développement logiciel (SDK) Service Fabric dans le répertoire *C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Modifiez ce fichier pour changer le nom par défaut du certificat (recherchez la valeur *CN=ServiceFabricDevClusterCert*). Exécutez ce script en tant que `.\CertSetup.ps1 -Install`.

Maintenant, exportez le certificat vers un fichier PFX avec un mot de passe protégé. Tout d’abord, récupérez l’empreinte du certificat. Dans le menu *Démarrer*, exécutez *Gérer les certificats d’ordinateur*. Accédez au dossier **Ordinateur local/Personnel** et recherchez le certificat que vous venez de créer. Double-cliquez sur le certificat pour l’ouvrir, sélectionnez l’onglet *Détails* et faites défiler jusqu’au champ *Thumbprint*. Copiez la valeur de l’empreinte dans la commande PowerShell ci-dessous, après avoir supprimé les espaces.  Remplacez la valeur `String` par un mot de passe suffisamment sécurisé et exécutez les commandes suivantes dans PowerShell :

```powershell   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Pour afficher les détails d’un certificat installé sur l’ordinateur, vous pouvez exécuter la commande PowerShell suivante :

```powershell
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

Si vous avez un abonnement Azure, vous pouvez également suivre les instructions de la section [Ajout de certificats à Key Vault](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).

## <a name="install-the-certificates"></a>Installer les certificats
Une fois que vous avez des certificats, vous pouvez les installer sur les nœuds du cluster. La dernière version de Windows PowerShell 3.x doit être installée sur vos nœuds. Vous devrez répéter cette procédure sur chaque nœud, pour les certificats de cluster et de serveur et pour chaque certificat secondaire éventuel.

1. Copiez le ou les fichiers .pfx sur le nœud.
2. Ouvrez une fenêtre PowerShell en tant qu’administrateur, puis entrez les commandes suivantes. Remplacez l’élément *$pswd* par le mot de passe que vous avez utilisé pour créer ce certificat. Remplacez l’élément *$PfxFilePath* par le chemin complet du fichier .pfx copié sur ce nœud.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Maintenant, définissez le contrôle d’accès sur ce certificat afin que le processus Service Fabric, qui s’exécute sous le compte Service réseau, puisse l’utiliser en exécutant le script suivant. Spécifiez l’empreinte numérique du certificat et « SERVICE RÉSEAU » comme compte de service. Vous pouvez vérifier que les ACL du certificat sont correctes en ouvrant le certificat dans *Démarrer* > *Gérer les certificats d’ordinateur* et en examinant *Toutes les tâches* > *Gérer les clés privées*.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current acl of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ace to the acl of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new acl
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate.
    get-acl $keyFullPath| fl
    ```
4. Répétez les étapes ci-dessus pour chaque certificat de serveur. Vous pouvez également utiliser ces étapes pour installer les certificats clients sur les ordinateurs qui seront autorisés à accéder au cluster.

## <a name="create-the-secure-cluster"></a>Créer le cluster sécurisé
Après avoir configuré la section relative à la **sécurité** du fichier **ClusterConfig.X509.MultiMachine.json**, vous pouvez passer à la section [Créer votre cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) pour configurer les nœuds et créer le cluster autonome. N’oubliez pas d’utiliser le fichier **ClusterConfig.X509.MultiMachine.json** lors de la création du cluster. Voici un exemple de commande :

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Quand le cluster Windows autonome sécurisé s’exécute correctement et que vous avez configuré les clients authentifiés pour qu’ils s’y connectent, suivez les instructions de la section [Se connecter à un cluster sécurisé avec PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) pour vous y connecter. Par exemple :

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Vous pouvez ensuite exécuter d’autres commandes PowerShell pour travailler avec ce cluster. Par exemple, [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode.md?view=azureservicefabricps) pour afficher la liste des nœuds sur ce cluster sécurisé.


Pour supprimer le cluster, connectez-vous au nœud du cluster dans lequel vous avez téléchargé le package Service Fabric, ouvrez une ligne de commande et accédez au dossier du package. Exécutez à présent la commande suivante :

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Une configuration incorrecte des certificats peut empêcher l’affichage du cluster pendant le déploiement. Pour diagnostiquer les problèmes de sécurité, ouvrez le groupe de l’Observateur d’événements *Journaux des applications et des services* > *Microsoft-Service Fabric*.
> 
> 


