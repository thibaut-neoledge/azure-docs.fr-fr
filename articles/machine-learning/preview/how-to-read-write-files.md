---
title: "Guide pratique pour lire et écrire des fichiers de données volumineux | Microsoft Docs"
description: "Comment lire et écrire des fichiers volumineux dans des expériences Azure Machine Learning."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fb3158ef786ad73440a59c07b38476a98ced0768
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="persisting-changes-and-dealing-with-large-files"></a>Persistance des modifications et traitement de fichiers volumineux

## <a name="execution-isolation-portability-and-reproducibility"></a>Isolation de l’exécution, portabilité et reproductibilité
Le service Azure Machine Learning - Expérimentation vous permet de configurer différentes cibles d’exécution, dont certaines locales, comme un ordinateur local ou un conteneur Docker sur un ordinateur local, et d’autres distantes, comme un conteneur Docker dans une machine distante ou un cluster HDInsight. Pour plus d’informations, reportez-vous à l’article [Configuration de l’exécution d’une expérimentation](experiment-execution-configuration.md). Avant toute exécution, le dossier de projet est copié dans la cible de calcul concernée. Il en est ainsi même dans le cas d’une exécution locale où un dossier temporaire local est utilisé à cet effet. 

L’objectif de cette conception est de garantir l’isolation d’exécution, la reproductibilité et la portabilité. Si vous exécutez le même script deux fois, dans la même cible de calcul ou dans une cible de calcul différente, vous êtes assuré d’obtenir les mêmes résultats. Les modifications apportées par la première exécution ne doivent pas affecter la deuxième exécution. Grâce à cette conception, nous pouvons traiter les cibles de calcul en tant que ressources de calcul sans état sans affinité avec les travaux exécutés une fois terminés.

## <a name="challenges"></a>Défis
Parallèlement aux avantages de la portabilité et de la répétabilité, cette conception soulève des défis uniques :
### <a name="persisting-state-changes"></a>Persistance des changements d’état
Si votre script modifie l’état dans le contexte de calcul, les changements ne sont pas conservés pour votre prochaine exécution, ni propagés vers la machine cliente automatiquement. 

Plus concrètement, si votre script crée un sous-dossier ou écrit un fichier, ce fichier ou dossier ne se trouve pas dans le répertoire de votre projet après l’exécution. Ils restent dans un dossier temporaire de l’environnement de la cible de calcul où qu’il soit. Ils peuvent être utilisés à des fins de débogage, mais vous ne pouvez jamais prendre de dépendances sur leur existence.

### <a name="dealing-with-large-files-in-project-folder"></a>Traitement des fichiers volumineux dans le dossier de projet

Si votre dossier de projet contient des fichiers volumineux, une latence peut apparaître quand le dossier est copié dans l’environnement de calcul cible au début de chaque exécution. Même si l’exécution se produit localement, cette latence entraîne toujours un trafic disque inutile qui doit être évité. C’est pourquoi nous limitons la taille d’un projet à 25 Mo.

## <a name="option-1-use-the-outputs-folder"></a>Option 1 : Utiliser le dossier outputs
Il s’agit de l’option recommandée si votre script génère des fichiers, que vous vous attendez à ce que ces fichiers changent à chaque exécution d’expérience et que vous souhaitez conserver un historique de ces fichiers. Parmi les cas d’usage courants, citons la formation d’un modèle, la création d’un jeu de données ou le traçage d’un graphe en tant que fichier image dans le cadre de l’exécution de la formation d’un modèle. Vous pouvez alors comparer ces sorties d’une exécution à l’autre, sélectionner un fichier de sortie (par exemple, un modèle) produit par une exécution précédente et l’utiliser pour une autre tâche (par exemple, pour une notation).

Fondamentalement, vous pouvez écrire des fichiers dans un dossier nommé `outputs` relatif au répertoire racine. Il s’agit d’un dossier spécial qui bénéficie d’un traitement spécial de la part du service Expérimentation. Tout ce que votre script y crée pendant l’exécution, tel qu’un fichier de modèle, un fichier de données ou un fichier image tracé (collectivement appelés _artefacts_), est copié dans le compte Stockage Blob Azure associé à votre compte d’expérimentation une fois l’exécution terminée. Ils font dès lors partie de l’enregistrement de l’historique des exécutions.

Voici un exemple rapide de stockage d’un modèle dans le dossier `outputs` :
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Vous pouvez télécharger un _artefact_ en accédant à la section **Fichiers de sortie** de la page de détails d’une exécution particulière dans Azure Machine Learning Workbench, le sélectionner et cliquer sur le bouton **Télécharger**. Vous pouvez également utiliser la commande `az ml asset download` à partir de la fenêtre de l’interface CLI.

Pour voir un exemple plus complet, consultez la script Python `iris_sklearn.py` dans l’exemple de projet _Classifying Iris_.

## <a name="option-2-use-the-shared-folder"></a>Option 2 : Utiliser le dossier partagé
Utiliser un dossier partagé accessible entre les exécutions indépendantes peut être une option intéressante pour les scénarios suivants, tant que vous n’avez pas besoin de conserver un historique de ces fichiers pour chaque exécution : 
- Votre script doit charger des données à partir de fichiers locaux, tels que des fichiers csv ou un répertoire de fichiers texte ou image, en tant que données de formation ou de test. 
- Votre script traite des données brutes et écrit des résultats intermédiaires, comme des données de formation avec des caractéristiques à partir de fichiers texte/image, qui sont ensuite utilisés dans une exécution d’apprentissage. 
- Votre script génère un modèle, que votre script de notation suivant doit choisir et utiliser à des fins d’évaluation. 

Un inconvénient important est que le dossier partagé réside localement sur la cible de calcul que vous choisissez. Ainsi, cette option ne fonctionne que si toutes vos exécutions de script référençant ce dossier partagé sont exécutées sur la même cible de calcul et que celle-ci n’est pas recyclée entre les exécutions.

La fonctionnalité de dossier partagé vous permet de lire ou d’écrire dans un dossier spécial identifié par une variable d’environnement, `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Exemple
Voici un exemple de code Python montrant comment utiliser ce dossier de partage pour lire et écrire un fichier texte :
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Pour voir un exemple plus complet, consultez le fichier `iris_sklearn_shared_folder.py` dans l’exemple de projet _Classifying Iris_.

Avant d’utiliser cette fonctionnalité, vous devez définir certaines configurations simples dans le fichier `.compute` représentant le contexte d’exécution ciblé dans le dossier `aml_config`. Le chemin réel de ce dossier peut varier en fonction de la cible de calcul que vous choisissez et de la valeur que vous configurez.

### <a name="configure-local-compute-context"></a>Configurer le contexte de calcul local

Pour activer cette fonctionnalité sur un contexte de calcul local, ajoutez simplement la ligne suivante à votre fichier `.compute` représentant l’environnement _local_ (généralement appelé `local.compute`).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

`~/.azureml/share` est le chemin du dossier de base par défaut. Vous pouvez le remplacer par n’importe quel chemin absolu local accessible à l’exécution du script. Les nom de compte d’expérimentation, nom d’espace de travail et nom de projet sont automatiquement ajoutés au répertoire de base, composant ainsi le chemin complet du répertoire partagé. Par exemple, les fichiers peuvent être écrits dans le chemin suivant (et récupérés de celui-ci) si vous utilisez la valeur par défaut précédente :

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-docker-compute-context-local-or-remote"></a>Configurer le contexte de calcul Docker (local ou distant)
Pour activer cette fonctionnalité sur un contexte de calcul Docker, vous devez ajouter les deux lignes suivantes à votre fichier _.compute_ Docker local ou distant.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>`sharedVolume` doit être défini sur `true` quand vous utilisez la variable d’environnement `AZUREML_NATIVE_SHARE_DIRECTORY` pour accéder au dossier partagé, sinon l’exécution échoue.

Le code s’exécutant dans le conteneur Docker voit toujours ce dossier partagé comme étant `/azureml-share/`. Ce chemin de dossier tel que vu par le conteneur Docker n’est pas configurable. En outre, vous ne devez pas prendre de dépendance sur ce nom de dossier dans votre code. Au lieu de cela, utilisez toujours le nom de variable d’environnement `AZUREML_NATIVE_SHARE_DIRECTORY` pour adresser ce dossier. Il est mappé à un dossier local sur le contexte de calcul/la machine hôte Docker. Le répertoire de base de ce dossier local est la valeur configurable du paramètre `nativeSharedDirectory` dans le fichier `.compute`. Le chemin local du dossier partagé sur la machine hôte, si vous utilisez la valeur par défaut ci-dessus, est le suivant :
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!TIP]
>Notez que le chemin du dossier partagé sur le disque local est le même entre un contexte de calcul local et un contexte de calcul Docker local. Cela signifie que vous pouvez même partager des fichiers entre une exécution locale et une exécution Docker locale.

Vous pouvez placer les données d’entrée directement dans ces dossiers en vue de leur récupération éventuelle par vos exécutions locales ou Docker sur cette machine. Vous pouvez également, à partir de vos exécutions locales ou Docker, écrire des fichiers dans ce dossier afin qu’ils y soient rendus persistants, survivant ainsi au cycle de vie de l’exécution.

Pour plus d’informations sur les fichiers de configuration dans le service d’exécution Azure Machine Learning, reportez-vous à l’article [Fichiers de configuration de l’exécution](experiment-execution-configuration-reference.md).

>[!NOTE]
>La variable d’environnement `AZUREML_NATIVE_SHARE_DIRECTORY` n’est pas prise en charge dans le contexte de calcul HDInsight. Toutefois, vous pouvez facilement obtenir le même résultat en utilisant explicitement un chemin absolu WASB pour lire/écrire dans le stockage Blob lié.

## <a name="option-3-use-external-durable-storage"></a>Option 3 : Utiliser un stockage durable externe

Vous êtes bien sûr libre d’utiliser un stockage durable externe pour conserver l’état pendant l’exécution. Cette approche est utile dans les scénarios suivants :
- Vos données d’entrée sont déjà stockées dans un stockage durable accessible à partir de l’environnement de calcul cible.
- Ces fichiers n’ont pas besoin de faire partie des enregistrements de l’historique des exécutions.
- Ces fichiers doivent être partagés par des exécutions dans différents environnements de calcul.
- Ces fichiers doivent survivre au contexte de calcul lui-même.

À titre d’exemple, citons l’utilisation du stockage blob Azure à partir de votre code Python/PySpark.

Voici un exemple rapide d’utilisation du stockage blob Azure à partir d’un code Python :
```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Voici également un exemple rapide d’attachement d’un Stockage Blob Azure arbitraire à un runtime Spark HDI :
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Conclusion
Étant donné qu’Azure Machine Learning exécute des scripts en copiant le dossier de projet entier dans le contexte de calcul cible, vous devez accorder une attention particulière aux fichiers d’entrée, de sortie et intermédiaires volumineux. Vous pouvez utiliser le dossier `outputs` spécial, le dossier partagé accessible via la variable d’environnement `AZUREML_NATIVE_SHARE_DIRECTORY` ou un stockage durable externe pour les transactions impliquant des fichiers volumineux. 

## <a name="next-steps"></a>Étapes suivantes
- Consultez [Configuration de l’exécution d’une expérimentation](experiment-execution-configuration-reference.md).
- Découvrez comment le projet de didacticiel [Classifying Iris](tutorial-classifying-iris-part-1.md) utilise le dossier `outputs` pour rendre persistant un modèle formé.
