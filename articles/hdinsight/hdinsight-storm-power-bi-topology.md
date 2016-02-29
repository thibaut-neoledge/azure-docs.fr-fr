<properties
 pageTitle="Écriture de données dans Power BI à partir d’Apache Storm | Microsoft Azure"
 description="Écrivez des données dans Power BI à partir d’une topologie C# s’exécutant sur un cluster Apache Storm dans HDInsight. En outre, créez un rapport et un tableau de bord en temps réel à l’aide de Power BI."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="01/08/2016"
 ms.author="larryfr"/>

# Utilisation de Power BI pour visualiser les données d'une topologie Apache Storm

Power BI vous permet d'afficher visuellement des données sous forme de rapports ou de tableaux de bord. À l'aide de l'API REST de Power BI, vous pouvez facilement utiliser les données d'une topologie s'exécutant sur un cluster Apache Storm dans HDInsight pour Power BI.

Dans ce document, vous allez découvrir comment utiliser Power BI pour créer un rapport et un tableau de bord à partir de données créées par une topologie Storm.

## Composants requis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un utilisateur Azure Active Directory avec un accès [Power BI](https://powerbi.com)

* Visual Studio (l'une des versions suivantes)

    * Visual Studio 2012 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * Visual Studio 2015 [CTP6](http://visualstudio.com/downloads/visual-studio-2015-ctp-vs)

* Les outils HDInsight pour Visual Studio : consultez [Prise en main des outils HDInsight pour Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) pour plus d'informations sur l'installation.

## Fonctionnement

Cet exemple contient une topologie C# Storm qui génère une phrase de manière aléatoire, la fractionne en mots, compte les mots et envoie les mots et le nombre de mots à l'API REST de Power BI. Le package NuGet [PowerBi.Api.Client](https://github.com/Vtek/PowerBI.Api.Client) est utilisé pour communiquer avec Power BI.

Les fichiers suivants dans ce projet implémentent les fonctionnalités spécifiques de Power BI :

* **PowerBiBolt.cs** : implémente le bolt Storm, qui envoie des données à Power BI

* **Data.cs** : décrit la ligne/l'objet de données qui sera envoyé à Power BI

> [AZURE.WARNING] Power BI semble autoriser la création de plusieurs jeux de données de même nom. Cela peut se produire si le jeu de données n'existe pas et que votre topologie crée plusieurs instances du bolt Power BI. Pour éviter ce problème, définissez l'indicateur de parallélisme du bolt sur 1 (comme indiqué dans cet exemple) ou créez le jeu de données avant de déployer la topologie.
>
> L'application de console **CreateDataset** incluse dans cette solution est fournie en exemple pour montrer comment créer le jeu de données en dehors de la topologie.

## Enregistrer une application Power BI

1. Suivez les étapes du guide de [démarrage rapide de Power BI](https://msdn.microsoft.com/library/dn931989.aspx) pour ouvrir un compte Power BI.

2. Suivez les étapes de la page [Inscrire une application](https://msdn.microsoft.com/library/dn877542.aspx) pour créer une inscription de l'application. Ces informations vous serviront pour accéder à l'API REST de Power BI.

    > [AZURE.IMPORTANT] Enregistrez l'**ID du client** pour l'inscription de l'application.

## Télécharger l'exemple

Téléchargez l’[exemple HDInsight C# Storm Power BI](https://github.com/Blackmist/hdinsight-csharp-storm-powerbi). Pour le télécharger, clonez-le/répliquez-le à l'aide de [git](http://git-scm.com/) ou utilisez le lien **Télécharger** pour télécharger un fichier zip de l'archive.

## Configurer l'exemple

1. Ouvrez l'exemple dans Visual Studio. Dans l'**Explorateur de solutions**, ouvrez le fichier **App.config**, puis recherchez l'élément **<OAuth .../>**. Entrez des valeurs pour les propriétés suivantes de cet élément.

    * **Client** : l'ID du client pour l'inscription de l'application créée précédemment.

    * **Utilisateur** : un compte Azure Active Directory ayant accès à Power BI.

    * **Mot de passe** : le mot de passe du compte Azure Active Directory.

2. (Facultatif). Le nom du jeu de données par défaut utilisé par ce projet est **Words**. Pour le modifier, cliquez avec le bouton droit sur le projet **WordCount** dans l'**Explorateur de solutions**, sélectionnez **Propriétés**, puis **Paramètres**. Modifiez l'entrée **DatasetName** en ajoutant la valeur souhaitée.

2. Enregistrez et fermez les fichiers.

## Déployer l'exemple

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **WordCount** et sélectionnez **Envoyer à Storm dans HDInsight**. Sélectionnez le cluster HDInsight dans la liste déroulante **Cluster Storm** de la boîte de dialogue.

    > [AZURE.NOTE] L'affichage des noms de serveur dans la liste déroulante **Cluster Storm** peut prendre plusieurs secondes.
    >
    > Si vous y êtes invité, entrez les informations d'identification de connexion à votre abonnement Azure. Si vous disposez de plusieurs abonnements, connectez-vous à celui qui contient votre cluster Storm dans HDInsight.

2. Une fois la topologie correctement envoyée, les topologies Storm du cluster doivent s'afficher. Sélectionnez la topologie WordCount à partir de la liste pour afficher des informations sur la topologie en cours d'exécution.

    ![Les topologies, avec la topologie WordCount sélectionnée](./media/hdinsight-storm-power-bi-topology/topologysummary.png)

    > [AZURE.NOTE] Vous pouvez également afficher les topologies Storm dans l'Explorateur de serveurs : développez Azure, HDInsight, cliquez avec le bouton droit sur un cluster Storm dans HDInsight, puis sélectionnez Afficher les topologies Storm.

3. Quand vous affichez le **Résumé de la topologie**, faites défiler l'écran jusqu'à la section **Bolts**. Dans cette section, notez la colonne **Exécuté** du bolt **PowerBI**. Utilisez le bouton d'actualisation en haut de la page pour que la valeur zéro soit remplacée par une autre valeur. Quand ce nombre commence à augmenter, cela signifie que les éléments sont en cours d'écriture dans Power BI.

## Créer un rapport

1. Dans un navigateur, accédez à [https://PowerBI.com](https://powerbi.com). Connectez-vous avec votre compte.

2. À gauche de la page, développez **Jeux de données**. Sélectionnez l'entrée **Words**. Il s'agit du jeu de données créé dans l'exemple de topologie.

    ![Entrée du jeu de données Words](./media/hdinsight-storm-power-bi-topology/words.png)

3. Dans la zone **Champs**, développez **WordCount**. Faites glisser les entrées **Count** et **Word** au milieu de la page. Un graphique est créé avec une barre pour chaque mot qui indique combien de fois le mot s'est produit.

    ![Graphique WordCount](./media/hdinsight-storm-power-bi-topology/wordcountchart.png)

4. En haut de la page, sélectionnez **Enregistrer** pour créer un rapport. Entrez **Word Count** comme nom du rapport.

5. Sélectionnez le logo Power BI pour revenir au tableau de bord. Le rapport **Word Count** apparaît désormais sous **Rapports**.

## Créer un tableau de bord dynamique

1. À côté de **Tableau de bord**, sélectionnez l'icône **+** pour créer un tableau de bord. Nommez le nouveau tableau de bord **Live Word Count**.

2. Sélectionnez le rapport **Word Count** créé précédemment. Sélectionnez le graphique quand il s'affiche, puis l'icône en forme d'épingle en bas à droite du graphique. Vous devez recevoir une notification indiquant qu'il a été épinglé au tableau de bord.

    ![graphique avec une punaise affichée](./media/hdinsight-storm-power-bi-topology/pushpin.png)

2. Sélectionnez le logo Power BI pour revenir au tableau de bord. Sélectionnez le tableau de bord **Live Word Count**. Il contient à présent le graphique Word Count et les mises à jour du graphique en tant que de nouvelles entrées. Elle sont envoyées à Power BI à partir de la topologie WordCount s'exécutant dans HDInsight.

    ![Le tableau de bord dynamique](./media/hdinsight-storm-power-bi-topology/dashboard.png)

## Arrêter la topologie WordCount

La topologie continue de s'exécuter jusqu'à ce qu'elle soit arrêtée ou supprimée du cluster Storm dans HDInsight. Procédez comme suit pour arrêter la topologie :

1. Dans Visual Studio, ouvrez la fenêtre **Résumé de la topologie** pour la topologie WordCount. Si le résumé de la topologie n'est pas déjà ouvert, accédez à l'**Explorateur de serveurs**, développez les entrées **Azure** et **HDInsight**, cliquez avec le bouton droit sur le cluster Storm dans HDInsight et sélectionnez **Afficher les topologies Storm**. Enfin, sélectionnez la topologie **WordCount**.

2. Sélectionnez le bouton **Arrêter** pour arrêter la topologie **WordCount**.

    ![Bouton Arrêter dans le résumé de la topologie](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## Étapes suivantes

Dans ce document, vous avez appris comment envoyer des données à partir d'une topologie Storm à Power BI à l'aide de REST. Pour plus d'informations sur l'utilisation d'autres technologies Azure, consultez les rubriques suivantes :

* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0218_2016-->