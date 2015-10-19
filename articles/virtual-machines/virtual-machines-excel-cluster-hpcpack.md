<properties
 pageTitle="Cluster HPC Pack pour Excel et SOA | Microsoft Azure"
 description="Prise en main d'un cluster HPC Pack pour exécuter des charges de travail Excel et SOA, à l'aide du modèle de déploiement Resource Manager."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager"/>

<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/18/2015"
 ms.author="danlep"/>

# Prise en main d’un cluster HPC Pack dans Azure pour exécuter des charges de travail Excel et SOA

Cet article vous montre comment déployer un cluster HPC Pack dans les services d'infrastructure Azure (IaaS) à l'aide d'un modèle de démarrage rapide Azure ou d’un script de déploiement Azure PowerShell. Vous utiliserez des images de machine virtuelle Azure Marketplace conçues pour exécuter des charges de travail d’architecture orientée services (SOA) ou Microsoft Excel avec HPC Pack. Vous pouvez utiliser le cluster pour exécuter des services Excel HPC et SOA simples à partir d'un ordinateur client local. Les services Excel HPC incluent le déchargement de classeurs Excel et les fonctions Excel définies par l'utilisateur (UDF).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création de ressources avec le modèle de déploiement Resource Manager.

Le diagramme général suivant montre le cluster HPC Pack que vous allez créer.

![Cluster HPC avec des nœuds exécutant des charges de travail Excel][scenario]

## Composants requis

* **Ordinateur client** : vous aurez besoin d'un ordinateur client Windows pour exécuter le script de déploiement de cluster Azure PowerShell (si vous choisissez cette méthode de déploiement) et pour soumettre des exemples de tâches Excel et SOA au cluster.

* **Abonnement Azure** : si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

* **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs, en particulier si vous choisissez de déployer plusieurs nœuds de cluster avec des tailles de machines virtuelles multiprocesseurs. Si vous utilisez un modèle de démarrage rapide Azure, sachez que le quota de cœurs dans le Gestionnaire de ressources dépend de la région Azure et que vous devrez peut-être l’augmenter dans une région spécifique. Consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md). Pour augmenter un quota, [ouvrez une demande de support client en ligne gratuitement](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).


## Étape 1. Configuration d’un cluster HPC Pack dans Azure

Nous allons vous montrer deux méthodes de configuration du cluster : tout d'abord à l'aide d'un modèle de démarrage rapide Azure et du portail Azure en version préliminaire, et, deuxièmement, à l'aide d'un script de déploiement Azure PowerShell.


### Utilisation d’un modèle de démarrage rapide
Utilisez un modèle de démarrage rapide Azure pour déployer rapidement et facilement un cluster HPC Pack dans le portail Azure en version préliminaire. Lorsque vous ouvrez le modèle dans le portail, vous obtenez une interface utilisateur simple vous permettant de saisir les paramètres pour votre cluster. Voici la procédure à suivre.

1. Consultez [Create HPC Cluster template page on GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Si vous le souhaitez, examinez les informations sur le modèle et le code source.

2. Cliquez sur **Déployer vers Azure** pour lancer un déploiement avec le modèle dans le portail Azure en version préliminaire.

    ![Modèle de déploiement vers Azure][github]

3. Dans le portail, procédez comme suit pour saisir les paramètres du modèle de cluster HPC.

    a. Sur la page **Modifier le modèle**, cliquez sur **Enregistrer**.

    ![Enregistrement du modèle][template]

    b. Sur la page **Paramètres**, saisissez les valeurs des paramètres du modèle. (Cliquez sur l'icône en regard de chaque paramètre pour obtenir de l'aide.) Des exemples de valeurs sont affichés dans l'écran suivant. Cet exemple crée un nouveau cluster HPC Pack nommé *hpc01* dans le domaine *hpc.local*, constitué d'un nœud principal et de deux nœuds de calcul. Les nœuds de calcul sont créés à partir d'une image de machine virtuelle HPC Pack, comprenant Microsoft Excel.

    ![Saisie des paramètres][parameters]

    >[AZURE.NOTE]La machine virtuelle du nœud principal est créée automatiquement à partir de la [dernière image Marketplace](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) de HPC Pack 2012 R2 sur Windows Server 2012 R2. Actuellement, l'image repose sur HPC Pack 2012 R2 Update 2.
    >
    >Les machines virtuelles du nœud de calcul sont créées à partir de la dernière image de la famille de nœuds de calcul sélectionnée. Sélectionnez l'option **ComputeNode** pour l'image de calcul à des fins générales HPC Pack 2012 R2 Update 2 la plus récente. Sélectionnez l'option **ComputeNodeWithExcel** pour la dernière image de nœud de calcul HPC Pack comprenant une version d'évaluation de Microsoft Excel Professional Plus 2013. Si vous souhaitez déployer un cluster pour les sessions SOA générales ou pour le déchargement Excel UDF, choisissez l'option **ComputeNode** (sans Excel installé).
    >
    >En cas d'utilisation de **ComputeNodeWithExcel** pour les charges de travail de production, vous devez fournir une licence Excel valide pour activer Excel sur les nœuds de calcul. Dans le cas contraire, la version d'évaluation de Microsoft Excel pourrait expirer dans les 30 jours et l'exécution du classeur Excel échouerait en permanence avec l’exception COMExeption (0x800AC472). Dans ce cas, vous pouvez connecter le nœud principal à clusrun « % ProgramFiles(x86)%\\Microsoft Office\\Office15\\OSPPREARM.exe » sur tous les nœuds de calcul Excel via la console HPC Cluster Manager pour rallonger la période d’évaluation d’Excel de 30 jours. Le nombre de renouvellements de la période de gratuité est de 2, ensuite vous devrez peut-être fournir une licence Excel valide.

    c. Choisissez l’abonnement.

    d. Créez un groupe de ressources pour le cluster, tel que *hpc01RG*.

    e. Choisissez un emplacement pour le groupe de ressources, comme l’Est des États-Unis.

    f. Lisez les conditions de la page **Mentions légales**. Si vous les acceptez, cliquez sur **Acheter**.

    g. Lorsque vous avez terminé de définir les valeurs du modèle, cliquez sur **Créer**.

    ![Création du cluster][create]

3.	Lorsque le déploiement est terminé (cela dure environ 30 minutes généralement), exportez le fichier de certificat de cluster depuis le nœud principal du cluster. Par la suite, ce certificat public sera importé sur l'ordinateur client pour fournir l'authentification côté serveur pour une liaison HTTP sécurisée.

    a. Connectez-vous au nœud principal par le Bureau à distance à partir du portail Azure en version préliminaire.

     ![Connexion au nœud principal][connect]

    b. Utilisez les procédures standard pour exporter le certificat du nœud principal à l’aide du Gestionnaire de certificat (situé sous Cert:\\LocalMachine\\My) sans la clé privée. Dans cet exemple, exportez *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Exportation du certificat][cert]

### Utilisation du script de déploiement du HPC Pack IaaS

Le script de déploiement du HPC Pack IaaS fournit une autre façon polyvalente de déployer un cluster HPC Pack. Il s'exécute en mode de gestion des services Azure (ASM), tandis que le modèle s'exécute en mode Azure Resource Manager (ARM). Le script est compatible avec un abonnement dans le service Azure Global ou Azure Chine.

**Autres composants requis**

* **Azure PowerShell** : [installez et configurez Azure PowerShell](../powershell-install-configure.md) (version 0.8.10 ou ultérieure) sur votre ordinateur client.

* **Script de déploiement IaaS de HPC Pack** : téléchargez et décompressez la dernière version du script à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Vérifiez la version du script en exécutant `New-HPCIaaSCluster.ps1 –Version`. Cet article est basé sur la version 4.4.0 ou ultérieure du script.

**Création du fichier de configuration**

 Le script de déploiement de HPC Pack IaaS utilise un fichier de configuration XML comme entrée, qui décrit l’infrastructure du cluster HPC. Pour déployer un cluster constitué d’un nœud principal et de 18 nœuds de calcul créés depuis l’image de nœuds de calcul comprenant Microsoft Excel, remplacez les valeurs par celles convenant à votre environnement dans l’exemple de fichier de configuration suivant. Pour plus d'informations sur le fichier de configuration, consultez le fichier Manual.rtf dans le dossier de script et la rubrique [Créer un cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
<PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName HPCPackInstalled="true">96316178b0644ae08bc4e037635ce104__HPC-Pack-2012R2-Update2-CN-Excel-4.4.4868.0-WS2012R2-ENU</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Notes relatives au fichier de configuration**

* Le **VMName** du nœud principal doit être exactement identique à **ServiceName**. Autrement, l'exécution de la tâche SOA échouera.

* Veillez à spécifier **EnableWebPortal** afin que le certificat du nœud principal soit généré et exporté.

* Le script PowerShell de post-configuration PostConfig.ps1 configure certains paramètres sur le nœud principal, comme la définition de la chaîne de connexion de stockage Azure, la suppression du rôle de nœud de calcul du nœud principal et la mise en ligne de tous les nœuds lors de leur déploiement. Voici un exemple de script.

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Exécutez le script**

1. Ouvrez la console PowerShell sur l’ordinateur client en tant qu’administrateur.

2. Accédez au dossier de script (E:\\IaaSClusterScript dans cet exemple).

    ```
    cd E:\IaaSClusterScript
```

4. Exécutez la commande suivante pour déployer le cluster HPC Pack. Cet exemple part du principe que le fichier de configuration se trouve dans E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

Le script de déploiement de HPC Pack s'exécute pendant un certain temps. Le script exporte et télécharge le certificat de cluster et l'enregistre dans le dossier Documents de l'utilisateur actuel sur l'ordinateur client. Le script génère un message similaire à celui ci-dessous. Vous allez maintenant importer le certificat dans le magasin de certificats approprié.

```
You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
 C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_2015070716
2011.cer
```

## Étape 2. Déchargement de classeurs Excel et exécution d’UDF à partir d'un client local

### Déchargement de classeurs Excel

Suivez ces étapes pour décharger un classeur Excel à exécuter sur le cluster HPC Pack dans Azure. Pour ce faire, Excel 2010 ou 2013 doit déjà être installé sur l'ordinateur client.

1. Utilisez une des méthodes de l'étape 1 pour déployer un cluster HPC Pack avec l’image de nœud de calcul Excel. Obtenez le fichier de certificat de cluster (.cer), le nom d'utilisateur et le mot de passe du cluster.

2. Sur l'ordinateur client, importez le certificat de cluster sous Cert:\\CurrentUser\\Root.

3. Assurez-vous qu'Excel est installé. Créez un fichier Excel.exe.config avec le contenu suivant dans le même dossier qu’Excel.exe sur l'ordinateur client. Cela garantit que le complément HPC Pack 2012 R2 Excel COM et la bibliothèque de stockage Azure seront chargés avec succès. Notez que le « href » ci-dessous doit pointer vers « %CCP\_HOME%Bin\\Microsoft.WindowsAzure.Storage.dll » sur la machine cliente.

    ```
<?xml version="1.0"?>
<configuration>
    <startup useLegacyV2RuntimeActivationPolicy="true">
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
    </startup>
    <runtime>
        <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
            <dependentAssembly>
                <assemblyIdentity name="Microsoft.WindowsAzure.Storage"  culture="neutral" publicKeyToken="31bf3856ad364e35"/>
                <codeBase version="4.3.0.0" href="C:\Program Files\Microsoft HPC Pack 2012\Bin\Microsoft.WindowsAzure.Storage.dll"/>
            </dependentAssembly>
        </assemblyBinding>
    </runtime>
</configuration>
```
4.	Téléchargez la version complète de l'[installation HPC Pack 2012 R2 Update 2](http://www.microsoft.com/download/details.aspx?id=47755) et installez le client HPC Pack, ou téléchargez et installez les [utilitaires clients HPC Pack 2012 R2 Update 2](https://www.microsoft.com/download/details.aspx?id=47754) et le redistributable Visual C++ 2010 adapté à votre ordinateur ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.	Dans cet exemple, nous utilisons un exemple de classeur Excel nommé ConvertiblePricing\_Complete.xlsb, qui peut être téléchargé [ici](https://www.microsoft.com/fr-fr/download/details.aspx?id=2939).

6.	Copiez le classeur Excel dans un dossier de travail comme D:\\Excel\\Run.

7.	Ouvrez le classeur Excel. Sur le ruban **Développer**, cliquez sur **Compléments COM** et confirmez que le complément COM d'Excel HPC Pack est chargé avec succès, comme indiqué dans l'écran suivant.

    ![Complément Excel pour HPC Pack][addin]

8.	Modifiez la macro VBA HPCControlMacros dans Excel en modifiant les lignes commentées, comme illustré dans le script suivant. Remplacez les valeurs par celles appropriées pour votre environnement.

    ![Macro Excel pour HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.	Copiez le livre de travail Excel dans un répertoire de téléchargement comme D:\\Excel\\Upload, tel que spécifié dans la constante HPC\_DependsFiles dans la macro VBA.

10.	Cliquez sur le bouton **Cluster** sur la feuille de calcul pour exécuter le classeur sur le cluster Azure IaaS.

### Exécution d’UDF Excel

Pour exécuter des UDF Excel, suivez les étapes 1 à 3 précédentes pour configurer l'ordinateur client. Pour des UDF Excel, vous n'avez pas besoin de disposer de l'application Excel installée sur les nœuds de calcul, vous pouvez donc choisir une image de nœud de calcul normale à l'étape 1 plutôt que l'image de nœud de calcul Excel.

>[AZURE.NOTE]Il existe une limite de 34 caractères dans la boîte de dialogue de connecteur de cluster dans Excel 2010 et 2013. Si le nom de cluster complet est plus long, par exemple hpcexcelhn01.southeastasia.cloudapp.azure.com, il ne rentre pas dans la boîte de dialogue. La solution de contournement consiste à appliquer l'Update 2 KB3085833 de QFE (télécharger [ici](http://www.microsoft.com/fr-fr/download/details.aspx?id=48725)) pour l'API de session SOA sur l'ordinateur client, puis à définir une variable à l'échelle de l'ordinateur, par exemple *CCP\_IAASHN* avec la valeur du nom du cluster long et à entrer *% CCP\_IAASHN %* dans la boîte de dialogue comme nom de nœud principal du cluster.

Une fois le cluster déployé, poursuivez avec les étapes suivantes pour exécuter un exemple intégré d’UDF Excel. Pour les UDF Excel personnalisées, consultez ces [ressources](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) pour générer les XLL et les déployer sur le cluster IaaS.

1.	Ouvrez un nouveau classeur Excel. Dans le ruban **Développer**, cliquez sur **Compléments**. Puis, dans la boîte de dialogue, cliquez sur **Parcourir**, accédez au dossier %CCP\_HOME%Bin\\XLL32 et sélectionnez l'exemple ClusterUDF32.xll. Si le ClusterUDF32 n'existe pas sur la machine cliente, vous pouvez le copier à partir du dossier %CCP\_HOME%Bin\\XLL32 sur le nœud principal.

    ![Sélection de l'UDF][udf]

2.	Cliquez sur **Fichier** > **Options** > **Avancé**. Sous **Formules**, cochez **Autoriser les fonctions XLL définies par l'utilisateur à s'exécuter sur un cluster de calcul**. Puis, cliquez sur **Options** et saisissez le nom de cluster complet dans **Nom de nœud principal du cluster**. (Comme indiqué précédemment, cette zone de saisie est limitée à 34 caractères, un nom de cluster plus long peut ne pas entrer. Vous pouvez appliquer la mise à jour Update 2 QFE KB3085833 sur le client, puis définir une variable au niveau de la machine pour le nom de cluster plus long).

    ![Configuration de l’UDF][options]

3.	Cliquez sur la cellule qui contient la valeur =XllGetComputerNameC() et appuyez sur Entrée pour exécuter le calcul UDF sur le cluster IaaS. La fonction récupère simplement le nom du nœud de calcul sur lequel s'exécute l'UDF. Lors de la première exécution, une boîte de dialogue d’informations d'identification vous invite à entrer le nom d'utilisateur et un mot de passe pour vous connecter au cluster IaaS.

    ![Exécution de l’UDF][run]

    S’il faut calculer beaucoup de cellules, appuyez sur Alt-Maj-Ctrl + F9 pour exécuter le calcul sur toutes les cellules.

## Étape 3. Exécution d’une charge de travail SOA depuis un client local

Pour exécuter des applications SOA générales sur le cluster HPC Pack IaaS, utilisez d'abord une des méthodes de l’étape 1 pour déployer le cluster IaaS, à l'aide d'une image de nœud de calcul générique (car vous n'aurez pas besoin d’Excel sur les nœuds de calcul). Puis, procédez comme suit.

1. Après avoir récupéré le certificat du cluster, importez-le sur l'ordinateur client sous Cert:\\CurrentUser\\Root.

2. Installez le [Kit de développement logiciel (SDK) HPC Pack 2012 R2 Update 2](http://www.microsoft.com/download/details.aspx?id=47756) et les [utilitaires clients HPC Pack 2012 R2 Update 2](https://www.microsoft.com/download/details.aspx?id=47754) afin de pouvoir développer et exécuter des applications clientes SOA.

3. Téléchargez l'[exemple de code](https://www.microsoft.com/download/details.aspx?id=41633) HellowWorldR2. Ouvrez HelloWorldR2.sln dans Visual Studio 2010 ou 2012.

4. Créez d'abord le projet EchoService et déployez le service sur le cluster IaaS de la même façon que pour un déploiement sur un cluster local. Pour des instructions détaillées, consultez le fichier Readme.doc dans HelloWordR2. Modifiez et générez le projet HellowWorldR2 et d’autres comme décrit ci-dessous pour générer les applications clientes SOA qui s’exécutent sur un cluster IaaS Azure à partir d'un ordinateur client local.

### Utilisation de la liaison HTTP avec la file d'attente de stockage Azure

Pour utiliser une liaison HTTP avec une file d'attente de stockage Azure, apportez quelques modifications à l'exemple de code.

* Mettez le nom du cluster à jour.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Si vous le souhaitez, utilisez la valeur TransportScheme par défaut dans SessionStartInfo ou définissez-la explicitement sur HTTP.

```
    info.TransportScheme = TransportScheme.Http;
```

* Utilisez la liaison par défaut pour le BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    Ou définissez-la explicitement à l'aide de basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Éventuellement, définissez l'indicateur UseAzureQueue sur true dans SessionStartInfo. S’il n’est pas défini, il est défini comme true par défaut lorsque le nom de cluster a des suffixes de domaine Azure et que le TransportScheme est HTTP.

    ```
    info.UseAzureQueue = true;
```

###Utilisation de la liaison HTTP sans la file d'attente de stockage Azure

Pour ce faire, vous devez définir explicitement l’indicateur UseAzureQueue sur false dans SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### Utilisation de la liaison NetTcp

Pour utiliser une liaison NetTcp, la configuration est semblable à la connexion à un cluster local. Vous devrez ouvrir quelques points de terminaison sur la machine virtuelle du nœud principal. Dans le portail de gestion Azure, procédez comme suit.


1. Arrêtez la machine virtuelle.

2. Ajoutez les ports TCP 9090, 9087, 9091, 9094 pour les services Session, Broker, travail Broker et de données respectivement

    ![Configuration des points de terminaison][endpoint]

3. Démarrez la machine virtuelle.

L'application cliente SOA ne nécessite aucune modification à l'exception de la modification du nom principal en nom complet du cluster IaaS.

## Étapes suivantes

* Consultez [ces ressources](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) pour plus d'informations sur l'exécution de charges de travail Excel avec HPC Pack.

* Consultez [Gestion des Services SOA dans Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) pour plus d'informations sur le déploiement et la gestion des services SOA avec HPC Pack.

<!--Image references-->
[scenario]: ./media/virtual-machines-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-excel-cluster-hpcpack/udf.png

<!---HONumber=Oct15_HO2-->