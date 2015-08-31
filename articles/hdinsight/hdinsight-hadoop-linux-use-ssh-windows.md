<properties
   pageTitle="Utilisation de clés SSH avec Hadoop sur des clusters Linux à partir de Windows | Microsoft Azure"
	description="Apprenez à créer et utiliser des clés SSH pour l'authentification dans les clusters HDInsight sous Linux. Connectez des clusters à partir de clients Windows en utilisant le client SSH PuTTY."
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
	ms.date="07/24/2015"
	ms.author="larryfr"/>

#Utilisation de SSH avec Hadoop Linux dans HDInsight à partir de Windows (aperçu)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

Les clusters HDInsight Azure sous Linux permettent d’utiliser l’accès SSH (Secure Shell) via un mot de passe ou une clé SSH. Ce document fournit des informations relatives à la connexion à HDInsight depuis les clients Windows à l’aide du client SSH PuTTY.

> [AZURE.NOTE]Les étapes de cet article partent du principe que vous utilisez un client Windows. Si vous utilisez un client Linux, Unix ou OS X, consultez la rubrique [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

##Configuration requise

* **PuTTY** et **PuTTYGen** pour les clients Windows. Ces utilitaires sont disponibles à l’adresse suivante : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

* Un navigateur web moderne qui prend en charge HTML5.

OU

* [Interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli.md).

##Qu'est-ce que SSH ?

SSH est un utilitaire permettant de se connecter et d'exécuter à distance des commandes sur un serveur distant. Avec HDInsight sous Linux, SSH établit une connexion chiffrée avec le nœud principal du cluster et fournit une ligne de commande que vous utilisez pour taper les commandes. Les commandes sont ensuite exécutées directement sur le serveur.

##Création d'une clé SSH (facultatif)

En créant un cluster HDInsight sous Linux, vous pouvez utiliser un mot de passe ou une clé SSH pour l’authentification au serveur quand vous utilisez SSH. Les clés SSH sont considérées comme plus sûres puisqu'elles sont basées sur un certificat. Utilisez les informations suivantes si vous envisagez d'utiliser les clés SSH avec votre cluster.

1. Ouvrez PuTTYGen.

2. Pour le **Type de clé à générer**, sélectionnez **SSH-2 RSA**, puis cliquez sur **Générer**.

	![Interface de PuTTYGen](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Déplacez la souris dans la zone sous la barre de progression jusqu'à ce la barre soit remplie. Le déplacement de la souris génère des données aléatoires qui sont utilisées pour générer la clé.

	![déplacez la souris](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	Lorsque la clé est générée, la clé publique s'affiche.

4. Pour plus de sécurité, vous pouvez entrer une phrase secrète dans le champ **Phrase secrète de la clé**, puis taper la même valeur dans le champ **Confirmer la phrase secrète**.

	![phrase secrète](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

	> [AZURE.NOTE]Nous vous recommandons vivement d'utiliser une phrase secrète sûre pour cette clé. Toutefois, si vous oubliez cette phrase secrète, il sera impossible de la récupérer.

5. Cliquez sur **Enregistrer la clé privée** pour enregistrer la clé dans un fichier **.ppk**. Cela sera utilisé pour l'authentification à votre cluster HDInsight sous Linux.

	> [AZURE.NOTE]Stockez cette clé dans un emplacement sûr car elle peut être utilisée pour accéder à votre cluster HDInsight sous Linux.

6. Cliquez sur **Enregistrer la clé publique** pour enregistrer la clé en fichier **.txt**. Cela vous permet de réutiliser la clé publique par la suite lorsque vous créez des clusters supplémentaires HDInsight sous Linux.

	> [AZURE.NOTE]La clé publique est également affiché en haut de PuTTYGen. Vous pouvez cliquer avec le bouton droit dans ce champ, copier la valeur, puis la coller dans un formulaire au moment de la création d'un cluster dans la version préliminaire du portail Azure.

##Création d'un cluster HDInsight sous Linux

À la création d'un cluster HDInsight sous Linux, vous devez fournir la clé publique précédemment créée. À partir des clients Windows, il existe deux façons de créer un cluster HDInsight sous Linux :

* **Version préliminaire du portail Azure** : utilise un portail web pour créer le cluster.

* **Interface de ligne de commande Azure pour Mac, Linux et Windows** : utilise des commandes de ligne de commande pour créer le cluster.

Chacune de ces méthodes nécessite une clé publique. Pour plus d’informations sur la création d’un cluster HDInsight sur Linux, consultez la rubrique [Approvisionnement de clusters HDInsight sur Linux](hdinsight-hadoop-provision-linux-clusters.md).

###Portail Azure en version préliminaire

Quand vous utilisez la [version préliminaire du portail Azure][preview-portal] pour créer un cluster HDInsight sous Linux, vous devez saisir un **nom d'utilisateur SSH**, puis sélectionner cette option pour entrer un **MOT DE PASSE** ou une **CLÉ PUBLIQUE SSH**.

Si vous sélectionnez **Clé publique SSH**, vous pouvez soit coller la clé publique (affichée dans le champ de __clé publique pour le coller dans le fichier OpenSSH authorized\_keys__ dans PuttyGen) dans le champ __SSH PublicKey__ champ ou sélectionnez __Sélectionner un fichier__ pour parcourir et sélectionner le fichier qui contient la clé publique.

![Image du formulaire de demande de clé publique](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Cela crée une connexion pour l'utilisateur indiqué et active l'authentification par mot de passe ou l'authentification par clé SSH.

###Interface de ligne de commande Azure pour Mac, Linux et Windows

Vous pouvez utiliser l’[interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli.md) afin de créer un cluster en utilisant la commande `azure hdinsight cluster create`.

Pour plus d'informations sur l'utilisation de cette commande, consultez la rubrique [Configuration de clusters Hadoop sur Linux dans HDInsight à l'aide d'options personnalisées￼](hdinsight-hadoop-provision-linux-clusters.md).

##Connexion à un cluster HDInsight sous Linux

1. Ouvrez PuTTY.

	![interface de putty](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. Si vous avez fourni une clé SSH lors de la création de votre compte d’utilisateur, vous devez effectuer l’étape suivante pour sélectionner la clé privée à utiliser lors de l’authentification au cluster :

	Dans **Catégorie**, développez **Connexion**, puis **SSH** et sélectionnez **Auth**. Enfin, cliquez sur **Parcourir**, puis sélectionnez le fichier .ppk qui contient votre clé privée.

	![interface de putty, sélectionnez une clé privée](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. Dans **Catégorie**, cliquez sur **Session**. À partir de l’écran **Options de base pour votre session PuTTY**, entrez l’adresse SSH de votre serveur HDInsight dans le champ **Nom d’hôte (ou adresse IP)**. L’adresse SSH est le nom de votre cluster, elle est ensuite suivie de **-ssh.azurehdinsight.net**. Par exemple : **moncluster-ssh.azurehdinsight.net**.

	![interface de putty avec adresse ssh saisie](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. Pour enregistrer les identifiants de connexion en vue d’une utilisation future, entrez un nom pour cette connexion sous **Sessions enregistrées**, puis cliquez sur **Enregistrer**. La connexion sera ajoutée à la liste des sessions enregistrées.

5. Cliquez sur **Ouvrir** pour vous connecter au cluster.

	> [AZURE.NOTE]Si c'est la première fois que vous vous connectez au cluster, vous allez recevoir une alerte de sécurité. C’est normal. Sélectionnez **Oui** pour mettre en cache la clé du serveur RSA2 et continuer.

6. Lorsque vous y êtes invité, entrez le nom d'utilisateur que vous avez entré lors la création du cluster. Si vous avez donné un mot de passe pour le compte d'utilisateur, il vous sera demandé d'entrer le mot de passe.

> [AZURE.NOTE]Les étapes ci-dessus supposent que vous utilisez le port 22, qui se connecte à headnode0 sur le cluster HDInsight. Si vous utilisez le port 23, vous allez vous connecter au headnode1. Consultez la rubrique [Disponibilité et fiabilité des clusters Hadoop dans HDInsight](hdinsight-high-availability-linux.md) pour plus d'informations sur les noeuds principaux.

###Connexion à des nœuds de travail

Les nœuds de travail ne sont pas directement accessibles de l’extérieur du centre de données Azure, mais ils sont accessibles à partir du nœud principal du cluster via le protocole SSH.

Si vous avez fourni une clé SSH lors de la création de votre compte d’utilisateur, vous devez effectuer les étapes suivantes pour utiliser la clé privée lors de l’authentification au cluster si vous souhaitez vous connecter aux nœuds de travail.

1. Installez Pageant à partir de l'adresse suivante : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). Cet utilitaire permet de mettre en cache des clés SSH pour PuTTY.

2. Exécutez Pageant. Il est réduit à une icône dans la barre d’état. Cliquez avec le bouton droit sur l’icône et sélectionnez **Ajouter une clé**.

    ![ajout d’une clé](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. Lorsque la boîte de dialogue s’affiche, sélectionnez le fichier .ppk qui contient la clé, puis cliquez sur **Ouvrir**. La clé est ainsi ajoutée à Pageant, qui la fournira à PuTTY lors de la connexion au cluster.

    > [AZURE.IMPORTANT]Si vous avez utilisé une clé SSH pour sécuriser votre compte, vous devez effectuer les étapes précédentes avant de pouvoir vous connecter aux nœuds de travail.

4. Ouvrez PuTTY.

5. Si vous utilisez une clé SSH pour vous authentifier, dans la section **Catégorie**, développez **Connexion**, puis **SSH** et sélectionnez **Auth**.

    Dans la section **Paramètres d’authentification**, activez le paramètre **Autoriser le transfert de l’agent**. Vous autorisez ainsi PuTTY à transmettre automatiquement l’authentification par certificat via la connexion au nœud principal du cluster, lors de la connexion aux nœuds de travail.

    ![autoriser le transfert de l’agent](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. Connectez-vous au cluster, comme décrit précédemment. Si vous utilisez une clé SSH pour vous authentifier, vous n’avez pas besoin de la sélectionner : la clé SSH ajoutée à Pageant est utilisée pour s’authentifier auprès du cluster.

7. Une fois la connexion établie, utilisez les éléments suivants pour récupérer une liste des nœuds dans votre cluster. Remplacez *ADMINPASSWORD* par le mot de passe de votre compte d’administrateur de cluster. Remplacez *CLUSTERNAME* par le nom de votre cluster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Cette commande renvoie des informations au format JSON pour les nœuds du cluster, notamment `host_name`, qui contient le nom de domaine complet (FQDN) pour chaque nœud. Voici l’exemple d’une entrée `host_name` renvoyée par la commande **curl** :

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. Une fois que vous avez obtenu la liste des nœuds de travail auxquels vous souhaitez vous connecter, utilisez la commande suivante à partir de la session PuTTY pour ouvrir une connexion à un nœud de travail :

        ssh USERNAME@FQDN

    Remplacez *USERNAME* par votre nom d’utilisateur SSH et *FQDN* par le nom de domaine complet du nœud de travail. Par exemple : `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE]Si vous utilisez un mot de passe pour l’authentification de votre session SSH, vous devez entrer à nouveau le mot de passe. Si vous utilisez une clé SSH, la connexion doit se terminer sans invite de commandes.

9. Une fois la session établie, l’invite de commandes de votre session PuTTY passe de `username@headnode` à `username@workernode` pour indiquer que vous êtes connecté au nœud de travail. Les commandes que vous exécutez à ce stade sont exécutées sur le nœud de travail.

10. Une fois les actions sur le nœud de travail terminées, utilisez la commande `exit` pour fermer la session sur le nœud de travail. Vous revenez à l’invite de commandes `username@headnode`.

##Ajout d’autres comptes

Si vous devez ajouter des comptes à votre cluster, procédez comme suit :

1. Générez une nouvelle clé publique et une clé privée pour le nouveau compte d'utilisateur comme décrit précédemment.

2. Depuis une session SSH vers le cluster, ajoutez le nouvel utilisateur avec la commande suivante :

		sudo adduser --disabled-password <username>

	Cela créera un nouveau compte d'utilisateur mais désactivera l'authentification par mot de passe.

3. Créez le répertoire et les fichiers d’enregistrement de la clé en utilisant les commandes suivantes :

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. Lorsque l'éditeur nano s'ouvre, copiez et collez les contenus de la clé publique pour le nouveau compte d'utilisateur. Enfin, utilisez **Ctrl-X** pour enregistrer le fichier et quitter l’éditeur.

	![image de l’éditeur nano avec exemple de clé](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. Utilisez la commande suivante pour modifier la propriété du dossier .ssh et son contenu vers le nouveau compte d’utilisateur :

		sudo chown -hR <username>:<username> /home/<username>/.ssh

6. Vous devriez maintenant pouvoir vous authentifier au serveur avec le nouveau compte d'utilisateur et la clé privée.

##<a id="tunnel"></a>Tunneling SSH

Vous pouvez utiliser SSH pour transférer des requêtes locales, telles que des demandes web, vers le cluster HDInsight. La requête sera ensuite acheminée vers la ressource demandée comme si elle provenait du nœud principal du cluster HDInsight.

> [AZURE.IMPORTANT]Un tunnel SSH est requis pour accéder à l'interface utilisateur web correspondant aux services Hadoop sopme. Par exemple, l'interface utilisateur de l'historique des travaux ou de l'interface utilisateur du Gestionnaire de ressources est uniquement accessible à l'aide d'un tunnel SSH.

Suivez les étapes suivantes pour créer un tunnel SSH et configurer son utilisation par votre navigateur afin de vous connecter au cluster :

1. Ouvrez PuTTY, puis entrez vos identifiants de connexion, comme indiqué précédemment dans la section [Connexion à un cluster Linux](#connect-to-a-linux-based-hdinsight-cluster).

2. Dans la rubrique **Catégorie** située à gauche dans la boîte de dialogue, développez **Connexion** et **SSH**, puis sélectionnez **Tunnels**.

3. Indiquez les informations suivantes dans le formulaire des **Options de contrôle de transfert du port SSH**.

	* **Port source** : le port sur le client que vous souhaitez transférer. Par exemple : **9876**.

	* **Destination** : l’adresse SSH pour le cluster HDInsight sous Linux. Par exemple : **moncluster-ssh.azurehdinsight.net**.

	* **Dynamique** : active le routage dynamique du proxy SOCKS.

	![image des options de tunneling](./media/hdinsight-hadoop-linux-use-ssh-windows/puttytunnel.png)

4. Cliquez sur **Ajouter** pour ajouter les paramètres, puis cliquez sur **Ouvrir** pour ouvrir une connexion SSH.

5. Lorsque vous y êtes invité, connectez-vous au serveur. Cela permettra d'établir une session SSH et d'activer le tunnel.

6. Configurez le programme client, tel que Firefox, pour utiliser **localhost:9876** comme proxy **SOCKS v5**. Voici comment se présentent les paramètres Firefox :

	![image des paramètres de Firefox](./media/hdinsight-hadoop-linux-use-ssh-windows/socks.png)

	> [AZURE.NOTE]Sélectionner **DNS Distant** permet de résoudre les requêtes DNS à l’aide du cluster HDInsight. Si cette option n’est pas sélectionnée, le DNS est résolu localement.

	Vous pouvez vérifier que le trafic est routé via le tunnel en accédant à un site, tel que [http://www.whatismyip.com/](http://www.whatismyip.com/), avec les paramètres de proxy activés et désactivés dans Firefox. Lorsqu’ils sont activés, l’adresse IP sera celle d’une machine dans le centre de données Microsoft Azure.

###Extensions du navigateur

Si la configuration du navigateur pour utiliser le tunnel fonctionne, en principe vous n'avez pas besoin d'acheminer tout le trafic via le tunnel. Les extensions de navigateur telles que [FoxyProxy](http://getfoxyproxy.org/) prennent en charge la correspondance des requêtes URL (FoxyProxy Standard ou Plus uniquement) pour que seules les requêtes d'URL spécifiques soient envoyées via le tunnel.

Si vous avez installé FoxyProxy Standard, suivez les étapes suivantes pour configurer celui-ci uniquement pour le trafic de transfert de HDInsight via le tunnel.

1. Ouvrez l'extension FoxyProxy dans votre navigateur. Par exemple, dans Firefox, sélectionnez l'icône FireProxy à côté du champ d'adresse.

	![icône foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxy.png)

2. Sélectionnez **Ajouter un nouveau proxy**, puis l’onglet **Général** et entrez un nom de proxy pour **HDInsightProxy**.

	![onglet général foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxygeneral.png)

3. Sélectionnez l’onglet **Informations du proxy** et remplissez les champs suivants :

	* **Hôte ou adresse IP** : il s’agit de localhost, puisque nous utilisons un tunnel SSH sur la machine locale.

	* **Port** : port que vous avez utilisé pour le tunnel SSH

	* **Proxy SOCKS** : sélectionnez celui-ci pour autoriser le navigateur à utiliser le tunnel comme proxy.

	* **SOCKS v5** : sélectionnez celui-ci pour définir la version requise pour le proxy.

	![proxy foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxyproxy.png)

4. Sélectionnez l’onglet **Type d’URL**, puis **Ajouter un nouveau type**. Utilisez ce qui suit pour définir le type, puis cliquez sur **OK** :

	* **Nom de type** - **nœud principal** : il s’agit simplement d’un nom de type convivial.

	* **Modèle d’URL** - ***nœud principal*** : cela définit un type qui correspond à toute URL contenant les mots **nœud principal**.

	![type foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxypattern.png)

4. Cliquez sur **OK** pour ajouter le proxy et fermer les **Paramètres de proxy**.

5. Dans la partie supérieure de la boîte de dialogue FoxyProxy, changez le **Mode de sélection** pour **Utiliser les proxys en fonction de leurs types et priorités prédéfinis**, puis cliquez sur **Fermer**.

	![mode de sélection foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-windows/selectmode.png)

Après avoir suivi ces étapes, seules les requêtes pour les URL qui contiennent la chaîne **nœud principal** seront acheminées via le tunnel SSL.

##Étapes suivantes

Maintenant que vous savez comment vous authentifier avec une clé SSH, apprenez à utiliser MapReduce avec Hadoop dans HDInsight.

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)

* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)

* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

<!---HONumber=August15_HO8-->