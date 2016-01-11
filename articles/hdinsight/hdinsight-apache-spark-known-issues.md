<properties 
	pageTitle="Problèmes connus d’Apache Spark dans HDInsight | Microsoft Azure" 
	description="Problèmes connus d’Apache Spark dans HDInsight" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2015" 
	ms.author="jgao"/>

# Problèmes connus d’Apache Spark dans Azure HDInsight (Linux)

Ce document effectue le suivi de tous les problèmes connus pour la version préliminaire publique de Spark.

##Livy divulgue une session interactive
 
**Symptôme :**

Lorsque Livy est redémarré avec une session interactive (à partir d’Ambari ou à cause d’un redémarrage de la machine virtuelle du nœud principal 0) encore active, une session de travail interactive sera divulguée. Pour cette raison, de nouvelles tâches peuvent rester bloquées à l’état Accepté sans pouvoir être démarrées.

**Atténuation :**

Pour contourner ce problème, suivez la procédure ci-après :

1. SSH dans le nœud principal. 
2. Exécutez la commande suivante pour rechercher l’ID d’application des tâches interactives démarrées via Livy. 

        yarn application –list

    Le nom des tâches par défaut sera Livy si ces tâches ont été démarrées avec une session interactive Livy ne comportant aucun nom explicite spécifié. Pour la session Livy démarrée via le bloc-notes Jupyter, le nom de la tâche démarrera avec remotesparkmagics\_*.

3. Exécutez la commande suivante pour mettre fin à ces tâches.

        yarn application –kill <Application ID>

De nouvelles tâches commencent à être exécutées.

##Serveur d’historique Spark non démarré 

**Symptôme :**
 
Le serveur d’historique Spark ne démarre pas automatiquement après la création d’un cluster.

**Atténuation :**

Démarrez manuellement le serveur d’historique à partir d’Ambari.

##Démarrage du bloc-notes plus long que prévu 

**Symptôme :**

La première instruction du bloc-notes Jupyter à l’aide de Spark Magic peut nécessiter plusieurs minutes.

**Atténuation :**
 
Aucune solution de contournement. Cela nécessite parfois du temps.

##Impossible de personnaliser les configurations principales/de mémoire

**Symptôme :**
 
Vous ne pouvez pas spécifier d’autres configurations principales/de mémoire que la valeur par défaut des noyaux Spark/Pyspark.

**Atténuation :**
 
Cette fonctionnalité sera bientôt disponible.

##Délai d’attente du bloc-notes Jupyter lors de la création de la session

**Symptôme :**

Lorsque le cluster Spark manque de ressources, les noyaux Spark et Pyspark du bloc-notes Jupyter expirent en essayant de créer la session. Atténuations :

1. Libérez certaines ressources de votre cluster Spark :

    - Arrêtez les autres blocs-notes Spark en allant dans le menu Fermer et Arrêter ou en cliquant sur Arrêter dans l’explorateur du bloc-notes.
    - Arrêtez les autres applications Spark à partir de YARN.

2. Redémarrez le bloc-notes que vous tentiez de démarrer. Un nombre suffisant de ressources devrait désormais être disponible pour vous permettre de créer une session.

##Problème de format des résultats du bloc-notes

**Symptôme :**
 
Les résultats bloc-notes ne sont pas dans le bon format après l’exécution d’une cellule de noyaux Spark et Pyspark Jupyter. Cela comprend les résultats positifs de l’exécution de cellules ainsi que des stacktraces Spark ou d’autres erreurs.

**Atténuation :**
 
Ce problème sera résolu dans une version ultérieure.

##Erreurs de frappe dans les exemples de bloc-notes
 
- **Bloc-notes Python 4 (Analyse des journaux avec Spark à l’aide d’une bibliothèque personnalisée)**

    « Supposons que vous le copiez vers wasb:///example/data/iislogparser.py » devrait être « Supposons que vous le copiez vers wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py" ».

- **Bloc-notes Python 5 (Spark Machine Learning : analyse prédictive sur les données d’inspection alimentaire à l’aide de MLLib)**

    « Un aperçu rapide peut nous aider à examiner la distribution de ces résultats » contient du code incorrect qui ne s’exécutera pas. Il doit être modifié pour obtenir ce qui suit :

        countResults = df.groupBy('results').count().withColumnRenamed('count', 'cnt').collect() 
        labels = [row.results for row in countResults] 
        sizes = [row.cnt for row in countResults] 
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral'] 
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors) plt.axis('equal') 
        
- **Bloc-notes Python 5 (Spark Machine Learning : analyse prédictive sur les données d’inspection alimentaire à l’aide de MLLib)**

    Le commentaire final indique que le taux de faux négatifs et le taux de faux positifs s’élèvent respectivement à 12,6 et 16 %. Ces chiffres sont inexacts ; exécutez le code pour afficher le diagramme circulaire avec les véritables pourcentages.

- **Bloc-notes Python 6 et 7**

    La première cellule ne parvient pas à enregistrer la méthode sc.stop() à appeler lorsque le bloc-notes se ferme. Dans certaines circonstances, cela peut entraîner une divulgation des ressources Spark. Vous pouvez éviter cela en vous veillant à exécuter l’importation de atexit; atexit.register(lambda: sc.stop()) dans ces blocs-notes avant de les arrêter. Si vous avez perdu accidentellement des ressources, suivez les instructions ci-dessus pour arrêter mettre fin aux applications YARN divulguées.
     
## Problème d’autorisation dans le répertoire des journaux Spark 

**Symptôme :**
 
Lorsque hdiuser soumet une tâche avec spark-submit, il existe une erreur java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log (Autorisation refusée) et le journal du pilote n’est pas écrit.

**Atténuation :**
 
1. Ajoutez hdiuser au groupe Hadoop. 
2. Fournissez les autorisations 777 sur /var/log/spark après la création du cluster. 
3. Mettez à jour l’emplacement du journal Spark à l’aide d’Ambari pour obtenir un répertoire avec les autorisations 777.  
4. Exécutez spark-submit en tant que sudo. 

##Voir aussi

- [Vue d’ensemble : Apache Spark sur Azure HDInsight (Linux)](hdinsight-apache-spark-overview.md)
- [Prise en main : approvisionner Apache Spark sur HDInsight (Linux) et exécuter des requêtes interactives à l’aide de Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_1223_2015-->