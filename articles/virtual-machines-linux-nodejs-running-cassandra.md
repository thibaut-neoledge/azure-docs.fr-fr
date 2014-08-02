<properties linkid="services-linux-cassandra-with-linux" urlDisplayName="Cassandra with Linux" pageTitle="Run Cassandra with Linux on Azure" metaKeywords="" description="Explains how to run a Cassandra cluster on Linux in Azure Virtual Machines." metaCanonical="" services="virtual-machines" documentationCenter="Node.js" title="Running Cassandra with Linux on Azure and Accessing it from Node.js" authors="" solutions="" manager="" editor="" />

Exécution de Cassandra avec Linux sur Azure et accès au cluster depuis Node.js
==============================================================================

**Auteur :** Hanu Kommalapati

Sommaire
--------

-   [Vue d'ensemble](#overview)
-   [Schéma de déploiement de Cassandra](#schematic)
-   [Déploiement composite](#composite)
-   [Déploiement d'Azure Virtual Machines](#deployment)
-   [Tâche 1 : déploiement d'un cluster Linux](#task1)
-   [Tâche 2 : configuration de Cassandra sur chaque machine virtuelle](#task2)
-   [Tâche 3 : accès au cluster Cassandra depuis Node.js](#task3)
-   [Conclusion](#conclusion)

Vue d'ensemble
--------------

Azure offre un service de base de données NoSQL via le stockage de tables Azure qui permet un stockage sans schéma des objets métier. Ce service peut être utilisé depuis Node.JS, .NET, Java et d'autres langages compatibles HTTP et REST. Toutefois, jusqu'à présent, d'autres bases de données NoSQL connues telles que Cassandra et Couchbase ne pouvaient pas être exécutées sur la plateforme PaaS d'Azure en raison de son modèle de service cloud sans état. Azure Virtual Machines permet désormais d'exécuter ces bases de données NoSQL sur Azure sans modifier le code base. Ce document explique comment exécuter un cluster Cassandra sur des machines virtuelles et y accéder depuis Node.js. Il ne couvre pas le déploiement de Cassandra pour les opérations de production dans un environnement réel où un cluster Cassandra contenant plusieurs centres de données doit être appréhendé avec des stratégies de sauvegarde et de récupération associées. Dans cet exercice, nous utiliserons la version Ubuntu 12.04 de Linux et Cassandra 1.0.10, mais le processus peut s'appliquer à n'importe quelle distribution de Linux.

Schéma de déploiement de Cassandra
----------------------------------

La fonctionnalité Azure Virtual Machines permet d'exécuter des bases de données NoSQL comme [Cassandra](http://wiki.apache.org/cassandra/) sur un cloud public Microsoft aussi facilement que dans un environnement de cloud privé. La seule différence concerne la configuration du réseau virtuel qui est propre à l'infrastructure Azure Virtual Machines. Lors de la rédaction de ce document, Cassandra n'était pas disponible en tant que service géré sur Azure. Par conséquent, dans cet article, nous étudierons la configuration d'un cluster Cassandra sur Azure Virtual Machines ainsi que l'accès à celui-ci à partir d'une autre instance de Linux également hébergée dans Azure Virtual Machines. Les extraits de code Node.js présentés peuvent également être utilisés à partir d'une application ou d'un service Web hébergé par PaaS. L'un des points forts d'Azure est qu'il permet au modèle d'application composite de bénéficier des avantages de PaaS et d'IaaS.

Deux modèles de déploiement peuvent être utilisés pour l'environnement d'application Cassandra : le déploiement Azure Virtual Machines intégré et un déploiement composite. Dans le cadre d'un déploiement composite, un cluster Cassandra hébergé par Azure Virtual Machines est utilisé à partir d'une application Web (ou d'un service Web) Azure hébergé par PaaS à l'aide de l'interface Thrift via l'équilibrage de charge. Bien que chaque nœud Cassandra traite par proxy la requête adressée à d'autres nœuds homologues en cas de défaillance d'un espace de clé, l'équilibrage de charge contribue à équilibrer la charge des requêtes au niveau de l'entrée. Il crée également un bac à sable (sandbox) protégé par un pare-feu pour mieux contrôler les données.

Déploiement composite
---------------------

L'objectif d'un déploiement composite est d'optimiser l'utilisation de PaaS tout en réduisant l'encombrement de la machine virtuelle au strict minimum afin de limiter la surcharge imposée par la gestion de l'infrastructure des machines virtuelles. Compte tenu de la surcharge de gestion du serveur, déployez uniquement les composants qui nécessitent un comportement avec état dont la modification s'avère difficile pour diverses raisons, comme le temps d'accès au marché, l'absence de visibilité sur le code source et l'accès de bas niveau au système d'exploitation.

![Schéma de déploiement composite](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

Déploiement d'Azure Virtual Machines
------------------------------------

![Déploiement d'une machine virtuelle](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

Dans les schémas ci-dessus, un cluster Cassandra à 4 nœuds est déployé dans Azure Virtual Machines derrière un équilibrage de charge configuré pour autoriser le trafic Thrift. L'application PaaS hébergée par Azure accède au cluster en utilisant des bibliothèques Thrift au langage spécifique. Des bibliothèques sont disponibles pour différents langages, comme Java, C\#, Node.js, Python et C++. Le déploiement Azure Virtual Machines intégré illustré dans le deuxième schéma utilise les données des applications exécutées dans un autre service cloud hébergé sur Azure Virtual Machines.

Tâche 1 : déploiement d'un cluster Linux
----------------------------------------

Dans la version préliminaire de Virtual Machines, pour que les machines virtuelles Linux appartiennent au même réseau virtuel, toutes les machines doivent être déployées dans le même service cloud. La séquence standard pour créer un cluster est la suivante :

![Schéma de la séquence de création d'un cluster](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux4.png)

**Étape 1 : génération de la paire de clés SSH**

Au moment du déploiement, Azure requiert une clé publique X509 encodée PEM ou DER. Générez une paire de clés publiques/privées en suivant les instructions de la rubrique [Utilisation de SSH avec Linux sur Azure](http://www.windowsazure.com/en-us/manage/linux/how-to-guides/ssh-into-linux/). Si vous envisagez d'utiliser putty.exe en tant que client SSH sur Windows ou Linux, vous devez convertir la clé privée RSA encodée PEM au format PPK via puttygen.exe. Pour ce faire, suivez les instructions de la page [Génération d'une paire de clés SSH pour le déploiement d'une machine virtuelle Linux sur Windows Azure](http://blogs.msdn.com/b/hanuk/archive/2012/06/07/generating-ssh-key-pair-for-linux-vm-deployment-on-windows-azure.aspx).

**Étape 2 : création d'une machine virtuelle Ubuntu**

Pour créer la première machine virtuelle Ubuntu, connectez-vous à la version préliminaire du portail Azure, cliquez sur **Nouveau**, **Machine virtuelle**, **From Gallery**, **Unbuntu Server 12.xx**, puis cliquez sur la flèche droite. Un didacticiel décrivant la création d'une machine virtuelle Linux est disponible à la rubrique [Création d'une machine virtuelle exécutant Linux](http://www.windowsazure.com/en-us/manage/linux/tutorials/virtual-machine-from-gallery/).

Entrez ensuite les informations suivantes sur l'écran VM Configuration :

<table data-morhtml="true">
	<tr data-morhtml="true">
		<th data-morhtml="true">Nom du champ</th>
		<th data-morhtml="true">Valeur du champ</th>
		<th data-morhtml="true">Remarques</th>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Nom de la machine virtuelle</td>
		<td data-morhtml="true">hk-cas1</td>
		<td data-morhtml="true">Nom d'h&ocirc;te de la machine virtuelle</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Nouveau nom d'utilisateur</td>
		<td data-morhtml="true">localadmin</td>
		<td data-morhtml="true">Dans Ubuntu&nbsp;12.xx, &laquo;&nbsp;admin&nbsp;&raquo; est un nom d'utilisateur r&eacute;serv&eacute;.</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Nouveau mot de passe</td>
		<td data-morhtml="true"><i data-morhtml="true">Mot de passe fort</i></td>
		<td data-morhtml="true"></td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Confirmer le mot de passe</td>
		<td data-morhtml="true"><i data-morhtml="true">Mot de passe fort</i></td>
		<td data-morhtml="true"></td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Taille</td>
		<td data-morhtml="true">Small</td>
		<td data-morhtml="true">S&eacute;lectionnez la machine virtuelle en fonction des besoins E/S. </td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Secure using SSH Key for Authentication</td>
		<td data-morhtml="true">Clic sur la case &agrave; cocher</td>
		<td data-morhtml="true">D&eacute;terminez si vous souhaitez utiliser une cl&eacute; SSH pour la s&eacute;curisation.</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Certificat</td>
		<td data-morhtml="true"><i data-morhtml="true">Nom de fichier du certificat de cl&eacute; publique</i></td>
		<td data-morhtml="true">Cl&eacute; publique SSH encod&eacute;e DER ou PEM g&eacute;n&eacute;r&eacute;e avec OpenSSL ou d'autres outils</td>
	</tr>
</table>

Entrez les informations suivantes sur l'écran VM Mode :

<table data-morhtml="true">
	<tr data-morhtml="true">
		<th data-morhtml="true">Nom du champ</th>
		<th data-morhtml="true">Valeur du champ</th>
		<th data-morhtml="true">Remarques</th>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Standalone Virtual VM</td>
		<td data-morhtml="true">&laquo;&nbsp;cocher&nbsp;&raquo; la case d'option</td>
		<td data-morhtml="true">S'applique &agrave; la premi&egrave;re machine virtuelle. Pour les machines virtuelles suivantes, nous utiliserons l'option &laquo;&nbsp;Connect to Existing VM&nbsp;&raquo;.</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Nom DNS</td>
		<td data-morhtml="true"><i data-morhtml="true">nom unique</i>.cloudapp.net</td>
		<td data-morhtml="true">Donnez un nom d'&eacute;quilibrage de charge non sp&eacute;cifique &agrave; la machine.</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Compte de stockage</td>
		<td data-morhtml="true"><i data-morhtml="true">Compte de stockage par d&eacute;faut</i></td>
		<td data-morhtml="true">Utilisez le compte de stockage par d&eacute;faut que vous avez cr&eacute;&eacute;.</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Region/Affinity Group/Virtual Network</td>
		<td data-morhtml="true">Ouest des &Eacute;tats-Unis</td>
		<td data-morhtml="true">S&eacute;lectionnez une r&eacute;gion &agrave; partir de laquelle vos applications&nbsp;Web auront acc&egrave;s au cluster Cassandra.</td>
	</tr>
</table>

Répétez la procédure ci-dessus pour toutes les machines virtuelles qui appartiendront au cluster Cassandra. À ce stade, toutes les machines appartiennent au même réseau et peuvent mutuellement s'adresser des requêtes Ping. Si la commande Ping ne fonctionne pas, vérifiez la configuration du pare-feu de la machine virtuelle (par exemple, iptables) pour vous assurer que le protocole ICMP est autorisé. Veillez à désactiver ICMP une fois la connectivité réseau testée afin de réduire le vecteur d'attaque.

**Étape 3 : ajout d'un point de terminaison Thrift équilibré en charge**

À l'issue des étapes 1 et 2, un point de terminaison SSH doit déjà être défini pour chaque machine virtuelle. Ajoutons maintenant le point de terminaison Thrift équilibré en charge avec un port public 9160. La séquence est la suivante :

a. Dans la vue des détails de la première machine virtuelle, cliquez sur « Ajouter un point de terminaison ».

b. Sur l'écran « Add endpoint to virtual machine », sélectionnez la case d'option « Ajouter un point de terminaison ».

c. Cliquez sur la flèche droite.

d. Sur l'écran « Specify endpoint details », entrez ce qui suit :

<table data-morhtml="true">
	<tr data-morhtml="true">
		<th data-morhtml="true">Nom du champ</th>
		<th data-morhtml="true">Valeur du champ</th>
		<th data-morhtml="true">Remarques</th>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Nom</td>
		<td data-morhtml="true">Cassandra</td>
		<td data-morhtml="true">Il doit s'agir d'un nom de point de terminaison unique.</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Protocole</td>
		<td data-morhtml="true">TCP</td>
		<td data-morhtml="true"></td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Port public</td>
		<td data-morhtml="true">9160</td>
		<td data-morhtml="true">Port Thrift par d&eacute;faut </td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Port priv&eacute;</td>
		<td data-morhtml="true">9160</td>
		<td data-morhtml="true">Sauf si vous avez modifi&eacute; ce champ dans cassandra.yaml</td>
	</tr>
</table>

À l'issue de ces opérations, le champ LOAD BALANCED du point de terminaison Cassandra de la première machine virtuelle sera défini sur « NON ». Ignorez ce paramètre pour le moment, car il sera remplacé par « OUI » lorsque nous aurons ajouté ce point de terminaison aux machines virtuelles suivantes.

e. Sélectionnez maintenant la deuxième machine virtuelle et ajoutez le point de terminaison en répétant la procédure ci-dessus. La seule différence est que vous devez ici sélectionner « Load-balance traffic on an existing endpoint » et choisir « cassandra-960 » dans la liste déroulante. À ce stade, le mappage des points de terminaison avec les deux machines virtuelles modifie l'état du champ LOAD BALANCED qui passe de « NON » à « OUI ».

Répétez le point « e » pour les nœuds suivants du cluster.

Maintenant que les machines virtuelles sont prêtes, il est temps de configurer Cassandra sur chacune d'elles. Dans la mesure où Cassandra n'est pas un composant standard sur la plupart des distributions Linux, nous allons procéder à un déploiement manuel.

[Veuillez noter que nous avons ici recours à une approche manuelle pour installer le logiciel sur chaque machine virtuelle. Toutefois, ce processus peut être accéléré en configurant une machine virtuelle Cassandra opérationnelle, en la capturant comme image de base et en créant des instances supplémentaires à partir de cette image de base. Des instructions relatives à la capture de l'image Linux sont disponibles à la rubrique [Capture de l'image d'une machine virtuelle Linux](https://www.windowsazure.com/en-us/manage/linux/how-to-guides/capture-an-image/).]

Tâche 2 : configuration de Cassandra sur chaque machine virtuelle
-----------------------------------------------------------------

**Étape 1 : conditions préalables à l'installation**

Cassandra requiert une machine virtuelle Java. Par conséquent, installez la dernière version de JRE en utilisant la commande suivante pour les distributions dérivées de Debian comme Ubuntu :

    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer

**Étape 2 : installation de Cassandra**

1.  Connectez-vous à la machine virtuelle Linux (Ubuntu) via SSH.

2.  Utilisez wget pour télécharger Cassandra à partir du miroir suggéré à l'adresse (http://cassandra.apache.org/download/)[http://cassandra.apache.org/download/] vers le répertoire « \~/downloads », sous le nom « apache-cassandra-bin.tar.gz ». Veuillez noter que le numéro de la version n'apparaît pas dans le fichier téléchargé afin que la publication reste non spécifique à la version.

3.  Décompressez le fichier .tar dans le répertoire de connexion par défaut en exécutant la commande suivante :

        tar -zxvf downloads/apache-cassandra-bin.tar.gz

    La commande ci-dessus décompresse l'archive dans le répertoire apache-cassandra- [version].

4.  Créez les deux répertoires par défaut suivants pour accueillir les journaux et données :

         $ sudo chown -R /var/lib/cassandra
         $ sudo chown -R /var/log/cassandra

5.  Octroyez des autorisations d'accès en écriture à l'identité d'utilisateur sous laquelle Cassandra sera exécuté.

        a.   sudo chown -R <user>:<group> /var/lib/cassandra
        b.  sudo chown -R <user>:<group> /var/log/cassandra
        To use current user context, replace the <user> and <group> with $USER and $GROUP

6.  Démarrez Cassandra à partir du répertoire apache-cassandra-[version]/bin en utilisant la commande suivante :

        $ ./cassandra

La commande ci-dessus démarre le nœud Cassandra en arrière-plan. Utilisez -cassandra "f" pour démarrer le processus en mode premier plan.

L'erreur mx4j peut apparaître dans le journal. Cassandra fonctionnera normalement sans mx4j, mais sa présence est requise pour gérer l'installation. Mettez fin au processus Cassandra avant de passer à l'étape suivante.

**Étape 3 : installation de mx4j**

    a)   Téléchargez mx4j : wget [http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download](http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download) -O mx4j.tar.gz
    b)  tar -zxvf mx4j.tar.gz
    c)  cp mx4j-23.0.2/lib/*.jar ~/apache-cassandra-<version>/lib
    d)  rm -rf mx4j-23.0.2
    e)  rm mx4j.tar.gz

Redémarrez le processus Cassandra.

**Étape 4 : test de l'installation de Cassandra**

Exécutez la commande suivante à partir du répertoire Bin de Cassandra pour vous connecter via le client Thrift :

    cassandra-cli -h localhost -p 9160

**Étape 5 : activation de Cassandra pour les connexions externes**

Par défaut, Cassandra est uniquement configuré pour écouter sur l'adresse de retour de boucle, par conséquent cette modification est obligatoire pour les connexions externes.

Modifiez « conf/cassandra.yaml » en remplaçant **listen\_address** et **rpc\_address** par l'adresse IP ou le nom d'hôte du serveur de manière à ce que le nœud actuel soit visible par les autres nœuds ainsi que par les équilibrages de charge externes.

Répétez les étapes 1 à 5 pour tous les nœuds du cluster.

Maintenant que toutes les machines virtuelles individuelles sont prêtes et dotées du logiciel requis, il est temps d'établir la communication entre les nœuds via la configuration de l'amorçage. Pour plus d'informations sur la configuration d'un cluster à plusieurs nœuds, consultez la page <http://wiki.apache.org/cassandra/MultinodeCluster>.

**Étape 6 : configuration d'un cluster à plusieurs nœuds**

Modifiez cassandra.yaml en remplaçant les propriétés suivantes sur toutes les machines virtuelles :

**a) nom\_cluster**

Le nom par défaut du cluster est défini sur « Test Cluster » ; remplacez-le par un nom permettant d'identifier votre application. Exemple : « AppStore ». Si vous avez déjà démarré le cluster avec « Test Cluster » pour effectuer un test pendant l'installation, vous recevrez une erreur signalant une incohérence au niveau des noms. Pour y remédier, supprimez tous les fichiers du répertoire /var/lib/cassandra/data/system.

**b) amorces**

Les adresses IP spécifiées ici seront utilisées par les nouveaux nœuds pour en savoir plus sur la topologie en anneau. Définissez les nœuds les plus fiables comme amorces au format virgules de séparation : « *host1*,*host2* ». Exemple : « hk-ub1,hk-ub2 ».

Nous accepterons les jetons par défaut fournis par les serveurs d'amorçage, car il ne s'agit pas du propos de cet exercice. Pour une génération optimale des jetons, reportez-vous au script Python de la page : <http://wiki.apache.org/cassandra/GettingStarted>.

Redémarrez Cassandra sur tous les nœuds pour appliquer les modifications précédentes.

**Étape 7 : test du cluster à plusieurs nœuds**

L'outil Nodetool installé dans le répertoire Bin de Cassandra facilite les opérations relatives au cluster. Nous utiliserons l'outil Nodetool pour vérifier la configuration du cluster à l'aide du format de commande suivant :

    $ bin/nodetool -h <hostname> -p 7199 ring

Si la configuration est correcte, l'outil affichera les informations présentées ci-dessous pour un cluster à 3 nœuds :

<table data-morhtml="true">
	<tr data-morhtml="true">
		<td data-morhtml="true">Adresse</td>
		<td data-morhtml="true">Contr&ocirc;leur de domaine</td>
		<td data-morhtml="true">Rack</td>
		<td data-morhtml="true">Statut</td>
		<td data-morhtml="true">&Eacute;tat</td>
		<td data-morhtml="true">Chargement</td>
		<td data-morhtml="true">Appartenance</td>
		<td data-morhtml="true">Jeton</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true"></td>
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true">149463697837832744402916220269706844972</td>	
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">10.26.196.68</td>
		<td data-morhtml="true">datacenter1</td>	
		<td data-morhtml="true">rack1</td>	
		<td data-morhtml="true">Up</td>	
		<td data-morhtml="true">Normal</td>	
		<td data-morhtml="true">15,69&nbsp;Ko</td>	
		<td data-morhtml="true">25,98&nbsp;%</td>	
		<td data-morhtml="true">114445918355431753244435008039926455424</td>	
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">10.26.198.81</td>
		<td data-morhtml="true">datacenter1</td>	
		<td data-morhtml="true">rack1</td>	
		<td data-morhtml="true">Up</td>	
		<td data-morhtml="true">Normal</td>	
		<td data-morhtml="true">15,69&nbsp;Ko</td>	
		<td data-morhtml="true">53,44%</td>	
		<td data-morhtml="true">70239176883275351288292106998553981501</td>	
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">10.26.198.84</td>
		<td data-morhtml="true">datacenter1</td>	
		<td data-morhtml="true">rack1</td>	
		<td data-morhtml="true">Up</td>	
		<td data-morhtml="true">Normal</td>	
		<td data-morhtml="true">18,35&nbsp;Ko</td>	
		<td data-morhtml="true">25,98&nbsp;%</td>	
		<td data-morhtml="true">149463697837832744402916220269706844972</td>	
	</tr>
</table>

À ce stade, le cluster est prêt à accueillir les clients Thrift via l'URL du service cloud (nom DNS attribué lors de la création de la première machine virtuelle) créée pendant la tâche « Déploiement d'un cluster Linux ».

Tâche 3 : accès au cluster Cassandra depuis Node.js
---------------------------------------------------

Créez une machine virtuelle Linux sur Azure à l'aide du processus décrit lors des tâches précédentes. Vérifiez que cette machine virtuelle est autonome, car nous allons l'utiliser comme client pour accéder au cluster Cassandra. Nous allons installer Node.js, NPM et [cassandra-client](https://github.com/racker/node-cassandra-client) depuis GitHub avant de nous connecter au cluster Cassandra à partir de cette machine virtuelle :

**Étape 1 : installation de Node.js et NPM**

a) Installez les éléments requis.

    sudo apt-get install g++ libssl-dev apache2-utils make

b) Nous allons utiliser la source de GitHub pour les opérations de compilation et d'installation ; avant de pouvoir cloner le référentiel, nous devons installer le composant d'exécution principal de Git :

    sudo apt-get install git-core

c) Clonez le référentiel du nœud.

    git clone git://github.com/joyent/node.git

d) La commande ci-dessus crée le répertoire en lui attribuant le nom « node ». Exécutez la séquence de commandes suivante pour compiler et installer Node.js :

    cd node
    ./configure
    make
    sudo make install

e) Installez NPM à partir de fichiers binaires stables en exécutant la commande suivante :

    curl http://npmjs.org/install.sh | sh

**Étape 2 : installation du package cassandra-client**

    npm cassandra-client 

**Étape 3 : préparation du stockage Cassandra**

Le stockage Cassandra repose sur des concepts d'espace de clé (KEYSPACE) et de famille de colonnes (COLUMNFAMILY) semblables aux structures de base de données (DATABASE) et de table (TABLE) du langage SGBDR. Chaque KEYSPACE contient un ensemble de définitions COLUMNFAMILY. Chaque COLUMNFAMILY contient un ensemble de lignes et chaque ligne contient différentes colonnes, comme le montre la vue composite ci-dessous :

![Lignes et colonnes](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux3.png)

Nous allons utiliser le cluster Cassandra précédemment déployé pour illustrer l'accès depuis Node.js en créant et en interrogeant les structures de données ci-dessus. Nous allons créer un script Node.js simple afin de procéder à la préparation de base du cluster pour stocker les données du client. Les techniques présentées dans le script sont facilement utilisables dans une application ou des services Web Node.js. N'oubliez pas que ces extraits sont uniquement destinés à illustrer le fonctionnement. Pour les solutions réelles, le code présenté peut largement être amélioré (notamment en termes de sécurité, de journalisation, d'extensibilité, etc.).

Nous allons définir les variables requises au niveau du script pour inclure PooledConnection à partir du module cassandra-client ainsi que les paramètres de nom d'espace de clé et de connexion d'espace de clé fréquemment utilisés :

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

Pour préparer le stockage des données du client, nous devons d'abord créer un KEYSPACE à l'aide de l'exemple de script suivant :

    casdemo.js: 
    function createKeyspace(callback){
       var cql = 'CREATE KEYSPACE ' + ksName + ' WITH 
       strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
       var sysConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'],  
                             keyspace: 'system', use_bigints: false };
       var con = new pooledCon(sysConOptions);
       con.execute(cql,[],function(err) {
       if (err) {
         console.log("Failed to create Keyspace: " + ksName);
         console.log(err);
       }
       else {
         console.log("Created Keyspace: " + ksName);
         callback(ksConOptions, populateCustomerData);
       }
       });
       con.shutdown();
    } 

La fonction createKeyspace utilise une fonction de rappel comme argument afin d'exécuter la fonction de création de la COLUMNFAMILY, car KEYSPACE est une condition préalable à la création de la famille de colonnes. Notez que nous devons nous connecter au KEYSPACE « système » pour la définition du KEYSPACE de l'application. Dans ces extraits, le [langage CQL (Cassandra Query Language)](http://cassandra.apache.org/doc/cql/CQL.html) est systématiquement utilisé pour les interactions avec le cluster. Dans la mesure où le langage CQL composé dans le script ci-dessus ne dispose d'aucune marque de paramètre, nous utilisons un ensemble de paramètres vide (« [] ») pour la méthode PooledConnection.execute().

Une fois l'espace de clé créé, la fonction createColumnFamily(), présentée dans l'extrait suivant, est exécutée pour créer les définitions COLUMNFAMILY requises :

    casdemo.js: 
    //Crée COLUMNFAMILY
    function createColumnFamily(ksConOptions, callback){
      var params = ['customers_cf','custid','varint','custname',
                    'text','custaddress','text'];
      var cql = 'CREATE COLUMNFAMILY 
     (
     
     PRIMARY KEY,
     
    , 
     
    )';
    var con =  new pooledCon(ksConOptions);
      con.execute(cql,params,function(err) {
          if (err) {
             console.log("Failed to create column family: " + params[0]);
             console.log(err);
          }
          else {
             console.log("Created column family: " + params[0]);
             callback();
          }
      });
      con.shutdown();
    } 

Le modèle de langage CQL paramétrable est combiné à un objet params afin de générer un langage CQL valide pour la création de la COLUMNFAMILY. Une fois la COLUMNFAMILY créée, le rappel fourni, dans ce cas populateCustomerData(), est appelé dans le cadre de la chaîne d'appel asynchrone.

    casdemo.js: 
    //Renseignez les données
    function populateCustomerData() {
       var params = ['John','Infinity Dr, TX', 1];
       updateCustomer(ksConOptions,params);

       params = ['Tom','Fermat Ln, WA', 2];
       updateCustomer(ksConOptions,params);
    }

    //La mise à jour entraînera aussi l'insertion de l'enregistrement si aucun autre n'existe
    function updateCustomer(ksConOptions,params)
    {
      var cql = 'UPDATE customers_cf SET custname=
    ,custaddress=
     where 
                 custid=
    ';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,params,function(err) {
          if (err) console.log(err);
          else console.log("Inserted customer : " + params[0]);
      });
      con.shutdown();
    }

populateCustomerData() insère quelques lignes dans la COLUMNFAMILY intitulée customers\_cf. Dans le langage CQL, la commande de mise à jour UPDATE insère l'enregistrement si celui-ci n'est pas déjà présent dans le processus qui rend l'instruction d'insertion INSERT redondante.

Pour le moment, nous avons créé la chaîne de rappel : createKeyspace() vers createColumnFamily() vers populateCustomerData(). Il est maintenant temps d'exécuter le code via l'extrait suivant :

    casdemo.js:
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    createKeyspace(createColumnFamily);
    //Suite non présentée

Exécutez la commande suivante à partir de l'invite de l'interpréteur de commandes pour exécuter le script :

    //La commande suivante crée le KEYSPACE, la COLUMNFAMILY et //insère deux enregistrements de client
    $ node casdemo.js

La méthode readCustomer() accède au cluster hébergé par Azure et affiche l'extrait JSON extrait après l'exécution de la requête CQL :

    casdemo.js: 
    //Lisez les deux lignes insérées ci-dessus
    function readCustomer(ksConOptions)
    {
      var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,[],function(err,rows) {
          if (err) 
             console.log(err);
          else 
             for (var i=0; i<rows.length; i++)
                console.log(JSON.stringify(rows[i]));
        });
       con.shutdown();
    } 

Modifiez casdemo.js pour ajouter la fonction ci-dessus et appeler celle-ci après avoir commenté la méthode createKeyspace() précédemment appelée, comme illustré ci-dessous :

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    //createKeyspace(createColumnFamily);
    readCustomer(ksConOptions)
    //Suite du code non présentée

Conclusion
----------

La fonctionnalité Azure Virtual Machines permet de créer des machines virtuelles Linux (images fournies par les partenaires de Microsoft) et Windows afin de procéder à la migration des serveurs et applications existants sans effectuer la moindre modification. Le serveur de base de données NoSQL Cassandra décrit dans cet article en est un exemple. Le cluster Cassandra configuré dans ce document est accessible aux services cloud hébergés par Azure ainsi qu'aux clouds publics tiers et aux clouds privés des environnements de système d'exploitation Windows et Linux. Dans cet article, nous avons étudié Node.js en tant que client, mais Cassandra peut également être accessible à partir de .NET, de Java et d'environnements utilisant d'autres langages.

