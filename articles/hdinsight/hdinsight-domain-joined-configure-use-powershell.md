---
title: "Configuration des clusters HDInsight joints à un domaine avec Azure PowerShell | Microsoft Docs"
description: "Découvrez comment configurer les clusters HDInsight joints à un domaine à l’aide d’Azure PowerShell"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a13b2f7a-612d-4800-bc92-7fc0524f3e89
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4f8d2956e9f0240392ba839b076d632ccc45d728
ms.openlocfilehash: 3217399ee868707309e96234d4954548b063534a


---
# <a name="configure-domain-joined-hdinsight-clusters-preview-using-azure-powershell"></a>Configurer des clusters HDInsight joints à un domaine (version préliminaire) à l’aide d’Azure PowerShell
Apprenez à configurer un cluster Azure HDInsight avec Azure Active Directory (Azure AD) et [Apache Ranger](http://hortonworks.com/apache/ranger/) à l’aide d’Azure PowerShell. Un script Azure PowerShell est fourni pour rendre la configuration plus rapide et moins susceptible d’engendrer des erreurs. Le cluster HDInsight peut être configuré uniquement sur les clusters Linux. Pour plus d’informations, consultez [Introduire des clusters HDInsight joints à un domaine](hdinsight-domain-joined-introduction.md).

Une configuration de cluster HDInsight jointe à un domaine standard implique les étapes suivantes :

1. Créez un réseau virtuel Azure pour votre instance Azure AD.  
2. Créez et configurez Azure AD et Azure AD AS.
3. Ajoutez une machine virtuelle au réseau virtuel Classic pour la création d’unité d’organisation. 
4. Créez une unité d’organisation pour Azure AD AS.
5. Créez un réseau virtuel HDInsight dans le mode Resource Management Azure.
6. Configurez les zones DNS inversé pour l’instance Azure AD AS.
7. Homologuez les deux réseaux virtuels.
8. Créez un cluster HDInsight.

Le script PowerShell fourni effectue les étapes 3 à 7. Vous devez effectuer les étapes 1 et 2 manuellement.  Si vous préférez ne pas utiliser Azure PowerShell, consultez [Configurer des clusters HDInsight joints à un domaine](hdinsight-domain-joined-configure.md). 

Voici un exemple de la topologie finale :

![Topologie HDInsight jointe à un domaine](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

Azure AD ne prenant en charge que les réseaux virtuels Classic et les clusters HDInsight Linux ne prenant en charge que les réseaux virtuels Azure Resource Manager, l’intégration HDInsight Azure AD requiert deux réseaux virtuels et une homologation mutuelle. Pour bénéficier d’une comparaison entre les deux modèles de déploiement, consultez [Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources](../azure-resource-manager/resource-manager-deployment-model.md). Les deux réseaux virtuels doivent se trouver dans la région de l’instance Azure AD DS.

> [!NOTE]
> Ce didacticiel part du principe que vous ne possédez pas d’instance Azure AD. Si vous en possédez un, vous pouvez passer la section de l’étape 2.
> 
> 

## <a name="prerequisites"></a>Conditions préalables
Vous devez disposer des éléments suivants pour terminer ce didacticiel :

* Familiarisez-vous avec les [services de domaine Azure AD](https://azure.microsoft.com/services/active-directory-ds/), et la structure de [tarification](https://azure.microsoft.com/pricing/details/active-directory-ds/).
* Assurez-vous que votre abonnement est dans la liste approuvée de cette version préliminaire publique. Pour ce faire, envoyez un e-mail à hdipreview@microsoft.com, avec votre ID d’abonnement.
* Un certificat SSL signé par une autorité signataire pour votre domaine. Le certificat est requis pour la configuration du LDAP sécurisé. Les certificats auto-signés ne peuvent pas être utilisés.
* Azure PowerShell.  Consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="create-an-azure-classic-vnet-for-your-azure-ad"></a>Créez un réseau virtuel Azure pour votre instance Azure AD.
Pour plus d’instructions, consultez [ceci](hdinsight-domain-joined-configure.md#create-an-azure-classic-vnet).

## <a name="create-and-configure-azure-ad-and-azure-ad-ds"></a>Créez et configurez Azure AD et Azure AD AS.
Pour plus d’instructions, consultez [ceci](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad).

## <a name="run-the-powershell-script"></a>Exécution du script PowerShell
Le script PowerShell peut être téléchargé à partir de [GitHub](https://github.com/hdinsight/DomainJoinedHDInsight). Décompressez le fichier zip et enregistrez les fichiers localement.

**Pour modifier le script PowerShell**

1. Ouvrez run.ps1 à l’aide de Windows PowerShell ISE ou n’importe quel éditeur de texte.
2. Renseignez les valeurs pour les variables suivantes :
   
   * **$SubscriptionName** – le nom de l’abonnement Azure dans lequel vous souhaitez créer votre cluster HDInsight. Vous avez déjà créé un réseau virtuel Classic dans cet abonnement et allez créer un réseau virtuel Azure Resource Manager pour le cluster HDInsight sous abonnement.
   * **$ClassicVNetName** - le réseau virtuel classique qui contient le Azure AD DS. Ce réseau virtuel doit se trouver dans le même abonnement que celui fourni ci-dessus. Ce réseau virtuel doit être créé à l’aide du portail Azure, et non du portail Classic. Si vous suivez les instructions de [Configurer des clusters HDInsight joints à un domaine (version préliminaire)](hdinsight-domain-joined-configure.md#create-an-azure-classic-vnet), le nom par défaut devrait être contosoaadvnet.
   * **$ClassicResourceGroupName** – nom du groupe Resource Manager pour le réseau virtuel classique mentionné ci-dessus. Par exemple contosoaadrg. 
   * **$ArmResourceGroupName** – nom du groupe de ressources dans lequel vous souhaitez créer le cluster HDInsight. Vous pouvez utiliser le même groupe de ressources que pour $ArmResourceGroupName.  Si le groupe de ressources n’existe pas, le script le crée.
   * **$ArmVNetName** - nom du réseau virtuel Resource Manager dans lequel vous souhaitez créer le cluster HDInsight. Ce réseau virtuel sera placé dans $ArmResourceGroupName.  Si le réseau virtuel n’existe pas, le script PowerShell le crée. S’il existe, il doit faire partie du groupe de ressources que vous fournissez ci-dessus.
   * **$AddressVnetAddressSpace** – l’espace d’adressage du réseau pour le réseau virtuel de Resource Manager. Assurez-vous que cet espace d’adressage est disponible. Cet espace d'adressage ne peut pas chevaucher l'espace d'adressage classique du réseau local. Par exemple : “10.1.0.0/16”
   * **$ArmVnetSubnetName** - nom du sous-réseau du réseau virtuel Resource Manager dans lequel vous souhaitez placer les machines virtuelles du cluster HDInsight. Si le sous-réseau n’existe pas, le script PowerShell le crée. S’il existe, il doit faire partie du réseau virtuel que vous fournissez ci-dessus.
   * **$AddressSubnetAddressSpace** – la plage d’adressage du réseau pour le sous-réseau du réseau virtuel de Resource Manager. Les adresses IP de machines virtuelles du cluster HDInsight proviendront de cette plage d’adresses de sous-réseau. Par exemple : “10.1.0.0/24”.
   * **$ActiveDirectoryDomainName** – le nom de domaine Azure AD auquel vous souhaitez joindre les machines virtuelles du cluster HDInsight. Par exemple, “contoso.onmicrosoft.com”
   * **$ClusterUsersGroups** – le nom commun des groupes de sécurité de l’AD que vous souhaitez synchroniser avec le cluster HDInsight. Les utilisateurs de ce groupe de sécurité seront en mesure d’ouvrir une session sur le tableau de bord du cluster à l’aide de leurs informations d’identification au domaine Active Directory. Ces groupes de sécurité doivent exister dans Active Directory. Par exemple, « hiveusers » ou « clusteroperatorusers ».
   * **$OrganizationalUnitName** - l’unité d’organisation dans le domaine dans lequel vous souhaitez placer les machines virtuelles du cluster HDInsight et les principaux du service utilisés par le cluster. Le script PowerShell crée cette unité d’organisation si elle n’existe pas. Par exemple, « HDInsightOU ».
3. Enregistrez les modifications.

**Pour exécuter le script**

1. Exécutez **Windows PowerShell** en tant qu’administrateur.
2. Accédez au dossier de run.ps1. 
3. Exécutez le script en tapant le nom du fichier et en appuyant sur **ENTRÉE**.  3 boîtes de dialogue de connexion s’affichent :
   
   1. **Connexion au portail Azure Classic** – saisissez les informations d’identification qui vous permettent de vous connecter au portail Azure Classic. Vous devez avoir créé Azure AD et Azure AD DS à l’aide de ces informations d’identification.
   2. **Connexion au portail Azure Resource Manager** – saisissez les informations d’identification qui vous permettent de vous connecter au portail Azure Resource Manager.
   3. **Nom d’utilisateur de domaine** – saisissez les informations d’identification du nom d’utilisateur de domaine pour lequel vous souhaitez être un administrateur sur le cluster HDInsight. Si vous avez créé un répertoire Azure AD à partir de rien, vous devez avez probablement créé cet utilisateur à l’aide de cette documentation. 
      
      > [!IMPORTANT]
      > Saisissez le nom d’utilisateur au format : 
      > 
      > Nomdomaine\nomutilisateur (par exemple contoso.onmicrosoft.com\clusteradmin)
      > 
      > 
      
      Cet utilisateur doit disposer de 3 privilèges : joindre des machines au domaine Active Directory fourni ; créer des principaux du service et des objets ordinateur dans l’unité d’organisation fournie ; et ajouter des règles de proxy DNS inversé.

Lors de la création des zones DNS inversées, le script vous invite à saisir un ID de réseau. Cet ID de réseau doit être le préfixe d’adresse du réseau virtuel de Resource Manager. Par exemple, si l’espace d’adressage du sous-réseau du réseau virtuel de Resource Manager est 10.2.0.0/24, saisissez 10.2.0.0/24 lorsque l’outil vous invite à saisir l’ID de réseau. 

## <a name="create-hdinsight-cluster"></a>Créer un cluster HDInsight
Dans cette section, vous créez un cluster Hadoop Linux dans HDInsight à l’aide du portail Azure ou du [modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Pour d'autres méthodes de création de cluster et comprendre les paramètres, consultez [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Pour plus d’informations sur l’utilisation d’un modèle Resource Manager pour créer des clusters Hadoop dans HDInsight, consultez [Création de clusters Hadoop dans HDInsight à l’aide de modèles Azure Resource Manager](hdinsight-hadoop-create-windows-clusters-arm-templates.md).

**Pour créer un cluster HDInsight joint à un domaine à l’aide du portail Azure**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau**, **Intelligence et analyse**, puis sur **HDInsight**.
3. Dans le panneau **Nouveau cluster HDInsight**, entrez ou sélectionnez les valeurs suivantes :
   
   * **Nom du cluster** : saisissez un nom pour le cluster HDInsight joint au domaine.
   * **Abonnement** : sélectionnez l’abonnement Azure utilisé pour créer ce cluster.
   * **Configuration du cluster** :
     
     * **Type de cluster** : Hadoop Actuellement, le cluster HDInsight joint au domaine est uniquement pris en charge sur les clusters Hadoop.
     * **Système d’exploitation** : Linux  Le cluster HDInsight joint au domaine est pris en charge uniquement sur les clusters HDInsight Linux.
     * **Version** : Hadoop 2.7.3 (HDI 3.5). Le cluster HDInsight joint au domaine est pris en charge uniquement sur le cluster HDInsight version 3.5.
     * **Type de cluster** : PREMIUM
       
       Pour enregistrer les modifications, cliquez sur **Sélectionner**.
   * **Informations d’identification** : configurez les informations d’identification pour les utilisateurs du cluster et SSH.
   * **Source de données** : créez un compte de stockage ou utilisez un compte de stockage existant en tant que compte de stockage par défaut pour le cluster HDInsight. L’emplacement doit être celui des deux réseaux virtuels.  Il s’agit également de l’emplacement du cluster HDInsight.
   * **Tarification** : sélectionnez le nombre de nœuds de travail de votre cluster.
   * **Configurations avancées** : 
     
     * **Jonction de domaine et réseau virtuel/sous-réseau** : 
       
       * **Paramètres du domaine** : 
         
         * **Nom du domaine** : contoso.onmicrosoft.com
         * **Mot de passe utilisateur du domaine** :saisissez un nom d’utilisateur du domaine. Ce domaine doit présenter les privilèges suivants : joignez les machines aux domaines et placez-les dans l’unité d’organisation configurée précédemment ; créez les principaux du service au sein de l’unité d’organisation configurée précédemment ; créez les entrées du DNS inversé. Cet utilisateur de domaine devient l’administrateur de ce cluster HDInsight joint à un domaine.
         * **Mot de passe du domaine** : entrez le mot de passe de l’utilisateur du domaine.
         * **Unité d’organisation** : saisissez le nom unique de l’unité d’organisation configurée précédemment. Par exemple : UO = HDInsightOU, CD = contoso, CD = onmicrosoft, CD = com
         * **URL LDAPS** : ldaps://contoso.onmicrosoft.com:636
         * **Access user group (Accéder au groupe d’utilisateurs)** : spécifiez le groupe de sécurité dont vous souhaitez synchroniser les utilisateurs au cluster. Par exemple, HiveUsers.
           
           Pour enregistrer les modifications, cliquez sur **Sélectionner**.
           
           ![Configurer les paramètres du domaine du portail HDInsight joint](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Réseau virtuel** : contosohdivnet
       * **Sous-réseau** : Subnet1
         
         Pour enregistrer les modifications, cliquez sur **Sélectionner**.        
         Pour enregistrer les modifications, cliquez sur **Sélectionner**.
   * **Groupe de ressources** : sélectionnez le groupe de ressources utilisé pour le réseau virtuel HDInsight (contosohdirg).
4. Cliquez sur **Create**.  

Pour créer le cluster HDInsight joint au domaine, vous pouvez également utiliser le modèle de gestion des ressources Azure. Appliquez la procédure suivante :

**Pour créer un cluster joint à un domaine à l’aide du modèle de gestion des ressources**

1. Cliquez sur l’image suivante pour ouvrir un modèle Resource Manager dans le portail Azure. Le modèle Resource Manager est situé dans un conteneur blob public. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure-use-powershell/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. À partir du panneau **Paramètres**, saisissez les informations suivantes :
   
   * **Abonnement** : (sélectionnez votre abonnement Azure.)
   * **Groupe de ressources** : cliquez sur **Use existing (Utiliser l’existant)**, et spécifiez le groupe de ressources utilisé.  Par exemple, contosohdirg. 
   * **Emplacement** : spécifiez un emplacement de groupe de ressources.
   * **Nom du cluster** : saisissez un nom pour le cluster Hadoop créé. Par exemple, contosohdicluster.
   * **Type de cluster** : sélectionnez un type de cluster.  La valeur par défaut est **hadoop**.
   * **Emplacement** : sélectionnez un emplacement pour le cluster.  Le compte de stockage par défaut utilise le même emplacement.
   * **Cluster Worker Node count (Nombre de nœuds de travail du cluster)** : sélectionnez le nombre de nœuds de travail.
   * **Nom d’utilisateur et mot de passe de cluster** : le nom de connexion par défaut est **admin**.
   * **Nom d’utilisateur SSH et mot de passe** : le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez le renommer. 
   * **ID réseau virtuel** : /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>
   * **Sous-réseau du réseau virtuel** : /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>/subnets/Subnet1
   * **Nom de domaine** : contoso.onmicrosoft.com
   * **Organization Unit DN (Nom de domaine de l’unité d’organisation)** : UO = HDInsightOU,CD = contoso, CD = onmicrosoft, CD = com
   * **Cluster Users Group D Ns**: "\"NC = HiveUsers, UO = AADDC Users,CD =<DomainName>, CD = onmicrosoft,CD = com\""
   * **LDAPUrls** : ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (saisissez le nom d’utilisateur de l’administrateur du domaine)
   * **DomainAdminPassword** : (saisissez le mot de passe utilisateur de l’administrateur du domaine)
   * **J’accepte les termes et conditions mentionnés ci-dessus **: (vérification)
   * **Épingler au tableau de bord** : (vérification)
3. Cliquez sur **Achat**. Vous verrez une nouvelle vignette intitulée **Déploiement du déploiement de modèle**. La création d’un cluster prend environ 20 minutes. Une fois le cluster créé, vous pouvez cliquer sur le panneau du cluster dans le portail pour l’ouvrir.

Après avoir terminé ce didacticiel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Pour obtenir des instructions sur la suppression d’un cluster, consultez [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configuration de stratégies Hive pour les clusters HDInsight joints à un domaine](hdinsight-domain-joined-run-hive.md).
* Pour utiliser des clusters HDInsight joints à un domaine, consultez [Utilisation de SSH avec Hadoop sous Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).




<!--HONumber=Dec16_HO3-->


