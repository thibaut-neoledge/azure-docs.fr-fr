---
title: "Le Kit de développement logiciel (SDK) d’appareil Azure IoT pour C | Microsoft Docs"
description: "Prenez en main Azure IoT device SDK pour C et apprenez à créer des applications d’appareil qui communiquent avec un IoT Hub."
services: iot-hub
documentationcenter: 
author: olivierbloch
manager: timlt
editor: 
ms.assetid: e448b061-6bdd-470a-a527-15ec03cca7b9
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2016
ms.author: obloch
translationtype: Human Translation
ms.sourcegitcommit: ef066a50b71389cb1cdd3bb0f8d342a34a4cc722
ms.openlocfilehash: 0033f1cc036e1eef63f0728be8d5d73c8dbbccf6


---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT device SDK pour C
Le **Kit de développement logiciel (SDK) d’appareil Azure IoT** (Azure IoT device SDK) est un ensemble de bibliothèques conçu pour simplifier le processus d’envoi d’événements et de réception de messages à partir du service **Azure IoT Hub**. Il existe différentes variantes de ce kit de développement logiciel, chacune concernant une plateforme spécifique, mais cet article met l'accent sur le **kit de développement logiciel Azure IoT device SDK pour C**.

Le kit de développement logiciel d’appareil Azure IoT pour C est rédigé en code ANSI C (C99) afin d’optimiser sa portabilité. Il est ainsi adapté pour fonctionner sur plusieurs plateformes et appareils, en particulier quand la réduction de l’encombrement du disque et de l’empreinte mémoire est une priorité.  

Le Kit de développement logiciel (SDK) a été testé sur un large éventail de plateformes (consultez le [Catalogue d’appareils certifiés Azure pour l’IoT](https://catalog.azureiotsuite.com/) pour plus d’informations). Bien que cet article contienne des procédures pas à pas d’exemple de code s’exécutant sur la plateforme Windows, n’oubliez pas que le code décrit dans cet article est exactement le même dans l’ensemble des plateformes prises en charge.

Dans cet article, vous allez découvrir l’architecture du kit de développement logiciel (SDK) d’appareil Azure IoT pour C. Nous allons vous montrer comment initialiser la bibliothèque de périphériques, envoyer des événements à IoT Hub et recevoir des messages de sa part. Les informations de cet article sont suffisantes pour commencer à utiliser le Kit de développement logiciel (SDK), mais elles vous fournissent également des indications qui vous permettront d’obtenir des informations supplémentaires sur les bibliothèques.

## <a name="sdk-architecture"></a>Architecture du kit de développement logiciel (SDK)
Vous trouverez [**Azure IoT device SDK pour C**](https://github.com/Azure/azure-iot-sdk-c) dans le référentiel GitHub. Vous pouvez consulter les détails de l’[API dans Référence de l’API C](https://azure.github.io/azure-iot-sdk-c/index.html).

Vous trouverez la dernière version des bibliothèques dans la branche **maître** de ce référentiel :

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Le dépôt contient l’ensemble des kits de développement logiciel d’appareils Azure IoT. Toutefois, cet article concerne plus particulièrement le Kit de développement logiciel Azure IoT device SDK *pour C* , qui se trouve dans le dossier **c** .

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

* L’implémentation de base du Kit de développement logiciel (SDK) se trouve dans le dossier **iothub\_client** qui contient l’implémentation de la couche d’API la plus basse du Kit de développement logiciel (SDK) : la bibliothèque **IoTHubClient**. La bibliothèque **IoTHubClient** contient des API implémentant des messages bruts permettant d’envoyer des messages à IoT Hub et de recevoir des messages de ce dernier. Quand vous utilisez cette bibliothèque, vous êtes chargé de l’implémentation de la sérialisation des messages (en utilisant éventuellement l’exemple de sérialiseur décrit ci-dessous), mais d’autres détails concernant la communication avec IoT Hub sont automatiquement gérés.
* Le dossier **serializer** contient des fonctions d’assistance et des exemples montrant comment sérialiser des données avant d’effectuer un envoi à Azure IoT Hub à l’aide de la bibliothèque cliente. Notez que l’utilisation du sérialiseur n’est pas obligatoire et est fournie uniquement à titre de commodité. Lorsque vous utilisez la bibliothèque **serializer** , vous commencez par définir un modèle désignant les événements que vous souhaitez envoyer à IoT Hub, ainsi que les messages que vous attendez de sa part. Une fois le modèle défini, le Kit de développement logiciel (SDK) vous fournit une surface d’API qui vous permet de travailler facilement avec des événements et des messages, sans que vous ayez à vous soucier des détails de la sérialisation.
  La bibliothèque dépend d’autres bibliothèques open source qui implémentent le transport à l’aide de plusieurs protocoles (MQTT, AMQP).
* La bibliothèque **IoTHubClient** dépend d’autres bibliothèques open source :
  * La bibliothèque de [l’utilitaire partagé Azure C](https://github.com/Azure/azure-c-shared-utility), qui fournit des fonctionnalités communes pour les tâches de base (comme la manipulation de chaînes ou de listes, les E/S, etc.) nécessaires dans plusieurs Kits de développement logiciel (SDK) C liés à Azure.
  * La bibliothèque [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) , qui est l’implémentation côté client du protocole AMQP optimisée pour les appareils avec contraintes de ressources.
  * La bibliothèque [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) , qui est une bibliothèque à usage général implémentant le protocole MQTT et optimisée pour les appareils avec contraintes de ressources.

Un exemple de code permet de mieux comprendre le processus. Les sections suivantes vous guident à travers quelques-uns des exemples d’applications inclus dans le kit de développement logiciel. Cela devrait vous donner une idée des différentes fonctionnalités des couches architecturales du kit de développement logiciel (SDK) et vous initier au fonctionnement de l’API.

## <a name="before-running-the-samples"></a>Préalable à l’exécution des exemples
Avant de pouvoir exécuter les exemples du Kit de développement logiciel (SDK) d’appareil Azure IoT pour C, vous devez, le cas échéant, créer une instance du service sur votre abonnement Azure et effectuer 2 tâches :

* Préparer votre environnement de développement
* Obtenir les informations d’identification de l’appareil

Si vous devez créer une instance d’Azure IoT Hub dans votre abonnement Azure, suivez les instructions mentionnées [ici](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md).

Le [fichier readme (Lisez-moi)](https://github.com/Azure/azure-iot-sdk-c) inclus avec le Kit de développement logiciel (SDK) fournit des instructions pour préparer votre environnement de développement et obtenir les informations d’identification de l’appareil.
et les sections suivantes contiennent des commentaires supplémentaires sur ces instructions.

### <a name="preparing-your-development-environment"></a>Préparation de votre environnement de développement
Même si les packages sont fournis pour certaines plateformes (telles que NuGet pour Windows ou apt_get pour Debian et Ubuntu) et que les exemples utilisent ces packages quand ils sont disponibles, les instructions ci-dessous expliquent comment générer la bibliothèque et les exemples directement à partir du code.

Tout d’abord, vous devez obtenir un exemplaire du kit de développement logiciel (SDK) à partir de GitHub, puis générer la source. Vous devez obtenir une copie de la source à partir de la branche **maître** du [référentiel GitHub](https://github.com/Azure/azure-iot-sdk-c):

Après avoir téléchargé une copie de la source, vous devez effectuer les étapes décrites dans l’article du Kit de développement logiciel (SDK) [« Prepare your development environment »](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)(Préparez votre environnement de développement).

Voici quelques conseils pour vous aider à exécuter la procédure décrite dans le guide de préparation :

* Au moment d’installer l’utilitaire **CMake**, choisissez l’option permettant d’ajouter **CMake** à la variable d’environnement PATH destinée à **tous les utilisateurs** (l’ajout à **l’utilisateur actuel** fonctionne aussi) :
  
  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)
* Avant d’ouvrir **l’invite de commandes Développeur pour VS2015**, installez les outils en ligne de commande Git. Pour installer ces outils, exécutez les opérations suivantes :
  
  1. Lancez le programme d’installation **Microsoft Visual Studio 2015** (ou choisissez **Microsoft Visual Studio 2015** dans le panneau de configuration **Programmes et fonctionnalités**, puis sélectionnez **Modifier**).
  2. Assurez-vous que la fonctionnalité **Git pour Windows** est sélectionnée dans le programme d’installation. Vous pouvez également sélectionner l’option **Extension GitHub pour Visual Studio** pour fournir l’intégration à l’IDE :
     
        ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)
  3. Exécutez l’Assistant d’installation pour installer les outils.
  4. Ajoutez le répertoire **bin** d’outils Git à la variable d’environnement **PATH**. Sous Windows, l'opération se présente comme suit :
     
        ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

Quand vous avez terminé toutes les étapes décrites dans la page [« Prepare your development environment »](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) (Préparez votre environnement de développement), vous êtes prêt à compiler les exemples d’applications.

### <a name="obtaining-device-credentials"></a>Obtention des informations d’identification d’appareil
Maintenant que votre environnement de développement est configuré, vous devez à présent obtenir un ensemble d’informations d’identification d’appareils.  Pour qu’un appareil puisse accéder à un IoT Hub, vous devez d’abord ajouter l’appareil au registre des identifiés de l’IoT Hub. Lorsque vous ajoutez votre périphérique, vous obtenez un jeu d’informations d’identification dont vous avez besoin pour permettre au périphérique de se connecter au hub IoT. Les exemples d’application qui figurent dans la section qui suit attendent ces informations d’identification sous la forme de **chaîne de connexion de périphérique**.

Le référentiel open source du Kit de développement logiciel (SDK) fournit deux outils permettant de gérer le IoT Hub. L’un est une application Windows appelée *Explorateur d’appareils*. Le second est un outil d’interface de ligne de commande (CLI) interplateforme basé sur node.js appelé *iothub-explorer*. Pour obtenir plus d’informations sur ces outils, cliquez [ici](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md).

Comme nous allons passer en revue l’exécution des exemples sur Windows dans cet article, nous utilisons l’outil Explorateur d’appareils. Mais vous pouvez également utiliser iothub-explorer si vous préférez les outils de l’interface de ligne de commande (CLI).

L’outil [Explorateur d’appareils](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) utilise les bibliothèques de service Azure IoT pour effectuer diverses fonctions sur IoT Hub, notamment ajouter des appareils. Si vous utilisez l'outil Explorateur d'appareils pour ajouter un appareil, vous obtiendrez une chaîne de connexion correspondante. Vous avez besoin de cette chaîne de connexion pour que les exemples d'applications s'exécutent.

Si vous n’êtes pas déjà familiarisé avec la procédure, la procédure qui suit explique comment utiliser l’outil Explorateur d’appareils pour ajouter un périphérique et obtenir une chaîne de connexion d’appareil.

Un programme d’installation Windows de l’outil Explorateur d’appareils est disponible sur la [page de publication du Kit de développement logiciel (SDK)](https://github.com/Azure/azure-iot-sdks/releases). Mais vous pouvez également exécuter l’outil directement à partir de son code en ouvrant **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** dans **Microsoft Visual Studio 2015** et en générant la solution.

Au moment d’exécuter le programme, vous allez voir cette interface :

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Entrez votre **chaîne de connexion IoT Hub** dans le premier champ, puis cliquez sur **Mettre à jour**. Vous configurez ainsi l’outil pour lui permettre de communiquer avec IoT Hub.

Une fois la chaîne de connexion IoT Hub configurée, cliquez sur l’onglet **Gestion** :

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

C’est de cet endroit que vous allez gérer les périphériques inscrits dans votre IoT Hub.

Vous pouvez créer un périphérique en cliquant sur le bouton **Créer** . Une boîte de dialogue avec un jeu de clés (primaire et secondaire) préalablement remplies s’affiche. Il suffit d’entrer un **ID d’appareil**, puis de cliquer sur **Créer**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Une fois l’appareil créé, la liste des appareils est actualisée avec tous les appareils inscrits, notamment celui que vous venez de créer. Si vous cliquez avec le bouton droit sur le nouvel appareil, le menu qui suit s’affiche :

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Si vous choisissez l’option **Copier la chaîne de connexion de l’appareil sélectionné** , la chaîne de connexion en question est copiée dans le Presse-papiers. Conservez une copie de la chaîne de connexion de l’appareil. Vous en aurez besoin au moment d’exécuter les exemples d’application décrits dans les prochaines sections.

Une fois que vous avez effectué les opérations ci-dessus, vous êtes prêt à commencer l’exécution du code. En haut du fichier source principal, les deux exemples décrits ci-dessous contiennent une constante qui permet d’entrer une chaîne de connexion. Par exemple, la ligne correspondante de l’application **iothub\_client\_sample\_amqp** se présente comme suit.

```
static const char* connectionString = "[device connection string]";
```

Si vous souhaitez poursuivre l’opération, saisissez la chaîne de connexion de votre appareil ici, recompilez la solution. Vous serez alors en mesure d’exécuter l’exemple.

## <a name="iothubclient"></a>IoTHubClient
Dans le dossier **iothub\_client** du référentiel [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) trouve un dossier **samples** contenant une application appelée **iothub\_client\_sample\_amqp**.

La version Windows de l’application **iothub\_client\_sample\_ampq** contient la solution Visual Studio suivante :

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Cette solution inclut un seul projet : Il est important de noter que cette solution installe quatre packages NuGet :

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.AmqpTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.uamqp

Quand vous travaillez avec le Kit de développement logiciel (SDK), vous devez toujours utiliser le package **Microsoft.Azure.C.SharedUtility** . Étant donné que cet exemple s’appuie sur AMQP, vous devez également inclure les packages **Microsoft.Azure.uamqp** et **Microsoft.Azure.IoTHub.AmqpTransport** (il existe des packages équivalents pour MQTT et HTTP). Comme l’exemple utilise la bibliothèque **IoTHubClient**, vous devez également inclure le package **Microsoft.Azure.IoTHub.IoTHubClient** dans votre solution.

L’implémentation de l’exemple d’application est disponible dans le fichier source **iothub\_client\_sample\_amqp.c**.

Nous allons utiliser cet exemple d’application pour vous montrer les éléments requis pour utiliser la bibliothèque **IoTHubClient** .

### <a name="initializing-the-library"></a>Initialisation de la bibliothèque
> [!NOTE]
> Avant d’utiliser les bibliothèques, vous devrez peut-être procéder à une initialisation spécifique à la plateforme. Par exemple, si vous prévoyez d’utiliser AMQP sur Linux, vous devez initialiser la bibliothèque OpenSSL. Les exemples du [référentiel GitHub](https://github.com/Azure/azure-iot-sdk-c) appellent la fonction de l’utilitaire **platform_init** quand le client démarre et appelle la fonction **platform_deinit** avant de se fermer. Ces fonctions sont déclarées dans le fichier d’en-tête « platform.h ». Nous vous conseillons d’examiner les définitions de ces fonctions pour votre plateforme cible dans le [référentiel](https://github.com/Azure/azure-iot-sdk-c) pour déterminer si vous devez inclure du code d’initialisation de la plateforme dans votre client.
> 
> 

Pour commencer à travailler avec les bibliothèques, vous devez d’abord attribuer un pointeur client IoT Hub :

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Notez que nous transférons une copie de la chaîne de connexion d’appareil vers cette fonction (celle que nous avons obtenue dans l’Explorateur d’appareils). Nous désignons également le protocole à utiliser. Cet exemple utilise AMQP, mais MQTT et HTTP sont également possibles.

Lorsque vous disposez d’un pointeur **IOTHUB\_CLIENT\_HANDLE** valide, vous pouvez commencer à appeler des API pour envoyer des événements et recevoir des messages de la part d’IoT Hub. Nous examinerons cela plus tard.

### <a name="sending-events"></a>Envoi des événements
L’envoi d’événements vers IoT Hub exige que vous effectuiez les opérations suivantes :

Tout d’abord, créez un message :

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

Ensuite, envoyez le message :

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

À chaque fois que vous envoyez un message, vous spécifiez une référence à une fonction de rappel invoquée lors de l’envoi des données :

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Notez l’appel de la fonction **IoTHubMessage\_Destroy** quand vous en avez terminé avec le message. Vous devez effectuer cet appel pour libérer les ressources affectées au moment de la création du message.

### <a name="receiving-messages"></a>Réception de messages
La réception d’un message est une opération asynchrone. Tout d’abord, vous enregistrez un rappel à appeler lorsque le périphérique reçoit un message :

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Le dernier paramètre est un pointeur vide vers ce que vous souhaitez. Dans l’exemple, il s’agit d’un pointeur vers un entier, mais il peut s’agir d’un pointeur vers une structure de données plus complexe. Ainsi, la fonction de rappel peut fonctionner sur l’état partagé avec l’appelant de cette fonction.

Lorsque le périphérique reçoit un message, la fonction de rappel enregistrée est appelée :

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Notez que vous utilisez la fonction **IoTHubMessage\_GetByteArray** pour récupérer le message, qui, dans cet exemple, est une chaîne.

### <a name="uninitializing-the-library"></a>Désinitialisation de la bibliothèque
Quand vous avez terminé d’envoyer des événements et de recevoir des messages, vous pouvez annuler l’initialisation de la bibliothèque IoT : Pour ce faire, lancez l'appel de fonction suivant :

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Cette opération permet de libérer les ressources allouées précédemment par la fonction **IoTHubClient\_CreateFromConnectionString**.

Comme vous pouvez le voir, il est facile d’envoyer des événements et de recevoir des messages avec la bibliothèque **IoTHubClient** . La bibliothèque traite les détails de la communication avec IoT Hub, notamment le protocole à utiliser (du point de vue du développeur, il s’agit d’une option de configuration simple).

La bibliothèque **IoTHubClient** offre également un contrôle précis de la manière de sérialiser les événements que votre appareil envoie à IoT Hub. Dans certains cas, il s’agit d’un avantage, mais dans d’autres cas, il s’agit d’un détail d’implémentation qui ne vous concerne pas. Si tel est le cas, vous pouvez envisager d’utiliser la bibliothèque **serializer** , que nous décrirons dans la prochaine section.

## <a name="serializer"></a>serializer
Sur le plan conceptuel, la bibliothèque **serializer** se trouve au-dessus de la bibliothèque **IoTHubClient** dans le Kit de développement logiciel (SDK). Elle utilise la bibliothèque **IoTHubClient** pour la communication sous-jacente avec IoT Hub, mais ajoute des fonctions de modélisation qui soulagent le développeur de la charge que représente le traitement de la sérialisation de message. Le fonctionnement de la bibliothèque est mieux illustré par un exemple.

Le dossier **serializer** du référentiel [azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c) est un dossier **samples** (exemples) contenant une application appelée **simplesample\_amqp**. La version Windows de cet exemple inclut la solution Visual Studio suivante :

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Comme l’exemple précédent, celui-ci contient plusieurs packages NuGet :

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.AmqpTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.uamqp

Nous avons vu la plupart de ces éléments dans l’exemple précédent, mais **Microsoft.Azure.IoTHub.Serializer** est nouveau. Ceci est obligatoire lorsque nous utilisons la bibliothèque **serializer** .

L’implémentation de l’exemple d’application est disponible dans le fichier **simplesample\_amqp.c**.

Les sections suivantes vous guident à travers les éléments clés de cet exemple.

### <a name="initializing-the-library"></a>Initialisation de la bibliothèque
Pour commencer à travailler avec la bibliothèque **serializer** , vous devez appeler les API d’initialisation :

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

L’appel à la fonction **serializer\_init** est un appel unique utilisé pour initialiser la bibliothèque sous-jacente. Vous pouvez ensuite appeler la fonction **IoTHubClient\_CreateFromConnectionString**, qui est la même API que celle de l’exemple **IoTHubClient**. Cet appel définit la chaîne de connexion de l’appareil (c’est également là que vous choisissez le protocole à utiliser). Notez que cet exemple utilise AMQP comme protocole de transport, mais que HTTP peut également être utilisé.

Enfin, appelez la fonction **CREATE\_MODEL\_INSTANCE**. Notez que **WeatherStation** est l’espace de noms du modèle et **ContosoAnemometer**, celui du modèle lui-même. Une fois que l’instance de modèle est créée, vous pouvez l’utiliser pour commencer à envoyer des événements et à recevoir des messages. Cependant, il est important de comprendre ce qu’est un modèle.

### <a name="defining-the-model"></a>Définition du modèle
Un modèle de la bibliothèque **serializer** définit les événements que votre appareil peut envoyer à IoT Hub et les messages, appelés *actions* dans le langage de la modélisation qu’il peut recevoir. Un modèle se définit avec un ensemble de macros C comme dans l’exemple d’application **simplesample\_amqp** :

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Les macros **BEGIN\_NAMESPACE** et **END\_NAMESPACE** se servent toutes les deux de l’espace de noms du modèle comme argument. En principe, tous les éléments compris entre ces deux macros sont la définition du ou des modèles et structures de données que les modèles utilisent.

Dans cet exemple, il existe un seul modèle appelé **ContosoAnemometer**. Ce modèle définit deux événements que votre périphérique peut envoyer à IoT Hub : **DeviceId** et **WindSpeed**. Il définit également trois actions (messages) que votre appareil peut recevoir : **TurnFanOn**, **TurnFanOff** et **SetAirResistance**. Chaque événement possède un type et chaque action a un nom (et éventuellement, un ensemble de paramètres).

Les événements et les actions définis dans le modèle définissent une surface API que vous pouvez utiliser pour envoyer des événements à IoT Hub et répondre aux messages envoyés à l’appareil. Un exemple permet de mieux comprendre.

### <a name="sending-events"></a>Envoi des événements
Ce modèle définit les événements que vous pouvez envoyer à IoT Hub. Dans cet exemple, cela correspond à l’un des deux événements définis à l’aide de la macro **WITH_DATA**. Par exemple, si vous souhaitez envoyer un événement **WindSpeed** à un concentrateur IoT, vous devez exécuter quelques opérations. La première consiste à définir les données que vous souhaitez envoyer :

```
myWeather->WindSpeed = 15;
```

Le modèle que nous avons défini précédemment nous permet de le faire en définissant un membre d’une **structure**. Ensuite, nous sérialisons l’événement que vous souhaitez envoyer :

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

Ce code sérialise l’événement vers une mémoire tampon (référencée par **destination**). Enfin, nous devons envoyer l’événement à IoT Hub avec ce code :

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

Il s’agit d’une fonction d’assistance de l’exemple d’application qui envoie notre événement sérialisé à IoT Hub :

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

Ce code ressemble beaucoup à ce que nous avons vu dans l’application **iothub\_client\_sample\_amqp**, dans laquelle nous avons créé un message à partir d’un tableau d’octets, puis utilisé **IoTHubClient\_SendEventAsync** pour l’envoyer à IoT Hub. Ensuite, il suffit de libérer le pointeur de message et le tampon de données sérialisées que nous avons affectés plus tôt.

L’avant-dernier paramètre de **IoTHubClient\_SendEventAsync** est une référence à une fonction de rappel appelée lorsque l’envoi des données a abouti. Voici un exemple de fonction de rappel :

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Le deuxième paramètre est un pointeur vers le contexte utilisateur, le même que celui que nous avons transmis à **IoTHubClient\_SendEventAsync**. Dans ce cas, le contexte est un simple compteur, mais il peut s’agir de tout élément que vous choisissez.

C’est tout ce qu’il faut savoir sur l’envoi d’événements. Le seul sujet qu’il nous reste à aborder est le mode de réception des messages.

### <a name="receiving-messages"></a>Réception de messages
La réception d’un message fonctionne de la même façon que les messages de la bibliothèque **IoTHubClient** . Tout d’abord, vous enregistrez une fonction de rappel de message :

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Vous écrivez ensuite la fonction de rappel invoquée à la réception d’un message :

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Ce code est réutilisable. Le code est le même quelle que soit la solution. Cette fonction reçoit le message et prend en charge son acheminement vers la fonction appropriée via l’appel **d’EXECUTE\_COMMAND**. La fonction appelée à ce stade dépend de la définition des actions dans notre modèle.

Quand vous définissez une action dans votre modèle, vous devez mettre en œuvre une fonction qui est appelée au moment où l’appareil reçoit le message correspondant. Par exemple, si votre modèle définit cette action :

```
WITH_ACTION(SetAirResistance, int, Position)
```

Vous devez définir une fonction avec cette signature :

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Notez que le nom de la fonction correspond au nom de l’action dans le modèle et que les paramètres de la fonction correspondent aux paramètres spécifiés pour l’action. Le premier paramètre est toujours requis et contient un pointeur vers l’instance de notre modèle.

Lorsque le périphérique reçoit un message qui correspond à cette signature, la fonction associée est appelée. Ainsi, hormis l’inclusion du code réutilisable d’ **IoTHubMessage**, recevoir des messages revient à définir une fonction simple pour chaque action définie dans votre modèle.

### <a name="uninitializing-the-library"></a>Désinitialisation de la bibliothèque
Lorsque vous avez terminé d’envoyer les données et la réception des messages, vous pouvez annuler l’initialisation de la bibliothèque IoT :

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Chacune de ces trois fonctions s’aligne sur les trois fonctions d’initialisation décrites précédemment. L’appel de ces API vous permet de libérer les ressources affectées au préalable.

## <a name="next-steps"></a>Étapes suivantes
Cet article a abordé les principes de base de l’utilisation des bibliothèques dans le **Kit de développement logiciel (SDK) d’appareil Azure IoT (Azure IoT device SDK) pour C**. Il vous a fourni suffisamment d’informations pour comprendre ce qui est inclus dans le Kit de développement logiciel (SDK), son architecture et la manière d’utiliser les exemples Windows. Le prochain article poursuit la description du kit de développement logiciel en approfondissant les explications relatives à [la bibliothèque IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Pour en savoir plus sur le développement pour IoT Hub, voir les [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks].

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Simulation d’un appareil avec le Kit de développement logiciel (SDK) de la passerelle IoT][lnk-gateway]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Feb17_HO2-->


