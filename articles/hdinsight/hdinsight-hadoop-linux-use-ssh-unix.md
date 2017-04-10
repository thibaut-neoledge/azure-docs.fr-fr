---
title: "Utilisation de clés SSH avec un cluster Hadoop basé sur Linux, Unix ou OS X | Microsoft Docs"
description: " Vous pouvez accéder à un cluster HDInsight à l’aide de SSH (Secure Shell). Ce document donne des informations sur l’utilisation de SSH pour se connecter à HDInsight à partir des clients Windows, Linux, Unix ou OS X."
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
ms.date: 04/03/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 248e820ccd2c68a8500aab3233c5beea3c8cc868
ms.lasthandoff: 04/04/2017

---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>Se connecter à HDInsight (Hadoop) à l’aide de SSH

Découvrez comment utiliser [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) pour vous connecter en toute sécurité à HDInsight. HDInsight peut utiliser Linux (Ubuntu) comme système d’exploitation pour les nœuds du cluster. SSH peut être utilisé pour se connecter aux nœuds principaux et de périmètre d’un cluster Linux et pour exécuter des commandes directement sur ces nœuds.

Le tableau suivant contient les informations d’adresse et de port nécessaires lors de la connexion à HDInsight à l’aide de SSH :

| Adresse | Port | Se connecte au... |
| ----- | ----- | ----- |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Nœud de périmètre (R Server sur HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Nœud de périmètre (n’importe quel autre type de cluster, si un nœud de périmètre existe) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Nœud principal primaire |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Nœud principal secondaire |

> [!NOTE]
> Remplacez `<edgenodename>` par le nom du nœud de périmètre.
>
> Remplacez `<clustername>` par le nom de votre cluster HDInsight :
>
> Nous vous recommandons de __toujours vous connecter au nœud de périmètre__ si vous en avez un. Les nœuds principaux hébergent des services qui sont essentiels au bon fonctionnement du cluster. Le nœud de périmètre exécute uniquement ce que vous placez dans celui-ci.
>
> Pour plus d’informations sur l’utilisation des nœuds de périmètre, consultez [Utiliser des nœuds de périmètre vides dans HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node).

## <a name="ssh-clients"></a>Clients SSH

La plupart des systèmes d’exploitation fournissent le client `ssh`. Microsoft Windows ne fournit pas de client SSH par défaut. Un client SSH pour Windows est disponible dans chacun des packages suivants :

* [Interpréteur de commande sur Ubuntu pour Windows 10](https://msdn.microsoft.com/commandline/wsl/about) : la commande `ssh` est fournie par l’interpréteur de commande sur la ligne de commande Windows.

* [Git (https://git-scm.com/)](https://git-scm.com/) : la commande `ssh` est fournie par la ligne de commande GitBash.

* [GitHub Desktop (https://desktop.github.com/)](https://desktop.github.com/) : la commande `ssh` est fournie par la ligne de commande Git Shell. GitHub Desktop peut être configuré pour utiliser un interpréteur de commande, l’invite de commandes Windows ou la ligne de commande PowerShell en tant que ligne de commande pour Git Shell.

* [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH) : l’équipe PowerShell effectue le portage d’OpenSSH vers Windows et fournit des versions de test.

    > [!WARNING]
    > Le package OpenSSH inclut le composant de serveur SSH, `sshd`. Ce composant démarre un serveur SSH sur votre système, permettant ainsi à d’autres personnes de s’y connecter. Ne configurez pas ce composant et n’ouvrez pas le port 22, sauf si vous souhaitez héberger un serveur SSH sur votre système. Il n’est pas nécessaire de communiquer avec HDInsight.

Il existe également plusieurs clients SSH graphiques, tels que [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) et [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/). Alors que ces clients peuvent être utilisés pour se connecter à HDInsight, le processus de connexion à un serveur est différent de celui utilisant l’utilitaire `ssh`. Pour plus d’informations, consultez la documentation du client graphique que vous utilisez.

## <a id="sshkey"></a>Authentification : clés SSH

Les clés SSH utilisent le [chiffrement à clé publique](https://en.wikipedia.org/wiki/Public-key_cryptography) pour sécuriser le cluster. Les clés SSH sont plus sûres que les mots de passe et fournissent un moyen simple pour sécuriser votre cluster HDInsight.

Si votre compte SSH est sécurisé à l’aide d’une clé, le client doit fournir la clé privée correspondante lorsque vous vous connectez :

* La plupart des clients peuvent être configurés pour utiliser une __clé par défaut__. Par exemple, le client `ssh` recherche une clé privée à l’emplacement `~/.ssh/id_rsa` sur des environnements Linux et Unix.

* Vous pouvez spécifier le __chemin d’accès à une clé privée__. Avec le client `ssh`, le paramètre `-i` est utilisé pour spécifier le chemin d’accès à la clé privée. Par exemple, `ssh -i ~/.ssh/hdinsight sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Si vous avez __plusieurs clés privées__ à utiliser avec différents serveurs, des utilitaires tels que [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) peuvent servir à sélectionner automatiquement la clé à utiliser.

> [!IMPORTANT]
>
> Si vous sécurisez votre clé privée avec une phrase secrète, vous devez entrer la phrase secrète lors de l’utilisation de la clé. Les utilitaires tels que `ssh-agent` peuvent mettre en cache le mot de passe pour plus de commodité.

### <a name="create-an-ssh-key-pair"></a>Création d’une paire de clés SSH

Utilisez la commande `ssh-keygen` pour créer des fichiers de clés publiques et privées. La commande suivante génère une paire de clés RSA 2 048 bits qui peut être utilisée avec HDInsight :

    ssh-keygen -t rsa -b 2048

Vous êtes invité à renseigner diverses informations pendant le processus de création de la clé. Par exemple, l’emplacement dans lequel sont stockées les clés ou vos préférences en termes d’utilisation de phrase secrète. Une fois le processus terminé, deux fichiers sont créés ; une clé publique et une clé privée.

* La __clé publique__ est utilisée pour créer un cluster HDInsight. La clé publique est dotée d’une extension de `.pub`.

* La __clé privée__ est utilisée pour authentifier votre client au cluster HDInsight.

> [!IMPORTANT]
> Vous pouvez sécuriser vos clés à l’aide d’une phrase secrète. Il s’agit effectivement d’un mot de passe sur votre clé privée. Même si quelqu’un obtient votre clé privée, il doit avoir la phrase secrète pour utiliser la clé.

### <a name="create-hdinsight-using-the-public-key"></a>Créer un cluster HDInsight à l’aide de la clé publique

| Méthode de création | Utilisation de la clé publique |
| ------- | ------- |
| **Portail Azure** | Décochez l’option __Use same password as cluster login__ (Utiliser le même mot de passe que pour la connexion au cluster), puis sélectionnez __Clé publique__ comme type d’authentification SSH. Enfin, sélectionnez le fichier de clé publique ou collez le contenu texte du fichier dans le champ __SSH public key__ (Clé publique SSH).</br>![Boîte de dialogue de clé publique SSH lors de la création du cluster HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | Utilisez le paramètre `-SshPublicKey` de l’applet de commande `New-AzureRmHdinsightCluster` et transmettez le contenu de la clé publique sous forme de chaîne.|
| **Azure CLI 1.0** | Utilisez le paramètre `--sshPublicKey` de la commande `azure hdinsight cluster create` et transmettez le contenu de la clé publique sous forme de chaîne. |
| **Modèle Resource Manager** | Pour obtenir un exemple d’utilisation des clés SSH avec un modèle, consultez [Deploy HDInsight on Linux (w/ Azure Storage, SSH key)](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) (Déployer HDInsight sur Linux (avec Stockage Azure, clé SSH). L’élément `publicKeys` dans le fichier [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) est utilisé pour transmettre les clés à Azure lors de la création du cluster. |

## <a id="sshpassword"></a>Authentification : mot de passe

Les comptes SSH peuvent être sécurisés à l’aide d’un mot de passe. Lorsque vous vous connectez à HDInsight à l’aide de SSH, vous êtes invité à entrer le mot de passe.

> [!WARNING]
> Il est déconseillé d’utiliser l’authentification par mot de passe pour SSH. Les mots de passe peuvent être devinés et sont vulnérables aux attaques en force brute. Nous vous recommandons plutôt d’utiliser des [clés SSH pour l’authentification](#sshkey).

### <a name="create-hdinsight-using-a-password"></a>Créer un cluster HDInsight à l’aide d’un mot de passe

| Méthode de création | Spécification du mot de passe |
| --------------- | ---------------- |
| **Portail Azure** | Par défaut, le compte utilisateur SSH a le même mot de passe que le compte de connexion du cluster. Pour utiliser un autre mot de passe, décochez l’option __Use same password as cluster login__ (Utiliser le même mot de passe que pour la connexion au cluster), puis entrez le mot de passe dans le champ __SSH password__ (Mot de passe SSH).</br>![Boîte de dialogue de mot de passe SSH lors de la création du cluster HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | Utilisez le paramètre `--SshCredential` de l’applet de commande `New-AzureRmHdinsightCluster` et transmettez un objet `PSCredential` qui contient le nom de compte et mot de passe utilisateur SSH. |
| **Azure CLI 1.0** | Utilisez le paramètre `--sshPassword` de la commande `azure hdinsight cluster create` et fournissez la valeur de mot de passe. |
| **Modèle Resource Manager** | Pour obtenir un exemple d’utilisation d’un mot de passe avec un modèle, consultez [Deploy HDInsight cluster with Storage and SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) (Déployer un cluster HDInsight avec Stockage Azure et un mot de passe SSH). L’élément `linuxOperatingSystemProfile` dans le fichier [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) est utilisé pour transmettre le nom de compte et mot de passe SSH à Azure lors de la création du cluster.|

### <a name="change-the-ssh-password"></a>Modifier le mot de passe SSH

Pour plus d’informations sur la modification du mot de passe du compte utilisateur SSH, consultez la section __Modifier les mots de passe__ du document [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-portal-linux.md#change-passwords).

## <a id="domainjoined"></a>Authentification : HDInsight joint à un domaine

Si vous utilisez un __cluster HDInsight joint à un domaine__, vous devez utiliser la commande `kinit` après vous être connecté via SSH. Cette commande vous invite à renseigner un utilisateur de domaine et un mot de passe, et authentifie votre session auprès du domaine Active Directory Azure associé au cluster.

Pour plus d’informations, consultez la section [Configurer des clusters HDInsight joints à un domaine (version préliminaire)](hdinsight-domain-joined-configure.md).

## <a name="connect-to-worker-and-zookeeper-nodes"></a>Se connecter à des nœuds Zookeeper et Worker

Les nœuds Zookeeper et Worker ne sont pas directement accessibles à partir d’Internet, mais ils sont accessibles à partir des nœuds principaux ou de périmètre du cluster. Pour vous connecter a d’autres nœuds, procédez comme suit :

1. Utilisez SSH pour vous connecter au nœud principal ou de périphérie :

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. À partir de la connexion SSH vers le nœud principal ou de périphérie, utilisez la commande `ssh` pour vous connecter à un nœud de travail dans le cluster :

        ssh sshuser@wn0-myhdi

    Pour récupérer la liste des noms de domaine des nœuds dans le cluster, consultez les exemples du document [Gérer des clusters HDInsight à l’aide de l’API REST d’Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Si le compte SSH est sécurisé à l’aide d’un __mot de passe__, vous êtes invité à le saisir et la connexion est établie.

Si le compte SSH est sécurisé à l’aide de __clés SSH__, vous devez vous assurer que votre environnement local est configuré pour le transfert de l’agent SSH.

> [!NOTE]
> Une autre méthode permettant d’accéder directement à tous les nœuds du cluster consiste à installer HDInsight dans un réseau virtuel Azure. Ensuite, vous pouvez joindre votre machine à distance au même réseau virtuel et accéder directement à tous les nœuds du cluster.
>
> Pour plus d’informations, consultez [Extension des capacités de HDInsight à l’aide d’Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

### <a name="configure-ssh-agent-forwarding"></a>Configurer le transfert de l’agent SSH

> [!IMPORTANT]
> Les étapes suivantes partent du principe que vous disposez d’un système basé sur Linux/UNIX et que vous utilisez l’interpréteur de commandes sous Windows 10. Si ces étapes ne fonctionnent pas pour votre système, vous devrez peut-être consulter la documentation de votre client SSH.

1. Avec un éditeur de texte, ouvrez `~/.ssh/config`. Si ce fichier n’existe pas, créez-le en entrant `touch ~/.ssh/config` dans la ligne de commande.

2. Ajoutez le texte suivant au fichier `config`.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Remplacez les informations __Hôte__ avec l’adresse du nœud que vous connectez à l’aide de SSH. L’exemple précédent utilise le nœud de périmètre. Cette entrée permet de configurer le transfert de l’agent SSH pour le nœud spécifié.

3. Testez le transfert de l’agent SSH à l’aide de la commande suivante à partir du terminal :

        echo "$SSH_AUTH_SOCK"

    Cette commande renvoie des informations semblables au texte suivant :

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Si aucun élément n’est renvoyé, alors `ssh-agent` n’est pas en cours d’exécution. Reportez-vous aux informations de scripts de démarrage de l’agent dans [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Utilisation de ssh agent avec SSH) ou consultez la documentation de votre client SSH pour connaître les étapes spécifiques concernant l’installation et la configuration de `ssh-agent`.

4. Après avoir vérifié que **ssh agent** est en cours d’exécution, utilisez la commande suivante pour ajouter votre clé privée SSH à l’agent :

        ssh-add ~/.ssh/id_rsa

    Si votre clé privée est stockée dans un fichier différent, remplacez `~/.ssh/id_rsa` par le chemin d’accès au fichier.

5. Connectez-vous aux nœuds principaux ou aux nœuds de périmètre du cluster à l’aide de SSH. Utilisez ensuite la commande SSH pour vous connecter à un nœud Worker ou Zookeeper. La connexion est établie à l’aide de la clé transmise.

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de SSH Tunneling pour accéder à l’interface Web Ambari, JobHistory, NameNode, Oozie et d’autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md)
* [Extension des capacités de HDInsight à l’aide d’Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md)
* [Utiliser des nœuds de périmètre vides dans HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
