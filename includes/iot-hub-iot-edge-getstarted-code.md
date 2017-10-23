## <a name="typical-output"></a>Exemple de résultat

L’exemple ci-après présente la sortie inscrite dans le fichier journal par l’exemple Hello World. Nous avons mis la sortie en forme pour optimiser la lisibilité :

```json
[{
    "time": "Mon Apr 11 13:42:50 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:42:50 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:42:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:43:00 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:45:00 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Extraits de code

Cette section présente certains éléments clés du code dans l’exemple hello\_world.

### <a name="iot-edge-gateway-creation"></a>Création de la passerelle IoT Edge

Pour créer une passerelle, implémentez un *processus de passerelle*. Ce programme crée l’infrastructure interne (le répartiteur), charge les modules IoT Edge et configure le processus de passerelle. IoT Edge fournit la fonction **Gateway\_Create\_From\_JSON** pour vous permettre d’amorcer une passerelle à partir d’un fichier JSON. Pour utiliser la fonction **Gateway\_Create\_From\_JSON**, transmettez-lui le chemin d’accès à un fichier JSON qui spécifie les modules IoT Edge à charger.

Vous trouverez le code du processus de passerelle de l’exemple *Hello World* dans le fichier [main.c][lnk-main-c]. Pour une meilleure lisibilité, l’extrait de code ci-dessous affiche une version abrégée du code du processus de passerelle. Cet exemple crée une passerelle, puis attend que l’utilisateur appuie sur la touche **ENTRÉE** avant de mettre fin à la passerelle.

```C
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

Le fichier de paramètres JSON contient une liste des modules IoT Edge à charger et des liens entre ces modules. Chaque module IoT Edge doit spécifier les éléments suivants :

* **nom** : un nom unique pour le module.
* **chargeur** : un chargeur qui sait comment charger le module souhaité. Les chargeurs correspondent à un point d’extension pour le chargement des différents types de modules. IoT Edge fournit des chargeurs compatibles avec des modules écrits nativement en C, en Node.js, en Java et en .NET. L’exemple Hello World utilise uniquement le chargeur C natif, car tous les modules de cet exemple sont des bibliothèques dynamiques écrites en C. Reportez-vous aux exemples [Node.js](https://github.com/Azure/iot-edge/blob/master/samples/nodejs_simple_sample/), [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample) ou [.NET](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample) pour en savoir plus sur l’utilisation des modules IoT Edge écrits dans d’autres langages.
    * **nom** : le nom du chargeur utilisé pour charger le module.
    * **point d’entrée** : le chemin d'accès à la bibliothèque contenant le module. Sur Linux, cette bibliothèque est un fichier .so, sous Windows, un fichier .dll. Le point d’entrée est spécifique au type de chargeur utilisé. Le point d’entrée du chargeur Node.js est un fichier .js. Le point d’entrée du chargeur Java est un chemin de classe et un nom de classe. Le point d’entrée du chargeur .NET est un nom d’assembly et un nom de classe.

* **args** : toute information de configuration nécessaire au module.

Le code suivant correspond au code JSON utilisé pour déclarer tous les modules IoT Edge de l’exemple Hello World sur Linux. La nécessité d’attribuer un argument à un module dépend de la conception du module. Dans cet exemple, le module d’enregistreur d’événements prend un argument qui correspond au chemin d’accès au fichier de sortie, et le module hello\_world n’accepte aucun argument.

```json
"modules" :
[
    {
        "name" : "logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/logger/liblogger.so"
        }
        },
        "args" : {"filename":"log.txt"}
    },
    {
        "name" : "hello_world",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/hello_world/libhello_world.so"
        }
        },
        "args" : null
    }
]
```

Le fichier JSON contient également les liaisons entre les modules transmis au répartiteur. Une liaison possède deux propriétés :

* **source** : un nom de module de la section `modules`, ou `\*`.
* **récepteur** : un nom de module de la section `modules`.

Chaque liaison définit un itinéraire de message et une direction. Les messages du module **source** sont remis au module **sink**. Vous pouvez définir le module **source** sur `\*`, ce qui indique que le module **sink** reçoit les messages de n’importe quel module.

Le code suivant correspond au code JSON utilisé pour configurer les liens entre les modules utilisés dans l’exemple hello\_world sur Linux. Tous les messages générés par le module `hello_world` sont utilisés par le module `logger`.

```json
"links":
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="helloworld-module-message-publishing"></a>Publication des messages du module hello\_world

Vous trouverez le code utilisé par le module hello\_world pour publier des messages dans le fichier ['hello_world.c'][lnk-helloworld-c]. L’extrait de code ci-dessous représente une version modifiée du code. Les commentaires ajoutés et une partie du code de gestion d’erreur utilisé ont été supprimés pour une meilleure lisibilité :

```C
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

Le module hello\_world ne traite jamais les messages que les autres modules IoT Edge publient sur le répartiteur. Pour cette raison, l’implémentation du rappel de message dans le module hello\_world est inopérante.

```C
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-processing"></a>Traitement des messages du module de l’enregistreur d’événements

Le module d’enregistreur d’événements reçoit des messages du répartiteur et les écrit dans un fichier. Il ne publie jamais les messages. Par conséquent, le code du module enregistreur n’appelle jamais la fonction **Broker_Publish**.

La fonction **Logger_Receive** du fichier [logger.c][lnk-logger-c] est le rappel que le répartiteur appelle pour remettre les messages au module enregistreur. L’extrait de code ci-dessous représente une version modifiée de ce code. Les commentaires ajoutés et une partie du code de gestion d’erreur utilisé ont été supprimés pour une meilleure lisibilité :

```C
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de cet article, vous avez exécuté une passerelle IoT Edge simple qui consigne des messages dans un fichier journal. Pour exécuter un exemple qui envoie des messages à IoT Hub, consultez :

- [IoT Edge – send device-to-cloud messages with a simulated device using Linux (IoT Edge : envoyer des messages appareil-à-cloud avec un appareil simulé à l’aide de Linux)][lnk-gateway-simulated-linux]. 
- [IoT Edge – send device-to-cloud messages with a simulated device using Linux (IoT Edge : envoyer des messages appareil-à-cloud avec un appareil simulé à l’aide de Windows)][lnk-gateway-simulated-windows].


<!-- Links -->
[lnk-main-c]: https://github.com/Azure/iot-edge/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/iot-edge/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/iot-edge/blob/master/modules/logger/src/logger.c
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-gateway-simulated-linux]: ../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md
[lnk-gateway-simulated-windows]: ../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md