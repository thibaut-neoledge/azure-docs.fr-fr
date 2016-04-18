<properties
   pageTitle="Script PowerShell pour déployer des clusters HPC Linux | Microsoft Azure"
   description="Exécuter un script PowerShell pour déployer un cluster HPC Pack Linux dans les services d’infrastructure Azure"
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
   ms.date="04/05/2016"
   ms.author="danlep"/>

# Créer un cluster de calcul haute performance (HPC) Linux avec le script de déploiement du HPC Pack IaaS

Exécutez le script PowerShell de déploiement du HPC Pack IaaS sur un ordinateur client pour déployer un cluster HPC complet pour les charges de travail Linux dans les services d’infrastructure Azure (IaaS). Si vous souhaitez déployer un cluster HPC Pack dans Azure pour les charges de travail Windows, consultez [Créer un cluster HPC Windows avec le script de déploiement du HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## Exemples de fichiers de configuration

### Exemple 1

Le fichier de configuration suivant crée une nouvelle forêt de domaines et déploie un cluster HPC Pack qui a 1 nœud principal avec des bases de données locales et 20 nœuds de calcul Linux. Tous les services cloud sont créés directement dans l’emplacement East Asia. Les nœuds de calcul Linux sont créés dans 4 services cloud et 4 comptes de stockage (c’est-à-dire _MyLnxCN-0001_ à _MyLnxCN-0005_ dans _MyLnxCNService01_ et _mylnxstorage01_ ; _MyLnxCN-0006_ à _MyLnxCN-0010_ dans _MyLnxCNService02_ et _mylnxstorage02_ ; _MyLnxCN-0011_ à _MyLnxCN-0015_ dans _MyLnxCNService03_ et _mylnxstorage03_ ; et _MyLnxCN-0016_ à _MyLnxCN-0020_ dans _MyLnxCNService04_ et _mylnxstorage04_). Les nœuds de calcul sont créés à partir d’une image Linux OpenLogic CentOS version 7.0.

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
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## Résolution de problèmes

* **Erreur « Le réseau virtuel n’existe pas »** : si vous exécutez le script de déploiement du HPC Pack IaaS pour déployer plusieurs clusters dans Azure simultanément sous un même abonnement, un ou plusieurs déploiements peuvent échouer avec l’erreur « Le réseau virtuel *nom\_VNet* n’existe pas ». Si cette erreur se produit, réexécutez le script de déploiement qui a échoué.

* **Problème d’accès à Internet à partir du réseau virtuel Azure** : si vous créez un cluster HPC Pack avec un nouveau contrôleur de domaine en utilisant le script de déploiement, ou si vous promouvez manuellement un nœud principal de machine virtuelle en contrôleur de domaine, vous pouvez rencontrer des problèmes de connexion des machines virtuelles du réseau virtuel Azure à Internet. Cela peut se produire si un serveur DNS redirecteur est automatiquement configuré sur le contrôleur de domaine et si ce serveur DNS redirecteur ne se résout pas correctement.

    Pour contourner ce problème, ouvrez une session sur le contrôleur de domaine et supprimez le paramètre de configuration du redirecteur ou configurez un serveur DNS redirecteur valide. Pour ce faire, dans le Gestionnaire de serveur, cliquez sur **Outils** > **DNS** pour ouvrir le Gestionnaire DNS, puis double-cliquez sur **Redirecteurs**.
    
## Étapes suivantes

* Pour obtenir un didacticiel qui utilise le script pour créer un cluster et exécuter une charge de travail HPC Linux, consultez [Exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md) ou [Exécution d’OpenFOAM avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

* Vous pouvez également utiliser un modèle Azure Resource Manager pour déployer un cluster HPC Pack. Pour obtenir un exemple, consultez [Création d’un cluster HPC avec des nœuds de calcul Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

<!---HONumber=AcomDC_0406_2016-->