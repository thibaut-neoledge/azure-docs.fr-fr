<properties
	pageTitle="Utilisation du Kit de développement logiciel (SDK) d’appareil Azure IoT pour C | Microsoft Azure"
	description="Découvrez et commencez à utiliser l’exemple de code dans le Kit de développement logiciel d’appareil Azure IoT pour C."
	services="iot-hub"
	documentationCenter=""
	authors="MichelBarnett"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2015"
     ms.author="michelb"/>

# Présentation du kit de développement logiciel (SDK) d’appareil Azure IoT pour C

Le **kit de développement logiciel (SDK) d’appareil Azure IoT** est un ensemble de bibliothèques conçu pour simplifier le processus d’envoi d’événements et de réception de messages depuis le service **Azure IoT Hub**. Il existe différentes variantes de ce kit de développement logiciel, chacune concernant une plateforme spécifique, mais cet article met l’accent sur le **kit de développement logiciel (SDK) d’appareil Azure IoT pour C**.

Le kit de développement logiciel d’appareil Azure IoT pour C est rédigé en code ANSI C (C99) afin d’optimiser sa portabilité. Il est ainsi adapté pour fonctionner sur plusieurs plateformes et périphériques, en particulier lorsque la réduction de l’encombrement du disque et de l’empreinte mémoire est une priorité.

Le kit de développement logiciel a été testé sur un large éventail de plateformes (voir la [documentation relative au kit de développement logiciel](https://github.com/Azure/azure-iot-sdks/tree/master/c) pour plus d’informations). Bien que cet article contienne des procédures pas à pas d’exemple de code s’exécutant sur la plateforme *Windows*, n’oubliez pas que le code décrit ci-dessous est exactement le même dans l’ensemble des plateformes prises en charge.

Dans cet article, vous allez découvrir l’architecture du kit de développement logiciel (SDK) d’appareil Azure IoT pour C. Nous allons vous montrer comment initialiser la bibliothèque de périphériques, envoyer des événements à IoT Hub et recevoir des messages de sa part. Les informations de cet article sont suffisantes pour commencer à utiliser le kit de développement logiciel (SDK), mais elles vous fournissent également des pointeurs qui vous permettront d’obtenir des informations supplémentaires sur les bibliothèques.


## Architecture du kit de développement logiciel (SDK)

Vous trouverez le **kit de développement logiciel (SDK) d’appareil Azure IoT pour C** dans le référentiel GitHub suivant :

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

Vous trouverez la dernière version des bibliothèques dans la branche **maître** de ce référentiel :

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Le référentiel contient l’ensemble des kits de développement logiciel d’appareils Azure IoT. Cependant, cet article concerne plus particulièrement le kit de développement logiciel (SDK) d’appareil Azure IoT *pour C* se trouvant dans le dossier **c**.

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

Vous trouverez l’implémentation de base du kit de développement logiciel dans les dossiers **common**, **iothub\_client** et **serializer** du référentiel. Le dossier **common** contient le code partagé utilisé dans les bibliothèques (en général, vous n’utilisez pas directement le code du dossier **common**). Toutefois, les dossiers **iothub\_client** et **serializer** contiennent des implémentations des deux couches distinctes du kit de développement logiciel que vous allez utiliser dans votre code.

-   **IoTHubClient** : le dossier **iothub\_client** contient l’implémentation de la couche d’API la plus basse de la bibliothèque dans le kit de développement logiciel, la bibliothèque **IoTHubClient**. la bibliothèque **IoTHubClient** contient des API permettant d’envoyer des événements à IoT Hub et de recevoir des messages de ce dernier. Si vous utilisez cette bibliothèque, vous êtes responsable de l’implémentation de la sérialisation du message, mais d’autres détails concernant la communication avec IoT Hub sont gérés pour vous.

-   **serializer** : le dossier **serializer** contient l’implémentation d’une bibliothèque **serializer**. La bibliothèque **serializer** ajoute les fonctionnalités de modélisation aux fonctions déjà fournies par **IoTHubClient**. Lorsque vous utilisez la bibliothèque **serializer**, vous commencez par définir un modèle désignant les événements que vous souhaitez envoyer à IoT Hub, ainsi que les messages que vous attendez de sa part. Cependant, une fois le modèle défini, le kit de développement logiciel vous fournit une surface d’API qui vous permet de travailler facilement avec des événements et des messages, sans que vous ayez à vous soucier des détails de la sérialisation.

Tout cela est plus facile à comprendre lorsqu’on l’illustre le propos avec un exemple de code. Les sections suivantes vous guident à travers quelques-uns des exemples d’applications inclus dans le kit de développement logiciel. Cela devrait vous donner une idée des différentes fonctionnalités des couches architecturales du kit de développement logiciel (SDK) et vous initier au fonctionnement de l’API.

## Préalable à l’exécution des exemples

Avant de pouvoir exécuter les exemples dans le kit de développement logiciel d’appareil Azure IoT pour C, vous devez effectuer deux tâches : préparer votre environnement de développement et récupérer les informations d’identification d’appareil. Le [fichier Lisez-moi](https://github.com/Azure/azure-iot-sdks/tree/master/c) inclus dans le kit de développement logiciel fournit des instructions pour les deux tâches, et les sections suivantes contiennent des commentaires supplémentaires sur ces instructions.

### Préparation de votre environnement de développement

Tout d’abord, vous devez obtenir un exemplaire du kit de développement logiciel (SDK) à partir de GitHub, puis générer la source. Vous devez extraire une copie de la source de la branche **maître** du référentiel GitHub :

<https://github.com/Azure/azure-iot-sdks>

Une fois que vous avez téléchargé une copie de la source,vous devez exécuter certaines opérations avant d’exécuter votre code. Vous trouverez un récapitulatif de ces étapes dans l’article sur le kit de développement logiciel intitulé [Préparer votre environnement de développement](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md). La majeure partie du travail que vous aurez à faire pour Windows implique la [préparation de bibliothèques Qpid Proton, ](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#preparing-qpid-proton-libraries-in-windows) ainsi que la [vérification de votre environnement](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#verify-your-environment). Voici quelques conseils pour vous aider à exécuter la procédure décrite dans le guide de préparation :

-   Lorsque vous créez votre variable d’environnement **PROTON\_PATH**, faites-en une variable d’environnement **système** comme indiqué ci-dessous :

  ![](media/iot-hub-device-sdk-c-intro/07-EnvironmentVariables.PNG)

-   Au moment d’installer l’utilitaire **CMake**, choisissez l’option permettant d’ajouter **CMake** au chemin d’accès système PATH destiné à **tous les utilisateurs** (l’ajout à **l’utilisateur actuel** fonctionne aussi) :

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)

-   Veillez à installer la version de Python adéquate. En général, dans Windows, vous devez installer la version x 86 :

  ![](media/iot-hub-device-sdk-c-intro/09-Python.PNG)

Vous pouvez utiliser les options par défaut dans le programme d’installation.

- Ajoutez le répertoire Python à la variable d’environnement système **PATH**. Sous Windows, cela se présente comme suit : ![](media/iot-hub-device-sdk-c-intro/16-PythonPath.PNG)

-   Avant d’ouvrir l’**invite de commandes Développeur pour VS2015**, installez les *outils de ligne de commande Git*. Pour installer ces outils, exécutez les opérations suivantes :

	1. Lancez le programme d’installation **Visual Studio 2015** (ou choisissez **Microsoft Visual Studio 2015** dans le panneau de configuration **Programmes et fonctionnalités**, puis sélectionnez **Modifier**).
	2. Assurez-vous que la fonctionnalité **Git pour Windows** est sélectionnée dans le programme d’installation. Vous pouvez également vérifier l’option **GitHub Extension pour Visual Studio** pour fournir l’intégration IDE : ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)
	3. Exécutez l’Assistant d’installation pour installer les outils.
	4. Ajoutez le répertoire d’outils Git **bin** à la variable d’environnement système **PATH**. Sous Windows, cela se présente comme suit : ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

Lorsque vous exécutez le script **build\_proton.cmd** depuis l’invite de commande, le script extrait la source Proton de son référentiel GitHub, puis génère les bibliothèques Proton.

Si vous souhaitez exécuter les exemples MQTT, vous devez respecter les instructions de la section [Génération de la bibliothèque Apache MQTT](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#building-the-apache-mqtt-library-in-windows) dans la documentation du kit de développement logiciel. Enfin, la dernière chose que vous devrez faire consiste à exécuter **build.cmd**.

Une fois les opérations qui précèdent terminées, vous êtes prêt à compiler les exemples d’applications.

### Obtention des informations d’identification d’appareil

Maintenant que votre environnement de développement est configuré, il ne vous reste qu’à obtenir un ensemble d’informations d’identification d’appareils. Pour pouvoir accéder à un concentrateur IoT, vous devez d’abord ajouter le périphérique au registre de périphérique du concentrateur IoT. Lorsque vous ajoutez votre périphérique, vous obtenez un jeu d’informations d’identification dont vous avez besoin pour permettre au périphérique de se connecter au hub IoT. Les exemples d’application qui figurent dans la section qui suit attendent ces informations d’identification sous la forme de **chaîne de connexion de périphérique**.

L’outil [Explorateur de périphériques](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) (contenu dans le kit de développement logiciel d’appareil Azure IoT) utilise les bibliothèques de service Azure IoT pour effectuer diverses fonctions IoT Hub, et notamment ajouter des périphériques. Si vous utilisez l’Explorateur de périphérique pour ajouter un appareil, vous devez obtenir la chaîne de connexion correspondante, et vous avez besoin de cette chaîne de connexion pour exécuter les exemples d’applications.

Si vous n’êtes pas déjà familiarisé avec la procédure, la procédure qui suit explique comment utiliser l’Explorateur de périphérique pour ajouter un périphérique et obtenir une chaîne de connexion d’appareil.

Ouvrez **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** dans **Visual Studio 2015** et créez la solution. Au moment d’exécuter le programme, vous allez voir cette interface :

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Saisissez votre **chaîne de connexion IoT Hub** dans le premier champ, puis cliquez sur le bouton **Mettre à jour**. Vous configurez ainsi l’outil pour lui permettre de communiquer avec IoT Hub.

Une fois la chaîne de connexion IoT Hub configurée, cliquez sur l’onglet **Gestion** :

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

C’est de cet endroit que vous allez gérer les périphériques inscrits dans votre IoT Hub.

Vous pouvez créer un périphérique en cliquant sur le bouton **Créer**. Une boîte de dialogue avec un jeu de clés (primaire et secondaire) s’affiche. Elle est complètement renseignée. Il suffit de saisir un **ID de périphérique**, puis de cliquer sur le bouton **Créer**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Une fois l’appareil créé, la liste Périphériques est actualisée avec tous les appareils inscrits, et notamment celui que vous venez de créer. Si vous effectuez un clic droit sur le nouveau périphérique, le menu qui suit s’affiche :

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Si vous choisissez l’option **Copier la chaîne de connexion du périphérique sélectionné**, la chaîne en question est copiée dans le presse-papiers. Conservez une copie. Vous en aurez besoin au moment d’exécuter les exemples d’application décrits ci-dessous.

Une fois que vous avez effectué les opérations ci-dessus, vous êtes prêt à commencer l’exécution du code. Les deux exemples décrits ci-dessous contiennent, en haut du fichier source principal, une constante qui permet de saisir une chaîne de connexion. Par exemple, voici la ligne correspondant à l’application **iothub\_client\_sample\_amqp**.

```
static const char* connectionString = "[device connection string]";
```

Si vous souhaitez poursuivre l’opération, saisissez la chaîne de connexion de votre appareil ici, recompilez la solution. Vous serez alors en mesure d’exécuter l’exemple.

## IoTHubClient

Le dossier **iothub\_client** du référentiel azure-iot-sdks contient un dossier d’**exemples** contenant une application appelée **iothub\_client\_sample\_amqp**.

La version Windows de l’application **iothub\_client\_sample\_ampq** contient la solution Visual Studio suivante :

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Cette solution inclut un seul projet : Il est cependant important de noter que cette solution permet d’installer quatre packages GitHub :

  ![](media/iot-hub-device-sdk-c-intro/17-iothub-client-sample-amqp-githubpackages.PNG)

Lorsque vous travaillez avec le kit de développement logiciel, vous devez utiliser **Microsoft.Azure.IoTHub.Common** Étant donné que cet exemple s’appuie sur AMQP, nous devons également inclure les packages **Apache.QPID.Proton.AzureIoT** et **Microsoft.Azure.IoTHub.AmqpTransport** (il existe des packages équivalents pour HTTP). Et étant donné que l’exemple utilise la bibliothèque **IoTHubClient**, nous devons également inclure le projet **Microsoft.Azure.IoTHub.IoTHubClient** dans notre solution.

Vous trouverez l’implémentation de l’exemple d’application dans le fichier source **iothub\_client\_sample\_amqp.c** :

  ![](media/iot-hub-device-sdk-c-intro/13-iothub_client_sample_amqp_c.PNG)

Nous allons utiliser cet exemple d’application pour vous montrer les éléments requis pour utiliser la bibliothèque **IoTHubClient**.


### Initialisation de la bibliothèque

Pour commencer à travailler avec les bibliothèques, vous devez d’abord attribuer un pointeur client IoT Hub :

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Notez que nous transférons une copie de notre chaîne de connexion de périphérique vers cette fonction (celle que nous avons obtenue dans l’Explorateur de périphériques). Nous allons également désigner le protocole que nous souhaitons utiliser. Nous utilisons AMQP dans cet exemple, mais HTTP est également une option.

Lorsque vous disposez d’un pointeur **IOTHUB\_CLIENT\_HANDLE** valide, vous pouvez commencer à appeler des API pour envoyer des événements et recevoir des messages de la part d’IoT Hub. Nous examinerons cela plus tard.

### Envoi des événements

L’envoi d’événements vers IoT Hub, nécessite que vous effectuiez les opérations suivantes :

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

Notez l’appel de la fonction **IoTHubMessage\_Destroy** lorsque vous en avez terminé avec votre message. Cette opération sert à libérer les ressources affectées au moment de la création du message.

### Réception de messages

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

Notez la façon dont vous utilisez la fonction **IoTHubMessage\_GetByteArray** pour récupérer le message, qui, dans cet exemple, est une chaîne.

### Désinitialisation de la bibliothèque

Lorsque vous avez fini d’envoyer des événements et de recevoir des messages, vous pouvez annuler l’initialisation de la bibliothèque IoT. Pour ce faire, vous devez utiliser l’appel de fonction suivant :

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Cette opération permet de libérer les ressources allouées précédemment par la fonction **IoTHubClient\_CreateFromConnectionString**.

Comme vous pouvez le voir, il est facile d’envoyer des événements et de recevoir des messages avec la bibliothèque **IoTHubClient**. La bibliothèque est en charge des détails de communication avec IoT Hub, et notamment du protocole à utiliser (qui, du point de vue du développeur, est une option de configuration simple).

La bibliothèque **IoTHubClient** vous offre également un contrôle précis de la façon de sérialiser les événements que votre périphérique envoie à IoT Hub. Dans certains cas, il s’agit d’un avantage, mais dans d’autres cas, il s’agit d’un détail d’implémentation qui ne vous concerne pas. Si tel est le cas, vous pouvez envisager d’utiliser la bibliothèque **serializer**, sujet que nous aborderons ensuite.

## Serializer

Sur le plan conceptuel, la bibliothèque **serializer** se trouve au-dessus de la bibliothèque **IoTHubClient** dans le kit de développement logiciel. Elle utilise la bibliothèque **IoTHubClient** pour la communication sous-jacente avec IoT Hub, mais ajoute des fonctions de modélisation qui soulagent le développeur de la charge que représente le traitement de la sérialisation de message. Le fonctionnement de la bibliothèque est mieux illustré par un exemple.

Le dossier **serializer** du référentiel azure-iot-sdks est un dossier d’**exemples** contenant une application appelée **simplesample\_amqp**. La version Windows de cet exemple inclut la solution Visual Studio suivante :

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Comme l’exemple précédent, celui-ci contient plusieurs packages NuGet :

  ![](media/iot-hub-device-sdk-c-intro/18-simplesample_amqp-githubpackages.PNG)

Nous avons vu la plupart de ces éléments dans l’exemple précédent, mais **Microsoft.Azure.IoTHub.Serializer** est nouveau. Ceci est obligatoire lorsque nous utilisons la bibliothèque **serializer**.

Vous pouvez trouver l’implémentation de l’exemple d’application dans le fichier de code **simplesample\_amqp.c** :

  ![](media/iot-hub-device-sdk-c-intro/15-simplesample_amqp_c.PNG)

Les sections suivantes vous guident à travers les éléments clés de cet exemple.

### Initialisation de la bibliothèque

Pour commencer à travailler avec la bibliothèque **serializer**, vous devez appeler les API d’initialisation :

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

L’appel de la fonction **serializer\_init** est un appel unique utilisé pour initialiser la bibliothèque sous-jacente. Vous pouvez ensuite appeler la fonction **IoTHubClient\_CreateFromConnectionString**, qui est une API similaire à celle que nous avons vue dans l’exemple **IoTHubClient**. Cet appel définit la chaîne de connexion de l’appareil (c’est également là que vous choisissez le protocole à utiliser). Notez que cet exemple utilise AMQP comme protocole de transport, mais nous aurions pu utiliser HTTP.

Enfin, appelez la fonction **CREATE\_MODEL\_INSTANCE**. Notez que **WeatherStation** est l’espace de noms du modèle et **ContosoAnemometer**, celui du modèle lui-même. Une fois que l’instance de modèle est définie, vous pouvez l’utiliser pour commencer à envoyer des événements et à recevoir des messages. Mais tout d’abord, il est important de comprendre ce qu’est un modèle.

### Définition du modèle

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

Les événements et les actions définies dans le modèle définissent une surface API que vous pouvez utiliser pour envoyer des événements à IoT Hub, et répondre aux messages envoyés à l’appareil. Un exemple permet de mieux comprendre.

### Envoi des événements

Ce modèle définit les événements que vous pouvez envoyer à IoT Hub. Dans cet exemple, cela correspond à un des deux événements définis à l’aide de la macro **WITH\_DATA**. Par exemple, si vous souhaitez envoyer un événement **WindSpeed** à IoT Hub, vous devez exécuter quelques opérations. La première consiste à définir les données que vous souhaitez envoyer :

```
myWeather->WindSpeed = 15;
```

Le modèle que nous avons défini plus haut nous permet de le faire simplement, en définissant un membre de structure. Ensuite, nous devons sérialiser l’événement que vous souhaitez envoyer :

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

Ce code ressemble beaucoup à ce que nous avons vu dans l’application **iothub\_client\_sample\_amqp**, pour laquelle nous avons créé un message à partir d’un tableau d’octets, puis utilisé **IoTHubClient\_SendEventAsync** pour l’envoyer à IoT Hub. Ensuite, il suffit de libérer le pointeur de message et le tampon de données sérialisées que nous avons affectés plus tôt.

Du deuxième paramètre **IoTHubClient\_SendEventAsync** au dernier, il est fait référence à une fonction de rappel appelée lorsque l’envoi des données a abouti. Voici un exemple de fonction de rappel :

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Le deuxième paramètre est un pointeur vers le contexte utilisateur, le même que celui que nous avons transmis à **IoTHubClient\_SendEventAsync**. Dans ce cas, ce contexte est un compteur simple, mais il peut s’agir de tout ce que vous voulez.

Tous les éléments nécessaires à l’envoi d’événements sont là. Le seul sujet qu’il nous reste à aborder est le mode de réception des messages.

### Réception de messages

La réception d’un message fonctionne de la même façon que les messages de la bibliothèque **IoTHubClient**. Tout d’abord, vous enregistrez une fonction de rappel de message :

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Vous écrivez ensuite la fonction de rappel invoquée lors de la réception d’un message :

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

Ce code est réutilisable. Il s’agit du même code quelle que soit la solution. Cette fonction reçoit le message et prend en charge son acheminement vers la fonction appropriée via l’appel d’**EXECUTE\_COMMAND**. En gros, la fonction appelée à ce stade dépend de la définition des actions dans notre modèle...

Lorsque vous définissez une action dans votre modèle, vous devez implémenter une fonction qui sera appelée lorsque l’appareil recevra le message correspondant. Par exemple, si votre modèle définit cette action :

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

Lorsque le périphérique reçoit un message qui correspond à cette signature, la fonction associée est appelée. Donc, si l’on met à part l’inclusion du code réutilisable d’**IoTHubMessage**, la réception de messages revient à définir une fonction unique pour chaque action définie dans votre modèle.

### Désinitialisation de la bibliothèque

Lorsque vous avez terminé d’envoyer les données et la réception des messages, vous pouvez annuler l’initialisation de la bibliothèque IoT :

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Chacune de ces trois fonctions s’aligne sur les trois fonctions d’initialisation décrites précédemment. L’appel de ces API vous permet de libérer les ressources affectées au préalable.

## Étapes suivantes

Cet article aborde les principes fondamentaux de l’utilisation des bibliothèques dans le **kit de développement logiciel du périphérique Azure IoT pour C**. Il devrait vous fournir suffisamment d’informations pour comprendre ce qui est inclus dans le kit de développement logiciel, son architecture et la façon d’utiliser avec les exemples Windows. Le prochain article poursuit la description du kit de développement logiciel en approfondissant les explications relatives à [la bibliothèque IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

<!---HONumber=Oct15_HO3-->