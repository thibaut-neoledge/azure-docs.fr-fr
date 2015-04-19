<properties
   pageTitle="Utilisation des clés SSH avec Hadoop de HDInsight sous Linux à partir de Linux, Unix ou OS X | Azure"
   description="Apprenez à créer et utiliser des clés SSH pour l'authentification dans les clusters HDInsight sous Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

##Utilisation de SSH avec Hadoop de HDInsight sous Linux à partir de Linux, Unix ou OS X (aperçu)

Les clusters HDInsight sous Linux offrent la possibilité de sécuriser l'accès SSH au moyen d'un mot de passe ou d'une clé SSH. Ce document donne des informations sur l'utilisation de SSH avec HDInsight à partir des clients Linux, Unix ou OS X.

> [AZURE.NOTE] Les étapes de cet article partent du principe que vous utilisez un client Linux, Unix ou OS X. Bien que ces étapes puissent être effectuées sur un client Windows si vous avez installé un package qui fournit  `ssh` et  `ssh-keygen` (tel que Git pour Windows,) nous recommandons aux clients Windows de suivre les étapes décrites dans [Utilisation de SSH avec HDInsight (Hadoop) sous Linux à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##Conditions préalables

* **ssh-keygen** et **ssh** pour les clients Linux, Unix, et OS X. Cet utilitaire est généralement fourni avec votre système d'exploitation ou disponible dans le système de gestion du package.

* Un navigateur Web moderne qui prend en charge HTML5

OU

* Les outils en ligne de commande multiplateforme Azure

##Qu'est-ce que SSH ?

SSH est un utilitaire permettant de se connecter et d'exécuter à distance des commandes sur un serveur distant. Avec HDInsight sous Linux, SSH établit une connexion sécurisée au nœud principal du cluster et fournit une ligne de commande que vous utilisez pour taper les commandes. Les commandes sont ensuite exécutées directement sur le serveur.

##Création d'une clé SSH (facultatif)

À la création d'un cluster HDInsight sous Linux, vous avez la possibilité d'utiliser un mot de passe ou une clé SSH pour l'authentification au serveur lorsque vous utilisez SSH. Les clés SSH sont considérées comme plus sûres puisqu'elles sont basées sur un certificat. Utilisez les informations suivantes si vous envisagez d'utiliser les clés SSH avec votre cluster.

1. Ouvrez une session de terminal et utilisez la commande suivante pour vérifier si vous disposez de clés SSH existantes.

		ls -al ~/.ssh

	Recherchez les fichiers suivants dans la liste des répertoires. Ces noms sont ceux couramment utilisés pour les clés SSH publiques.

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. Si vous ne souhaitez utiliser un fichier existant ou si vous ne disposez pas de clés SSH existantes, suivez les instructions suivantes pour générer un nouveau fichier.

		ssh-keygen -t rsa

	Les informations suivantes vous seront demandées :

	* L'emplacement du fichier : par défaut dans ~/.ssh/id\_rsa.
	* Une phrase secrète : il vous sera demander de la saisir à nouveau.
	
		> [AZURE.NOTE] Nous vous recommandons vivement d'utiliser une phrase secrète sûre pour cette clé. Toutefois, si vous oubliez cette phrase secrète, il sera impossible de la récupérer.

	Une fois la commande terminée, vous aurez deux nouveaux fichiers, la clé privée (par exemple **id\_rsa**,) et la clé publique (par exemple **id\_rsa.pub**.)

##Création d'un cluster HDInsight sous Linux

À la création d'un cluster HDInsight sous Linux, vous devez fournir la **clé publique** précédemment créée. Pour les clients Linux, Unix ou OS X, vous pouvez créer un cluster HDInsight de deux façons :

* **Portail de gestion Azure** : utilise un portail Web pour créer le cluster.

* **Interface de ligne de commande multiplateforme Azure (xplat-cli)** : utilise les commandes de la ligne de commande pour créer le cluster

Chacune de ces méthodes nécessite un **mot de passe** ou une **clé publique**. Pour plus d'informations sur la création d'un cluster HDInsight sous Linux, consultez <a href="/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Approvisionner des clusters HDInsight sous Linux</a>.

###Portail de gestion Azure

En utilisant le portail pour créer un cluster HDInsight sous Linux, vous devez entrer un **nom d'utilisateur SSH** puis sélectionner pour entrer un **mot de passe** ou une **clé publique SSH**. Si vous sélectionnez une **clé publique SSH**, vous devez coller la clé publique (contenue dans le fichier avec l'extension **.pub**) dans le formulaire suivant.

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] Le fichier de clé est simplement un fichier texte. Le contenu doit être identique à ce qui suit.
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Cela créé, pour l'utilisateur indiqué, une connexion au moyen du mot de passe ou de la clé publique que vous avez fournie.

###Interface de ligne de commande multiplateforme Azure

Vous pouvez utiliser l' <a href="../xplat-cli/" target="_brad">Interface de ligne de commande multiplateforme Azure</a> pour créer un cluster avec la commande  `azure hdinsight cluster create`.

Pour plus d'informations sur l'utilisation de cette commande, consultez <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">Approvisionnement de clusters Hadoop Linux dans HDInsight à l'aide d'options personnalisées</a>

##Connexion à un cluster HDInsight sous Linux

À partir d'une session de terminal, utilisez la commande SSH pour vous connecter au cluster en saisissant le nom d'utilisateur et l'adresse du cluster.

* **Adresse SSH** : le nom du cluster suivi de **-ssh.azurehdinsight.net**. Par exemple, **mycluster-ssh.azurehdinsight.net**.

* **Nom d'utilisateur** : le nom d'utilisateur SSH que vous avez fourni lors de la création du cluster.

L'exemple suivant permettra de se connecter au cluster **mycluster** en tant qu'utilisateur **me**.

	ssh me@mycluster-ssh.azurehdinsight.net

Si vous avez utilisé un **mot de passe** pour le compte d'utilisateur, il vous sera demandé d'entrer le mot de passe.

Si vous avez utilisé une **clé SSH** sécurisée par une phrase secrète, il vous sera demandé d'entrer cette phrase secrète. Dans le cas contraire, SSH essaiera une authentification automatique au moyen d'une des clés privées locales sur votre client.

> [AZURE.NOTE] Si SSH ne s'effectue pas l'authentification automatiquement avec la bonne **clé privée**, utilisez le paramètre **-i** et indiquez le chemin d'accès de la clé privée. L'exemple suivant charge la **clé privée** à partir de `~/.ssh/id_rsa`.
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

##Ajout de comptes supplémentaires

2. Générez une nouvelle **clé publique** et **clé privée** pour le nouveau compte d'utilisateur comme décrit dans la rubrique [Création d'une clé SSH](#create) .

	> [AZURE.NOTE] La clé privée doit être générée sur un client que l'utilisateur utilisera pour se connecter au cluster ou elle doit être transférée en toute sécurité vers ce client après la création.

1. Depuis une session SSH vers le cluster, ajoutez le nouvel utilisateur avec la ligne de commande suivante :

		sudo adduser --disabled-password <username> 

	Cela créera un nouveau compte d'utilisateur mais désactivera l'authentification par mot de passe.

2. Créez le répertoire et les fichiers pour conserver la clé en utilisant les commandes suivantes :

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Lorsque l'éditeur nano s'ouvre, copiez et collez les contenus de la **clé publique** pour le nouveau compte d'utilisateur. Enfin, utilisez **Ctrl-X** pour enregistrer le fichier et quitter l'éditeur.

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Utilisez la commande suivante pour modifier la propriété du dossier .ssh et le contenu pour le nouveau compte d'utilisateur.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Vous devriez maintenant pouvoir vous authentifier au serveur avec le nouveau compte d'utilisateur et la **clé privée**.

##<a id="tunnel"></a>Tunneling SSH

Vous pouvez aussi utiliser SSH pour transférer des requêtes locales, telles que des demandes Web, vers le cluster HDInsight. La requête sera ensuite acheminée vers la ressource demandée comme si elle provenait du nœud principal du cluster HDInsight.

Cela peut être très utile pour accéder aux services Web sur le cluster HDInsight qui utilise des noms de domaine internes pour le nœud principal ou le nœud de travail dans le cluster. Par exemple, certaines rubriques de la page Web Ambari utilisent des noms de domaine internes tels que **headnode0.mycluster.d1.internal.cloudapp.net**. Ces noms ne peuvent pas être résolus en dehors du cluster. Toutefois, les requêtes tunnelées par SSH proviennent de l'intérieur du cluster et seront résolues correctement.

Suivez les étapes suivantes pour créer un tunnel SSH et configurer votre navigateur pour vous connecter au cluster.

1. La commande suivante peut être utilisée pour créer un tunnel SSH vers le nœud principal du cluster.

		ssh -C2qTnNf -D 9876 username@clustername-ssh.azurehdinsight.net

	Cette commande va permettre de créer une connexion qui achemine le trafic vers le port local 9876 du cluster via SSH. Les options sont :

	* **D 8080** : le port local qui acheminera le trafic via le tunnel

	* **C** : compresse toutes les données car le trafic Web se constitue principalement de texte.

	* **2** : force le SSH à essayer le protocole version 2 uniquement

	* **q** : mode silencieux

	* **T** : désactive l'allocation pseudo-tty puisque nous transférons simplement un port
	
	* **n** : empêche la lecture STDIN puisque nous transférons simplement un port

	* **N** : n'exécute pas une commande à distance puisque nous transférons simplement un port

	* **f** : s'exécute à l'arrière-plan

	Si vous avez configuré le cluster avec une **clé SSH**, il vous faudra peut-être utiliser le paramètre `-i` et indiquer le chemin d'accès vers la clé SSH privée.

	Une fois la commande terminée, le trafic envoyé au port 9876 sur l'ordinateur local sera acheminé via SSL au nœud principal du cluster et semblera provenir de celui-ci.

2. Configurez le programme client, tel que Firefox, pour utiliser **localhost:9876** comme proxy **SOCKS v5**. Voici comment se présentent les paramètres Firefox :

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-unix/socks.png)

	> [AZURE.NOTE] La sélection de **DNS Distant** résoudra les requêtes DNS à l'aide du cluster HDInsight. Si cette option n'est pas sélectionnée, le DNS sera résolu localement.

	Vous pouvez vérifier que le trafic est routé via le tunnel par accédant à un site tel que <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> avec les paramètres de proxy activés et désactivés dans Firefox. Lorsqu'ils sont activés, l'adresse IP sera celle d'une machine dans le centre de données Microsoft Azure.

###Extensions du navigateur

Si la configuration du navigateur pour utiliser le tunnel fonctionne, en principe vous n'avez pas besoin d'acheminer tout le trafic via le tunnel. Les extensions de navigateur telles que <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a> prennent en charge la correspondance pour les requêtes URL (FoxyProxy Standard ou Plus uniquement) pour que seules les requêtes pour des URL spécifiques soient envoyées via le tunnel.

Si vous avez installé **FoxyProxy Standard**, suivez les étapes suivantes pour configurer celui-ci uniquement pour le trafic de transfert de HDInsight via le tunnel.

1. Ouvrez l'extension FoxyProxy dans votre navigateur. Par exemple, dans Firefox, sélectionnez l'icône FireProxy à côté du champ d'adresse.

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxy.png)

2. Sélectionnez **Ajouter un nouveau proxy**, l'onglet **Général** puis entrez un nom de proxy pour **HDInsight**.

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-unix/foxygeneral.png)

3. Sélectionnez l'**onglet Informations du proxy** et remplissez les champs suivants.

	* **Hôte ou adresse IP** : localhost, puisque nous utilisons un tunnel SSH sur la machine locale.

	* **Port** : le port que vous avez utilisé pour le tunnel SSH.

	* **Proxy SOCKS** : sélectionnez celui-ci pour activer l'utilisation du tunnel comme proxy dans le navigateur.

	* **SOCKS v5** : sélectionnez celui-ci pour définir la version requise pour le proxy.

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxyproxy.png)

4. Sélectionnez l'onglet **Type d'URL** puis sélectionnez **Ajouter un nouveau type**. Utilisez ce qui suit pour définir le type puis cliquez sur **OK**.

	* **Nom de type** - **nœud principal** : il s'agit simplement d'un nom de type convivial.

	* **Modèle d'URL** - **\*nœud principal\*** : cela définit un type qui correspond à toute URL contenant les mots **nœud principal**.

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-unix/foxypattern.png)

4. Sélectionnez **Ok** pour ajouter le proxy et fermer les **Paramètres de proxy**.

5. En haut de la boîte de dialogue FoxyProxy, changez le **Mode de sélection** pour **Utiliser les proxys basés sur leurs types et priorités prédéfinis** puis sélectionnez **Fermer**.

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-unix/selectmode.png)

Après avoir suivi ces étapes, seules les requêtes pour les URL qui contiennent la chaîne **nœud principal** seront acheminées via le tunnel SSL.

##Étapes suivantes

Maintenant que vous savez effectuer une authentification avec une clé SSH, apprenez comment utiliser MapReduce avec Hadoop dans HDInsight.

* [Utiliser Hive avec HDInsight](hdinsight-use-hive.md)

* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)

* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
 
<!--HONumber=47-->
