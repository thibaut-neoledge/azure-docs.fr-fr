<properties 
   pageTitle="Utiliser Apache Phoenix et SQuirreL dans HDinsight | Microsoft Azure" 
   description="Découvrez comment utiliser Apache Phoenix dans HDInsight et comment installer et configurer SQuirreL sur votre poste de travail pour vous connecter à un cluster HBase dans HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/02/2015"
   ms.author="jgao"/>

# Utiliser Apache Phoenix et SQuirreL avec les clusters HBase dans HDinsight  

Découvrez comment utiliser [Apache Phoenix](http://phoenix.apache.org/) dans HDInsight et comment installer et configurer SQuirreL sur votre poste de travail pour vous connecter à un cluster HBase dans HDInsight. Pour plus d'informations sur Phoenix, consultez [Phoenix en 15 minutes ou moins](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Pour en savoir plus sur la grammaire Phoenix, consultez la page [Grammaire Phoenix](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Pour des informations sur la version de Phoenix dans HDInsight, consultez [Nouveautés des versions de cluster Hadoop fournies par HDInsight][hdinsight-versions].

##Utiliser SQLLine
[SQLLine](http://sqlline.sourceforge.net/) est un utilitaire de ligne de commande pour exécuter SQL.

###Configuration requise
Avant de pouvoir utiliser SQLLine, vous devez disposer des éléments suivants :

- **Un cluster HBase dans HDInsight**. Pour plus d'informations sur l'approvisionnement d'un cluster HBase, consultez [Prise en main d'Apache HBase dans HDInsight][hdinsight-hbase-get-started].
- **Une connexion au cluster HBase à l'aide du protocole RDP (Remote Desktop Protocol)**. Pour obtenir des instructions, consultez [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure Classic][hdinsight-manage-portal].

**Pour déterminer le nom d'hôte**

1. Ouvrez la **ligne de commande Hadoop** à partir du Bureau.
2. Exécutez la commande suivante pour obtenir le suffixe DNS :

		ipconfig

	Notez le **suffixe DNS propre à la connexion**. Par exemple, *myhbasecluster.f5.internal.cloudapp.net*. Quand vous vous connectez à un cluster HBase, vous devez vous connecter à l'un des Zookeepers avec le nom de domaine complet. Chaque cluster HDInsight contient 3 Zookeepers : *zookeeper0*, *zookeeper1* et *zookeeper2*. Le nom de domaine complet doit ressembler à *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**Pour utiliser SQLLine**

1. Ouvrez la **ligne de commande Hadoop** à partir du Bureau.
2. Exécutez les commandes suivantes pour ouvrir SQLLine :

		cd %phoenix_home%\bin
		sqlline.py [The FQDN of one of the Zookeepers]

	![hdinsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

	Commandes utilisées dans l’exemple :

		CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
		
		!tables;
		
		UPSERT INTO Company VALUES(1, 'Microsoft');
		
		SELECT * FROM Company;

Pour plus d’informations, consultez le [manuel SQLLine](http://sqlline.sourceforge.net/#manual) et la [grammaire Phoenix](http://phoenix.apache.org/language/index.html).


















##Utiliser SQuirreL

Le [client SQuirrel SQL](http://squirrel-sql.sourceforge.net/) est un programme Java graphique qui vous permet d'afficher la structure d'une base de données compatible JDBC, parcourir les données dans des tables, exécuter des commandes SQL, etc.. SQuirreL peut être utilisé pour se connecter à Phoenix Apache sur HDInsight.

Cette section vous indique comment installer et configurer SQuirreL sur votre poste de travail pour vous connecter à un cluster HBase dans HDInsight via une connexion VPN.

###Composants requis

Avant de suivre les procédures, vous devez disposer des éléments suivants :

- Un cluster HBase déployé sur un réseau virtuel Azure avec une machine virtuelle DNS. Pour des instructions, consultez [Approvisionnement de clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet]. 

	>[AZURE.IMPORTANT] Vous devez installer un serveur DNS sur le réseau virtuel. Pour obtenir des instructions, consultez la page [Configurer des serveurs DNS entre deux réseaux virtuels Azure](hdinsight-hbase-geo-replication-configure-DNS.md).

- Obtenez le suffixe DNS propre à la connexion du cluster HBase. Pour l'obtenir, ouvrez une session RDP sur le cluster, puis exécutez IPConfig. Le suffixe DNS doit ressembler à :

		myhbase.b7.internal.cloudapp.net
- Téléchargez et installez [Microsoft Visual Studio Express 2013 pour Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) sur votre station de travail. Vous utiliserez l'outil makecert du package pour créer votre certificat.  
- Téléchargez et installez l'[environnement d'exécution Java](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) sur votre station de travail. La version 3.0 ou supérieure du client SQL SQuirreL requiert la version 1.6 ou supérieure de JRE.  


###Configurer une connexion VPN de point à site au réseau virtuel Azure

La configuration d'une connexion VPN de point à site comprend 3 étapes :

1. [Configurer un réseau virtuel et une passerelle de routage dynamique](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Créer vos certificats](#Create-your-certificates)
3. [Configurer votre client VPN](#Configure-your-VPN-client)

Consultez [Configurer une connexion VPN de point à site à un réseau virtuel Azure](../vpn-gateway/vpn-gateway-point-to-site-create.md) pour plus d'informations

#### Configurer un réseau virtuel et une passerelle de routage dynamique

Assurez-vous d'avoir approvisionné un cluster HBase dans un réseau virtuel Azure (voir la configuration requise pour cette section). L'étape suivante consiste à configurer une connexion de point à site.

**Pour configurer la connexion de point à site**

1. Connectez-vous au [portail Azure Classic][azure-portal].
2. Dans le volet gauche, cliquez sur **RÉSEAUX**.
3. Cliquez sur le réseau virtuel que vous avez créé (voir [Approvisionnement de clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet]).
4. Cliquez sur **CONFIGURER** en haut.
5. Dans la section **connectivité de point à site**, sélectionnez **Configurer la connectivité de point à site**. 
6. Configurer **IP DE DÉPART** et **CIDR** pour spécifier la plage d'adresses IP qui déterminera l'adresse IP de vos clients VPN au moment de la connexion. La plage ne peut pas chevaucher les plages de votre réseau local et du réseau virtuel Azure auquel vous allez vous connecter. Par exemple, si vous avez sélectionné 10.0.0.0/20 pour le réseau virtuel, vous pouvez sélectionner 10.1.0.0/24 pour l'espace d'adressage du client. Consultez la page [Connectivité de point à site][vnet-point-to-site-connectivity] pour plus d'informations.
7. Dans la section des espaces d'adressage de réseau virtuel, cliquez sur **ajouter un sous-réseau de passerelle**.
7. Cliquez sur **ENREGISTRER** en bas de la page.
8. Cliquez sur **OUI** pour confirmer la modification. Attendez que le système ait terminé d'appliquer la modification avant de passer à la procédure suivante.


**Pour créer une passerelle de routage dynamique**

1. Dans le portail Azure Classic, cliquez sur **TABLEAU DE BORD** en haut de la page.
2. Cliquez sur **CRÉER UNE PASSERELLE** en bas de la page.
3. Cliquez sur **OUI** pour confirmer. Attendez que la passerelle soit créée.
4. Cliquez sur **TABLEAU DE BORD** en haut. Un schéma visuel du réseau virtuel est affiché :

	![Diagramme virtuel de point à site du réseau virtuel Azure][img-vnet-diagram]

	Le diagramme indique 0 connexion du client. Une fois établie la connexion au réseau virtuel, le nombre passe à un.

#### Créer vos certificats

L'une des méthodes pour créer un certificat X.509 consiste à utiliser l'outil de création de certificats (makecert.exe) fourni avec [Microsoft Visual Studio Express 2013 pour Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx).


**Pour créer un certificat racine auto-signé**

1. Dans votre station de travail, ouvrez une fenêtre d'invite de commandes.
2. Accédez au dossier des outils Visual Studio. 
3. La commande suivante dans l'exemple ci-dessous crée et installe un certificat racine dans le magasin de certificats Personal sur votre station de travail, ainsi que le fichier .cer correspondant que vous téléchargerez plus tard dans le portail Azure Classic. 

		makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

	Indiquez le répertoire de destination du fichier .cer, HBaseVnetVPNRootCertificate étant le nom que vous voulez utiliser pour le certificat.

	Ne fermez pas l'invite de commandes. Vous en aurez besoin dans la prochaine procédure.

	>[AZURE.NOTE] Comme vous avez créé un certificat racine permettant de générer des certificats clients, il peut être utile d'exporter ce certificat avec sa clé privée et de l'enregistrer à un emplacement sûr à partir duquel il pourra être récupéré.

**Pour créer un certificat client**

- Dans la même invite de commandes (sur le même ordinateur que celui où vous avez créé le certificat racine, le certificat client doit être généré à partir du certificat racine), exécutez la commande suivante :

  		makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

	HBaseVnetVPNRootCertificate est le nom du certificat racine. Il doit correspondre au nom du certificat racine.

	Le certificat racine et le certificat client sont stockés dans le magasin de certificats personnels de votre ordinateur. Utilisez certmgr.msc à des fins de vérification.

	![Certificat VPN de point à site du réseau virtuel Azure][img-certificate]

	Vous devez installer un certificat client sur chaque ordinateur que vous voulez connecter au réseau virtuel. Nous vous recommandons de créer un certificat client unique pour chaque ordinateur que vous souhaitez connecter au réseau virtuel. Pour exporter les certificats clients, utilisez certmgr.msc.

**Pour télécharger le certificat racine sur le portail Azure Classic**

1. Dans le portail Azure Classic, cliquez sur **RÉSEAU** à gauche.
2. Cliquez sur le réseau virtuel sur lequel est déployé votre cluster HBase.
3. Cliquez sur **CERTIFICATS** en haut.
4. Cliquez sur **TÉLÉCHARGER** en bas et spécifiez le fichier de certificat racine que vous avez créé dans l'avant-dernière procédure. Attendez que le certificat soit importé.
5. Cliquez sur **TABLEAU DE BORD** en haut. Le diagramme virtuel affiche l'état.


#### Configurer votre client VPN



**Pour télécharger et installer le package client VPN**

1. Dans la page TABLEAU DE BORD du réseau virtuel, dans la section Aperçu rapide, cliquez sur **Télécharger le package client VPN 64 bits** ou **Télécharger le package client VPN 32 bits** selon la version du système d'exploitation de votre station de travail.
2. Cliquez sur **Exécuter** pour installer le package.
3. À l'invite de sécurité, cliquez sur **Plus d'informations**, puis sur **Exécuter quand même**.
4. Cliquez sur **Oui** deux fois.

**Pour se connecter au VPN**

1. Sur le Bureau de votre poste de travail, cliquez sur l'icône Réseaux de la barre des tâches. Vous devez voir une connexion VPN avec le nom de votre réseau virtuel.
2. Cliquez sur le nom de la connexion VPN.
3. Cliquez sur **Connecter**.

**Pour tester la connexion VPN et la résolution de noms de domaine**

- Dans la station de travail, ouvrez une invite de commandes et testez l'un des noms suivants, le suffixe DNS du cluster HBase étant myhbase.b7.internal.cloudapp.net :

		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		headnode0.myhbase.b7.internal.cloudapp.net
		headnode1.myhbase.b7.internal.cloudapp.net
		workernode0.myhbase.b7.internal.cloudapp.net

###Installation et configuration de SQuirreL sur votre poste de travail

**Pour installer SQuirreL**

1. Téléchargez le fichier jar du client SQL SQuirreL à l’adresse [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation).
2. Ouvrez/exécutez le fichier jar. Pour cela, vous avez besoin de l'[environnement d'exécution Java](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html).
3. Cliquez sur **Suivant** deux fois.
4. Spécifiez un chemin d'accès pour lequel vous disposez de l'autorisation d'écriture, puis cliquez sur **Suivant**.
	>[AZURE.NOTE] Le dossier d'installation par défaut se trouve dans le dossier C:\\Program Files\\squirrel-sql-3.6. Pour pouvoir écrire dans ce chemin d'accès, le programme d'installation doit disposer des privilèges d'administrateur. Vous pouvez ouvrir une invite de commandes en tant qu'administrateur, accéder au dossier bin de Java et puis exécuter
	>
	>     java.exe -jar [the path of the SQuirreL jar file] 
5. Cliquez sur **OK** pour confirmer la création du répertoire cible.
6. La configuration par défaut installe les packages de base et standard. Cliquez sur **Next**.
7. Cliquez sur **Suivant** deux fois, puis sur **Terminé**.


**Pour installer le pilote Phoenix**

Le fichier jar du pilote phoenix se trouve dans le cluster HBase. Le chemin d'accès ressemble au suivant selon les versions :

	C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
Vous devez le copier sur votre poste de travail sous [Dossier d’installation SQuirreL]/lib. Le moyen le plus simple est d'ouvrir une session RDP sur le cluster, puis de copier et coller le fichier (CTRL+C et CTRL+V) pour le copier sur votre station de travail.

**Pour ajouter un pilote Phoenix à SQuirreL**

1. Ouvrez le client SQL SQuirreL dans votre poste de travail.
2. Cliquez sur l'onglet **Driver** à gauche.
2. Dans le menu **Drivers**, cliquez sur **New Driver**.
3. Entrez les informations suivantes :

	- **Name** : Phoenix
	- **Example URL** : jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
	- **Class Name** : org.apache.phoenix.jdbc.PhoenixDriver

	>[AZURE.WARNING] N'utilisez que des minuscules dans l'exemple d'URL. Vous pouvez utiliser le quorum complet zookeeper au cas où l’un d’eux est inactif. Les noms d’hôte sont zookeeper0, zookeeper1 et zookeeper2.

	![Pilote HDInsight HBase Phoenix SQuirreL][img-squirrel-driver]
4. Cliquez sur **OK**.

**Pour créer un alias pour le cluster HBase**

1. Dans SQuirreL, cliquez sur l’onglet **Aliases** à gauche.
2. Dans le menu **Aliases**, cliquez sur **New Alias**.
3. Entrez les informations suivantes :

	- **Name** : nom du cluster HBase ou un nom de votre choix.
	- **Driver** : Phoenix. Le nom du pilote doit correspondre à celui que vous avez créé dans la dernière procédure.
	- **URL** : l'URL est copiée à partir de la configuration de votre pilote. N'utilisez que des minuscules.
	- **Nom d’utilisateur** : il s’agit de texte. Étant donné que vous utilisez une connectivité VPN ici, le nom d’utilisateur n’est pas du tout utilisé.
	- **Password** : il s’agit de texte.

	![Pilote HDInsight HBase Phoenix SQuirreL][img-squirrel-alias]
4. Cliquez sur **Test**. 
5. Cliquez sur **Connecter**. Quand la connexion est établie, SQuirreL ressemble à ce qui suit :

	![HBase Phoenix SQuirreL][img-squirrel]

**Pour exécuter un test**

1. Cliquez sur l'onglet **SQL** juste à côté de l'onglet **Objects**.
2. Copiez et collez le code suivant :

		CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. Cliquez sur le bouton d'exécution.

	![HBase Phoenix SQuirreL][img-squirrel-sql]
4. Revenez à l'onglet **Objects**.
5. Développez le nom d'alias, puis **TABLE**. La nouvelle table doit être affichée.
 
##Étapes suivantes
Dans cet article, vous avez appris comment utiliser Apache Phoenix dans HDInsight. Pour plus d'informations, consultez les rubriques suivantes :

- [Vue d'ensemble de HDInsight HBase][hdinsight-hbase-overview] : HBase est une base de données NoSQL open source Apache basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de grandes quantités de données non structurées et semi-structurées.
- [Approvisionnement de clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet] :avec l'intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase.
- [Configurer la réplication HBase dans HDInsight](hdinsight-hbase-geo-replication.md) : découvrez comment configurer la réplication HBase entre deux centres de données Azure. 
- [Analyse de sentiments Twitter avec HBase dans HDInsight][hbase-twitter-sentiment] : découvrez comment effectuer une [analyse de sentiments](http://en.wikipedia.org/wiki/Sentiment_analysis) en temps réel des données volumineuses à l'aide de HBase dans un cluster Hadoop sous HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 

<!---HONumber=AcomDC_0218_2016-->