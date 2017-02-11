---
title: "Utilisation de SSH Tunneling pour accéder à l’interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d’autres interfaces Web"
description: "Apprenez à utiliser un tunnel SSH pour accéder de façon sécurisée à des ressources Web hébergées sur vos nœuds HDInsight sous Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 446212192829cc55fefe4b1a1954e64e123c2c44


---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Utilisation de SSH Tunneling pour accéder à l’interface Web Ambari, JobHistory, NameNode, Oozie et d’autres interfaces Web
Les clusters HDInsight sous Linux donnent accès à l'interface utilisateur Web Ambari à Internet, mais pas pour certaines fonctionnalités de l'interface utilisateur. Par exemple, l'interface utilisateur Web pour d'autres services qui sont exposés via Ambari. Pour bénéficier de toutes les fonctionnalités de l'interface utilisateur Web Ambari, vous devez utiliser un tunnel SSH vers le principal cluster.

## <a name="what-requires-an-ssh-tunnel"></a>Pourquoi un tunnel SSH est-il nécessaire ?
De nombreux menus dans Ambari ne se remplissent pas complètement sans un tunnel SSH, car ils s'appuient sur des sites et des services Web exposés par d'autres services Hadoop s'exécutant sur le cluster. Souvent, ces sites Web ne sont pas sécurisés. Il est donc dangereux de les exposer directement sur Internet. Parfois, le service exécute le site Web sur un autre nœud de cluster, comme un nœud Zookeeper.

L’interface utilisateur Web Ambari utilise les services suivants, qui ne sont pas accessibles sans un tunnel SSH :

* JobHistory,
* NameNode,
* Thread Stacks,
* Interface utilisateur Web Oozie
* Interface HBase Master et interface de journaux

Si vous utilisez des actions de script pour personnaliser votre cluster, tous les services ou utilitaires que vous installez et qui exposent une interface utilisateur Web nécessitent un tunnel SSH. Par exemple, si vous installez Hue à l'aide d'une action de script, vous devez utiliser un tunnel SSH pour accéder à l'interface utilisateur Web Hue.

## <a name="what-is-an-ssh-tunnel"></a>Qu'est-ce qu’un tunnel SSH ?
[Le tunneling Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) achemine le trafic envoyé à un port sur votre station de travail locale, par le biais d’une connexion SSH à votre nœud principal de cluster HDInsight, où la demande est ensuite résolue comme si elle avait été créée sur le nœud principal. La réponse est alors acheminée via le tunnel sur votre station de travail.

## <a name="prerequisites"></a>Composants requis
Lorsque vous utilisez un tunnel SSH pour le trafic Web, vous devez disposer des éléments suivants :

* Un client SSH. Pour les distributions Linux et Unix ou pour Macintosh OS X, la commande `ssh` est fournie avec le système d'exploitation. Pour Windows, nous vous recommandons [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
  
  > [!NOTE]
  > Si vous souhaitez utiliser un client SSH autre que `ssh` ou PuTTY, veuillez consulter la documentation de votre client qui explique comment établir un tunnel SSH.
  > 
  > 
* Un navigateur Web qui peut être configuré pour utiliser un proxy SOCKS

## <a name="a-nameusesshacreate-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Création d’un tunnel à l'aide de la commande SSH
Utilisez la commande suivante pour créer un tunnel SSH à l'aide de la commande `ssh` . Remplacez **USERNAME** par un utilisateur SSH de votre cluster HDInsight et **CLUSTERNAME** par le nom de votre cluster HDInsight

    ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Cette commande va permettre de créer une connexion qui achemine le trafic vers le port local 9876 du cluster via SSH. Les options sont :

* **D 9876** : port local qui acheminera le trafic via le tunnel.
* **C** : compresse toutes les données car le trafic web est principalement du texte
* **2** : force le SSH à essayer le protocole version 2 uniquement
* **q** : mode silencieux
* **T** : désactive l’allocation pseudo-tty puisque nous transférons simplement un port
* **n** : empêche la lecture STDIN puisque nous transférons simplement un port
* **N** : n’exécute pas une commande à distance puisque nous transférons simplement un port
* **f** : s’exécute à l’arrière-plan

Si vous avez configuré le cluster avec une clé SSH, il vous faudra peut-être utiliser le paramètre `-i` et indiquer le chemin d’accès vers la clé SSH privée.

Une fois la commande terminée, le trafic envoyé au port 9876 sur l’ordinateur local sera acheminé via SSL au nœud principal du cluster et semblera provenir de celui-ci.

## <a name="a-nameuseputtyacreate-a-tunnel-using-putty"></a><a name="useputty"></a>Création d’un tunnel à l'aide de PuTTY
Pour créer un tunnel SSH à l’aide de PuTTY, procédez comme suit.

1. Ouvrez PuTTY et saisissez vos informations de connexion. Si vous ne connaissez pas PuTTY, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md) pour plus d'informations sur son utilisation avec HDInsight.
2. Dans la rubrique **Catégorie** située à gauche dans la boîte de dialogue, développez **Connexion** et **SSH**, puis sélectionnez **Tunnels**.
3. Indiquez les informations suivantes dans le formulaire des **Options de contrôle de transfert du port SSH** .
   
   * **Port source** : le port sur le client que vous souhaitez transférer. Par exemple : **9876**.
   * **Destination** : l’adresse SSH pour le cluster HDInsight sous Linux. Par exemple : **moncluster-ssh.azurehdinsight.net**.
   * **Dynamique** : active le routage dynamique du proxy SOCKS.
     
     ![image des options de tunneling](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)
4. Cliquez sur **Ajouter** pour ajouter les paramètres, puis cliquez sur **Ouvrir** pour ouvrir une connexion SSH.
5. Lorsque vous y êtes invité, connectez-vous au serveur. Cela permettra d'établir une session SSH et d'activer le tunnel.

## <a name="use-the-tunnel-from-your-browser"></a>Utilisation du tunnel à partir de votre navigateur
> [!NOTE]
> Dans les étapes de cette section, nous utilisons le navigateur FireFox, car il est disponible gratuitement pour les systèmes d’exploitation Linux, Unix, Macintosh OS X et Windows. D’autres navigateurs modernes prenant en charge l’utilisation d’un proxy SOCKS fonctionneront également.
> 
> 

1. Configurez le navigateur pour utiliser **localhost:9876** comme un proxy **SOCKS v5**. Voici comment se présentent les paramètres Firefox : si vous avez utilisé un port autre que 9876, modifiez le port par celui que vous avez utilisé :
   
    ![image des paramètres de Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)
   
   > [!NOTE]
   > La sélection de **DNS Distant** résout les requêtes DNS à l’aide du cluster HDInsight. Si cette option n’est pas sélectionnée, le DNS est résolu localement.
   > 
   > 
2. Vérifiez que le trafic est routé via le tunnel en accédant à un site, comme [http://www.whatismyip.com/](http://www.whatismyip.com/) , avec les paramètres de proxy activés et désactivés dans Firefox. Quand ils sont activés, l’adresse IP est celle d’une machine du centre de données Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Vérification avec l'interface utilisateur Web Ambari
Une fois le cluster établi, suivez ces étapes pour vérifier que vous pouvez accéder à des interfaces utilisateur Web de service à partir du Web Ambari :

1. Dans votre navigateur, accédez à http://headnodehost:8080. L’adresse `headnodehost` est envoyée via le tunnel au cluster et renvoie le nœud principal sur lequel Ambari s’exécute. Lorsque vous y êtes invité, saisissez le nom de l’utilisateur admin (admin) et le mot de passe correspondant à votre cluster. Vous pouvez y être invité une seconde fois par l'interface utilisateur Web Ambari. Dans ce cas, saisissez de nouveau ces informations.
   
   > [!NOTE]
   > Lorsque vous utilisez l’adresse http://headnodehost:8080 pour vous connecter au cluster, vous vous connectez directement via le tunnel au nœud principal sur lequel s’exécute Ambari à l’aide de HTTP et la communication est sécurisée à l’aide du tunnel SSH. Lorsque vous vous connectez via internet sans utiliser de tunnel, la communication est sécurisée à l’aide de HTTPS. Pour vous connecter à Internet à l’aide de HTTPS, utilisez https://CLUSTERNAME.azurehdinsight.net, où **CLUSTERNAME** est le nom du cluster.
   > 
   > 
2. Dans l'interface utilisateur Web Ambari, sélectionnez HDFS dans la liste située sur la gauche de la page.
   
    ![Image avec HDFS sélectionné](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)
3. Lorsque les informations de service HDFS s’affichent, sélectionnez **Liens rapides**. Une liste des nœuds principaux du cluster s'affiche. Sélectionnez l’un des nœuds principaux, puis **Interface utilisateur NameNode**.
   
    ![Image avec le menu Liens rapides développé](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)
   
   > [!NOTE]
   > Si votre connexion Internet est lente ou que le nœud principal est très occupé, il est possible que vous obteniez un indicateur d’attente au lieu d’un menu lorsque vous sélectionnez **Liens rapides**. Dans ce cas, patientez une minute ou deux pour recevoir les données du serveur, puis réessayez.
   > 
   > Si la résolution de votre moniteur est trop faible ou que la fenêtre du navigateur n’est pas agrandie, certaines entrées du menu **Liens rapides** peuvent être tronquées par le côté droit de l’écran. Dans ce cas, développez le menu à l'aide de la souris, puis utilisez la touche fléchée droite pour faire défiler l'écran vers la droite afin d’afficher le reste du menu.
   > 
   > 
4. Une page semblable à celle-ci doit s'afficher :
   
    ![Image de l’interface utilisateur NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)
   
   > [!NOTE]
   > Notez l’URL de cette page. Elle doit être semblable à **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Elle utilise le nom de domaine complet (FQDN) interne du nœud et n'est pas accessible sans l’utilisation d’un tunnel SSH.
   > 
   > 

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à créer et utiliser un tunnel SSH, consultez les articles suivants pour plus d'informations sur la surveillance et la gestion de votre cluster à l'aide d’Ambari :

* [Gestion des clusters HDInsight à l'aide d’Ambari](hdinsight-hadoop-manage-ambari.md)

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les articles suivants :

* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)




<!--HONumber=Nov16_HO3-->


