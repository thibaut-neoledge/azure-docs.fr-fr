



Exécutez le script PowerShell de déploiement du HPC Pack IaaS sur un ordinateur client pour déployer un cluster HPC Pack complet dans les services d’infrastructure Azure (IaaS). Le script fournit plusieurs options de déploiement et peut ajouter des nœuds de calcul de cluster exécutant des distributions Linux prises en charge ou des systèmes d’exploitation Windows Server.

Selon votre environnement et vos choix, le script peut créer toute l’infrastructure de clusters, y compris le réseau virtuel Azure, les comptes de stockage, les services cloud, le contrôleur de domaine, les bases de données SQL locales ou distantes, le nœud principal, les nœuds du répartiteur, les nœuds de calcul et les nœuds de service cloud Azure (« nœuds d’extension » ou PaaS). Sinon, le script peut utiliser l’infrastructure Azure existante, puis créer le nœud principal de cluster HPC, les nœuds du répartiteur, les nœuds de calcul et les nœuds d’extension Azure.


Pour des informations générales sur la planification d’un cluster HPC Pack, consultez [Évaluation du produit et planification](https://technet.microsoft.com/library/jj899596.aspx) et [Mise en route](https://technet.microsoft.com/library/jj899590.aspx) dans la bibliothèque TechNet HPC Pack.

>[AZURE.NOTE]Vous pouvez également utiliser un modèle Azure Resource Manager pour déployer un cluster HPC Pack. Pour obtenir un exemple, consultez [Création d’un cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/), [Création d’un cluster HPC avec une image de nœud de calcul personnalisée](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)ou [Création d’un cluster HPC avec des nœuds de calcul Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

## Configuration requise

* **Abonnement Azure** : vous pouvez utiliser un abonnement dans le service Azure Global ou Azure Chine. Vos limites d’abonnement affecteront le nombre et le type de nœuds de cluster que vous pouvez déployer. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes du service](../articles/azure-subscription-service-limits.md).


* **Ordinateur client Windows avec Azure PowerShell 0.8.7 ou ultérieur installé et configuré**. Consultez l’article [Installation et configuration d’Azure PowerShell](../articles/powershell-install-configure.md). Le script s’exécute dans Azure Service Management.


* **Script de déploiement du HPC Pack IaaS** : téléchargez et décompressez la dernière version du script à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Vérifiez la version du script en exécutant `New-HPCIaaSCluster.ps1 –Version`. Cet article est basé sur la version 4.4.0 du script.

* **Fichier de configuration de script** : vous devez créer un fichier XML qui sera utilisé par le script pour configurer le cluster HPC. Pour obtenir plus d’informations et des exemples, consultez les sections appropriées, plus loin dans cet article.


## Syntaxe

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]Vous devez exécuter chacun des scripts en tant qu’administrateur.

### Paramètres

* **ConfigFile** : spécifie le chemin d’accès du fichier de configuration pour décrire le cluster HPC. Pour plus d’informations, consultez [Fichier de configuration](#Configuration-file) dans cette rubrique, ou le fichier Manual.rtf, dans le dossier contenant le script.

* **AdminUserName** : spécifie le nom d’utilisateur. Si la forêt de domaines est créée par le script, cela devient le nom d’utilisateur de l’administrateur local pour toutes les machines virtuelles, ainsi que le nom d’administrateur de domaine. Si la forêt de domaines existe déjà, cela indique l’utilisateur du domaine comme nom d’utilisateur d’administrateur local pour installer HPC Pack.

* **AdminPassword** : spécifie le mot de passe de l’administrateur. S’il n’a pas été spécifié dans la ligne de commande, le script vous invite à entrer le mot de passe.

* **HPCImageName** (facultatif) : spécifie le nom d’image de la machine virtuelle HPC Pack à utiliser pour le déploiement du cluster HPC. Ce doit être une image HPC Pack fournie par Microsoft à partir d’Azure Marketplace. S’il n’a pas été spécifié (recommandé dans la plupart des cas), le script choisit la dernière image HPC Pack publiée.

    >[AZURE.NOTE] Le déploiement échouera si vous ne spécifiez pas d’image HPC Pack valide.

* **LogFile** (facultatif) : spécifie le chemin du fichier journal de déploiement. S’il n’a pas été spécifié, le script crée un fichier journal dans le répertoire temp de l’ordinateur qui exécute le script.

* **Force** (facultatif) : supprime toutes les invites de confirmation.

* **NoCleanOnFailure** (facultatif) : spécifie que les machines virtuelles Azure qui n’ont pas été correctement déployées ne soient pas supprimées. Vous devez supprimer manuellement ces machines virtuelles avant de réexécuter le script pour poursuivre le déploiement, sinon le déploiement peut échouer.

* **PSSessionSkipCACheck** (facultatif) : pour chaque service cloud avec des machines virtuelles déployées par ce script, un certificat auto-signé est automatiquement généré par Azure, et toutes les machines virtuelles du service cloud utilisent ce certificat en tant que certificat Windows Remote Management (WinRM) par défaut. Pour déployer des fonctionnalités HPC dans ces machines virtuelles Azure, le script par défaut installe temporairement ces certificats dans le magasin Ordinateur local\\Autorités de certification racines de confiance de l’ordinateur client pour supprimer l’erreur de sécurité d’autorité de certification non approuvée pendant l’exécution du script ; les certificats sont supprimés quand le script se termine. Si ce paramètre est spécifié, les certificats ne sont pas installés sur l’ordinateur client et l’avertissement de sécurité est supprimé.

    >[AZURE.IMPORTANT] Ce paramètre n’est pas recommandé pour les déploiements de production.

### Exemple

L’exemple suivant crée un cluster HPC Pack à l’aide du fichier de configuration *MyConfigFile.xml* et spécifie les informations d’identification d’administration pour l’installation du cluster.

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### Considérations supplémentaires

* Le script utilise l’image de machine virtuelle HPC Pack dans Azure Marketplace pour créer le nœud principal du cluster. La dernière image est basée sur Windows Server 2012 R2 Datacenter avec HPC Pack 2012 R2 Update 3.

* Le script peut éventuellement activer la soumission de travaux via le portail web HPC Pack ou l’API REST du HPC Pack.

* Le script peut éventuellement exécuter des scripts de pré- et post-configuration personnalisés sur le nœud principal si vous souhaitez installer des logiciels supplémentaires ou configurer d’autres paramètres.


## Fichier de configuration

Le fichier de configuration pour le script de déploiement est un fichier XML. Le fichier de schéma HPCIaaSClusterConfig.xsd se trouve dans le dossier de script de déploiement du HPC Pack IaaS. **IaaSClusterConfig** est l’élément racine du fichier de configuration qui contient les éléments enfants décrits en détail dans le fichier Manual.rtf dans le dossier de script de déploiement. Pour consulter des fichiers d’exemple pour les différents scénarios, consultez [Exemples de fichiers de configuration](#Example-configuration-files) dans cet article.

## Exemples de fichiers de configuration

### Exemple 1

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaines existante. Le cluster possède 1 nœud principal avec des bases de données locales et 12 nœuds de calcul avec l’extension de machine virtuelle BGInfo appliquée. L’installation automatique des mises à jour Windows est désactivée pour toutes les machines virtuelles dans la forêt de domaines. Tous les services cloud sont créés directement dans l’emplacement East Asia. Les nœuds de calcul sont créés dans 3 services cloud et 3 comptes de stockage (c’est-à-dire, _MyHPCCN-0001_ à _MyHPCCN-0005_ dans _MyHPCCNService01_ et _mycnstorage01_ ; _MyHPCCN-0006_ à _MyHPCCN0010_ dans _MyHPCCNService02_ et _mycnstorage02_ ; et _MyHPCCN-0011_ à _MyHPCCN-0012_ dans _MyHPCCNService03_ et _mycnstorage03_). Les nœuds de calcul sont créés à partir d’une image privée existante capturée depuis un nœud de calcul. Le service d’agrandissement et de réduction automatiques est activé avec des intervalles d’agrandissement et de réduction par défaut.

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

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaines existante. Le cluster contient 1 nœud principal, 1 serveur de base de données avec un disque de données de 500 Go, 2 nœuds de répartiteur exécutant le système d’exploitation Windows Server 2012 R2 et 5 nœuds de calcul exécutant le système d’exploitation Windows Server 2012 R2. Le service cloud MyHPCCNService est créé dans le groupe d’affinités *MyIBAffinityGroup*. Tous les autres services cloud sont créés dans le groupe d’affinités *MyAffinityGroup*. L’API REST du planificateur de travaux HPC et le portail web HPC sont activés sur le nœud principal.

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

Le fichier de configuration suivant crée une nouvelle forêt de domaines et le déploiement d’un cluster HPC Pack qui a 1 nœud principal avec des bases de données locales et 20 nœuds de calcul Linux. Tous les services cloud sont créés directement dans l’emplacement East Asia. Les nœuds de calcul Linux sont créés dans 4 services cloud et 4 comptes de stockage (c’est-à-dire _MyLnxCN-0001_ à _MyLnxCN-0005_ dans _MyLnxCNService01_ et _mylnxstorage01_ ; _MyLnxCN-0006_ à _MyLnxCN-0010_ dans _MyLnxCNService02_ et _mylnxstorage02_ ; _MyLnxCN-0011_ à _MyLnxCN-0015_ dans _MyLnxCNService03_ et _mylnxstorage03_ ; et _MyLnxCN-0016_ à _MyLnxCN-0020_ dans _MyLnxCNService04_ et _mylnxstorage04_). Les nœuds de calcul sont créés à partir d’une image Linux OpenLogic CentOS version 7.0.

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


### Exemple 4

Le fichier de configuration suivant déploie un cluster HPC Pack qui possède un nœud principal avec des bases de données locales et 5 nœuds exécutant le système d’exploitation Windows Server 2008 R2. Tous les services cloud sont créés directement dans l’emplacement East Asia. Le nœud principal agit en tant que contrôleur de domaine de la forêt de domaines.

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

### Exemple 5

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaines existante. Le cluster possède 1 nœud principal avec des bases de données locales, 2 modèles de nœud Azure sont créés et 3 nœuds Azure de taille moyenne sont créés pour le modèle de nœud Azure _AzureTemplate1_. Un fichier de script s’exécutera sur le nœud principal après la configuration de ce dernier.

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
## Problèmes connus


* **Erreur « Le réseau virtuel n’existe pas »** : si vous exécutez le script de déploiement du HPC Pack IaaS pour déployer plusieurs clusters dans Azure simultanément sous un même abonnement, un ou plusieurs déploiements peuvent échouer avec l’erreur « Le réseau virtuel *nom\_VNet* n’existe pas ». Si cette erreur se produit, réexécutez le script de déploiement qui a échoué.

* **Problème d’accès à Internet à partir du réseau virtuel Azure** : si vous créez un cluster HPC Pack avec un nouveau contrôleur de domaine en utilisant le script de déploiement, ou si vous promouvez manuellement une machine virtuelle en contrôleur de domaine, vous pouvez rencontrer des problèmes de connexion des machines virtuelles du réseau virtuel Azure à Internet. Cela peut se produire si un serveur DNS redirecteur est automatiquement configuré sur le contrôleur de domaine et si ce serveur DNS redirecteur ne se résout pas correctement.

    Pour contourner ce problème, ouvrez une session sur le contrôleur de domaine et supprimez le paramètre de configuration du redirecteur ou configurez un serveur DNS redirecteur valide. Pour ce faire, dans le Gestionnaire de serveur, cliquez sur **Outils** > **DNS** pour ouvrir le Gestionnaire DNS, puis double-cliquez sur **Redirecteurs**.

* **Problèmes d’accès au réseau RDMA à partir de machines virtuelles de taille A8 ou A9** : si vous ajoutez des machines virtuelles à nœud de calcul ou nœud de répartiteur Windows Server de taille A8 ou A9 à l’aide du script de déploiement, vous pouvez rencontrer des problèmes pour la connexion de ces machines virtuelles au réseau d’application RDMA. Cela peut se produire par exemple si l’extension HpcVmDrivers n’est pas installée correctement lorsque les machines virtuelles de taille A8 ou A9 sont ajoutées au cluster. Par exemple, l’extension peut être bloquée à l’état d’installation.

    Pour contourner ce problème, vérifiez tout d’abord l’état de l’extension dans les machines virtuelles. Si l’extension n’est pas installée correctement, essayez de supprimer les nœuds de cluster HPC, puis ajoutez de nouveau les nœuds. Par exemple, vous pouvez ajouter des machines virtuelles à nœud de calcul en exécutant le script Add-HpcIaaSNode.ps1 sur le nœud principal.

<!---HONumber=AcomDC_0330_2016-->