<properties
	pageTitle="Exécution d'un cluster MariaDB (MySQL) sur Azure"
	description="Création d'un cluster MySQL MariaDB + Galera sur des machines virtuelles Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="sabbour"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/15/2015"
	ms.author="v-ahsab"/>

# Cluster MariaDB (MySQL) - Didacticiel Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Nous créons un cluster [Galera](http://galeracluster.com/products/) multimaîtres de [MariaDB](https://mariadb.org/en/about/), un remplacement de dernière minute solide, évolutif et fiable pour MySQL, qui fonctionnera dans un environnement à haute disponibilité dans des machines virtuelles Azure.

## Présentation de l'architecture

Cette rubrique comprend les étapes suivantes :

1. Création d'un cluster à trois nœuds
2. Séparation des disques de données du disque du système d'exploitation
3. Création des disques de données avec le paramètre RAID 0/agrégation pour augmenter le nombre d'E/S par seconde
4. Utilisation de l'équilibreur de charge Azure pour équilibrer la charge pour les trois nœuds
5. Pour réduire les tâches répétitives, créez une image de machine virtuelle contenant MariaDB+Galera et utilisez-la pour créer d'autres machines virtuelles de cluster.

![Architecture](./media/virtual-machines-mariadb-cluster/Setup.png)

> [AZURE.NOTE]Cette rubrique utilise les outils d'[interface de ligne de commande Azure]. Assurez-vous donc de les télécharger et de les connecter à votre abonnement Azure en suivant les instructions. Si vous avez besoin d'une référence pour les commandes disponibles dans l'interface de ligne de commande Azure, consultez ce lien pour accéder à la [référence des commandes de l'interface de ligne de commande Azure]. Vous devrez également [créer une clé SSH pour l'authentification] et noter l'**emplacement du fichier .pem**.


## Création du modèle

### Infrastructure

1. Créez un groupe d'affinités pour contenir les ressources

		azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"

2. Création d'un réseau virtuel

		azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Créez un compte de stockage pour héberger tous nos disques. Notez que vous ne devez pas placer plus de 40 disques à utilisation intensive sur le même compte de stockage pour éviter d'atteindre la limite d'E/S par seconde du compte de stockage (20 000). Dans ce cas, nous sommes très éloignés de ce numéro. Pour des raisons de simplification ,nous allons donc stocker tout sur le même compte.

		azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Recherchez le nom de l'image de machine virtuelle CentOS 7

		azure vm image list | findstr CentOS
Le résultat devrait ressembler à `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Utilisez le nom obtenu dans l'étape suivante.

4. Créez le modèle de machine virtuelle, en remplaçant **/path/to/key.pem** par le chemin d'accès où vous avez stocké la clé .pem SSH générée

		azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser

5. Attachez quatre disques de données de 4 500 Go à la machine virtuelle à utiliser dans la configuration RAID

		FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd

6. Accédez avec SSH au modèle de machine virtuelle que vous avez créé sur **mariadbhatemplate.cloudapp.net:22** et connectez-vous à l'aide de votre clé privée.

### Logiciel

1. Obtenez la racine

        sudo su

2. Installez la prise en charge RAID :

     - Installez mdadm

        		yum install mdadm

     - Créez la configuration RAID0/répartition avec un système de fichiers EXT4

				mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
				mdadm --detail --scan >> /etc/mdadm.conf
				mkfs -t ext4 /dev/md0

     - Créez le répertoire de point de montage

				mkdir /mnt/data

     - Récupérez l'UUID du périphérique RAID nouvellement créé

				blkid | grep /dev/md0

     - Modifiez /etc/fstab

        		vi /etc/fstab

     - Ajoutez le périphérique afin d'activer le montage automatique au redémarrage, en remplaçant l'UUID par la valeur obtenue avec la commande **blkid** précédente

        		UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2

     - Montez la nouvelle partition

        		mount /mnt/data

3. Installez MariaDB :

     - Créez le fichier MariaDB.repo :

              	vi /etc/yum.repos.d/MariaDB.repo

     - Remplissez-le avec le contenu ci-dessous

				[mariadb]
				name = MariaDB
				baseurl = http://yum.mariadb.org/10.0/centos7-amd64
				gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
				gpgcheck=1

     - Supprimez les fichiers postfix et mariadb-libs existants pour éviter les conflits

    		yum remove postfix mariadb-libs-*

     - Installez MariaDB avec Galera

    		yum install MariaDB-Galera-server MariaDB-client galera

4. Déplacez le répertoire de données MySQL vers le périphérique en mode bloc RAID

     - Copiez le répertoire actuel de MySQL dans son nouvel emplacement et supprimez l'ancien répertoire

    		cp -avr /var/lib/mysql /mnt/data  
    		rm -rf /var/lib/mysql

     - Définissez les autorisations sur le nouveau répertoire en conséquence

        	chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  

     - Créer un lien symlink pointant l'ancien répertoire vers le nouvel emplacement sur la partition RAID

    		ln -s /mnt/data/mysql /var/lib/mysql

5. Étant donné que [SELinux interférera avec les opérations de cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), il est nécessaire de le désactiver pour la session active (jusqu'à ce qu'une version compatible soit publiée). Modifiez `/etc/selinux/config` pour le désactiver pour les redémarrages suivants :

	        setenforce 0

       puis remplacez `/etc/selinux/config` par `SELINUX=permissive`

6. Validez l'exécution de MySQL

    - Démarrez MySQL

    		service mysql start

    - Sécurisez l'installation de MySQL, définissez le mot de passe racine, supprimez les utilisateurs anonymes, en désactivant la connexion racine à distance et en supprimant la base de données de test

            mysql_secure_installation

    - Créez un utilisateur de la base de données pour les opérations de cluster et, éventuellement, vos applications

			mysql -u root -p
			GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit

   - Arrêtez MySQL

			service mysql stop

7. Créez l'espace réservé de configuration

	- Modifiez la configuration de MySQL pour créer un espace réservé pour les paramètres du cluster. Ne remplacez pas **`<Vairables>`** et ne supprimez pas les commentaires maintenant. Ces opérations auront lieu une fois que nous aurons créé une machine virtuelle à partir de ce modèle.

			vi /etc/my.cnf.d/server.cnf

	- Modifiez la section **[galera]** et effacez-la

	- Modifiez la section **[mariadb]**

			wsrep_provider=/usr/lib64/galera/libgalera_smm.so
            binlog_format=ROW
            wsrep_sst_method=rsync
			bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
			default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2

            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
			#wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. Ouvrez les ports requis sur le pare-feu (à l'aide de FirewallD sur CentOS 7)

	- MySQL : `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA : `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - GALERA IST : `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC : `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Rechargez le pare-feu : `firewall-cmd --reload`

9.  Optimisez le système pour les performances. Pour plus de détails, consultez cet article sur la [stratégie de réglage des performances]

	- Modifiez de nouveau le fichier de configuration MySQL

			vi /etc/my.cnf.d/server.cnf

	- Modifiez la section **[mariadb]** et ajoutez les éléments ci-dessous

	> [AZURE.NOTE]Il est recommandé que **innodb\\_buffer\\_pool\_size** soit égal à 70 % de la mémoire de votre machine virtuelle. Elle a été définie à 2,45 Go ici pour la machine virtuelle Azure de taille moyenne avec 3,5 Go de RAM.

	        innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
			query_cache_size = 0

10. Arrêtez MySQL, désactivez l'exécution du service MySQL au démarrage pour éviter de désorganiser le cluster lors de l'ajout d'un nouveau nœud, puis annulez l'approvisionnement de la machine.

		service mysql stop
        chkconfig mysql off
		waagent -deprovision

11. Capturez la machine virtuelle via le portail. (Actuellement, le [problème n° 1268 dans les outils d'interface de ligne de commande Azure] décrit le fait que les images capturées par les outils de l’interface de ligne de commande Azure ne capturent pas les disques de données attachés.)

	- Arrêtez la machine via le portail
    - Cliquez sur Capturer, spécifiez le nom de l'image **mariadb-galera-image**, fournissez une description et cochez « J'ai exécuté waagent ». ![Capture de la machine virtuelle](./media/virtual-machines-mariadb-cluster/Capture.png) ![Capture de la machine virtuelle](./media/virtual-machines-mariadb-cluster/Capture2.PNG)

## Création du cluster

Créez trois machines virtuelles à partir du modèle que vous venez de créer, puis configurez et démarrez le cluster.

1. Créez la première machine virtuelle CentOS 7 à partir de l'image **mariadb-galera-image** que vous avez créée, en fournissant le nom de réseau virtuel **mariadbvnet** et le sous-réseau **mariadb**, la taille **moyenne** de la machine support, en passant le nom de service cloud **mariadbha** (ou le nom auquel vous souhaitez accéder via mariadbha.cloudapp.net), en définissant le nom de cette machine sur **mariadb1** et le nom d'utilisateur sur **azureuser**, en activant l'accès SSH, en passant le fichier .pem de certificat SSH et en remplaçant **/path/to/key.pem** par le chemin d'accès où vous avez stocké la clé SSH .pem générée.

	> [AZURE.NOTE]Les commandes ci-dessous sont réparties sur plusieurs lignes pour plus de clarté, mais vous devez les entrer chacune sur une seule ligne.

		azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 22
		--vm-name mariadb1
		mariadbha mariadb-galera-image azureuser

2. Créez deux machines virtuelles supplémentaires en les _connectant_ au service cloud **mariadbha** actuellement créé, en modifiant aussi le **nom de la machine virtuelle** et en configurant le **port SSH** sur un port unique qui n'entre pas en conflit avec d'autres machines virtuelles dans le même service cloud.

		azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 23
		--vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
et pour MariaDB3

		azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 24
		--vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser

3. Vous devrez obtenir l'adresse IP interne de chacune des trois machines virtuelles pour l'étape suivante :

	![Obtention d’une adresse IP virtuelle](./media/virtual-machines-mariadb-cluster/IP.png)

4. Accédez avec SSH aux trois machines virtuelles et modifiez le fichier de configuration de chacune d'elles

		sudo vi /etc/my.cnf.d/server.cnf

	Annulez les commentaires **`wsrep_cluster_name`** et **`wsrep_cluster_address`** en supprimant le caractère **#** au début, et vérifiez que ces paramètres correspondent à ce que vous voulez. Remplacez également **`<ServerIP>`** dans **`wsrep_node_address`** et **`<NodeName>`** dans **`wsrep_node_name`** par l'adresse IP et le nom de la machine virtuelle, et annulez ces commentaires.

5. Démarrez le cluster sur MariaDB1 et laissez-le s'exécuter au démarrage

		sudo service mysql bootstrap
        chkconfig mysql on

6. Démarrez MySQL sur MariaDB2 et MariaDB3 et laissez-le s'exécuter au démarrage

		sudo service mysql start
        chkconfig mysql on

## Équilibrage de la charge du cluster
Lorsque vous avez créé les machines virtuelles en cluster, vous les avez ajoutées dans un groupe à haute disponibilité appelé **clusteravset** pour être sûr qu'ils sont placés sur différents domaines d'erreur et de mise à jour et qu'Azure n'effectue jamais de maintenance sur toutes les machines en même temps. Cette configuration remplit les critères pour être prise en charge par ce contrat de niveau de Service (SLA) Azure.

Maintenant, vous utilisez l'équilibreur de charge Azure pour équilibrer les demandes parmi nos trois nœuds.

Exécutez les commandes ci-dessous sur votre ordinateur à l'aide de l'interface de ligne de commande Azure. La structure des paramètres de commande est la suivante : `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

	azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Pour finir, étant donné que l'interface de ligne de commande définit l'intervalle de sondage d'équilibrage de charge à 15 secondes (qui peut être un peu trop long), modifiez cet intervalle dans le portail sous **Points de terminaison** pour toutes les machines virtuelles

![Ajouter un point de terminaison](./media/virtual-machines-mariadb-cluster/Endpoint.PNG)

puis cliquez sur Reconfigurer le jeu d'équilibrage de la charge

![Reconfigurer le jeu d'équilibrage de la charge](./media/virtual-machines-mariadb-cluster/Endpoint2.PNG)

et modifiez l'intervalle de sondage sur 5 secondes, puis enregistrez

![Modifier l’intervalle de sondage](./media/virtual-machines-mariadb-cluster/Endpoint3.PNG)

## Validation du cluster

La partie la plus complexe du travail est terminée. Le cluster doit maintenant être accessible à `mariadbha.cloudapp.net:3306`, qui atteindra l'équilibreur de charge et acheminera les demandes entre les trois machines virtuelles de manière fluide et efficace.

Utilisez le client MySQL de votre choix pour vous connecter ou connectez-vous simplement à partir de l'une des machines virtuelles pour vérifier que ce cluster fonctionne.

	 mysql -u cluster -h mariadbha.cloudapp.net -p

Ensuite, créez une base de données et insérez-y des données

	CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
	INSERT INTO TestTable (value)  VALUES ('Value1');
	INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Vous obtiendrez le tableau ci-dessous

	+----+--------+
	| id | value  |
	+----+--------+
	|  1 | Value1 |
	|  4 | Value2 |
	+----+--------+
	2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Dans cet article, vous avez créé un cluster MariaDB+Galera hautement disponible à trois nœuds sur Azure Virtual Machines exécutant CentOS 7. La charge des machines virtuelles est équilibrée grâce à l'équilibreur de charge Azure.

Vous souhaiterez peut-être étudier [une autre façon de mettre MySQL en cluster sur Linux] et quelques méthodes pour [optimiser et tester les performances de MySQL sur les machines virtuelles Linux Azure].

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[interface de ligne de commande Azure]: http://azure.microsoft.com/documentation/articles/xplat-cli/
[référence des commandes de l'interface de ligne de commande Azure]: http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/
[créer une clé SSH pour l'authentification]: http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[stratégie de réglage des performances]: http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/
[optimiser et tester les performances de MySQL sur les machines virtuelles Linux Azure]: http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/
[problème n° 1268 dans les outils d'interface de ligne de commande Azure]: https://github.com/Azure/azure-xplat-cli/issues/1268
[une autre façon de mettre MySQL en cluster sur Linux]: http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/

<!---HONumber=Oct15_HO3-->