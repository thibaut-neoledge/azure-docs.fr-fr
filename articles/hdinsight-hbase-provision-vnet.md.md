<properties linkid="manage-services-hdinsight-hbase-provision-on-vnet" urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Provision HBase clusters on Azure Virtual Network | Azure" metaKeywords="" description="Learn how to create HDInsight clusters on Azure Virtual Network." metaCanonical="" services="hdinsight" documentationCenter="" title="Provision HBase clusters on Azure Virtual Network" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Approvisionnement de clusters HBase sur Azure Virtual Network

Découvrez comment créer des clusters HDInsight sur Azure Virtual Network.

Avec l'intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase. Voici les avantages :

-   Connectivité directe de l'application web aux nœuds du cluster HBase, ce qui permet les communications au moyen d'API RPC Java HBase.
-   Amélioration des performances en évitant à votre trafic de transiter par plusieurs passerelles et équilibrages de charge.
-   Traitement des informations critiques de façon plus sécurisée sans exposer de points de terminaison publics.

> [WACOM.NOTE]Actuellement, seuls les clusters HBase peuvent être approvisionnés dans des réseaux Azure Virtual Network. Les clusters Hadoop ne sont pas pris en charge.

## Dans cet article

-   [Configuration requise][]
-   [Approvisionnement de clusters HBase dans un réseau virtuel][]
-   [Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d'API RPC Java HBase][]
-   [Étapes suivantes][]

## <span id="prerequisites"></span></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   **Un abonnement Azure**. Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][], les [offres spéciales membres][] ou la [version d'évaluation gratuite][].

-   **Un poste de travail sur lequel Azure PowerShell est installé et configuré**. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][]. Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu'administrateur et définir la stratégie d'exécution sur *RemoteSigned*. Consultez la page [Exécution de scripts Windows PowerShell][].

    Avant d'exécuter vos scripts PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l'aide de la cmdlet suivante :

        Add-AzureAccount

    Si vous possédez plusieurs abonnements Azure, utilisez la cmdlet suivante pour définir l'abonnement en cours :

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Approvisionnement d'un cluster HBase dans un réseau virtuel

**Pour créer un réseau virtuel au moyen du portail de gestion :**

1.  Connectez-vous au [portail de gestion Azure][].
2.  Dans le bas de la page, cliquez sur **NOUVEAU**, **SERVICES DE RÉSEAU**, **RÉSEAU VIRTUEL**, puis sur **CRÉATION RAPIDE**.
3.  Tapez ou sélectionnez les valeurs suivantes :

    -   **Nom** : nom de votre réseau virtuel.
    -   **Espace d'adressage** : choisissez un espace d'adressage pour le réseau virtuel qui est suffisant grand pour fournir des adresses pour tous les nœuds du cluster. Si ce n'est pas le cas, l'approvisionnement échoue.
    -   **Nombre maximal de machines virtuelles** : choisissez l'un des nombres maximum de machines virtuelles.
    -   **Emplacement** : l'emplacement doit être le même que celui du cluster HBase que vous allez créer.
    -   **Serveur DNS** : étant donné que cet article utilise le serveur DNS interne fourni par Azure, vous pouvez choisir **Aucun**. Des procédures de configuration de réseau plus avancées avec des serveurs DNS personnalisés sont également prises en charge. Pour des instructions détaillées, consultez la page [][]<http://msdn.microsoft.com/library/azure/jj156088.aspx></a>.

4.  Cliquez sur **CRÉER UN RÉSEAU VIRTUEL**. Le nom du nouveau réseau virtuel apparaît dans la liste. Attendez que la valeur **Créé** apparaisse dans la colonne Statut.
5.  Dans le volet principal, cliquez sur le réseau virtuel que vous venez de créer.
6.  En haut de la page, cliquez sur **TABLEAU DE BORD**.
7.  Sous **aperçu rapide**, prenez note de l'**ID RÉSEAU VIRTUEL**. Vous en aurez besoin lors de l'approvisionnement du cluster HBase.
8.  En haut de la page, cliquez sur **CONFIGURER**.
9.  En bas de la page, le nom du sous-réseau par défaut est **Sous-réseau-1**. Facultativement, vous pouvez renommer le sous-réseau ou ajouter un nouveau sous-réseau pour le cluster HBase. Prenez note du nom du sous-réseau, car vous en aurez besoin lors de l'approvisionnement du cluster.
10. Vérifiez **CIDR (NOMBRE D'ADRESSES)** pour le sous-réseau qui sera utilisé pour le cluster. Le nombre d'adresses doit être supérieur au nombre de nœuds de travail plus 7 (Gateway : 2, Headnode : 2, Zookeeper : 3). Par exemple, si vous avez besoin d'un cluster HBase à 10 nœuds, le nombre d'adresses pour le sous-réseau doit être supérieur à 17 (10+7). Si ce n'est pas le cas, le déploiement échoue.

    > [WACOM.NOTE] Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

11. Cliquez sur **Enregistrer** en bas de la page, si vous avez mis à jour les valeurs de sous-réseau.

> [WACOM.NOTE] Les clusters HDInsight utilisent le stockage d'objets blob Azure pour stocker des données. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight][]. Vous aurez besoin d'un compte de stockage et d'un conteneur de stockage d'objets blob. Le compte de stockage doit être situé au même emplacement que le réseau virtuel et le cluster.

**Pour créer un compte de stockage Azure et un conteneur de stockage d'objets blob :**

1.  Connectez-vous au [portail de gestion Azure][].
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

**Pour approvisionner un cluster HBase au moyen d'Azure PowerShell :**

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

Pour tester le nouveau cluster HBase, vous pouvez utiliser les procédures figurant dans [Prise en main de HBase avec Hadoop dans HDInsight][].

## <span id="connect"></span></a>Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d'API RPC Java HBase

1.  Approvisionnez une machine virtuelle IaaS dans le même réseau virtuel Azure et le même sous-réseau. Ainsi, la machine virtuelle et le cluster HBase utilisent tous deux le même serveur DNS interne pour résoudre les noms d'hôte. Pour cela, vous devez choisir l'option À partir de la galerie et sélectionner le réseau virtuel au lieu d'un centre de données. Pour obtenir des instructions, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server][]. Une image standard de Windows Server 2012 avec une petite taille de machine virtuelle est suffisante.

2.  Obtenez le suffixe DNS propre à la connexion du cluster HBase. Pour cela, ouvrez une session RDP sur le cluster HBase (vous serez connecté au nœud principal) et exécutez **ipconfig** à partir d'une invite de commandes. Pour des instructions sur l'activation du protocole RDP et la façon de se connecter au moyen de ce dernier, consultez la page [Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure][].

    ![hdinsight.hbase.dns.suffix][]

3.  Modifiez la configuration Suffixe DNS principal de la machine virtuelle. Cela permet à la machine virtuelle de résoudre automatiquement le nom d'hôte du cluster HBase sans spécifier de façon explicite le suffixe. Par exemple, le nom d'hôte *workernode0* sera résolu correctement sur le nœud workernode0 du cluster HBase.
     Pour modifier la configuration :

    1.  Ouvrez une connexion RDP à la machine virtuelle.
    2.  Ouvrez **Éditeur de stratégie de groupe locale**. L'exécutable est gpedit.msc.
    3.  Développez **Configuration ordinateur**, **Modèles d'administration**, **Réseau**, puis cliquez sur **Client DNS**.
    4.  Définissez **Suffixe DNS principal** sur la valeur obtenue à l'étape 2 :

        ![hdinsight.hbase.primary.dns.suffix][]

    1.  Cliquez sur **OK**.
    2.  Redémarrez la machine virtuelle.

    À présent, la machine virtuelle est prête à communiquer avec le cluster HBase. Pour tester la connexion, exécutez « ping headnode0 » à partir de la machine virtuelle.

## <span id="nextsteps"></span></a>Étapes suivantes

Dans ce didacticiel, nous avons appris à approvisionner un cluster HBase. Pour plus d'informations, consultez les rubriques suivantes :

-   [Prise en main de HDInsight][]
-   [Approvisionnement de clusters dans HDInsight][]
-   [Prise en main de HBase avec Hadoop dans HDInsight][]
-   [Analyse de sentiments Twitter avec HBase dans HDInsight][]
-   [Présentation du réseau virtuel][].

  [Configuration requise]: #prerequisites
  [Approvisionnement de clusters HBase dans un réseau virtuel]: #hbaseprovision
  [Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d'API RPC Java HBase]: #connect
  [Étapes suivantes]: #nextsteps
  [formules d'abonnement]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [offres spéciales membres]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [version d'évaluation gratuite]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell
  [Exécution de scripts Windows PowerShell]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [portail de gestion Azure]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight]: ../hdinsight-use-blob-storage/
  [Prise en main de HBase avec Hadoop dans HDInsight]: ../hdinsight-hbase-get-started/
  [Création d'une machine virtuelle exécutant Windows Server]: ../virtual-machines-windows-tutorial/
  [Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.suffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [hdinsight.hbase.primary.dns.suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
  [Prise en main de HDInsight]: ../hdinsight-get-started/
  [Approvisionnement de clusters dans HDInsight]: ../hdinsight-provision-clusters/
  [Analyse de sentiments Twitter avec HBase dans HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Présentation du réseau virtuel]: http://msdn.microsoft.com/library/azure/jj156007.aspx
