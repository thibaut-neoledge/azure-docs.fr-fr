## <a name="typical-output"></a>Exemple de résultat
Voici un exemple de résultat inscrit dans le fichier journal par l’exemple Hello World. Des sauts de ligne et des tabulations ont été ajoutés pour une meilleure lisibilité :

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Extraits de code
Cette section présente certains éléments clés du code dans l'exemple Hello World.

### <a name="gateway-creation"></a>Création de la passerelle
Le développeur doit écrire le *processus de passerelle*. Ce programme crée l’infrastructure interne (le répartiteur), charge les modules et configure tous les éléments pour qu’ils fonctionnent correctement. Le SDK fournit la fonction **Gateway_Create_From_JSON** pour vous permettre d'amorcer une passerelle à partir d'un fichier JSON. Pour utiliser la fonction **Gateway_Create_From_JSON**, vous devez lui transmettre le chemin d'accès à un fichier JSON qui spécifie les modules à charger. 

Vous trouverez le code du processus de passerelle dans l’exemple Hello World du fichier [main.c][lnk-main-c]. Pour une meilleure lisibilité, l'extrait de code ci-dessous montre une version abrégée du code du processus de passerelle. Ce programme crée une passerelle puis attend que l'utilisateur appuie sur la touche **ENTRÉE** avant de mettre fin à la passerelle. 

```
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

Le fichier de paramètres JSON contient une liste des modules à charger et des liens entre les modules.
Chaque module doit spécifier les éléments suivants :

* **nom** : un nom unique pour le module.
* **chargeur** : un chargeur qui sait comment charger le module souhaité.  Les chargeurs correspondent à un point d’extension pour le chargement des différents types de modules. Nous fournissons des chargeurs compatibles avec des modules écrits nativement en C, en Node.js, en Java et en .NET. L’exemple Hello World utilise uniquement le chargeur « natif », étant donné que tous les modules de cet exemple sont des bibliothèques dynamiques écrites en C. Reportez-vous aux exemples [Node.js](https://github.com/Azure/azure-iot-gateway-sdk/blob/develop/samples/nodejs_simple_sample/), [Java](https://github.com/Azure/azure-iot-gateway-sdk/tree/develop/samples/java_sample) ou [.NET](https://github.com/Azure/azure-iot-gateway-sdk/tree/develop/samples/dotnet_binding_sample) pour plus d’informations sur l’utilisation des modules écrits dans d’autres langages.
    * **nom** : nom du chargeur utilisé pour charger le module.  
    * **point d’entrée** : le chemin d'accès à la bibliothèque contenant le module. Il s’agit d’un fichier .so sous Linux, et d’un fichier .dll sous Windows. Notez que ce point d’entrée est spécifique au type de chargeur utilisé. Par exemple, le point d’entrée d’un chargeur Node.js est un fichier .js, le point d’entrée d’un chargeur Java est un chemin d’accès des classes + nom de classe et le point d’entrée d’un chargeur .NET est un nom d’assembly + nom de classe.

* **args**: toute information de configuration nécessaire au module.

Le code suivant correspond au JSON utilisé pour déclarer tous les modules de l’exemple Hello World sur Linux. La nécessité d’attribuer un argument à un module dépend de la conception du module. Dans cet exemple, le module enregistreur prend un argument qui correspond au chemin d'accès au fichier de sortie, et le module Hello World n'accepte aucun argument.

```
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

Le fichier JSON contient également les liaisons entre les modules qui seront transmis au répartiteur. Une liaison possède deux propriétés :

* **source** : un nom de module de la section `modules`, ou « \* ».
* **récepteur** : un nom de module de la section `modules`.

Chaque liaison définit un itinéraire de message et une direction. Les messages du module `source` doivent être remis au module `sink`. `source` peut avoir la valeur « \* », qui signifie que `sink` recevra des messages à partir de n’importe quel module.

Le code suivant correspond au JSON utilisé pour configurer les liens entre les modules utilisés dans l’exemple Hello World sur Linux. Tous les messages générés par le module `hello_world` seront utilisés par le module `logger`.

```
"links": 
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="hello-world-module-message-publishing"></a>Publication des messages du module Hello World
Vous trouverez le code utilisé par le module « hello world » pour publier des messages dans le fichier ['hello_world.c'][lnk-helloworld-c]. L'extrait de code ci-dessous montre une version modifiée avec des commentaires supplémentaires et un code de gestion d’erreur supprimé pour une meilleure lisibilité :

```
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

### <a name="hello-world-module-message-processing"></a>Traitement des messages du module Hello World
Le module Hello World n’a jamais besoin de traiter les messages que les autres modules publient sur le répartiteur. Cela rend inopérante l’implémentation du rappel de message dans le module Hello World.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Publication et traitement des messages du module enregistreur
Le module enregistreur reçoit des messages du répartiteur et les inscrit dans un fichier. Il ne publie jamais les messages. Par conséquent, le code du module enregistreur n’appelle jamais la fonction **Broker_Publish**.

La fonction **Logger_Recieve** du fichier [logger.c][lnk-logger-c] est le rappel que le répartiteur appelle pour remettre les messages au module enregistreur. L'extrait de code ci-dessous montre une version modifiée avec des commentaires supplémentaires et un code de gestion d’erreur supprimé pour une meilleure lisibilité :

```
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
Pour savoir comment utiliser le Kit de développement logiciel (SDK) de la passerelle IoT, consultez les rubriques suivantes :

* [Kit de développement logiciel (SDK) de la passerelle IoT– envoyer des messages appareil-à-cloud avec un appareil simulé à l’aide de Linux][lnk-gateway-simulated].
* [Kit de développement logiciel (SDK) de la passerelle Azure IoT][lnk-gateway-sdk] sur GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md

<!--HONumber=Feb17_HO2-->


