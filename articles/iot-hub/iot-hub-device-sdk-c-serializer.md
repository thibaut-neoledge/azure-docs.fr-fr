<properties
	pageTitle="Kit de développement logiciel (SDK) IoT Azure pour C - Serializer | Microsoft Azure"
	description="En savoir plus sur la bibliothèque Serializer dans le Kit de développement logiciel (SDK) d’appareil Azure IoT pour C ."
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
     ms.workload="nana"
     ms.date="09/29/2015"
     ms.author="michelb"/>

# Kit de développement logiciel (SDK) d’appareils Microsoft Azure IoT pour C - En savoir plus sur Serializer

Le [premier article](iot-hub-device-sdk-c-intro.md) de cette série a présenté le **Kit SDK d’appareils Azure IoT pour C**. Il a été suivi par un article décrivant plus précisément la bibliothèque [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). Dans cet article, nous terminerons sur le sujet du SDK avec une description plus détaillée du composant restant : la bibliothèque **serializer**.

Dans l’article d’introduction nous avons décrit comment utiliser la bibliothèque **serializer** pour envoyer des événements et recevoir des messages vers et depuis IoT Hub. Dans cet article, nous allons approfondir en fournissant une explication plus complète de la façon de modéliser vos données avec le langage de macro **serializer**. Nous allons inclure plus de détails sur la façon dont la bibliothèque sérialise les messages (et dans certains cas comment vous pouvez contrôler le comportement de sérialisation). Nous décrirons également certains paramètres que vous pouvez modifier déterminant la taille des modèles que vous créez.

Nous allons conclure en revisitant certains des sujets abordés dans les articles précédents, notamment la gestion des messages et des propriétés. Mais comme vous allez le voir, ces fonctionnalités fonctionnent de la même manière que vous utilisiez la bibliothèque **serializer** ou la bibliothèque **IoTHubClient**.

Toutes les procédures décrites ci-dessous sont basées sur des exemples de SDK du **serializer**. Si vous souhaitez approfondir, consultez les applications **simplesample\_amqp** et **simplesample\_http** incluses dans le Kit SDK d’appareils Azure IoT pour C.

## Le langage de modélisation

L’[article d’introduction](iot-hub-device-sdk-c-intro.md) de cette série a présenté le langage de modélisation du **Kit SDK d’appareils Azure IoT pour C** via l’exemple fourni dans l’application **simplesample\_amqp** :

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

Comme vous pouvez le voir, le langage de modélisation est basé sur les macros C. La définition commence toujours par **BEGIN\_NAMESPACE** et se termine toujours par **END\_NAMESPACE**. Il est courant d’affecter à l’espace de noms le nom de votre entreprise ou le nom du projet sur lequel vous travaillez, comme c’est le cas dans cet exemple.

À l’intérieur de l’espace de noms figurent les définitions de modèles. Dans ce cas, il s’agit d’un modèle unique pour un anémomètre. Là encore, n’importe quel nom peut être affecté au modèle, mais en règle générale, on lui affecte le nom de l’appareil ou le type de données que vous souhaitez échanger avec IoT Hub.

Les modèles contiennent une définition des événements que vous pouvez entrer dans IoT Hub (les *données*), ainsi que les messages que vous pouvez recevoir depuis IoT Hub (les *actions*). Comme vous pouvez le constater dans cet exemple, les événements ont un type et un nom ; les actions ont un nom et des paramètres facultatifs (chacun avec un type).

Cet exemple n’illustre pas les types de données supplémentaires pris en charge par le Kit de développement logiciel (SDK). Nous allons aborder ce point par la suite.

> Remarque : IoT Hub désigne les données envoyées par un appareil sous le terme *événements*, tandis que le langage de modélisation les désigne sous le terme *données* (définies à l’aide de l’instruction **WITH\_DATA**). De la même façon, IoT Hub désigne les données envoyées par un appareil sous le terme *messages*, tandis que le langage de modélisation les désigne sous le terme *actions* (définies à l’aide de l’instruction **WITH\_ACTION**). Sachez simplement que ces termes peuvent être utilisés de manière interchangeable dans cet article.

### Types de données pris en charge

Les types de données suivants sont pris en charge dans les modèles créés avec la bibliothèque **serializer** :

| Type | Description |
|-------------------------|----------------------------------------|
| double | nombre à virgule flottante double précision |
| int | entier 32 bits |
| float | nombre à virgule flottante simple précision |
| long | entier long |
| int8\_t | entier 8 bits |
| int16\_t | entier 16 bits |
| int32\_t | entier 32 bits |
| int64\_t | entier 64 bits |
| valeur booléenne | booléenne |
| ascii\_char\_ptr | Chaîne ASCII |
| EDM\_DATE\_TIME\_OFFSET | décalage de date et d’heure |
| EDM\_GUID | GUID |
| EDM\_BINARY | binaire |
| DECLARE\_STRUCT | type de données complexe |

Commençons par ce dernier type de données. L’argument **DECLARE\_STRUCT** permet de définir des types de données complexes, qui sont simplement des regroupements des autres types primitifs. Cela nous permet de définir un modèle qui ressemble à ceci :

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Notre modèle contient un événement de données unique de type **TestType**. Et **TestType** est un type complexe qui inclut plusieurs membres montrant collectivement les types primitifs pris en charge par le langage de modélisation de la bibliothèque **serializer**.

Pour envoyer des données à IoT Hub avec un modèle comme celui ci-dessus, nous pouvons écrire du code ressemblant à ceci :

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

En fait, nous attribuons une valeur à chaque membre de la structure **Test**, puis nous appelons **SendAsync** pour envoyer l’événement de données **Test** dans le cloud. **SendAsync** est une fonction d’assistance qui envoie un événement unique de données à IoT Hub :

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
	unsigned char* destination;
	size_t destinationSize;
	if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
	{
		// null terminate the string
		char* destinationAsString = (char*)malloc(destinationSize + 1);
		if (destinationAsString != NULL)
		{
			memcpy(destinationAsString, destination, destinationSize);
			destinationAsString[destinationSize] = '\0';
			IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
			if (messageHandle != NULL)
			{
				IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

				IoTHubMessage_Destroy(messageHandle);
			}
			free(destinationAsString);
		}
		free(destination);
	}
}
```

Cette fonction sérialise l’événement de données et l’envoie à IoT Hub à l’aide de la commande **IoTHubClient\_SendEventAsync**. Il s’agit du même code que celui passé en revue dans les articles précédents (**SendAsync** encapsule simplement la logique dans une fonction pratique).

**GetDateTimeOffset** est une autre fonction d’assistance utilisée dans l’extrait de code ci-dessus. Cette fonction transforme l’heure donnée en une valeur de type **EDM\_DATE\_TIME\_OFFSET** :

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
	struct tm newTime;
	gmtime_s(&newTime, &time);
	EDM_DATE_TIME_OFFSET dateTimeOffset;
	dateTimeOffset.dateTime = newTime;
	dateTimeOffset.fractionalSecond = 0;
	dateTimeOffset.hasFractionalSecond = 0;
	dateTimeOffset.hasTimeZone = 0;
	dateTimeOffset.timeZoneHour = 0;
	dateTimeOffset.timeZoneMinute = 0;
	return dateTimeOffset;
}
```

Si nous exécutons le code ci-dessus, le message suivant est envoyé à IoT Hub :

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Remarque : la sérialisation se fait en JSON, le format généré par la bibliothèque **serializer**. Notez également que chaque membre de l’objet JSON sérialisé correspond aux membres de la structure **TestType** définie dans notre modèle. Les valeurs correspondent également exactement à celles que nous avons utilisées dans le code. Toutefois, notez que les données binaires sont codées en base64 : AQID est le codage en base64 de {0 x 01, 0 x 02, 0 x 03}.

Cet exemple montre l’avantage de l’utilisation de la bibliothèque **serializer** : elle permet d’envoyer du code JSON dans le cloud, sans avoir à gérer explicitement la sérialisation dans notre application. Il vous suffit de définir les valeurs des événements de données dans notre modèle, puis d’appeler des API simples pour envoyer ces événements dans le cloud.

Avec les informations ci-dessus, nous pouvons définir des modèles qui incluent la plage des types de données pris en charge, y compris des types complexes (et le cas échéant, nous pourrions même inclure des types complexes au sein d’autres types complexes). Mais le code JSON sérialisé généré par l’exemple ci-dessus soulève un point important. La *façon* dont nous envoyons des données avec la bibliothèque **serializer** détermine exactement comment le code JSON est formé. C’est ce point particulier que nous allons ensuite aborder.

## En savoir plus sur la sérialisation

La section précédente présente un exemple de la sortie générée par la bibliothèque **serializer**. Dans cette section, nous allons nous concentrer sur ce sujet, en expliquant comment la bibliothèque sérialise les données et comment vous pouvez contrôler ce comportement à l’aide des API de sérialisation.

Pour faire progresser la discussion sur la sérialisation, nous allons utiliser un nouveau modèle basé sur un thermostat. Mais tout d’abord, voici un peu de contexte au scénario que nous tentons d’appliquer...

Nous souhaitons modéliser un thermostat qui mesure la température et l’humidité. Mais chaque élément de données va être envoyé à IoT Hub différemment. Par défaut, le thermostat entre un événement de température toutes les 2 minutes ; un événement d’humidité est entré toutes les 15 minutes. Lorsque l’un de ces événements est entré, il doit inclure un horodatage, c’est-à-dire l’heure à laquelle la température ou l’humidité correspondante a été mesurée.

Sur la base de ce scénario, nous allons montrer deux façons différentes de modéliser les données et nous allons expliquer l’effet de la modélisation sur la sortie sérialisée.

### Modèle n° 1

Voici la première version d’un modèle prenant en charge le scénario ci-dessus :

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Notez que le modèle inclut deux événements de données : **Temperature** et **Humidity**. Mais contrairement aux exemples précédents, le type de chaque événement est une structure définie à l’aide de l’instruction **DECLARE\_STRUCT**. **TemperatureEvent** comprend une mesure de température et un horodatage ; **HumidityEvent** contient une mesure d’humidité et un horodatage. Ce modèle propose une façon naturelle de modéliser les données du scénario décrit ci-dessus. Lorsque nous envoyons un événement dans le cloud, nous allons envoyer soit une paire température/horodatage, soit une paire humidité/horodatage.

Nous pouvons envoyer un événement de température dans le cloud à l’aide d’un code similaire à celui-ci :

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Nous allons utiliser des valeurs codées en dur pour la température et l’humidité dans l’exemple de code, mais imaginez que nous récupérions en fait ces valeurs par échantillonnage des capteurs correspondants sur le thermostat.

Le code ci-dessus utilise l’outil d’assistance **GetDateTimeOffset** présenté précédemment. Et, pour des raisons qui s’éclairciront plus loin, ce code sépare la tâche de sérialisation et d’envoi de l’événement. Le code ci-dessus sérialise l’événement de température dans une mémoire tampon. La fonction d’assistance **sendMessage** (incluse dans **simplesample\_amqp**) envoie alors l’événement à IoT Hub :

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Ce code est un sous-ensemble de l’outil d’assistance **SendAsync** décrit dans la section précédente. Nous n’allons donc pas revenir dessus.

Lorsque nous exécutons le code précédent pour envoyer l’événement de température, ce format sérialisé de l’événement est envoyé à IoT Hub :

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Nous allons envoyer une température de type **TemperatureEvent** et cette structure contient un membre **Temperature** et **Time**. Ceci est reflété directement dans les données sérialisées. Aucune surprise.

De même, nous pouvons envoyer un événement d’humidité avec ce code :

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Et le format sérialisé envoyé à IoT Hub ressemble à ceci :

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Comme attendu, là encore.

Avec le modèle ci-dessus, vous pouvez imaginer comment d’autres événements pourraient aisément être ajoutés : il suffit de définir plus de structures en utilisant **DECLARE\_STRUCT**, et inclure l’événement correspondant dans le modèle à l’aide de **WITH\_DATA**.

Mais à présent, nous allons modifier le modèle afin qu’il inclue les mêmes données, mais avec une structure différente.

### Modèle n° 2

Voici un modèle alternatif à celui proposé ci-dessus :

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Dans ce cas, nous avons éliminé les macros **DECLARE\_STRUCT** et nous définissons simplement les éléments de données à partir de notre scénario à l’aide de types simples du langage de modélisation.

Pour le moment, nous allons ignorer l’événement **Time**. Ceci mis à part, voici le code pour entrer l’événement **Temperature** :

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ce code envoie l’événement suivant sérialisé à IoT Hub :

```
{"Temperature":75}
```

Et le code pour l’envoi de l’événement Humidity ressemble à ceci :

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ce code envoie ceci à IoT Hub :

```
{"Humidity":45}
```

Jusqu’à présent, toujours pas de surprise. Mais nous allons modifier notre façon d’utiliser la macro SERIALIZE...

La macro **SERIALIZE** peut utiliser plusieurs événements de données comme arguments. Cela nous permet de sérialiser l’événement **Temperature** et **Humidity** ensemble et de les envoyer à IoT Hub via un appel unique :

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

L’on peut supposer que le résultat de ce code est l’envoi de deux événements de données à IoT Hub :

[

{"Temperature":75},

{"Humidity":45}

]

En d’autres termes, vous pourriez vous attendre à ce que ce code soit identique à l’envoi des événements **Temperature** et **Humidity** séparément, à la seule différence qu’il est plus commode de transmettre les deux événements dans la macro **SERIALIZE** avec un appel unique. Toutefois, ce n’est pas le cas. Au lieu de cela, le code ci-dessus envoie cet événement de données unique à IoT Hub :

{"Temperature":75, "Humidity":45}

Cela peut sembler étrange, étant donné que notre modèle définit **Temperature** et **Humidity** comme deux événements *distincts* :

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Plus précisément, nous n’avons pas modélisé ces événements où **Temperature** et **Humidity** se trouvent dans la même structure :

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Si nous avions utilisé ce modèle, il serait plus facile de comprendre l’envoi de **Temperature** et **Humidity** dans le même message sérialisé. Mais il peut être difficile de comprendre ce fonctionnement lorsque vous transmettez les deux événements de données dans la macro **SERIALIZE** à l’aide du modèle n° 2.

Ce comportement est plus facile à comprendre si vous connaissez les hypothèses envisagées par la bibliothèque **serializer**. Pour que ce soit plus clair, revenons à notre modèle :

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Pensez à ce modèle en tant que modèle orienté objets. Dans ce cas, nous allons modéliser un dispositif physique (un Thermostat) et ce dispositif comprend des attributs tels que la **température** et l’**humidité**.

Si nécessaire, nous pouvons envoyer l’état complet de notre modèle avec un code ressemblant à ceci :

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

En supposant que les valeurs de température, d’humidité et d’heure sont définies, nous verrions un événement envoyé à IoT Hub, comme ceci :

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Mais souvent, il peut arriver que vous souhaitiez envoyer uniquement *certaines* propriétés du modèle vers le cloud (cela est particulièrement vrai si votre modèle contient un grand nombre d’événements de données). Dans ce cas, il est utile d’envoyer uniquement un sous-ensemble des événements de données, comme dans notre exemple précédent :

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Cela génère exactement le même événement sérialisé que si nous avions défini un **TemperatureEvent** avec un membre **Temperature** et **Time**, comme nous l’avons fait avec le modèle n° 1. Mais dans ce cas, nous avons pu générer exactement le même événement sérialisé à l’aide d’un modèle différent (modèle n° 2) parce que nous avons appelé la macro **SERIALIZE** d’une manière différente.

L’élément principal à retenir, c’est que si vous transmettez plusieurs événements de données dans la macro **SERIALIZE,**, cela suppose que chaque événement est une propriété dans un objet JSON unique.

La meilleure approche consiste dépend de vous et de la façon dont vous voyez votre modèle. Si vous considérez que vous envoyez des « événements » dans le cloud et que chaque événement contient un ensemble défini de propriétés, alors la première approche est judicieuse. Dans ce cas, vous utiliseriez **DECLARE\_STRUCT** pour définir la structure de chaque événement et l’inclure dans votre modèle avec la macro **WITH\_DATA**. Vous envoyez ensuite chaque événement, comme nous l’avons fait dans le premier exemple ci-dessus. Dans cette approche, vous ne transmettriez qu’un événement unique de données dans la macro **SERIALIZER**.

Si vous envisagez votre modèle comme un modèle orienté objet, alors la seconde approche peut vous correspondre. Dans ce cas, les éléments définis à l’aide de **WITH\_DATA** sont les « propriétés » de votre objet. Dans ce cas, vous transmettez à **SERIALIZE** le sous-ensemble d’événements de votre choix, selon la quantité de votre « objet » à envoyer dans le cloud.

Aucune approche n’est meilleure que l’autre. Sachez que la bibliothèque **serializer** fonctionne ainsi et sélectionnez l’approche de modélisation qui correspond le mieux à vos besoins.

## Gestion des messages

Jusqu’à présent, cet article a étudié uniquement l’envoi d’événements vers IoT Hub et n’a pas abordé la réception de messages. La raison est la suivante : ce que vous devez savoir concernant la réception de messages a été largement couvert dans un [article précédent](iot-hub-device-sdk-c-intro.md). Rappel de cet article : vous traitez les messages en enregistrant une fonction de rappel de message :

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

Cette implémentation de **IoTHubMessage** prend en charge l’appel de la fonction spécifique pour chaque action de votre modèle. Par exemple, si votre modèle définit cette action :

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

Et **SetAirResistance** est appelé lorsque ce message est envoyé sur votre appareil.

Nous n’avons pas encore expliqué à quoi ressemble la version sérialisée du message. En d’autres termes, si vous souhaitez envoyer un message **SetAirResistance** sur votre appareil, comment cela se présente-t-il ?

Si vous envoyez un message sur un appareil, cela se fait via le Kit de développement logiciel (SDK) de services Azure IoT. Mais vous devez savoir quelle chaîne envoyer pour invoquer une action donnée... le format général pour l’envoi d’un message ressemble à ceci :

```
{"Name" : "", "Parameters" : "" }
```

Vous envoyez un objet JSON sérialisé avec deux propriétés : **Name** est le nom de l’action (ou message) et **Parameters** contient les paramètres de cette action.

Par exemple, pour invoquer **SetAirResistance**, vous pourriez envoyer ce message sur un appareil :

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Le nom de l’action doit correspondre exactement à une action définie dans votre modèle. De même, les noms de paramètre doivent correspondre également. Notez également que la casse doit être respectée. **Name** et **Parameters** sont toujours en majuscules. Et veillez à respecter la casse pour le nom d’action et les paramètres dans votre modèle. Dans cet exemple le nom d’action est « SetAirResistance » et non « setairresistance ».

Cette section décrit tout ce que vous devez savoir lors de l’envoi d’événements et de la réception de messages avec la bibliothèque **serializer**. Mais avant de poursuivre, intéressons-nous à certains paramètres que vous pouvez configurer pour contrôler la taille de votre modèle.

## Configuration des macros

Si vous utilisez la bibliothèque **Serializer**, il convient de connaître une partie importante du SDK, accessible ici :

> .\\c\\common\\tools\\macro\_utils\_h\_generator.

Ce dossier contient une solution Visual Studio appelée **macro\_utils\_h\_generator.sln** :

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

Le programme de cette solution génère le fichier **macro\_utils.h** figurant dans le répertoire .\\c\\common\\inc. Un fichier macro\_utils.h par défaut est inclus avec le SDK. Mais cette solution vous permet de modifier certains paramètres, puis de recréer le fichier d’en-tête en fonction de ces paramètres.

Les deux paramètres essentiels dont vous devez vous préoccuper sont **nArithmetic** et **nMacroParameters** qui sont définis dans ces deux lignes de la macro\_utils.tt :

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Les valeurs ci-dessus sont les paramètres par défaut inclus avec le SDK. Chaque paramètre a la signification suivante :

-   nMacroParameters : contrôle le nombre de paramètres que vous pouvez avoir dans une définition de macro DECLARE\_MODEL.

-   nArithmetic : contrôle le nombre *total* de membres autorisés dans un modèle

Ces paramètres sont importants parce qu’ils déterminent la taille possible de votre modèle. Par exemple, prenez cette définition de modèle :

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Comme mentionné précédemment, **DECLARE\_MODEL** est juste une macro C. Le nom du modèle et l’instruction **WITH\_DATA** (encore une autre macro) sont des paramètres de **DECLARE\_MODEL**. **nMacroParameters** définit le nombre de paramètres pouvant être inclus dans **DECLARE\_MODEL**. Ces éléments définissent effectivement le nombre possible de déclarations d’événements de données et d’actions. Par conséquent, avec la limite par défaut de 124, cela signifie que vous serez en mesure de définir un modèle avec une combinaison d’environ 60 actions et événements de données. Si vous tentez de dépasser cette limite, vous obtiendrez des erreurs du compilateur similaires à celles-ci :

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

Le paramètre **nArithmetic** concerne davantage le fonctionnement interne du langage de la macro que votre application. Mais il contrôle grosso modo le nombre *total* de membres que vous pouvez avoir dans votre modèle, y compris les macros **DECLARE\_STRUCT**. Donc si vous commencez à voir des erreurs du compilateur comme celles-ci, essayez d’augmenter **nArithmetic** :

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Si vous souhaitez modifier ces paramètres, modifiez les valeurs dans le fichier macro\_utils.tt, recompilez la solution macro\_utils\_h\_generator.sln, puis exécutez le programme compilé. Lorsque vous procédez ainsi, un nouveau fichier macro\_utils.h est généré et placé dans le répertoire .\\common\\inc.

Pour utiliser la nouvelle version de macro\_utils.h, vous devrez supprimer le package NuGet **serializer** de votre solution et le remplacer par le projet Visual Studio **serializer**. Cela permet de compiler votre code par rapport au code source de la bibliothèque serializer, ce qui inclut la mise à jour macro\_utils.h. Supposons que vous vouliez effectuer cette opération pour **simplesample\_amqp**. Commencez par supprimer le package NuGet pour la bibliothèque serializer à partir de la solution :

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Puis ajoutez ce projet à votre solution Visual Studio :

> .\\c\\serializer\\build\\windows\\serializer.vcxproj

Lorsque vous avez terminé, votre solution doit ressembler à ceci :

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Désormais, lorsque vous compilez votre solution, la version macro\_utils.h mise à jour sera incluse dans votre fichier binaire.

La principale chose à savoir, c’est qu’une trop forte augmentation de ces valeurs risque de dépasser les limites du compilateur. Sur ce point, le paramètre **nMacroParameters** est le principal paramètre concerné. La norme C99 indique qu’un minimum de 127 paramètres sont autorisés dans une définition de macro. Le compilateur Microsoft respecte exactement cette spécification (avec une limite de 127), vous ne pourrez donc pas augmenter **nMacroParameters** au-delà de la valeur par défaut. Mais d’autres compilateurs peuvent vous permettre de le faire (par exemple, le compilateur GNU prend en charge une limite plus élevée).

Jusqu’à présent, nous avons abordé pratiquement tout ce que vous devez savoir sur l’écriture de code avec la bibliothèque **serializer**. Avant de conclure, nous allons revoir certaines rubriques d’articles précédents qui vous posent peut-être question.

## API de niveau inférieur

L’exemple d’application utilisé dans cet article est **simplesample\_amqp**. Cet exemple utilise les API de niveau supérieur (non-« LL ») pour envoyer des événements et recevoir des messages. Si vous utilisez ces API, un thread d’arrière-plan s’exécute, prenant en charge l’envoi d’événements et la réception de messages. Toutefois, le cas échéant, nous pouvons utiliser les API de niveau inférieur pour éliminer ce thread d’arrière-plan et de prendre le contrôle explicite lorsque nous envoyons des événements ou recevons des messages du cloud.

Comme décrit dans un [article précédent](iot-hub-device-sdk-c-iothubclient.md), un ensemble de fonctions composent des API de niveau supérieur :

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_Destroy

Ces API sont décrites dans **simplesample\_amqp**.

Mais il existe un ensemble d’API de niveau inférieur analogue.

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

Le principal à retenir est que les API de niveau inférieur fonctionnent exactement de la même façon que décrit dans les articles précédents. Vous pouvez utiliser le premier ensemble d’API si vous souhaitez un thread d’arrière-plan pour gérer les événements d’envoi et réception de messages. Et vous utiliserez le second ensemble d’API si vous souhaitez contrôler explicitement vos envois et réceptions de données vers et depuis IoT Hub. Les deux ensembles d’API fonctionnent aussi bien l’un que l’autre avec la bibliothèque **serializer**.

Pour obtenir un exemple d’utilisation des API de niveau inférieur avec la bibliothèque **serializer**, voir l’application **simplesample\_http**.

## Rubriques supplémentaires

Voici quelques autres sujets qu’il est intéressant de mentionner à nouveau : gestion des propriétés, utilisation d’autres informations d’identification sur l’appareil et options de configuration. Toutes ces rubriques sont traitées dans un [article précédent](iot-hub-device-sdk-c-iothubclient.md). Le point essentiel à retenir, c’est que toutes ces fonctionnalités fonctionnent de la même manière avec la bibliothèque **serializer** ou avec la bibliothèque **IoTHubClient**. Par exemple, si vous souhaitez joindre des propriétés à un événement à partir de votre modèle, vous utiliserez **IoTHubMessage\_Properties** et **Map**\_**AddorUpdate** de la même manière que décrit précédemment :

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Que l’événement ait été généré à partir de la bibliothèque **serializer** ou manuellement via la bibliothèque **IoTHubClient** n’a pas d’importance.

En ce qui concerne les autres informations d’identification sur l’appareil, on peut indifféremment utiliser **IoTHubClient\_LL\_Create** ou **IoTHubClient\_CreateFromConnectionString** pour allouer un **IOTHUB\_CLIENT\_HANDLE**.

Enfin, si vous utilisez la bibliothèque **serializer**, vous pouvez définir des options de configuration avec **IoTHubClient\_LL\_SetOption** tout comme vous l’avez fait lors de l’utilisation de la bibliothèque **IoTHubClient**.

Les API d’initialisation sont des fonctionnalités secondaires uniques de la bibliothèque **serializer**. Avant de pouvoir commencer à travailler avec la bibliothèque, vous devez appeler **serializer\_init** :

```
serializer_init(NULL);
```

Cet appel doit être effectué juste avant l’appel de **IoTHubClient\_CreateFromConnectionString**.

De même, lorsque vous avez fini d’utiliser la bibliothèque, le dernier appel effectué sera normalement l’appel de **serializer\_deinit** :

```
serializer_deinit();
```

Sinon, toutes les autres fonctionnalités répertoriées ci-dessus fonctionnent de la même manière dans la bibliothèque **serializer** ou dans la bibliothèque **IoTHubClient**. Pour plus d’informations sur ces rubriques, consultez l’[article précédent](iot-hub-device-sdk-c-iothubclient.md) de cette série.

## Étapes suivantes

Cet article détaille les aspects uniques de la bibliothèque **serializer** contenue dans le **Kit SDK d’appareils Azure IoT pour C**. Ces informations devraient vous aider à bien comprendre comment utiliser des modèles pour envoyer des événements et recevoir des messages vers et depuis IoT Hub.

Ceci conclut également la série en trois parties sur le développement d’applications avec le **Kit SDK d’appareils Azure IoT pour C**. Ces informations devraient suffire pour vous aider à commencer et à bien comprendre le fonctionnement des API. Si vous cherchez des informations supplémentaires, quelques exemples dans le SDK n’ont pas été abordés ci-dessus. Sinon, la [documentation du SDK](https://github.com/Azure/azure-iot-sdks) est une ressource précieuse pour obtenir des informations complémentaires.

<!---HONumber=Oct15_HO4-->