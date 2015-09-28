<properties
   pageTitle="Utilisation de clés SSH avec un cluster Hadoop basé sur Linux sur Linux, Unix ou OS X | Microsoft Azure"
   description="Vous pouvez accéder à un cluster HDInsight basé sur Linux à l’aide de SSH (Secure Shell). Ce document donne des informations sur l'utilisation de SSH avec HDInsight à partir des clients Linux, Unix ou OS X."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/15/2015"
   ms.author="larryfr"/>

#Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X :

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell) vous permet d’effectuer des opérations à distance sur des clusters HDInsight sous Linux à l’aide d’une interface de ligne de commande. Ce document donne des informations sur l'utilisation de SSH avec HDInsight à partir des clients Linux, Unix ou OS X.

> [AZURE.NOTE]Les étapes de cet article partent du principe que vous utilisez un client Linux, Unix ou OS X. Bien que ces étapes puissent être effectuées sur un client Windows si vous avez installé un package qui fournit `ssh`et `ssh-keygen` (tel que Git pour Windows,) nous recommandons aux clients Windows de suivre les étapes décrites dans [Utilisation de SSH avec HDInsight (Hadoop) sous Linux à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##Configuration requise

* **ssh-keygen** et **ssh** pour les clients Linux, Unix, et OS X. Ces utilitaires sont généralement fournis avec votre système d’exploitation ou disponibles dans le système de gestion du package.

* Un navigateur web moderne qui prend en charge HTML5.

OU

* [Interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli.md).

##Qu'est-ce que SSH ?

SSH est un utilitaire permettant de se connecter et d'exécuter à distance des commandes sur un serveur distant. Avec HDInsight sous Linux, SSH établit une connexion chiffrée avec le nœud principal du cluster et fournit une ligne de commande que vous utilisez pour taper les commandes. Les commandes sont ensuite exécutées directement sur le serveur.

###Nom d'utilisateur du SSH

Un nom d’utilisateur SSH est le nom que vous utilisez pour vous authentifier auprès du cluster HDInsight. Quand vous spécifiez un nom d’utilisateur SSH lors de la création du cluster, cet utilisateur est créé sur tous les nœuds du cluster. Une fois le cluster créé, vous pouvez utiliser ce nom d’utilisateur pour vous connecter aux nœuds principaux du cluster HDInsight. À partir des nœuds principaux, vous pouvez ensuite vous connecter aux nœuds de travail individuels.

> [AZURE.NOTE]Un nom d’utilisateur SSH doit être unique. Dans la mesure où un nom d’utilisateur SSH crée un compte d’utilisateur sur le cluster HDInsight, il ne doit pas être en conflit avec des utilisateurs existants créés par HDInsight. Voici la liste des noms dont l’usage est réservé aux services exécutés sur le cluster HDInsight et qui ne peuvent pas être utilisés en tant que nom d’utilisateur SSH :
>
> root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

###Clé publique ou mot de passe SSH

Un utilisateur SSH peut utiliser un mot de passe ou une clé publique à des fins d’authentification. Un mot de passe est simplement une chaîne de texte que vous créez, tandis qu’une clé publique fait partie d’une paire de clés de chiffrement générées pour vous identifier de manière unique.

Une clé offre davantage de protection qu’un mot de passe, mais sa génération nécessite des étapes supplémentaires et les fichiers contenant la clé doivent être conservés dans un emplacement sécurisé. Si quelqu’un parvient à accéder aux fichiers de la clé, il peut alors accéder à votre compte. Par ailleurs, si vous perdez les fichiers de la clé, vous ne pouvez plus vous connecter à votre compte.

Une paire de clés se compose d’une clé publique (envoyée au serveur HDInsight) et d’une clé privée (conservée sur votre ordinateur client). Quand vous vous connectez au serveur HDInsight à l’aide de SSH, le client SSH utilise la clé privée contenue sur votre ordinateur pour s’authentifier auprès du serveur.

##Création d’une clé SSH

Utilisez les informations suivantes si vous envisagez d'utiliser les clés SSH avec votre cluster. Si vous prévoyez d’utiliser un mot de passe, vous pouvez ignorer cette section.

1. Ouvrez une session terminal et utilisez la commande suivante pour voir si vous avez des clés SSH existantes :

		ls -al ~/.ssh

	Recherchez les fichiers suivants dans la liste des répertoires. Ces noms sont ceux couramment utilisés pour les clés SSH publiques.

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. Si vous ne souhaitez pas utiliser un fichier existant ou si vous ne disposez pas de clés SSH existantes, utilisez les instructions suivantes pour générer un nouveau fichier :

		ssh-keygen -t rsa

	Les informations suivantes vous seront demandées :

	* l’emplacement du fichier : par défaut dans ~/.ssh/id\_rsa ;
	* une phrase secrète : il vous sera demander de la saisir à nouveau.

		> [AZURE.NOTE]Nous vous recommandons vivement d'utiliser une phrase secrète sûre pour cette clé. Toutefois, si vous oubliez cette phrase secrète, il sera impossible de la récupérer.

	Une fois la commande exécutée, vous obtenez deux nouveaux fichiers, la clé privée (par exemple, **id\_rsa**) et la clé publique (par exemple, **id\_rsa.pub**).

##Création d'un cluster HDInsight sous Linux

À la création d'un cluster HDInsight sous Linux, vous devez fournir la clé publique précédemment créée. Pour les clients Linux, Unix ou OS X, vous pouvez créer un cluster HDInsight de deux façons :

* **Portail Azure en version préliminaire** : utilise un portail web pour créer le cluster.

* **Interface de ligne de commande Azure pour Mac, Linux et Windows** : utilise des commandes de ligne de commande pour créer le cluster.

Chacune de ces méthodes nécessite un mot de passe ou une clé publique. Pour plus d’informations sur la création d’un cluster HDInsight sur Linux, voir [Approvisionnement de clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md).

###Portail Azure en version préliminaire

Quand vous utilisez le [portail Azure en version préliminaire][preview-portal] pour créer un cluster HDInsight sous Linux, vous devez saisir un **NOM D’UTILISATEUR SSH**, puis sélectionner cette option pour entrer un **MOT DE PASSE** ou une **CLÉ PUBLIQUE SSH**.

Si vous sélectionnez **CLÉ PUBLIQUE SSH**, vous pouvez soit coller la clé publique (contenue dans le fichier avec l’extension **.pub**) dans le champ __SSH PublicKey__, ou choisir __Sélectionner un fichier__ pour parcourir et sélectionner le fichier de clé publique.

![Image du formulaire de demande de clé publique](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE]Le fichier de clé est simplement un fichier texte. Le contenu doit être identique à ce qui suit :```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

L’utilisateur indiqué peut ainsi se connecter avec le mot de passe ou la clé publique que vous avez fournis.

###Interface de ligne de commande Azure pour Mac, Linux et Windows

Vous pouvez utiliser l’[interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat.md) afin de créer un cluster en utilisant la commande `azure hdinsight cluster create`.

Pour plus d’informations sur l’utilisation de cette commande, voir [Approvisionnement de clusters Hadoop sous Linux dans HDInsight à l'aide d'options personnalisées￼](hdinsight-hadoop-provision-linux-clusters.md).

##Connexion à un cluster HDInsight sous Linux

À partir d’une session terminal, utilisez la commande SSH pour vous connecter au nœud principal du cluster en saisissant le nom d’utilisateur et l’adresse du cluster :

* **Adresse SSH** : le nom du cluster suivi de **-ssh.azurehdinsight.net**. Par exemple **mycluster-ssh.azurehdinsight.net**

* **Nom d’utilisateur** : le nom d’utilisateur SSH que vous avez fourni en créant le cluster

L’exemple suivant permettra de se connecter au cluster **mycluster** en tant qu’utilisateur **me** :

	ssh me@mycluster-ssh.azurehdinsight.net

Si vous avez utilisé un mot de passe pour le compte d'utilisateur, il vous sera demandé d'entrer le mot de passe.

Si vous avez utilisé une clé SSH sécurisée avec une phrase secrète, vous devez entrer la phrase secrète. Dans le cas contraire, SSH tente une authentification automatique avec l’une des clés privées locales sur votre client.

> [AZURE.NOTE]Si SSH n’effectue pas l’authentification automatique avec la bonne clé privée, utilisez le paramètre **-i** et indiquez le chemin d’accès de la clé privée. L’exemple suivant charge la clé privée à partir de `~/.ssh/id_rsa` :
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

Si aucun port n'est spécifié, SSH sera par défaut connecté au port 22, qui se connecte à headnode0 sur le cluster HDInsight par défaut. Si vous utilisez le port 23, vous allez vous connecter au headnode1. Pour plus d’informations sur les nœuds principaux, voir [Disponibilité et fiabilité des clusters Hadoop dans HDInsight](hdinsight-high-availability-linux.md).

###Connexion à des nœuds de travail

Les nœuds de travail ne sont pas directement accessibles de l’extérieur du centre de données Azure, mais ils sont accessibles à partir du nœud principal du cluster via le protocole SSH.

Si vous utilisez une clé SSH pour authentifier votre compte d’utilisateur, vous devez effectuer les étapes suivantes sur votre client :

1. Avec un éditeur de texte, ouvrez `~/.ssh/config`. Si ce fichier n’existe pas, créez-le en entrant `touch ~/.ssh/config` dans le terminal.

2. Ajoutez le code suivant au fichier. Remplacez *CLUSTERNAME* par le nom de votre cluster HDInsight.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Le transfert de l’agent SSH est alors configuré pour votre cluster HDInsight.

3. Testez le transfert de l’agent SSH à l’aide de la commande suivante à partir du terminal :

        echo "$SSH_AUTH_SOCK"

    Cette commande renvoie des informations similaires à celles-ci :

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Si aucun élément n’est renvoyé, **ssh-agent** ne doit pas être en cours d’exécution. Pour connaître les étapes spécifiques d’installation et de configuration de **ssh-agent**, voir la documentation de votre système d’exploitation ou la page [Utilisation de ssh-agent avec ssh](http://mah.everybody.org/docs/ssh).

4. Après avoir vérifié que **ssh agent** est en cours d’exécution, utilisez la commande suivante pour ajouter votre clé privée SSH à l’agent :

        ssh-add ~/.ssh/id_rsa

    Si votre clé privée est stockée dans un fichier différent, remplacez `~/.ssh/id_rsa` par le chemin d’accès au fichier.

Utilisez les étapes suivantes pour vous connecter aux nœuds de travail de votre cluster.

> [AZURE.IMPORTANT]Si vous utilisez une clé SSH pour authentifier votre compte, vous devez effectuer les étapes précédentes pour vérifier que le transfert de l’agent fonctionne.

1. Connectez-vous au cluster HDInsight via SSH comme décrit précédemment.

2. Une fois connecté, utilisez les éléments suivants pour récupérer la liste des nœuds dans votre cluster. Remplacez *ADMINPASSWORD* par le mot de passe de votre compte d’administrateur de cluster. Remplacez *CLUSTERNAME* par le nom de votre cluster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Cette commande renvoie des informations au format JSON pour les nœuds du cluster, notamment `host_name`, qui contient le nom de domaine complet (FQDN) pour chaque nœud. Voici l’exemple d’une entrée `host_name` renvoyée par la commande **curl** :

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. Une fois que vous avez obtenu la liste des nœuds de travail auxquels vous souhaitez vous connecter, utilisez la commande suivante à partir de la session SSH sur le serveur pour ouvrir une connexion à un nœud de travail :

        ssh USERNAME@FQDN

    Remplacez *USERNAME* par votre nom d’utilisateur SSH et *FQDN* par le nom de domaine complet du nœud de travail. Par exemple : `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE]Si vous utilisez un mot de passe pour l’authentification de votre session SSH, vous devez entrer à nouveau le mot de passe. Si vous utilisez une clé SSH, la connexion doit se terminer sans invite de commandes.

4. Une fois la session établie, l’invite de commandes du terminal passe de `username@headnode` à `username@workernode` pour indiquer que vous êtes connecté au nœud de travail. Les commandes que vous exécutez à ce stade sont exécutées sur le nœud de travail.

4. Une fois les actions sur le nœud de travail terminées, utilisez la commande `exit` pour fermer la session sur le nœud de travail. Vous revenez à l’invite de commandes `username@headnode`.

##Ajout d’autres comptes

1. Générez de nouvelles clés publique et privée pour le nouveau compte d’utilisateur, comme décrit dans la section [Création d’une clé SSH](#create-an-ssh-key-optional).

	> [AZURE.NOTE]Vous devez générer la clé privée sur le client à partir duquel l’utilisateur se connectera au cluster ou vous devez la transférer de manière sécurisée vers ce client après sa création.

1. Depuis une session SSH vers le cluster, ajoutez le nouvel utilisateur avec la commande suivante :

		sudo adduser --disabled-password <username>

	Cela créera un nouveau compte d'utilisateur mais désactivera l'authentification par mot de passe.

2. Créez le répertoire et les fichiers d’enregistrement de la clé en utilisant les commandes suivantes :

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Lorsque l'éditeur nano s'ouvre, copiez et collez les contenus de la clé publique pour le nouveau compte d'utilisateur. Enfin, utilisez **Ctrl-X** pour enregistrer le fichier et quitter l’éditeur.

	![image de l’éditeur nano avec exemple de clé](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Utilisez la commande suivante pour modifier la propriété du dossier .ssh et son contenu vers le nouveau compte d’utilisateur :

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Vous devriez maintenant pouvoir vous authentifier au serveur avec le nouveau compte d'utilisateur et la clé privée.

##<a id="tunnel"></a>Tunneling SSH

Vous pouvez utiliser SSH pour transférer des requêtes locales, telles que des demandes web, vers le cluster HDInsight. La requête sera ensuite acheminée vers la ressource demandée comme si elle provenait du nœud principal du cluster HDInsight.

> [AZURE.IMPORTANT]Un tunnel SSH est requis pour accéder à l’interface utilisateur web de certains services Hadoop. Par exemple, l'interface utilisateur de l'historique des travaux ou de l'interface utilisateur du Gestionnaire de ressources est uniquement accessible à l'aide d'un tunnel SSH.

Pour plus d’informations sur la création et l’utilisation d’un tunnel SSH, voir [Utilisation de SSH Tunneling pour accéder à l’interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d’autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md).

##Étapes suivantes

Maintenant que vous savez comment vous authentifier avec une clé SSH, apprenez à utiliser MapReduce avec Hadoop dans HDInsight.

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)

* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)

* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

<!---HONumber=Sept15_HO3-->