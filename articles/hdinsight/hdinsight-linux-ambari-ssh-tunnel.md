<properties
pageTitle="Utilisation de SSH Tunneling pour accéder à l’interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d’autres interfaces Web"
description="Apprenez à utiliser un tunnel SSH pour accéder de façon sécurisée à des ressources Web hébergées sur vos nœuds HDInsight sous Linux."
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
ms.date="07/22/2016"
ms.author="larryfr"/>

#Utilisation de SSH Tunneling pour accéder à l’interface Web Ambari, JobHistory, NameNode, Oozie et d’autres interfaces Web

Les clusters HDInsight sous Linux donnent accès à l'interface utilisateur Web Ambari à Internet, mais pas pour certaines fonctionnalités de l'interface utilisateur. Par exemple, l'interface utilisateur Web pour d'autres services qui sont exposés via Ambari. Pour bénéficier de toutes les fonctionnalités de l'interface utilisateur Web Ambari, vous devez utiliser un tunnel SSH vers le principal cluster.

##Pourquoi un tunnel SSH est-il nécessaire ?

De nombreux menus dans Ambari ne se remplissent pas complètement sans un tunnel SSH, car ils s'appuient sur des sites et des services Web exposés par d'autres services Hadoop s'exécutant sur le cluster. Souvent, ces sites Web ne sont pas sécurisés. Il est donc dangereux de les exposer directement sur Internet. Parfois, le service exécute le site Web sur un autre nœud de cluster, comme un nœud Zookeeper.

L’interface utilisateur Web Ambari utilise les services suivants, qui ne sont pas accessibles sans un tunnel SSH :

* JobHistory,
* NameNode,
* Thread Stacks,
* Interface utilisateur Web Oozie
* Interface HBase Master et interface de journaux

Si vous utilisez des actions de script pour personnaliser votre cluster, tous les services ou utilitaires que vous installez et qui exposent une interface utilisateur Web nécessitent un tunnel SSH. Par exemple, si vous installez Hue à l'aide d'une action de script, vous devez utiliser un tunnel SSH pour accéder à l'interface utilisateur Web Hue.

##Qu'est-ce qu’un tunnel SSH ?

[Le tunneling Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) achemine le trafic envoyé à un port sur votre station de travail locale, par le biais d’une connexion SSH à votre nœud principal de cluster HDInsight, où la demande est ensuite résolue comme si elle avait été créée sur le nœud principal. La réponse est alors acheminée via le tunnel sur votre station de travail.

##Composants requis

Lorsque vous utilisez un tunnel SSH pour le trafic Web, vous devez disposer des éléments suivants :

* Un client SSH. Pour les distributions Linux et Unix ou pour Macintosh OS X, la commande `ssh` est fournie avec le système d'exploitation. Pour Windows, nous vous recommandons [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

	> [AZURE.NOTE] Si vous souhaitez utiliser un client SSH autre que `ssh` ou PuTTY, veuillez consulter la documentation de votre client qui explique comment établir un tunnel SSH.

* Un navigateur Web qui peut être configuré pour utiliser un proxy SOCKS

* __(facultatif)__ : un plug-in comme [FoxyProxy](http://getfoxyproxy.org/,) pouvant appliquer des règles qui acheminent uniquement les demandes spécifiques via le tunnel.

	> [AZURE.WARNING] Sans un plug-in comme FoxyProxy, toutes les demandes effectuées via le navigateur peuvent être routées via le tunnel. Cela peut entraîner un chargement plus lent des pages Web dans votre navigateur.

##<a name="usessh"></a>Création d’un tunnel à l'aide de la commande SSH

Utilisez la commande suivante pour créer un tunnel SSH à l'aide de la commande `ssh`. Remplacez __USERNAME__ par un utilisateur SSH de votre cluster HDInsight et __CLUSTERNAME__ par le nom de votre cluster HDInsight

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Cette commande va permettre de créer une connexion qui achemine le trafic vers le port local 9876 du cluster via SSH. Les options sont :

* **D 9876** : port local qui acheminera le trafic via le tunnel.

* **C** : compresse toutes les données car le trafic web est principalement du texte

* **2** : force le SSH à essayer le protocole version 2 uniquement

* **q** : mode silencieux

* **T** : désactive l’allocation pseudo-tty puisque nous transférons simplement un port

* **n** : empêche la lecture STDIN puisque nous transférons simplement un port

* **N** : n’exécute pas une commande à distance puisque nous transférons simplement un port

* **f** : s’exécute à l’arrière-plan

Si vous avez configuré le cluster avec une clé SSH, il vous faudra peut-être utiliser le paramètre `-i` et indiquer le chemin d’accès vers la clé SSH privée.

Une fois la commande terminée, le trafic envoyé au port 9876 sur l’ordinateur local sera acheminé via SSL au nœud principal du cluster et semblera provenir de celui-ci.

##<a name="useputty"></a>Création d’un tunnel à l'aide de PuTTY

Pour créer un tunnel SSH à l’aide de PuTTY, procédez comme suit.

1. Ouvrez PuTTY et saisissez vos informations de connexion. Si vous ne connaissez pas PuTTY, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md) pour plus d'informations sur son utilisation avec HDInsight.

2. Dans la rubrique **Catégorie** située à gauche dans la boîte de dialogue, développez **Connexion** et **SSH**, puis sélectionnez **Tunnels**.

3. Indiquez les informations suivantes dans le formulaire des **Options de contrôle de transfert du port SSH**.

	* **Port source** : le port sur le client que vous souhaitez transférer. Par exemple : **9876**.

	* **Destination** : l’adresse SSH pour le cluster HDInsight sous Linux. Par exemple : **moncluster-ssh.azurehdinsight.net**.

	* **Dynamique** : active le routage dynamique du proxy SOCKS.

	![image des options de tunneling](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Cliquez sur **Ajouter** pour ajouter les paramètres, puis cliquez sur **Ouvrir** pour ouvrir une connexion SSH.

5. Lorsque vous y êtes invité, connectez-vous au serveur. Cela permettra d'établir une session SSH et d'activer le tunnel.

##Utilisation du tunnel à partir de votre navigateur

> [AZURE.NOTE] Dans les étapes de cette section, nous utilisons le navigateur FireFox, car il est disponible gratuitement pour les systèmes d’exploitation Linux, Unix, Macintosh OS X et Windows. Les autres navigateurs modernes comme Google Chrome, Microsoft Edge ou Apple Safari devraient fonctionner aussi bien ; cependant, le plug-in FoxyProxy utilisé dans certaines étapes peut ne pas être disponible pour tous les navigateurs.

1. Configurez le navigateur pour utiliser **localhost:9876** comme un proxy **SOCKS v5**. Voici comment se présentent les paramètres Firefox : si vous avez utilisé un port autre que 9876, modifiez le port par celui que vous avez utilisé :

	![image des paramètres de Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)

	> [AZURE.NOTE] La sélection de **DNS Distant** résout les requêtes DNS à l’aide du cluster HDInsight. Si cette option n’est pas sélectionnée, le DNS est résolu localement.

2. Vérifiez que le trafic est routé via le tunnel en accédant à un site, comme [http://www.whatismyip.com/](http://www.whatismyip.com/), avec les paramètres de proxy activés et désactivés dans Firefox. Quand ils sont activés, l’adresse IP est celle d’une machine du centre de données Microsoft Azure.

###Extensions du navigateur

Si la configuration du navigateur pour utiliser le tunnel fonctionne, en principe vous n'avez pas besoin d'acheminer tout le trafic via le tunnel. Les extensions de navigateur comme [FoxyProxy](http://getfoxyproxy.org/) prennent en charge la correspondance des requêtes URL (FoxyProxy Standard ou Plus uniquement) pour que seules les requêtes d'URL spécifiques soient envoyées via le tunnel.

Si vous avez installé FoxyProxy Standard, suivez les étapes suivantes pour configurer celui-ci uniquement pour le trafic de transfert de HDInsight via le tunnel.

1. Ouvrez l'extension FoxyProxy dans votre navigateur. Par exemple, dans Firefox, sélectionnez l'icône FireProxy à côté du champ d'adresse.

	![icône foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxy.png)

2. Sélectionnez **Ajouter un nouveau proxy**, puis l’onglet **Général** et entrez un nom de proxy pour **HDInsightProxy**.

	![onglet général foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxygeneral.png)

3. Sélectionnez l’onglet **Informations du proxy** et remplissez les champs suivants :

	* **Hôte ou adresse IP** : il s’agit de localhost, puisque nous utilisons un tunnel SSH sur la machine locale.

	* **Port** : port que vous avez utilisé pour le tunnel SSH

	* **Proxy SOCKS** : sélectionnez celui-ci pour autoriser le navigateur à utiliser le tunnel comme proxy.

	* **SOCKS v5** : sélectionnez celui-ci pour définir la version requise pour le proxy.

	![proxy foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxyproxy.png)

4. Sélectionnez l’onglet **Type d’URL**, puis **Ajouter un nouveau type**. Utilisez ce qui suit pour définir le type, puis cliquez sur **OK** :

	* **Nom de type** - **clusternodes** : il s’agit simplement d’un nom de type convivial.

	* **Modèle d’URL** - **\*internal.cloudapp.net\*** : définit un modèle qui correspond au nom de domaine complet interne des nœuds du cluster.

	![type foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxypattern.png)

    Ajoutez un autre modèle, en utilisant les informations suivantes pour les paramètres :

    * __Nom de modèle__ : nœud principal
    * __Modèle d’URL__ : \*hôte du nœud principal\*

    Cliquez sur OK pour enregistrer ce modèle.

4. Cliquez sur **OK** pour ajouter le proxy et fermer les **Paramètres de proxy**.

5. Dans la partie supérieure de la boîte de dialogue FoxyProxy, changez le **Mode de sélection** pour **Utiliser les proxys en fonction de leurs types et priorités prédéfinis**, puis cliquez sur **Fermer**.

	![mode de sélection foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/selectmode.png)

Après avoir suivi ces étapes, seules les requêtes pour les URL qui contiennent la chaîne __internal.cloudapp.net__ seront acheminées via le tunnel SSL.

##Vérification avec l'interface utilisateur Web Ambari

Une fois le cluster établi, suivez ces étapes pour vérifier que vous pouvez accéder à des interfaces utilisateur Web de service à partir du Web Ambari :

1. Dans votre navigateur, accédez à http://headnodehost:8080. L’adresse `headnodehost` est envoyée via le tunnel au cluster et renvoie le nœud principal sur lequel Ambari s’exécute. Lorsque vous y êtes invité, saisissez le nom de l’utilisateur admin (admin) et le mot de passe correspondant à votre cluster. Vous pouvez y être invité une seconde fois par l'interface utilisateur Web Ambari. Dans ce cas, saisissez de nouveau ces informations.
    
    > [AZURE.NOTE] Lorsque vous utilisez l’adresse http://headnodehost:8080 pour vous connecter au cluster, vous vous connectez directement via le tunnel au nœud principal sur lequel s’exécute Ambari à l’aide de HTTP et la communication est sécurisée à l’aide du tunnel SSH. Lorsque vous vous connectez via internet sans utiliser de tunnel, la communication est sécurisée à l’aide de HTTPS. Pour vous connecter à Internet à l’aide de HTTPS, utilisez https://CLUSTERNAME.azurehdinsight.net, où __CLUSTERNAME__ est le nom du cluster.

2. Dans l'interface utilisateur Web Ambari, sélectionnez HDFS dans la liste située sur la gauche de la page.

	![Image avec HDFS sélectionné](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Lorsque les informations de service HDFS s’affichent, sélectionnez __Liens rapides__. Une liste des nœuds principaux du cluster s'affiche. Sélectionnez l’un des nœuds principaux, puis __Interface utilisateur NameNode__.

	![Image avec le menu Liens rapides développé](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

	> [AZURE.NOTE] Si votre connexion Internet est lente ou que le nœud principal est très occupé, il est possible que vous obteniez un indicateur d’attente au lieu d’un menu lorsque vous sélectionnez __Liens rapides__. Dans ce cas, patientez une minute ou deux pour recevoir les données du serveur, puis réessayez.
    >
	> Si la résolution de votre moniteur est trop faible ou que la fenêtre du navigateur n’est pas agrandie, certaines entrées du menu __Liens rapides__ peuvent être tronquées par le côté droit de l’écran. Dans ce cas, développez le menu à l'aide de la souris, puis utilisez la touche fléchée droite pour faire défiler l'écran vers la droite afin d’afficher le reste du menu.

4. Une page semblable à celle-ci doit s'afficher :

	![Image de l’interface utilisateur NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

	> [AZURE.NOTE] Observez l’URL de cette page. Elle doit être semblable à \_\_http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster__. Elle utilise le nom de domaine complet (FQDN) interne du nœud et n'est pas accessible sans l’utilisation d’un tunnel SSH.

##Étapes suivantes

Maintenant que vous avez appris à créer et utiliser un tunnel SSH, consultez les articles suivants pour plus d'informations sur la surveillance et la gestion de votre cluster à l'aide d’Ambari :

* [Gestion des clusters HDInsight à l'aide d’Ambari](hdinsight-hadoop-manage-ambari.md)

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les articles suivants :

* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

<!---HONumber=AcomDC_0727_2016-->
