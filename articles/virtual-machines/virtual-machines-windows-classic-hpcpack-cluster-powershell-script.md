<properties
   pageTitle="Script PowerShell pour déployer des clusters Windows HPC | Microsoft Azure"
   description="Exécuter un script PowerShell pour déployer un cluster Windows HPC Pack dans les services d’infrastructure Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="04/05/2016"
   ms.author="danlep"/>

# Créer un cluster de calcul haute performance (HPC) Windows avec le script de déploiement du HPC Pack IaaS

Exécutez le script PowerShell de déploiement du HPC Pack IaaS sur un ordinateur client pour déployer un cluster HPC complet pour les charges de travail Windows dans les services d’infrastructure Azure (IaaS). Si vous souhaitez déployer un cluster HPC Pack dans Azure pour les charges de travail Linux, consultez [Créer un cluster HPC Linux avec le script de déploiement du HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## Exemples de fichiers de configuration

### Exemple 1

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaines existante. Le cluster possède 1 nœud principal avec des bases de données locales et 12 nœuds de calcul avec l’extension de machine virtuelle BGInfo appliquée. L’installation automatique des mises à jour Windows est désactivée pour toutes les machines virtuelles dans la forêt de domaines. Tous les services cloud sont créés directement dans l’emplacement East Asia. Les nœuds de calcul sont créés dans 3 services cloud et 3 comptes de stockage (c’est-à-dire, _MyHPCCN-0001_ à _MyHPCCN-0005_ dans _MyHPCCNService01_ et _mycnstorage01_ ; _MyHPCCN-0006_ à _MyHPCCN0010_ dans _MyHPCCNService02_ et _mycnstorage02_ ; et _MyHPCCN-0011_ à _MyHPCCN-0012_ dans _MyHPCCNService03_ et _mycnstorage03_). Les nœuds de calcul sont créés à partir d’une image privée existante capturée depuis un nœud de calcul. Le service d’agrandissement et de réduction automatiques est activé avec des intervalles d’agrandissement et de réduction par défaut.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### Exemple 2

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaines existante. Le cluster contient 1 nœud principal, 1 serveur de base de données avec un disque de données de 500 Go, 2 nœuds de répartiteur exécutant le système d’exploitation Windows Server 2012 R2 et 5 nœuds de calcul exécutant le système d’exploitation Windows Server 2012 R2. Le service cloud MyHPCCNService est créé dans le groupe d’affinités *MyIBAffinityGroup*. Tous les autres services cloud sont créés dans le groupe d’affinités *MyAffinityGroup*. L’API REST du planificateur de travaux HPC et le portail web HPC sont activés sur le nœud principal.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```

### Exemple 3

Le fichier de configuration suivant déploie un cluster HPC Pack qui possède un nœud principal avec des bases de données locales et 5 nœuds exécutant le système d’exploitation Windows Server 2008 R2. Tous les services cloud sont créés directement dans l’emplacement East Asia. Le nœud principal agit en tant que contrôleur de domaine de la forêt de domaines.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2008R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### Exemple 4

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaines existante. Le cluster possède 1 nœud principal avec des bases de données locales, 2 modèles de nœud Azure sont créés et 3 nœuds Azure de taille moyenne sont créés pour le modèle de nœud Azure _AzureTemplate1_. Un fichier de script s’exécutera sur le nœud principal après la configuration de ce dernier.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## Résolution de problèmes


* **Erreur « Le réseau virtuel n’existe pas »** : si vous exécutez le script de déploiement du HPC Pack IaaS pour déployer plusieurs clusters dans Azure simultanément sous un même abonnement, un ou plusieurs déploiements peuvent échouer avec l’erreur « Le réseau virtuel *nom\_VNet* n’existe pas ». Si cette erreur se produit, réexécutez le script de déploiement qui a échoué.

* **Problème d’accès à Internet à partir du réseau virtuel Azure** : si vous créez un cluster HPC Pack avec un nouveau contrôleur de domaine en utilisant le script de déploiement, ou si vous promouvez manuellement un nœud principal de machine virtuelle en contrôleur de domaine, vous pouvez rencontrer des problèmes de connexion des machines virtuelles du réseau virtuel Azure à Internet. Cela peut se produire si un serveur DNS redirecteur est automatiquement configuré sur le contrôleur de domaine et si ce serveur DNS redirecteur ne se résout pas correctement.

    Pour contourner ce problème, ouvrez une session sur le contrôleur de domaine et supprimez le paramètre de configuration du redirecteur ou configurez un serveur DNS redirecteur valide. Pour ce faire, dans le Gestionnaire de serveur, cliquez sur **Outils** > **DNS** pour ouvrir le Gestionnaire DNS, puis double-cliquez sur **Redirecteurs**.

* **Problèmes d’accès au réseau RDMA à partir de machines virtuelles de taille A8 ou A9** : si vous ajoutez des machines virtuelles à nœud de calcul ou nœud de répartiteur Windows Server de taille A8 ou A9 à l’aide du script de déploiement, vous pouvez rencontrer des problèmes pour la connexion de ces machines virtuelles au réseau d’application RDMA. Cela peut se produire par exemple si l’extension HpcVmDrivers n’est pas installée correctement lorsque les machines virtuelles de taille A8 ou A9 sont ajoutées au cluster. Par exemple, l’extension peut être bloquée à l’état d’installation.

    Pour contourner ce problème, vérifiez tout d’abord l’état de l’extension dans les machines virtuelles. Si l’extension n’est pas installée correctement, essayez de supprimer les nœuds de cluster HPC, puis ajoutez de nouveau les nœuds. Par exemple, vous pouvez ajouter des machines virtuelles à nœud de calcul en exécutant le script Add-HpcIaaSNode.ps1 sur le nœud principal.
    
## Étapes suivantes

* Essayez d’exécuter une charge de travail test sur le cluster. Pour obtenir un exemple, consultez le [guide de mise en route](https://technet.microsoft.com/library/jj884144) du HPC Pack.

* Pour obtenir un didacticiel qui utilise le script pour créer un cluster et exécuter une charge de travail HPC, consultez [Prise en main d’un cluster HPC Pack dans Azure pour exécuter des charges de travail Excel et SOA](virtual-machines-windows-excel-cluster-hpcpack.md).

* Essayez les outils de HPC Pack pour démarrer, arrêter, ajouter et supprimer des nœuds de calcul à partir d’un cluster que vous créez. Consultez [Gérer des nœuds de calcul dans un cluster HPC Pack dans Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Vous pouvez également utiliser un modèle Azure Resource Manager pour déployer un cluster HPC Pack. Pour obtenir des exemples, consultez [Création d’un cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) et [Création d’un cluster HPC avec une image de nœud de calcul personnalisée](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

<!---HONumber=AcomDC_0406_2016-->