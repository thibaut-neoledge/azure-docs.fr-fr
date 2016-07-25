<properties
   pageTitle="Script PowerShell pour déployer des clusters HPC Linux | Microsoft Azure"
   description="Exécuter un script PowerShell pour déployer un cluster HPC Pack Linux sur les machines virtuelles Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# Créer un cluster de calcul haute performance (HPC) Linux avec le script de déploiement du HPC Pack IaaS

Exécutez le script PowerShell de déploiement du HPC Pack IaaS pour déployer un cluster HPC complet pour les charges de travail Linux sur les machines virtuelles Azure. Le cluster se compose d’un nœud principal joint à Active Directory, exécutant Windows Server et Microsoft HPC Pack, et de nœuds de calcul qui exécutent l’une des distributions Linux prises en charge par HPC Pack. Si vous souhaitez déployer un cluster HPC Pack dans Azure pour les charges de travail Windows, consultez [Créer un cluster HPC Windows avec le script de déploiement du HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). Vous pouvez également utiliser un modèle Azure Resource Manager pour déployer un cluster HPC Pack. Pour obtenir un exemple, consultez [Création d’un cluster HPC avec des nœuds de calcul Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## Exemple de fichier de configuration

Le fichier de configuration suivant crée un contrôleur de domaine et une forêt de domaines, et déploie un cluster HPC Pack composé de 1 nœud principal avec des bases de données locales et de 10 nœuds de calcul Linux. Tous les services cloud sont créés directement dans l’emplacement East Asia. Les nœuds de calcul Linux sont créés dans 2 services cloud et 2 comptes de stockage (c’est-à-dire _MyLnxCN-0001_ à _MyLnxCN-0005_ dans _MyLnxCNService01_ et _mylnxstorage01_, et _MyLnxCN-0006_ à _MyLnxCN-0010_ dans _MyLnxCNService02_ et _mylnxstorage02_). Les nœuds de calcul sont créés à partir d’une image Linux OpenLogic CentOS version 7.0.

Utilisez vos propres valeurs pour votre nom d’abonnement et les noms de compte et de service.

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
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## Résolution de problèmes

* **Erreur « Le réseau virtuel n’existe pas »** : si vous exécutez le script de déploiement du HPC Pack IaaS pour déployer plusieurs clusters dans Azure simultanément sous un même abonnement, un ou plusieurs déploiements peuvent échouer avec l’erreur « Le réseau virtuel *nom\_VNet* n’existe pas ». Si cette erreur se produit, réexécutez le script de déploiement qui a échoué.

* **Problème d’accès à Internet à partir du réseau virtuel Azure** : si vous créez un cluster HPC Pack avec un nouveau contrôleur de domaine en utilisant le script de déploiement, ou si vous promouvez manuellement une machine virtuelle de nœud principal en contrôleur de domaine, vous pouvez rencontrer des problèmes de connexion des machines virtuelles du réseau virtuel Azure à Internet. Cela peut se produire si un serveur DNS redirecteur est automatiquement configuré sur le contrôleur de domaine et si ce serveur DNS redirecteur ne se résout pas correctement.

    Pour contourner ce problème, ouvrez une session sur le contrôleur de domaine et supprimez le paramètre de configuration du redirecteur ou configurez un serveur DNS redirecteur valide. Pour ce faire, dans le Gestionnaire de serveur, cliquez sur **Outils** > **DNS** pour ouvrir le Gestionnaire DNS, puis double-cliquez sur **Redirecteurs**.
    
## Étapes suivantes

* Consultez la page [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) pour plus d’informations sur les distributions Linux prises en charge, le déplacement des données et la soumission de tâches à un cluster HPC Pack avec des nœuds de calcul Linux.
* Pour accéder à des didacticiels qui utilisent le script pour créer un cluster et exécuter une charge de travail HPC Linux, consultez :
    * [Exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
    * [Exécution d’OpenFOAM avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
    * [Exécution de STAR-CCM+ avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

<!---HONumber=AcomDC_0713_2016-->