---
title: Cluster HPC Pack pour Excel et SOA | Microsoft Docs
description: "Prise en main de l’exécution de charges de travail Excel et SOA à grande échelle sur un cluster HPC Pack dans Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 08/25/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: c8b8b6af19a347804563b091760b18dfc47aa503
ms.lasthandoff: 03/25/2017


---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Prise en main de l’exécution de charges de travail Excel et SOA sur un cluster HPC Pack dans Azure
Cet article vous montre comment déployer un cluster Microsoft HPC Pack dans des machines virtuelles Azure à l’aide d’un modèle de démarrage rapide Azure ou éventuellement d’un script de déploiement Azure PowerShell. Le cluster utilise des images de machine virtuelle Azure Marketplace conçues pour exécuter des charges de travail d’architecture orientée services (SOA) ou Microsoft Excel avec HPC Pack. Vous pouvez utiliser le cluster pour exécuter des services Excel HPC et SOA simples à partir d'un ordinateur client local. Les services Excel HPC incluent le déchargement de classeurs Excel et les fonctions Excel définies par l'utilisateur (UDF).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Le diagramme général suivant montre le cluster HPC Pack que vous créez.

![Cluster HPC avec des nœuds exécutant des charges de travail Excel][scenario]

## <a name="prerequisites"></a>Composants requis
* **Ordinateur client** : vous avez besoin d’un ordinateur client Windows pour envoyer des exemples de tâches Excel et SOA au cluster. Vous avez également besoin d’un ordinateur Windows pour exécuter le script de déploiement de cluster Azure PowerShell (si vous choisissez cette méthode de déploiement) et
* **Abonnement Azure** : si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.
* **Quota de cœurs** : vous devez peut-être augmenter le quota de cœurs, en particulier si vous déployez plusieurs nœuds de cluster avec des tailles de machines virtuelles multiprocesseurs. Si vous utilisez un modèle de démarrage rapide Azure, le quota de cœurs dans le Resource Manager est défini par région Azure. Dans ce cas, vous devrez peut-être augmenter le quota d’une région spécifique. Consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md). Pour augmenter un quota, [ouvrez une demande de service clientèle en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitement.
* **Licence Microsoft Office** : si vous déployez des nœuds de calcul à l’aide d’une image de machine virtuelle HPC Pack Marketplace avec Microsoft Excel, une version d’évaluation de Microsoft Excel Professionnel Plus 2013 est installée et valable pendant 30 jours. À la fin de la période d’évaluation, vous devez fournir une licence valide de Microsoft Office pour activer Excel et continuer à exécuter les charges de travail. Consultez [Activation d’Excel](#excel-activation) plus loin dans cet article. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Étape 1. Configuration d’un cluster HPC Pack dans Azure
Nous allons vous montrer deux options de configuration du cluster : tout d'abord à l'aide d'un modèle de démarrage rapide Azure et du portail Azure, et, deuxièmement, à l'aide d'un script de déploiement Azure PowerShell.

### <a name="option-1-use-a-quickstart-template"></a>Option 1. Utilisation d’un modèle de démarrage rapide
Utilisez un modèle de démarrage rapide Azure pour déployer rapidement et facilement un cluster HPC Pack dans le portail Azure. Lorsque vous ouvrez le modèle dans le portail, vous obtenez une interface utilisateur simple vous permettant de saisir les paramètres pour votre cluster. Voici la procédure à suivre. 

> [!TIP]
> Si vous le souhaitez, utilisez un [modèle Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) qui crée un cluster similaire spécialement pour les charges de travail Excel. Les étapes sont légèrement différentes de celles qui suivent.
> 
> 

1. Consultez [Create HPC Cluster template page on GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Si vous le souhaitez, examinez les informations sur le modèle et le code source.
2. Cliquez sur **Déployer vers Azure** pour lancer un déploiement avec le modèle dans le portail Azure.
   
   ![Modèle de déploiement vers Azure][github]
3. Dans le portail, procédez comme suit pour saisir les paramètres du modèle de cluster HPC.
   
   a. Sur la page **Paramètres**, saisissez ou modifiez les valeurs des paramètres du modèle. (Cliquez sur l'icône en regard de chaque paramètre pour obtenir de l'aide.) Des exemples de valeurs sont affichés dans l'écran suivant. Cet exemple crée un cluster nommé *hpc01* dans le domaine *hpc.local*, constitué d’un nœud principal et de 2 nœuds de calcul. Les nœuds de calcul sont créés à partir d'une image de machine virtuelle HPC Pack comprenant Microsoft Excel.
   
   ![Saisie des paramètres][parameters]
   
   > [!NOTE]
   > La machine virtuelle du nœud principal est créée automatiquement à partir de la [dernière image Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) de HPC Pack 2012 R2 sur Windows Server 2012 R2. Actuellement, l'image repose sur HPC Pack 2012 R2 Update 3.
   > 
   > Les machines virtuelles du nœud de calcul sont créées à partir de la dernière image de la famille de nœuds de calcul sélectionnée. Sélectionnez l’option **ComputeNodeWithExcel** pour la dernière image de nœud de calcul HPC Pack comprenant une version d’évaluation de Microsoft Excel Professionnel Plus 2013. Pour déployer un cluster pour les sessions SOA générales ou pour le déchargement Excel UDF, choisissez l'option **ComputeNode** (sans Excel installé).
   > 
   > 
   
   b. Choisissez l’abonnement.
   
   c. Créez un groupe de ressources pour le cluster, tel que *hpc01RG*.
   
   d. Choisissez un emplacement pour le groupe de ressources, comme les États-Unis du Centre.
   
   e. Lisez les conditions de la page **Mentions légales** . Si vous les acceptez, cliquez sur **Acheter**. Une fois que vous avez défini les valeurs du modèle, cliquez sur **Créer**.
4. Lorsque le déploiement est terminé (cela dure environ 30 minutes généralement), exportez le fichier de certificat de cluster depuis le nœud principal du cluster. Par la suite, vous importez ce certificat public sur l'ordinateur client pour fournir l'authentification côté serveur pour une liaison HTTP sécurisée.
   
   a. Connectez-vous au nœud principal par le Bureau à distance à partir du portail Azure.
   
    ![Connexion au nœud principal][connect]
   
   b. Utilisez les procédures standard du Gestionnaire de certificat pour exporter le certificat du nœud principal (situé sous Cert:\LocalMachine\My) sans la clé privée. Dans cet exemple, exportez *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Exportation du certificat][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Option 2. Utilisation du script de déploiement du HPC Pack IaaS
Le script de déploiement du HPC Pack IaaS fournit une autre façon polyvalente de déployer un cluster HPC Pack. Il crée un cluster dans le modèle de déploiement classique, tandis que le modèle utilise le modèle de déploiement d’Azure Resource Manager. Le script est également compatible avec un abonnement dans le service Azure Global ou Azure China.

**Autres composants requis**

* **Azure PowerShell** - [installez et configurez Azure PowerShell](/powershell/azureps-cmdlets-docs) (version 0.8.10 ou ultérieure) sur votre ordinateur client.
* **Script de déploiement de HPC Pack IaaS** : Téléchargez et décompressez la dernière version du script à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Vérifiez la version du script en exécutant `New-HPCIaaSCluster.ps1 –Version`. Cet article est basé sur la version 4.5.0 ou ultérieure du script.

**Création du fichier de configuration**

 Le script de déploiement de HPC Pack IaaS utilise un fichier de configuration XML comme entrée, qui décrit l’infrastructure du cluster HPC. Pour déployer un cluster constitué d’un nœud principal et de 18 nœuds de calcul créés depuis l’image de nœuds de calcul comprenant Microsoft Excel, remplacez les valeurs par celles convenant à votre environnement dans l’exemple de fichier de configuration suivant. Pour plus d’informations sur le fichier de configuration, consultez le fichier Manual.rtf dans le dossier de script et la rubrique [Créer un cluster HPC avec le script de déploiement HPC Pack IaaS](windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Notes relatives au fichier de configuration**

* Le **VMName** du nœud principal **DOIT** être identique au **ServiceName**, sinon l’exécution des tâches SOA échoue.
* Veillez à spécifier **EnableWebPortal** afin que le certificat du nœud principal soit généré et exporté.
* Le fichier spécifie un script PowerShell de post-configuration PostConfig.ps1 qui s’exécute sur le nœud principal. L’exemple de script suivant configure la chaîne de connexion de stockage Azure, supprime le rôle de nœud de calcul à partir du nœud principal et affiche tous les nœuds en ligne lorsqu’ils sont déployés. 

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
2. Accédez au dossier de script (E:\IaaSClusterScript dans cet exemple).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. Exécutez la commande suivante pour déployer le cluster HPC Pack. Cet exemple part du principe que le fichier de configuration se trouve dans E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

L’exécution du script de déploiement HPC Pack dure un certain temps. Le script exporte et télécharge le certificat de cluster et l'enregistre dans le dossier Documents de l'utilisateur actuel sur l'ordinateur client. Il script génère un message similaire à celui ci-dessous. Vous allez maintenant importer le certificat dans le magasin de certificats approprié.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Étape 2. Déchargement de classeurs Excel et exécution d’UDF à partir d'un client local
### <a name="excel-activation"></a>Activation d’Excel
En cas d’utilisation d’une image de machine virtuelle ComputeNodeWithExcel pour les charges de travail de production, vous devez fournir une clé de licence Microsoft Office valide pour activer Excel sur les nœuds de calcul. Sinon, la version d’évaluation d’Excel expire au bout de 30 jours et l’exécution des classeurs Excel échoue avec l’exception COMExeption (0x800AC472). 

Vous pouvez rallonger la période d’évaluation de 30 jours supplémentaires : ouvrez une session sur le nœud principal et exécutez la commande clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` sur tous les nœuds de calcul Excel par le biais de HPC Cluster Manager. Vous pouvez rallonger la période d’évaluation 2 fois au maximum. Ensuite, vous devez fournir une clé de licence Office valide.

La version d’Office Professionnel Plus 2013 installée sur l’image de machine virtuelle est une édition de volume avec une clé de licence de volume générique (GVLK). Vous pouvez l’activer par le biais du service de gestion de clés (KMS), de l’activation basée sur Active Directory (AD-BA) ou d’une clé d’activation multiple (MAK). 

    * Pour utiliser l’activation KMS/AD-BA, utilisez un serveur KMS existant ou configurez-en un à l’aide du pack de licences en volume Microsoft Office 2013. (Si vous le souhaitez, configurez le serveur sur le nœud principal.) Activez ensuite la clé d’hôte KMS par Internet ou par téléphone. Puis, exécutez la commande `ospp.vbs` pour définir le port et le serveur KMS et activer Office sur tous les nœuds de calcul Excel. 

    * Pour utiliser une clé MAK, commencez par exécuter la commande clusrun `ospp.vbs` pour entrer la clé, puis activez tous les nœuds de calcul Excel par Internet ou par téléphone. 

> [!NOTE]
> Les clés de produit commercialisées pour Office Professionnel Plus 2013 ne peuvent pas être utilisées avec cette image de machine virtuelle. Si vous disposez de clés valides et d’un support d’installation pour des éditions Office ou Excel autres que cette édition en volume Office Professionnel Plus 2013 (édition de volume), vous pouvez également les utiliser. Tout d’abord, désinstallez cette édition de volume et installez l’édition dont vous disposez. Le nœud de calcul Excel réinstallé peut être capturé comme une image de machine virtuelle personnalisée à utiliser dans un déploiement à grande échelle.
> 
> 

### <a name="offload-excel-workbooks"></a>Déchargement de classeurs Excel
Suivez ces étapes pour décharger un classeur Excel afin qu’il s’exécute sur le cluster HPC Pack dans Azure. Pour ce faire, Excel 2010 ou 2013 doit déjà être installé sur l'ordinateur client.

1. Utilisez l’une des options de l'étape 1 pour déployer un cluster HPC Pack avec l’image de nœud de calcul Excel. Obtenez le fichier de certificat de cluster (.cer), le nom d'utilisateur et le mot de passe du cluster.
2. Sur l'ordinateur client, importez le certificat de cluster sous Cert:\CurrentUser\Root.
3. Assurez-vous qu'Excel est installé. Créez un fichier Excel.exe.config avec le contenu suivant dans le même dossier qu’Excel.exe sur l'ordinateur client. Cette étape garantit que le complément COM Excel de HPC Pack 2012 R2 est chargé avec succès.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Configurez le client de manière à soumettre des travaux au cluster HPC Pack. Une option consiste à télécharger l’intégralité de [l’installation de HPC Pack 2012 R2 Update 3](http://www.microsoft.com/download/details.aspx?id=49922) et à installer le client HPC Pack. Vous pouvez également télécharger et installer les [utilitaires clients HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923) et le redistributable Visual C++ 2010 adapté à votre ordinateur ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).
5. Dans cet exemple, nous utilisons un exemple de classeur Excel nommé ConvertiblePricing_Complete.xlsb. Vous pouvez le télécharger [ici](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
6. Copiez le classeur Excel dans un dossier de travail comme D:\Excel\Run.
7. Ouvrez le classeur Excel. Sur le ruban **Développer**, cliquez sur **Compléments COM** et confirmez que le complément COM Excel HPC Pack est chargé.
   
   ![Complément Excel pour HPC Pack][addin]
8. Modifiez la macro VBA HPCControlMacros dans Excel en modifiant les lignes commentées, comme illustré dans le script suivant. Remplacez les valeurs par celles appropriées pour votre environnement.
   
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
9. Copiez le classeur Excel dans un répertoire de téléchargement tel que D:\Excel\Upload. Ce répertoire est spécifié dans la constante HPC_DependsFiles dans la macro VBA.
10. Pour exécuter le classeur sur le cluster dans Azure, cliquez sur le bouton **Cluster** sur la feuille de calcul.

### <a name="run-excel-udfs"></a>Exécution d’UDF Excel
Pour exécuter des UDF Excel, suivez les étapes 1 à 3 précédentes pour configurer l'ordinateur client. Pour des UDF Excel, vous n’avez pas besoin d’installer l’application Excel sur les nœuds de calcul. Par conséquent, lors de la création des nœuds de calcul de votre cluster, vous pouvez choisir une image de nœud de calcul normale au lieu de l’image de nœud de calcul Excel.

> [!NOTE]
> Il existe une limite de 34 caractères dans la boîte de dialogue de connecteur de cluster dans Excel 2010 et 2013. Cette boîte de dialogue vous permet de spécifier le cluster qui exécute les UDF. Si le nom de cluster complet est plus long (par exemple hpcexcelhn01.southeastasia.cloudapp.azure.com), il ne tient pas dans la boîte de dialogue. La solution consiste à définir une variable à l’échelle de la machine (telle que *CCP_IAASHN*) avec la valeur du nom de cluster trop long. Entrez ensuite *%CCP_IAASHN%* dans la boîte de dialogue en tant que le nom de nœud principal du cluster. 
> 
> 

Une fois le cluster déployé, poursuivez avec les étapes suivantes pour exécuter un exemple intégré d’UDF Excel. Pour les UDF Excel personnalisées, consultez ces [ressources](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) pour générer les XLL et les déployer sur le cluster IaaS.

1. Ouvrez un nouveau classeur Excel. Dans le ruban **Développer**, cliquez sur **Compléments**. Puis, dans la boîte de dialogue, cliquez sur **Parcourir**, accédez au dossier %CCP_HOME%Bin\XLL32 et sélectionnez l’exemple ClusterUDF32.xll. Si le ClusterUDF32 n'existe pas sur la machine cliente, copiez-le à partir du dossier %CCP_HOME%Bin\XLL32 sur le nœud principal.
   
   ![Sélection de l'UDF][udf]
2. Cliquez sur **Fichier** > **Options** > **Avancé**. Sous **Formules**, cochez l’option **Autoriser l’exécution de fonctions XLL définies par l’utilisateur sur un cluster de calcul**. Puis cliquez sur **Options** et entrez le nom de cluster complet dans **Nom de nœud principal du cluster**. (Comme indiqué précédemment, cette zone de saisie est limitée à 34 caractères, un nom de cluster plus long peut ne pas entrer. Vous pouvez utiliser des variables au niveau de la machine ici pour les noms de cluster longs.)
   
   ![Configuration de l’UDF][options]
3. Pour exécuter le calcul UDF sur le cluster, cliquez sur la cellule qui contient la valeur =XllGetComputerNameC() et appuyez sur Entrée. La fonction récupère simplement le nom du nœud de calcul sur lequel s'exécute l'UDF. Lors de la première exécution, une boîte de dialogue d’informations d'identification vous invite à entrer le nom d'utilisateur et un mot de passe pour vous connecter au cluster IaaS.
   
   ![Exécution de l’UDF][run]
   
   S’il faut calculer beaucoup de cellules, appuyez sur Alt-Maj-Ctrl + F9 pour exécuter le calcul sur toutes les cellules.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Étape 3. Exécution d’une charge de travail SOA depuis un client local
Pour exécuter des applications SOA générales sur le cluster HPC Pack IaaS, utilisez tout d’abord l’une des méthodes de l’étape 1 pour déployer le cluster. Dans ce cas, spécifiez une image de nœud de calcul générique, car vous n’avez pas besoin d’Excel sur les nœuds de calcul. Puis, procédez comme suit.

1. Après avoir récupéré le certificat du cluster, importez-le sur l'ordinateur client sous Cert:\CurrentUser\Root.
2. Installez le [Kit de développement logiciel (SDK) HPC Pack 2012 R2 Update 3](http://www.microsoft.com/download/details.aspx?id=49921) et [les utilitaires clients HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923). Ces outils vous permettent de développer et d’exécuter des applications clientes SOA.
3. Téléchargez [l’exemple de code](https://www.microsoft.com/download/details.aspx?id=41633)HelloWorldR2. Ouvrez HelloWorldR2.sln dans Visual Studio 2010 ou 2012.
4. Générez tout d’abord le projet EchoService. Déployez le service sur le cluster IaaS de la même manière que pour un déploiement sur un cluster local. Pour des instructions détaillées, consultez le fichier Readme.doc dans HelloWordR2. Modifiez et générez le code HellWorldR2 et d’autres projets comme décrit dans la section suivante pour générer les applications clientes SOA qui s’exécutent sur un cluster IaaS Azure.

### <a name="use-http-binding-with-azure-storage-queue"></a>Utilisation de la liaison HTTP avec la file d'attente de stockage Azure
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

### <a name="use-http-binding-without-azure-storage-queue"></a>Utilisation de la liaison HTTP sans la file d'attente de stockage Azure
Pour utiliser une liaison Http sans une file d’attente de stockage Azure, vous devez définir explicitement l’indicateur UseAzureQueue sur false dans SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Utilisation de la liaison NetTcp
Pour utiliser une liaison NetTcp, la configuration est la même que pour se connecter à un cluster local. Vous devez ouvrir quelques points de terminaison sur la machine virtuelle du nœud principal. Si vous avez utilisé le script de déploiement HPC Pack IaaS pour créer le cluster, par exemple, définissez les points de terminaison dans le portail Azure Classic comme suit.

1. Arrêtez la machine virtuelle.
2. Ajoutez les ports TCP 9090, 9087, 9091 et 9094 pour les services Session, Broker, worker Broker et de données respectivement
   
    ![Configuration des points de terminaison][endpoint]
3. Démarrez la machine virtuelle.

L'application cliente SOA ne nécessite aucune modification à l'exception de la modification du nom principal en nom complet du cluster IaaS.

## <a name="next-steps"></a>Étapes suivantes
* Consultez [ces ressources](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) pour plus d'informations sur l'exécution de charges de travail Excel avec HPC Pack.
* Consultez [Gestion des Services SOA dans Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) pour plus d'informations sur le déploiement et la gestion des services SOA avec HPC Pack.

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png

