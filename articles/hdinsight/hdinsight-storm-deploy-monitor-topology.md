---
title: "Déploiement et gestion des topologies Apache Storm sur HDInsight | Microsoft Docs"
description: "Apprenez à déployer, surveiller et gérer des topologies Apache Storm à l’aide du tableau de bord Storm sur HDInsight. Utilisez les outils Hadoop pour Visual Studio."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5e542072-f014-42aa-82d6-2694a76df520
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: ed825353b3a042fe79985f4c79ec476dbd7cf5a5
ms.lasthandoff: 03/07/2017


---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Déploiement et gestion des topologies Apache Storm sur HDInsight Windows

Le tableau de bord Storm vous permet de déployer et d’exécuter des topologies Apache Storm sur votre cluster HDInsight à l’aide votre navigateur web. Vous pouvez également utiliser le tableau de bord pour surveiller et gérer des topologies en cours d’exécution. Si vous utilisez Visual Studio, les outils HDInsight pour Visual Studio fournissent des fonctionnalités similaires dans Visual Studio.

Le tableau de bord Storm et les fonctionnalités Storm des outils HDInsight s’appuient sur l’API REST Storm, qui peut être utilisée pour créer vos propres solutions d’analyse et de gestion.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un tableau de bord Storm sur un cluster HDInsight utilisant Windows comme système d’exploitation. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).
>
> Pour plus d’informations sur le déploiement et la gestion des topologies avec un cluster HDInsight utilisant Linux, consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="prerequisites"></a>Composants requis

* **Apache Storm sur HDInsight** : pour connaître les étapes de création d’un cluster, voir [Prise en main d’Apache Storm sur HDInsight](hdinsight-apache-storm-tutorial-get-started.md).

* Pour le **tableau de bord Storm**: un navigateur Web moderne qui prend en charge HTML5.

* Pour **Visual Studio** : le Kit de développement logiciel (SDK) Azure 2.5.1 ou une version ultérieure et les outils HDInsight pour Visual Studio. Consultez la rubrique [Prise en main de HDInsight Tools pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) pour installer et configurer les outils HDInsight pour Visual Studio.

    L’une des versions suivantes de Visual Studio :

  * Visual Studio 2012 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (toute édition)

  * Visual Studio 2017 (toute édition)

## <a name="storm-dashboard"></a>Tableau de bord Storm

Le tableau de bord Storm est une page web disponible sur votre cluster Storm. L’URL est **https://&lt;clustername>.azurehdinsight.net/**, où **clustername** est le nom de votre Storm sur le cluster HDInsight.

Dans la partie supérieure du tableau de bord Storm, sélectionnez **Soumettre la topologie**. Suivez les instructions affichées sur la page pour exécuter une topologie d’exemple ou pour télécharger et exécuter une topologie que vous avez créée.

![la page d’envoi de la topologie][storm-dashboard-submit]

### <a name="storm-ui"></a>Interface utilisateur de Storm

Dans le tableau de bord Storm, sélectionnez le lien **Interface utilisateur de Storm** . Ce lien affiche les informations sur le cluster, ainsi que les topologies en cours d’exécution.

![l’interface utilisateur Storm][storm-dashboard-ui]

> [!NOTE]
> Avec certaines versions d'Internet Explorer, vous constaterez peut-être que l'interface utilisateur Storm n'est pas actualisée après votre première visite. Par exemple, elle n’affiche pas les nouvelles topologies que vous avez soumises, ou indique qu’une topologie est active alors que vous l’avez précédemment arrêtée. Microsoft est conscient de ce problème et recherche actuellement une solution.

#### <a name="main-page"></a>Page principale

La page principale de l’interface utilisateur de Storm fournit les informations suivantes :

* **Résumé du cluster**: des informations de base sur le cluster Storm.

* **Résumé de la topologie**: une liste des topologies en cours d’exécution. Utilisez les liens de cette section pour afficher plus d’informations sur les topologies spécifiques.

* **Résumé du superviseur**: des informations sur le superviseur Storm.

* **Configuration Nimbus**: configuration Nimbus du cluster.

#### <a name="topology-summary"></a>Résumé de la topologie

La sélection d’un lien de la section **Résumé de la topologie** affiche les informations suivantes sur la topologie :

* **Résumé de la topologie**:des informations de base sur la topologie.

* **Actions de la topologie**: les actions de gestion que vous pouvez effectuer sur la topologie.

  * **Activer**: reprend le traitement d’une topologie arrêtée.

  * **Désactiver**: suspend une topologie en cours d’exécution.

  * **Rééquilibrer**: ajuste le parallélisme de la topologie. Il convient de rééquilibrer les topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Cela permet à la topologie d’ajuster le parallélisme pour compenser l’augmentation ou la diminution du nombre de nœuds du cluster.

      Pour plus d’informations, consultez [Présentation du parallélisme d’une topologie Storm (http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

  * **Supprimer**: met fin à une topologie Storm après expiration du délai spécifié.

* **Topology stats**: statistiques relatives à la topologie. Utilisez les liens de la colonne **Fenêtre** pour définir l’intervalle de temps des entrées restantes sur la page.

* **Spouts**: les spouts utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur des spouts spécifiques.

* **Bolts**: les bolts utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur des bolts spécifiques.

* **Configuration de la topologie**: configuration de la topologie sélectionnée.

#### <a name="spout-and-bolt-summary"></a>Résumé relatif aux spouts et aux bolts

La sélection d’un spout à partir de la section **Spouts** ou **Bolts** affiche les informations suivantes sur l’élément sélectionné :

* **Résumé du composant**: des informations de base sur le spout ou le bolt.

* **Statistiques du spout/bolt**: des statistiques relatives au spout ou au bolt. Utilisez les liens de la colonne **Fenêtre** pour définir l’intervalle de temps des entrées restantes sur la page.

* **Statistiques d’entrée** (bolt uniquement) : des informations sur les flux d’entrée consommés par le bolt.

* **Statistiques de sortie**: des informations sur les flux de données émis par ce spout ou ce bolt.

* **Exécuteurs**: informations sur les instances du spout ou du bolt. Sélectionnez l’entrée **Port** d’un exécuteur spécifique afin d’afficher le journal des informations de diagnostic généré pour cette instance.

* **Erreurs**: les informations d’erreur pour ce spout ou ce bolt.

## <a name="hdinsight-tools-for-visual-studio"></a>Outils HDInsight pour Visual Studio

Les outils HDInsight permettent de soumettre des topologies C# ou hybrides à votre cluster Storm. La procédure suivante utilise un exemple d’application. Pour plus d’informations sur la création de vos propres topologies à l’aide des outils HDInsight, consultez [Développement de topologies C# à l’aide des outils HDInsight pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Utilisez les étapes suivantes pour déployer un exemple sur votre Storm sur le cluster HDInsight, puis afficher et gérer la topologie.

1. Si vous n’avez pas encore installé la dernière version des outils HDInsight pour Visual Studio, consultez [Prise en main de HDInsight Tools pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Ouvrez Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.

3. Dans la boîte de dialogue **Nouveau projet**, développez **Installé** > **Modèles**, puis sélectionnez **HDInsight**. Dans la liste des modèles, sélectionnez **Exemple Storm**. En bas de la boîte de dialogue, entrez un nom pour l’application.

    ![image](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

4. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Envoyer à Storm sur HDInsight**.

   > [!NOTE]
   > Si vous y êtes invité, entrez les informations d’identification de connexion pour votre abonnement Azure. Si vous disposez de plusieurs abonnements, connectez-vous à celui qui contient votre cluster Storm dans HDInsight.

5. Sélectionnez votre Storm sur le cluster HDInsight dans la liste déroulante **Cluster Storm**, puis sélectionnez **Envoyer**. Vous pouvez contrôler si l’envoi a bien été effectué à l’aide de la fenêtre **Sortie** .

6. Lorsque la topologie a été envoyée avec succès, les **Topologies Storm** du cluster doivent apparaître. Sélectionnez la topologie à partir de la liste pour afficher des informations sur la topologie en cours d’exécution.

    ![Visual Studio Monitor](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > Vous pouvez également afficher les **Topologies Storm** dans l’**Explorateur de serveurs** en développant **Azure** > **HDInsight**, puis en cliquant avec le bouton droit sur le cluster HDInsight et en sélectionnant **Affichage des topologies Storm**.

    Sélectionnez la forme des spouts et bolts pour afficher des informations sur ces composants. Une nouvelle fenêtre s’ouvre pour chaque élément sélectionné.

   > [!NOTE]
   > Le nom de la topologie représente le nom de classe de la topologie (dans ce cas, `HelloWord`,) avec ajout d’un horodatage.

7. Dans la vue **Résumé de la topologie**, sélectionnez **Supprimer** pour arrêter la topologie.

   > [!NOTE]
   > Les topologies Storm poursuivent leur exécution jusqu'à ce qu'elles soient arrêtées ou que le cluster soit supprimé.


## <a name="rest-api"></a>API REST

L’interface utilisateur Storm repose sur l’API REST, ce qui vous permet de profiter de fonctionnalités de gestion et de surveillance similaires à l’aide de l’API REST. À l'aide de l'API REST, vous pouvez créer des outils personnalisés pour gérer et surveiller les topologies Storm.

Pour plus d’informations, consultez la rubrique [API REST de l’interface utilisateur Storm](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). Les informations suivantes sont spécifiques à l’utilisation de l’API REST avec Apache Storm sur HDInsight.

### <a name="base-uri"></a>URI de base

L’URI de base pour l’API REST sur les clusters HDInsight est **https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/**, où **clustername** est le nom de votre Storm sur le cluster HDInsight.

### <a name="authentication"></a>Authentification

Les requêtes à l’API REST doivent utiliser l’ **authentification de base**avec le nom et le mot de passe d’administrateur de cluster HDInsight.

> [!NOTE]
> Étant donné que l’authentification de base est envoyée en texte clair, vous devez **toujours** utiliser HTTPS pour sécuriser les communications avec le cluster.

### <a name="return-values"></a>Valeurs de retour

Les informations renvoyées par l’API REST sont uniquement utilisables au sein du cluster ou des machines virtuelles sur le même réseau virtuel Azure que le cluster. Par exemple, le nom de domaine complet (FQDN) retourné pour les serveurs Zookeeper n’est pas accessible à partir d’Internet.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à déployer et surveiller des topologies à l’aide du tableau de bord Storm, découvrez comment :

* [Développer des topologies C# à l’aide des outils HDInsight pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Développer des topologies basées sur Java à l’aide de Maven](hdinsight-storm-develop-java-topology.md)

Pour accéder à une liste d’exemples supplémentaires de topologies, consultez la rubrique [Exemples de topologies Storm sur HDInsight](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png

