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
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Connecteur Microsoft HDInsight #

Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d'un flux. Le connecteur HDInsight vous permet de créer un cluster Hadoop sur Azure et de soumettre plusieurs tâches Hadoop telles que Hive, Pig, MapReduce et Streaming MapReduce. Le service Azure HDInsight déploie et approvisionne des clusters Hadoop Apache dans le cloud, en apportant une infrastructure logicielle conçue pour gérer et analyser des données volumineuses, et générer des rapports à leur sujet. Hadoop Core fournit un mode de stockage de données fiable grâce au système HDFS (Hadoop Distributed File System), ainsi qu'un modèle de programmation simple MapReduce destiné à traiter et à analyser, en parallèle, les données stockées dans ce système distribué. Grâce au connecteur HDInsight, vous pouvez faire tourner un cluster, soumettre un travail et attendre que ce travail soit terminé.

###Actions de base
		
- Créer un cluster
- Attendre la création du cluster
- Soumettre un travail Pig
- Soumettre un travail Hive
- Soumettre un travail MapReduce
- Attendre la fin du travail
- Supprimer un cluster


## Créer une instance de l'application API du connecteur HDInsight ##

Pour utiliser le connecteur HDInsight, vous devez créer une instance de l'application API de ce connecteur. Pour cela, procédez comme suit :

1. Ouvrez Azure Marketplace à l'aide de l'option '+ NOUVEUA' située en bas à gauche du portail Azure.
2. Accédez à " Web et mobilité > API Apps " et recherchez " Connecteur HDInsight ".
3. Fournissez les détails génériques comme le nom, le plan de service de l'application, etc. dans le premier panneau.
4. Dans les paramètres du package, indiquez le nom d'utilisateur et le mot de passe du cluster HDInsight.


 ![][1]  

## Configuration de certificat (facultatif) ##

Remarque : cette étape n'est requise que si vous souhaitez effectuer des opérations de gestion (création et suppression de clusters) dans l'application logique.

Accédez à l'application API récemment créée en sélectionnant Parcourir -> API Apps -> <Nom de l'application API créée>. Le composant  'Sécurité' indique 0, ce qui signifie qu'aucun certificat de gestion n'est chargé.

![][2] 

Pour charger le certificat de gestion dans votre application API, vous procédez comme suit.
1. Cliquez sur le composant  'Sécurité'.
2. Cliquez sur  Charger le certificat' dans la lame  'Sécurité' qui s'ouvre.
3. Naviguez et sélectionnez le fichier de certificat dans la lame suivante.
4. Une fois le certificat sélectionné, cliquez sur OK.

Une fois le certificat chargé, ces informations s'affichent.

![][3] 

Remarque : si vous souhaitez modifier le certificat, vous pouvez en charger un autre qui va remplacer celui-ci.

## Utilisation dans une application logique ##

Le connecteur HDInsight ne peut être utilisé que comme une action dans l'application logique. Prenons une application logique simple qui crée un cluster, exécute une tâche  'Hive' et supprime le cluster à l'issue de cette tâche.


- Lors de la création ou de la modification d'une application logique, choisissez l'application API du connecteur HDInsight créé comme action. Elle montre toutes les actions disponibles.

![][5] 


- Sélectionnez  'Créer le cluster', indiquez tous les paramètres requis du cluster et cliquez sur ✓.

![][6] 



- L'action s'affiche maintenant comme configurée dans l'application logique. La ou les sorties de l'action s'affichent et peuvent être utilisées comme des entrées dans une action suivante. 

![][7] 



- Sélectionnez le même connecteur HDInsight dans la galerie, comme action. Sélectionnez l'action  'Attendre la création du cluster', indiquez tous les paramètres requis et cliquez sur ✓.

![][8] 



- Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action  'Soumettre la tâche Hive', indiquez tous les paramètres requis et cliquez sur ✓.

![][9] 



- Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action  'Attendre la fin de la tâche', indiquez tous les paramètres requis et cliquez sur ✓.

![][10] 



- Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action  'Soumettre la tâche Hive', indiquez tous les paramètres requis et cliquez sur ✓.

![][11] 


Vous pouvez cliquer sur  'Exécuter' pour démarrer l'application logique manuellement et tester le scénario.

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


<!--HONumber=52-->