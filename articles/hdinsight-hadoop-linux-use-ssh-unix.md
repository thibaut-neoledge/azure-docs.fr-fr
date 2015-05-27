<properties
   pageTitle="Utilisez les clés SSH avec Hadoop de HDInsight sous Linux à partir de Linux, Unix ou OS X | Azure"
   description="Apprenez à créer et utiliser des clés SSH pour l'authentification dans les clusters HDInsight sous Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/20/2015"
   ms.author="larryfr"/>

#Utilisation de SSH avec Hadoop dans HDInsight sous Linux à partir de Linux, Unix ou OS X (version préliminaire)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

Les clusters HDInsight Azure sous Linux permettent d’utiliser l’accès SSH (Secure Shell) via un mot de passe ou une clé SSH. Ce document donne des informations sur l'utilisation de SSH avec HDInsight à partir des clients Linux, Unix ou OS X.

> [AZURE.NOTE]Les étapes de cet article partent du principe que vous utilisez un client Linux, Unix ou OS X. Bien que ces étapes puissent être effectuées sur un client Windows si vous avez installé un package qui fournit `ssh`et `ssh-keygen` (tel que Git pour Windows,) nous recommandons aux clients Windows de suivre les étapes décrites dans [Utilisation de SSH avec HDInsight (Hadoop) sous Linux à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##Configuration requise

* **ssh-keygen** et **ssh** pour les clients Linux, Unix, et OS X. Ces utilitaires sont généralement fournis avec votre système d’exploitation ou disponibles dans le système de gestion du package.

* Un navigateur web moderne qui prend en charge HTML5.

OU

* [Interface de ligne de commande Azure pour Mac, Linux et Windows](xplat-cli.md).

##Qu'est-ce que SSH ?

SSH est un utilitaire permettant de se connecter et d'exécuter à distance des commandes sur un serveur distant. Avec HDInsight sous Linux, SSH établit une connexion chiffrée avec le nœud principal du cluster et fournit une ligne de commande que vous utilisez pour taper les commandes. Les commandes sont ensuite exécutées directement sur le serveur.

##Création d'une clé SSH (facultatif)

En créant un cluster HDInsight sous Linux, vous pouvez utiliser un mot de passe ou une clé SSH pour l’authentification au serveur quand vous utilisez SSH. Les clés SSH sont considérées comme plus sûres puisqu'elles sont basées sur un certificat. Utilisez les informations suivantes si vous envisagez d'utiliser les clés SSH avec votre cluster.

1. Ouvrez une session terminal et utilisez la commande suivante pour voir si vous avez des clés SSH existantes :

		ls -al ~/.ssh

	Recherchez les fichiers suivants dans la liste des répertoires. Ces noms sont ceux couramment utilisés pour les clés SSH publiques.

	* id_dsa.pub
	* id_ecdsa.pub
	* id_ed25519.pub
	* id_rsa.pub

2. Si vous ne souhaitez pas utiliser un fichier existant ou si vous ne disposez pas de clés SSH existantes, utilisez les instructions suivantes pour générer un nouveau fichier :

		ssh-keygen -t rsa

	Les informations suivantes vous seront demandées :

	* l’emplacement du fichier : par défaut dans \~/.ssh/id_rsa ;
	* une phrase secrète : il vous sera demander de la saisir à nouveau.

		> [AZURE.NOTE]Nous vous recommandons vivement d'utiliser une phrase secrète sûre pour cette clé. Toutefois, si vous oubliez cette phrase secrète, il sera impossible de la récupérer.

	Une fois la commande exécutée, vous obtenez deux nouveaux fichiers, la clé privée (par exemple, **id_rsa**) et la clé publique (par exemple, **id_rsa.pub**).

##Création d'un cluster HDInsight sous Linux

À la création d'un cluster HDInsight sous Linux, vous devez fournir la clé publique précédemment créée. Pour les clients Linux, Unix ou OS X, vous pouvez créer un cluster HDInsight de deux façons :

* **Portail Azure** : utilise un portail web pour créer le cluster.

* **Interface de ligne de commande Azure pour Mac, Linux et Windows** : utilise des commandes de ligne de commande pour créer le cluster.

Chacune de ces méthodes nécessite un mot de passe ou une clé publique. Pour plus d’informations sur la création d’un cluster HDInsight sous Linux, consultez la rubrique <a href="/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Approvisionnement de clusters HDInsight sous Linux</a>.

###Portail Azure

Quand vous utilisez le portail pour créer un cluster HDInsight sous Linux, vous devez entrer un **NOM D’UTILISATEUR SSH**, puis sélectionner cette option pour entrer un **MOT DE PASSE** ou une **CLÉ PUBLIQUE SSH**. Si vous sélectionnez une **CLÉ PUBLIQUE SSH**, vous devez coller la clé publique (contenue dans le fichier avec l’extension **.pub**) dans le formulaire suivant :

![Image du formulaire de demande de clé publique](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE]Le fichier de clé est simplement un fichier texte. Le contenu doit être identique à ce qui suit :```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

L’utilisateur indiqué peut ainsi se connecter avec le mot de passe ou la clé publique que vous avez fournis.

###Interface de ligne de commande Azure pour Mac, Linux et Windows

Vous pouvez utiliser l’[interface de ligne de commande Azure pour Mac, Linux et Windows](xplat.md) afin de créer un cluster en utilisant la commande `azure hdinsight cluster create`.

Pour plus d’informations sur l’utilisation de cette commande, consultez la rubrique <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">Approvisionnement de clusters Hadoop sous Linux dans HDInsight à l’aide d’options personnalisées</a>.

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

    Si aucun élément n’est renvoyé, **ssh-agent** ne doit pas être en cours d’exécution. Consultez la documentation de votre système d’exploitation pour connaître les étapes spécifiques d’installation et de configuration de **ssh-agent**, ou consultez la page <a href="http://mah.everybody.org/docs/ssh" target="_blank">Utilisation de ssh-agent avec ssh</a>.

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

Vous pouvez aussi utiliser SSH pour transférer des requêtes locales, telles que des demandes web, vers le cluster HDInsight. La requête sera ensuite acheminée vers la ressource demandée comme si elle provenait du nœud principal du cluster HDInsight.

Vous avez ainsi facilement accès aux services web sur le cluster HDInsight qui utilisent des noms de domaine internes pour le nœud principal ou le nœud de travail dans le cluster. Par exemple, certaines sections de la page web Ambari utilisent des noms de domaine internes, tels que **headnode0.mycluster.d1.internal.cloudapp.net**. Ces noms ne peuvent pas être résolus en dehors du cluster. Toutefois, les requêtes tunnelées par SSH proviennent de l’intérieur du cluster et seront correctement résolues.

Suivez les étapes suivantes pour créer un tunnel SSH et configurer votre navigateur pour vous connecter au cluster.

1. Vous pouvez utiliser la commande suivante pour créer un tunnel SSH vers le nœud principal du cluster :

		ssh -C2qTnNf -D 9876 username@clustername-ssh.azurehdinsight.net

	Cette commande va permettre de créer une connexion qui achemine le trafic vers le port local 9876 du cluster via SSH. Les options sont :

	* **D 8080** : port local qui acheminera le trafic via le tunnel

	* **C** : compresse toutes les données car le trafic web est principalement du texte

	* **2** : force le SSH à essayer le protocole version 2 uniquement

	* **q** : mode silencieux

	* **T** : désactive l’allocation pseudo-tty puisque nous transférons simplement un port

	* **n** : empêche la lecture STDIN puisque nous transférons simplement un port

	* **N** : n’exécute pas une commande à distance puisque nous transférons simplement un port

	* **f** : s’exécute à l’arrière-plan

	Si vous avez configuré le cluster avec une clé SSH, il vous faudra peut-être utiliser le paramètre `-i` et indiquer le chemin d’accès vers la clé SSH privée.

	Une fois la commande terminée, le trafic envoyé au port 9876 sur l’ordinateur local sera acheminé via SSL au nœud principal du cluster et semblera provenir de celui-ci.

2. Configurez le programme client, tel que Firefox, pour utiliser **localhost:9876** comme proxy **SOCKS v5**. Voici comment se présentent les paramètres Firefox :

	![image des paramètres de Firefox](./media/hdinsight-hadoop-linux-use-ssh-unix/socks.png)

	> [AZURE.NOTE]La sélection de **DNS Distant** résout les requêtes DNS à l’aide du cluster HDInsight. Si cette option n’est pas sélectionnée, le DNS est résolu localement.

	Vous pouvez vérifier que le trafic est routé via le tunnel en accédant à un site, tel que <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a>, avec les paramètres de proxy activés et désactivés dans Firefox. Quand ils sont activés, l’adresse IP est celle d’une machine du centre de données Microsoft Azure.

###Extensions du navigateur

Si la configuration du navigateur pour utiliser le tunnel fonctionne, en principe vous n'avez pas besoin d'acheminer tout le trafic via le tunnel. Les extensions de navigateur telles que <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a> prennent en charge la correspondance des requêtes URL (FoxyProxy Standard ou Plus uniquement) pour que seules les requêtes d’URL spécifiques soient envoyées via le tunnel.

Si vous avez installé FoxyProxy Standard, suivez les étapes suivantes pour configurer celui-ci uniquement pour le trafic de transfert de HDInsight via le tunnel.

1. Ouvrez l'extension FoxyProxy dans votre navigateur. Par exemple, dans Firefox, sélectionnez l'icône FireProxy à côté du champ d'adresse.

	![icône foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxy.png)

2. Sélectionnez **Ajouter un nouveau proxy**, puis l’onglet **Général** et entrez un nom de proxy pour **HDInsightProxy**.

	![onglet général foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxygeneral.png)

3. Sélectionnez l’onglet **Informations du proxy** et remplissez les champs suivants :

	* **Hôte ou adresse IP** : il s’agit de localhost, puisque nous utilisons un tunnel SSH sur la machine locale.

	* **Port** : port que vous avez utilisé pour le tunnel SSH

	* **Proxy SOCKS** : sélectionnez celui-ci pour autoriser le navigateur à utiliser le tunnel comme proxy.

	* **SOCKS v5** : sélectionnez celui-ci pour définir la version requise pour le proxy.

	![proxy foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxyproxy.png)

4. Sélectionnez l’onglet **Type d’URL**, puis **Ajouter un nouveau type**. Utilisez ce qui suit pour définir le type, puis cliquez sur **OK** :

	* **Nom de type** - **nœud principal** : il s’agit simplement d’un nom de type convivial.

	* **Modèle d’URL** - ***nœud principal*** : cela définit un type qui correspond à toute URL contenant les mots **nœud principal**.

	![type foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxypattern.png)

4. Cliquez sur **OK** pour ajouter le proxy et fermer les **Paramètres de proxy**.

5. Dans la partie supérieure de la boîte de dialogue FoxyProxy, changez le **Mode de sélection** pour **Utiliser les proxys en fonction de leurs types et priorités prédéfinis**, puis cliquez sur **Fermer**.

	![mode de sélection foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-unix/selectmode.png)

Après avoir suivi ces étapes, seules les requêtes pour les URL qui contiennent la chaîne **nœud principal** seront acheminées via le tunnel SSL.

##Étapes suivantes

Maintenant que vous savez comment vous authentifier avec une clé SSH, apprenez à utiliser MapReduce avec Hadoop dans HDInsight.

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)

* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)

* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=54-->