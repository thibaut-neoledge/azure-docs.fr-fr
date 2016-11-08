---
title: Procédure pas à pas Azure Event Hubs Archive | Microsoft Docs
description: Exemple qui utilise le Kit de développement logiciel (SDK) Azure Python pour illustrer l’utilisation de la fonctionnalité Event Hubs Archive.
services: event-hubs
documentationcenter: ''
author: djrosanova
manager: timlt
editor: ''

ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: darosa;sethm

---
# Procédure pas à pas Event Hubs Archive : Python
Event Hubs Archive est une nouvelle fonctionnalité d’Event Hubs, qui vous permet de fournir automatiquement les données de flux de votre Event Hub à un compte de stockage blob Azure de votre choix. Cela facilite le traitement par lots des données de flux en temps réel. Cet article décrit comment utiliser Event Hubs Archive avec Python. Pour plus d’informations sur Event Hubs Archive, consultez [l’article sur la vue d’ensemble](event-hubs-archive-overview.md).

Cet exemple utilise le Kit de développement logiciel (SDK) Azure Python pour illustrer l’utilisation de la fonctionnalité Archive. Le sender.py envoie la télémétrie de l’environnement simulé à Event Hubs au format JSON. Event Hub est configuré pour utiliser la fonctionnalité Archive afin d’écrire ces données dans le stockage blob en lots. Le archivereader.py lit ensuite ces objets blob et crée un fichier append par appareil et écrit les données dans des fichiers .csv.

Les opérations que nous allons effectuer

1. Créer un compte de stockage d’objets blob Azure et un conteneur d’objets blob dans celui-ci à l’aide du portail Azure
2. Créer un espace de noms Event Hub à l’aide du portail Azure
3. Créer un Event Hub avec la fonctionnalité Archive activée à l’aide du portail Azure
4. Envoyer des données au Event Hub avec un script Python
5. Lire les fichiers de l’archive et les traiter avec un autre script Python

Composants requis

1. Python 2.7x
2. Un abonnement Azure

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Création d'un compte Azure Storage
1. Connectez-vous au [portail Azure][portail Azure].
2. Dans le panneau de navigation gauche du portail, cliquez sur Nouveau, puis sur Données + stockage et sur Compte de stockage.
3. Renseignez les champs dans le panneau de compte de stockage, puis cliquez sur **Créer**.
   
   ![][1]
4. Après l’affichage du message**Déploiements réussis**, cliquez sur le nouveau compte de stockage et dans le panneau **Bases** panneau, cliquez sur **Objets blob**. Lorsque le panneau **Service Blob** s’ouvre, cliquez sur **+ Conteneur** en haut. Nommez **l’archive** de conteneur, puis fermez le panneau **Service Blob**.
5. Cliquez sur **Clés d’accès** dans le panneau de gauche et copiez le nom du compte de stockage et la valeur de **key1**. Enregistrez ces valeurs dans le Bloc-notes ou un autre emplacement temporaire.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## Créer un script Python pour envoyer des événements à votre Event Hub
1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code][Visual Studio Code].
2. Créez un script appelé **sender.py**. Ce script envoie 200 événements à votre Event Hub. Ce sont de simples lectures environnementales envoyées au format JSON.
3. Collez le code suivant dans sender.py :
   
   ```
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send\_event('myhub', s)
       print y
   ```
4. Mettez à jour le code précédent pour utiliser votre nom d’espace de noms et les valeurs de clé que vous avez obtenues lors de la création de l’espace de noms Event Hubs.

## Créer un script Python pour lire vos fichiers d’archive
1. Remplissez les champs du panneau et cliquez sur **Créer**.
2. Créez un script appelé **archivereader.py**. Ce script lit les fichiers d’archive et crée un fichier par appareil pour écrire les données uniquement pour cet appareil.
3. Collez le code suivant dans archivereader.py :
   
   ```
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
       reader = DataFileReader(open(filename, 'rb'), DatumReader())
       dict = {}
       for reading in reader:
           parsed\_json = json.loads(reading["Body"])
           if not 'id' in parsed\_json:
               return
           if not dict.has\_key(parsed\_json['id']):
           list = []
           dict[parsed\_json['id']] = list
       else:
           list = dict[parsed\_json['id']]
           list.append(parsed\_json)
       reader.close()
       for device in dict.keys():
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')
   
   def startProcessing(accountName, key, container):
       print 'Processor started using path: ' + os.getcwd()
       block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
       generator = block\_blob\_service.list\_blobs(container)
       for blob in generator:
           if blob.properties.content\_length != 0:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = string.replace(blob.name, '/', '\_')
               block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block\_blob\_service.delete\_blob(container, blob.name)
   startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
   ```
4. Veillez à coller les valeurs appropriées pour votre nom de compte de stockage et la clé dans l’appel à `startProcessing`.

## Exécuter les scripts
1. Ouvrez une invite de commandes comprenant Python dans son chemin d’accès, puis exécutez ces commandes pour installer les packages de configuration requise Python :
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Si vous disposez d’une version antérieure de azure-storage ou azure, vous devrez peut-être utiliser l’option **--upgrade**.
   
   Vous devrez peut-être également exécuter les éléments suivants (cela n’est pas nécessaire sur la plupart des systèmes) :
   
   ```
   pip install cryptography
   ```
2. Remplacez votre répertoire par celui dans lequel vous avez enregistré sender.py et archivereader.py, puis exécutez cette commande :
   
   ```
   start python sender.py
   ```
   
   Cela démarre un nouveau processus Python pour exécuter l’expéditeur.
3. Attendez quelques minutes pour que l’archive s’exécute. Puis tapez la commande suivante dans la fenêtre de commande d’origine :
   
    ```
    python archivereader.py
    ```

Ce processeur d’archive utilise le répertoire local pour télécharger tous les objets blob à partir du compte de stockage/conteneur. Il traite tous ceux qui ne sont pas vides et écrit les résultats sous la forme de fichiers .csv dans le répertoire local.

## Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Overview of Event Hubs Archive][Overview of Event Hubs Archive] \(Vue d’ensemble d’Event Hubs Archive)
* Un [exemple d'application complet qui utilise des hubs d’événements][exemple d'application complet qui utilise des hubs d’événements].
* L’exemple de [montée en puissance du traitement des événements avec Event Hubs][montée en puissance du traitement des événements avec Event Hubs].
* [Vue d’ensemble des concentrateurs d’événements][Vue d’ensemble des concentrateurs d’événements]

[portail Azure]: https://portal.azure.com/
[Overview of Event Hubs Archive]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[Visual Studio Code]: https://code.visualstudio.com/
[Vue d’ensemble des concentrateurs d’événements]: event-hubs-overview.md
[exemple d'application complet qui utilise des hubs d’événements]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[montée en puissance du traitement des événements avec Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

<!---HONumber=AcomDC_0914_2016-->