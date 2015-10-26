<properties
   pageTitle="Utilisation du connecteur HDInsight dans des applications logiques | Microsoft Azure App Service"
   description="Comment créer et configurer le connecteur HDInsight ou une application API et l'utiliser dans une application logique d’Azure App Service"
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
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# Utilisation et ajout du connecteur HDInsight dans votre application logique
Le connecteur HDInsight vous permet de créer un cluster Hadoop sur Azure et de soumettre plusieurs tâches Hadoop telles que Hive, Pig, MapReduce et Streaming MapReduce. Le service Azure HDInsight déploie et approvisionne des clusters Hadoop Apache dans le cloud, en apportant une infrastructure logicielle conçue pour gérer et analyser des données volumineuses, et générer des rapports à leur sujet. Hadoop Core fournit un mode de stockage de données fiable grâce au système HDFS (Hadoop Distributed File System), ainsi qu'un modèle de programmation simple MapReduce destiné à traiter et à analyser, en parallèle, les données stockées dans ce système distribué. Grâce au connecteur HDInsight, vous pouvez créer ou supprimer un cluster, soumettre un travail et attendre que ce travail soit terminé.

Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d’un flux. Vous pouvez ajouter le connecteur HDInsight à votre flux d’entreprise et traiter les données dans le cadre de ce flux de travail dans une application logique.

### Actions de base

- Créer un cluster
- Attendre la création du cluster
- Soumettre un travail Pig
- Soumettre un travail Hive
- Soumettre un travail MapReduce
- Attendre la fin du travail
- Supprimer un cluster


## Créer l’application API du connecteur HDInsight ##

Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur HDInsight », sélectionnez-le et sélectionnez **Créer**.
3. Entrez le nom, le plan App Service et d'autres propriétés.
4. Dans les paramètres du package, entrez le nom d’utilisateur et le mot de passe du cluster HDInsight. Sélectionnez **OK**.
5. Sélectionnez **Créer** : ![][1]  

## Configuration de certificat (facultatif) ##

> [AZURE.NOTE]cette étape n’est requise que si vous souhaitez effectuer des opérations de gestion (création et suppression de clusters) dans l’application logique.

Accédez à l'application API Connecteur HDInsight que vous venez de créer. Vous verrez que le composant « Sécurité » indique 0, ce qui signifie qu'aucun certificat de gestion n'a été téléchargé : ![][2]

Pour charger le certificat de gestion dans votre application API :

1. Sélectionnez le composant « Sécurité ».
2. Dans le panneau « Sécurité », sélectionnez **TÉLÉCHARGER LE CERTIFICAT**.
3. Naviguez et sélectionnez le fichier de certificat dans le panneau suivant.
4. Une fois le certificat sélectionné, sélectionnez **OK**.

Une fois le certificat chargé, ces informations s’affichent : ![][3]

> [AZURE.NOTE]si vous souhaitez modifier le certificat, vous pouvez en charger un autre qui remplace le certificat existant.

## Utilisation du connecteur dans une application logique ##

Le connecteur HDInsight ne peut être utilisé que comme une action dans l’application logique. Prenons une application logique simple qui crée un cluster, exécute une tâche « Hive » et supprime le cluster à l'issue de cette tâche.


1. Dans la carte « Démarrer la logique », cliquez sur « Exécuter cette logique manuellement ».
2. Sélectionnez l'application API du connecteur HDInsight créée à partir de la galerie. Les actions disponibles sont listées : ![][5]

3. Sélectionnez « Créer un cluster », entrez tous les paramètres requis du cluster et sélectionnez ✓ : ![][6]

4. L’action s'affiche maintenant comme configurée dans l'application logique. La ou les sorties de l’action s’affichent et peuvent être utilisées comme des entrées dans les actions suivantes : ![][7]

5. Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action « Attendre la création du cluster », entrez tous les paramètres requis et sélectionnez ✓ : ![][8]

6. Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action « Soumettre un travail Hive », entrez tous les paramètres requis et sélectionnez ✓ : ![][9]

7. Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action « Attendre la fin du travail », entrez tous les paramètres requis et sélectionnez ✓ : ![][10]

8. Sélectionnez le même connecteur HDInsight dans la galerie comme action. Sélectionnez l'action « Supprimer un cluster », entrez tous les paramètres requis et sélectionnez ✓ : ![][11]

9. Enregistrez l'application logique à l'aide de la commande de sauvegarde située en haut du concepteur.

Pour tester le scénario, sélectionnez **Exécuter maintenant** afin de démarrer l'application logique manuellement.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d'API REST Swagger sur [Référence de connecteurs et d'applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).


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

<!---HONumber=Oct15_HO3-->