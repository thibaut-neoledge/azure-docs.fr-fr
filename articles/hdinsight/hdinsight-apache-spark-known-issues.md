<properties 
	pageTitle="Problèmes connus d’Apache Spark dans HDInsight | Microsoft Azure" 
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
	ms.date="02/17/2016" 
	ms.author="nitinme"/>

# Problèmes connus d’Apache Spark dans Azure HDInsight (Linux)

Ce document fait le suivi de tous les problèmes connus pour la version Preview publique de HDInsight Spark.

##Livy divulgue une session interactive
 
**Symptôme :**

Lorsque Livy est redémarré avec une session interactive (à partir d’Ambari ou à cause d’un redémarrage de la machine virtuelle du nœud principal 0) encore active, une session de travail interactive sera divulguée. Pour cette raison, de nouvelles tâches peuvent rester bloquées à l’état Accepté sans pouvoir être démarrées.

**Atténuation :**

Pour contourner ce problème, suivez la procédure ci-après :

1. SSH dans le nœud principal. 
2. Exécutez la commande suivante pour rechercher l’ID d’application des tâches interactives démarrées via Livy. 

        yarn application –list

    Le nom des tâches par défaut sera Livy si ces tâches ont été démarrées avec une session interactive Livy ne comportant aucun nom explicite spécifié. Pour la session Livy démarrée via le bloc-notes Jupyter, le nom de la tâche démarrera avec remotesparkmagics\_*.

3. Exécutez la commande suivante pour mettre fin à ces tâches.

        yarn application –kill <Application ID>

De nouvelles tâches commencent à être exécutées.

##Serveur d’historique Spark non démarré 

**Symptôme :**
 
Le serveur d’historique Spark ne démarre pas automatiquement après la création d’un cluster.

**Atténuation :**

Démarrez manuellement le serveur d’historique à partir d’Ambari.

##Erreur lors du chargement d’un bloc-notes de taille supérieure

**Symptôme :**

Vous pouvez obtenir une erreur **`Error loading notebook`** lorsque vous tentez de charger un bloc-notes de taille supérieure.

**Atténuation :**

Si vous obtenez cette erreur, cela ne signifie pas que vos données sont endommagées ou perdues. Vos blocs-notes sont toujours sur le disque, sous `/var/lib/jupyter`, et vous pouvez exécuter SSH dans le cluster pour y accéder. Vous pouvez copier les blocs-notes depuis le cluster vers votre ordinateur local (à l’aide de SCP ou WinSCP) pour en faire une sauvegarde afin d’éviter la perte de toutes les données importantes dans le bloc-notes. Vous pouvez ensuite créer un tunnel SSH dans votre nœud principal sur le port 8001, afin d’accéder à Jupyter sans avoir à passer par la passerelle. À partir de là, vous pouvez effacer la sortie de votre bloc-notes et l’enregistrer de nouveau pour réduire la taille du bloc-notes au minimum.

Pour éviter cette erreur à l’avenir, gardez en tête les conseils suivants :

* Il est important que la taille du bloc-notes reste réduite. Aucune sortie de vos travaux sous Spark qui est envoyée à Jupyter n’est conservée dans le bloc-notes. Pour Jupyter, il est en général recommandé d’éviter l’exécution de l’élément `.collect()` sur des RDD ou trames de données larges ; en revanche, si vous souhaitez lire le contenu d’un RDD, pensez à exécuter `.take()` ou `.sample()` afin que votre sortie ne devienne pas trop volumineuse.
* En outre, lorsque vous enregistrez un bloc-notes, supprimez toutes les cellules de sortie pour réduire sa taille.



##Démarrage du bloc-notes plus long que prévu 

**Symptôme :**

La première instruction du bloc-notes Jupyter à l’aide de Spark Magic peut nécessiter plusieurs minutes.

**Explication :**
 
Cela se produit lorsque la première cellule du code est exécutée. En arrière-plan, la configuration de la session est lancée, et les contextes Spark, SQL et Hive sont définis. Une fois ces contextes définis, la première instruction est exécutée et donne l'impression que l'instruction prend beaucoup de temps.

##Délai d’attente du bloc-notes Jupyter lors de la création de la session

**Symptôme :**

Lorsque le cluster Spark manque de ressources, les noyaux Spark et Pyspark du bloc-notes Jupyter expirent en essayant de créer la session.

**Atténuations :**

1. Libérez certaines ressources de votre cluster Spark :

    - Arrêtez les autres blocs-notes Spark en allant dans le menu Fermer et Arrêter ou en cliquant sur Arrêter dans l’explorateur du bloc-notes.
    - Arrêtez les autres applications Spark à partir de YARN.

2. Redémarrez le bloc-notes que vous tentiez de démarrer. Un nombre suffisant de ressources devrait désormais être disponible pour vous permettre de créer une session.

## Problème d’autorisation dans le répertoire des journaux Spark 

**Symptôme :**
 
Lorsque hdiuser soumet une tâche avec spark-submit, il existe une erreur java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log (Autorisation refusée) et le journal du pilote n’est pas écrit.

**Atténuation :**
 
1. Ajoutez hdiuser au groupe Hadoop. 
2. Fournissez les autorisations 777 sur /var/log/spark après la création du cluster. 
3. Mettez à jour l’emplacement du journal Spark à l’aide d’Ambari pour obtenir un répertoire avec les autorisations 777.  
4. Exécutez spark-submit en tant que sudo. 

##Voir aussi

- [Vue d’ensemble : Apache Spark sur Azure HDInsight (Linux)](hdinsight-apache-spark-overview.md)
- [Prise en main : approvisionner Apache Spark sur HDInsight (Linux) et exécuter des requêtes interactives à l’aide de Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_0224_2016-->