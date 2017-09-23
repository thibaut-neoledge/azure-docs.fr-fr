---
title: Installer RStudio avec R Server sur HDInsight - Azure | Microsoft Docs
description: Comment installer RStudio avec R Server sur HDInsight.
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 416420d855505508735ebd8526e93efdb230ad53
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>Installation de RStudio avec R Server sur HDInsight

Cet article explique comment installer la version (gratuite) communautaire de [RStudio Server](https://www.rstudio.com/products/rstudio-server/) sur le nœud périphérique d’un cluster à l’aide d’un script personnalisé. RStudio Server fournit un IDE sur navigateur utilisable par des clients distants ; il est très répandu sous Linux. Il existe actuellement plusieurs environnements de développement intégré (IDE) disponibles pour R, notamment :

- [Outils R pour Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS) de Microsoft ; 
- [RStudio Server](https://www.rstudio.com/products/rstudio-server/) ; 
- [StatET](http://www.walware.de/goto/statet) sur Eclipse de WalWare.

L’installation de RStudio Server sur le nœud périphérique d’un cluster HDInsight présente l’avantage d’offrir une expérience d’IDE complète pour le développement et l’exécution de scripts R avec R Server sur le cluster. Cette configuration peut être considérablement plus productive que l’utilisation par défaut de la console R.

> [!NOTE]
> La procédure décrite dans cet article s'applique uniquement si vous n’avez pas choisi d'installer RStudio Server Community Edition lors de la configuration de votre cluster. Si vous l’avez ajouté lors de l’approvisionnement, vous pouvez y accéder en cliquant sur la mosaïque **Tableaux de bord R Server** dans l’entrée de votre cluster sur le Portail Azure, puis en cliquant sur la mosaïque **R Studio Server**. 

Si vous préférez utiliser la version commerciale Pro sous licence de RStudio Server, vous devez suivre les instructions d’installation de [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [!NOTE]
> Si vous utilisez un cluster HDInsight pour lequel R Server a été installé à l’aide de l’option [Installer une action de script R](hdinsight-hadoop-r-scripts-linux.md), la procédure décrite dans ce document ne fonctionnera pas correctement, car il faut qu’un R Server se trouve sur le cluster HDInsight.
>
> 

## <a name="prerequisites"></a>Composants requis

* Un cluster Azure HDInsight avec R Server installé. Pour obtenir des instructions, consultez la page [Prise en main de R Server sur les clusters HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Un client SSH. Pour les distributions Linux et Unix ou pour Macintosh OS X, la commande `ssh` est fournie avec le système d'exploitation. Pour Windows, nous vous recommandons [Cygwin](http://www.redhat.com/services/custom/cygwin/) avec [l’option OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU) ou [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Installation de RStudio sur le cluster à l’aide d’un script personnalisé

Voici la procédure à suivre :

1. Identifiez le nœud de périmètre du cluster. Concernant le cluster HDInsight avec R Server, voici la convention d’affectation de noms pour le nœud principal et le nœud de périmètre.
   * Nœud principal `CLUSTERNAME-ssh.azurehdinsight.net`
   * Nœud de périmètre `CLUSTERNAME-ed-ssh.azurehdinsight.net` 

2. Établissez une connexion SSH dans le nœud périphérique du cluster selon le modèle d’affectation de noms fourni à l’étape 1. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Une fois que vous êtes connecté, devenez un utilisateur racine sur le cluster. Dans la session SSH, utilisez la commande suivante :

        sudo su -

4. Téléchargez le script personnalisé pour installer RStudio. Utilisez la commande suivante :

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Modifiez les autorisations sur le fichier de script personnalisé et exécutez le script. Utilisez les commandes suivantes :

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Si vous avez utilisé un mot de passe SSH en créant un cluster HDInsight avec R Server, vous pouvez ignorer cette étape et passer à la suivante. Si vous avez utilisé à la place une clé SSH pour créer le cluster, vous devez définir un mot de passe pour votre utilisateur SSH. Vous avez besoin de ce mot de passe pour vous connecter à RStudio. Exécutez les commandes suivantes :

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:


7. Lorsque vous êtes invité à entrer le **Mot de passe Kerberos actuel**, appuyez sur **ENTRÉE**.  Notez que vous devez remplacer `USERNAME` par un utilisateur SSH pour votre cluster HDInsight. Si votre mot de passe est défini correctement, le message suivant s’affiche :

        passwd: password updated successfully

    Quittez la session SSH.

8. Créez un tunnel SSH vers le cluster en mappant `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` sur le cluster HDInsight à l’ordinateur client. Vous devez créer un tunnel SSH avant d’ouvrir une nouvelle session de navigateur.

   * Sur un client Linux ou un client Windows avec [Cygwin](http://www.redhat.com/services/custom/cygwin/), ouvrez une session de terminal et utilisez la commande suivante :

             ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       Remplacez **USERNAME** par un utilisateur SSH de votre cluster HDInsight et **CLUSTERNAME** par le nom de votre cluster HDInsight.
       Vous pouvez également utiliser une clé SSH plutôt qu’un mot de passe en ajoutant `-i id_rsa_key`.        
   * Si vous utilisez un client Windows avec PuTTY

     1. Ouvrez PuTTY et saisissez vos informations de connexion.
     2. Dans la rubrique **Catégorie** située à gauche dans la boîte de dialogue, développez **Connexion** et **SSH**, puis sélectionnez **Tunnels**.
     3. Indiquez les informations suivantes dans le formulaire des **Options de contrôle de transfert du port SSH** .

        * **Port source** : le port sur le client que vous souhaitez transférer. Par exemple, **8787**.
        * **Destination** - La destination doit être mappée à l’ordinateur client local. Par exemple, **localhost:8787**.

            ![Création d’un tunnel SSH](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Création d’un tunnel SSH")

     4. Cliquez sur **Ajouter** pour ajouter les paramètres, puis cliquez sur **Ouvrir** pour ouvrir une connexion SSH.
     5. Lorsque vous y êtes invité, connectez-vous au serveur pour établir une session SSH et activer le tunnel.

9. Ouvrez un navigateur web et entrez l’URL suivante en fonction du port que vous avez entré pour le tunnel :

        http://localhost:8787/ 

10. Vous êtes invité à entrer le nom d’utilisateur SSH et le mot de passe pour vous connecter au cluster. Si vous avez utilisé une clé SSH en créant le cluster, vous devez entrer le mot de passe que vous avez créé à l’étape 5.

    ![Connexion à R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Création d’un tunnel SSH")

11. Pour tester si l’installation de RStudio s’est bien déroulée, vous pouvez exécuter un script de test qui exécute des travaux R MapReduce et Spark sur le cluster. Pour télécharger le script de test à exécuter dans RStudio, revenez à la console SSH et entrez les commandes suivantes :

    *    Si vous avez créé un cluster Hadoop avec R, utilisez cette commande :

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
    *    Si vous avez créé un cluster Spark avec R, utilisez cette commande :

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

12. Dans RStudio, vous verrez le script de test que vous avez téléchargé. Double-cliquez sur le fichier pour l’ouvrir, sélectionnez son contenu, puis cliquez sur **Exécuter**. Vous devriez voir la sortie dans le volet **Console** :

   ![Test de l’installation](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Test de l’installation")

Vous pouvez également taper `source(testhdi.r)` ou `source(testhdi_spark.r)` pour exécuter le script.

## <a name="see-also"></a>Voir aussi

* [Calcul d’options de contexte pour R Server sur les clusters HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)
* [Options d’Azure Storage pour R Server sur HDInsight](hdinsight-hadoop-r-server-storage.md)


