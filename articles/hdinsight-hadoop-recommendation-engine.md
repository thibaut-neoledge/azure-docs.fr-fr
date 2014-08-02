<properties linkid="manage-services-hdinsight-recommendation-engine-using-mahout" urlDisplayName="Hadoop Recommendation Engine" pageTitle="Hadoop recommendation engine (.NET) | Azure" metaKeywords="Azure Apache Mahout, Azure recommendation example, Azure recommendation tutorial, Azure recommendation engine" description="A tutorial that teaches how to use the Apache Mahout recommendation engine with Azure to create song suggestions based on listening habits." disqusComments="1" umbracoNaviHide="1" title="Simple recommendation engine using Apache Mahout" authors="jgao" manager="paulettm" editor="cgronlun" />

Moteur de recommandation simple avec Apache Mahout
==================================================

Apache Mahout est une bibliothèque à apprentissage automatique conçue pour être utilisée dans les applications à apprentissage automatique évolutives. Les moteurs des personnes ayant effectué une recommandation sont l'un des types d'applications à apprentissage automatique les plus populaires utilisés actuellement. Ils offrent de nombreuses applications marketing évidentes.

Apache Mahout fournit une implémentation intégrée du filtrage collaboratif basé sur l'article. Cette approche est fréquemment utilisée dans le cadre des explorations de données de recommandation. Le filtrage collaboratif basé sur l'article a été développé par Amazon.com. Son principe est que les données relatives aux préférences d'utilisateur démontrant des corrélations entre les préférences d'objets peuvent être utilisées pour influencer les goûts des futurs utilisateurs d'un groupe semblable.

Dans ce didacticiel, vous allez utiliser le site [Million Song Dataset](http://labrosa.ee.columbia.edu/millionsong/tasteprofile) et télécharger le [jeu de données](http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip) pour créer des recommandations de chansons pour les utilisateurs, d'après leurs habitudes d'écoute passées.

Vous apprendrez à effectuer les opérations suivantes :

-   Utilisation des moteurs de recommandation

Ce didacticiel se compose des parties suivantes :

1.  [Installation et configuration](#setup)
2.  [Examen et formatage des données](#segment1)
3.  [Installation de Mahout](#Segment2)
4.  [Exécution du travail Mahout](#segment2)

Installation et configuration
-----------------------------

Ce didacticiel part du principe que vous avez installé Azure et la version préliminaire de HDInsight et que vous avez créé un cluster HDInsight sur lequel vous pouvez exécuter un exemple. Si vous n'avez pas encore effectué ces actions, consultez le didacticiel [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) pour obtenir des instructions sur le respect de ces prérequis.

Examen et formatage des données
-------------------------------

Cet exemple présente la façon dont les utilisateurs expriment leur préférence pour certaines chansons. L'hypothèse est que le nombre de fois où un utilisateur écoute une chanson permet de mesurer la préférence de cet utilisateur pour cette chanson. Les modèles détectés dans les données de préférence peuvent être utilisés pour prévoir les futures préférences des utilisateurs d'après certaines de leurs préférences musicales exprimées. Vous pouvez afficher un exemple de ce jeu de données dans la section **Description** de la page Web [Echo Nest Taste Profile Subset](http://labrosa.ee.columbia.edu/millionsong/tasteprofile) :

![Echo Nest Taste Profile Subset](./media/hdinsight-hadoop-recommendation-engine/the-echo-nest-taste-profile-subset.png)

### Exemple de données de Million Song Dataset

Pour utiliser ce jeu de données avec Mahout, vous devez accomplir deux actions :

1.  convertir les ID des chansons et des utilisateurs en valeurs entières ;
2.  enregistrer les nouvelles valeurs avec leur classement dans un fichier séparé par des virgules.

Si Visual Studio 2010 n'est pas installé sur votre ordinateur, ignorez cette étape et accédez à la section Exécution du travail Mahout pour obtenir une version pré-générée.

Lancez tout d'abord Visual Studio 2010, puis sélectionnez **Fichier -\> Nouveau -\> Projet**. Dans le volet **Modèles installés**, sous le nœud **Visual C\#**, sélectionnez la catégorie **Fenêtre**, puis sélectionnez **Application console** dans la liste. Nommez le projet « ConvertToMahoutInput » et cliquez sur le bouton **OK**.

![création d'une application console](./media/hdinsight-hadoop-recommendation-engine/creating-a-console-application.png)

### Création d'une application console

1.  Une fois l'application créée, ouvrez le fichier **Program.cs** et ajoutez les membres statiques suivants dans la classe **Programme** :

         const char tab = '\u0009';
         static Dictionary<string, int> usersMapping = new Dictionary<string, int>();
         static Dictionary<string, int> songMapping = new Dictionary<string, int>();    

2.  Ensuite, ajoutez l'instruction `using System.IO;` et remplissez la méthode **Main** à l'aide du code suivant :

         var inputStream = File.Open(args[0], FileMode.Open);
         var reader = new StreamReader(inputStream);

         var outStream = File.Open("mInput.txt", FileMode.OpenOrCreate);
         var writer = new StreamWriter(outStream);

         var i = 1;

         var line = reader.ReadLine();
         while (!string.IsNullOrWhiteSpace(line))
         {
            i++;
            if (i \> 5000)
            break;
            var outLine = line.Split(tab); 

            int user = GetUser(outLine[0]);
            int song = GetSong(outLine[1]); 

            writer.Write(user);
            writer.Write(',');
            writer.Write(song);
            writer.Write(',');
            writer.WriteLine(outLine[2]);

            line = reader.ReadLine();

         }

         Console.WriteLine("saved {0} lines to {1}", i, args[0]);

         reader.Close();
         writer.Close();

         SaveMapping(usersMapping, "users.csv");
         SaveMapping(songMapping, "mInput.csv");

         Console.WriteLine("Mapping saved");
         Console.ReadKey();

3.  À présent, créez les fonctions **GetUser** et **GetSong**, qui convertissent les ID en entiers :

         static int GetUser(string user)
         {
             if (!usersMapping.ContainsKey(user))
                 usersMapping.Add(user, usersMapping.Count + 1);

             return usersMapping[user];
         }

         static int GetSong(string song)
         {
             if (!songMapping.ContainsKey(song))
                 songMapping.Add(song, songMapping.Count + 1);

             return songMapping[song];
         }

4.  Enfin, créez l'utilitaire permettant d'implémenter la méthode SaveMapping, qui enregistre les dictionnaires de mappage de robot en fichiers .csv.

         static void SaveMapping(Dictionary<string, int> mapping, string fileName)
         {
             var stream = File.Open(fileName, FileMode.Create);
             var writer = new StreamWriter(stream);

             foreach (var key in mapping.Keys)
             {
                writer.Write(key);
                writer.Write(',');
                writer.WriteLine(mapping[key]);
             }

             writer.Close();
         }

5.  Téléchargez l'exemple de données à partir de [ce lien](http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip). Une fois le téléchargement terminé, ouvrez **train\_triplets.txt.zip** et procédez à l'extraction du fichier **train\_triplets.txt**.

    Lors de l'exécution de l'utilitaire, ajoutez un argument de ligne de commande avec l'emplacement de **train\_triplets.txt**. Pour cela, cliquez avec le bouton droit sur le nœud de projet **ConvertToMahoutInput** dans l'**Explorateur de solutions** et sélectionnez **Propriétés**. Sur la page des propriétés du projet, sélectionnez l'onglet **Débogage** sur le côté gauche, puis ajoutez le chemin du fichier &lt;chemin d'accès local\>train\_triplets.txt dans la zone de texte **Arguments de ligne de commande** :

    ![définition des arguments de ligne de commande](./media/hdinsight-hadoop-recommendation-engine/setting-command-line-arguments.png)

### Définition des arguments de ligne de commande

-   Appuyez sur **F5** pour exécuter le programme. Une fois l'opération terminée, ouvrez le dossier **bin\\Debug** à partir de l'emplacement dans lequel le projet a été enregistré, puis affichez le résultat de l'utilitaire. Vous devriez retrouver les fichiers users.txt et mInput.txt.

Installation de Mahout
----------------------

-   Ouvrez le portail du cluster HDInsight, puis cliquez sur l'icône **Bureau à distance**.

    ![Icône Gérer le cluster](./media/hdinsight-hadoop-recommendation-engine/the-manage-cluster-icon.png)

### Icône Bureau à distance

HDInsight ne comprend pas Mahout par défaut. Celui-ci faisant partie de l'écosystème Hadoop, il peut être téléchargé sur le site Web [Mahout](http://mahout.apache.org/). La version la plus récente est la 0.7, mais cet ensemble d'instructions est compatible avec les versions 0.5 ou 0.7.

1.  Téléchargez tout d'abord [Mahout version 0.7](http://www.apache.org/dyn/closer.cgi/mahout/) sur votre ordinateur.

2.  Ensuite, copiez-le sur le cluster. Pour cela, sélectionnez le fichier .zip local et appuyez sur Ctrl+C pour copier, puis collez-le dans votre cluster Hadoop.

    ![Téléchargement de Mahout](./media/hdinsight-hadoop-recommendation-engine/uploading-mahout.PNG)

### Copie de Mahout sur le nœud principal

1.  Une fois le processus de copie terminé, cliquez avec le bouton droit sur le fichier .zip et procédez à l'extraction de la distribution Mahout dans C:\\apps\\dist. Mahout est à présent installé sous C:\\apps\\dist\\mahout-distribution-0.7.

2.  Renommez le dossier c:\\apps\\dist\\mahout-0.7 pour plus de simplicité.

### Exécution du travail Mahout

1.  Copiez le fichier **mInput.txt** du dossier **bin\\Debug** vers le dossier **c:\\** sur le cluster distant. Une fois le fichier copié, procédez à son extraction. Comme indiqué dans la section précédente, pour effectuer la copie d'un fichier sur une session RDP distante, sélectionnez les fichiers sur votre ordinateur, appuyez sur Ctrl+C, puis sur Ctrl+V dans la fenêtre de session RDP.

2.  Créez un fichier contenant l'ID de l'utilisateur pour lequel vous allez générer des recommandations. Pour cela, créez simplement un fichier texte nommé **users.txt** dans **c:\\**, contenant l'ID d'un seul utilisateur.

**Remarque**

Vous pouvez générer des recommandations pour plusieurs utilisateurs en plaçant leur ID sur des lignes séparées. Si vous rencontrez des problèmes lors de la génération des fichiers mInput.txt et users.txt, vous pouvez télécharger une version pré-générée dans ce [référentiel](https://github.com/wenming/BigDataSamples/tree/master/mahout) Github. Il est plus pratique de télécharger tous les éléments en un [fichier .zip](https://github.com/wenming/BigDataSamples/archive/master.zip). Recherchez les fichiers users.txt et mInput.txt et copiez-les sur le cluster distant, dans le dossier c:\\.

Vous devez alors ouvrir une fenêtre Terminal de Hadoop et accéder au dossier qui contient les fichiers users.txt et mInput.txt.

![Fenêtre de commande Mahout](./media/hdinsight-hadoop-recommendation-engine/mahout-commandwindow.PNG)

### Fenêtre de commande Hadoop

1.  Ensuite, copiez les fichiers **mInput.txt** et **users.txt** dans HDFS. Pour cela, ouvrez l'**interface de commande Hadoop** et exécutez les commandes suivantes :

         hadoop dfs -copyFromLocal c:\mInput.txt input\mInput.txt
         hadoop dfs -copyFromLocal c:\users.txt input\users.txt

2.  Vérifiez que les fichiers ont été copiés dans HDFS :

         hadoop fs -ls input/

    Les lignes ci-dessous doivent s'afficher :

         Found 2 items
         -rwxrwxrwx   1 writer supergroup      53322 2013-03-08 20:32 /user/writer/input/mInput.txt
         -rwxrwxrwx   1 writer supergroup        353 2013-03-08 20:33 /user/writer/input/users.txt

3.  À présent, vous pouvez exécuter le travail Mahout grâce à la commande suivante :

         c:\apps\dist\mahout-0.7\bin>hadoop jar c:\Apps\dist\mahout-0.7\mahout-core-0.7-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob -s SIMILARITY_COOCCURRENCE --input=input/mInput.txt --output=output --usersFile=input/users.txt

    Le moteur de recommandation peut utiliser de nombreuses autres fonctions de « distance » pour comparer le vecteur de fonctions pour différents utilisateurs. Pour effectuer des tests, remplacez la classe Similarité par SIMILARITY\_COOCCURRENCE, SIMILARITY\_LOGLIKELIHOOD, SIMILARITY\_TANIMOTO\_COEFFICIENT, SIMILARITY\_CITY\_BLOCK, SIMILARITY\_COSINE, SIMILARITY\_PEARSON\_CORRELATION ou SIMILARITY\_EUCLIDEAN\_DISTANCE. Dans le cadre de ce didacticiel, nous n'explorerons pas dans le détail le thème des données scientifiques de Mahout.

4.  Le travail Mahout s'exécute pendant plusieurs minutes. Un fichier de résultat est ensuite créé. Exécutez la commande suivante pour créer une copie locale du fichier de résultat :

         hadoop fs -copyToLocal output/part-r-00000 c:\output.txt

5.  Ouvrez le fichier **output.txt** dans le dossier racine **c:\\** et inspectez son contenu. La structure du fichier est la suivante :

         user    [song:rating,song:rating, ...]

6.  Si vous souhaitez utiliser d'autres parties de Mahout sur votre cluster, vous devez enregistrer une copie de Mahout.cmd dans le répertoire Bin de la distribution Mahout.

Résumé
------

Les moteurs des personnes ayant effectué une recommandation offrent des fonctionnalités importantes pour de nombreux sites de réseaux sociaux modernes, de shopping en ligne, de diffusion multimédia et autres. Mahout fournit un moteur de recommandation prêt à l'emploi et facile à utiliser. Il contient de nombreuses fonctions utiles et est évolutif sur Hadoop.

Étapes suivantes
----------------

Bien que cet article présente l'utilisation de la ligne de commande Hadoop, vous pouvez également réaliser des tâches avec la console interactive HDInsight. Pour plus d'informations, consultez [Recommandations : HDInsight Interactive JavaScript et consoles Hive][interactive-console].

