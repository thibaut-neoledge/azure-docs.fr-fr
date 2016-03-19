<properties
   pageTitle="Utiliser des composants Python dans une topologie Storm sur HDinsight | Microsoft Azure"
   description="Découvrez comment utiliser des composants Python avec Apache Storm sur Azure HDInsight. Vous y apprendrez à utiliser les composants Python à partir d’une topologie Storm basée sur Java et Clojure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/01/2016"
   ms.author="larryfr"/>

#Développer des topologies Storm Apache à l’aide de Python sur HDInsight

Apache Storm prend en charge plusieurs langages et vous permet de combiner des composants de plusieurs langages dans une même topologie. Dans cet article, vous apprendrez à utiliser les composants de Python dans vos topologies Storm basées sur Java et Clojure à partir de HDInsight.

##Configuration requise

* Python 2.7 ou ultérieure

* Java JDK 1.7 ou ultérieure

* [Leiningen](http://leiningen.org/)

##Prise en charge multi-langage de Storm

Storm a été conçu pour prendre en charge des composants codés à l’aide de n’importe quel langage de programmation. Cette prise en charge suppose toutefois que les composants apprennent à fonctionner avec la [définition de Thrift pour Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Pour Python, un module est fourni dans le cadre du projet Apache Storm pour vous permettre de communiquer facilement avec Storm. Ce module est disponible à l’adresse [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Apache Storm est un processus Java qui s’exécute sur la machine virtuelle Java (JVM). Autrement dit, les composants codés dans d’autres langues sont exécutés sous la forme de sous-processus. Les bits Storm exécutés dans la JVM communiquent avec ces sous-processus à l’aide de messages JSON envoyées via stdin/stdout. Vous trouverez plus de détails sur la communication entre les composants dans la documentation relative au [Protocole multi-langage](https://storm.apache.org/documentation/Multilang-protocol.html).

###Le module Storm

Le module Storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) fournit les bits nécessaires pour créer des composants Python compatibles avec Storm.

Il génère par exemple des bits `storm.emit` pour émettre des uplets ou des bits `storm.logInfo` pour écrire des données dans des fichiers journaux. Je vous encourage à lire ce fichier pour comprendre ce qu’il apporte.

##Défis

Le module __storm.py__ vous permet de créer des « spouts » Python qui consomment des données, ainsi que des « bolts » qui traitent les données. Mais la définition globale de la topologie Storm qui établit la communication entre les composants est toujours écrite à l’aide de Java ou Clojure. En outre, si vous utilisez Java, vous devez également créer des composants Java qui font office d’interface avec les composants de Python.

Puisque les clusters Storm sont exécutés en mode distribué, vous devez vous assurer que tous les modules requis par vos composants Python sont disponibles sur tous les nœuds de travail du cluster. Storm ne vous permet pas de le faire facilement avec des ressources codées à l’aide de plusieurs langages. Vous devez donc soit inclure toutes les dépendances dans le fichier jar de la topologie, soit installer manuellement les dépendances sur chaque nœud de travail dans le cluster.

Certains projets, tels que [Pyleus](https://github.com/Yelp/pyleus) et [Streamparse](https://github.com/Parsely/streamparse), cherchent à résoudre ces problèmes. Bien que les deux puissent être exécutés sur des clusters HDInsight sous Linux, ils ne seront pas abordés dans le présent document puisqu’ils nécessitent des personnalisations lors de l’installation du cluster et ne sont pas entièrement testés sur les clusters HDInsight. Des remarques sur l’utilisation de ces infrastructures avec HDInsight sont jointes à la fin de ce document.

###Définition des topologies Java et Clojure

Entre les deux méthodes de définition d’une topologie, Clojure constitue de loin l’approche la plus simple et la plus propre, puisque vous pouvez directement référencer les composants Python dans la définition de la topologie. Pour les définitions de topologie basées sur Java, vous devez également définir les composants Java qui traitent certaines opérations, telles que la déclaration des champs dans les tuples renvoyés à partir des composants Python.

Ce document décrit les deux méthodes et fournit quelques exemples de projets.

##Composants de Python avec une topologie Java

> [AZURE.NOTE] Cet exemple est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount), dans le répertoire __JavaTopology__. Il s’agit d’un projet Maven. Si vous n’êtes pas familiarisé avec Maven, consultez la page [Développement de topologies Java pour une application de base de comptage du nombre de mots avec Apache Storm et Maven sur HDInsight](hdinsight-storm-develop-java-topology.md) pour plus d’informations sur la création d’un projet Maven dans le cadre d’une topologie Storm.

Une topologie basée sur Java qui utilise Python (ou d’autres composants de langage JVM) semble utiliser initialement des composants Java. Mais en observant chacun des spouts/bolts Java, vous découvrirez un code semblable au suivant :

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Ce code marque le point où Java appelle Python et exécute le script qui contient la logique bolt réelle. Les spouts/bolts Java (dans cet exemple) déclarent simplement les champs dans le tuple qui sera émis par le composant sous-jacent de Python.

Dans cet exemple, les fichiers Python réels sont stockés dans le répertoire `/multilang/resources`. Le répertoire `/multilang` est référencé dans le fichier __pom.xml__ :

<resources> <resource> <!-- Where the Python bits are kept --> <directory>${basedir}/multilang</directory> </resource> </resources>

Il regroupe tous les fichiers du dossier `/multilang` dans le fichier jar qui sera créé à partir de ce projet.

> [AZURE.IMPORTANT] Notez que ceci spécifie uniquement le répertoire `/multilang` et non `/multilang/resources`. Storm attend des ressources non JVM dans un répertoire `resources` ; la recherche s’effectue donc déjà en interne. Le regroupement des composants dans ce dossier vous permet simplement d’effectuer des références par nom dans le code Java. Par exemple : `super("python", "countbolt.py");`. Autrement dit, Storm voit le répertoire `resources` à la racine (/) lorsqu’il accède aux ressources multi-langage.
>
> Pour cet exemple de projet, le module `storm.py` est inclus dans le répertoire `/multilang/resources`.

###Créer et exécuter le projet

Pour exécuter ce projet en local, utilisez la commande Maven suivante pour générer et exécuter en mode local :

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Utilisez les touches ctrl + c pour arrêter le processus.

Pour déployer le projet sur un cluster HDInsight exécutant Apache Storm, procédez comme suit :

1. Créez un fichier uber jar:

        mvn package

    Vous obtiendrez un fichier nommé __WordCount--1.0-SNAPSHOT.jar__ dans le répertoire `/target` de ce projet.

2. Téléchargez le fichier jar dans le cluster Hadoop en procédant de l’une des manières suivantes :

    * Pour les clusters HDInsight __basés sur Linux__ : utilisez `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` pour copier le fichier jar sur le cluster, en remplaçant USERNAME par votre nom d’utilisateur SSH et CLUSTERNAME par le nom du cluster HDInsight.

        Une fois le téléchargement du fichier terminé, connectez-vous au cluster à l’aide de SSH et démarrez la topologie à l’aide de `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Pour les clusters HDInsight __basés sur Windows__ : connectez-vous au tableau de bord Storm en ouvrant la page HTTPS://CLUSTERNAME.azurehdinsight.net/ dans votre navigateur. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight et indiquez le nom et le mot de passe d’administrateur lorsque vous y êtes invité.

        À l’aide du formulaire, exécutez les étapes suivantes :

        * __Fichier jar__ : cliquez sur __Parcourir__, puis sélectionnez le fichier __WordCount-1.0-SNAPSHOT.jar__
        * __Nom de la classe__ : entrez `com.microsoft.example.WordCount`
        * __Paramètres supplémentaires__ : entrez un nom convivial tel que `wordcount` pour identifier la topologie

        Enfin, sélectionnez __Soumettre__ pour démarrer la topologie.

> [AZURE.NOTE] Une fois démarrée, la topologie Storm s’exécute jusqu’à ce qu’elle soit arrêtée (supprimée). Pour arrêter la topologie, utilisez la commande `storm kill TOPOLOGYNAME` à partir de la ligne de commande (session SSH sur un cluster Linux, par exemple) ou à l’aide de l’interface utilisateur Storm, sélectionnez la topologie, puis cliquez sur __Supprimer__.

##Composants de Python avec une topologie Clojure

> [AZURE.NOTE] Cet exemple est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount), dans le répertoire __ClojureTopology__.

Cette topologie a été créée en utilisant [Leiningen](http://leiningen.org) pour [créer un nouveau projet Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Après quoi, les modifications suivantes ont été apportées au projet structuré :

* __project.clj__ : ajout de dépendances pour Storm et d’exclusions pour les éléments qui peuvent poser un problème lors de leur déploiement vers le serveur HDInsight.
* __ressources/ressources__ : Leiningen crée un répertoire `resources` par défaut. Cependant, les fichiers stockés à cet emplacement semblent ajoutés à la racine du fichier jar créé à partir de ce projet ; or Storm attend des fichiers dans un sous-répertoire nommé `resources`. Un sous-répertoire a donc été ajouté et les fichiers Python sont maintenant stockés dans `resources/resources`. Au moment de l’exécution, ce répertoire sera traité comme la racine (/) pour l’accès aux composants de Python.
* __src/wordcount/core.clj__ : ce fichier contient la définition de la topologie et est référencé à partir du fichier __project.clj__. Pour plus d’informations sur l’utilisation de Clojure pour la définition d’une topologie Storm, consultez la page [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###Créer et exécuter le projet

__Pour générer et exécuter le projet en local__, utilisez la commande suivante :

    lein do clean, run

Pour arrêter la topologie, utilisez les touches __Ctrl + C__.

__Pour générer un uberjar et effectuer le déploiement vers HDInsight__, procédez comme suit :

1. Créez un uberjar contenant la topologie et les dépendances requises :

        lein uberjar

    Créez un nouveau fichier nommé `wordcount-1.0-SNAPSHOT.jar` dans le répertoire `target\uberjar+uberjar`.
    
2. Utilisez l’une des méthodes suivantes pour déployer et exécuter la topologie sur un cluster HDInsight :

    * __HDInsight Linux__
    
        1. Copiez le fichier sur le nœud principal du cluster HDInsight à l’aide de `scp`. Par exemple :
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Remplacez USERNAME par un utilisateur SSH de votre cluster et CLUSTERNAME par le nom de votre cluster HDInsight.
            
        2. Une fois le fichier copié dans le cluster, utilisez le protocole SSH pour vous connecter au cluster et soumettre la tâche. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez l’un des articles suivants :
        
            * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Une fois connecté, procédez comme suit pour démarrer la topologie :
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight Windows__
    
        1. Connectez-vous au tableau de bord Storm en ouvrant la page HTTPS://CLUSTERNAME.azurehdinsight.net/ dans votre navigateur. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight et indiquez le nom et le mot de passe d’administrateur lorsque vous y êtes invité.

        2. À l’aide du formulaire, exécutez les étapes suivantes :

            * __Fichier jar__ : cliquez sur __Parcourir__, puis sélectionnez le fichier __wordcount-1.0-SNAPSHOT.jar__
            * __Nom de la classe__ : entrez `wordcount.core`
            * __Paramètres supplémentaires__ : entrez un nom convivial tel que `wordcount` pour identifier la topologie

            Enfin, sélectionnez __Soumettre__ pour démarrer la topologie.

> [AZURE.NOTE] Une fois démarrée, la topologie Storm s’exécute jusqu’à ce qu’elle soit arrêtée (supprimée). Pour arrêter la topologie, utilisez la commande `storm kill TOPOLOGYNAME` à partir de la ligne de commande (session SSH sur un cluster Linux) ou à l’aide de l’interface utilisateur Storm, sélectionnez la topologie, puis cliquez sur __Supprimer__.

##Framework Pyleus

[Pyleus](https://github.com/Yelp/pyleus) est un framework qui vise à simplifier l’utilisation de Python avec Storm en fournissant les éléments suivants :

* __Définitions de topologie basée sur YAML__ : permet de définir plus simplement la topologie, sans connaissance particulière de Java ou Clojure
* __Sérialiseur MessagePack__ : MessagePack est utilisé comme sérialisation par défaut, au lieu de JSON. Cela peut accélérer la communication entre les composants
* __Gestion des dépendances__ : Virtualenv est utilisé pour garantir que les dépendances de Python sont déployées sur tous les nœuds de travail. Cela suppose d’installer Virtualenv sur les nœuds de travail

> [AZURE.IMPORTANT] L’utilisation de Pyleus implique d’installer Storm dans votre environnement de développement. La distribution Apache Storm 0.9.3 de base semble générer des fichiers JAR incompatibles avec la version de Storm fournie avec HDInsight. Par conséquent, la procédure suivante suppose d’utiliser le cluster HDInsight comme environnement de développement.

Vous pouvez générer les exemples de topologies Pyleus en utilisant le nœud principal HDInsight comme environnement de génération :

1. Lorsque vous configurez une nouvelle instance Storm sur le cluster HDInsight, vous devez vous assurer que Python Virtualenv est présent sur les nœuds du cluster. Lorsque vous créez un nouveau cluster HDInsight basé sur Linux, utilisez les paramètres d’action de script suivants dans [Personnalisation du cluster](hdinsight-hadoop-customize-cluster.md) :

    * __Nom__ : indiquez simplement un nom convivial
    * \_\_ Script URI\_\_ : utilisez la valeur `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh`. Ce script installera Python Virtualenv sur les nœuds.
    
        > [AZURE.NOTE] Il créera également des répertoires qui seront utilisés par le framework Streamparse dans la suite de ce document.
        
    * __Nimbus__ : cochez cette entrée afin que le script soit appliqué aux nœuds (principaux) Nimbus.
    * __Superviseur__ : cochez cette entrée afin que le script soit appliqué aux nœuds superviseur (nœuds de travail).
    
    Laissez les autres champs vides.

1. Une fois le cluster créé, connectez-vous à l’aide de SSH :

    * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. À partir de la connexion SSH, procédez comme suit pour créer un environnement virtuel et installer Pyleus :

        virtualenv pyleus_venv
        source pyleus_venv
        pip install pyleus

3. Téléchargez ensuite le référentiel git Pyleus et générez l’exemple WordCount :

        sudo apt-get install git
        git clone https://github.com/Yelp/pyleus.git
        pyleus build pyleus/examples/word_count/pyleus_topology.yaml
    
    Une fois la génération terminée, vous obtiendrez un nouveau fichier nommé `word_count.jar` dans le répertoire actif.
    
4. Pour soumettre la topologie au cluster Storm, utilisez la commande suivante :

        pyleus submit -n localhost word_count.jar
    
    Le paramètre `-n` spécifie l’hôte Nimbus. Puisque nous nous trouvons sur le nœud principal, nous pouvons utiliser `localhost`.
    
    Vous pouvez également utiliser la commande `pyleus` pour exécuter d’autres actions Storm. Procédez comme suit pour énumérer les topologies en cours d’exécution, puis supprimez la topologie `word_count` :
    
        pyleus list -n localhost
        pyleus kill -n localhost word_count

##Framework Streamparse

[Streamwork](https://github.com/Parsely/streamparse) est un framework qui vise à simplifier l’utilisation de Python avec Storm en fournissant les éléments suivants :

* __Structure__ : permet de créer facilement la structure d’un projet, puis de modifier les fichiers pour ajouter votre logique
* __Fonctions Clojure DSL__ : réduisent le niveau de détail de l’utilisation des composants Python dans une définition de topologie Clojure
* __Gestion des dépendances__ : Virtualenv est utilisé pour garantir que les dépendances de Python sont déployées sur tous les nœuds de travail. Cela suppose d’installer Virtualenv sur les nœuds de travail
* __Déploiement distant__ : Streamparse peut utiliser l’automatisation SSH pour déployer des composants sur les nœuds de travail et créer un tunnel SSH pour communiquer avec Nimbus. Vous pouvez donc facilement effectuer votre déploiement sur un cluster Linux comme HDInsight à partir de votre environnement de développement.

> [AZURE.IMPORTANT] Streamparse s’appuie sur des composants qui attendent des [signaux Unix](https://en.wikipedia.org/wiki/Unix_signal), lesquels ne sont pas disponibles sous Windows. Vous devez utiliser un environnement de développement Linux, Unix ou OS X et un cluster HDInsight basé sur Linux.

1. Lorsque vous configurez une nouvelle instance Storm sur le cluster HDInsight, vous devez vous assurer que Python Virtualenv est présent sur les nœuds du cluster. Lorsque vous créez un nouveau cluster HDInsight basé sur Linux, utilisez les paramètres d’action de script suivants dans [Personnalisation du cluster](hdinsight-hadoop-customize-cluster.md) :

    * __Nom__ : indiquez simplement un nom convivial
    * \_\_ Script URI\_\_ : utilisez la valeur `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh`. Ce script permet d’installer Python Virtualenv sur les nœuds et de créer les répertoires qui seront utilisés par Streamparse.
    * __Nimbus__ : cochez cette entrée afin que le script soit appliqué aux nœuds (principaux) Nimbus.
    * __Superviseur__ : cochez cette entrée afin que le script soit appliqué aux nœuds superviseur (nœuds de travail).
    
    Laissez les autres champs vides.
    
    > [AZURE.WARNING] Vous devez également utiliser une __clé publique__ pour sécuriser l’utilisateur SSH pour votre cluster HDInsight afin de permettre le déploiement à distance à l’aide de Streamparse.
    >
    > Pour plus d’informations sur l’utilisation des clés SSH sur HDInsight, consultez l’un des articles suivants :
    >
    > * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Pendant l’approvisionnement du cluster, installez Streamparse dans votre environnement de développement à l’aide de la commande suivante :

        pip install streamparse
        
3. Une fois Streamparse installé, utilisez la commande suivante pour créer un exemple de projet :

        sparse quickstart wordcount
        
    Cette commande permettra de créer un répertoire nommé `wordcount` et d’y ajouter un exemple de projet WordCount.

4. Modifiez les répertoires dans le répertoire `wordcount` et démarrez la topologie en mode local :

        cd wordcount
        sparse run

    Pour arrêter la topologie, utilisez les touches Ctrl + C.

###Déployer la topologie

Après avoir créé votre cluster HDInsight basé sur Linux, procédez comme suit pour déployer la topologie dans le cluster :

1. Pour rechercher les noms de domaine complets des nœuds de travail pour votre cluster, utilisez la commande suivante :

        curl -u admin:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts | grep '"host_name" : "worker'
    
    Cette commande permet de récupérer les informations des hôtes du cluster, de les transférer dans grep et de renvoyer les entrées pour les nœuds de travail. Vous devriez obtenir un résultat semblable à ce qui suit :
    
        "host_name" : "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    
    Enregistrez les informations `"workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"`, car elles seront utilisées dans l’étape suivante.

2. Ouvrez le fichier __config.json__ dans le répertoire `wordcount` et modifiez les entrées suivantes :

    * __user__ : définissez cette propriété sur le nom du compte utilisateur SSH que vous avez configuré pour le cluster HDInsight. Cela vous permettra de vous authentifier sur le cluster lors du déploiement du projet
    * __nimbus__ : définissez cette propriété sur `CLUSTERNAME-ssh.azurehdinsight.net`. Remplacez CLUSTERNAME par le nom de votre cluster. Ce nom est utilisé lors de la communication avec le nœud Nimbus, qui correspond au nœud principal du cluster
    * __workers__ : renseigne l’entrée de travail par les noms d’hôte correspondant aux nœuds de travail que vous avez récupérés à l’aide de curl. Par exemple :
    
        ```
"workers": [
    "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net",
    "workernode1.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    ]
        ```
    
    * __virtualenv\_root__ : définissez cette valeur sur « /virtualenv »
    
    Cela configure le projet pour votre cluster HDInsight, notamment le répertoire `/virtualenv` qui a été créé lors de l’approvisionnement par l’action de script.

4. Puisque le déploiement de Streamparse sur HDInsight suppose de transférer votre authentification aux travailleurs via le nœud principal, vous devez démarrer `ssh-agent` sur votre station de travail. Pour la plupart des systèmes d’exploitation, cet agent est démarré automatiquement. Exécutez la commande suivante pour vérifier qu’il fonctionne correctement :

        echo "$SSH_AUTH_SOCK"
    
    Si `ssh-agent` est opérationnel, vous devriez recevoir une réponse ayant l’aspect suivant :
    
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
    
    > [AZURE.NOTE] Le chemin d’accès complet peut varier selon votre système d’exploitation. Sur OS X, par exemple, le chemin d’accès peut être du type `/private/tmp/com.apple.launchd.vq2rfuxaso/Listeners`. Quoi qu’il en soit, la commande doit renvoyer un chemin d’accès si l’agent s’exécute normalement.
    
    Si rien n’est renvoyé, utilisez la commande `ssh-agent` pour démarrer l’agent.
    
5. Vérifiez que l’agent connaît la clé utilisée pour s’authentifier auprès du serveur HDInsight. Utilisez la commande suivante pour répertorier les clés qui sont disponibles pour l’agent :

        ssh-add -L
    
    Cette commande renvoie les clés privées qui ont été ajoutées à l’agent. Vous pouvez comparer les résultats au contenu de la clé privée que vous avez générée lors de la création d’une clé SSH pour vous authentifier sur HDInsight.
    
    Si aucune information n’est renvoyée, ou si les informations renvoyées ne correspondent pas à votre clé privée, procédez comme suit pour ajouter la clé privée à l’agent :
    
        ssh-add /path/to/key/file
    
    Par exemple, `ssh-add ~/.ssh/id_rsa`.

4. Vous devez également configurer SSH pour qu’il sache qu’un transfert doit être utilisé pour votre cluster HDInsight. Ajoutez le bloc de code suivant à `~/.ssh/config` : Si ce fichier n’existe pas, créez-le et utilisez le code suivant comme contenu :

        Host *.azurehdinsight.net
          ForwardAgent yes
        
        Host *.internal.cloudapp.net
          ProxyCommand ssh CLUSTERNAME-ssh.azurehdinsight.net nc %h %p
    
    Remplacez CLUSTERNAME par le nom de votre cluster HDInsight.
    
    L’agent SSH est alors configuré sur votre station de travail pour permettre la transmission de vos informations d’identification SSH via le système *.azurehdinsight.net auquel vous vous connectez. Dans ce cas, il s’agit du nœud principal de votre cluster. Ensuite, il configure la commande utilisée pour le trafic SSH de proxy entre le nœud principal et les nœuds de travail individuels (internal.cloudapp.net). Cela permet à Streamparse de se connecter au nœud principal, puis à chacun des nœuds de travail, en utilisant l’authentification par clé utilisée pour votre compte SSH.
    
5. Pour finir, utilisez la commande suivante pour soumettre la topologie de votre environnement de développement local au cluster HDInsight :

        sparse submit
    
    Cette commande permet d’établir la connexion au cluster HDInsight, de déployer la topologie et les dépendances de Python, puis de démarrer la topologie.

##Étapes suivantes

Dans ce document, vous avez appris à utiliser les composants de Python à partir d’une topologie Storm. Consultez les documents suivants pour découvrir d’autres façons de travailler avec HDInsight :

* [Développement de programmes de diffusion en continu Python pour HDInsight](hdinsight-hadoop-streaming-python.md)
* [Utilisation de Python avec Hive et Pig dans HDInsight](hdinsight-python.md)

<!---HONumber=AcomDC_0204_2016-->