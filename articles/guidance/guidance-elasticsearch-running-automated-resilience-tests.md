<properties
   pageTitle="Exécution des tests de résilience Elasticsearch automatisés | Microsoft Azure"
   description="Description de la façon dont vous pouvez exécuter les tests de résilience dans votre propre environnement."
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

# Exécution de tests de résilience Elasticsearch automatisés

Cet article fait [partie d’une série](guidance-elasticsearch.md).

[Resilience and Recovery Testing] (Test de résilience et de récupération) décrit une série de tests exécutée sur un cluster Elasticsearch et destinée à déterminer la façon dont le système a réagi à certains types de défaillances courants et le degré d’efficacité de la récupération. Quatre scénarios ont été testés :

- **Échec de nœud et redémarrage sans perte de données**. Un nœud de données est arrêté, puis redémarré après 5 minutes. Elasticsearch a été configuré pour ne pas réaffecter les partitions manquantes dans cet intervalle, donc aucune Entrée/Sortie supplémentaire n’est engagée dans le déplacement de partitions. Lorsque le nœud redémarre, le processus de récupération réactualise les partitions de ce nœud.

- **Échec de nœud avec perte de données grave**. Un nœud de données est arrêté et les données qu’il contient sont effacées pour simuler la défaillance de disque grave. Le nœud est ensuite redémarré (après 5 minutes), et agit en fait en tant que remplacement du nœud d’origine. Le processus de récupération exige la reconstruction des données manquantes pour ce nœud, et peut impliquer la relocalisation des partitions présentes sur d’autres nœuds.

- **Échec de nœud et redémarrage sans perte de données, mais avec réaffectation des partitions**. Un nœud de données est arrêté et les partitions qu’il contient sont réaffectées à d’autres nœuds. Le nœud est redémarré et d’autres réaffectations ont lieu pour rééquilibrer le cluster.

- **Mises à jour propagées**. Chaque nœud du cluster est arrêté et redémarré après un court intervalle pour simuler le redémarrage de machines après une mise à jour logicielle. Un seul nœud est arrêté à chaque fois. Les partitions ne sont pas réaffectées pendant l’arrêt d’un nœud.

Ces tests ont été rédigés comme des scripts pour permettre une exécution automatique. Ce document explique comment répéter les tests dans votre propre environnement.

## Configuration requise

Les tests automatisés nécessitent les éléments suivants :

- un cluster Elasticsearch.

- un environnement JMeter configuré comme indiqué dans les [Performance testing guidance] (Guide pour les tests de performances).

- Les ajouts suivants ont été installés uniquement sur la machine virtuelle JMeter Master.

    - Java Runtime 7.

    - Nodejs 4.x.x ou version ultérieure.

    - Outils de ligne de commande Git.

## Fonctionnement des scripts

Les scripts de test sont conçus pour fonctionner sur la machine virtuelle JMeter Master. Lorsque vous sélectionnez un test à exécuter, les scripts exécutent la suite d’opérations suivante :

1.  Démarrez un plan de test JMeter en transférant les paramètres que vous avez spécifiés.

2.  Copiez un script qui effectue les opérations requises par le test sur une machine virtuelle spécifiée dans le cluster. Il peut s’agir d’une machine virtuelle dotée d’une adresse IP publique, ou d’une machine virtuelle *Jumpbox* si vous avez créé le cluster à l’aide du [modèle de démarrage rapide Azure Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch).

3.  Exécutez le script sur la machine virtuelle (ou Jumpbox).

L’image qui suit représente la structure de l’environnement de test et le cluster Elasticsearch. Remarquez que les scripts de test utilisent SSH pour se connecter à chacun des nœuds du cluster pour effectuer diverses opérations Elasticsearch, par exemple l’arrêt ou le redémarrage d’un nœud.

![](./media/guidance-elasticsearch/resilience-testing1.png)

## Tests de configuration de JMeter

Avant d’exécuter les tests de résilience, vous devez compiler et déployer les tests JUnit contenus dans le dossier *resiliency/jmeter/tests*. Ces tests sont référencés par le plan de test JMeter. Pour plus d’informations, consultez la procédure d’importation d’un projet de test JUnit existant dans Eclipse, dans [Deploying a JMeter JUnit sampler for testing Elasticsearch performance][] (Déploiement d’un échantillonneur JUnit JMeter pour le test de performances Elasticsearch).

Il existe deux versions de tests JUnit, figurant dans les dossiers suivants :

- **Elasticsearch17.** Le projet de ce dossier génère le fichier Elasticsearch17.jar. Utilisez ce fichier JAR pour tester Elasticsearch versions 1.7.x

- **Elasticsearch20**. Le projet présent dans ce dossier génère le fichier Elasticsearch20.jar. Utilisez ce fichier JAR pour tester Elasticsearch version 2.0.0 et ultérieures

Copiez le fichier JAR approprié avec le reste des dépendances à vos machines JMeter. Le processus est décrit dans la procédure Deploying a JUnit Test to JMeter (Déploiement d’un test JUnit sur JMeter) dans les [Considérations relatives à JMeter].

## Configuration de la sécurité de machine virtuelle pour chaque nœud

Les scripts de test nécessitent l’installation d’un certificat d’authentification sur chaque nœud du cluster Elasticsearch. Ainsi, les scripts sont exécutés automatiquement, sans demander de nom d’utilisateur ou de mot de passe lorsqu’ils se connectent aux différentes machines virtuelles.

Commencez par vous connecter à l’un des nœuds de cluster Elasticsearch (ou la machine virtuelle Jumpbox), puis exécutez la commande suivante pour générer une clé d’authentification :

```Shell
ssh-keygen -t rsa
```

Pendant que vous êtes connecté au nœud Elasticsearch (ou Jumpbox), exécutez les commandes suivantes pour chaque nœud dans le cluster Elasticsearch. Remplacez `<username>` par le nom d’un utilisateur valide sur chaque machine virtuelle, puis remplacez `<nodename>` par le nom DNS de l’adresse IP de la machine virtuelle qui héberge le nœud Elasticsearch. Remarque : vous serez invité à saisir le mot de passe de l’utilisateur au moment lors de l’exécution des commandes. Pour plus d’informations, consultez [Connexion SSH sans mot de passe](http://www.linuxproblem.org/art_9.html) :

```Shell
ssh <username>@<nodename> mkdir -p .ssh (
cat .ssh/id\_rsa.pub | ssh <username>*@<nodename> 'cat &gt;&gt; .ssh/authorized\_keys'
```

## Téléchargement et configuration des scripts de test

Les scripts de test sont fournis dans un référentiel Git. Utilisez la procédure suivante pour télécharger et configurer les scripts.

Sur l’ordinateur JMeter Master sur lequel vous allez exécuter les tests, ouvrez une fenêtre du bureau Git (Git BASH) et clonez le référentiel contenant les scripts, comme suit :

```Shell
git clone https://github.com/mspnp/azure-guidance.git
```

Accédez au dossier *resiliency-tests* et exécutez la commande suivante pour installer les dépendances requises pour exécuter les tests :

```Shell
npm install
```

Si JMeter Master s’exécute sous Windows, [téléchargez PLINK](http://the.earth.li/~sgtatham/putty/latest/x86/plink.exe) et copiez-le dans le dossier *resiliency-tests/lib*

Si JMeter Master s’exécute sous Linux, vous n’avez pas besoin de télécharger PLINK, mais vous devrez configurer SSH sans mot de passe entre JMeter Master et le nœud Elasticsearch ou Jumpbox que vous avez utilisé en exécutant les opérations décrites dans la procédure [Configure VM Security for Each Node](#configuring-vm-security-for-each-node) (Configurer la sécurité de machine virtuelle pour chaque nœud).

Modifiez le fichier `config.js` et éditez les paramètres de configuration qui suivent pour les adapter à votre environnement de test et un cluster Elasticsearch. Ces paramètres sont communs à tous les tests :

| Nom | Description | Valeur par défaut |
| ---- | ----------- | ------------- |
| `jmeterPath` | Chemin d’accès local dans lequel jmeter est situé | `C:/apache-jmeter-2.13` |
| `resultsPath` | Répertoire relatif dans lequel le script vide le résultat | `results` |
| `verbose` | Indique si le script génère une sortie en mode commenté ou non. | `true` |
| `remote` | Indique si les tests jmeter s’exécutent localement ou sur des serveurs distants | `true` |
| `cluster.clusterName` | Nom du cluster Elasticsearch. | `elasticsearch` |
| `cluster.jumpboxIp` | Adresse IP de l’ordinateur jumpbox. |-| | `cluster.username` | Utilisateur administrateur que vous avez créé lors du déploiement de cluster |-| | `cluster.password` | Mot de passe de l’utilisateur admin |-| | `cluster.loadBalancer.ip` | Adresse IP d’équilibreur de charge Elasticsearch |-| | `cluster.loadBalancer.url` | URL d’équilibreur de charge de base |-|

## Exécution des tests

Accédez au dossier *résilience-tests* et exécutez la commande suivante :

```Shell
node app.js
```

Le menu suivant doit apparaître :

![](./media/guidance-elasticsearch/resilience-testing2.png)

Saisissez le numéro du scénario que vous souhaitez exécuter : `11`, `12`, `13` ou `21`.

Une fois le scénario sélectionné, le test s’exécute automatiquement. Les résultats sont stockés sous la forme d’un ensemble de fichiers CSV dans un dossier créé sous le répertoire *résultats*. Chaque exécution a son propre dossier de résultats. Vous pouvez utiliser Excel pour analyser ces données et les présenter sous forme de graphique.

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Performance testing guidance]: guidance-elasticsearch-performance-testing-environment.md
[JMeter guidance]: guidance-elasticsearch-implementing-jmeter.md
[Considérations relatives à JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md
[Query aggregation and performance]: guidance-elasticsearch-query-aggregation-performance.md
[Resilience and Recovery]: guidance-elasticsearch-resilience-recovery.md
[Resilience and Recovery Testing]: guidance-elasticsearch-resilience-testing.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->