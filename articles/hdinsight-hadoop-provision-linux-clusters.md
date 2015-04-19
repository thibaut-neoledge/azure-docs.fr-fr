<properties 
   pageTitle="Approvisionnement de clusters Hadoop Linux dans HDInsight | Azure" 
   description="Découvrez comment approvisionner des clusters Hadoop Linux pour HDInsight à l'aide du portail de gestion, de la ligne de commande et du Kit de développement logiciel (SDK) .NET." 
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
   ms.date="03/03/2015"
   ms.author="nitinme"/>


#Approvisionnement de clusters Hadoop Linux dans HDInsight à l'aide d'options personnalisées (version préliminaire)

Dans cet article, vous allez découvrir les différentes méthodes pour approvisionner de façon personnalisée un cluster Hadoop Linux sur Azure HDInsight, en utilisant le portail de gestion Azure, PowerShell, des outils de ligne de commande ou le kit de développement logiciel (SDK) .NET HDInsight.

## Qu'est-ce qu'un cluster HDInsight ?

Vous êtes-vous déjà demandé pourquoi nous évoquons les clusters à chaque fois que nous parlons d'Hadoop ou des BigData ? C'est parce qu'Hadoop permet le traitement distribué de grandes quantités de données, diffusées entre les différents nœuds d'un cluster. Le cluster dispose d'une architecture principal/secondaire composée d'un maître (également nommé nœud ou nœud de nom) et de plusieurs esclaves (également nommés nœuds de données). Pour plus d'informations, consultez <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![HDInsight Cluster][img-hdi-cluster]

Un cluster HDInsight résume les détails de l'implémentation Hadoop pour que vous n'ayez pas à vous demander comment communiquer avec différents nœuds d'un cluster. Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez les ressources de calcul Azure contenant Hadoop et ses applications associées. Pour plus d'informations, consultez la rubrique [Présentation de Hadoop dans HDInsight](hdinsight-hadoop-introduction.md). Les données destinées à l'attrition sont stockées dans un stockage d'objets blob Azure, également nommé  *Stockage Azure : Blob* (ou WASB) dans le contexte de HDInsight. Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md).

Cet article fournit des instructions sur les différentes méthodes d'approvisionnement de cluster. Si vous recherchez une approche de démarrage rapide pour approvisionner un cluster, consultez [Prise en main d'Azure HDInsight sur Linux](hdinsight-hadoop-linux-get-started.md).

**Configuration requise :**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement. Les applets de commande HDInsight PowerShell permettent d'effectuer les tâches avec votre abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les pages traitant des <a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">formules d'abonnement</a>, des <a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">offres spéciales membres</a> ou de la <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">version d'évaluation gratuite</a>.
- Clés SSH. Si vous souhaitez gérer à distance un cluster Linux à l'aide de SSH avec une clé plutôt qu'un mot de passe. L'utilisation d'une clé constitue la méthode  recommandée, car elle est plus sécurisée. Pour savoir comment générer des clés SSH, consultez les articles suivants :
	-  Depuis un ordinateur Linux : [Utilisation de HDInsight Linux (Hadoop) depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Depuis un ordinateur Windows : [Utilisation de SSH avec HDInsight Linux (Hadoop) depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Options de configuration

### Clusters sur Linux

HDInsight offre la possibilité d'approvisionner des clusters Linux sur Azure. Approvisionnez un cluster Linux si vous maîtrisez Linux ou Unix, en effectuant une migration à partir d'une solution Hadoop Linux existante, ou si vous souhaitez intégrer facilement des composants de l'écosystème Hadoop conçus pour Linux. Pour en savoir plus sur Azure HDInsight sur Linux, consultez la page [Présentation d'Hadoop sur HDInsight](hdinsight-hadoop-introduction.md). 

### Stockage supplémentaire

Durant la configuration, vous devez spécifier un compte de stockage d'objet blob Azure, ainsi qu'un conteneur par défaut. Ceci est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des objets blob supplémentaires qui seront également associés à votre cluster.

Pour plus d'informations sur l'utilisation des magasins d'objets blob secondaires, consultez [Utilisation du stockage d'objets blob Azure avec HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-use-blob-storage/).

### Metastore

Le Metastore contient des informations sur les tables Hive, les partitions, les schémas, les colonnes, etc. Ces informations sont utilisées par Hive pour localiser l'emplacement de stockage des données sur HDFS (ou WASB pour HDInsight.) Hive utilise par défaut une base de données intégrée pour stocker ces informations.

Lors de l'approvisionnement d'un cluster HDInsight, vous pouvez indiquer une base de données SQL qui contiendra le Metastore pour Hive. Ceci permet de conserver les informations des métadonnées lorsque vous supprimez un cluster, car elles sont alors stockées de manière externe dans la base de données SQL.

> [WACOM.NOTE] À l'heure actuelle, la possibilité d'utiliser des metastores n'est disponible que lors de l'approvisionnement de HDInsight Linux à l'aide de .NET SDK. Pour en savoir plus, consultez la rubrique [Approvisionnement de clusters HDInsight sur Linux à l'aide du kit de développement logiciel .NET SDK](#sdk).


## <a id="options"></a> Options d'approvisionnement des clusters HDInsight Linux

Vous pouvez approvisionner un cluster HDInsight Hadoop Linux depuis un ordinateur Linux ou un ordinateur Windows. Le tableau suivant fournit des informations sur les options d'approvisionnement disponibles depuis les différents systèmes d'exploitation, ainsi que des liens vers des instructions pour chacun d'entre eux.

Approvisionnement de clusters Linux depuis un ordinateur qui est doté de ce système d'exploitation| Utilisation du portail de gestion Azure | Utilisation de l'interface CLI multiplateforme | Utilisation du kit SDK .NET | Utilisation de PowerShell
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Cliquez [ici](#portal) | Cliquez [ici](#cli)| Non applicable | Bientôt disponible
Windows | Cliquez [ici](#portal) | Cliquez [ici](#cli) | Cliquez [ici](#sdk) | Bientôt disponible

### <a id="portal"></a> Utilisation du portail de gestion Azure

Les clusters HDInsight utilisent un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage se trouvant dans le même centre de données est nécessaire pour pouvoir créer un cluster HDInsight. Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md). Pour plus d'informations sur la création d'un compte Azure Storage, consultez la page [Création d'un compte de stockage][azure-create-storageaccount].


> [WACOM.NOTE] Actuellement, seules les régions **Est de l'Asie**, **Sud-Est de l'Asie**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Centre-Nord des États-Unis** et **Centre-Sud des États-Unis** peuvent héberger des clusters HDInsight.

**Pour créer un cluster HDInsight en utilisant l'option de création personnalisée** :

1. Connectez-vous au [portail de gestion Azure][azure-management-portal].
2. Cliquez sur **NOUVEAU** au bas de la page, sur **SERVICES DE DONNÉES**, sur **HDINSIGHT**, puis sur **CRÉATION PERSONNALISÉE**.
3. Dans la page **Détails du cluster**, entrez ou sélectionnez les valeurs suivantes :

	![Provide Hadoop HDInsight cluster details](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom du cluster</td>
			<td><p>Nommez le cluster. </p>
				<ul>
				<li>Le nom DNS doit commencer et finir par un caractère alphanumérique, et peut contenir des traits d'union.</li>
				<li>Le champ doit être une chaîne comportant entre 3 et 63 caractères.</li>
				</ul></td></tr>
		<tr><td>Type du cluster</td>
			<td>Pour le type du cluster, sélectionnez <strong>Hadoop</strong>.</td></tr>
		<tr><td>Système d'exploitation</td>
			<td>Sélectionnez <b>Présentation d'Ubuntu 12.04 LTS</b> pour approvisionner un cluster HDInsight sur Linux. Pour configurer un cluster Windows, consultez <a href="http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/" target="_blank">Approvisionnement de clusters Hadoop Windows dans HDInsight</a>.</td></tr>
		<tr><td>Version de HDInsight</td>
			<td>Choisissez la version. Pour HDInsight sur Linux, la valeur par défaut est HDInsight version 3.2, qui utilise Hadoop 2.5.</td></tr>
		</table>

	Entrez ou sélectionnez les valeurs, comme indiqué dans le tableau, puis cliquez sur la flèche de droite.

4. Dans la page **Configurer le cluster**, entrez ou sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td>Nœuds de données</td><td>Nombre de nœuds de données que vous souhaitez déployer. À des fins de test, créez un cluster à nœud unique. <br />La limite de taille de cluster varie pour les abonnements Azure. Contactez le support de facturation Azure pour augmenter la limite.</td></tr>
	<tr><td>Région/Réseau virtuel</td><td><p>Choisissez la même région que pour le compte de stockage que vous avez créé dans la dernière procédure. HDInsight requiert en effet que le compte de stockage soit situé dans la même région. Plus loin dans la configuration, vous ne pourrez choisir qu'un compte de stockage situé dans la région spécifiée ici.</p><p>Les régions disponibles sont : <strong>Asie orientale</strong>, <strong>Asie du Sud-Est</strong>, <strong>Europe du Nord</strong>, <strong>Europe de l'Ouest</strong>, <strong>Est des États-Unis</strong>, <strong>Ouest des États-Unis</strong>, <strong>Nord du centre des États-Unis</strong>, <strong>Sud du centre des États-Unis</strong><br/></p></td></tr>
	</table>



5. Sur la page **Configuration de l'utilisateur du cluster**, entrez les valeurs suivantes :

    ![Provide Hadoop HDInsight cluster user](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Mot de passe HTTP</td>
			<td>Spécifiez le mot de passe par défaut de l'utilisateur HTTP, <i>admin</i>.</td></tr>
		<tr><td>Nom d'utilisateur du SSH</td>
			<td>Spécifiez le nom d'utilisateur du SSH. Vous utiliserez ce nom d'utilisateur pour lancer une session SSH à distance sur les nœuds de cluster HDInsight.</td></tr>
		<tr><td>Type d'authentification SSH</td>
			<td>Indiquez si vous souhaitez utiliser un mot de passe ou une clé SSH pour authentifier un utilisateur SSH</td></tr>
		<tr><td>Mot de passe SSH</td>
			<td>Si vous choisissez un mot de passe comme type d'authentification, indiquez le mot de passe SSH pour authentifier un utilisateur SSH. Vous serez invité à entrer ce mot de passe lorsque vous essayerez d'ouvrir une session SSH à distance, sur la machine Linux.</td></tr>
		<tr><td>Clé publique SSH</td>
			<td>Si vous choisissez une clé comme type d'authentification, indiquez la clé publique SSH que vous avez déjà générée. Pour lancer une session SSH avec un nœud dans le cluster Linux, vous devrez utiliser la clé privée associée à cette clé publique.<br>
			Pour savoir comment générer une clé SSH sur un ordinateur Linux, cliquez <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">ici</a>. Pour savoir comment générer une clé SSH sur un ordinateur Windows, cliquez <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">ici</a>.
		</td></tr>
		</table>

	> [WACOM.NOTE] Nous vous recommandons d'utiliser l'authentification par clé publique SSH avec SSH car celle-ci est plus sécurisée que l'authentification par mot de passe.

	Cliquez sur la flèche droite.


6. Dans la page **Compte de stockage**, entrez la valeur suivante :

    ![Provide storage account for Hadoop HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Compte de stockage</td>
			<td>Spécifiez le compte de stockage Azure qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Vous pouvez choisir l'une des trois options suivantes :
			<ul>
				<li>Utiliser le stockage existant</li>
				<li>Créer un nouveau stockage</li>
				<li>Utiliser le stockage associé à un autre abonnement</li>
			</ul>
			</td></tr>
		<tr><td>Nom du compte</td>
			<td><ul>
				<li>Si vous choisissez d'utiliser un stockage existant, pour <strong>Nom du compte</strong>, sélectionnez un compte de stockage existant. La liste déroulante répertorie uniquement les comptes de stockage situés dans le centre de données sur lequel vous voulez approvisionner le cluster.</li>
				<li>Si vous choisissez l'option <strong>Créer un nouveau stockage</strong> ou <strong>Utiliser le stockage associé à un autre abonnement</strong>, vous devez fournir le nom du compte de stockage.</li>
			</ul></td></tr>
		<tr><td>Clé du compte</td>
			<td>Si vous choisissez l'option <strong>Utiliser le stockage associé à un autre abonnement</strong>, spécifiez la clé de ce compte de stockage.</td></tr>
		<tr><td>Conteneur par défaut</td>
			<td><p>Permet de spécifier le conteneur par défaut du compte de stockage qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Si vous avez choisi l'option <strong>Utiliser le stockage existant</strong> pour le champ <strong>Compte de stockage</strong> et qu'il n'y a aucun conteneur existant dans ce compte, le conteneur est créé par défaut avec le même nom que celui du cluster. Si un conteneur portant le nom du cluster existe déjà, un numéro de séquence est ajouté au nom de conteneur. Par exemple, mon_conteneur1, mon_conteneur2, et ainsi de suite. Cependant, si le compte de stockage existant a un conteneur dont le nom est différent de celui du cluster spécifié, vous pouvez également utiliser ce conteneur.</p>
            <p>Si vous avez choisi de créer un stockage ou d'utiliser le stockage d'un autre abonnement Azure, vous devez spécifier le nom du conteneur par défaut.</p>
        </td></tr>
		<tr><td>Comptes de stockage supplémentaires</td>
			<td>HDInsight prend en charge plusieurs comptes de stockage. Le nombre de comptes de stockage supplémentaires pouvant être utilisés par un cluster n'est pas limité. Toutefois, si vous créez un cluster à l'aide du portail de gestion, la limite est établie à sept en raison de contraintes liées à l'interface utilisateur. Chaque compte de stockage supplémentaire que vous spécifiez dans ce champ ajoute une page Compte de stockage supplémentaire vers l'Assistant vous permettant de spécifier les informations de compte. Par exemple, dans la capture d'écran suivante, 1 compte de stockage supplémentaire est sélectionné, la page 5 est donc ajoutée à la boîte de dialogue.</td></tr>
	</table>

	Cliquez sur la flèche droite.

7. Si vous avez choisi de configurer un stockage supplémentaire pour le cluster, dans la page **Compte de stockage**, entrez les informations du compte pour le compte de stockage supplémentaire :

	![Provide additional storage details for HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

    De nouveau, vous avez le choix entre le stockage existant, la création d'un stockage ou l'utilisation du stockage d'un autre abonnement Azure. La procédure pour fournir les valeurs est semblable à la procédure précédente.

    > [WACOM.NOTE] Une fois qu'un compte de stockage Azure a été choisi pour votre cluster HDInsight, vous ne pouvez ni le supprimer, ni le remplacer par un autre.

 	Après avoir spécifié le compte de stockage supplémentaire, cliquez sur la coche pour démarrer l'approvisionnement du cluster. 

###<a id="cli"></a> Utilisation de la ligne de commande interplateforme

Une autre possibilité pour la mise en service d'un cluster HDInsight est l'interface de ligne de commande interplateforme. L'outil en ligne de commande est mis en œuvre dans Node.js. Il peut être utilisé sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux. Vous pouvez installer l'interface CLI à partir des emplacements suivants :

- **Kit de développement logiciel Node.JS** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Interface de ligne de commande interplateforme** - <a href="https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Pour obtenir une aide générale sur la façon d'utiliser l'interface de ligne de commande, consultez [Outil de ligne de commande Azure pour Mac et Linux][azure-command-line-tools].

Les instructions ci-dessous expliquent comment installer la ligne de commande interplateforme sur Linux et Windows et comment l'utiliser pour approvisionner un cluster.

- [Configuration d'une ligne de commande interplateforme Azure pour Linux](#clilin)
- [Configuration d'une ligne de commande interplateforme Azure pour Windows](#cliwin)
- [Approvisionnement de clusters HDInsight à l'aide d'une ligne de commande interplateforme Azure](#cliprovision)

#### <a id="clilin"></a>Configuration d'une ligne de commande interplateforme pour Linux

Pour configurer votre ordinateur Linux à l'aide d'outils de ligne de commande Azure, procédez comme suit :

- Installez une ligne de commande interplateforme l'aide de NPM
- Connectez-vous à un abonnement Azure

**Pour installer l'interface de ligne de commande à l'aide de NPM** :

1.	Ouvrez une fenêtre de terminal sur votre ordinateur Linux et exécutez la commande suivante :

		sudo npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Exécutez la commande suivante pour vérifier l'installation :

		azure hdinsight -h

	Vous pouvez utiliser le commutateur *-h* à différents niveaux pour afficher les informations d'aide. Par exemple :

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Pour vous connecter à votre abonnement Azure** :

Avant d'utiliser l'interface de ligne de commande, vous devez configurer la connectivité entre votre poste de travail et Azure. L'interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d'Azure dans un fichier de paramètres de publication. Ce dernier peut ensuite être importé en tant que paramètre de configuration local persistant dont l'interface de ligne de commande se servira pour les opérations ultérieures. Vous importez vos paramètres de publication une fois pour toutes.

> [WACOM.NOTE] Le fichier de paramètres de publication contient des informations critiques. Microsoft vous recommande de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés de dossier ou utilisez BitLocker. 


1.	Ouvrez une fenêtre de terminal.
2.	Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	La commande ouvre la page Web sur laquelle vous pouvez télécharger le fichier de paramètres de publication. Si la page Web ne s'ouvre pas, cliquez sur le lien de la fenêtre de terminal pour ouvrir la page du navigateur et connectez-vous au portail. 

3.	Téléchargez le fichier de paramètres de publication sur votre ordinateur.
5.	À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

		azure account import <path/to/the/file>

	
#### <a id="cliwin"></a>Configuration d'une ligne de commande interplateforme pour Windows

Pour configurer votre ordinateur Windows à l'aide d'outils de ligne de commande Azure, procédez comme suit :

- Installez une ligne de commande interplateforme (à l'aide de NPM ou de Windows installer)
- Téléchargement et importation de paramètres de publication de compte Azure


L'interface de ligne de commande peut être installée au moyen du  *Gestionnaire de package Node.js (NPM)* ou de Windows Installer. Microsoft vous recommande de procéder à l'installation en utilisant uniquement l'une des deux options suivantes.

**Pour installer l'interface de ligne de commande à l'aide de NPM**

1.	Accédez à **www.nodejs.org**.
2.	Cliquez sur **INSTALL** et suivez les instructions en conservant les paramètres par défaut.
3.	Ouvrez **Invite de commandes** (ou  *Invite de commandes Azure* ou  *Invite de commandes développeur pour VS2012* à partir de votre poste de travail.
4.	Exécutez la commande suivante dans la fenêtre d'invite de commandes.

		npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [WACOM.NOTE] Si un message d'erreur indique que la commande NPM est introuvable, vérifiez que les chemins d'accès suivants se trouvent dans la variable d'environnement : <i>C:\Program Files (x86)\nodejs;C:\Users\[nom_utilisateur]\AppData\Roaming\npm</i> ou <i>C:\Program Files\nodejs;C:\Users\[nom_utilisateur]\AppData\Roaming\npm</i>

5.	Exécutez la commande suivante pour vérifier l'installation :

		azure hdinsight -h

	Vous pouvez utiliser le commutateur *-h* à différents niveaux pour afficher les informations d'aide. Par exemple :

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Pour installer l'interface de ligne de commande à l'aide de Windows Installer**

1.	Accédez à **http://azure.microsoft.com/downloads/**.
2.	Faites défiler l'écran pour accéder à la section **Outils de ligne de commande**, puis cliquez sur **Interface de ligne de commande interplateforme** et suivez les étapes de l'Assistant Web Platform Installer.

**Pour télécharger et importer des paramètres de publication**

Avant d'utiliser l'interface de ligne de commande, vous devez configurer la connectivité entre votre poste de travail et Azure. L'interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d'Azure dans un fichier de paramètres de publication. Ce dernier peut ensuite être importé en tant que paramètre de configuration local persistant dont l'interface de ligne de commande se servira pour les opérations ultérieures. Vous importez vos paramètres de publication une fois pour toutes.

> [WACOM.NOTE] Le fichier de paramètres de publication contient des informations critiques. Microsoft vous recommande de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés de dossier ou utilisez BitLocker.


1.	Ouvrez une **Invite de commandes**.
2.	Exécutez la commande suivante pour télécharger le fichier de paramètres de publication.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	La commande ouvre la page web à partir de laquelle vous pouvez télécharger le fichier de paramètres de publication.

3.	À l'invite d'enregistrement du fichier, cliquez sur **Enregistrer** et indiquez un emplacement pour l'enregistrement du fichier.
5.	À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

		azure account import <path/to/the/file>

	
#### <a id="cliprovision"></a>Approvisionnement de clusters HDInsight à l'aide d'une ligne de commande interplateforme Azure

Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant la ligne de commande interplateforme :

- Création d'un compte Azure Storage
- Approvisionnement d'un cluster


**Pour créer un compte Azure Storage** :

HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage est nécessaire avant de pouvoir créer un cluster HDInsight. Il doit se trouver dans le même centre de données.

- À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour créer un compte Azure Storage :

		azure storage account create [options] <StorageAccountName>

	À l'invite demandant d'indiquer un emplacement, sélectionnez l'endroit où vous souhaitez approvisionner un cluster HDINsight. Le stockage doit être effectué là où se trouve le cluster HDinsight. Actuellement, seules les régions **Est de l'Asie**, **Sud-Est de l'Asie**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Centre-Nord des États-Unis** et **Centre-Sud des États-Unis** peuvent héberger des clusters HDInsight.  

Pour plus d'informations sur la création d'un compte de stockage Azure à l'aide du portail de gestion Azure, consultez la rubrique [Création, gestion ou suppression d'un compte de stockage][azure-create-storageaccount].

Si vous disposez déjà d'un compte de stockage, mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

	-- lists storage accounts
	azure storage account list

	-- Shows information for a storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a storage account
	azure storage account keys list <StorageAccountName>

Pour plus d'informations sur l'obtention d'informations à l'aide du portail de gestion, consultez la section  * Affichage, copie et régénération de clés d'accès de stockage* de la rubrique [Création, gestion ou suppression d'un compte de stockage][azure-create-storageaccount].

Un cluster HDInsight requiert également un conteneur dans un compte de stockage. Si le compte de stockage que vous fournissez ne contient pas de conteneur, la commande  *azure hdinsight cluster create* vous invite à entrer un nom de conteneur avant de le créer. Cependant, si vous voulez créer le conteneur au préalable, vous pouvez utiliser la commande suivante :

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Une fois que le compte de stockage et le conteneur d'objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour approvisionner un cluster HDInsight**

- À partir de la fenêtre d'invite de commandes, exécutez la commande suivante :

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <StorageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>		

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Pour approvisionner un cluster HDInsight à l'aide d'un fichier de configuration**

Généralement, vous approvisionnez un cluster HDInsight, vous exécutez les tâches, puis vous supprimez le cluster pour réduire les coûts. L'interface de ligne de commande vous donne la possibilité d'enregistrer les configurations dans un fichier, afin de pouvoir les réutiliser chaque fois que vous approvisionnez un cluster.

- À partir de la fenêtre d'invite de commandes, exécutez les commandes suivantes :


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

		#If requred, include commands to use additional blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster using the config file
		azure hdinsight cluster create --config <file>

	>[WACOM.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l'affichage du serveur, cliquez sur **Configurer**, puis pour **Services Microsoft Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Pour énumérer et afficher les détails de cluster**

- Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Pour supprimer un cluster**

- Utilisez les commandes suivantes pour supprimer un cluster :

		azure hdinsight cluster delete <ClusterName>



###<a id="sdk"></a>Utilisation du Kit de développement logiciel (SDK) HDInsight .NET
Le kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET.

Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight sur Linux en utilisant le kit de développement logiciel (SDK)SDK :

- Installation du kit de développement logiciel (SDK) .NET HDInsight
- Création d'un certificat auto-signé
- Création d'une application console
- Exécution de l'application


**Pour installer le SDK .NET HDInsight**

Vous pouvez installer la dernière version publiée du kit de développement logiciel (SDK) disponible sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Pour créer un certificat auto-signé**

Créez un certificat auto-signé, installez-le sur votre poste de travail et téléchargez-le dans votre abonnement Azure. Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138).


**Création d'une application console Visual Studio**

1. Ouvrez Visual Studio 2013.

2. Dans le menu Fichier, cliquez sur **Nouveau**, puis sur **Projet**.

3. Dans Nouveau projet, entrez ou sélectionnez les valeurs suivantes :

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriété</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valeur</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Catégorie</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modèles/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. Cliquez sur **OK** pour créer le projet.

5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**.

6. Exécutez les commandes suivantes dans la console pour installer les packages.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Cette commande ajoute des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

7. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8. Ajoutez les instructions using suivantes au début du fichier :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Dans la fonction Main(), copiez et collez le code suivant :

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;
		
		//if required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores
		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
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
			HiveMetastore = hiveMetastore,		//only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//only if you created an ooziemetastore object earlier
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

10. Remplacez les variables au début de la fonction main().

**Pour exécuter l'application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. La création d'un cluster HDInsight peut prendre plusieurs minutes.



##<a id="nextsteps"></a> Étapes suivantes
Cet article vous a présenté différentes méthodes pour configurer un cluster HDInsight Hadoop sur Linux. Pour en savoir plus, consultez les articles suivants :

- [Utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md). Familiarisez-vous avec l'utilisation d'un cluster HDInsight sur Linux.
- [Gestion de clusters HDInsight à l'aide d'Ambari | Aure](hdinsight-hadoop-manage-ambari.md). Découvrez comment gérer et contrôler votre Hadoop Linux sur le cluster HDInsight à l'aide d'Ambari Web ou de l'API REST d'Ambari. 
- [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce]. Découvrez les différentes façons d'exécuter des tâches MapReduce sur un cluster.
- [Utilisation de Hive avec HDInsight][hdinsight-use-hive]. Découvrez les différentes façons d'exécuter une requête Hive sur un cluster.
- [Utilisation de Pig avec HDInsight][hdinsight-use-pig]. Découvrez les différentes façons d'exécuter une tâche Pig sur un cluster.
- [Utilisation du stockage d'objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md). Découvrez comment HDInsight utilise le stockage d'objets blob Azure pour stocker des données pour les clusters HDInsight.
- [Téléchargement de données vers HDInsight][hdinsight-upload-data]. Découvrez comment utiliser des données stockées dans un stockage d'objets blob Azure pour un cluster HDInsight.

[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

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
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=47-->
