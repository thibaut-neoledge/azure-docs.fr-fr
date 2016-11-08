---
title: Utiliser une fonction définie par l’utilisateur (UDF) de Java avec Hive dans HDInsight | Microsoft Docs
description: Découvrez comment créer et utiliser une fonction définie par l’utilisateur (UDF) de Java à partir de Hive dans HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: larryfr

---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Utiliser une fonction UDF Java avec Hive dans HDInsight
Le langage Hive est parfait pour traiter des données dans HDInsight, mais vous avez parfois besoin d’un langage plus généraliste. Hive vous permet de créer des fonctions définies par l’utilisateur (UDF) avec différents langages de programmation. Dans ce document, vous allez apprendre à utiliser une fonction UDF Java depuis Hive.

## <a name="requirements"></a>Configuration requise
* Un abonnement Azure
* Un cluster HDInsight (Windows ou Linux)
  
  > [!NOTE]
  > La plupart des étapes de ce document fonctionnent sur les deux types de cluster. Toutefois, les étapes utilisées pour télécharger l’UDF compilée dans le cluster et l’exécuter sont spécifiques aux clusters basés sur Linux. Vous trouverez des liens vers des informations qui peuvent être utilisées avec les clusters Windows.
  > 
  > 
* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou ultérieur (ou un équivalent, par exemple, OpenJDK)
* [Apache Maven](http://maven.apache.org/)
* Un éditeur de texte ou un IDE Java
  
  > [!IMPORTANT]
  > Si vous utilisez un serveur HDInsight sous Linux, mais créez des fichiers Python sur un client Windows, vous avez besoin d’un éditeur qui utilise LF comme fin de ligne. Si vous ne savez pas si votre éditeur utilise LF ou CRLF, consultez la section [Dépannage](#troubleshooting) pour savoir comment supprimer le caractère CR à l’aide des utilitaires sur le cluster HDInsight.
  > 
  > 

## <a name="create-an-example-udf"></a>Créer un exemple de fonction UDF
1. À partir d’une ligne de commande, utilisez ce qui suit pour créer un nouveau projet Maven :
   
        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
   > [!NOTE]
   > Si vous utilisez PowerShell, vous devez placer les paramètres entre guillemets. Par exemple, `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.
   > 
   > 
   
    Cette action crée un répertoire nommé **exampleudf**, qui contient le projet Maven.
2. Une fois le projet créé, supprimez le répertoire **exampleudf/src/test** qui a été créé dans le cadre du projet ; il ne sera pas utilisé pour cet exemple.
3. Ouvrez le fichier **exampleudf/pom.xml** et remplacez l’entrée `<dependencies>` existante par la suivante :
   
        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>
   
    Ces entrées spécifient la version de Hadoop et Hive fournie avec les clusters HDInsight 3.3 et 3.4. Vous trouverez des informations sur les versions de Hadoop et Hive incluses dans HDInsight dans le document [Contrôle des versions du composant HDInsight](hdinsight-component-versioning.md) .
   
    Ajoutez une section `<build>` avant la ligne `</project>` à la fin du fichier. Cette section doit comporter les éléments suivants :
   
        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                            </transformer>
                        </transformers>
                        <!-- Keep us from getting a bad signature error -->
                        <filters>
                            <filter>
                                <artifact>*:*</artifact>
                                <excludes>
                                    <exclude>META-INF/*.SF</exclude>
                                    <exclude>META-INF/*.DSA</exclude>
                                    <exclude>META-INF/*.RSA</exclude>
                                </excludes>
                            </filter>
                        </filters>
                    </configuration>
                    <executions>
                        <execution>
                            <phase>package</phase>
                            <goals>
                                <goal>shade</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
   
    Ces entrées définissent la génération du projet. Plus précisément, la version de java utilisée par le projet et la méthode de génération d’un uberjar pour le déploiement sur le cluster.
   
    Enregistrez le fichier une fois les modifications apportées.
4. Renommez **exampleudf/src/main/java/com/microsoft/examples/App.java** en **ExampleUDF.java**, puis ouvrez le fichier dans votre éditeur.
5. Remplacez le contenu du fichier **ExampleUDF.java** par le code suivant, puis enregistrez-le.
   
        package com.microsoft.examples;
   
        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;
   
        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }
   
    Cela permet d’implémenter une fonction UDF qui accepte une valeur de chaîne et retourne une version de la chaîne en minuscules.

## <a name="build-and-install-the-udf"></a>Générer et installer la fonction UDF
1. Utilisez la commande suivante pour compiler et empaqueter la fonction UDF :
   
        mvn compile package
   
    Cela va générer, puis empaqueter la fonction UDF sous **exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar**.
2. Utilisez la commande `scp` pour copier le fichier sur le cluster HDInsight.
   
        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
   
    Remplacez **myuser** avec le compte d’utilisateur SSH de votre cluster. Remplacez **mycluster** par le nom du cluster. Si vous avez utilisé un mot de passe pour sécuriser votre compte SSH, vous serez invité à le saisir. Si vous avez utilisé un certificat, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le fichier de clé privée.
3. Connectez-vous au cluster à l'aide de SSH. 
   
        ssh myuser@mycluster-ssh.azurehdinsight.net
   
    Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les documents suivants.
   
   * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
4. À partir de la session SSH, copiez le fichier jar sur le stockage HDInsight.
   
        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Utiliser la fonction UDF à partir de Hive
1. Utilisez la commande suivante pour démarrer le client Beeline depuis la session SSH.
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    Cette commande suppose que vous avez utilisé la valeur par défaut d’ **admin** pour le compte de connexion de votre cluster.
2. Une fois sur l’invite `jdbc:hive2://localhost:10001/>` , entrez la commande suivante pour ajouter la fonction UDF dans Hive et l’exposer en tant que fonction.
   
        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
3. Utilisez la fonction UDF pour convertir les valeurs extraites d’une table en chaînes de caractères minuscules.
   
        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
   
    Cette action va sélectionner la plate-forme du périphérique (Android, Windows, iOS, etc.) dans la table, convertir la chaîne en caractères minuscules, puis les afficher. Le résultat se présente alors comme suit.
   
        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir les autres façons de travailler avec Hive, consultez [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md).

Pour plus d’informations sur les fonctions définies par l’utilisateur de Hive, consultez la section [Hive Operators and User-Defined Functions (Opérateurs et fonctions définies par l’utilisateur de Hive)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) du wiki Hive sur le site apache.org.

<!--HONumber=Oct16_HO2-->


