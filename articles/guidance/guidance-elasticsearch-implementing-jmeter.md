<properties
   pageTitle="Considérations relatives à l’implémentation d’un plan de test JMeter pour Elasticsearch | Microsoft Azure"
   description="Comment exécuter des tests de performances pour Elasticsearch avec JMeter."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>
   
# Considérations relatives à l’implémentation d’un plan de test JMeter pour Elasticsearch

Cet article fait [partie d’une série](guidance-elasticsearch-introduction.md).

Les tests de performances effectués par rapport à Elasticsearch ont été implémentés à l’aide de plans de test JMeter, ainsi que de code Java incorporé en tant que test JUnit pour effectuer des tâches telles que le chargement de données sur le cluster. Les plans de test et le code JUnit sont décrits dans les documents Optimisation des performances d’ingestion de données avec Elasticsearch sur Azure et Optimisation de l’agrégation de données et des performances des requêtes avec Elasticsearch sur Azure.

L’objectif de ce document est de résumer l’expérience clé acquise au cours de la construction et de l’exécution de ces plans de test. La page [JMeter Best Practices](http://jmeter.apache.org/usermanual/best-practices.html) (en anglais) du site web Apache JMeter contient des conseils plus généraux sur la façon d’utiliser JMeter efficacement.

## Implémentation d’un plan de test JMeter

La liste suivante résume les éléments à prendre en compte au moment de la création d’un plan de test JMeter :

- Créez un groupe de threads distinct pour chaque test que vous souhaitez effectuer. Un test peut comprendre plusieurs étapes, comprenant des contrôleurs de logique, des minuteurs, des préprocesseurs, des post-processeurs, des échantillonneurs et des écouteurs.

- Évitez de créer trop de threads dans un groupe de threads. Si le nombre de threads est excessif, JMeter échoue avec des exceptions de type « Mémoire insuffisante ». Il est préférable d’ajouter des serveurs subordonnés JMeter exécutant chacun un plus petit nombre de threads que de tenter d’exécuter un grand nombre de threads sur un seul serveur JMeter.

![](./media/guidance-elasticsearch-jmeter-testing1.png)

- Pour évaluer les performances du cluster, incorporez le plug-in [Perfmon Metrics Collector](http://jmeter-plugins.org/wiki/PerfMon/) dans le plan de test ; il s’agit d’un écouteur JMeter disponible en tant que plug-in JMeter standard. Enregistrez les données de performances brutes dans un ensemble de fichiers au format CSV et traitez-les quand le test est terminé. Cette approche est plus efficace et impose moins de contrainte sur JMeter que le traitement des données au fur et à mesure qu’elles sont capturées. Vous pouvez utiliser un outil tel qu’Excel pour importer les données et générer une gamme de graphiques à des fins analytiques.

![](./media/guidance-elasticsearch-jmeter-testing2.png)

Vous devez envisager de capturer les informations suivantes :

- Utilisation de l’UC pour chaque nœud du cluster Elasticsearch.

- Nombre d’octets lus par seconde à partir du disque pour chaque nœud.

- Si possible, pourcentage de temps processeur passé à attendre que les opérations d’E/S soient effectuées sur chaque nœud. Cette mesure n’est pas toujours possible pour les machines virtuelles Windows, mais pour Linux, vous pouvez créer une métrique personnalisée (métrique EXEC) qui exécute la commande d’environnement suivante pour appeler *vmstat* sur un nœud :

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

Le champ 16 dans la sortie de *vmstat* contient le temps processeur passé à attendre l’exécution des opérations d’E/S. Pour plus d’informations sur le fonctionnement de cette instruction, consultez la [commande vmstat](http://linuxcommand.org/man_pages/vmstat8.html).

- Nombre d’octets envoyés et reçus sur le réseau au niveau de chaque nœud.

- Utilisez des écouteurs de rapport d’agrégation distincts pour enregistrer les performances et la fréquence des opérations ayant réussi ou échoué. Capturez les données de réussite et d’échec dans différents fichiers.

![](./media/guidance-elasticsearch-jmeter-testing3.png)

- Simplifiez au maximum chaque cas de test JMeter pour pouvoir corréler directement des performances avec des actions de test spécifiques. Pour les cas de test qui exigent une logique complexe, envisagez d’encapsuler cette logique dans un test JUnit et utilisez l’échantillonneur de demandes JUnit dans JMeter pour exécuter le test.

- Utilisez l’échantillonneur de requêtes HTTP pour effectuer les opérations HTTP, telles que GET, POST, PUT ou DELETE. Par exemple, vous pouvez exécuter des recherches Elasticsearch en utilisant une requête POST et en fournissant les détails de la requête dans la zone *Body Data* :

![](./media/guidance-elasticsearch-jmeter-testing4.png)

- Pour faciliter la répétabilité et la réutilisation, paramétrez les plans de test JMeter. Vous pourrez ensuite utiliser un script pour automatiser l’exécution des plans de test.

## Implémentation d’un test JUnit

Vous pouvez incorporer du code complexe dans un plan de test JMeter en créant un ou plusieurs tests JUnit. Vous pouvez écrire un test JUnit à l’aide d’un IDE Java tel qu’Eclipse. Le document « Procédure : création et déploiement d’un échantillonneur JUnit JMeter pour tester les performances d’Elasticsearch » fournit des informations sur la façon de configurer un environnement de développement approprié.

La liste suivante récapitule certaines meilleures pratiques à suivre pendant l’écriture du code pour un test JUnit :

- Utilisez le constructeur de classe de test pour transmettre des paramètres d’initialisation dans le test. JMeter peut utiliser un constructeur qui accepte un argument de chaîne unique. Dans le constructeur, décomposez cet argument en chacun de ses éléments, comme illustré dans l’exemple de code suivant :

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/* JUnit test class constructor */
public ElasticSearchLoadTest2(String params) {
	/* params is a string containing a set of comma separated values for:
		hostName
		indexName
		typeName
		port
		clustername
		itemsPerBatch
	*/

    /* Parse the parameter string into an array of string items */
	String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
	String[] items = params.split(delims);

    /* Note: Parameter validation code omitted */

	/* Use the parameters to populate variables used by the test */
	hostName = items[0];
	indexName = items[1];
	typeName = items[2];
	port = Integer.parseInt(items[3]);
	clusterName = items[4];
	itemsPerBatch = Integer.parseInt(items[5]);

	if(itemsPerBatch == 0)
		itemsPerBatch = 1000;
}
```

- Évitez les opérations d’E/S ou coûteuses dans le constructeur ou la classe de test d’installation, car elles s’exécutent chaque fois que le test JUnit est exécuté (un même test JUnit peut s’exécuter plusieurs milliers de fois pour chaque test de performances exécuté à partir de JMeter).

- Envisagez une installation unique si l’initialisation des cas de test est gourmande en ressources.

- Si le test a besoin d’un nombre élevé de paramètres d’entrée, stockez les informations de configuration du test dans un fichier de configuration distinct et passez l’emplacement de ce fichier dans le constructeur.

- Si possible, ne codez pas en dur les chemins de fichier dans le code de test de charge, afin d’éviter les échecs dus aux différences entre les systèmes d’exploitation tels que Windows et Linux.

- Utilisez des assertions pour indiquer les échecs dans les méthodes de test JUnit afin de pouvoir effectuer leur suivi avec JMeter et les utiliser comme métriques métiers. Si possible, transmettez les informations concernant la cause de l’échec, comme illustré en gras dans l’exemple de code suivant :

```Java
@Test
public void bulkInsertTest() throws IOException {
	...
	BulkResponse bulkResponse = bulkRequest.execute().actionGet();
	assertFalse(
		bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
		...
}
```

<!---HONumber=AcomDC_0211_2016-->