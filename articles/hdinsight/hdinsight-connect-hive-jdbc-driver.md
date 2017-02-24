---
title: "Utiliser JDBC pour interroger Hive sur Azure HDInsight"
description: "Découvrez comment utiliser JDBC pour vous connecter à Hive sur Azure HDInsight et exécuter des requêtes à distance sur les données stockées dans le cloud."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 928f8d2a-684d-48cb-894c-11c59a5599ae
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 50a9c3929a4d3194c3786a3d4f6cdd1b73fb5867
ms.openlocfilehash: 41c3e20dc059ecc344cba6ca09ec04892e202d63


---
# <a name="connect-to-hive-on-azure-hdinsight-using-the-hive-jdbc-driver"></a>Se connecter à Hive sur Azure HDInsight à l’aide du pilote JDBC Hive

[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Dans ce document, vous allez apprendre à utiliser JDBC dans une application Java pour soumettre à distance des requêtes Hive à un cluster HDInsight. Vous allez apprendre à vous connecter à partir du client SQuirreL SQL et à vous connecter par programme à partir de Java.

Pour plus d’informations sur l’interface JDBC pour Hive, consultez [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop sur HDInsight. Les clusters basés sur Linux ou Windows fonctionnent.

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL est une application cliente JDBC.

Pour générer et exécuter l’exemple d’application Java lié à cet article, vous devez disposer des éléments suivants.

* Le [Kit de développeur Java (JDK) version 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou supérieure.
* [Apache Maven](https://maven.apache.org). Maven est un système de conception de projets Java qui est utilisé par le projet associé à cet article.

## <a name="connection-string"></a>Chaîne de connexion

Les connexions JDBC à un cluster HDInsight sur Azure sont établies sur le port 443, et le trafic est sécurisé à l’aide de SSL. La passerelle publique derrière laquelle se trouvent les clusters redirige le trafic vers le port d’écoute réel d’HiveServer2. Ainsi, une chaîne de connexion classique ressemble à ce qui suit :

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

## <a name="authentication"></a>Authentification

Lors de l’établissement de la connexion, vous devez utiliser les nom et mot de passe d’administrateur du cluster HDInsight pour vous authentifier auprès de la passerelle du cluster. Lors d’une connexion à partir de clients JDBC, tels que SQuirreL SQL, vous devez entrer les nom et mot de passe d’administrateur dans les paramètres du client.

À partir d’une application Java, vous devez utiliser les nom et mot de passe lors de l’établissement d’une connexion. Par exemple, le code Java suivant ouvre une nouvelle connexion à l’aide de la chaîne de connexion, du nom d’administrateur et du mot de passe :

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Connexion avec un client SQuirreL SQL

SQuirreL SQL est un client JDBC permettant d’exécuter à distance des requêtes Hive avec votre cluster HDInsight. Les étapes suivantes supposent que vous avez déjà installé SQuirreL SQL. Elles expliquent comment télécharger et configurer les pilotes pour Hive.

1. Copiez les pilotes JDBC pour Hive à partir de votre cluster HDInsight.
   
    * Pour **HDInsight sous Linux**, procédez comme suit pour télécharger les fichiers jar requis.
     
        1. Créez un répertoire destiné à contenir les fichiers. Par exemple, `mkdir hivedriver`.
        2. À partir d’une invite de commandes, de Bash, de PowerShell ou d’une autre invite de ligne de commande, passez des répertoires au nouveau répertoire, puis utilisez les commandes suivantes pour copier les fichiers du cluster HDInsight.
        
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
        
            Remplacez **USERNAME** par le nom de compte d’utilisateur SSH pour le cluster. Remplacez **CLUSTERNAME** par le nom du cluster HDInsight.
        
        > [!NOTE]
        > Dans les environnements Windows, vous ne disposerez peut-être pas de la commande `scp`. Dans ce cas, utilisez l’utilitaire PSCP à la place. Vous pouvez le télécharger à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * Pour **HDInsight sous Windows**, procédez comme suit pour télécharger les fichiers jar.
     
        1. Dans le portail Azure, sélectionnez votre cluster HDInsight, puis l’icône **Bureau à distance** .
        
            ![Icône Bureau à distance](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. Dans le panneau Bureau à distance, utilisez le bouton **Connexion** pour vous connecter au cluster. Si le Bureau à distance n’est pas activé, entrez un nom d’utilisateur et un mot de passe dans le formulaire, puis sélectionnez **Activer** pour activer le Bureau à distance pour le cluster.
        
            ![Panneau Bureau à distance](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)
        
            Après sélection du bouton **Connexion**, un fichier .rdp est téléchargé. Utilisez ce fichier pour lancer le client Bureau à distance. Lorsque vous y êtes invité, utilisez les nom d’utilisateur et mot de passe que vous avez entrés pour accéder au Bureau à distance.

        3. Une fois connecté, copiez les fichiers suivants à partir de la session Bureau à distance sur votre ordinateur local. Placez-les dans un répertoire local nommé `hivedriver`.
        
            * C:\apps\dist\hive-0.14.0.2.2.9.1-7\lib\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\lib\hadoop-auth-2.6.0.2.2.9.1-7.jar
          
            > [!NOTE]
            > Les numéros de version inclus dans les chemins d’accès et noms de fichier peuvent différer pour votre cluster.

        4. Une fois les fichiers copiés, fermez la session Bureau à distance.

2. Démarrez l’application SQuirreL SQL. Dans la partie gauche de la fenêtre, sélectionnez **Pilotes**.
   
    ![Onglet Pilotes dans la partie gauche de la fenêtre](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

3. Parmi les icônes en haut de la boîte de dialogue **pilotes**, sélectionnez l’icône **+** pour créer un pilote.
   
    ![Icônes des pilotes](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

4. Dans la boîte de dialogue Ajouter un pilote, ajoutez les informations suivantes.
   
    * **Nom** : Hive
    * **Exemple d’URL **: `jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`
    * **Chemin d’accès à la classe supplémentaire** : utilisez le bouton Ajouter pour ajouter les fichiers jar téléchargé précédemment
    * **Nom de la classe** : org.apache.hive.jdbc.HiveDriver
    
   ![boîte de dialogue ajouter un pilote](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)
    
   Cliquez sur **OK** pour enregistrer ces paramètres.

5. Dans la partie gauche de la fenêtre SQL SQuirreL, sélectionnez **Alias**. Cliquez sur l’icône **+** pour créer un alias de connexion.
   
    ![ajouter un nouvel alias](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

6. Dans la boîte de dialogue **Ajouter un Alias** , utilisez les valeurs suivantes.
   
    * **Nom** : Hive sur HDInsight

    * **Pilote** : sélectionnez le pilote **Hive** dans la liste déroulante

    * **URL**: jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
     
        Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

    * **Nom d’utilisateur** : nom de compte de connexion de votre cluster HDInsight. Par défaut, il s’agit de `admin`.

    * **Mot de passe** : mot de passe du compte de connexion du cluster. Il s’agit du mot de passe que vous avez fourni lors de la création du cluster HDInsight.
     
    ![boîte de dialogue ajouter un alias](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)
     
    Utilisez le bouton **Test** pour vérifier que la connexion fonctionne. Quand la boîte de dialogue **Se connecter à : Hive sur HDInsight** s’affiche, sélectionnez **Connexion** pour effectuer le test. Si le test réussit, la boîte de dialogue **Connexion réussie** s’affiche.
     
    Cliquez sur le bouton **OK** au bas de la boîte de dialogue **Ajouter un alias** pour enregistrer l’alias de connexion.

7. Dans la liste déroulante **Se connecter à** en haut de SQuirreL SQL, sélectionnez **Hive sur HDInsight**. Lorsque vous y êtes invité, sélectionnez **Connexion**.
   
    ![boîte de dialogue connexion](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

8. Une fois connecté, entrez la requête suivante dans la boîte de dialogue Requête SQL, puis sélectionnez l’icône **Exécuter**. La zone de résultats doit afficher les résultats de la requête.
   
        select * from hivesampletable limit 10;
   
    ![boîte de dialogue requête sql, incluant les résultats](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Connexion à partir d’un exemple d’application Java

Vous trouverez un exemple d’utilisation d’un client Java pour interroger Hive sur HDInsight à l’adresse suivante : [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Suivez les instructions indiquées dans le référentiel pour générer et exécuter l’exemple.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Une erreur inattendue s'est produite lors de l'ouverture d'une connexion SQL

**Symptômes** : lors de la connexion à un cluster HDInsight version 3.3 ou 3.4, vous pouvez recevoir un message indiquant qu'une erreur inattendue s'est produite. L'arborescence des appels de procédure pour cette erreur commence par les lignes suivantes :

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Cause** : cette erreur est due à une incohérence entre la version du fichier commons-codec.jar utilisé par SQuirreL et celle exigée par les composants JDBC Hive téléchargés à partir du cluster HDInsight.

**Résolution** : pour corriger cette erreur, procédez comme suit.

1. Téléchargez le fichier jar common-codec à partir de votre cluster HDInsight.
   
        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Quittez SQuirreL et accédez au répertoire où SQuirreL est installé sur votre système. Dans le répertoire SquirreL, sous le dossier `lib` , remplacez le fichier commons-codec.jar existant par le fichier téléchargé à partir du cluster HDInsight.

3. Redémarrez SQuirreL. L'erreur ne devrait plus apparaître lors de la connexion à Hive sur HDInsight.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez vu comment utiliser JDBC avec Hive, utilisez les liens suivants pour découvrir d’autres façons d’utiliser Azure HDInsight.

* [Téléchargement de données vers HDInsight](hdinsight-upload-data.md)
* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)




<!--HONumber=Feb17_HO2-->


