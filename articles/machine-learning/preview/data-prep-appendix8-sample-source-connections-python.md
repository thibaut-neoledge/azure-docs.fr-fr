---
title: "Exemples de connexions de données sources supplémentaires avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit un ensemble d’exemples de connexions de données sources possibles avec la préparation des données Azure ML."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 550cca100314009f63eec2136e8c65426d8bf07f
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="sample-of-custom-source-connections-python"></a>Exemple de connexions sources personnalisées (Python) 
Avant de lire cette annexe, consultez [Vue d’ensemble de l’extensibilité Python](data-prep-python-extensibility-overview.md).

## <a name="loading-data-from-dataworld"></a>Chargement de données à partir de data.world

### <a name="prerequisites"></a>Prérequis

#### <a name="register-yourself-at-dataworld"></a>Vous inscrire auprès de data.world
Un jeton d’API, que vous obtenez à partir du site web de data.world, est nécessaire.

#### <a name="install-dataworld-library"></a>Installer la bibliothèque data.world

Ouvrez l’interface de ligne de commande d’Azure Machine Learning Workbench à partir de _Fichier -> Ouvrir une interface de ligne de commande_.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Ensuite, exécutez `dw configure` sur la ligne de commande et spécifiez votre jeton. Quand vous entrez votre jeton, un répertoire .dw/ est créé dans votre répertoire de base et votre jeton y est stocké.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Vous devriez maintenant pouvoir importer des bibliothèques data.world.

#### <a name="load-data-into-data-preparation"></a>Charger des données dans la préparation des données

Créez un flux de données basé sur un script et utilisez le script suivant pour charger les données à partir de data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#load the dataset
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-cosmosdb-data-into-data-preparation"></a>Charger des données CosmosDB dans la préparation des données

Créez un flux de données basé sur un script et utilisez le script suivant pour charger les données à partir de CosmosDB (vous devrez tout d’abord installer les bibliothèques ; consultez le lien vers le document de référence indiqué plus haut).

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

