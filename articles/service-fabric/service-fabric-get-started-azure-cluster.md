---
title: Configurer un cluster Azure Service Fabric | Microsoft Docs
description: "Ce guide de démarrage rapide vous aide à créer un cluster Service Fabric Windows ou Linux sur Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: ryanwi
ms.openlocfilehash: caf76bb739fa92982c511c8e3e6c6aaf2bf9d6c1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Créer votre premier cluster Service Fabric sur Azure
Un [cluster Azure Service Fabric](service-fabric-deploy-anywhere.md) est un groupe de machines virtuelles ou physiques connectées au réseau, dans lequel vos microservices sont déployés et gérés. Ce guide de démarrage rapide vous aide à créer en quelques minutes seulement un cluster à cinq nœuds s’exécutant sur Windows ou Linux, via [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) ou le [portail Azure](http://portal.azure.com). Vous pouvez également utiliser l’interface de ligne de commande Azure (CLI) pour cette tâche.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Connectez-vous au portail Azure à l’adresse [http://portal.azure.com](http://portal.azure.com).

### <a name="create-the-cluster"></a>Création du cluster

1. Dans l’angle supérieur gauche du portail Azure, sélectionnez **Nouveau**.
2. Recherchez **Service Fabric**, puis sélectionnez **Cluster Service Fabric** dans les résultats obtenus. Sélectionnez ensuite **Créer**.
3. Remplissez le formulaire Service Fabric **de base**. Sous **Système d’exploitation**, sélectionnez la version de Windows ou de Linux sur laquelle vous voulez exécuter les nœuds du cluster. Le nom d’utilisateur et le mot de passe que vous avez entrés vous serviront pour vous connecter à la machine virtuelle. Pour **Groupe de ressources** créez-en un nouveau. Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont créées et gérées collectivement. Lorsque vous avez terminé, sélectionnez **OK**.

    ![Capture d’écran du résultat de configuration du cluster][cluster-setup-basics]

4. Remplissez le formulaire de **configuration de cluster**. Dans **Nombre de type de nœud**, entrez **1**.

5. Sélectionnez **Type de nœud 1 (principal)**, puis remplissez le formulaire de **configuration du type de nœud**. Entrez un nom de type de nœud, puis définissez le [Niveau de durabilité](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) sur **Bronze**. Sélectionnez ensuite une taille de machine virtuelle.

    Les types de nœud définissent la taille de la machine virtuelle, le nombre de machines virtuelles, les points de terminaison personnalisés et d’autres paramètres pour les machines virtuelles de ce type. Chaque type de nœud défini est configuré comme un groupe de machines virtuelles identiques distinct, utilisé pour déployer et gérer les machines virtuelles comme un ensemble. Chaque type de nœud peut faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité. Le premier type de nœud, ou type de nœud principal, correspond à celui où les services système de Service Fabric sont hébergés. Ce type de nœud doit inclure au moins cinq machines virtuelles.

    Pour un déploiement de production, la [planification de la capacité](service-fabric-cluster-capacity.md) est une étape importante. Pour ce guide de démarrage rapide, toutefois, vous n’exécutez pas d’applications. Vous pouvez donc sélectionner la taille de machine virtuelle *DS1_v2 Standard*. Sélectionnez le [niveau de fiabilité](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) **Silver** et spécifiez une capacité de groupe de machines virtuelles identiques initiale de 5.  

    Les points de terminaison personnalisés ouvrent des ports Azure Load Balancer, afin que vous puissiez vous connecter avec des applications exécutées sur le cluster.  Entrez **80, 8172** pour ouvrir les ports 80 et 8172.

    Ne cochez pas la case **Configurer les paramètres avancés**, qui est utilisée pour personnaliser les points de terminaison de gestion TCP/HTTP, les plages de ports des applications, les [contraintes de placement](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) et les [propriétés de capacité](service-fabric-cluster-resource-manager-metrics.md).    
    
    ![Capture d’écran de la configuration du type de nœud][node-type-config]

    Sélectionnez **OK**.

6. Dans le formulaire de **configuration de cluster**, définissez **Diagnostics** sur **Activé**. Pour ce guide de démarrage rapide, vous n’avez pas besoin d’entrer de propriétés de [paramètres Fabric](service-fabric-cluster-fabric-settings.md).  Sous **Version Fabric**, sélectionnez le mode de mise à niveau **automatique** afin que Microsoft mette automatiquement à jour la version du code de structure exécutant le cluster.  Définissez le mode sur **Manuel**si vous souhaitez [choisir une version prise en charge](service-fabric-cluster-upgrade.md) vers laquelle effectuer la mise à niveau.     

    Sélectionnez **OK**.

7. Remplissez le formulaire de **sécurité**. Pour ce guide de démarrage rapide, sélectionnez **Non sécurisé**. Notez qu’en règle générale, vous devez créer un cluster sécurisé pour les charges de travail de production. Toute personne peut se connecter de façon anonyme à un cluster non sécurisé et effectuer des opérations de gestion.  

   Service Fabric utilise des certificats à des fins d’authentification et de chiffrement pour sécuriser les divers aspects d’un cluster et de ses applications. Pour plus d’informations, consultez [Scénarios de sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md). Pour activer l’authentification utilisateur à l’aide d’Azure Active Directory, ou pour configurer des certificats pour la sécurité de l’application, consultez [Créer un cluster à partir d’un modèle Resource Manager](service-fabric-cluster-creation-via-arm.md).

    Sélectionnez **OK**.

8. Passez en revue le résumé. Si vous souhaitez télécharger un modèle Azure Resource Manager généré à partir des paramètres que vous avez entrés, sélectionnez **Télécharger le modèle et les paramètres**. Sélectionnez **Créer** pour créer le cluster.

    Vous pouvez voir la progression de la création dans les notifications. (Sélectionnez l’icône représentant une cloche près de la barre d’état dans l’angle supérieur droit de votre écran.) Si vous avez sélectionné **Épingler au tableau d’accueil** pendant la création du cluster, **Déploiement du cluster Service Fabric** apparaît épinglé au **tableau d’accueil**.

### <a name="connect-to-the-cluster-by-using-powershell"></a>Se connecter à un cluster à l’aide de PowerShell
Vérifiez que le cluster est en cours d’exécution en vous connectant via PowerShell. Le module Service Fabric PowerShell est installé avec le [Kit de développement logiciel (SDK) Service Fabric](service-fabric-get-started.md). L’applet de commande [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) établit une connexion au cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
Pour obtenir des exemples de connexion à un cluster, consultez [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md) . Lorsque vous êtes connecté au cluster, utilisez l’applet de commande [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) pour afficher une liste des nœuds du cluster et les informations d’état pour chaque nœud. **HealthState** doit être à l’état *OK* pour chaque nœud.

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>Supprimer le cluster
Un cluster Service Fabric est composé d’autres ressources Azure en plus de la ressource de cluster elle-même. Pour supprimer complètement un cluster Service Fabric, vous devez également supprimer toutes les ressources qui le composent. Le plus simple pour supprimer le cluster et toutes les ressources qu’il consomme consiste à supprimer le groupe de ressources. Pour découvrir d’autres méthodes permettant de supprimer un cluster ou certaines des ressources d’un groupe de ressources (mais pas toutes), consultez [Supprimer un cluster](service-fabric-cluster-delete.md).

Supprimer un groupe de ressources dans le portail Azure :
1. Recherchez le cluster Service Fabric que vous souhaitez supprimer.
2. Sur la page des éléments essentiels du cluster, sélectionnez le nom du **groupe de ressources**.
3. Sur la page des **éléments essentiels du groupe de ressources**, sélectionnez **Supprimer le groupe de ressources**. Suivez ensuite les instructions de cette page pour procéder à la suppression du groupe de ressources.
    ![Capture d’écran de la page des éléments essentiels du groupe de ressources, avec Supprimer le groupe de ressources mis en surbrillance][cluster-delete]


## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell
Une autre méthode pour créer le cluster consiste à utiliser PowerShell. Voici comment procéder :

1. Téléchargez le [module Azure PowerShell version 4.0 ou ultérieure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) sur votre ordinateur.

2. Exécutez l’applet de commande [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster) pour créer un cluster Service Fabric à cinq nœuds, sécurisé par un certificat X.509. La commande crée un certificat auto-signé et le charge vers un nouveau coffre de clés. Le certificat est également copié dans un répertoire local. Définissez le paramètre *-OS* pour choisir la version de Windows ou Linux qui s’exécute sur les nœuds de cluster. Personnalisez les paramètres selon vos besoins. 

    ```powershell
    #Provide the subscription Id
    $subscriptionId = 'yourSubscriptionId'

    # Certificate variables.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $certfolder="c:\mycertificates\"

    # Variables for VM admin.
    $adminuser="vmadmin"
    $adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

    # Variables for common values
    $clusterloc="SouthCentralUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=5 

    # Set the context to the subscription ID where the cluster will be created
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Create the Service Fabric cluster.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
    ```

    L’exécution de la commande peut durer entre 10 et 30 minutes. Le résultat contient des informations sur le certificat, le coffre de clés dans lequel il a été chargé et le dossier local dans lequel le certificat est copié.     

3. Copiez l’intégralité du résultat et enregistrez-le dans un fichier texte (vous en aurez besoin ultérieurement). Prenez note des informations suivantes dans le résultat : 

    - CertificateSavedLocalPath
    - CertificateThumbprint
    - ManagementEndpoint
    - ClientConnectionEndpointPort

### <a name="install-the-certificate-on-your-local-machine"></a>Installation du certificat sur un ordinateur local
  
Pour vous connecter au cluster, installez le certificat dans le magasin personnel de l’utilisateur actuel. 

Exécutez la commande suivante :

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\<certificatename>.pfx `
        -Password $certpwd
```

Vous êtes maintenant prêt à vous connecter à votre cluster sécurisé.

### <a name="connect-to-a-secure-cluster"></a>Se connecter à un cluster sécurisé 

Exécutez l’applet de commande [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) pour vous connecter à un cluster sécurisé. Les détails du certificat doivent correspondre à un certificat utilisé pour configurer le cluster. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <ManagementEndpoint>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <CertificateThumbprint> `
          -FindType FindByThumbprint -FindValue <CertificateThumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

L’exemple suivant vous indique paramètres d’exemple : 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Exécutez la commande suivante pour vérifier que vous êtes connecté et que le cluster est intègre.

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>Supprimer le cluster
Un cluster est composé d’autres ressources Azure en plus de la ressource de cluster elle-même. Le plus simple pour supprimer le cluster et toutes les ressources qu’il consomme consiste à supprimer le groupe de ressources. 

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```
## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure
Une autre méthode pour créer le cluster consiste à utiliser l’interface de ligne de commande (CLI). Voici comment procéder :

1. Installez [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) sur votre ordinateur.
2. Connectez-vous à Azure et sélectionnez l’abonnement pour lequel vous souhaitez créer le cluster.
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. Exécutez la commande [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) pour créer un cluster Service Fabric à cinq nœuds, sécurisé par un certificat X.509. La commande crée un certificat auto-signé et le charge vers un nouveau coffre de clés. Le certificat est également copié dans un répertoire local. Définissez le paramètre *-os* pour choisir la version de Windows ou Linux qui s’exécute sur les nœuds de cluster. Personnalisez les paramètres selon vos besoins.

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster. The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>Connexion au cluster
Exécutez la commande de l’interface de ligne de commande suivante pour vous connecter au cluster à l’aide du certificat.  Lorsque vous utilisez un certificat client pour l’authentification, vérifiez que les détails du certificat correspondent à un certificat déployé sur les nœuds de cluster. Utilisez l’option `--no-verify` pour un certificat auto-signé.

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

Exécutez la commande suivante pour vérifier que vous êtes connecté et que le cluster est intègre.

```azurecli
az sf cluster health
```

### <a name="connect-to-the-nodes-directly"></a>Se connecter directement aux nœuds 

Pour vous connecter aux nœuds d’un cluster Linux, vous pouvez utiliser la commande SSH (Secure Shell). Spécifiez un numéro de port à partir de 3389. Par exemple, pour le cluster à cinq nœuds créé précédemment, les commandes seraient les suivantes :
```bash
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3389
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3390
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3391
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3392
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3393
```

### <a name="remove-the-cluster"></a>Supprimer le cluster
Un cluster est composé d’autres ressources Azure en plus de la ressource de cluster elle-même. Le plus simple pour supprimer le cluster et toutes les ressources qu’il consomme consiste à supprimer le groupe de ressources. 

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez configuré un cluster de développement, essayez ce qui suit :
* [Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Supprimer un cluster](service-fabric-cluster-delete.md) 
* [Déployer des applications à l’aide de PowerShell](service-fabric-deploy-remove-applications.md)
* [Déployer des applications à l’aide de l’interface de ligne de commande](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
