<properties urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Approvisionnement de clusters HBase sur Azure Virtual Network | Azure" metaKeywords="" description="D&eacute;couvrez comment cr&eacute;er des clusters HDInsight sur Azure Virtual Network." metaCanonical="" services="hdinsight" documentationCenter="" title="Approvisionnement de clusters HBase sur Azure Virtual Network" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Approvisionnement de clusters HBase sur Azure Virtual Network

Découvrez comment créer des clusters HDInsight sur Azure Virtual Network.

Avec l'intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase. Voici les avantages :

-   Connectivité directe de l'application web aux nœuds du cluster HBase, ce qui permet les communications au moyen d'API RPC Java HBase.
-   Amélioration des performances en évitant à votre trafic de transiter par plusieurs passerelles et équilibrages de charge.
-   Traitement des informations critiques de façon plus sécurisée sans exposer de points de terminaison publics.

## Dans cet article

-   [Configuration requise][Configuration requise]
-   [Approvisionnement de clusters HBase dans un réseau virtuel][Approvisionnement de clusters HBase dans un réseau virtuel]
-   [Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d'API RPC Java HBase][Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d'API RPC Java HBase]
-   [Approvisionnement d'un cluster HBase au moyen de PowerShell][Approvisionnement d'un cluster HBase au moyen de PowerShell]
-   [Étapes suivantes][Étapes suivantes]

## <span id="prerequisites"></span></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   **Un abonnement Azure**. Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][formules d'abonnement], les [offres spéciales membres][offres spéciales membres] ou la [version d'évaluation gratuite][version d'évaluation gratuite].

-   **Un poste de travail sur lequel Azure PowerShell est installé et configuré**. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell]. Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu'administrateur et définir la stratégie d'exécution sur *RemoteSigned*. Consultez la page [Exécution de scripts Windows PowerShell][Exécution de scripts Windows PowerShell].

    Avant d'exécuter vos scripts PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l'aide de la cmdlet suivante :

        Add-AzureAccount

    Si vous possédez plusieurs abonnements Azure, utilisez la cmdlet suivante pour définir l'abonnement en cours :

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Approvisionnement d'un cluster HBase dans un réseau virtuel

**Pour créer un réseau virtuel au moyen du portail de gestion :**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Dans le bas de la page, cliquez sur **NOUVEAU**, **SERVICES DE RÉSEAU**, **RÉSEAU VIRTUEL**, puis sur **CRÉATION RAPIDE**.
3.  Tapez ou sélectionnez les valeurs suivantes :

    -   **Nom** : nom de votre réseau virtuel.
    -   **Espace d'adressage** : choisissez un espace d'adressage pour le réseau virtuel qui est suffisant grand pour fournir des adresses pour tous les nœuds du cluster. Si ce n'est pas le cas, l'approvisionnement échoue.
    -   **Nombre maximal de machines virtuelles** : choisissez l'un des nombres maximum de machines virtuelles.
    -   **Emplacement** : l'emplacement doit être le même que celui du cluster HBase que vous allez créer.
    -   **Serveur DNS** : étant donné que cet article utilise le serveur DNS interne fourni par Azure, vous pouvez choisir **Aucun**. Des procédures de configuration de réseau plus avancées avec des serveurs DNS personnalisés sont également prises en charge. Pour des instructions détaillées, consultez la page <http://msdn.microsoft.com/library/azure/jj156088.aspx>.

4.  Cliquez sur **CRÉER UN RÉSEAU VIRTUEL**. Le nom du nouveau réseau virtuel apparaît dans la liste. Attendez que la valeur **Créé** apparaisse dans la colonne Statut.
5.  Dans le volet principal, cliquez sur le réseau virtuel que vous venez de créer.
6.  En haut de la page, cliquez sur **TABLEAU DE BORD**.
7.  Sous **aperçu rapide**, prenez note de l'**ID RÉSEAU VIRTUEL**. Vous en aurez besoin lors de l'approvisionnement du cluster HBase.
8.  En haut de la page, cliquez sur **CONFIGURER**.
9.  En bas de la page, le nom du sous-réseau par défaut est **Sous-réseau-1**. Facultativement, vous pouvez renommer le sous-réseau ou ajouter un nouveau sous-réseau pour le cluster HBase. Prenez note du nom du sous-réseau, car vous en aurez besoin lors de l'approvisionnement du cluster.
10. Vérifiez **CIDR (NOMBRE D'ADRESSES)** pour le sous-réseau qui sera utilisé pour le cluster. Le nombre d'adresses doit être supérieur au nombre de nœuds de travail plus 7 (Gateway : 2, Headnode : 2, Zookeeper : 3). Par exemple, si vous avez besoin d'un cluster HBase à 10 nœuds, le nombre d'adresses pour le sous-réseau doit être supérieur à 17 (10+7). Si ce n'est pas le cas, le déploiement échoue.

    > [WACOM.NOTE] Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

11. Cliquez sur **Enregistrer** en bas de la page, si vous avez mis à jour les valeurs de sous-réseau.

> [WACOM.NOTE] Les clusters HDInsight utilisent le stockage d'objets blob Azure pour stocker des données. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight][Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight]. Vous aurez besoin d'un compte de stockage et d'un conteneur de stockage d'objets blob. Le compte de stockage doit être situé au même emplacement que le réseau virtuel et le cluster.

**Pour créer un compte de stockage Azure et un conteneur de stockage d'objets blob :**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

3.  Tapez ou sélectionnez les valeurs suivantes :

    -   **URL** : nom du compte de stockage.
    -   **EMPLACEMENT** : emplacement du compte de stockage. Vérifiez qu'il correspond à l'emplacement du réseau virtuel. Les groupes d'affinités ne sont pas pris en charge.
    -   **RÉPLICATION** : à des fins de test, utilisez **Redondant en local** pour réduire le coût.

4.  Cliquez sur **CREATE STORAGE ACCOUNT**. Le nouveau compte de stockage figure dès lors dans la liste de stockage.
5.  Attendez que le **STATUT** du nouveau compte de stockage passe à **En ligne**.
6.  Cliquez sur le nouveau compte de stockage dans la liste pour le sélectionner.
7.  Cliquez sur **GÉRER LES CLÉS D'ACCÈS** dans le bas de la page.
8.  Notez le **NOM DU COMPTE DE STOCKAGE** et la **CLÉ D'ACCÈS PRIMAIRE** (ou la **CLÉ D'ACCÈS SECONDAIRE**. Les deux clés fonctionnent). Vous en aurez besoin plus loin dans le didacticiel.
9.  En haut de la page, cliquez sur **CONTENEUR**.
10. En bas de la page, cliquez sur **AJOUTER**.
11. Entrez le nom du conteneur. Ce conteneur sera utilisé comme conteneur par défaut pour le cluster HBase. Par défaut, le nom du conteneur par défaut correspond au nom du cluster. Pour le champ **ACCÈS**, gardez la valeur **Privé**.
12. Cliquez sur l'icône en forme de coche pour créer le conteneur.

**Approvisionnement d'un cluster HBase en utilisant le portail Azure :**

> [WACOM.NOTE] Pour plus d'informations sur l'approvisionnement d'un nouveau cluster HBase au moyen de PowerShell, consultez la rubrique [Approvisionnement d'un cluster HBase au moyen de PowerShell][Approvisionnement d'un cluster HBase au moyen de PowerShell].

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, sur **HDINSIGHT**, puis cliquez sur **CRÉATION PERSONNALISÉE**.
3.  Entrez un **NOM DE CLUSTER** et sélectionnez la **VERSION HDINSIGHT** à utiliser pour le cluster.

    ![cluster name and version fields][cluster name and version fields]

4.  Sélectionnez le nombre de **NŒUDS DE DONNÉES** à créer pour le cluster, ainsi que la région ou le réseau virtuel Azure (**RÉGION/RÉSEAU VIRTUEL**) à utiliser pour ce cluster.

    ![number of nodes and region fields][number of nodes and region fields]

5.  Entrez le **NOM D'UTILISATEUR** de l'administrateur et le **MOT DE PASSE** à utiliser pour ce cluster.

    ![administrator name and password fields][administrator name and password fields]

6.  Choisissez d'utiliser un nouveau compte de stockage ou un compte de stockage existant. Si vous en choisissez un nouveau, entrez le **NOM DE COMPTE** et le **CONTENEUR PAR DÉFAUT** à créer. Pour finir, cliquez sur la coche pour créer le cluster.

    ![storage account selection][storage account selection]

Pour commencer à utiliser votre nouveau cluster HBase, vous pouvez utiliser les procédures figurant dans la rubrique [Prise en main de HBase avec Hadoop dans HDInsight][Prise en main de HBase avec Hadoop dans HDInsight].

## <span id="connect"></span></a>Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d'API RPC Java HBase

1.  Approvisionnez une machine virtuelle IaaS dans le même réseau virtuel Azure et le même sous-réseau. Ainsi, la machine virtuelle et le cluster HBase utilisent tous deux le même serveur DNS interne pour résoudre les noms d'hôte. Pour cela, vous devez choisir l'option À partir de la galerie et sélectionner le réseau virtuel au lieu d'un centre de données. Pour obtenir des instructions, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server][Création d'une machine virtuelle exécutant Windows Server]. Une image standard de Windows Server 2012 avec une petite taille de machine virtuelle est suffisante.

2.  Lorsque vous utilisez une application Java pour vous connecter à distance à HBase, vous devez utiliser le nom de domaine complet. Pour déterminer cela, nous devons obtenir le suffixe DNS propre à la connexion du cluster HBase. À cette fin, utilisez Curl pour interroger Ambari ou le Bureau à distance pour effectuer une connexion au cluster.

    -   **Curl** - utilisez la commande suivante :

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        Dans les données JSON renvoyées, recherchez l'entrée « host\_name ». Elle contient le nom de domaine complet des nœuds du cluster. Par exemple :

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        La partie du nom de domaine commençant par le nom de cluster est le suffixe DNS. Par exemple, mon\_cluster.b1.cloudapp.net.

    -   **PowerShell** - utilisez le script PowerShell suivant pour enregistrer la fonction **Get-ClusterDetail**, qui peut être utilisée pour renvoyer le suffixe DNS.

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS 
                 Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
                .Description
                 This command shows following 4 properties of an HDInsight cluster.
                 1. ZookeeperQuorum (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (only support HBase type cluster)
                    Shows a list of host FQDNs which run HBase rest server.
                 4. FQDNSuffix (support all type cluster)
                    Shows FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of hbase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs which run HBase rest server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        Après avoir exécuté le script PowerShell, utilisez la commande suivante pour renvoyer le suffixe DNS au moyen de la fonction Get-ClusterDetail. Spécifiez votre nom de cluster HDInsight HBase, ainsi que le nom et le mot de passe de l'administrateur lors de l'utilisation de cette commande.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        Ceci renvoie le suffixe DNS. Par exemple, **votre\_nom\_cluster.b4.internal.cloudapp.net**.

    > [WACOM.NOTE] Vous pouvez également utiliser le Bureau à distance pour vous connecter au cluster HBase (vous serez connecté au nœud principal) et exécuter **ipconfig** à partir d'une invite de commandes pour obtenir le suffixe DNS. Pour des instructions sur l'activation du protocole RDP et la façon de se connecter au moyen de ce dernier, consultez la page [Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure][Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure].
    >
    > ![hdinsight.hbase.dns.suffix][hdinsight.hbase.dns.suffix]

<!--  3.  Change the Primary DNS Suffix configuration of the virtual machine. This enables virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to the workernode0 of the HBase cluster.       To make the configuration change:      1. RDP into the virtual machine.      2. Open **Local Group Policy Editor**. The executable is gpedit.msc.     3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.      - Set **Primary DNS Suffix** to the value obtained in the step 2:           ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]     4. Click **OK**.      5. Reboot the virtual machine. -->

Pour vérifier que la machine virtuelle peut communiquer avec le cluster HBase, utilisez la commande suivante `ping headnode0.<dns suffix>` à partir de la machine virtuelle. Par exemple, ping headnode0.mon\_cluster.b1.cloudapp.net

Pour utiliser ces informations dans une application Java, vous pouvez suivre la procédure décrite dans la rubrique [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)][Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)] pour créer une application. Pour que l'application soit connectée à un serveur HBase distant, modifiez le fichier **hbase-site.xml** dans cet exemple afin d'utiliser le nom de domaine complet pour ZooKeeper. Par exemple :

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [WACOM.NOTE] Pour plus d'informations sur la résolution de noms sur des réseaux Azure Virtual Network, y compris la façon d'utiliser votre propre serveur DNS, consultez la rubrique [Résolution de noms (DNS)][Résolution de noms (DNS)].

## <span id="powershell"></span></a>Approvisionnement d'un cluster HBase au moyen d'Azure PowerShell :\*\*

1.  Ouvrez PowerShell ISE.
2.  Copiez et collez ce qui suit dans le volet de script.

        $hbaseClusterName = "<HBaseClusterName>"
        $hadoopUserName = "<HBaseClusterUsername>"
        $hadoopUserPassword = "<HBaseClusterUserPassword>"
        $location = "<HBaseClusterLocation>"  #i.e. "West US"
        $clusterSize = <HBaseClusterSize>  
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>"
        $storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
        $storageAccountKey = "<AzureStorageAccountKey>"
        $storageContainerName = "<AzureBlobStorageContainer>"

        $password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 

        New-AzureHDInsightCluster -Name $hbaseClusterName `
                                  -ClusterType HBase `
                                  -Version 3.1 `
                                  -Location $location `
                                  -ClusterSizeInNodes $clusterSize `
                                  -Credential $creds `
                                  -VirtualNetworkId $vnetID `
                                  -SubnetName $subNetName `
                                  -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                                  -DefaultStorageAccountKey $storageAccountKey `
                                  -DefaultStorageContainerName $storageContainerName

3.  Cliquez sur **Exécuter le script** ou appuyez sur **F5**.
4.  Pour valider le cluster, vous pouvez vérifier le cluster à partir du portail de gestion ou exécuter la cmdlet PowerShell suivante à partir du volet inférieur :

    Get-AzureHDInsightCluster

## <span id="nextsteps"></span></a>Étapes suivantes

Dans ce didacticiel, nous avons appris à approvisionner un cluster HBase. Pour plus d'informations, consultez les rubriques suivantes :

-   [Prise en main de HDInsight][Prise en main de HDInsight]
-   [Approvisionnement de clusters dans HDInsight][Approvisionnement de clusters dans HDInsight]
-   [Prise en main de HBase avec Hadoop dans HDInsight][Prise en main de HBase avec Hadoop dans HDInsight]
-   [Analyse de sentiments Twitter avec HBase dans HDInsight][Analyse de sentiments Twitter avec HBase dans HDInsight]
-   [Présentation du réseau virtuel][Présentation du réseau virtuel].

  [Configuration requise]: #prerequisites
  [Approvisionnement de clusters HBase dans un réseau virtuel]: #hbaseprovision
  [Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d'API RPC Java HBase]: #connect
  [Approvisionnement d'un cluster HBase au moyen de PowerShell]: #powershell
  [Étapes suivantes]: #nextsteps
  [formules d'abonnement]: http://azure.microsoft.com/fr-fr/pricing/purchase-options/
  [offres spéciales membres]: http://azure.microsoft.com/fr-fr/pricing/member-offers/
  [version d'évaluation gratuite]: http://azure.microsoft.com/fr-fr/pricing/free-trial/
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell
  [Exécution de scripts Windows PowerShell]: http://technet.microsoft.com/fr-fr/library/ee176949.aspx
  [portail de gestion Azure]: https://manage.windowsazure.com
  [Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight]: ../hdinsight-use-blob-storage/
  [cluster name and version fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png
  [number of nodes and region fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard2.png
  [administrator name and password fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard3.png
  [storage account selection]: ./media/hdinsight-hbase-provision-vnet/hbasewizard4.png
  [Prise en main de HBase avec Hadoop dans HDInsight]: ../hdinsight-hbase-get-started/
  [Création d'une machine virtuelle exécutant Windows Server]: ../virtual-machines-windows-tutorial/
  [Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.suffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)]: azure.microsoft.com/fr-fr/documentation/articles/hdinsight-hbase-build-java-maven/
  [Résolution de noms (DNS)]: http://msdn.microsoft.com/fr-fr/library/azure/jj156088.aspx
  [Prise en main de HDInsight]: ../hdinsight-get-started/
  [Approvisionnement de clusters dans HDInsight]: ../hdinsight-provision-clusters/
  [Analyse de sentiments Twitter avec HBase dans HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Présentation du réseau virtuel]: http://msdn.microsoft.com/library/azure/jj156007.aspx
