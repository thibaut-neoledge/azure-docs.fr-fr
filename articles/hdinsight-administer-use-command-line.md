<properties 
	pageTitle="Gestion des clusters Hadoop à l’aide d’une interface de ligne de commande | Microsoft Azure" 
	description="Découvrez comment utiliser l’interface de ligne de commande interplateforme pour gérer les clusters Hadoop dans HDInsight sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>

# Gestion des clusters Hadoop dans HDInsight à l’aide de l’interface de ligne de commande interplateforme

Apprenez à utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows afin de gérer les clusters Hadoop dans Azure HDInsight. L’interface de ligne de commande Azure est implémentée dans Node.js. Elle peut être utilisée sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux.

L’interface de ligne de commande Azure est open source. Le code source est géré dans GitHub sur <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>.

Cet article traite uniquement de l’utilisation de l’interface de ligne de commande dans Windows. Pour une aide générale sur l'utilisation de l'interface de ligne de commande, consultez la page [Utilisation des outils en ligne de commande Azure pour Mac et Linux][azure-command-line-tools].


##Configuration requise

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Abonnement Azure** - Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][azure-purchase-options], les [offres spéciales membres][azure-member-offers] ou la [version d'évaluation gratuite][azure-free-trial].

##Installation
L’interface de ligne de commande peut être installée via le *Gestionnaire de package Node.js (NPM)* ou Windows Installer.

**Pour installer l’interface de ligne de commande au moyen de NPM**

1.	Accédez à **www.nodejs.org**.
2.	Cliquez sur **INSTALLER** et suivez les instructions en conservant les paramètres par défaut.
3.	Ouvrez **Invite de commandes** (ou **Invite de commandes Azure** ou **Invite de commandes développeur pour VS2012**) à partir de votre poste de travail.
4.	Exécutez la commande suivante dans la fenêtre d’invite de commandes :

		npm install -g azure-cli

	> [AZURE.NOTE]Si vous obtenez une erreur indiquant que la commande du NPM est introuvable, vérifiez que les chemins d’accès suivants figurent dans la variable d’environnement **PATH** : <i>C:\\Program Files (x86)\\nodejs;C:\\Users[nomutilisateur]\\AppData\\Roaming\\npm</i> ou <i>C:\\Program Files\\nodejs;C:\\Users[nomutilisateur]\\AppData\\Roaming\\npm</i>


5.	Exécutez la commande suivante pour vérifier l'installation :

		azure hdinsight -h

	Vous pouvez utiliser le commutateur **-h** à différents niveaux pour afficher les informations d'aide. Par exemple :
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Pour installer l’interface de ligne de commande à l’aide de Windows Installer**

1.	Accédez à **http://azure.microsoft.com/downloads/**. 
2.	Faites défiler l’écran pour accéder à la section **Outils en ligne de commande**, puis cliquez sur **Interface de ligne de commande interplateforme** et suivez les étapes de l’Assistant Web Platform Installer.

##Téléchargement et importation d’un fichier de paramètres de publication de compte Azure

Avant d'utiliser l'interface de ligne de commandes, vous devez configurer la connectivité entre votre poste de travail et Azure. L'interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d'Azure dans un fichier de paramètres de publication. Ce dernier peut ensuite être importé en tant que paramètre de configuration local persistant dont l'interface de ligne de commande se servira pour les opérations ultérieures. Vous n’avez besoin d’importer vos paramètres de publication qu’une seule fois.

> [AZURE.NOTE]Le fichier de paramètres de publication contient des informations critiques. Il est recommandé de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés du dossier ou utilisez le chiffrement du lecteur BitLocker.


**Téléchargement et importation du fichier de paramètres de publication**

1.	Ouvrez une invite de commandes.
2.	Exécutez la commande suivante pour télécharger le fichier des paramètres de publication :

		azure account download
 
	![Interface de ligne de commande pour le téléchargement du compte Azure.][image-cli-account-download-import]

	La commande affiche les instructions permettant de télécharger le fichier, ainsi qu’une URL.

3.	Ouvrez Internet Explorer et accédez à l'URL figurant dans la fenêtre d'invite de commandes.
4.	Cliquez sur **Enregistrer** pour enregistrer le fichier sur le poste de travail.
5.	À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

		azure account import <file>

	Dans la capture d’écran précédente, le fichier de paramètres de publication a été enregistré dans le dossier C:\\HDInsight sur le poste de travail.


##Approvisionnement d'un cluster HDInsight

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


HDInsight utilise le conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage est nécessaire avant de pouvoir créer un cluster HDInsight.

Après avoir importé le fichier de paramètres de publication, vous pouvez utiliser la commande suivante pour créer un compte de stockage :

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE]Il doit se trouver dans le même centre de données que le cluster HDInsight.


Pour plus d’informations sur la création d’un compte Azure Storage au moyen du portail Azure, consultez la rubrique [Création, gestion ou suppression d’un compte de stockage][azure-create-storageaccount].

Si vous disposez déjà d’un compte de stockage, mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

	-- Lists Storage accounts
	azure account storage list
	-- Shows a Storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a Storage account
	azure account storage keys list <StorageAccountName>

Pour plus d’informations sur l’obtention d’informations au moyen du portail Azure, consultez la section « Affichage, copie et régénération des clés d’accès de stockage » de la rubrique [Création, gestion ou suppression d’un compte de stockage][azure-create-storageaccount].


La commande **azure hdinsight cluster create** crée le conteneur s'il n'existe pas. Si vous avez choisi de créer le conteneur au préalable, vous pouvez utiliser la commande suivante :

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
		
Une fois que le compte de stockage et le conteneur d’objets blob sont prêts, vous êtes prêt à créer un cluster :

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##Approvisionnement d’un cluster HDInsight au moyen d’un fichier de configuration
Généralement, vous approvisionnez un cluster HDInsight, y exécutez des tâches, puis le supprimez, afin de réduire les coûts. L'interface de ligne de commande vous donne la possibilité d'enregistrer les configurations dans un fichier, de sorte que vous pouvez les réutiliser chaque fois que vous mettez en service un cluster.
 
	azure hdinsight cluster config create <file>
	 
	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
	 
	azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
	       --storageAccountKey "<StorageAccountKey>"
	 
	azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
	 
	azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
	 
	azure hdinsight cluster create --config <file>
		 


![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##Énumération et affichage des détails de cluster
Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :
	
	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>
	
![HDI.CLIListCluster][image-cli-clusterlisting]


##Suppression d'un cluster
Utilisez les commandes suivantes pour supprimer un cluster :

	azure hdinsight cluster delete <ClusterName>




##Étapes suivantes
Dans cet article, vous avez appris comment effectuer différentes tâches d'administration sur un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l’aide du portail Azure][hdinsight-admin-portal]
* [Administration de HDInsight à l’aide d’Azure PowerShell][hdinsight-admin-powershell]
* [Prise en main d’Azure HDInsight][hdinsight-get-started]
* [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows][azure-command-line-tools]


[azure-command-line-tools]: xplat-cli.md
[azure-create-storageaccount]: storage/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Énumération et affichage des clusters"

<!--HONumber=54-->