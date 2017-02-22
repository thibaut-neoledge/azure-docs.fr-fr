---
title: "Utilisation de clés SSH avec un cluster Hadoop basé sur Linux, Unix ou OS X | Microsoft Docs"
description: " Vous pouvez accéder à un cluster HDInsight à l’aide de SSH (Secure Shell). Ce document donne des informations sur l’utilisation de SSH avec HDInsight à partir des clients Windows, Linux, Unix ou OS X.."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 37409ad3f50cdd4a7a384c96a57a35ef8c83fb8f


---
# <a name="use-ssh-with-hdinsight-hadoop-from-windows-linux-unix-or-os-x"></a>Utilisation de clés SSH avec un cluster Hadoop basé sur Linux, Unix ou OS X

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH (Windows, Linux, Unix, OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) vous permet de vous connecter à un cluster HDInsight sous Linux et d’exécuter des commandes à l’aide d’une interface de ligne de commande. Ce document fournit des informations de base sur SSH et des informations spécifiques sur l’utilisation de SSH avec HDInsight.

## <a name="what-is-ssh"></a>Qu'est-ce que SSH ?

SSH est un protocole réseau de chiffrement qui vous permet de communiquer de manière sécurisée avec un serveur distant sur un réseau non sécurisé. SSH est utilisé pour fournir une connexion sécurisée de ligne de commande à un serveur distant. Dans ce cas, les nœuds principaux ou le nœud de périphérie d’un cluster HDInsight.

Vous pouvez également utiliser SSH pour « tunneler » le trafic réseau de votre client vers le cluster HDInsight. L’utilisation d’un tunnel vous permet d’accéder aux services sur le cluster HDInsight qui ne sont pas exposés directement à Internet. Pour plus d’informations sur l’utilisation du tunneling SSH avec HDInsight, voir [Utilisation de SSH tunneling avec HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ssh-clients"></a>Clients SSH

De nombreux systèmes d’exploitation fournissent des fonctionnalités de client SSH via les utilitaires de ligne de commande `ssh` et `scp`.

* __SSH__ : un client SSH général qui peut être utilisé pour établir une session de ligne de commande à distance et créer des tunnels.
* __SCP__ : un utilitaire qui copie des fichiers entre les systèmes locaux et distants à l’aide du protocole SSH.

Historiquement, Windows n’a jamais fourni de client SSH jusqu’à l’Édition anniversaire de Windows 10. Cette version de Windows inclut l’interpréteur de commandes sur la fonctionnalité de Windows 10 pour les développeurs, qui fournit `ssh`, `scp` et d’autres commandes Linux. Pour plus d’informations sur l’interpréteur de commandes pour Windows 10, consultez la page [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about) (Interpréteur de commandes sous Ubuntu et Windows).

Si vous utilisez Windows et que vous n’avez pas accès à un interpréteur de commandes Windows 10, nous vous recommandons les clients SSH suivants :

* [GIT pour Windows](https://git-for-windows.github.io/):  fournit les utilitaires de ligne de commande `ssh` et `scp`.
* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) : fournit un client SSH graphique.
* [MobaXterm](http://mobaxterm.mobatek.net/) : fournit un client SSH graphique.
* [Cygwin](https://cygwin.com/) : fournit les utilitaires de ligne de commande `ssh` et `scp`.

> [!NOTE]
> Les étapes décrites dans ce document partent du principe que vous avez accès à la commande `ssh`. Si vous utilisez un client tel que puTTY ou MobaXterm, consultez la documentation de ce produit pour les paramètres et la commande équivalents.

## <a name="ssh-authentication"></a>Authentification SSH

Une connexion SSH peut être authentifiée à l’aide d’un mot de passe ou [d’un chiffrement à clé publique (https://fr.wikipedia.org/wiki/Cryptographie_asym%C3%A9trique)](https://en.wikipedia.org/wiki/Public-key_cryptography). L’utilisation d’une clé est l’option la plus sécurisée, car elle n’est pas vulnérable à la plupart des attaques visant les mots de passe. Toutefois, la création et la gestion de clés sont plus complexes que l’utilisation d’un mot de passe.

L’utilisation du chiffrement à clé publique implique la création d’une paire de clés _publique_ et _privée_.

* La **clé publique** est chargée sur les nœuds de votre cluster HDInsight, ou tout autre service que vous souhaitez utiliser avec le chiffrement à clé publique.

* La **clé privée** correspond à ce que vous présentez au cluster HDInsight pour vous connecter à l’aide d’un client SSH, afin de vérifier votre identité. Protégez cette clé privée. Ne la partagez pas.

    Vous pouvez ajouter une sécurité supplémentaire en créant une phrase secrète pour la clé privée. Vous devez fournir cette phrase secrète avant de pouvoir utiliser la clé.

### <a name="create-a-public-and-private-key"></a>Créer une clé publique et une clé privée

L’utilitaire `ssh-keygen` est le moyen le plus simple pour créer une paire de clés publique et privée pour une utilisation avec HDInsight. À partir d’une ligne de commande, utilisez la commande suivante pour créer une nouvelle paire de clés à utiliser avec HDInsight :

> [!NOTE]
> Si vous utilisez un client SSH GUI, comme MobaXTerm ou puTTY, consultez la documentation de votre client sur la façon de générer des clés.

    ssh-keygen -t rsa -b 2048

Les informations suivantes vous sont demandées :

* L’emplacement du fichier : par défaut dans `~/.ssh/id_rsa`.

* Une phrase secrète facultative : si vous indiquez une phrase secrète, vous devez l’entrer à nouveau lors de l’authentification auprès de votre cluster HDInsight.

> [!IMPORTANT]
> La phrase secrète est un mot de passe pour la clé privée. Chaque fois que vous utilisez la clé privée pour l’authentification, vous devez fournir la phrase secrète avant de pouvoir utiliser la clé. Si quelqu'un obtient votre clé privée, il ne pourra pas l’utiliser sans la phrase secrète.
>
> Si vous oubliez cette phrase secrète, il sera impossible de la réinitialiser ou de la récupérer.

Une fois la commande terminée, vous aurez deux nouveaux fichiers :

* __id\_rsa__ : ce fichier contient la clé privée.

    > [!WARNING]
    > Vous devez limiter l’accès à ce fichier pour empêcher tout accès non autorisé aux services sécurisés par la clé publique.

* __id\_rsa.pub__ : ce fichier contient la clé publique. Vous aurez besoin de ce fichier lors de la création d’un cluster HDInsight.

    > [!NOTE]
    > Peu importe qui a accès à la clé _publique_. La clé publique en elle-même ne sert qu’à vérifier la clé privée. Les services tels que le serveur SSH utilisent la clé publique pour vérifier votre identité lorsque vous vous authentifiez à l’aide de la clé privée.

## <a name="configure-ssh-on-hdinsight"></a>Configurer SSH sur HDInsight

Lorsque vous créez un cluster HDInsight sous Linux, vous devez fournir un _nom d’utilisateur SSH_ et un _mot de passe_ ou une _clé publique_. Lors de la création du cluster, ces informations sont utilisées pour créer une connexion sur les nœuds du cluster HDInsight. Le mot de passe ou la clé publique sont utilisés pour sécuriser le compte d’utilisateur.

Pour plus d’informations sur la configuration SSH lors de la création du cluster, consultez les documents suivants :

* [Créer un cluster HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Créer un cluster HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
* [Créer un cluster HDInsight à l’aide d’Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Créer un cluster HDInsight à l’aide de modèles Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* [Créer un cluster HDInsight à l’aide du kit de développement logiciel (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Créer un cluster HDInsight à l’aide de REST](hdinsight-hadoop-create-linux-clusters-curl-rest.md)

### <a name="additional-ssh-users"></a>Utilisateurs SSH supplémentaires

Même si les utilisateurs SSH supplémentaires peuvent être ajoutés au cluster après sa création, cette opération n’est pas recommandée.

* Vous devez ajouter manuellement de nouveaux utilisateurs SSH à chaque nœud du cluster.

* Les nouveaux utilisateurs SSH ont le même accès à HDInsight que l’utilisateur par défaut. Il n’existe aucun moyen de limiter l’accès aux données ou aux tâches dans HDInsight en fonction du compte d’utilisateur SSH.

Pour limiter l’accès par utilisateur, vous devez utiliser un cluster HDInsight joint à un domaine. Un cluster HDInsight joint à un domaine utilise Active Directory pour contrôler l’accès aux ressources de cluster.

L’utilisation d’un cluster HDInsight joint à un domaine vous permet de vous authentifier à l’aide d’Active Directory après vous être connecté avec SSH. Plusieurs utilisateurs peuvent se connecter à l’aide de SSH, puis s’authentifier à leur compte Active Directory après la connexion. Consultez la section [HDInsight joint à un domaine](#domainjoined) pour plus d’informations.

##<a name="a-idconnecta-connect-to-hdinsight"></a><a id="connect"></a> Connexion à HDInsight

Même si tous les nœuds dans un cluster HDInsight sont exécutés sur le serveur SSH, vous ne pouvez vous connecter qu’aux nœuds principaux ou de périphérie via le réseau Internet public.

* Pour vous connecter aux _nœuds principaux_, utilisez `CLUSTERNAME-ssh.azurehdinsight.net`, où __CLUSTERNAME__ est le nom du cluster HDInsight. La connexion sur le port 22 (la valeur par défaut pour SSH) vous permet de vous connecter au nœud principal. Le port 23 se connecte au nœud principal secondaire.

* Pour vous connecter à un _nœud de périphérie_, utilisez `EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net`, où __EDGENAME__ est le nom du nœud de périphérie et __CLUSTERNAME__ est le nom du cluster HDInsight. Utilisez le port 22 lors de la connexion au nœud de périphérie.

Les exemples suivants montrent comment se connecter aux nœuds principaux et de périphérie d’un cluster nommé __myhdi__ à l’aide du nom d’utilisateur SSH __sshuser__. Le nœud de périphérie est nommé __myedge__.

| Action à réaliser... | Élément à utiliser... |
| ----- | ----- |
| Connexion au nœud principal | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| Connexion au nœud principal secondaire | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| Connexion au nœud de périmètre | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

Si vous utilisez un mot de passe pour sécuriser votre compte SSH, vous êtes invité à le saisir.

Si vous utilisez une clé publique pour sécuriser le compte SSH, vous devrez peut-être spécifier le chemin d’accès à la clé privée correspondante à l’aide du commutateur `-i`. L’exemple suivant illustre l’utilisation du commutateur `-i` :

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>Connexion à d’autres nœuds

Les nœuds de travail et Zookeeper ne sont pas directement accessibles depuis l’extérieur du cluster, mais ils sont accessibles à partir des nœuds principaux ou de périphérie du cluster. Pour y parvenir, procédez comme suit :

1. Utilisez SSH pour vous connecter au nœud principal ou de périphérie :

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. À partir de la connexion SSH vers le nœud principal ou de périphérie, utilisez la commande `ssh` pour vous connecter à un nœud de travail dans le cluster :

        ssh sshuser@wn0-myhdi

    Pour récupérer la liste de nœuds de travail dans le cluster, consultez l’exemple indiquant comment récupérer le nom de domaine complet des nœuds de cluster dans le document [Gérer des clusters HDInsight à l'aide de l’API REST d’Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Si le compte SSH est sécurisé à l’aide d’un mot de passe, vous êtes invité à le saisir et la connexion est établie.

Si vous utilisez une clé SSH pour authentifier votre compte d’utilisateur, vous devez vous assurer que votre environnement local est configuré pour le transfert de l’agent SSH.

> [!IMPORTANT]
> Les étapes suivantes partent du principe que vous disposez d’un système basé sur Linux/UNIX et que vous utilisez l’interpréteur de commandes sous Windows 10. Si ces étapes ne fonctionnent pas pour votre système, vous devrez peut-être consulter la documentation de votre client SSH.

1. Avec un éditeur de texte, ouvrez `~/.ssh/config`. Si ce fichier n’existe pas, créez-le en entrant `touch ~/.ssh/config` dans la ligne de commande.

2. Ajoutez le code suivant au fichier. Remplacez *CLUSTERNAME* par le nom de votre cluster HDInsight.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Cette entrée permet de configurer le transfert de l’agent SSH pour votre cluster HDInsight.

3. Testez le transfert de l’agent SSH à l’aide de la commande suivante à partir du terminal :

        echo "$SSH_AUTH_SOCK"

    Cette commande renvoie des informations semblables au texte suivant :

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Si aucun élément n’est renvoyé, cela indique que `ssh-agent` n’est pas en cours d’exécution. Reportez-vous aux informations de scripts de démarrage de l’agent dans [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Utilisation de ssh agent avec SSH) ou consultez la documentation de votre client SSH pour connaître les étapes spécifiques concernant l’installation et la configuration de `ssh-agent`.

4. Après avoir vérifié que **ssh agent** est en cours d’exécution, utilisez la commande suivante pour ajouter votre clé privée SSH à l’agent :

        ssh-add ~/.ssh/id_rsa

    Si votre clé privée est stockée dans un fichier différent, remplacez `~/.ssh/id_rsa` par le chemin d’accès au fichier.

<a id="domainjoined"></a>
### <a name="domain-joined-hdinsight"></a>HDInsight joint à un domaine

[HDInsight joint à un domaine](hdinsight-domain-joined-introduction.md) intègre Kerberos avec Hadoop dans HDInsight. Étant donné que l’utilisateur SSH n’est pas un utilisateur de domaine Active Directory, vous ne pouvez pas exécuter des commandes Hadoop avant de vous authentifier auprès d’Active Directory. Utilisez les étapes suivantes pour authentifier votre session SSH auprès d’Active Directory :

1. Connectez-vous à un cluster HDInsight joint à un domaine à l’aide de SSH, comme indiqué dans la section [Connexion à HDInsight](#connect). Par exemple, la commande suivante se connecte à un cluster HDInsight nommé __myhdi__ à l’aide du nom de compte SSH __sshuser__.

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Pour vous authentifier à l’aide d’un utilisateur de domaine et d’un mot de passe, utilisez les éléments suivants :

        kinit

     Lorsque vous y êtes invité, entrez un nom d’utilisateur de domaine et le mot de passe pour l’utilisateur de domaine.

    Pour plus d’informations sur la configuration d’utilisateurs de domaine pour les clusters HDInsight joints à un domaine, consultez [Configuration de clusters HDInisight joints à un domaine](hdinsight-domain-joined-configure.md).

Une fois l’authentification effectuée à l’aide de la commande `kinit`, vous pouvez utiliser les commandes Hadoop telles que `hdfs dfs -ls /` ou `hive`.

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>Tunneling SSH

Vous pouvez utiliser SSH pour transférer des requêtes locales, telles que des demandes web, vers le cluster HDInsight. La requête sera ensuite acheminée vers la ressource demandée comme si elle provenait du nœud principal du cluster HDInsight.

> [!IMPORTANT]
> Un tunnel SSH est requis pour accéder à l’interface utilisateur web de certains services Hadoop. Par exemple, l'interface utilisateur de l'historique des travaux ou de l'interface utilisateur du Gestionnaire de ressources est uniquement accessible à l'aide d'un tunnel SSH.

Pour plus d’informations sur la création et l’utilisation d’un tunnel SSH, consultez [Utiliser le tunneling SSH pour accéder à l’interface utilisateur web Ambari, JobHistory, NameNode, Oozie et d’autres interfaces utilisateur web](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment vous authentifier avec une clé SSH, apprenez à utiliser MapReduce avec Hadoop dans HDInsight.

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Feb17_HO3-->


