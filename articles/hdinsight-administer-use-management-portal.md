<properties 
	pageTitle="Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure | Azure" 
	description="Découvrez la façon d'administrer le service HDInsight. Créez un cluster HDInsight, ouvrez la console JavaScript interactive et ouvrez la console de commandes Hadoop.." 
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
	ms.date="11/21/2014" 
	ms.author="jgao"/>



# Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure

Le portail de gestion Azure vous permet d'approvisionner des clusters Hadoop dans HDInsight, de modifier le mot de passe utilisateur Hadoop et d'activer RDP afin d'accéder à la console de commandes Hadoop sur le cluster. Outre le portail de gestion, d'autres outils sont également disponibles pour administrer HDInsight. 

- Pour plus d'informations sur l'administration de HDInsight avec Azure PowerShell, consultez la rubrique [Administration de HDInsight à l'aide de PowerShell][hdinsight-admin-powershell].

- Pour plus d'informations sur l'administration de HDInsight avec les outils en ligne de commande interplateforme, consultez la rubrique [Administration de HDInsight à l'aide de l'interface de ligne de commande interplateforme][hdinsight-admin-cross-platform]. 

**Conditions préalables :**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Abonnement Azure**. Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les pages traitant des [formules d'abonnement][azure-purchase-options], des [offres spéciales membres][azure-member-offers], ou de la [version d'évaluation gratuite][azure-free-trial].


##Dans cet article

* [Approvisionnement de clusters HDInsight](#create)
* [Personnalisation de clusters HDInsight](#customize)
* [Modification du nom d'utilisateur et du mot de passe d'un cluster HDInsight](#password)
* [Connexion à des clusters HDInsight à l'aide de RDP](#rdp)
* [Créer un certificat auto-signé](#cert)
* [Octroi/révocation de l'accès aux services HTTP](#httpservice)
* [Ouverture de la console de commandes Hadoop](#hadoopcmd)
* [Étapes suivantes](#nextsteps)

##<a id="create"></a> Approvisionnement de clusters HDInsight

Plusieurs méthodes sont disponibles pour créer des clusters HDInsight. Cet article s'applique uniquement à l'utilisation de l'option Création rapide du portail de gestion Azure. Pour plus d'informations sur les autres options, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision].

Le cluster HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Pour plus d'informations sur l'expérience transparente offerte par le stockage d'objets blob Azure avec les clusters HDInsight, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

Un compte de stockage Azure doit être créé dans le centre de données où le cluster HDInsight est approvisionné. Les clusters HDInsight peuvent actuellement être approvisionnés dans cinq centres de données : 

- Asie du Sud-Est 
- Europe du Nord
- Europe de l'Ouest 
- Est des États-Unis 
- Ouest des États-Unis 

Pour plus d'informations sur la création d'un compte de stockage Azure, consultez la page [Création d'un compte de stockage][azure-create-storageaccount].


**Approvisionnement d'un cluster HDInsight**

1. Connectez-vous au [Portail de gestion Azure][azure-management-portal].
2. Cliquez sur **NOUVEAU** au bas de la page, cliquez sur **SERVICES DE DONNÉES**, sur **HDINSIGHT**, puis sur **CRÉATION RAPIDE**.

3. Renseignez les champs **Nom du cluster**, **Taille du cluster**, **Mot de passe d'administration de cluster** et **Compte de stockage** Azure, puis cliquez sur **Créer un cluster HDInsight**. Une fois le cluster créé et exécuté, l'état indique  *Running*.

	![HDI.QuickCreate][image-cluster-quickcreate]

	Lorsque l'option Création rapide est utilisée pour créer un cluster, le nom d'utilisateur par défaut du compte d'utilisateur Hadoop est *admin*. Pour attribuer un autre nom d'utilisateur au compte, vous pouvez utiliser l'option Création personnalisée à la place de l'option Création rapide. Vous pouvez également changer le nom du compte après son approvisionnement.

	Lorsque l'option Création rapide est utilisée pour créer un cluster, un nouveau conteneur portant le nom du cluster HDInsight est automatiquement créé sur le compte de stockage spécifié. Si vous souhaitez personnaliser le nom du conteneur à utiliser par défaut par le cluster, vous devez utiliser l'option Création personnalisée. 

	> [AZURE.NOTE] Une fois le compte de stockage Azure choisi pour votre cluster HDInsight, le seul moyen de le modifier consiste à supprimer le cluster et à en créer un autre avec le compte de stockage souhaité.

4. Cliquez sur le cluster qui vient d'être créé.  La page d'accueil s'affiche :

	![HDI.ClusterLanding][image-cluster-landing]


##<a id="customize"></a> Personnalisation de clusters HDInsight

HDInsight fonctionne avec un large éventail de composants Hadoop. Pour obtenir la liste des composants vérifiés et pris en charge, consultez la rubrique [Version de Hadoop dans Azure HDInsight][hdinsight-versions]. Pour personnaliser HDInsight, utilisez une des options suivantes :

- Utilisez les paramètres de personnalisation de cluster du Kit de développement logiciel (SDK) HDInsight .NET ou Azure PowerShell pendant l'approvisionnement du cluster. Les modifications apportées à la configuration seront ainsi conservées pendant toute la durée de vie du cluster et ne seront pas affectées par les réinitialisations des nœuds du cluster qu'exécute régulièrement la plateforme Azure à des fins de maintenance. Pour plus d'informations sur l'utilisation des paramètres de personnalisation des clusters, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision].
- Certains composants Java natifs, comme Mahout ou Cascading, peuvent être exécutés sur le cluster en tant que fichiers JAR. Ces derniers peuvent être distribués au stockage d'objets blob Azure (WASB) et envoyés aux clusters HDInsight à l'aide des mécanismes d'envoi de tâches Hadoop. Pour plus d'informations, consultez la rubrique [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]. 


	>[AZURE.NOTE] En cas de problèmes lors du déploiement ou de l'appel des fichiers .jar sur les clusters HDInsight, contactez le [support Microsoft][hdinsight-support].
	
	> Cascading n'est pas pris en charge par HDInsight et ne peut pas bénéficier du support Microsoft. Pour obtenir la liste des composants pris en charge, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][hdinsight-versions].


L'installation de logiciels personnalisés sur le cluster à l'aide d'une connexion Bureau à distance n'est pas prise en charge. Évitez de stocker des fichiers sur les disques du nœud principal, car vous les perdrez si vous devez recréer les clusters. Il est recommandé de stocker les fichiers sur le stockage d'objets blob Azure. Le stockage d'objets blob est permanent.

##<a id="password"></a> Modification du nom d'utilisateur et du mot de passe d'un cluster HDInsight
Un cluster HDInsight peut disposer de deux comptes d'utilisateur.  Le compte d'utilisateur du cluster HDInsight est créé lors du processus d'approvisionnement.  Vous pouvez également créer un compte d'utilisateur RDP pour accéder au cluster via RDP. Consultez la rubrique [Activation du Bureau à distance](#enablerdp).

**Pour modifier le nom d'utilisateur et le mot de passe du cluster HDInsight**

1. Connectez-vous au [Portail de gestion Azure][azure-management-portal].
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters HDInsight déployés s'affiche.
3. Cliquez sur le cluster HDInsight dont vous souhaitez réinitialiser le nom d'utilisateur et le mode de passe.
4. En haut de la page, cliquez sur **CONFIGURATION**.
5. Cliquez sur **OFF** en regard  de **SERVICES HADOOP**.
6. En bas de la page, cliquez sur **ENREGISTRER**, puis attendez la fin de la désactivation.
7. Une fois le service désactivé, cliquez sur **ON** en regard de **SERVICES HADOOP**.
8. Entrez un **NOM D'UTILISATEUR** et un **NOUVEAU MOT DE PASSE**.  Il s'agira des nouveaux nom d'utilisateur et mot de passe du cluster.
8. Cliquez sur **ENREGISTRER**.


##<a id="rdp"></a> Connexion à des clusters HDInsight à l'aide de RDP

Les informations d'identification du cluster fournies lors de sa création donnent accès aux services disponibles sur le cluster, mais pas au cluster proprement dit via le Bureau à distance. Par défaut, l'accès au Bureau à distance est désactivé. Par conséquent, l'accès direct au cluster nécessite une configuration supplémentaire après l'opération de création.

**Pour activer le Bureau à distance**

1. Connectez-vous au [Portail de gestion Azure][azure-management-portal].
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters HDInsight déployés s'affiche.
3. Cliquez sur le cluster HDInsight auquel vous souhaitez vous connecter.
4. En haut de la page, cliquez sur **CONFIGURATION**.
5. En bas de la page, cliquez sur **ACTIVER DISTANT**.
6. Dans l'Assistant **Configurer le Bureau à distance**, entrez un nom d'utilisateur et un mot de passe pour le Bureau à distance. Notez que le nom d'utilisateur et le mot de passe doivent être différents de ceux utilisés pour créer le cluster (*admin* par défaut avec l'option Création rapide). Entrez une date d'expiration dans la zone **DATE D'EXPIRATION**. Notez que la date d'expiration doit être postérieure à la date actuelle (une semaine maximum). Par défaut, l'heure d'expiration est définie sur minuit. Cliquez ensuite sur l'icône en forme de coche.

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]

	La date d'expiration doit être postérieure à la date actuelle (sept jours maximum). L'heure est définie sur minuit.

> [AZURE.NOTE] Une fois RDP activé pour un cluster, vous devez actualiser la page avant de pouvoir vous connecter au cluster.
 
**Pour vous connecter à un cluster à l'aide de RDP**

1. Connectez-vous au [Portail de gestion Azure][azure-management-portal].
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters HDInsight déployés s'affiche.
3. Cliquez sur le cluster HDInsight auquel vous souhaitez vous connecter.
4. En haut de la page, cliquez sur **CONFIGURATION**.
5. Cliquez sur **CONNEXION**, puis suivez les instructions.

##<a id="cert"></a>Créer un certificat auto-signé

Si vous voulez effectuer des opérations dans le cluster au moyen du Kit de développement logiciel (SDK) .NET, vous devez créer un certificat auto-signé sur le poste de travail, et également télécharger le certificat sur votre abonnement Azure. Il s'agit d'une tâche qui ne doit être exécutée qu'une seule fois. Vous pouvez installer le même certificat sur d'autres ordinateurs, pour autant que le certificat soit valide.

**Pour créer un certificat auto-signé**

1. Créez un certificat auto-signé permettant d'authentifier les demandes. Vous pouvez utiliser IIS ou [makecert][makecert-info] pour créer le certificat.
 
2. Accédez à l'emplacement du certificat, cliquez avec le bouton droit sur ce dernier, cliquez sur **Installer le certificat**, puis installez le certificat dans le magasin personnel de l'ordinateur. Modifiez les propriétés du certificat pour lui affecter un nom convivial.

3. Importez le certificat dans le portail de gestion Azure. À partir du portail, cliquez sur **Paramètres** dans la partie inférieure gauche de la page, puis cliquez sur **Certificats de gestion**. Dans la partie inférieure de la page, cliquez sur **Télécharger** et suivez les instructions de téléchargement du fichier .cer que vous avez créé à l'étape précédente.

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##<a id="httpservice"></a> Octroi/révocation de l'accès aux services HTTP

Les clusters HDInsight disposent des services Web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Par défaut, l'accès à ces services est octroyé. Vous pouvez révoquer/octroyer l'accès à partir du portail de gestion. 

>[AZURE.NOTE] En octroyant/révoquant l'accès, vous réinitialisez le nom d'utilisateur et le mot de passe du cluster.

**Pour octroyer/révoquer l'accès aux services web HTTP**

1. Connectez-vous au [Portail de gestion Azure][azure-management-portal].
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters HDInsight déployés s'affiche.
3. Cliquez sur le cluster HDInsight que vous souhaitez configurer.
4. En haut de la page, cliquez sur **CONFIGURATION**.
5. Cliquez sur **ON** ou **OFF** en regard de **SERVICES HADOOP**.  
6. Entrez un **NOM D'UTILISATEUR** et un **NOUVEAU MOT DE PASSE**.  Il s'agira des nouveaux nom d'utilisateur et mot de passe du cluster.
7. Cliquez sur **ENREGISTRER**.

Vous pouvez également utiliser les cmdlets Azure PowerShell :

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Consultez la rubrique [Administration de HDInsight à l'aide de PowerShell][hdinsight-admin-powershell].

##<a id="hadoopcmd"></a> Ouverture de la ligne de commande Hadoop

Pour vous connecter au cluster à l'aide du Bureau à distance et utiliser la ligne de commande Hadoop, vous devez d'abord activer l'accès Bureau à distance au cluster, comme décrit à la section précédente. 

**Pour ouvrir la ligne de commande Hadoop**

1. Connectez-vous au [Portail de gestion Azure][azure-management-portal].
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. Une liste de clusters Hadoop déployés s'affiche.
3. Cliquez sur le cluster HDInsight auquel vous souhaitez vous connecter.
3. Cliquez sur **CONFIGURATION** en haut de la page.
4. Cliquez sur **Connexion** au bas de la page.
5. Cliquez sur **Ouvrir**.
6. Entrez vos informations d'identification, puis cliquez sur **OK**.  Utilisez le nom d'utilisateur et le mot de passe que vous avez configurés lors de la création du cluster.
7. Cliquez sur **Oui**.
8. À partir du Bureau, double-cliquez sur **Ligne de commande Hadoop**.
		
	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Pour plus d'informations sur les commandes Hadoop, consultez la rubrique [Référence aux commandes Hadoop][hadoop-command-reference].

Sur la capture d'écran suivante, le numéro de la version d'Hadoop est intégré au nom du dossier. Il peut être modifié en fonction de la version des composants Hadoop installés sur le cluster. Vous pouvez utiliser des variables d'environnement Hadoop pour faire référence à ces dossiers.  Par exemple :

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%   
	cd %hcatalog_home%

##<a id="nextsteps"></a> Étapes suivantes
Cet article vous a appris à créer un cluster HDInsight à l'aide du portail de gestion Azure et à ouvrir l'outil en ligne de commande Hadoop. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l'aide de PowerShell][hdinsight-admin-powershell]
* [Administration de HDInsight à l'aide de l'interface de ligne de commande interplateforme][hdinsight-admin-cross-platform]
* [Approvisionnement de clusters HDInsight][hdinsight-provision]
* [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]
* [Prise en main d'Azure HDInsight][hdinsight-get-started]
* [Version de Hadoop dans Azure HDInsight][hdinsight-versions]

[hdinsight-admin-cross-platform]: ../hdinsight-administer-use-command-line/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-support]: http://azure.microsoft.com/support/options/
[makecert-info]: http://msdn.microsoft.com/library/bfsktky3(v=vs.110).aspx

[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hadoop-command-reference]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html

[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Cluster landing page"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop command line"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png
<!--HONumber=42-->
