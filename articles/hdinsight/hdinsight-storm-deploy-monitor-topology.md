<properties
   pageTitle="Déploiement et gestion des topologies Apache Storm sur HDInsight | Microsoft Azure"
   description="Apprenez à déployer, surveiller et gérer des topologies Apache Storm à l’aide du tableau de bord Storm sur HDInsight. Utilisez les outils Hadoop pour Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/26/2016"
   ms.author="larryfr"/>

#Déploiement et gestion des topologies Apache Storm sur HDInsight Windows

Le tableau de bord Storm vous permet de déployer et d’exécuter des topologies Apache Storm sur votre cluster HDInsight à l’aide votre navigateur web. Vous pouvez également utiliser le tableau de bord pour surveiller et gérer des topologies en cours d’exécution. Si vous utilisez Visual Studio, les outils HDInsight pour Visual Studio fournissent des fonctionnalités similaires dans Visual Studio.

Le tableau de bord Storm et les fonctionnalités Storm des outils HDInsight s’appuient sur l’API REST Storm, qui peut être utilisée pour créer vos propres solutions d’analyse et de gestion.

> [AZURE.IMPORTANT] Les étapes décrites dans ce document nécessitent un cluster HDInsight Storm basé sur Windows. Pour obtenir des informations sur l'utilisation d'un cluster Linux, consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

##Composants requis

* **Apache Storm sur HDInsight** : consultez la rubrique <a href="../hdinsight-storm-getting-started/" target="_blank">Prise en main d’Apache Storm sur HDInsight </a> pour connaître les étapes de création d’un cluster

* Pour le **tableau de bord Storm** : un navigateur Web moderne qui prend en charge HTML5

* Pour **Visual Studio** : le Kit de développement logiciel (SDK) Azure 2.5.1 ou une version ultérieure et les outils HDInsight pour Visual Studio. Consultez la rubrique <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Prise en main de HDInsight Tools pour Visual Studio</a> pour installer et configurer les outils HDInsight pour Visual Studio.

	L’une des versions suivantes de Visual Studio :

	* Visual Studio 2012 avec <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">Update 4</a>

	* Visual Studio 2013 avec <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">Update 4</a> ou <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Community</a>

	* <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

	> [AZURE.NOTE] Les outils HDInsight pour Visual Studio n’acceptent actuellement que Storm sur les versions de cluster HDInsight 3.2.

##Tableau de bord Storm

Le tableau de bord Storm est une page web disponible sur votre cluster Storm. L’URL est **https://&lt;clustername>.azurehdinsight.net/**, où **clustername** est le nom de votre Storm sur le cluster HDInsight.

Dans la partie supérieure du tableau de bord Storm, sélectionnez **Soumettre la topologie**. Suivez les instructions affichées sur la page pour exécuter une topologie d’exemple ou pour télécharger et exécuter une topologie que vous avez créée.

![la page d’envoi de la topologie][storm-dashboard-submit]

###Interface utilisateur de Storm

Dans le tableau de bord Storm, sélectionnez le lien **Interface utilisateur de Storm**. Ce lien affiche les informations sur le cluster, ainsi que les topologies en cours d’exécution.

![l’interface utilisateur Storm][storm-dashboard-ui]

> [AZURE.NOTE] Avec certaines versions d'Internet Explorer, vous constaterez peut-être que l'interface utilisateur Storm n'est pas actualisée après votre première visite. Par exemple, elle n’affiche pas les nouvelles topologies que vous avez soumises, ou indique qu’une topologie est active alors que vous l’avez précédemment arrêtée. Microsoft est conscient de ce problème et recherche actuellement une solution.

####Page principale

La page principale de l’interface utilisateur de Storm fournit les informations suivantes :

* **Résumé du cluster** : des informations de base sur le cluster Storm.

* **Résumé de la topologie** : une liste des topologies en cours d’exécution. Utilisez les liens de cette section pour afficher plus d’informations sur les topologies spécifiques.

* **Résumé du superviseur**: des informations sur le superviseur Storm.

* **Configuration Nimbus** : configuration Nimbus du cluster.

####Résumé de la topologie

La sélection d’un lien de la section **Résumé de la topologie** affiche les informations suivantes sur la topologie :

* **Résumé de la topologie** :des informations de base sur la topologie.

* **Actions de la topologie** : les actions de gestion que vous pouvez effectuer sur la topologie.

	* **Activer** : reprend le traitement d’une topologie arrêtée.

	* **Désactiver** : suspend une topologie en cours d’exécution.

	* **Rééquilibrer**: ajuste le parallélisme de la topologie. Il convient de rééquilibrer les topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Cela permet à la topologie d’ajuster le parallélisme pour compenser l’augmentation ou la diminution du nombre de nœuds du cluster.

		Pour plus d’informations, consultez la rubrique <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Présentation du parallélisme d’une topologie Storm</a>.

	* **Supprimer** : met fin à une topologie Storm après expiration du délai spécifié.

* **Topology stats** : statistiques relatives à la topologie. Utilisez les liens de la colonne **Fenêtre** pour définir l’intervalle de temps des entrées restantes sur la page.

* **Spouts** : les spouts utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur des spouts spécifiques.

* **Bolts** : les bolts utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur des bolts spécifiques.

* **Configuration de la topologie** : configuration de la topologie sélectionnée.

####Résumé relatif aux spouts et aux bolts

La sélection d’un spout à partir de la section **Spouts** ou **Bolts** affiche les informations suivantes sur l’élément sélectionné :

* **Résumé du composant** : des informations de base sur le spout ou le bolt.

* **Statistiques du spout/bolt** : des statistiques relatives au spout ou au bolt. Utilisez les liens de la colonne **Fenêtre** pour définir l’intervalle de temps des entrées restantes sur la page.

* **Statistiques d’entrée** (bolt uniquement) : des informations sur les flux d’entrée consommés par le bolt.

* **Statistiques de sortie** : des informations sur les flux de données émis par ce spout ou ce bolt.

* **Exécuteurs** : informations sur les instances du spout ou du bolt. Sélectionnez l’entrée **Port** d’un exécuteur spécifique afin d’afficher le journal des informations de diagnostic généré pour cette instance.

* **Erreurs** : les informations d’erreur pour ce spout ou ce bolt.

##Outils HDInsight pour Visual Studio

Les outils HDInsight permettent de soumettre des topologies C# ou hybrides à votre cluster Storm. La procédure suivante utilise un exemple d’application. Pour plus d’informations sur la création de vos propres topologies à l’aide des outils HDInsight, consultez [Développement de topologies C# à l’aide des outils HDInsight pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Utilisez les étapes suivantes pour déployer un exemple sur votre Storm sur le cluster HDInsight, puis afficher et gérer la topologie.

1. Si vous n’avez pas encore installé la dernière version des outils HDInsight pour Visual Studio, consultez <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Prise en main de HDInsight Tools pour Visual Studio</a>.

2. Ouvrez Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.

3. Dans la boîte de dialogue **Nouveau projet**, développez **Installé** > **Modèles**, puis sélectionnez **HDInsight**. Dans la liste des modèles, sélectionnez **Exemple Storm**. En bas de la boîte de dialogue, entrez un nom pour l’application.

	![image](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet et sélectionnez **Envoyer à Storm sur HDInsight**.

	> [AZURE.NOTE] Si vous y êtes invité, entrez les informations d'identification de connexion à votre abonnement Azure. Si vous disposez de plusieurs abonnements, connectez-vous à celui qui contient votre cluster Storm dans HDInsight.

2. Sélectionnez votre Storm sur le cluster HDInsight à partir de la liste déroulante **Cluster Storm**, puis sélectionnez **Envoyer**. Vous pouvez contrôler si l’envoi a bien été effectué à l’aide de la fenêtre **Sortie**.

3. Lorsque la topologie a été envoyée avec succès, les **Topologies Storm** du cluster doivent apparaître. Sélectionnez la topologie à partir de la liste pour afficher des informations sur la topologie en cours d’exécution.

	![Visual Studio Monitor](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

	> [AZURE.NOTE] Vous pouvez également afficher les **Topologies Storm** depuis l’**Explorateur de serveurs** en développant **Azure** > **HDInsight**, puis en cliquant avec le bouton droit sur le cluster HDInsight et en sélectionnant **Affichage des topologies Storm **.

	Sélectionnez la forme des spouts et bolts pour afficher des informations sur ces composants. Une nouvelle fenêtre s’ouvrira pour chaque élément sélectionné.
    
    > [AZURE.NOTE] Le nom de la topologie représente le nom de classe de la topologie (dans ce cas, `HelloWord`,) avec ajout d’un horodatage.

4. À partir de l’affichage du **Résumé de la topologie**, sélectionnez **Supprimer** pour arrêter la topologie.

	> [AZURE.NOTE] Les topologies Storm poursuivent leur exécution jusqu'à ce qu'elles soient arrêtées ou que le cluster soit supprimé.

##API REST

L’interface utilisateur Storm repose sur l’API REST, ce qui vous permet de profiter de fonctionnalités de gestion et de surveillance similaires à l’aide de l’API REST. À l'aide de l'API REST, vous pouvez créer des outils personnalisés pour gérer et surveiller les topologies Storm.

Pour plus d’informations, consultez la rubrique [API REST de l’interface utilisateur Storm](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). Les informations suivantes sont spécifiques à l’utilisation de l’API REST avec Apache Storm sur HDInsight.

###URI de base

L’URI de base pour l’API REST sur les clusters HDInsight est **https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/**, où **clustername** est le nom de votre Storm sur le cluster HDInsight.

###Authentification

Les requêtes à l’API REST doivent utiliser l’**authentification de base** avec le nom et le mot de passe d’administrateur de cluster HDInsight.

> [AZURE.NOTE] Étant donné que l’authentification de base est envoyée en texte clair, vous devez **toujours** utiliser HTTPS pour sécuriser les communications avec le cluster.

###Valeurs de retour

Les informations renvoyées par l’API REST sont uniquement utilisables au sein du cluster ou des machines virtuelles sur le même réseau virtuel Azure que le cluster. Par exemple, le nom de domaine complet (FQDN) retourné pour les serveurs Zookeeper ne sera pas accessible à partir d’Internet.

##Étapes suivantes

Maintenant que vous avez appris à déployer et surveiller des topologies à l’aide du tableau de bord Storm, découvrez comment :

* [Développer des topologies C# à l’aide des outils HDInsight pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Développer des topologies basées sur Java à l’aide de Maven](hdinsight-storm-develop-java-topology.md)

Pour accéder à une liste d’exemples supplémentaires de topologies, consultez la rubrique [Exemples de topologies Storm sur HDInsight](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png

<!---HONumber=AcomDC_0727_2016-->