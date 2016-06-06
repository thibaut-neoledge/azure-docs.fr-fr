<properties
   pageTitle="Déploiement et gestion de topologies Apache Storm sur HDInsight basé sur Linux | Microsoft Azure"
   description="Apprenez à déployer, surveiller et gérer des topologies Apache Storm à l’aide du tableau de bord Storm sur HDInsight basé sur Linux. Utilisez les outils Hadoop pour Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/20/2016"
   ms.author="larryfr"/>

# Déploiement et gestion des topologies Apache Storm sur HDInsight Linux

Ce document présente les principes fondamentaux de la gestion et de la surveillance des topologies Storm qui s’exécutent sur des clusters Storm Linux sur HDInsight.

> [AZURE.IMPORTANT] Les étapes décrites dans cet article nécessitent un cluster Storm Linux sur HDInsight. Pour plus d’informations sur le déploiement et la surveillance des topologies sur HDInsight Windows, consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Windows](hdinsight-storm-deploy-monitor-topology.md)

## Configuration requise

- **Un cluster Storm Linux sur HDInsight** : consultez [Prise en main d’Apache Storm sur HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) pour connaître les étapes de création d’un cluster

- **Des connaissances en SSH et SCP** : pour plus d’informations sur l’utilisation de SSH et SCP avec HDInsight, consultez les articles suivants :
    - **Clients Linux, Unix ou OS X** : consultez [Utilisation de SSH avec un cluster Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    - **Clients Windows** : consultez [Utilisation de SSH avec un cluster Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- **Un client SCP** : il est fourni avec tous les systèmes Linux, Unix et OS X. Pour les clients Windows, nous vous recommandons PSCP, disponible dans la [page de téléchargement de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## Démarrage d’une topologie Storm

1. Utilisez SSH pour vous connecter au cluster HDInsight. Remplacez **USERNAME** par le nom de votre connexion SSH. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Pour plus d’informations sur l’utilisation de SSH pour se connecter à votre cluster HDInsight, consultez les documents suivants :
    
        - **Linux, Unix or OS X clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Linux, OS X or Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        - **Windows clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Utilisez la commande suivante pour démarrer un exemple de topologie :

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Cette opération démarre l’exemple de topologie WordCount sur le cluster. Elle va générer de manière aléatoire des phrases et compter les occurrences de chaque mot dans les phrases.

    > [AZURE.NOTE] Pendant l’envoi de la topologie au cluster, vous devez d’abord copier le fichier jar contenant le cluster avant d’utiliser la commande `storm`. Pour ce faire, vous pouvez utiliser la commande `scp` à partir du client où se trouve le fichier. Par exemple, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > L’exemple WordCount et d’autres exemples de Storm Starter sont déjà inclus dans votre cluster sous `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##Surveillance et gestion à l’aide de la commande storm

L’utilitaire `storm` vous permet d’utiliser des topologies en cours d’exécution à partir de la ligne de commande. Voici une liste des commandes couramment utilisées. Pour obtenir la liste complète des commandes, utilisez `storm -h`.

###Liste de toutes les topologies

Utilisez la commande suivante pour répertorier toutes les topologies en cours d’exécution :

    storm list
    
La commande retourne des informations similaires à ce qui suit :

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

###Désactivation et réactivation

La désactivation d’une topologie la met en pause jusqu’à ce qu’elle soit arrêtée ou réactivée. Utilisez les commandes suivantes pour désactiver et réactiver :

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

###Arrêt d’une topologie en cours d’exécution

Les topologies Storm, une fois démarrées, continuent leur exécution jusqu’à ce qu’elles soient arrêtées. Pour ce faire, utilisez la commande suivante :

    storm stop TOPOLOGYNAME

###Rééquilibrage

Le rééquilibrage d’une topologie permet au système de réviser le parallélisme de la topologie. Par exemple, si vous avez redimensionné le cluster pour ajouter plus de nœuds, le rééquilibrage permet à une topologie en cours d’exécution d’utiliser les nouveaux nœuds.

> [AZURE.WARNING] Le rééquilibrage d’une topologie désactive d’abord la topologie, puis redistribue les workers uniformément sur le cluster avant de rétablir la topologie dans l’état où elle se trouvait avant le rééquilibrage. Par conséquent, si la topologie était active, elle redevient active. Si elle était désactivée, elle reste désactivée.

    storm rebalance TOPOLOGYNAME

##Surveillance et gestion à l’aide de l’interface utilisateur Storm

L’interface utilisateur Storm fournit une interface web incluse dans votre cluster HDInsight pour utiliser les topologies en cours d’exécution. Pour afficher l’interface utilisateur Storm, ouvrez un navigateur web et accédez à __https://CLUSTERNAME.azurehdinsight.net/stormui__, où __CLUSTERNAME__ est le nom de votre cluster.

> [AZURE.NOTE] Si vous êtes invité à fournir un nom d’utilisateur et un mot de passe, entrez l’administrateur de cluster (admin) et le mot de passe que vous avez utilisé pour la création du cluster.


### Page principale

La page principale de l’interface utilisateur de Storm fournit les informations suivantes :
- **Résumé du cluster** : des informations de base sur le cluster Storm.
- **Résumé de la topologie** : une liste des topologies en cours d’exécution. Utilisez les liens de cette section pour afficher plus d’informations sur les topologies spécifiques.
- **Résumé du superviseur**: des informations sur le superviseur Storm.
- **Configuration Nimbus** : configuration Nimbus du cluster.

### Résumé de la topologie

La sélection d’un lien de la section **Résumé de la topologie** affiche les informations suivantes sur la topologie :
- **Résumé de la topologie** :des informations de base sur la topologie.
- **Actions de la topologie** : les actions de gestion que vous pouvez effectuer sur la topologie.
  - **Activer** : reprend le traitement d’une topologie arrêtée.
  - **Désactiver** : suspend une topologie en cours d’exécution.
  - **Rééquilibrer**: ajuste le parallélisme de la topologie. Il convient de rééquilibrer les topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Cela permet à la topologie d’ajuster le parallélisme pour compenser l’augmentation ou la diminution du nombre de nœuds du cluster.

      Pour plus d’informations, consultez la rubrique <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Présentation du parallélisme d’une topologie Storm</a>.

  - **Supprimer** : met fin à une topologie Storm après expiration du délai spécifié.

- **Topology stats** : statistiques relatives à la topologie. Utilisez les liens de la colonne **Fenêtre** pour définir l’intervalle de temps des entrées restantes sur la page.
- **Spouts** : les spouts utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur des spouts spécifiques.
- **Bolts** : les bolts utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur des bolts spécifiques.
- **Configuration de la topologie** : configuration de la topologie sélectionnée.

### Résumé relatif aux spouts et aux bolts

La sélection d’un spout à partir de la section **Spouts** ou **Bolts** affiche les informations suivantes sur l’élément sélectionné :
- **Résumé du composant** : des informations de base sur le spout ou le bolt.
- **Statistiques du spout/bolt** : des statistiques relatives au spout ou au bolt. Utilisez les liens de la colonne **Fenêtre** pour définir l’intervalle de temps des entrées restantes sur la page.
- **Statistiques d’entrée** (bolt uniquement) : des informations sur les flux d’entrée consommés par le bolt.
- **Statistiques de sortie** : des informations sur les flux de données émis par ce spout ou ce bolt.
- **Exécuteurs** : informations sur les instances du spout ou du bolt. Sélectionnez l’entrée **Port** d’un exécuteur spécifique afin d’afficher le journal des informations de diagnostic généré pour cette instance.
- **Erreurs** : les informations d’erreur pour ce spout ou ce bolt.

## API REST

L’interface utilisateur Storm repose sur l’API REST, ce qui vous permet de profiter de fonctionnalités de gestion et de surveillance similaires à l’aide de l’API REST. À l'aide de l'API REST, vous pouvez créer des outils personnalisés pour gérer et surveiller les topologies Storm.

Pour plus d’informations, consultez la rubrique [API REST de l’interface utilisateur Storm](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Les informations suivantes sont spécifiques à l’utilisation de l’API REST avec Apache Storm sur HDInsight.

> [AZURE.IMPORTANT] L’API REST Storm n’est pas disponible publiquement sur Internet et est accessible à l’aide d’un tunnel SSH vers le nœud principal du cluster HDInsight. Pour plus d’informations sur la création et l’utilisation d’un tunnel SSH, consultez [Utilisation d’un tunnel SSH pour accéder à l’interface utilisateur web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d’autres interfaces utilisateur web](hdinsight-linux-ambari-ssh-tunnel.md).

### URI de base

L’URI de base de l’API REST sur les clusters HDInsight basés sur Linux est disponible sur le nœud principal à l’adresse **https://HEADNODEFQDN:8744/api/v1/** ; en revanche, le nom de domaine du nœud principal est généré lors de la création du cluster et il n’est pas statique.

Vous trouverez le nom de domaine complet (FQDN) du nœud principal du cluster de plusieurs façons différentes :

* __À partir d’une session SSH__ : utilisez la commande `headnode -f` à partir d’une session SSH vers le cluster.
* __À partir d’Ambari Web__ : sélectionnez __Services__ en haut de la page, puis sélectionnez __Storm__. Sous l’onglet __Résumé__, sélectionnez __Serveur de l’interface utilisateur de Storm__. Le nom de domaine complet du nœud que l’interface utilisateur de Storm et l’API REST exécutent figure en haut de la page.
* __À partir de l’API REST d’Ambari__ : utilisez la commande `curl -u admin:PASSWORD -G "https://CLUSTERNAME
.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` pour extraire des informations sur le nœud sur lequel l’interface utilisateur de Storm et l’API REST s’exécutent. Remplacez __PASSWORD__ par le mot de passe de l’administrateur du cluster. Remplacez __CLUSTERNAME__ par le nom du cluster. Dans la réponse, l’entrée « host\_name » contient le nom de domaine complet du nœud.

### Authentification

Les requêtes à l’API REST doivent utiliser l’**authentification de base** avec le nom et le mot de passe d’administrateur de cluster HDInsight.

> [AZURE.NOTE] Étant donné que l’authentification de base est envoyée en texte clair, vous devez **toujours** utiliser HTTPS pour sécuriser les communications avec le cluster.

### Valeurs de retour

Les informations renvoyées par l’API REST sont uniquement utilisables au sein du cluster ou des machines virtuelles sur le même réseau virtuel Azure que le cluster. Par exemple, le nom de domaine complet (FQDN) retourné pour les serveurs Zookeeper ne sera pas accessible à partir d’Internet.

## Étapes suivantes

Maintenant que vous avez appris à déployer et surveiller des topologies à l’aide du tableau de bord Storm, découvrez comment [développer des topologies Java à l’aide de Maven](hdinsight-storm-develop-java-topology.md).

Pour accéder à une liste d’exemples supplémentaires de topologies, consultez la rubrique [Exemples de topologies Storm sur HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0525_2016-->