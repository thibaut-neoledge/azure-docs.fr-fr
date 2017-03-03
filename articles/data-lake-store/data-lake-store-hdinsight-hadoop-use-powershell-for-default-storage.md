---
title: "PowerShell : cluster Azure HDInsight avec Data Lake Store (comme stockage par défaut) | Microsoft Docs"
description: "Utiliser Azure PowerShell pour créer et utiliser les clusters HDInsight avec Azure Data Lake"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/14/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 307070c755cff059b4b82494d28e06cf490a6f7a
ms.openlocfilehash: 6c5badbbea7385cac1407e4af148d5b647af04ac
ms.lasthandoff: 02/16/2017


---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-default-storage"></a>Utilisez Azure PowerShell pour créer un cluster HDInsight avec Data Lake Store (comme stockage par défaut)
> [!div class="op_single_selector"]
> * [Utilisation du portail](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilisation de PowerShell (pour le stockage par défaut)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilisation de PowerShell (pour le stockage supplémentaire)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilisation du gestionnaire des ressources](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Apprenez à utiliser Azure PowerShell pour configurer un cluster HDInsight avec Azure Data Lake Store **comme stockage par défaut**. Pour obtenir des instructions sur la création d’un cluster HDInsight avec Azure Data Lake Store comme stockage supplémentaire, voir [Créer un cluster HDInsight avec Data Lake Store en tant que stockage supplémentaire](data-lake-store-hdinsight-hadoop-use-powershell.md).

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Utilisation de Data Lake Store pour le stockage de cluster HDInsight

Voici quelques considérations importantes pour l’utilisation de HDInsight avec Data Lake Store :

* L’option permettant de créer des clusters HDInsight avec accès au Data Lake Store comme stockage par défaut est disponible si vous utilisez HDInsight version 3.5.

* Pour les clusters HBase (Windows et Linux), Data Lake Store n’est **pas pris en charge** comme option de stockage par défaut ou supplémentaire.


Pour configurer HDInsight afin qu'il fonctionne avec Data Lake Store à l'aide de PowerShell, la procédure est la suivante :

* Créer un Azure Data Lake Store
* Configurer l'authentification pour définir un accès à Data Lake Store en fonction du rôle
* Créer un cluster HDInsight avec authentification à Data Lake Store
* Lancer une tâche de test sur le cluster

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou version ultérieure**. Consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
* **Kit de développement logiciel (SDK) Windows**. Vous pouvez l’installer à partir [d’ici](https://dev.windows.com/en-us/downloads). Il vous permet de créer un certificat de sécurité.
* **Principal du service Azure Active Directory**. Les étapes de ce didacticiel indiquent comment créer un principal du service dans Azure AD. Toutefois, vous devez être administrateur Azure AD pour pouvoir créer un principal du service. Si vous êtes administrateur Azure AD, vous pouvez ignorer ce prérequis et poursuivre le didacticiel.

    **Si vous n’êtes pas administrateur Azure AD**, vous ne pouvez pas effectuer les étapes nécessaires à la création d’un principal du service. Dans ce cas, votre administrateur Azure AD doit d’abord créer un principal du service. Vous pourrez ensuite créer un cluster HDInsight avec Data Lake Store. En outre, le principal du service doit être créé à l’aide d’un certificat, comme décrit dans [Create a service principal with certificate](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) (Créer un principal du service avec certificat).

## <a name="create-an-azure-data-lake-store"></a>Créer un Azure Data Lake Store
Pour créer un Data Lake Store, procédez comme suit.

1. Sur votre bureau, ouvrez une nouvelle fenêtre Azure PowerShell et entrez l'extrait de code suivant. Lorsque vous êtes invité à vous connecter, vérifiez que vous vous connectez en tant qu’administrateur/propriétaire de l’abonnement :

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Si vous recevez une erreur similaire à `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` pendant l’inscription du fournisseur de ressources Data Lake Store, il est possible que votre abonnement ne figure pas dans la liste approuvée pour Azure Data Lake Store. Veillez à activer votre abonnement Azure pour la version préliminaire publique de Data Lake Store en suivant ces [instructions](data-lake-store-get-started-portal.md).
   >
   >
2. Un compte Azure Data Lake Store est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Créer un groupe de ressources Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Créer un groupe de ressources Azure")
3. Créer un compte Azure Data Lake Store. Le nom de compte que vous spécifiez doit contenir uniquement des lettres minuscules et des chiffres.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Créer un compte Azure Data Lake](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Créer un compte Azure Data Lake")
4. Vérifiez que le compte a bien été créé.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Le résultat doit être **True**.

5. L’utilisation de Data Lake Store en tant que stockage par défaut vous oblige à spécifier un chemin d’accès racine où les fichiers spécifiques au cluster sont copiés lors de la création du cluster. Utilisez les applets de commande ci-dessous pour créer un chemin d’accès racine, qui, dans l’extrait de code ci-dessous est **hdiadlcluster/clusters/**.

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurer l'authentification pour définir un accès à Data Lake Store en fonction du rôle
Chaque abonnement Azure est associé à un Azure Active Directory. Les utilisateurs et services qui accèdent aux ressources de l’abonnement avec les API du portail Azure Classic ou d’Azure Resource Manager doivent au préalable s’authentifier avec cette application Azure Active Directory. L’accès est accordé aux abonnements et services Azure en leur affectant le rôle approprié sur une ressource Azure.  Pour les services, un principal du service identifie le service dans Azure Active Directory (AAD). Cette section montre comment accorder l'accès à une ressource Azure (le compte Azure Data Lake Store créé précédemment) à un service d'application, comme HDInsight, en créant un principal du service pour l'application et en lui attribuant des rôles avec Azure PowerShell.

Pour configurer l'authentification Active Directory pour Azure Data Lake, vous devez effectuer les tâches suivantes.

* Créer un certificat auto-signé
* Créer une application dans Azure Active Directory et un principal du service

### <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé
Assurez-vous que le [SDK Windows](https://dev.windows.com/en-us/downloads) est installé avant de suivre la procédure décrite dans cette section. Vous devez également avoir créé un répertoire, comme **C:\mycertdir**, où sera créé le certificat.

1. Dans la fenêtre PowerShell, accédez à l’emplacement où vous avez installé le Kit de développement logiciel (SDK) Windows (en général, `C:\Program Files (x86)\Windows Kits\10\bin\x86`) et utilisez l’utilitaire [MakeCert][makecert] pour créer un certificat auto-signé et une clé privée. Utilisez les commandes suivantes.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        
        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Vous devrez entrer le mot de passe de la clé privée. Une fois la commande exécutée avec succès, **CertFile.cer** et **mykey.pvk** apparaissent dans le répertoire du certificat que vous avez spécifié.
2. Utilisez l’utilitaire [Pvk2Pfx][pvk2pfx] pour convertir en un fichier .pfx les fichiers .pvk et .cer créés par MakeCert. Exécutez la commande ci-dessous.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Lorsque vous y êtes invité, entrez le mot de passe de la clé privée spécifié au préalable. La valeur que vous donnez au paramètre **-po** est le mot de passe associé au fichier .pfx. Une fois la commande exécutée avec succès, CertFile.pfx est visible dans le répertoire du certificat que vous avez spécifié.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Créer une application Azure Active Directory et un principal du service
Cette section décrit les étapes pour créer un principal du service pour une application Azure Active Directory, attribuer un rôle au principal du service et vous authentifier en tant que principal du service en fournissant un certificat. Exécutez les commandes suivantes pour créer une application dans Azure Active Directory.

1. Collez les applets de commande suivantes dans la fenêtre de la console PowerShell. Vérifiez que la valeur que vous donnez à la propriété **-DisplayName** est unique. En outre, les valeurs pour **-HomePage** et **-IdentiferUris** sont des valeurs d’espace réservé et ne sont pas vérifiées.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Créez un principal du service avec l'ID d'application.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Accordez au principal du service l’accès à la racine et à tous les dossiers du chemin d’accès racine spécifié précédemment. Utilisez les applets de commande suivantes.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-default-storage"></a>Création d’un cluster Linux HDInsight avec Data Lake Store comme stockage par défaut

Dans cette section, nous créons un cluster HDInsight Hadoop Linux avec Data Lake Store comme stockage par défaut. Pour cette version, le cluster HDInsight et le Data Lake Store doivent se situer au même emplacement.

1. Commencez par récupérer l’ID du client de l’abonnement. Vous en aurez besoin ultérieurement.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
        
2. Créez le cluster HDInsight. Utilisez les applets de commande suivantes.

        # Set these variables
        
        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                          # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.5" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Après exécution de l'applet de commande, le résultat énumère les détails du cluster.

        
## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Exécuter des tâches de test sur le cluster HDInsight pour utiliser Data Lake Store
Après avoir configuré un cluster HDInsight, vous pouvez exécuter des tâches de test sur le cluster pour vérifier que le cluster HDInsight peut accéder à Data Lake Store. Pour ce faire, nous allons exécuter un exemple de tâche Hive qui crée une table avec les exemples de données déjà disponibles dans Data Lake Store dans **<cluster root>/example/data/sample.log**.

Dans cette section, vous allez utiliser SSH dans le cluster HDInsight Linux que vous avez créé et exécuter l’exemple de requête Hive.

* Si vous utilisez un client Windows pour utiliser SSH dans le cluster, consultez la rubrique [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Si vous utilisez un client Linux pour utiliser SSH dans le cluster, consultez la rubrique [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Une fois connecté, démarrez l'interface de ligne de commande Hive à l'aide de la commande suivante :

        hive
2. À l’aide de l’interface CLI, entrez les instructions suivantes pour créer une table nommée **vehicles** en utilisant les exemples de données dans Data Lake Store :

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Vous devriez voir la sortie de la requête dans la console SSH. 

    > [!NOTE]
       > Le chemin d’accès vers les données d’exemple dans la commande CREATE TABLE ci-dessus est `adl:///example/data/`, où `adl:///` est la racine du cluster. Pour l’exemple de la racine de cluster spécifiée dans ce didacticiel, il s’agit de `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Par conséquent, vous pouvez utiliser l’alternative la plus courte ou fournir le chemin d’accès complet vers la racine du cluster. 
       >
       >



## <a name="access-data-lake-store-using-hdfs-commands"></a>Accéder à Data Lake Store avec les commandes HDFS
Une fois que vous avez configuré le cluster HDInsight pour qu'il utilise Data Lake Store, vous pouvez utiliser les commandes de l'interpréteur de commandes HDFS pour accéder au magasin.

Dans cette section, vous allez utiliser SSH dans le cluster HDInsight Linux que vous avez créé et exécuter les commandes HDFS. 

* Si vous utilisez un client Windows pour utiliser SSH dans le cluster, consultez la rubrique [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Si vous utilisez un client Linux pour utiliser SSH dans le cluster, consultez la rubrique [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Une fois connecté, utilisez la commande du système de fichiers HDFS suivante pour répertorier les fichiers dans le Data Lake Store.

    hdfs dfs -ls adl:///

Vous pouvez également utiliser la commande `hdfs dfs -put` pour charger des fichiers dans Data Lake Store, puis utiliser `hdfs dfs -ls` pour vérifier si les fichiers ont été chargés avec succès.

## <a name="see-also"></a>Voir aussi
* [Portail : Créer un cluster HDInsight pour utiliser Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

