---
title: "Présentation du service d’exécution d’expérience d’Azure Machine Learning"
description: "Ce document donne une vue d’ensemble du service d’exécution d’expérience d’Azure Machine Learning"
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/17/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: bb1c7d318939c42edb9a51e28dec31593f2485f9
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="overview-of-azure-machine-learning-experiment-execution-service"></a>Présentation du service d’exécution d’expérience d’Azure Machine Learning
Le service d’exécution d’expérience d’Azure Machine Learning permet aux chercheurs de données de mener leurs expériences à l’aide des fonctionnalités de gestion et d’exécution d’Azure Machine Learning. Il fournit une infrastructure permettant une expérimentation agile avec des itérations rapides. Azure Machine Learning Workbench vous permet de commencer avec des exécutions locales sur votre ordinateur, et offre un chemin d’accès facile pour une montée en puissance et une montée en charge parallèle dans d’autres environnements tels que des machines virtuelles de science des données avec GPU ou des clusters HDInsight exécutant Spark.

Le service d’exécution d’expérience est conçu pour effectuer des exécutions isolées, reproductibles et cohérentes de vos expériences. Il vous aide à gérer vos cibles de calcul, environnements d’exécution et configurations de série de tests. Le fonctionnalités de gestion et d'exécution d’Azure Machine Learning Workbench vous permettent de vous déplacer facilement entre différents environnements. 

Vous pouvez exécuter un script Python ou PySpark dans un projet Azure Machine Learning Workbench localement ou à grande échelle dans le cloud. 

Les environnements dans lesquels vous pouvez exécuter vos scripts sont les suivants : 

* Python (3.5.2) sur votre ordinateur local installé par Azure Machine Learning Workbench ;
* Conda Python à l’intérieur d’un conteneur Docker sur un ordinateur local ;
* Conda Python à l’intérieur d’un conteneur Docker sur une machine Linux distante, par exemple, une [machine virtuelle de science des données basée sur la distribution Linux Ubuntu sur Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) ;
* [HDInsight pour Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) sur Azure.

>[!IMPORTANT]
>Actuellement, le service d’exécution Azure Machine Learning prend en charge les versions de runtime Python 3.5.2 et Spark 2.1.11. 


## <a name="key-concepts-in-azure-ml-experiment-execution"></a>Concepts clés de l’exécution d’expérience Azure Machine Learning
Il est important de comprendre que les concepts suivants concernant l’exécution d’expérience Azure Machine Learning. Les sections suivantes expliquent en détail comment appliquer ces concepts. 
### <a name="compute-target"></a>Cible de calcul
Une cible de calcul spécifie l’emplacement où exécuter le programme d’un utilisateur, par exemple, le bureau de l’utilisateur, un Docker distant sur une machine virtuelle, ou un cluster. La cible de calcul doit être adressable et accessible à l’utilisateur. Azure Machine Learning Workbench vous permet de créer des cibles de calcul et de gérer celles-ci à l’aide de l’application Workbench et de l’interface de ligne de commande. 

La commande _az ml computetarget attach_ dans l’interface de ligne de commande permet de créer une cible de calcul utilisable dans les exécutions.

### <a name="supported-compute-targets-are"></a>Les cibles de calcul prises en charge sont les suivantes :
* environnement Python (3.5.2) local sur votre ordinateur installé par Azure Machine Learning Workbench ;
* Docker local sur votre ordinateur ;
* Docker distant sur machines virtuelles exécutant la distribution Linux Ubuntu, par exemple, une [machine virtuelle de science des données basée sur la distribution Linux Ubuntu sur Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) ;
* [cluster HDInsight pour Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) sur Azure.

Actuellement, le service d’exécution Azure Machine Learning prend en charge les versions de runtime Python 3.5.2 et Spark 2.1.11. 

>[!IMPORTANT]
> Les machines virtuelles Windows exécutant Docker ne sont **pas** prises en charge en tant que cibles de calcul à distance.

### <a name="execution-environment"></a>Environnement d’exécution
L’environnement d’exécution définit la configuration de runtime et les dépendances nécessaires pour exécuter le programme dans Azure Machine Learning Workbench.

L’utilisateur gère l’environnement d’exécution local à l’aide de ses outils et gestionnaires de packages favoris en cas d’exécution sur le runtime par défaut d’Azure Machine Learning Workbench. 

Conda est utilisé pour gérer les exécutions sur Docker local et Docker distant, ainsi que les exécutions basées sur HDInsigh. Pour ces cibles de calcul, la configuration de l’environnement d’exécution est gérée via les fichiers **Conda_dependencies.yml** et **Spark_dependencies.yml**. Ces fichiers se trouvent dans le dossier **aml_config** à l’intérieur de votre projet.

**Les runtimes pris en charge pour les environnements d’exécution sont les suivants :**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Configuration de série de tests
En plus de la cible de calcul et de l’environnement d’exécution, Azure Machine Learning offre une infrastructure permettant de définir et modifier les configurations de série de tests. Dans le cadre d’une expérimentation itérative, les différentes exécutions de votre expérience peuvent nécessiter une configuration distincte. Vous balayez peut-être diverses plages de paramètres en utilisant des sources de données et paramètres de réglage Spark différents. Le service d’exécution Azure Machine Learning fournit une infrastructure pour la gestion de la configuration de série de tests.

L’exécution de la commande _az ml computetarget attach_ a pour effet de générer deux fichiers dans le dossier **aml_config** de votre projet : un dossier .compute et un dossier .runconfig suivant la convention de dénomination _<nom_de_votre_cible_de_calcul>.compute_ et _<nom_de_votre_cible_de_calcul>.runconfig_. Par souci de facilité, le fichier .runconfig est créé automatiquement lorsque vous créez une cible de calcul. Vous pouvez créer et gérer d’autres configurations de série de tests à l’aide de la commande _az ml runconfigurations_ dans l’interface de ligne de commande. Vous pouvez également les créer et modifier sur votre système de fichiers.

Une configuration de série de tests dans Azure Machine Learning Workbench vous permet également de spécifier des variables d’environnement. Vous pouvez spécifier des variables d’environnement et les utiliser dans votre code en ajoutant la section suivante à votre fichier .runconfig. 

```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

Ces variables d’environnement sont accessibles dans votre code. Par exemple, cet extrait de code Python imprime la variable d’environnement nommée « EXAMPLE_ENV_VAR1 ».
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**La figure suivante illustre le flux de haut niveau pour la série de tests d’expérimentation initiale.**_
![](media/experiment-execution-configuration/experiment-execution-flow.png)

## <a name="azure-ml-experiment-execution-scenarios"></a>Scénarios d’exécution d’expérience Azure Machine Learning
Cette section explore des scénarios d’exécution et montre comment Azure Machine Learning exécute les expériences, en particulier localement, sur une machine virtuelle distante et sur un cluster HDInsight. Cette section est une présentation pas à pas de la procédure menant de la création d’une cible de calcul à l’exécution de vos expériences.

>[!NOTE]
>Dans la suite de cet article, nous utilisons les commandes de l’interface de ligne de commande pour présenter les concepts et fonctionnalités. Les fonctionnalités décrites ici sont également utilisables à partir de l’application Workbench de bureau.

## <a name="launching-the-cli"></a>Lancement de l’interface de ligne de commande
Une manière simple de lancer l’interface de ligne de commande consiste à ouvrir un projet dans l’application de bureau Azure Machine Learning Workbench, puis à accéder à **Fichier --> Ouvrir l’invite de commandes**.

![](media/experiment-execution-configuration/opening-cli.png)

Cette commande a pour effet d’ouvrir une fenêtre de terminal dans laquelle vous pouvez entrer des commandes pour exécuter des scripts dans le dossier du projet actuel. Cette fenêtre de terminal est configurée avec l’environnement Python 3.5.2 installé par Workbench.

>[!NOTE]
> Lorsque vous exécutez une commande _az ml_ à partir de la fenêtre Commande, il se peut que vous deviez vous authentifier auprès d’Azure. L’interface de ligne de commande utilisant un cache d’authentification indépendant, la connexion à l’application de bureau Workbench ne signifie pas que vous êtes authentifié dans l’environnement de votre interface de ligne de commande. Pour vous authentifier, procédez comme suit. Le jeton d’authentification est mis en cache localement pendant un certain temps. Il suffit de répéter ces étapes lorsqu’il expire. Lorsque le jeton expire ou si vous rencontrez des erreurs d’authentification, exécutez les commandes suivantes :

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>Lorsque vous exécutez la commande _az ml_ à l’intérieur d’un dossier de projet, assurez-vous que le projet appartient à un compte d’expérimentation Azure Machine Learning de l’abonnement Azure _actuel_. Autrement, vous pourriez rencontrer des erreurs d’exécution.


## <a name="running-scripts-and-experiments"></a>Exécution de scripts et d’expériences
Azure Machine Learning Workbench vous permet d’exécuter vos scripts Python et PySpark sur différentes cibles de calcul à l’aide de la commande _az ml experiment submit_. Cette commande requiert la définition d’une configuration de série de tests. 

Azure Machine Learning Workbench crée un fichier .runconfig correspondant lorsque vous créez une cible de calcul, mais vous pouvez créer des configurations de série de tests supplémentaires à l’aide de la commande _az ml runconfiguration  create_. Vous pouvez aussi modifier manuellement les fichiers de configuration de série de tests.

Les configurations de série de tests apparaissent en tant que partie du processus d’exécution d’expérience dans l’application Workbench. 

>[!NOTE]
>Plus en savoir plus sur le fichier de configuration de série de tests, voir la section [Référence de configuration d’exécution d’expérience](experiment-execution-configuration-reference.md).

## <a name="running-a-script-locally-on-azure-ml-workbench-installed-runtime"></a>Exécution d’un script localement sur un runtime installé par Azure Machine Learning Workbench
Azure Machine Learning Workbench permet d’exécuter des scripts directement sur le runtime Python 3.5.2 installé par Azure Machine Learning Workbench. Ce runtime par défaut est installé au moment de la configuration d’Azure Machine Learning Workbench. Il inclut des bibliothèques et dépendances Azure Machine Learning. Les résultats et artefacts de la série de tests pour les exécutions locales sont toujours enregistrés dans le service d’historique des exécutions dans le cloud.

Contrairement aux exécutions sur Docker, cette configuration n’est _pas_ gérée par Conda. Vous devez configurer manuellement les dépendances de package pour l’environnement Python de votre Azure Machine Learning Workbench local.

Vous pouvez exécuter la commande suivante pour appliquer votre script localement à l’environnement Python installé par Azure Machine Learning Workbench. 

```
$az ml experiment submit -c local myscript.py
```

Vous pouvez trouver le chemin d’accès à l’environnement Python par défaut en tapant la commande suivante dans la fenêtre de l’interface de ligne de commande d’Azure Machine Learning Workbench.
```
$ conda env list
```

>[!NOTE]
>L’exécution locale de PySpark directement sur des environnements Spark locaux n’est **pas** prise en charge actuellement. Azure Machine Learning Workbench ne prend pas en charge les scripts PySpark exécutés sur un Docker local. Une image Docker de base d’Azure Machine Learning est intégrée dans la version préinstallée de Spark 2.1.11. 

_**Présentation de l’exécution locale d’un script Python :**_
![](media/experiment-execution-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Exécution d’un script sur un Docker local
Vous pouvez également exécuter vos projets sur un conteneur Docker sur votre ordinateur local via le service d’exécution Azure Machine Learning. Azure Machine Learning Workbench fournit une image Docker de base qui intègre des bibliothèques Azure Machine Learning, ainsi qu’un runtime Spark 2.1.11 pour faciliter les exécutions locales de Spark. Docker doit déjà être en cours d’exécution sur l’ordinateur local.

Pour exécuter votre script Python ou PySpark sur un Docker local, vous pouvez exécuter les commandes suivantes dans l’interface de ligne de commande.

```
$az ml experiment submit -c docker myscript.py
```
ou
```
az ml experiment submit --run-configuration docker myscript.py
```

L’environnement d’exécution sur un Docker local est préparé à l’aide de l’image Docker de base d’Azure Machine Learning. Azure Machine Learning Workbench télécharge cette image lors de la première exécution, et la superpose aux packages spécifiés dans le fichier conda_dependencies.yml de l’utilisateur. Cette opération ralentit la série de tests initiale, mais accélère sensiblement les séries suivantes du fait qu’Azure Machine Learning Workbench réutilise les couches mises en cache. 

>[!IMPORTANT]
>Vous devez commencer par exécuter la commande _az ml experiment prepare -c docker_ afin de préparer l’image Docker pour votre première série de tests. Vous pouvez également définir le paramètre **PrepareEnvironment** sur true dans votre fichier docker.runconfig. Cette action a pour effet de préparer automatiquement votre environnement à l’exécution de votre série de tests.  

>[!NOTE]
>Si vous exécutez un script PySpark sur Spark, le fichier spark_dependencies.yml est utilisé en plus du fichier conda_dependencies.yml.

L’exécution de vos scripts sur une image Docker offre les avantages suivants :

1. elle garantit que votre script peut être exécuté de manière fiable dans d’autres environnements d’exécution. L’exécution sur un conteneur Docker vous aide à détecter et à éviter toute référence locale susceptible d’avoir un impact sur la portabilité. 

2. Elle vous permet de tester rapidement le code sur des runtimes et infrastructures dont l’installation et la configuration sont complexes, comme Apache Spark, sans avoir à les installer vous-même.


_**Présentation de l’exécution d’un Docker local pour un script Python :**_
![](media/experiment-execution-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Exécution d’un script sur un Docker distant
Parfois, les ressources disponibles sur l’ordinateur local de l’utilisateur sont insuffisantes pour former le modèle souhaité. Dans ce cas, le service d’exécution Azure Machine Learning offre un moyen simple d’exécuter vos scripts Python ou PySpark sur des machines virtuelles plus puissantes en utilisant un Docker distant. 

Une machine virtuelle distante doit remplir les conditions suivantes :
* Elle doit exécuter la distribution Linux Ubuntu et être accessible via SSH. 
* Elle doit exécuter Docker.

>[!IMPORTANT]
> Les machines virtuelles Windows exécutant Docker ne sont **pas** prises en charge en tant que cibles de calcul distantes.


Vous pouvez utiliser la commande suivante afin de créer la définition de cible de calcul et la configuration de série de tests pour des exécutions basées sur un Docker distant.

```
az ml computetarget attach --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" --type remotedocker
```

Une fois la cible de calcul configurée, vous pouvez utiliser la commande suivante pour exécuter votre script.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>N’oubliez pas que l’environnement d’exécution est configuré à l’aide des spécifications du fichier conda_dependencies.yml. Le fichier spark_dependencies.yml est également utilisé si l’infrastructure PySpark est spécifiée dans le fichier .runconfig. 

Le processus de construction du Docker pour les machines virtuelles distantes étant exactement le même que pour les séries de tests du Docker local, vous devez vous attendre à une expérience d’exécution similaire.

>[!TIP]
>Si vous préférez éviter la latence résultant de la création de l’image Docker pour votre première série de tests, vous pouvez utiliser la commande suivante pour préparer la cible de calcul avant l’exécution de votre script. az ml experiment prepare -c <remotedocker>


_**Présentation de l’exécution de machine virtuelle distante pour un script Python :**_
![](media/experiment-execution-configuration/remote-vm-run.png)


## <a name="running-a-script-on-hdinsight-cluster"></a>Exécution d’un script sur un cluster HDInsight
HDInsight est une plateforme populaire pour l’analytique du Big Data qui prend en charge Apache Spark. Azure Machine Learning Workbench permet d’effectuer une expérimentation sur le Big Data à l’aide de clusters HDInsight Spark. 

Pour créer une cible de calcul et une configuration de série de tests pour un cluster Azure HDInsight Spark, vous pouvez utiliser la commande suivante :

```
$ az ml computetarget attach --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword" --type cluster 
```

>[!NOTE]
>Si vous utilisez un nom de domaine complet (FQDN) au lieu d’une adresse IP, et que votre cluster Azure HDInsight Spark est nommé _foo_, le point de terminaison SSH se trouve sur le nœud de pilote nommé _foo-ssh.azurehdinsight.net_. N’oubliez pas le suffixe **-ssh** dans le nom du serveur lors de l’utilisation du nom de domaine complet (FQDN) pour le paramètre _--address_.


Une fois que vous avez le contexte de calcul, la commande suivante vous permet d’exécuter votre script PySpark.

```
$ az ml experiment submit -c myhdi myscript.py
```

Azure Machine Learning Workbench prépare et gère l’environnement d’exécution sur le cluster HDInsight à l’aide de Conda. La configuration est gérée par les fichiers _conda_dependencies.yml_ et _spark_dependencies.yml_. 

Pour pouvoir exécuter des expériences dans ce mode, l’utilisateur a besoin d’un accès SSH au cluster HDInsight. 

>[!NOTE]
>Une configuration prise en charge est celle de clusters Azure HDInsight Spark exécutant Linux (distribution Linux Ubuntu avec Python/PySpark 3.5.2 et Spark 2.1.11).

_**Présentation de l’exécution d’un script PySpark basée sur HDInsight**_
![](media/experiment-execution-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Exécution d’un script sur GPU
Pour exécuter vos scripts sur des GPU, vous pouvez suivre les instructions du [Guide pratique pour utiliser des GPU dans Azure Machine Learning](how-to-use-gpu.md).


## <a name="next-steps"></a>Étapes suivantes
* [Créer et installer Azure Machine Learning](quickstart-installation.md)
* [Gestion des modèles](model-management-overview.md)

