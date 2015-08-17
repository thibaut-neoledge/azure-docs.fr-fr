<properties
	pageTitle="Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure | Microsoft Azure"
	description="Découvrez la façon d'administrer le service HDInsight. Créez un cluster HDInsight, ouvrez la console JavaScript interactive et ouvrez la console de commandes Hadoop."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>


<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="jgao"/>


# Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure

Le [portail Azure](https://manage.windowsazure.com) vous permet d’approvisionner des clusters Hadoop dans Azure HDInsight, de modifier le mot de passe utilisateur Hadoop et d’activer le protocole RDP (Remote Desktop Protocol) afin d’accéder à la console de commandes Hadoop sur le cluster.

## Autres outils d’administration de HDInsight
Outre le portail Azure, d’autres outils sont également disponibles pour administrer HDInsight.

- Pour plus d’informations sur l’administration de HDInsight avec Azure PowerShell, consultez la rubrique [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md).

- Pour plus d’informations sur l’administration de HDInsight à l’aide de l’interface de ligne de commande Azure, consultez la rubrique [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md).

##Composants requis

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Compte Azure Storage** - Le cluster HDInsight utilise un conteneur de stockage d’objets blob Azure comme système de fichiers par défaut. Pour plus d’informations sur l’expérience transparente offerte par le stockage d’objets blob Azure avec les clusters HDInsight, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md). Pour plus d’informations sur la création d’un compte Azure Storage, consultez la page [Création d’un compte de stockage](../storage-create-storage-account.md).


##Configuration de clusters HDInsight

Vous pouvez approvisionner des clusters HDInsight à partir du portail Azure à l’aide de l’option Création rapide ou Création personnalisée. Consultez les liens suivants pour obtenir des instructions :

- [Approvisionnement d’un cluster à l’aide de l’option Création rapide](../hdinsight-get-started.md#provision)
- [Approvisionnement d’un cluster à l’aide de l’option Création personnalisée](hdinsight-provision-clusters.md#portal)

[AZURE.INCLUDE [liste de centre de données](../../includes/hdinsight-pricing-data-centers-clusters.md)]


##Personnalisation de clusters HDInsight

HDInsight fonctionne avec un large éventail de composants Hadoop. Pour obtenir la liste des composants vérifiés et pris en charge, consultez la rubrique [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md). Vous pouvez personnaliser HDInsight en utilisant l’une des options suivantes :

- Utilisez Action de script pour exécuter des scripts personnalisés pouvant personnaliser un cluster afin de modifier la configuration de cluster ou d’installer des composants personnalisés tels que Giraph ou Solr. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster.md).
- Utilisez les paramètres de personnalisation de cluster du Kit de développement logiciel (SDK) HDInsight .NET ou Azure PowerShell pendant l’approvisionnement du cluster. Les modifications apportées à la configuration sont ainsi conservées pendant toute la durée de vie du cluster et ne sont pas affectées par les réinitialisations des nœuds du cluster qu’exécute régulièrement la plateforme Azure à des fins de maintenance. Pour plus d'informations sur l'utilisation des paramètres de personnalisation des clusters, consultez la rubrique [Approvisionnement de clusters HDInsight](hdinsight-provision-clusters.md).
- Certains composants Java natifs, comme Mahout et Cascading, peuvent être exécutés sur le cluster en tant que fichiers JAR. Ces derniers peuvent être distribués au stockage d’objets blob Azure et envoyés aux clusters HDInsight à l’aide des mécanismes d’envoi de tâches Hadoop. Pour plus d’informations, consultez la rubrique [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md).


	>[AZURE.NOTE]En cas de problèmes lors du déploiement ou de l’appel des fichiers JAR sur les clusters HDInsight, contactez le [support Microsoft](http://azure.microsoft.com/support/options/).

	> Cascading n'est pas pris en charge par HDInsight et ne peut pas bénéficier du support Microsoft. Pour obtenir la liste des composants pris en charge, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight.](hdinsight-component-versioning.md).


L’installation de logiciels personnalisés sur le cluster à l’aide d’une connexion Bureau à distance n’est pas prise en charge. Évitez de stocker des fichiers sur les disques du nœud principal, car vous les perdrez si vous devez recréer les clusters. Il est recommandé de stocker les fichiers sur le stockage d’objets blob Azure. Le stockage d'objets blob est permanent.

##Modification du nom d’utilisateur et du mot de passe d’un cluster HDInsight
Un cluster HDInsight peut disposer de deux comptes d'utilisateur. Le compte d’utilisateur du cluster HDInsight est créé lors du processus d’approvisionnement. Vous pouvez également créer un compte d’utilisateur RDP pour accéder au cluster via RDP. Consultez la rubrique [Activation du Bureau à distance](#connect-to-hdinsight-clusters-by-using-rdp).

**Modification du nom d’utilisateur et du mot de passe d’un cluster HDInsight**

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com/).
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters HDInsight déployés s'affiche.
3. Cliquez sur le cluster HDInsight dont vous souhaitez réinitialiser le nom d’utilisateur et le mot de passe.
4. En haut de la page, cliquez sur **CONFIGURATION**.
5. Cliquez sur **OFF** en regard de **SERVICES HADOOP**.
6. En bas de la page, cliquez sur **ENREGISTRER**, puis attendez la fin de la désactivation.
7. Une fois le service désactivé, cliquez sur **ON** en regard de **SERVICES HADOOP**.
8. Pour **NOM D’UTILISATEUR** et **NOUVEAU MOT DE PASSE**, saisissez le nouveau nom d’utilisateur et le nouveau mot de passe (respectivement) pour le cluster.
8. Cliquez sur **ENREGISTRER**.


##Connexion à des clusters HDInsight à l’aide de RDP

Les informations d’identification du cluster fournies lors de sa création donnent accès aux services disponibles sur le cluster, mais pas au cluster proprement dit via le Bureau à distance. Par défaut, l’accès au Bureau à distance est désactivé. Par conséquent, l’accès direct au cluster nécessite une configuration supplémentaire après l’opération de création.

**Activation du Bureau à distance**

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com/).
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters HDInsight déployés s'affiche.
3. Cliquez sur le cluster HDInsight auquel vous souhaitez vous connecter.
4. En haut de la page, cliquez sur **CONFIGURATION**.
5. En bas de la page, cliquez sur **ACTIVER DISTANT**.
6. Dans l’Assistant **Configurer le Bureau à distance**, entrez un nom d’utilisateur et un mot de passe pour le Bureau à distance. Notez que le nom d’utilisateur doit être différent de celui utilisé pour créer le cluster (**admin** par défaut avec l’option Création rapide). Entrez une date d'expiration dans la zone **DATE D'EXPIRATION**. Notez que la date d'expiration doit être postérieure à la date actuelle (jusqu’à 90 jours). Par défaut, l'heure d'expiration est définie sur minuit. Cliquez ensuite sur l'icône en forme de coche.

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]


> [AZURE.NOTE]Vous pouvez également utiliser le Kit de développement logiciel (SDK) .NET HDInsight pour activer le Bureau à distance sur un cluster. Utilisez la méthode **ActiverRdp** sur l’objet client HDInsight de la manière suivante : **client.EnableRdp(nom cluster, emplacement, "utilisateurrdp", "motdepasserdp", DateHeure.Now.AddDays(6))**. De la même manière, pour désactiver le Bureau à distance sur le cluster, vous pouvez utiliser **client.DisableRdp(nom cluster, emplacement)**. Pour plus d’informations sur ces méthodes, consultez la rubrique [Référence du Kit de développement logiciel (SDK) HDInsight .NET](http://go.microsoft.com/fwlink/?LinkId=529017). Cela s’applique uniquement aux clusters HDInsight fonctionnant sous Windows.



> [AZURE.NOTE]Une fois RDP activé pour un cluster, vous devez actualiser la page avant de pouvoir vous connecter au cluster.

**Pour vous connecter à un cluster à l’aide de RDP**

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com/).
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters HDInsight déployés s'affiche.
3. Cliquez sur le cluster HDInsight auquel vous souhaitez vous connecter.
4. En haut de la page, cliquez sur **CONFIGURATION**.
5. Cliquez sur **CONNEXION**, puis suivez les instructions.

##Créer un certificat auto-signé

Si vous voulez effectuer des opérations dans le cluster au moyen du Kit de développement logiciel (SDK) .NET, vous devez créer un certificat auto-signé sur le poste de travail, et également télécharger le certificat sur votre abonnement Azure. Il s'agit d'une tâche qui ne doit être exécutée qu'une seule fois. Vous pouvez installer le même certificat sur d'autres ordinateurs, pour autant que le certificat soit valide.

**Pour créer un certificat auto-signé**

1. Créez un certificat auto-signé permettant d'authentifier les demandes. Vous pouvez utiliser les services Internet (IIS) ou [makecert](http://go.microsoft.com/fwlink/?LinkId=534000) pour créer le certificat.

2. Accédez à l'emplacement du certificat, cliquez avec le bouton droit sur ce dernier, cliquez sur **Installer le certificat**, et installez le certificat dans le magasin personnel de l'ordinateur. Modifiez les propriétés du certificat pour lui affecter un nom convivial.

3. Importez le certificat dans le portail Azure. À partir du portail, cliquez sur **PARAMÈTRES** dans la partie inférieure gauche de la page, puis cliquez sur **CERTIFICATS DE GESTION**. Dans le bas de la page, cliquez sur **TÉLÉCHARGER** et suivez les instructions pour télécharger le fichier .cer que vous avez créé à l’étape précédente.

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##Octroi/révocation de l'accès aux services HTTP

Les clusters HDInsight disposent des services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Par défaut, l'accès à ces services est octroyé. Vous pouvez révoquer/octroyer l’accès à partir du portail Azure.

>[AZURE.NOTE]En octroyant/révoquant l’accès, vous réinitialisez le nom d’utilisateur et le mot de passe du cluster.

**Pour octroyer/révoquer l’accès aux services web HTTP**

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com/).
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters HDInsight déployés s'affiche.
3. Cliquez sur le cluster HDInsight que vous souhaitez configurer.
4. En haut de la page, cliquez sur **CONFIGURATION**.
5. Cliquez sur **ON** ou **OFF** en regard de **SERVICES HADOOP**.  
6. Pour **NOM D’UTILISATEUR** et **NOUVEAU MOT DE PASSE**, saisissez le nouveau nom d’utilisateur et le nouveau mot de passe (respectivement) pour le cluster.
7. Cliquez sur **ENREGISTRER**.

Vous pouvez également utiliser les applets de commande Azure PowerShell :

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Consultez la rubrique [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md).

##Ouvrez une ligne de commande Hadoop.

Pour vous connecter au cluster à l’aide du Bureau à distance et utiliser la ligne de commande Hadoop, vous devez d’abord activer l’accès Bureau à distance sur le cluster, comme décrit à la section précédente.

**Pour ouvrir une ligne de commande Hadoop**

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com/).
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. Une liste de clusters Hadoop déployés s'affiche.
3. Cliquez sur le cluster HDInsight auquel vous souhaitez vous connecter.
3. En haut de la page, cliquez sur **CONFIGURATION**.
4. En bas de la page, cliquez sur **CONNECTER**.
5. Cliquez sur **Ouvrir**.
6. Entrez vos informations d'identification, puis cliquez sur **OK**. Utilisez le nom d’utilisateur et le mot de passe que vous avez configurés lors de la création du cluster.
7. Cliquez sur **Oui**.
8. À partir du Bureau, double-cliquez sur **Hadoop Command Line**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Pour plus d’informations sur les commandes Hadoop, consultez la rubrique [Référence aux commandes Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Sur la capture d’écran précédente, le numéro de la version d’Hadoop est intégré au nom du dossier. Il peut être modifié en fonction de la version des composants Hadoop installés sur le cluster. Vous pouvez utiliser des variables d'environnement Hadoop pour faire référence à ces dossiers. Par exemple :

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%

##Mise à l’échelle des clusters
Consultez la rubrique [Mise à l’échelle des clusters Hadoop dans HDInsight](hdinsight-hadoop-cluster-scaling.md).

##Étapes suivantes
Cet article vous a appris à créer un cluster HDInsight à l’aide du portail Azure et à ouvrir l’outil en ligne de commande Hadoop. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)
* [Approvisionnement des clusters HDInsight](hdinsight-provision-clusters.md)
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Prise en main d’Azure HDInsight](../hdinsight-get-started.md)
* [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md)

[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Page d’accueil du cluster"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Ligne de commande Hadoop"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png
 

<!---HONumber=August15_HO6-->