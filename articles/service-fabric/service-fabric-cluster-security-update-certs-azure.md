---
title: "Ajouter, substituer et supprimer des certificats utilisés dans un cluster Service Fabric dans Azure | Microsoft Docs"
description: "Décrit comment charger un certificat de cluster secondaire, puis le substituer à l’ancien certificat principal."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 829af0b685f07c2e529edf1a0ef72b741d37a14c


---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Ajouter ou supprimer des certificats pour un cluster Service Fabric dans Azure
Il est recommandé de vous familiariser avec la façon dont Service Fabric utilise les certificats X.509. Pour cela, consultez [Scénarios de sécurité d’un cluster](service-fabric-cluster-security.md). Vous devez comprendre ce qu’est un certificat de cluster et quelle est son utilité avant de passer à la suite.

Service Fabric vous permet de spécifier deux certificats de cluster, un principal et un secondaire, lorsque vous configurez la sécurité par certificat lors de la création du cluster. Pour plus d’informations, consultez [Création d’un cluster avec le portail](service-fabric-cluster-creation-via-portal.md) ou [Création d’un cluster Azure avec Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Si vous effectuez le déploiement avec Resource Manager et que vous ne spécifiez qu’un seul certificat de cluster, ce dernier est utilisé comme certificat principal. Après la création du cluster, vous pouvez ajouter un certificat en tant que certificat secondaire.

> [!NOTE]
> Pour un cluster sécurisé, vous avez toujours besoin d’au moins un certificat (principal ou secondaire) valide (non révoqué ni arrivé à expiration) déployé. Dans le cas contraire, le cluster cesse de fonctionner. 90 jours avant l’expiration de tous les certificats valides, le système génère un suivi d’avertissement et un événement d’avertissement d’intégrité sur le nœud. Actuellement, Service Fabric n’envoie aucun courrier électronique ou notification d’aucune sorte à ce sujet. 
> 
> 

## <a name="add-a-secondary-certificate-using-the-portal"></a>Ajouter un certificat secondaire avec le portail
Pour ajouter un autre certificat comme certificat secondaire, vous devez charger le certificat dans un coffre de clés Azure, puis le déployer sur les machines virtuelles du cluster. Pour plus d’informations, consultez [Déployer des certificats sur les machines virtuelles à partir de coffres de clés gérés par les clients](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

1. Pour plus d’informations, consultez [Ajout de certificats à Key Vault](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).
2. Connectez-vous au [Portail Azure](https://portal.azure.com/) , puis accédez à la ressource de cluster à laquelle vous voulez ajouter ce certificat.
3. Sous **PARAMÈTRES**, cliquez sur **Sécurité** pour afficher le panneau Sécurité du cluster.
4. Cliquez sur le bouton **« + Certificat »** sur le panneau pour atteindre le panneau **« Ajouter un certificat »**.
5. Sélectionnez « Empreinte numérique du certificat secondaire » dans la liste déroulante et remplissez l’empreinte numérique du certificat secondaire que vous avez chargé vers le coffre de clés.

> [!NOTE]
> Contrairement au flux de travail de création du cluster, nous ne couvrons pas ici les détails des informations du coffre de clés, car nous partons du principe que, au moment où vous vous trouvez sur ce panneau, vous avez déjà déployé le certificat sur les machines virtuelles et qu’il est déjà disponible dans le magasin de certificats local de l’instance VMSS.
> 
> 

Cliquez sur **Certificat**. Un déploiement est lancé et une barre d’état bleue apparaît dans le panneau Sécurité du cluster.

![Capture d’écran des empreintes de certificat dans le portail][SecurityConfigurations_02]

Une fois ce déploiement terminé, vous pouvez utiliser le certificat principal ou le certificat secondaire pour effectuer des opérations de gestion sur le cluster.

![Capture d’écran du déploiement de certificat en cours][SecurityConfigurations_03]

Voici une capture du panneau Sécurité une fois le déploiement terminé.

![Capture d’écran des empreintes de certificat après le déploiement][SecurityConfigurations_08]

Vous pouvez maintenant utiliser le nouveau certificat que vous avez ajouté pour vous connecter et effectuer des opérations sur le cluster.

> [!NOTE]
> Il n’existe actuellement aucun moyen d’échanger le certificat principal et le certificat secondaire sur le portail, mais cette fonctionnalité est en préparation. Tant qu’il existe un certificat de cluster valide, le cluster fonctionne correctement.
> 
> 

## <a name="add-a-secondary-certificate-and-swap-it-to-be-the-primary-using-resource-manager-powershell"></a>Ajouter un certificat secondaire et en faire le certificat principal à l’aide de Resource Manager PowerShell
Ces étapes partent du principe que vous connaissez le fonctionnement de Resource Manager, que vous avez déployé au moins un cluster Service Fabric à l’aide d’un modèle Resource Manager et que vous disposez du modèle que vous avez utilisé pour configurer le cluster. Il est également supposé que vous maîtrisez l’utilisation de JSON.

> [!NOTE]
> Si vous cherchez un exemple de modèle et de paramètres pour suivre la procédure ou comme point de départ, téléchargez-le dans ce [référentiel-git]. (https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

#### <a name="edit-your-resource-manager-template"></a>Modifier votre modèle Resource Manager
Si vous utilisez l’exemple du [git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) pour suivre la procédure, vous trouverez ces modifications dans l’exemple 5-VM-1-NodeTypes-Secure_Step2.JSON. 5-VM-1-NodeTypes-Secure_Step1.JSON permet de déployer un cluster sécurisé

1. Ouvrez le modèle Resource Manager que vous avez utilisé pour déployer votre cluster.
2. Ajoutez un nouveau paramètre « secCertificateThumbprint » de type « string ». Si vous utilisez le modèle Resource Manager que vous avez téléchargé à partir du portail pendant la création ou à partir des modèles de démarrage rapide, recherchez simplement ce paramètre ; il est normalement déjà défini.  
3. Recherchez la définition de ressource « Microsoft.ServiceFabric/clusters ». Sous Propriétés, vous trouverez la balise JSON « Certificat », qui doit ressembler à l’extrait de code JSON suivant.
   
   ```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
        }
   ``` 
4. Ajoutez une nouvelle balise « thumbprintSecondary » et donnez-lui la valeur « [parameters(’secCertificateThumbprint’)] ».  

La définition de ressource doit maintenant ressembler à l’extrait de code ci-dessous (sans forcément être identique en tout point, en fonction de la source de votre modèle). Comme vous le pouvez le voir ci-dessous, vous spécifiez ici un nouveau certificat comme certificat primaire et vous transformez le certificat primaire actuel en certificat secondaire.  Cela entraîne la substitution de votre certificat actuel par le nouveau certificat en une seule étape de déploiement.

```JSON

      "properties": {
        "certificate": {
            "thumbprint": "[parameters('certificateThumbprint')]",
            "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },

```

#### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Modifier votre fichier de modèle pour refléter les nouveaux paramètres ajoutés précédemment
Si vous utilisez l’exemple du [git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) pour suivre la procédure, vous pouvez commencer à apporter des modifications à l’exemple 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON. 

Modifiez le fichier de paramètres du modèle Resource Manager, ajoutez les nouveaux paramètres du secCertificate, échangez les données du certificat principal actuel avec celles du certificat secondaire et remplacez les données du certificat principal par celles du nouveau certificat. 

```JSON
    "secCertificateThumbprint": {
      "value": "OLD Primary Certificate Thumbprint"
    },
   "secSourceVaultValue": {
      "value": "OLD Primary Certificate Key Vault location"
    },
    "secCertificateUrlValue": {
      "value": "OLD Primary Certificate location in the key vault"
     },
    "certificateThumbprint": {
      "value": "New Certificate Thumbprint"
    },
    "sourceVaultValue": {
      "value": "New Certificate Key Vault location"
    },
    "certificateUrlValue": {
      "value": "New Certificate location in the key vault"
     },

```

### <a name="deploy-the-template-to-azure"></a>Déployer le modèle sur Azure
1. Vous êtes maintenant prêt à déployer votre modèle sur Azure. Ouvrez une invite de commande Azure PS version 1 ou ultérieure.
2. Connectez-vous à votre compte Azure, puis sélectionnez l’abonnement Azure concerné. C’est une étape importante pour ceux qui ont accès à plusieurs abonnements Azure.

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Testez le modèle avant de le déployer. Utilisez le groupe de ressources sur lequel votre cluster est actuellement déployé.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Déployez le modèle sur votre groupe de ressources. Utilisez le groupe de ressources sur lequel votre cluster est actuellement déployé. Exécutez la commande New-AzureRmResourceGroupDeployment. Vous n’avez pas besoin de spécifier le mode, la valeur par défaut étant **incrémentielle**.

> [!NOTE]
> Si vous spécifiez le mode Complet, vous risquez de supprimer par inadvertance des ressources qui ne sont pas dans votre modèle. Par conséquent, ne l’utilisez pas dans ce scénario.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Voici un exemple renseigné de la même commande PowerShell.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

Une fois le déploiement terminé, connectez-vous à votre cluster avec le nouveau certificat et effectuez des requêtes. Si vous en avez la possibilité. Vous pouvez ensuite supprimer l’ancien certificat principal. 

Si vous utilisez un certificat auto-signé, n’oubliez pas de les importer dans votre magasin de certificats local TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Pour référence, voici la commande pour vous connecter à un cluster sécurisé 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Pour référence, voici la commande pour connaître l’intégrité du cluster

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="remove-the-old-certificate-using-the-portal"></a>Supprimer l’ancien certificat avec le portail
Voici le processus de suppression d’un ancien certificat pour que le cluster ne l’utilise plus :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) , puis accédez aux paramètres de sécurité de votre cluster.
2. Cliquez avec le bouton droit sur le certificat que vous souhaitez supprimer
3. Sélectionnez Supprimer et suivez les invites. 

[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png


## <a name="next-steps"></a>Étapes suivantes
Lisez les articles suivants pour plus d’informations sur la gestion des clusters :

* [Processus de mise à niveau du cluster Service Fabric et attentes à votre égard](service-fabric-cluster-upgrade.md)
* [Configurer l’accès en fonction du rôle pour les clients](service-fabric-cluster-security-roles.md)

<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png
[SecurityConfigurations_03]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_03.png
[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png
[SecurityConfigurations_08]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_08.png



<!--HONumber=Nov16_HO3-->


