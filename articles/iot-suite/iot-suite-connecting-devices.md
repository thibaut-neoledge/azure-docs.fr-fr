<properties
   pageTitle="Connexion de votre appareil à la solution de surveillance à distance Azure IoT Suite préconfigurée | Microsoft Azure"
   description="Explique comment connecter votre appareil à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’un exemple impliquant température et humidité."
   services="iot-hub"
   documentationCenter="na"
   authors="hegate"
   manager="jamesosb"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="hegate"/>


# Connexion de votre appareil à la solution de surveillance à distance Azure IoT Suite


## Présentation du scénario

Dans cet exemple, nous avons accès à trois sources de données simulées : température externe, température interne et humidité. Pour des raisons de simplicité, nous n’utilisons pas de capteurs réels (les données sont automatiquement générées dans le code client). Cependant nous vous encourageons, pour la prochaine opération, à connecter votre capteur favori et à envoyer des données réelles. Veuillez consulter les ressources associées à l’extension de votre section de solution pour plus d’informations.

## Composants requis

### Configurer votre Suite IoT

Si vous n’avez pas configuré votre solution préconfigurée de surveillance à distance, vous pouvez la configurer [ici](www.internetofyourthings.com).


### Configurer votre appareil dans la solution de surveillance à distance
```
Note: if you have already provisioned a device on your solution, you can skip this step.
```
Pour connecter votre appareil à la solution préconfigurée, vous devez obtenir du tableau de bord les informations d’identification de l’appareil. Elles seront utilisées dans votre application cliente afin que votre appareil puisse être identifié. Suivez cette opération

1.  Dans le coin inférieur gauche du tableau de bord, cliquez sur « Ajouter un périphérique ». ![][1]
2.  Dans Périphérique personnalisé, cliquez sur le bouton « Ajouter un nouveau ».

    ![][2]
3.  Choisissez votre propre ID d’appareil en saisissant un nom tel que realdevice1, puis cliquez sur ID de contrôle pour vous assurer que ce nom n’est pas encore utilisé. ![][3]

5. Copiez les informations d’identification fournies (ID d’appareil, nom d’hôte IoT Hub et clé d’appareil). Vous en aurez besoin plus tard dans votre application cliente pour connecter votre appareil à la solution. ![][4]
6. Assurez-vous que votre appareil s’affiche correctement dans la section Périphériques. L’état sera « En attente ». Ceci est normal jusqu’à ce que la connexion cloud soit établie.

    ![][5]

[1]: ./media/iot-suite-connecting-devices/suite0.png
[2]: ./media/iot-suite-connecting-devices/suite1.png
[3]: ./media/iot-suite-connecting-devices/suite2.png
[4]: ./media/iot-suite-connecting-devices/suite3.png
[5]: ./media/iot-suite-connecting-devices/suite5new.png

Maintenant, choisissez le langage que vous souhaitez utiliser pour poursuivre votre exemple. Dans ce didacticiel, nous avons créé un exemple de code pour C et node.js, mais vous pouvez également l’implémenter dans C# et Java.

## Envoi des données d’appareil à la solution de surveillance à distance à l’aide de C


### Exécution de votre appareil sous Linux

1. Configurer votre environnement : si vous n’avez jamais utilisé notre kit de développement logiciel d’appareil auparavant, découvrez comment configurer votre environnement sous Linux [ici](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux).

1. Ouvrez le fichier **c/serializer/samples/serializer/remote\_monitoring.c** dans un éditeur de texte.

2. Recherchez le code suivant dans le fichier : ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Remplacez « [Device Id] », « [Device Key] » par les données de votre appareil.

4. Utilisez les données d’appareil du nom d’hôte IoT Hub pour remplir le nom IoTHub et le suffixe IoTHub. Pour ce faire, vous devez fractionner IoTHub et IoTHubSuffix. Par exemple, si votre nom d’hôte IoT Hub est « Contoso.azure-devices.net », « Contoso » sera votre nom IoTHub et le reste, le suffixe. Il doit se présenter comme suit :

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

5. Enregistrez vos modifications et générez les exemples. Pour générer votre exemple, vous pouvez exécuter le script build.sh dans le répertoire **build\_all/c/linux**.

6. Exécutez l’exemple d’application **sérialiseur/c/samples/linux/remote\_monitoring/remote\_monitoring**.

Visualisez l’appareil inscrit et les données

7. Revenez au tableau de bord de la solution de surveillance à distance. Vous pouvez constater que l’état a été changé en « En cours d’exécution » dans la liste de périphériques. ![][18]

8. Cliquez sur le tableau de bord pour voir les données arriver. L’exemple est configuré pour envoyer 50 unités correspondant à la température interne, 55 unités correspondant à la température externe et 50 à l’humidité. Veuillez noter que le tableau de bord affiche uniquement les données de température et d’humidité par défaut.

8. Accédez ensuite à la [section](#command) Commande et contrôle pour apprendre à modifier la température sur votre appareil à partir de la solution de surveillance à distance.


### Exécution de votre appareil sous Windows


1. Configurer votre environnement : si vous n’avez jamais utilisé notre kit de développement logiciel d’appareil auparavant, découvrez comment configurer votre environnement sous Windows [ici](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows).

1. Démarrez une nouvelle instance de Visual Studio 2015. Ouvrez la solution **remote\_monitoring.sln** dans le dossier **c\\serializer\\build\\windows** de votre copie locale du référentiel.

2. Dans Visual Studio, dans **Explorateur de solutions**, accédez au dossier d’exemples. Dans le projet **remote\_monitoring**, ouvrez le fichier **remote\_monitoring.c**.

2. Recherchez le code suivant dans le fichier : ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Remplacez « [Device Id] », « [Device Key] » par les données de votre appareil.

4. Utilisez les données d’appareil du nom d’hôte IoT Hub pour remplir le nom IoTHub et le suffixe IoTHub. Pour ce faire, vous devez les fractionner comme suit :

    Si votre nom d’hôte IoT Hub est Contoso.azure-devices.net, Contoso correspondra au nom IoTHub et tous les éléments suivants constitueront le suffixe. Il doit se présenter comme suit :

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet **remote\_monitoring**, cliquez sur **Debug**, puis cliquez sur **Démarrer une nouvelle instance** pour générer et exécuter l’exemple. La console affiche les messages au fur et à mesure que l’application envoie des messages appareil à cloud à IoT Hub.

Visualisez l’appareil inscrit et les données

7. Revenez au tableau de bord de la solution de surveillance à distance. Vous pouvez constater que l’état a été changé en « En cours d’exécution » dans la liste de périphériques. ![][18]

8. Cliquez sur le tableau de bord pour voir les données arriver. L’exemple est configuré pour envoyer 50 unités correspondant à la température interne, 55 unités correspondant à la température externe et 50 à l’humidité. Veuillez noter que le tableau de bord affiche uniquement les données de température et d’humidité par défaut.

8. Accédez ensuite à la [section](#command) Commande et contrôle pour apprendre à modifier la température sur votre appareil à partir de la solution de surveillance à distance.


8. Accédez ensuite à la section Commande et contrôle pour apprendre à modifier la température sur votre appareil à partir de la solution de surveillance à distance.

### Exécution de votre appareil sur mbed

Les instructions qui suivent décrivent les étapes permettant de connecter un appareil [mbed-enabled Freescale FRDM-K64F](https://developer.mbed.org/platforms/FRDM-K64F/) à Azure IoT Hub.


Configuration requise

- Matériel requis : [mbed-enabled Freescale K64F](https://developer.mbed.org/platforms/FRDM-K64F/) ou équivalent.

Connectez l’appareil

- Connectez la carte à votre réseau avec un câble Ethernet. Cette opération est obligatoire, car l’exemple dépend de l’accès Internet.

- Connectez l’appareil à votre ordinateur à l’aide d’un câble micro USB. Veillez à joindre le câble au port USB de l’appareil adéquat, comme illustré [ici](https://developer.mbed.org/platforms/IBMEthernetKit/), dans la section « Prise en main ».

- Suivez les [instructions du manuel mbed](https://developer.mbed.org/handbook/SerialPC) pour configurer la connexion série vers votre appareil à partir de votre ordinateur de développement. Si vous êtes sous Windows, installez les pilotes de port série Windows situés [ici](http://developer.mbed.org/handbook/Windows-serial-configuration#1-download-the-mbed-windows-serial-port).

Créez le projet mbed et importez l’exemple de code

- Dans votre navigateur web, accédez [au site de développement](https://developer.mbed.org/) mbed.org. Si vous n’êtes pas inscrit, une option servant à créer un nouveau compte vous sera présentée (c’est gratuit). Autrement, connectez-vous avec les informations d’identification de votre compte. Cliquez sur **Compilateur** dans le coin supérieur droit de la page. Cela devrait vous permettre d’accéder à l’interface de gestion de l’espace de travail.

- Assurez-vous que la plate-forme matérielle que vous utilisez figure dans le coin supérieur droit de la fenêtre, ou cliquez sur l’icône du coin droit pour sélectionner votre plateforme matérielle.

- Cliquez sur **Importer** dans le menu principal. Cliquez ensuite sur **Cliquez ici** pour procéder à l’importation lien URL en regard du logo de globe mbed.

	![][6]

- Dans la fenêtre contextuelle, saisissez le lien vers l’exemple de code https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/

	![][7]

- Vous pouvez voir dans le compilateur mbed que l’importation de ce projet a entraîné l’importation de différentes bibliothèques. Certaines sont fournies et gérées par l’équipe Azure IoT ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [iothub\_http\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_http_transport/), [proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/)), tandis que d’autres sont des bibliothèques tierces disponibles dans le catalogue de bibliothèques mbed.

	![][8]

- Ouvrez remote\_monitoring\\remote\_monitoring.c, recherchez le code suivant dans le fichier : ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Remplacez [Device Id], [Device Key] par les données de votre périphérique.

4. Utilisez les données d’appareil du nom d’hôte IoT Hub pour remplir le nom IoTHub et le suffixe IoTHub. Pour ce faire, vous devez les fractionner comme suit :

    Si votre nom d’hôte IoT Hub est Contoso.azure-devices.net, Contoso correspondra au nom IoTHub et tous les éléments suivants constitueront le suffixe. Il doit se présenter comme suit :

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```
    ![][9]

Créez et exécutez le projet.

- Cliquez sur **Compiler** pour générer le programme. Vous pouvez sans risque ignorer les avertissements, mais si le traitement génère des erreurs, corrigez-les avant de continuer.

- Si la génération est réussie, un fichier .bin portant le nom de votre projet est généré. Copier le fichier .bin sur le périphérique. L’enregistrement du fichier .bin sur l’appareil provoque la réinitialisation de la session de terminal actuelle. Au moment de la reconnexion, réinitialisez de nouveau le terminal manuellement ou démarrez un nouveau terminal. Le périphérique mbed peut ainsi se réinitialiser et commencer l’exécution du programme.

- Connectez-vous à l’appareil en utilisant une application cliente SSH, tel que PuTTY. Vous pouvez déterminer le port série que votre appareil va utiliser en consultant le Gestionnaire de périphériques Windows :

	![][10]

- Dans PuTTY, cliquez sur le type de connexion **série**. L’appareil se connecte au 115200. Saisissez cette valeur dans la zone **Vitesse**. Cliquez ensuite sur **Ouvrir** :

	![][11]

Le programme démarre l’exécution. Il se peut que vous deviez réinitialiser le tableau (appuyez sur CTRL + Retour ou appuyez sur le bouton de réinitialisation du tableau) si le programme ne démarre pas automatiquement à la connexion.

Visualisez l’appareil inscrit et les données 7. Revenez au tableau de bord de la solution de surveillance à distance. Vous pouvez constater que l’état a été changé en « En cours d’exécution » dans la liste de périphériques. ![][18]

8. Cliquez sur le tableau de bord pour voir les données arriver. L’exemple est configuré pour envoyer 50 unités correspondant à la température interne, 55 unités correspondant à la température externe et 50 à l’humidité. Veuillez noter que le tableau de bord affiche uniquement les données de température et d’humidité par défaut.

8. Accédez ensuite à la [section](#command) Commande et contrôle pour apprendre à modifier la température sur votre appareil à partir de la solution de surveillance à distance.



[6]: ./media/iot-suite-connecting-devices/mbed1.png
[7]: ./media/iot-suite-connecting-devices/mbed2a.png
[8]: ./media/iot-suite-connecting-devices/mbed3a.png
[9]: ./media/iot-suite-connecting-devices/suite6.png
[10]: ./media/iot-suite-connecting-devices/mbed5a.png
[11]: ./media/iot-suite-connecting-devices/mbed6.png
[12]: ./media/iot-suite-connecting-devices/mbed7.png

Pour savoir comment exécuter la commande et le contrôle, accédez à la section qui suit dans ce didacticiel.

## Envoi de données d’appareil à la solution de surveillance à distance à l’aide de node.js



-   Dans notre référentiel azure-iot-sdks, localisez les fichiers suivants : packages.json (sous le /node/common) et remote\_monitoring.js sous node/device/samples /). Copiez-les sur votre appareil, et placez-les dans le même dossier.

- Ouvrez le fichier remote-monitoring.js et recherchez les variables suivantes :


   ```
   static const char* deviceId = "[Device Id]";
   static const char* deviceKey = "[Device Key]";
   static const char* hubName = "[IoTHub Name]";
   static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
   ```

-  Remplacez « [Device Id] », « [Device Key] » par les données de votre appareil.

-  Utilisez les données d’appareil du nom d’hôte IoT Hub pour remplir le nom IoTHub et le suffixe IoTHub. Pour ce faire, vous devez les fractionner comme suit :

   Si votre nom d’hôte IoT Hub est Contoso.azure-devices.net, Contoso correspondra au nom IoTHub et tous les éléments suivants constitueront le suffixe. Il doit se présenter comme suit :


   ```
     static const char* deviceId = "mydevice";
   static const char* deviceKey = "mykey";
   static const char* hubName = "Contoso";
   static const char* hubSuffix = "azure-devices.net";
   ```


- Enregistrez le fichier. Exécutez la commande suivante sur le dossier de destination :

```
npm install
node .
```

3.  Remplacez chacune des variables par les informations réunies lors de l’étape précédente. Enregistrez les modifications.


4. Ouvrez un interpréteur de commande (Linux) ou l’invite de commande Node.js (Windows) et accédez au dossier **node\\samples**. Exécutez ensuite l’exemple d’application à l’aide de la commande suivante :

    ```
    node simple_sample_remotemonitoring.js
    ```

Visualisez votre périphérique et les données entrantes

6. Sur le portail de solution préconfigurée, cliquez sur la section Périphériques pour vous assurer que l’état de votre appareil est passé sur « Exécution » et que vous pouvez voir toutes les données du fabricant.

7. Cliquez sur le tableau de bord et sélectionnez votre appareil dans « Affichage de périphérique ». Vous devriez maintenant voir les données de télémétrie surveillées dans la solution de surveillance à distance.


## <a name="command"></a>Commander et contrôler votre appareil depuis le tableau de bord

Maintenant que votre appareil est connecté et envoie des données de température générées automatiquement, vous pouvez commander et contrôler votre appareil à distance depuis IoT Hub. Vous pouvez mettre en œuvre plusieurs types de commandes qui correspondent à votre application métier. Dans ce cas, nous avons modifié la température, comme nous si nous devions en assurer le contrôle depuis la solution. Pour envoyer la commande, vous devez :

-  Cliquez sur votre ID de périphérique dans la liste Périphériques (vous trouverez la section de périphérique dans le menu de gauche).

	![][13]

- Dans le menu de droite où sont affichés les détails du périphérique, cliquez sur « Envoyer la commande »


- Sélectionnez la commande que vous souhaitez exécuter : dans ce cas, nous avons choisi « Température définie », car nous voulons modifier la température définie pour l’appareil. Sélectionnez cette commande et choisissez la valeur de température. Cliquez sur Envoyer la commande et la nouvelle température est transmise à l’appareil. Remarque : vous verrez que le résultat de la commande est « En attente » dans l’historique des commandes. C’est pourquoi, pour plus de simplicité, les exemples n’ont pas mis en œuvre la logique dans l’appareil pour répondre à IoT Hub. Vous pouvez faire cela en étendant la solution.

	![][14]
- Revenez au tableau de bord et assurez-vous que les données mises à jour vous parviennent. Vous devez voir les statistiques mises à jour sur la température et les nouvelles données affichées dans l’historique de télémétrie. ![][15]



[13]: ./media/iot-suite-connecting-devices/suite4.png
[14]: ./media/iot-suite-connecting-devices/suite7-1.png
[15]: ./media/iot-suite-connecting-devices/suite8a.png
[16]: ./media/iot-suite-connecting-devices/mbed4a.png
[17]: ./media/iot-suite-connecting-devices/suite9.png
[18]: ./media/iot-suite-connecting-devices/suite10.png


## Extension de votre solution

Il existe plusieurs façons d’étendre la fonctionnalité de cet exemple : connecter un capteur réel à votre appareil pour envoyer une vraie date d’envoi, mettre en œuvre des fonctionnalités de contrôle de commande, etc. Utilisez notre guide pour savoir comment étendre la solution de surveillance à distance pour en savoir plus à ce sujet.

<!---HONumber=Oct15_HO1-->