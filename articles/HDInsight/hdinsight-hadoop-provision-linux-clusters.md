<properties 
   pageTitle="Approvisionnement de clusters Hadoop Linux dans HDInsight | Microsoft Azure" 
   description="Découvrez comment approvisionner des clusters Hadoop Linux pour HDInsight à l'aide du portail de gestion, de la ligne de commande et du Kit de développement logiciel (SDK) .NET." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/17/2015"
   ms.author="nitinme"/>


#Approvisionnement de clusters Hadoop Linux dans HDInsight à l'aide d'options personnalisées (version préliminaire)

Dans cet article, vous allez découvrir les différentes méthodes pour approvisionner de façon personnalisée un cluster Hadoop Linux sur Azure HDInsight, en utilisant le portail Azure, Azure PowerShell, des outils de ligne de commande ou le Kit de développement logiciel (SDK) .NET HDInsight.

## Qu'est-ce qu'un cluster HDInsight ?

Vous êtes-vous déjà demandé pourquoi nous évoquons les clusters à chaque fois que nous parlons d’Hadoop ou des « données volumineuses » ? C'est parce qu'Hadoop permet le traitement distribué de grandes quantités de données, diffusées entres les différents nœuds d'un cluster. Le cluster dispose d’une architecture maître/esclave composée d’un nœud maître (également nommé nœud principal ou nœud de nom) et de plusieurs nœuds de travail (également nommés nœuds de données). Pour plus d’informations, consultez <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![Cluster HDInsight][img-hdi-cluster]


Un cluster HDInsight résume les détails de l'implémentation Hadoop pour que vous n'ayez pas à vous demander comment communiquer avec différents nœuds d'un cluster. Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez les ressources de calcul Azure contenant Hadoop et ses applications associées. Pour plus d'informations, consultez la rubrique [Introduction à Hadoop dans HDInsight](hdinsight-hadoop-introduction.md). Les données destinées à l’attrition sont stockées dans un stockage d’objets blob Azure. Pour plus d’informations, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).


Cet article fournit des instructions sur les différentes méthode d'approvisionnement de cluster. Si vous recherchez une approche de démarrage rapide pour approvisionner un cluster, consultez la rubrique [Prise en main d’Azure HDInsight sur Linux](../hdinsight-hadoop-linux-get-started.md).

**Configuration requise**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement. Les applets de commande Azure PowerShell pour HDInsight permettent d’effectuer les tâches en utilisant votre abonnement. Pour plus d’informations sur la façon de se procurer un abonnement, consultez la rubrique <a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">Options d’achat</a>, <a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">Offres spéciales membres</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">Version d’évaluation gratuite</a>.
- Clés Secure Shell (SSH). Si vous souhaitez gérer à distance un cluster Linux à l’aide de SSH avec une clé plutôt qu’un mot de passe, la méthode la plus sécurisée (et recommandée) est d’utiliser une clé. Pour savoir comment générer des clés SSH, consultez les articles suivants :
	-  Depuis un ordinateur Linux : [Utilisation de HDInsight Linux (Hadoop) depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	-  Depuis un ordinateur Windows : [Utilisation de SSH avec HDInsight Linux (Hadoop) depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a id="configuration"></a>Options de configuration

### Clusters sur Linux

HDInsight offre la possibilité d'approvisionner des clusters Linux sur Azure. Approvisionnez un cluster Linux si vous maîtrisez Linux ou Unix, en effectuant une migration à partir d’une solution Hadoop Linux existante, ou si vous souhaitez intégrer facilement des composants de l’écosystème Hadoop conçus pour Linux. Pour en savoir plus sur Azure HDInsight sur Linux, consultez la page [Présentation d’Hadoop sur HDInsight](hdinsight-hadoop-introduction.md).


### Stockage supplémentaire

Durant la configuration, vous devez spécifier un compte de stockage d’objet blob Azure, ainsi qu’un conteneur par défaut. Ceci est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des objets blob supplémentaires qui seront également associés à votre cluster.


Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).


### Metastore

Le metastore contient des informations concernant les tables Hive, les partitions, les schémas, les colonnes, etc. Ces informations sont utilisées par Hive pour trouver où sont stockées les données dans Hadoop HDFS (Distributed File System) ou dans le stockage d’objets blob Azure pour HDInsight. Hive utilise par défaut une base de données intégrée pour stocker ces informations.

Lors de l’approvisionnement d’un cluster HDInsight, vous pouvez indiquer une base de données SQL qui contiendra le metastore pour Hive. Ceci permet de conserver les informations des métadonnées lorsque vous supprimez un cluster, car elles sont alors stockées de manière externe dans la base de données SQL.


> [AZURE.NOTE]L’utilisation des metastores n’est actuellement disponible que lors de l’approvisionnement de HDInsight Linux à l’aide du Kit de développement logiciel (SDK) .NET. Pour en savoir plus, consultez la rubrique [Approvisionnement de clusters HDInsight sur Linux à l’aide du Kit de développement logiciel (SDK) .NET](#sdk).



## <a id="options"></a> Options d’approvisionnement des clusters HDInsight Linux

Vous pouvez approvisionner un cluster HDInsight Hadoop Linux depuis un ordinateur Linux ou Windows. Le tableau suivant fournit des informations sur les options d'approvisionnement disponibles depuis les différents systèmes d'exploitation, ainsi que des liens vers des instructions pour chacun d'entre eux.

Approvisionnement de clusters Linux à partir d’un ordinateur doté de ce système d’exploitation| Utilisation du portail Azure | Utilisation de l’interface de ligne de commande Azure | Utilisation du Kit de développement logiciel (SDK) .NET | Utilisation d'Azure PowerShell
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Cliquez [ici](#portal) | Cliquez [ici](#cli)| Non applicable | Non applicable
Windows | Cliquez [ici](#portal) | Cliquez [ici](#cli) | Cliquez [ici](#sdk) | Cliquez [ici](#powershell)

### <a id="portal"></a>Utilisation du portail Azure

Les clusters HDInsight utilisent un conteneur de stockage d’objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage doit se trouver dans le même centre de données pour que vous puissiez créer un cluster HDInsight. Pour plus d’informations, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md). Pour plus d’informations sur la création d’un compte Azure Storage, consultez la rubrique [Création d’un compte de stockage](../storage-create-storage-account.md).


> [AZURE.NOTE]Actuellement, seules les régions **Asie du Sud-Est**, **Europe du Nord**, **Est des États-Unis** et **Sud du centre des États-Unis** peuvent héberger des clusters HDInsight Linux.

**Pour créer un cluster HDInsight en utilisant l’option Création personnalisée**

1. Connectez-vous au [portail Azure][azure-management-portal].
2. Dans la partie inférieure de la page, cliquez sur **+ NEW**, sur **DATA SERVICES**, sur **HDINSIGHT**, puis sur **CUSTOM CREATE**.
3. Sur la page **Détails du cluster**, saisissez ou sélectionnez les valeurs suivantes :

	![Fournir des détails du cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom du cluster</td>
			<td><p>Nom du cluster. </p>
				<ul>
				<li>Le nom DNS (Domain Name System ) doit commencer et finir par un caractère alphanumérique et peut contenir des traits d’union.</li>
				<li>Le champ doit être une chaîne comportant entre 3&#160;et 63&#160;caractères.</li>
				</ul></td></tr>
		<tr><td>Type du cluster</td>
			<td>Sélectionnez <strong>Hadoop</strong>.</td></tr>
		<tr><td>Système d'exploitation</td>
			<td>Sélectionnez <b>Ubuntu&#160;12.04 LTS Preview</b> pour approvisionner un cluster HDInsight sur Linux. Pour approvisionner un cluster Windows, consultez la rubrique <a href="http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/" target="_blank">Approvisionnement de clusters Hadoop&#160;Windows dans HDInsight</a>.</td></tr>
		<tr><td>Version de HDInsight</td>
			<td>Choisissez la version. Pour HDInsight sur Linux, la valeur par défaut est HDInsight version&#160;3.2, qui utilise Hadoop&#160;2.6.</td></tr>
		</table>

	Entrez ou sélectionnez les valeurs, comme indiqué dans le tableau, puis cliquez sur la flèche de droite.

4. Sur la page **Configurer le cluster**, entrez ou sélectionnez les valeurs suivantes :

	![Fournir des détails du cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td>Nœuds de données</td><td>Nombre de nœuds de données que vous souhaitez déployer. À des fins de test, créez un cluster à nœud unique. <br />La limite de taille de cluster varie pour les abonnements Azure. Contactez le support de facturation Azure pour augmenter la limite.</td></tr>
	<tr><td>Région/Réseau virtuel</td><td><p>Choisissez la même région que pour le compte de stockage que vous avez créé précédemment. Les clusters HDInsight requièrent en effet que le compte de stockage soit situé dans la même région. Plus loin dans la configuration, vous ne pourrez choisir qu'un compte de stockage situé dans la région spécifiée ici.</p></td></tr>
	<tr><td>Taille du nœud principal</td><td><p>Sélectionnez une taille de machine virtuelle pour le nœud principal.</p></td></tr>
	<tr><td>Taille du nœud de données</td><td><p>Sélectionnez une taille de machine virtuelle pour le nœud de données.</p></td></tr>
	</table>

	>[AZURE.NOTE]Selon votre choix de machines virtuelles, les coûts peuvent varier. HDInsight utilise toutes les machines virtuelles de niveau standard pour les nœuds de cluster. Pour plus d’informations concernant les répercussions de la taille des machines virtuelles sur les prix, consultez la rubrique <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Tarification HDInsight</a>.


5. Sur la page **Configuration de l'utilisateur du cluster**, entrez les valeurs suivantes :

    ![Fournir l’utilisateur du cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Mot de passe HTTP</td>
		<td>Spécifiez le mot de passe par défaut de l’utilisateur HTTP, <strong>admin</strong>.</td></tr>
	<tr><td>Nom d’utilisateur du SSH</td>
		<td>Spécifiez le nom d’utilisateur du SSH. Vous utiliserez ce nom d’utilisateur pour lancer une session SSH à distance sur les nœuds de cluster HDInsight.</td></tr>
	<tr><td>Type d’authentification SSH</td>
		<td>Indiquez si vous souhaitez utiliser un mot de passe ou une clé SSH pour authentifier un utilisateur SSH.</td></tr>
	<tr><td>Mot de passe SSH</td>
		<td>Si vous choisissez un mot de passe comme type d’authentification, indiquez le mot de passe SSH pour authentifier un utilisateur SSH. Vous serez invité à entrer ce mot de passe lorsque vous essayerez d'ouvrir une session SSH à distance, sur la machine Linux.</td></tr>
	<tr><td>Clé publique SSH</td>
		<td>Si vous choisissez une clé comme type d’authentification, indiquez la clé publique SSH que vous avez déjà générée. Pour lancer une session SSH avec un nœud dans le cluster Linux, vous devrez utiliser la clé privée associée à cette clé publique.<br>
		Pour savoir comment générer une clé SSH sur un ordinateur Linux, cliquez <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">ici</a>. Pour savoir comment générer une clé SSH sur un ordinateur Windows, cliquez <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">ici</a>.
	</td></tr>
	<tr><td>Sélection du metastore Hive/Oozie</td>
		<td>Cochez cette case pour spécifier une base de données SQL sur le même centre de données que le cluster, afin de l’utiliser en tant que metastore Hive/Oozie. Si vous cochez cette case, vous devez renseigner plusieurs informations concernant la base de données SQL Azure dans les pages suivantes de l’Assistant. Ceci est utile si vous voulez conserver les métadonnées sur les tâches Hive/Oozie après la suppression d'un cluster.</td></tr>
	</td></tr>
	</table>

	> [AZURE.NOTE]Nous vous recommandons d’utiliser l’authentification par clé publique SSH avec SSH car celle-ci est plus sécurisée que l’authentification par mot de passe.

	Cliquez sur la flèche droite.

6. Sur la page **Configuration du metastore Hive/Oozie**, entrez les valeurs suivantes :

    ![Fournir l’utilisateur du cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	Spécifiez la base de données SQL Azure qui sera utilisée comme metastore Hive/Oozie. Vous pouvez spécifier la même base de données pour les metastores Hive et Oozie. Cette base de données SQL doit se trouver dans le même centre de données que le cluster HDInsight. La liste répertorie uniquement les bases de données SQL situées dans le même centre de données que celui spécifié sur la page <strong>Détails du cluster</strong>. Spécifiez également le nom d’utilisateur et un mot de passe pour se connecter à la base de données SQL Azure que vous avez sélectionnée.

    >[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

    Cliquez sur la flèche droite.


6. Sur la page  **Compte de stockage**, entrez les valeurs suivantes :


    ![Fournir un compte de stockage pour le cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Compte de stockage</td>
			<td>Spécifiez le compte Azure Storage qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Vous pouvez choisir l'une des trois options suivantes&#160;:
			<ul>
				<li><strong>Utiliser le stockage existant</strong></li>
				<li><strong>Créer un nouveau stockage</strong></li>
				<li><strong>Utiliser le stockage associé à un autre abonnement</strong></li>
			</ul>
			</td></tr>
		<tr><td>Nom du compte</td>
			<td><ul>
				<li>Si vous choisissez d’utiliser un stockage existant, pour <strong>Nom du compte</strong>, sélectionnez un compte de stockage existant. La liste déroulante répertorie uniquement les comptes de stockage situés dans le même centre de données que celui sur lequel vous voulez approvisionner le cluster.</li>
				<li>Si vous choisissez l’option <strong>Créer un nouveau stockage</strong> ou <strong>Utiliser le stockage associé à un autre abonnement</strong>, vous devez fournir le nom du compte de stockage.</li>
			</ul></td></tr>
		<tr><td>Clé du compte</td>
			<td>Si vous choisissez l’option <strong>Utiliser le stockage associé à un autre abonnement</strong>, veuillez indiquer la clé de ce compte de stockage.</td></tr>
		<tr><td>Conteneur par défaut</td>
			<td><p>Spécifiez le conteneur par défaut du compte de stockage qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Si vous sélectionnez l’option <strong>Utiliser le stockage existant</strong> pour le champ <strong>Compte de stockage</strong> et qu’il n’y a aucun conteneur existant dans ce compte, le conteneur est créé par défaut avec le même nom que celui du cluster. Si un conteneur portant le nom du cluster existe déjà, un numéro de séquence est ajouté au nom de conteneur. Par exemple, mon_conteneur1, mon_conteneur2, et ainsi de suite. Cependant, si le compte de stockage existant dispose d’un conteneur dont le nom est différent de celui du cluster spécifié, vous pouvez également utiliser ce conteneur.</p>
            <p>Si vous avez choisi de créer un stockage ou d'utiliser le stockage d'un autre abonnement Azure, vous devez spécifier le nom du conteneur par défaut.</p>
        </td></tr>
		<tr><td>Comptes de stockage supplémentaires</td>
			<td>HDInsight prend en charge plusieurs comptes de stockage. Le nombre de comptes de stockage supplémentaires pouvant être utilisés par un cluster n’est pas limité. Toutefois, si vous créez un cluster au moyen du portail Azure, la limite est établie à sept en raison de contraintes liées à l’interface utilisateur. Chaque compte de stockage supplémentaire que vous spécifiez dans ce champ ajoute une page <strong>Compte de stockage</strong> supplémentaire vers l’Assistant, ce qui vous permet de spécifier les informations de compte. Par exemple, dans la capture d’écran ci-dessus, un compte de stockage supplémentaire est sélectionné, la page&#160;5 est donc ajoutée à la boîte de dialogue.</td></tr>
	</table>

	Cliquez sur la flèche droite.

7. Si vous avez choisi de configurer un stockage supplémentaire pour le cluster, dans la page **Compte de stockage**, entrez les informations du compte pour le compte de stockage supplémentaire :

	![Fournir des détails de stockage supplémentaires pour un cluster HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page6.png)

    De nouveau, vous avez le choix entre le stockage existant, la création d'un stockage ou l'utilisation du stockage d'un autre abonnement Azure. La procédure pour fournir les valeurs est semblable à la procédure précédente.


    > [AZURE.NOTE]Une fois qu’un compte de stockage Azure a été choisi pour votre cluster HDInsight, vous ne pouvez ni le supprimer, ni le modifier pour un autre cluster.


 	Après avoir spécifié le compte de stockage supplémentaire, cliquez sur la coche pour démarrer l’approvisionnement du cluster.

###<a id="cli"></a> Utilisation de la ligne de commande interplateforme

Une autre possibilité pour l’approvisionnement d’un cluster HDInsight est l’interface de ligne de commande Azure pour Mac, Linux et Windows. L’interface de ligne de commande Azure est implémentée dans Node.js. Elle peut être utilisée sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux. Vous pouvez l’installer à partir des emplacements suivants :

- **Kit de développement logiciel (SDK) Node.js** : <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Interface de ligne de commande Azure pour Mac, Linux et Windows** : <a href="https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Pour une aide générale sur l’utilisation de l’interface de ligne de commande, consultez la page [Interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli.md).

Les instructions ci-dessous expliquent comment installer la ligne de commande interplateforme sur Linux et Windows et comment l'utiliser pour approvisionner un cluster.

- [Configuration d’une ligne de commande interplateforme pour Linux](#clilin)
- [Configuration d’une ligne de commande interplateforme pour Windows](#cliwin)
- [Approvisionnement de clusters HDInsight à l’aide d’une ligne de commande interplateforme](#cliprovision)

#### <a id="clilin"></a>Configuration d’une ligne de commande interplateforme pour Linux

Pour configurer votre ordinateur Linux à l’aide d’outils de ligne de commande Azure, procédez comme suit :

- Installation de l’interface de ligne de commande à l’aide du gestionnaire de package Node.js (NPM)
- Connectez-vous à un abonnement Azure

**Pour installer l’interface de ligne de commande avec NPM**

1.	Ouvrez une fenêtre de terminal sur votre ordinateur Linux et exécutez la commande suivante :

		sudo npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Exécutez la commande suivante pour vérifier l'installation :

		azure hdinsight -h

	Vous pouvez utiliser le commutateur **-h** à différents niveaux pour afficher les informations d'aide. Par exemple :

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Pour vous connecter à votre abonnement Azure**

Avant d'utiliser l'interface de ligne de commandes, vous devez configurer la connectivité entre votre poste de travail et Azure. L'interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d'Azure dans un fichier de paramètres de publication. Ce dernier peut ensuite être importé en tant que paramètre de configuration local persistant dont l'interface de ligne de commande se servira pour les opérations ultérieures. Vous n’avez besoin d’importer vos paramètres de publication qu’une seule fois.


> [AZURE.NOTE]Le fichier de paramètres de publication contient des informations critiques. Microsoft vous recommande de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés du dossier ou utilisez le chiffrement du lecteur BitLocker.



1.	Ouvrez une fenêtre de terminal.
2.	Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	La commande ouvre la page web sur laquelle télécharger le fichier de paramètres de publication. Si la page web ne s’ouvre pas, cliquez sur le lien de la fenêtre de terminal pour ouvrir la page du navigateur et connectez-vous au portail.

3.	Téléchargez le fichier de paramètres de publication sur votre ordinateur.
4.	À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

		azure account import <path/to/the/file>

	
#### <a id="cliwin"></a>Configuration d’une ligne de commande interplateforme pour Windows

Pour configurer votre ordinateur Windows à l’aide d’outils en ligne de commande Azure, procédez comme suit :

- Installation de l’interface de ligne de commande à l’aide de NPM ou Windows Installer
- Téléchargement et importation de paramètres de publication de compte Azure


L’interface de ligne de commande peut être installée à l’aide de NPM ou de Windows Installer. Microsoft vous recommande de procéder à cette installation en utilisant uniquement l’une des deux options suivantes.

**Pour installer l’interface de ligne de commande avec NPM**

1.	Accédez à **www.nodejs.org**.
2.	Cliquez sur **INSTALLER** et suivez les instructions en conservant les paramètres par défaut.
3.	Ouvrez **Invite de commandes** (ou **Invite de commandes Azure** ou **Invite de commandes développeur pour VS2012**) à partir de votre poste de travail.
4.	Exécutez la commande suivante dans la fenêtre d’invite de commandes :

		npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Si vous obtenez une erreur indiquant que la commande du NPM est introuvable, vérifiez que les chemins d’accès suivants figurent dans la variable d’environnement  **PATH** : <i>C:\Program Files (x86)\nodejs;C:\Users[nomutilisateur]\AppData\\Roaming\npm</i> ou <i>C:\Program Files\nodejs;C:\Users[nomutilisateur]\AppData\\Roaming\npm</i>.


5.	Exécutez la commande suivante pour vérifier l'installation :

		azure hdinsight -h

	Vous pouvez utiliser le commutateur **-h** à différents niveaux pour afficher les informations d'aide. Par exemple :

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Pour installer l’interface de ligne de commande à l’aide de Windows Installer**

1.	Accédez à **http://azure.microsoft.com/downloads/**.
2.	Faites défiler l'écran pour accéder à la section **Outils de ligne de commande**, puis cliquez sur **Interface de ligne de commande interplateforme** et suivez les étapes de l'Assistant Web Platform Installer.

**Pour télécharger et importer des paramètres de publication**

Avant d'utiliser l'interface de ligne de commandes, vous devez configurer la connectivité entre votre poste de travail et Azure. L'interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d'Azure dans un fichier de paramètres de publication. Ce dernier peut ensuite être importé en tant que paramètre de configuration local persistant dont l'interface de ligne de commande se servira pour les opérations ultérieures. Vous n’avez besoin d’importer vos paramètres de publication qu’une seule fois.


> [AZURE.NOTE]Le fichier de paramètres de publication contient des informations critiques. Microsoft vous recommande de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés du dossier ou utilisez BitLocker.



1.	Ouvrez une invite de commandes.
2.	Exécutez la commande suivante pour télécharger le fichier de paramètres de publication :

		azure account download


	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	La commande ouvre la page web sur laquelle télécharger le fichier de paramètres de publication.


3.	À l'invite d'enregistrement du fichier, cliquez sur **Enregistrer** et indiquez un emplacement pour l'enregistrement du fichier.
5.	À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

		azure account import <path/to/the/file>

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	
#### <a id="cliprovision"></a>Approvisionnement de clusters HDInsight à l’aide d’une ligne de commande interplateforme

Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight en utilisant la ligne de commande interplateforme :

- Création d'un compte Azure Storage
- Mise en service d'un cluster


**Pour créer un compte Azure Storage**

HDInsight utilise le conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage est nécessaire avant de pouvoir créer un cluster HDInsight. Il doit se trouver dans le même centre de données.

- À partir de la fenêtre d’invite de commandes, exécutez la commande suivante pour créer un compte Azure Storage :

		azure storage account create [options] <StorageAccountName>


	Lorsque l’invite vous demande d’indiquer un emplacement, sélectionnez-en un où un cluster HDInsight Linux puisse être approvisionné. Le stockage doit se trouver dans le lieu que le cluster HDInsight.


Pour plus d’informations sur la création d’un compte Azure Storage au moyen du portail Azure, consultez la rubrique [Création, gestion ou suppression d’un compte de stockage](../storage-create-storage-account.md).

Si vous disposez déjà d’un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer ces informations :

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Pour plus d’informations sur l’obtention d’informations au moyen du portail Azure, consultez la section « Affichage, copie et régénération des clés d’accès de stockage » de la rubrique [Création, gestion ou suppression d’un compte de stockage](../storage-create-storage-account.md).

Un cluster HDInsight requiert également un conteneur dans un compte de stockage. Si le compte de stockage que vous fournissez ne contient pas de conteneur, la commande **azure hdinsight cluster create** vous invite à entrer un nom de conteneur avant de le créer. Cependant, si vous voulez créer le conteneur au préalable, vous pouvez utiliser la commande suivante :

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Une fois que le compte de stockage et le conteneur d’objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour approvisionner un cluster HDInsight**

- À partir de la fenêtre d'invite de commandes, exécutez la commande suivante :

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <StorageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

	>[AZURE.NOTE]Les valeurs que vous spécifiez pour **--userName** et **--password** concernent l’utilisateur Hadoop. Pour l’utilisateur Hadoop, vous devez toujours spécifier ---userName en tant que « admin ».

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Approvisionnement d’un cluster HDInsight au moyen d’un fichier de configuration**

Généralement, vous mettez en service un cluster HDInsight, vous exécutez les tâches, puis vous supprimez le cluster pour réduire les coûts. L'interface de ligne de commande vous donne la possibilité d'enregistrer les configurations dans un fichier, de sorte que vous pouvez les réutiliser chaque fois que vous mettez en service un cluster.

- À partir de la fenêtre d'invite de commandes, exécutez les commandes suivantes :


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster. Make sure you specify --userName as "admin"
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>


	>[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.



	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Pour énumérer et afficher les détails de cluster**

- Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Pour supprimer un cluster**

- Utilisez les commandes suivantes pour supprimer un cluster :

		azure hdinsight cluster delete <ClusterName>

###<a id="powershell"></a> Utilisation d’Azure PowerShell
Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cette section fournit des instructions sur l’approvisionnement d’un cluster HDInsight à l’aide d’Azure PowerShell. Pour plus d’informations sur la configuration d’un poste de travail pour qu’il exécute des applets de commande HDInsight Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md). Pour plus d’informations sur l’utilisation d’Azure PowerShell avec HDInsight, consultez la rubrique [Administration de HDInsight avec PowerShell](hdinsight-administer-use-powershell.md). Pour la liste des applets de commande Windows PowerShell pour HDInsight, consultez la rubrique [Référence des applets de commande HDInsight](http://msdn.microsoft.com/library/windowsazure/dn479228.aspx).

Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight en utilisant Azure PowerShell :

- Création d'un compte Azure Storage
- Création d'un conteneur d'objets blob Azure
- Création d'un cluster HDInsight

Vous pouvez exécuter les scripts depuis la console Windows PowerShell ou depuis l’environnement de développement intégré (ISE) de Windows PowerShell.
 
HDInsight utilise le conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage et un conteneur de stockage sont nécessaires avant de pouvoir créer un cluster HDInsight. Ce compte de stockage doit se situer dans le même centre de données que le cluster HDInsight. Actuellement, seules les régions **Asie du Sud-Est**, **Europe du Nord**, **Est des États-Unis** et **Sud du centre des États-Unis** peuvent héberger des clusters HDInsight Linux.

**Pour vous connecter à votre compte Azure**

		Add-AzureAccount 

Vous êtes invité à entrer les informations d'identification de votre compte Azure.

**Pour créer un compte Azure Storage**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Si vous disposez déjà d’un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes Windows PowerShell suivantes pour récupérer ces informations :

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Pour créer un conteneur de stockage d’objets blob Azure**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Une fois que le compte de stockage et le conteneur d’objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour approvisionner un cluster HDInsight**

Les deux paramètres les plus importants que vous devez définir pour approvisionner les clusters Linux sont ceux où vous spécifiez le type de système d’exploitation et les détails de l’utilisateur SSH :

- Vérifiez que le paramètre **-OSType** est défini sur **Linux**.
- Pour utiliser le protocole SSH pour les sessions à distance sur les clusters, vous pouvez spécifier le mot de passe utilisateur SSH ou la clé publique SSH. Si vous spécifiez le mot de passe utilisateur SSH et la clé publique SSH, la clé sera ignorée. Si vous souhaitez utiliser la clé SSH pour les sessions à distance, vous devez spécifier un mot de passe SSH vide lorsque vous y êtes invité.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $containerName = $clusterName					  # Azure Blob container that is used as the default file system for the HDInsight cluster
		
		
		# Get the credentials for HTTP and SSH users for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster. 
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"           

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		
        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $clusterCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey


>[AZURE.NOTE]Les valeurs que vous spécifiez pour **$clusterCredentials** sont utilisées pour créer le compte d’utilisateur Hadoop pour le cluster. Vous utiliserez ce compte pour vous connecter au cluster. Les valeurs que vous spécifiez pour **$sshCredentials** sont utilisées pour créer le compte d’utilisateur SSH pour le cluster. Vous utilisez ce compte pour démarrer une session SSH à distance sur le cluster et pour exécuter des tâches. Si vous utilisez l’option Création rapide à partir du portail Azure pour approvisionner un cluster, le nom d’utilisateur Hadoop par défaut est « admin » et le nom d’utilisateur SSH par défaut est « hdiuser ».

L’approvisionnement du cluster peut prendre plusieurs minutes.

![HDI.CLI.Provision][image-hdi-ps-provision]

**Approvisionnement d’un cluster HDInsight au moyen d’options de configuration personnalisées**

Lorsque vous approvisionnez un cluster, vous pouvez utiliser d’autres options de configuration, telles qu’une connexion à plusieurs conteneurs de stockage d’objets blob Azure ou l’utilisation d’une base de données SQL Azure pour les metastores Hive et Oozie. Ceci vous permet de séparer la durée de vie de vos données et métadonnées de celle du cluster.

Les deux paramètres les plus importants que vous devez définir pour approvisionner les clusters Linux sont ceux où vous spécifiez le type de système d’exploitation et les détails de l’utilisateur SSH :

- Vérifiez que le paramètre **-OSType** est défini sur **Linux**.
- Pour utiliser le protocole SSH pour les sessions à distance sur les clusters, vous pouvez spécifier le mot de passe utilisateur SSH ou la clé publique SSH. Si vous spécifiez le mot de passe utilisateur SSH et la clé publique SSH, la clé sera ignorée. Si vous souhaitez utiliser la clé SSH pour les sessions à distance, vous devez spécifier un mot de passe SSH vide lorsque vous y êtes invité.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        		
		# Get the credentials for HTTP user, SSH user, and Hive/Oozie metastore databases for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster. 
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}

		# Create a cluster configuration file
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		        
		# Create the cluster
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $clusterLocation -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey

>[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

L’approvisionnement du cluster peut prendre plusieurs minutes.

![HDI.CLI.Provision][image-hdi-ps-config-provision]

###<a id="sdk"></a> Utilisation du Kit de développement logiciel (SDK) HDInsight .NET
Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET.

Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight sur Linux en utilisant le Kit de développement logiciel (SDK) :

- Installation du Kit de développement logiciel (SDK) HDInsight
- Créer un certificat auto-signé
- Création d'une application console
- Exécution de l'application


**Pour installer le Kit de développement logiciel (SDK) .NET HDInsight**

Vous pouvez installer la dernière version publiée du Kit de développement logiciel (SDK) disponible sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Pour créer un certificat auto-signé**

Créez un certificat auto-signé, installez-le sur votre poste de travail et téléchargez-le dans votre abonnement Azure. Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138).


**Pour créer une application console Visual Studio**

1. Ouvrez Visual Studio 2013.

2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriété</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valeur</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Catégorie</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modèles/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modèle</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Application console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. Cliquez sur **OK** pour créer le projet.

5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**.

6. Exécutez la commande suivante dans la console pour installer les packages :

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Cette commande ajoute des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

7. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8. Ajoutez les instructions using suivantes au début du fichier :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Dans la fonction **Main()**, copiez et collez le code suivant :

	> [AZURE.NOTE]Vérifiez que les régions que vous avez indiquées figurent parmi les suivantes, car ce sont les seules pouvant héberger des clusters HDInsight Linux : **Asie du Sud-Est**, **Europe du Nord**, **Est des États-Unis** et **Sud du centre des États-Unis**. Le compte de stockage que vous fournissez doit également être dans la même région.

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";		# Make sure you specify this username as "admin"
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;
		

		// If required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores

		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
			Version = version,
            OSType = OSType.Linux,
            SshUserName = sshusername,
            SshPublicKey = sshpublickey,
			HiveMetastore = hiveMetastore,		//Only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//Only if you created an ooziemetastore object earlier
        };

		// Configure Hive and Oozie if you opted for the metastores earlier
		clusterInfo.HiveConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("datanucleus.connectionPoolingType", "none"));
		clusterInfo.OozieConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("oozie.service.AuthorizationService.security.enabled", "false"));
        

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Remplacez les variables au début de la fonction **Main()**.

**Pour exécuter l’application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. La création d'un cluster HDInsight peut prendre plusieurs minutes.



##<a id="nextsteps"></a> Étapes suivantes
Cet article vous a présenté différentes méthodes pour configurer un cluster HDInsight Hadoop sur Linux. Pour en savoir plus, consultez les articles suivants :

- [Utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md) : familiarisez-vous avec l’utilisation d’un cluster HDInsight sur Linux.
- [Gestion des clusters HDInsight avec Ambari](hdinsight-hadoop-manage-ambari.md) : découvrez comment gérer et contrôler votre Hadoop Linux sur le cluster HDInsight à l’aide d’Ambari Web ou de l’API REST d’Ambari. 
- [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md) : découvrez les différentes façons d’exécuter des tâches MapReduce sur un cluster.
- [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md) : découvrez les différentes façons d’exécuter une requête Hive sur un cluster.
- [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md) : découvrez les différentes façons d’exécuter une tâche Pig sur un cluster.
- [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md) : découvrez comment HDInsight utilise le stockage d’objets blob Azure pour stocker des données pour les clusters HDInsight.
- [Téléchargement de données vers HDInsight](hdinsight-upload-data.md) : découvrez comment utiliser des données stockées dans un stockage d’objets blob Azure pour un cluster HDInsight.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Énumération et affichage des clusters"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Utilisation de Sqoop avec HDInsight"

<!--HONumber=54--> 