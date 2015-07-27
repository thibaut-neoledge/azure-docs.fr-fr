<properties
   pageTitle="Connecteur HDInsight"
   description="Utilisation du connecteur HDInsight"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/02/2015"
   ms.author="sameerch"/>


# Connecteur Microsoft HDInsight #

Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d’un flux. Le connecteur HDInsight vous permet de créer un cluster Hadoop sur Azure et de soumettre plusieurs tâches Hadoop telles que Hive, Pig, MapReduce et Streaming MapReduce. Le service Azure HDInsight déploie et approvisionne des clusters Hadoop Apache dans le cloud, en apportant une infrastructure logicielle conçue pour gérer et analyser des données volumineuses, et générer des rapports à leur sujet. Hadoop Core fournit un mode de stockage de données fiable grâce au système HDFS (Hadoop Distributed File System), ainsi qu'un modèle de programmation simple MapReduce destiné à traiter et à analyser, en parallèle, les données stockées dans ce système distribué. Grâce au connecteur HDInsight, vous pouvez créer ou supprimer un cluster, soumettre un travail et attendre que ce travail soit terminé.

###Actions de base

- Créer un cluster
- Attendre la création du cluster
- Soumettre un travail Pig
- Soumettre un travail Hive
- Soumettre un travail MapReduce
- Attendre la fin du travail
- Supprimer un cluster


## Créer une instance de l’application API du connecteur HDInsight ##

Pour utiliser le connecteur HDInsight, vous devez créer une instance de l’application API de ce connecteur. Pour cela, procédez comme suit :

1. Ouvrez Azure Marketplace à l'aide de l’option « +NOUVEAU » située à gauche du portail Azure, puis sélectionnez « Marketplace ».
2. Accédez à « API Apps », recherchez « Connecteur HDInsight », sélectionnez cette option, puis cliquez sur « Créer ».
3. Fournissez les détails courants comme le nom, le plan de service de l'application, etc. dans le premier panneau.
4. Dans les paramètres du package, indiquez le nom d'utilisateur et le mot de passe du cluster HDInsight, puis cliquez sur « OK ».
5. Cliquez sur « Créer ».


 ![][1]

## Configuration de certificat (facultatif) ##

Remarque : cette étape n'est requise que si vous souhaitez effectuer des opérations de gestion (création et suppression de clusters) dans l'application logique.

Accédez à l'application API Connecteur HDInsight que vous venez de créer. Vous verrez que le composant « Sécurité » indique 0, ce qui signifie qu'aucun certificat de gestion n'a été téléchargé.

![][2]

Pour charger le certificat de gestion dans votre application API, procédez comme suit. 1. Cliquez sur le composant « Sécurité ». 2. Dans le panneau « Sécurité », cliquez sur « TÉLÉCHARGER LE CERTIFICAT ». 3. Naviguez et sélectionnez le fichier de certificat dans le panneau suivant. 4. Une fois le certificat sélectionné, cliquez sur OK.

Une fois le certificat chargé, ces informations s’affichent.

![][3]

Remarque : si vous souhaitez modifier le certificat, vous pouvez en charger un autre qui remplacera celui-ci.

## Utilisation dans une application logique ##

Le connecteur HDInsight ne peut être utilisé que comme une action dans l’application logique. Prenons une application logique simple qui crée un cluster, exécute une tâche « Hive » et supprime le cluster à l'issue de cette tâche.


- Dans la carte « Démarrer la logique », cliquez sur « Exécuter cette logique manuellement ».
- Sélectionnez l'application d'API de connecteur HDInsight créée à partir de la galerie pour afficher toutes les actions disponibles.

![][5]


- Sélectionnez « Créer le cluster », indiquez tous les paramètres requis du cluster et cliquez sur ✓.

![][6]



- L’action s’affiche maintenant comme configurée dans l’application logique. La ou les sorties de l’action s’affichent et peuvent être utilisées comme des entrées dans une action suivante.

![][7]



- Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action « Attendre la création du cluster », indiquez tous les paramètres requis et cliquez sur ✓.

![][8]



- Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action « Soumettre un travail Hive », indiquez tous les paramètres requis et cliquez sur ✓.

![][9]



- Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action « Attendre la fin de la tâche », indiquez tous les paramètres requis et cliquez sur ✓.

![][10]



- Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action « Supprimer le cluster », indiquez tous les paramètres requis et cliquez sur ✓.

![][11]

- Enregistrez l'application logique à l'aide de la commande de sauvegarde située en haut de l'interface du concepteur.

Vous pouvez cliquer sur « Exécuter maintenant » pour démarrer l'application logique manuellement et tester le scénario.

<!--Image references-->
[1]: ./media/app-service-logic-connector-hdinsight/Create.jpg
[2]: ./media/app-service-logic-connector-hdinsight/CertNotConfigured.jpg
[3]: ./media/app-service-logic-connector-hdinsight/CertConfigured.jpg
[5]: ./media/app-service-logic-connector-hdinsight/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-hdinsight/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-hdinsight/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-hdinsight/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-hdinsight/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-hdinsight/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-hdinsight/LogicApp7.jpg

<!---HONumber=July15_HO3-->