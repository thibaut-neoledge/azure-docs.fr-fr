<properties
   pageTitle="Ajouter, substituer et supprimer des certificats utilisés dans un cluster Service Fabric dans Azure | Microsoft Azure"
   description="Décrit comment charger un certificat de cluster secondaire, puis le substituer à l’ancien certificat principal."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="chackdan"/>

# Ajouter ou supprimer des certificats pour un cluster Service Fabric dans Azure

Il est recommandé de vous familiariser avec la façon dont Service Fabric utilise les certificats X.509. Pour cela, consultez [Scénarios de sécurité du cluster](service-fabric-cluster-security.md). Vous devez comprendre ce qu’est un certificat de cluster et quelle est son utilité avant de passer à la suite.

Service Fabric vous permet de spécifier deux certificats de cluster, un principal et un secondaire, lorsque vous configurez la sécurité par certificat lors de la création du cluster. Reportez-vous à [Création d’un cluster avec le portail](service-fabric-cluster-creation-via-portal.md) ou [création d’un cluster Azure avec Azure Resource Manager](service-fabric-cluster-creation-via-Resource Manager.md) pour plus de détails. Si vous effectuez le déploiement avec Resource Manager et que vous ne spécifiez qu’un seul certificat de cluster, ce dernier est utilisé comme certificat principal. Après la création du cluster, vous pouvez ajouter un certificat en tant que certificat secondaire.

>[AZURE.NOTE] Pour un cluster sécurisé, vous avez toujours besoin d’au moins un certificat (principal ou secondaire) valide (non révoqué ni arrivé à expiration) déployé. Dans le cas contraire, le cluster cesse de fonctionner. 90 jours avant l’expiration de tous les certificats valides, le système génère un suivi d’avertissement et un événement d’avertissement d’intégrité sur le nœud. Actuellement, Service Fabric n’envoie aucun courrier électronique ou notification d’aucune sorte à ce sujet.


## Ajouter un certificat secondaire avec le portail
Pour ajouter un autre certificat comme certificat secondaire, vous devez charger le certificat dans un coffre de clés Azure, puis le déployer sur les machines virtuelles du cluster. Pour plus d’informations, consultez [Déployer des certificats sur les machines virtuelles à partir de coffres de clés gérés par les clients](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

1. Reportez-vous à [Télécharger un certificat X.509 dans le coffre de clés](service-fabric-secure-azure-cluster-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault) pour connaître la procédure.

2. Connectez-vous au [Portail Azure](https://portal.azure.com/), puis accédez à la ressource de cluster à laquelle vous voulez ajouter ce certificat.
3. Sous **PARAMÈTRES**, cliquez sur **Sécurité** pour afficher le panneau Sécurité du cluster.
4. Cliquez sur le bouton **« + Certificat »** sur le panneau pour atteindre le panneau **« Ajouter un certificat »**.
5. Sélectionnez « Empreinte numérique du certificat secondaire » dans la liste déroulante et remplissez l’empreinte numérique du certificat secondaire que vous avez chargé vers le coffre de clés.

>[AZURE.NOTE]
Contrairement au flux de travail de création du cluster, nous ne couvrons pas ici les détails des informations du coffre de clés, car nous partons du principe que, au moment où vous vous trouvez sur ce panneau, vous avez déjà déployé le certificat sur les machines virtuelles et qu’il est déjà disponible dans le magasin de certificats local de l’instance VMSS.

Cliquez sur **Certificat**. Un déploiement est lancé et une barre d’état bleue apparaît dans le panneau Sécurité du cluster.

![Capture d’écran des empreintes de certificat dans le portail][SecurityConfigurations_02]

Une fois ce déploiement terminé, vous pouvez utiliser le certificat principal ou le certificat secondaire pour effectuer des opérations de gestion sur le cluster.

![Capture d’écran du déploiement de certificat en cours][SecurityConfigurations_03]

Voici une capture du panneau Sécurité une fois le déploiement terminé.

![Capture d’écran des empreintes de certificat après le déploiement][SecurityConfigurations_08]


Vous pouvez maintenant utiliser le nouveau certificat que vous avez ajouté pour vous connecter et effectuer des opérations sur le cluster.

>[AZURE.NOTE]
Il n’existe actuellement aucun moyen d’échanger le certificat principal et le certificat secondaire sur le portail, mais cette fonctionnalité est en préparation. Tant qu’il existe un certificat de cluster valide, le cluster fonctionne correctement.

## Ajouter un certificat secondaire et en faire le certificat principal à l’aide de Resource Manager PowerShell

Ces étapes partent du principe que vous connaissez le fonctionnement de Resource Manager, que vous avez déployé au moins un cluster Service Fabric à l’aide d’un modèle Resource Manager, que vous disposez du modèle que vous avez utilisé pour configurer le cluster et que vous maîtrisez l’utilisation de JSON.

>[AZURE.NOTE]
Si vous cherchez un exemple de modèle et de paramètres pour suivre la procédure ou comme point de départ, téléchargez-le dans ce [référentiel-git]. (https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

#### Modifier votre modèle Resource Manager 

Si vous utilisez l’exemple du [référentiel-git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) pour suivre la procédure, vous trouverez ces modifications dans l’exemple 5-VM-1-NodeTypes-Secure\_Step2.JSON. 5-VM-1-NodeTypes-Secure\_Step1.JSON permet de déployer un cluster sécurisé

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

La définition de ressource doit maintenant ressembler à l’extrait de code ci-dessous (sans forcément être identique en tout point, en fonction de la source de votre modèle). Comme vous le pouvez le voir ci-dessous, vous spécifiez ici un nouveau certificat comme certificat primaire et vous transformez le certificat primaire actuel en certificat secondaire. Cela entraîne la substitution de votre certificat actuel par le nouveau certificat en une seule étape de déploiement.

```JSON

      "properties": {
        "certificate": {
            "thumbprint": "[parameters('certificateThumbprint')]",
            "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },

```

#### Modifier votre fichier de modèle pour refléter les nouveaux paramètres ajoutés précédemment

Si vous utilisez l’exemple du [référentiel-git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) pour suivre la procédure, vous pouvez commencer à apporter des modifications à l’exemple 5-VM-1-NodeTypes-Secure.parameters\_Step2.JSON.


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

### Déployer le modèle sur Azure

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

>[AZURE.NOTE]
Si vous spécifiez le mode Complet, vous risquez de supprimer par inadvertance des ressources qui ne sont pas dans votre modèle. Par conséquent, ne l’utilisez pas dans ce scénario.
   

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
 
## Supprimer l’ancien certificat avec le portail
Voici le processus de suppression d’un ancien certificat pour que le cluster ne l’utilise plus :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/), puis accédez aux paramètres de sécurité de votre cluster.
2. Cliquez avec le bouton droit sur le certificat que vous souhaitez supprimer
3. Sélectionnez Supprimer et suivez les invites.

[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png


## Étapes suivantes
Lisez les articles suivants pour plus d’informations sur la gestion des clusters :

- [Processus de mise à niveau du cluster Service Fabric et attentes à votre égard](service-fabric-cluster-upgrade.md)
- [Configurer l’accès en fonction du rôle pour les clients](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png
[SecurityConfigurations_03]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_03.png
[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png
[SecurityConfigurations_08]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_08.png

<!---HONumber=AcomDC_0817_2016-->

