<properties
   pageTitle="Déploiement d’un échantillonneur JUnit JMeter pour tester les performances d’Elasticsearch | Microsoft Azure"
   description="Comment utiliser un échantillonneur JUnit pour générer des données et les charger sur un cluster Elasticsearch."
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
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Déploiement d’un échantillonneur JUnit JMeter pour tester les performances d’Elasticsearch

Cet article fait [partie d’une série](guidance-elasticsearch.md).

Ce document décrit comment créer et utiliser un échantillonneur JUnit qui peut générer des données et les charger sur un cluster Elasticsearch dans le cadre d’un plan de test JMeter. Offrant une très grande souplesse, cette approche du test de charge peut générer de grandes quantités de données de test sans le recours à des fichiers de données externes.

> [AZURE.NOTE] Les tests de charge utilisés pour évaluer les performances d’ingestion de données comme décrit dans le document « Optimisation des performances d’ingestion de données avec Elasticsearch sur Azure » ont été construits à l’aide de cette approche. Les détails du code JUnit sont décrits dans ce document.

Pour tester les performances d’ingestion de données, le code JUnit a été développé à l’aide d’Eclipse (Mars), et les dépendances ont été résolues à l’aide de Maven. Les procédures suivantes décrivent le processus étape par étape pour installer Eclipse, configurer Maven, créer un test JUnit et déployer ce test en tant qu’échantillonneur de demandes JUnit dans un test JMeter.

> [AZURE.NOTE] Pour plus d’informations sur la structure et la configuration de l’environnement de test, consultez [Création d’un environnement de test de performances pour Elasticsearch sur Azure][].

## Installation des composants requis

Votre ordinateur de développement doit être doté de [Java Runtime Environment](http://www.java.com/en/download/ie_manual.jsp). Vous devez également installer l’[IDE Eclipse pour les développeurs Java](https://www.eclipse.org/downloads/index.php?show_instructions=TRUE).

> [AZURE.NOTE] Si vous utilisez la machine virtuelle principale JMeter décrite dans [Création d’un environnement de test de performances pour Elasticsearch sur Azure][] en tant qu’environnement de développement, téléchargez la version Windows 32 bits du programme d’installation d’Eclipse.

## Création d’un projet de test JUnit à des fins de test de charge dans Elasticsearch

Démarrez l’IDE Eclipse, s’il n’est pas déjà en cours d’exécution, puis fermez la page *Welcome*. Dans le menu *File*, cliquez sur *New*, puis sur *Java Project*.

![](./media/guidance-elasticsearch/jmeter-deploy7.png)

Dans la fenêtre *New Java Project*, entrez un nom de projet, sélectionnez *Use default JRE*, puis cliquez sur *Finish*.

![](./media/guidance-elasticsearch/jmeter-deploy8.png)

Dans la fenêtre *Package Explorer*, développez le nœud nommé d’après votre projet. Vérifiez qu’il contient un dossier nommé *src* et une référence à l’environnement JRE spécifié.

![](./media/guidance-elasticsearch/jmeter-deploy9.png)

Cliquez avec le bouton droit sur le dossier *src*, cliquez sur *New*, puis cliquez sur *JUnit Test Case*.

![](./media/guidance-elasticsearch/jmeter-deploy10.png)

Dans la fenêtre *New JUnit Test Case*, sélectionnez *New Junit 4 test*, entrez un nom pour le package (qui peut être le même que celui du projet à la différence que, par convention, il doit commencer par une lettre minuscule), un nom pour la classe de test, puis sélectionnez les options qui génèrent les stubs de méthode nécessaires pour votre test. Laissez la zone *Class under test* vide, puis cliquez sur *Finish*.

![](./media/guidance-elasticsearch/jmeter-deploy11.png)

Si la boîte de dialogue *New JUnit Test Case* suivante s’affiche, spécifiez l’option permettant d’ajouter la bibliothèque JUnit 4 au chemin de génération, puis cliquez sur *OK*.

![](./media/guidance-elasticsearch/jmeter-deploy12.png)

Vérifiez que le code de squelette pour le test JUnit est généré et affiché dans la fenêtre de l’éditeur Java.

![](./media/guidance-elasticsearch/jmeter-deploy13.png)

Dans *Package Explorer*, cliquez avec le bouton droit sur le nœud de votre projet, cliquez sur *Configure*, puis cliquez sur *Convert to Maven Project*.

> [AZURE.NOTE] En utilisant Maven, vous pouvez gérer plus facilement les dépendances externes (telles que les bibliothèques clientes Java Elasticsearch) dont dépend un projet.

![](./media/guidance-elasticsearch/jmeter-deploy14.png)

Dans la boîte de dialogue *Create new POM*, dans la zone de liste déroulante *Packaging*, sélectionnez *jar*, puis cliquez sur *Finish*.

![](./media/guidance-elasticsearch/jmeter-deploy15.png)

Le volet qui apparaît au-dessous de l’éditeur POM affiche éventuellement l’avertissement selon lequel *le chemin de génération spécifie l’environnement d’exécution J2SE-1.5, avec lequel aucun environnement JRE installé dans l’espace de travail n’est strictement compatible*, selon la version de Java que vous avez installée sur votre ordinateur de développement. Si vous disposez d’une version de Java postérieure à la version 1.5, vous pouvez ignorer cet avertissement.

![](./media/guidance-elasticsearch/jmeter-deploy16.png)

Dans l’éditeur POM, développez *Properties* puis cliquez sur *Create*.

![](./media/guidance-elasticsearch/jmeter-deploy17.png)

Dans la boîte de dialogue *Add Property*, dans la zone *Name*, tapez *es.version*, dans la zone *Value* tapez *1.7.2*, puis cliquez sur *OK*. C’est la version de la bibliothèque cliente Java Elasticsearch à utiliser (cette version pouvant être remplacée à l’avenir, le fait de définir la version en tant que propriété POM et de référencer cette propriété ailleurs dans le projet permet de modifier la version rapidement).

![](./media/guidance-elasticsearch/jmeter-deploy18.png)

Cliquez sur l’onglet *Dependencies* à la base de l’éditeur POM, puis cliquez sur *Add* en regard de la zone de liste *Dependencies*.

![](./media/guidance-elasticsearch/jmeter-deploy19.png)

Dans la boîte de dialogue *Select Dependency*, dans la zone *Group Id*, tapez *org.elasticsearch*, dans la zone *Artifact Id*, tapez *elasticsearch*, dans la zone *Version*, tapez *\\${es.version}*, puis cliquez sur *OK*. Les informations sur la bibliothèque cliente Java Elasticsearch sont conservées dans le référentiel central Maven en ligne ; grâce à cette configuration, la bibliothèque et ses dépendances sont automatiquement téléchargées quand le projet est généré.

![](./media/guidance-elasticsearch/jmeter-deploy20.png)

Dans le menu *File*, cliquez sur *Save All*. Cette action enregistre et génère le projet, en téléchargeant les dépendances spécifiées par Maven. Vérifiez que le dossier *Maven Dependencies* s’affiche dans Package Explorer. Développez ce dossier pour afficher les fichiers jar téléchargés qui assurent la prise en charge de la bibliothèque cliente Java Elasticsearch.

![](./media/guidance-elasticsearch/jmeter-deploy21.png)

## Importation d’un projet de test JUnit existant dans Eclipse

Cette procédure suppose que vous avez téléchargé un projet Maven précédemment créé à l’aide d’Eclipse.

Démarrez l’IDE d’Eclipse. Dans le menu *File*, cliquez sur *Import*.

![](./media/guidance-elasticsearch/jmeter-deploy22.png)

Dans la fenêtre *Select*, développez le dossier *Maven*, cliquez sur *Existing Maven Projects*, puis cliquez sur *Next*.

![](./media/guidance-elasticsearch/jmeter-deploy23.png)

Dans la fenêtre *Maven Projects*, spécifiez le dossier contenant le projet (dossier comportant le fichier pom.xml), cliquez sur *Select All*, puis cliquez sur *Finish*.

![](./media/guidance-elasticsearch/jmeter-deploy24.png)

Dans la fenêtre *Package Explorer*, développez le nœud correspondant à votre projet. Vérifiez que le projet contient un dossier nommé *src*. Ce dossier contient le code source du test JUnit. Le projet peut être compilé et déployé en suivant les instructions ci-dessous.

![](./media/guidance-elasticsearch/jmeter-deploy25.png)

## Déploiement d’un test JUnit sur JMeter

Ce projet suppose que vous avez créé un projet nommé LoadTest contenant une classe de test JUnit nommée `BulkLoadTest.java` qui accepte des paramètres de configuration transmis sous la forme d’une seule chaîne à un constructeur (mécanisme normalement utilisé du point de vue de JMeter).

Dans l’IDE Eclipse, dans Package Explorer, cliquez avec le bouton droit sur le nœud de projet, puis cliquez sur *Export*.

![](./media/guidance-elasticsearch/jmeter-deploy26.png)

Dans l’*Assistant d’exportation*, dans la page *Select*, développez le nœud *Java*, cliquez sur *JAR file*, puis cliquez sur *Next*.

![](./media/guidance-elasticsearch/jmeter-deploy27.png)

Dans la page *JAR File Specification*, dans la zone *Select the resources to export*, développez le projet en cours d’exportation et désélectionnez tout sauf le dossier *src*, désélectionnez *.classpath*, désélectionnez *.project* et désélectionnez *pom.xml*. Dans la zone *JAR file*, fournissez un nom de fichier et un emplacement pour le fichier JAR (en lui affectant l’extension de fichier .jar), puis cliquez sur *Finish*.

![](./media/guidance-elasticsearch/jmeter-deploy28.png)

À l’aide de l’Explorateur Windows, copiez le fichier JAR que vous venez de créer dans la machine virtuelle Java principale JMeter et enregistrez-le dans le dossier *apache-jmeter-2.13\\lib\\junit*, sous le dossier où vous avez installé JMeter (pour plus d’informations, consultez la procédure [Création de la machine virtuelle principale JMeter](guidance-elasticsearch-creating-performance-testing-environment.md#creating-the-jmeter-master-virtual-machine).)

Revenez à Eclipse et développez la fenêtre Package Explorer ; le dossier *Maven Dependencies* recense tous les fichiers JAR et leurs emplacements. Les fichiers répertoriés dans l’image suivante peuvent varier selon la version d’Elasticsearch que vous utilisez :

![](./media/guidance-elasticsearch/jmeter-deploy29.png)

À l’aide de l’Explorateur Windows, copiez chaque fichier JAR référencé dans le dossier Maven Dependencies dans le dossier *apache-jmeter-2.13\\lib\\junit* sur la machine virtuelle principale JMeter.

Si le dossier *lib\\junit* contient déjà d’anciennes versions de ces fichiers JAR, supprimez-les. Si vous les laissez en place, le test JUnit risque de ne pas fonctionner, car des références peuvent être résolues sur des fichiers JAR incorrects.

Sur la machine virtuelle principale JMeter, arrêtez JMeter s’il est en cours d’exécution. Démarrez JMeter. Dans JMeter, cliquez avec le bouton droit sur *Test Plan*, cliquez sur *Add*, cliquez sur *Threads (Users)*, puis cliquez sur *Thread Group*.

![](./media/guidance-elasticsearch/jmeter-deploy30.png)

Sous le nœud *Test Plan*, cliquez avec le bouton droit sur *Thread-Group*, cliquez sur *Add*, cliquez sur *Sampler*, puis cliquez sur *JUnit Request*.

![](./media/guidance-elasticsearch/jmeter-deploy31.png)

Dans la page *JUnit Request*, sélectionnez *Search for JUnit 4 annotations (instead of JUnit 3)*. Dans la liste déroulante *Classname*, sélectionnez votre classe de test de charge JUnit (elle apparaît sous la forme *&lt;package&gt;.&lt;classe&gt;*), dans la zone de liste déroulante *Test Method*, sélectionnez la méthode de test JUnit (cette méthode effectue le travail associé au test et doit avoir été marquée avec l’annotation *@test* dans le projet Eclipse), puis entrez les valeurs à passer au constructeur dans la zone *Constructor String Label*. Les détails affichés dans l’image suivante ne sont que des exemples ; dans votre cas, les données correspondant à *Classname*, *Test Method* et *Constructor String Label* sont susceptibles de différer de celles indiquées.

![](./media/guidance-elasticsearch/jmeter-deploy32.png)

Si votre classe n’apparaît pas dans la zone de liste déroulante *Classname*, cela signifie probablement que le fichier JAR n’a pas été exporté correctement, qu’il n’a pas été placé dans le dossier *lib\\junit* ou que certains fichiers JAR dépendants font défaut dans le dossier *lib\\junit*. Dans ce cas, réexportez le projet à partir d’Eclipse, assurez-vous que vous avez sélectionné la ressource *src*, copiez le fichier JAR dans le dossier *lib\\junit*, puis vérifiez que vous avez copié tous les fichiers JAR dépendants répertoriés par Maven dans le dossier *lib*.

Fermez JMeter. Il est inutile d’enregistrer le plan de test. Copiez le fichier JAR contenant la classe de test JUnit dans le dossier */home/&lt;nom d’utilisateur&gt;/apache-jmeter-2.13/lib/junit* sur chaque machine virtuelle subordonnée JMeter (*&lt;nom d’utilisateur&gt;* est le nom de l’utilisateur administratif que vous avez spécifié quand vous avez créé la machine virtuelle ; pour plus d’informations, consultez [Création de machines virtuelles subordonnées JMeter](guidance-elasticsearch-creating-performance-testing-environment.md#creating-the-jmeter-subordinate-virtual-machines).)

Copiez les fichiers JAR dépendants nécessaires pour la classe de test JUnit dans le dossier `/home/[username]/apache-jmeter-2.13/lib/junit` sur chaque machine virtuelle subordonnée JMeter. Au préalable, supprimez de ce dossier toute ancienne version des fichiers JAR.

Vous pouvez recourir à l’utilitaire `pscp` pour copier les fichiers depuis un ordinateur Windows vers Linux.

[Création d’un environnement de test de performances pour Elasticsearch sur Azure]: guidance-elasticsearch-creating-performance-testing-environment.md

<!---HONumber=AcomDC_0224_2016-->