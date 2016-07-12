<properties
	pageTitle="Gestion des appareils avec le Kit de développement logiciel (SDK) de passerelle | Microsoft Azure"
	description="Procédure pas à pas sur le Kit de développement logiciel (SDK) de passerelle IoT Hub vous expliquant comment implémenter la gestion des appareils quand vous utiliser le Kit de développement logiciel (SDK) de passerelle"
	services="iot-hub"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/15/2016"
     ms.author="dobett"/>


# Kit de développement logiciel (SDK) de passerelle IoT (version bêta) - gestion des appareils avec le Kit de développement logiciel (SDK) de passerelle

Ce didacticiel vous montre comment utiliser les fonctionnalités de [gestion des appareils][lnk-device-management] d’IoT Hub avec le [Kit de développement logiciel (SDK) de passerelle Azure IoT Hub][lnk-gateway-sdk]. Ce didacticiel utilise un module Intel Edison Compute pour héberger une passerelle avec les appareils simulés qui envoie la télémétrie à votre concentrateur IoT. Vous utilisez les fonctionnalités de gestion des appareils IoT Hub pour effectuer une mise à jour du microprogramme à distance sur la carte Edison.

Ce didacticiel contient les sections suivantes :

- **Architecture** : informations architecturales importantes concernant l’exemple de gestion des appareils.

- **Créer et exécuter** : les étapes requises pour créer et exécuter l’exemple.

## Architecture

Dans ce didacticiel, vous approvisionnez une carte Intel Edison de façon qu’elle agisse comme une passerelle d’appareil IoT connectée à IoT Hub. Vous configurez également la carte Edison de façon que vous puissiez la gérer via IoT Hub. Le diagramme suivant montre les éléments clés de la solution que vous créez dans ce didacticiel :

![Architecture de gestion des appareils et passerelle][img-architecture]

### Appareils

Il existe trois appareils IoT connectés à IoT Hub dans cette solution :

- La carte Edison est un appareil nommé **GW-device**. Dans le didacticiel, vous utilisez les fonctionnalités de gestion des appareils d’IoT Hub pour mettre à jour le microprogramme de cet appareil physique.

- Deux appareils simulés (**GW-ble1-demo** et **GW-ble2-demo**). Ces appareils simulent des appareils à faible consommation d’énergie Bluetooth qui se connectent à IoT Hub via la passerelle et envoient la télémétrie de température sur votre concentrateur.

### Logiciel de passerelle

Le logiciel de passerelle s’exécute en tant que service sur la carte Edison. Deux appareils simulés génèrent des données de télémétrie de température. Le module de mappage mappe ces appareils simulés sur les appareils enregistrés avec IoT Hub et le module HTTP gère la communication avec le point de terminaison IoT Hub. L’article [Kit de développement logiciel (SDK) de passerelle IoT (version bêta) : envoyer des messages appareil-à-cloud avec un appareil simulé][lnk-gateway-scenario] décrit en détail ce scénario.

### Client de gestion des appareils

Le [client de gestion des appareils][lnk-device-management] s’exécute en tant que service sur la carte Edison. Cela vous permet d’exécuter des travaux à distance sur la carte Edison comme les [mises à jour du microprogramme][lnk-dm-jobs], les redémarrages et les mises à jour de configuration, mais également d’interroger des propriétés d’appareil. Dans ce didacticiel, le client de gestion des appareils reçoit et traite une requête pour effectuer une mise à jour du microprogramme sur la carte Edison.

### IoT Hub

[Azure IoT Hub][lnk-iot-hub] est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur principal de solution. Dans ce didacticiel, IoT Hub :

- permet aux trois appareils de se connecter au cloud ;
- reçoit la télémétrie générée par les appareils simulés à partir de la passerelle ;
- vous permet d’envoyer une demande de mise à jour du microprogramme à la carte Edison ;
- surveille la progression du travail de mise à jour du microprogramme.

### Exemple d’interface utilisateur de gestion des appareils

[L’exemple d’interface utilisateur de gestion des appareils][lnk-dm-sample-ui] est un exemple d’application web qui vous permet d’utiliser les fonctionnalités de gestion des appareils d’IoT Hub dans une interface utilisateur web interactive. Par exemple, vous pouvez utiliser l’exemple d’interface utilisateur pour interroger les appareils connectés à votre IoT Hub et envoyer des travaux de mise à jour du microprogramme à vos appareils. Dans ce didacticiel, vous utilisez l’exemple d’interface utilisateur pour envoyer un travail de mise à jour du microprogramme à la carte Edison et surveiller la progression de ce travail jusqu’à ce qu’il soit terminé.

## Créer et exécuter

Pour exécuter cet exemple, vous devez créer une image personnalisée pour votre carte Edison qui inclut le logiciel de passerelle IoT Hub et le client de gestion des appareils.

Avant de commencer, vous devez vous assurer que vous pouvez connecter votre carte Edison à votre réseau sans fil. Pour configurer votre carte Edison, vous devez la connecter à un ordinateur hôte. Plus tard, vous utiliserez l’ordinateur hôte pour flasher votre carte Edison avec l’image personnalisée que vous créez. Intel dispose d’un ensemble de guides de démarrage comprenant des guides pour les systèmes d’exploitation suivants :

- [Get Started with the Intel Edison Development Board on Windows 64-bit][lnk-setup-win64] (Guide de démarrage de la carte Intel Edison Development sur Windows 64 bits).
- [Get Started with the Intel Edison Development Board on Windows 32-bit][lnk-setup-win32] (Guide de démarrage de la carte Intel Edison Development sur Windows 32 bits).
- [Getting Started with the Intel® Edison Board on Linux][lnk-setup-linux] (Guide de démarrage de la carte Intel® Edison sur Linux).

Pour configurer votre carte Edison et vous familiariser avec celle-ci, vous devez effectuer toutes les étapes de ces articles « de démarrage », à l’exception des étapes suivantes :

- Flasher le microprogramme le plus récent. Vous mettez à jour le microprogramme dans le cadre de ce didacticiel, vous n’avez donc pas besoin d’effectuer cette étape pour l’instant.
- La dernière étape, « Choose IDE » (Sélection d’IDE), qui ne s’applique pas à ce didacticiel.

Lorsque vous avez configuré la carte Edison et installé les pilotes nécessaires sur votre ordinateur hôte, vous devez vous assurer que vous pouvez vous connecter à la carte Edison à l’aide d’un terminal série. La page [Setting up a serial terminal][lnk-serial-connection] (Configurer un terminal série) sur le site Web Intel propose des liens vers des instructions de configuration pour les systèmes d’exploitation hôtes tels que Windows et Linux.

Vous devez également effectuer ces tâches.

- [Créer un concentrateur IoT][lnk-create-hub] dans votre abonnement Azure. Vous avez besoin du nom de votre concentrateur pour terminer ce didacticiel. Si vous ne possédez pas déjà d’abonnement Azure, vous pouvez obtenir un [compte gratuit][lnk-free-trial].
- Ajoutez trois appareils (**GW-ble1-demo**, **GW-ble2-demo** et **GW-device**) à votre concentrateur IoT et notez leur ID et clé d’appareil. Vous pouvez utiliser les outils [Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour ajouter ces appareils au concentrateur IoT que vous avez créé à l’étape précédente et récupérer ainsi leurs clés. Vous devez utiliser deux de ces appareils (**GW-ble1-demo** et **GW-ble2-demo**) en tant qu’appareils BLE simulés connectés à la passerelle et un appareil (**GW-device**) pour identifier l’appareil de passerelle Edison en tant que client de gestion des appareils que vous pouvez gérer à partir de votre IoT Hub.

### Préparer l’environnement de création et vérifier que vous pouvez créer une image personnalisée

Pour créer une image personnalisée pour votre carte Edison, vous avez besoin d’un environnement Linux. Ces étapes supposent que vous utilisez Ubuntu 14.04 qui, au moment de la rédaction de cet article, est la plate-forme que nous vous recommandons d’utiliser. Vous devez disposer d’au moins 40 Go d’espace libre sur votre partition d’origine.

> [AZURE.NOTE] L’exécution du script qui crée l’image personnalisée peut durer 6 heures sur un ordinateur quadricœur. Vous pouvez réduire ce délai en utilisant un ordinateur plus puissant qui possède plus de cœurs de processeur.

Pour les étapes de cette section, nous avons consulté les articles suivants : [Intel Edison Board Support Package][lnk-inteledison-bsp] (Package de support de carte Intel Edison), [Manually Building Yocto Images for the Intel Edison Board from Source][lnk-hackgnar] (Création manuelle d’images Yocto pour la carte Intel Edison à partir de la source) et [Creating a Custom Linux Kernel for the Edison (release 2.1)][lnk-shawnhymel] (Création d’un noyau Linux personnalisé pour la carte Edison (version 2.1).

1. Connectez-vous à votre ordinateur Ubuntu 14.04 et exécutez la commande suivante dans votre dossier d’origine pour télécharger le package source Edison :
    
    ```
    curl -O http://downloadmirror.intel.com/25028/eng/edison-src-ww25.5-15.tgz
    ```

2. Décompressez le package source pour créer un dossier **edison-src** dans votre dossier d’origine avec la commande suivante et accédez au nouveau dossier **edison-src** :
    
    ```
    tar xfvz edison-src-ww25.5-15.tgz
    cd edison-src/
    ```

3. Installez les packages requis :
    
    ```
    sudo apt-get -y install build-essential git diffstat gawk chrpath texinfo libtool gcc-multilib libsdl1.2-dev u-boot-tools
    ```

4. Créez les deux nouveaux répertoires dans le dossier **edison-src** :
    
    ```
    mkdir bitbake_download_dir
    mkdir bitbake_sstate_dir 
    ```

5. Exécutez le script suivant pour télécharger votre environnement de création. Si vous avez plus de 4 cœurs de processeur, définissez les arguments de script **--parallel\_make** et **--bb\_number\_thread** sur le nombre de cœurs disponibles :
    
    ```
    ./meta-intel-edison/setup.sh --dl_dir=bitbake_download_dir  --sstate_dir=bitbake_sstate_dir 
    ```

6. Mettez à jour l’emplacement du référentiel git Paho. Modifiez le fichier **~/edison-src/out/linux64/poky/meta-intel-iot-middleware/recipes-connectivity/paho-mqtt/paho-mqtt\_3.1.bb** et remplacez l’URL `git://git.eclipse.org/gitroot/paho/org.eclipse.paho.mqtt.c.git/` par `git://github.com/eclipse/paho.mqtt.c.git`.

7. Initialisez votre environnement de création avec les commandes suivantes :
    
    ```
    cd out/linux64/
    source poky/oe-init-build-env
    ```

8. Utilisez la commande suivante pour créer votre image personnalisée. L’exécution de cette commande pour la première fois peut durer 6 heures sur un ordinateur quadricœur. Après avoir ajouté vos propres personnalisations, les nouvelles créations seront beaucoup plus rapides :
    
    ```
    bitbake edison-image
    ```

9. Finalisez la création en exécutant les commandes suivantes :
    
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

Les fichiers nécessaires pour flasher la carte Edison sont maintenant dans le dossier **~/edison-src/out/linux64/build/toFlash/**.

### Ajouter le Kit de développement logiciel (SDK) de passerelle à votre image personnalisée

Les étapes décrites dans cette section vous guident dans le processus d’ajout du Kit de développement logiciel (SDK) de passerelle à votre image personnalisée afin que la carte Edison agisse comme une passerelle IoT lors de son démarrage. Pour ce didacticiel, la passerelle inclut un module qui simule deux appareils à faible consommation d’énergie Bluetooth (BLE) qui génèrent la télémétrie de température pour la passerelle à transférer à votre concentrateur IoT.

Effectuez les étapes suivantes sur le même ordinateur Ubuntu 14.04 que vous avez utilisé pour créer une image Edison dans la section précédente.

1. Clonez le Kit de développement logiciel (SDK) de passerelle dans votre dossier d’origine :
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-gateway-sdk.git --recursive
    ```

2. Configurez la passerelle pour vous connecter à votre IoT Hub et simuler deux appareils. Modifiez le fichier **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_intel\_edison.json** et remplacez les espaces réservés **IoTHubName**, **IoTHubSuffix**, **deviceID** et **deviceKey** par les valeurs que vous avez notées lorsque vous avez configuré votre IoT Hub. Utilisez les appareils **GW-ble1-demo** et **GW-ble2-demo** que vous avez créés précédemment.

3. Créez un dossier qui contiendra votre nouvelle recette :
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk
    ```

4. Copiez le fichier de recette appelé **azure-iot-field-gateway-sdk.bb** à partir du dossier **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/** dans le dossier **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk/** que vous venez de créer. Modifiez le fichier pour remplacer les deux occurrences de `<<userName>>` par votre nom d’utilisateur actuel.

5. Modifiez **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** pour ajouter une entrée pour votre nouvelle recette. Ajoutez la ligne suivante à la fin du fichier :
    
    ```
    IMAGE_INSTALL += "azure-iot-field-gateway-sdk"
    ```

6. Vous pouvez maintenant tester vos modifications en exécutant la commande **bitbake** pour créer uniquement la nouvelle recette :
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake azure-iot-field-gateway-sdk
    ```

### Ajouter le client de gestion des appareils Azure IoT Hub à votre image personnalisée

Les étapes décrites dans cette section vous guident dans le processus d’ajout du client de gestion des appareils IoT Hub à votre image personnalisée afin de pouvoir gérer l’appareil de passerelle Edison à partir de votre IoT Hub. Pour ce didacticiel, le client de gestion des appareils inclut un exemple de code pour permettre la mise à jour du microprogramme sur votre appareil de passerelle Edison.

Effectuez les étapes suivantes sur le même ordinateur Ubuntu 14.04 que vous avez utilisé dans la section précédente pour ajouter la passerelle à votre image Edison.

1. Clonez la branche **dmpreview** du référentiel des Kits de développement logiciel (SDK) IoT Hub dans votre dossier d’origine :
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-sdks -b dmpreview --recursive
    ```

2. Créez les dossiers suivants pour contenir votre nouvelle recette :
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files
    ```

3. Copiez le fichier **iotdm-edison-sample.bb** à partir du dossier **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** dans le dossier **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample**.

4. Copiez le fichier **iotdm\_edison\_sample.service** à partir du dossier **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** dans le dossier **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files**.

5. Modifiez le fichier **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** pour ajouter une entrée pour votre nouvelle recette. Ajoutez la ligne suivante à la fin du fichier :
    
    ```
    IMAGE_INSTALL += "iotdm-edison-sample"
    ```

6. Puisque le Kit de développement logiciel (SDK) de passerelle et le client de gestion des appareils partagent certaines bibliothèques, vous devez modifier le fichier **~/edison-src/out/linux64/poky/meta/classes/sstate.bbclass**. Ajoutez les lignes suivantes à la fin de ce fichier. Veillez à remplacer `<your user>` par votre nom d’utilisateur actuel :
    
    ```
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/lib/libaziotsharedutil.a"
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/include/azureiot"
    ```

7. Configurez le Wi-Fi pour démarrer automatiquement sur la carte Edison en modifiant le fichier **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-connectivity/wpa\_supplicant/wpa-supplicant/wpa\_supplicant.conf-sane** et en ajoutant les lignes suivantes à la fin du fichier. Veillez à remplacer `<your wifi ssid>` et `<your wifi password>` par les valeurs correctes pour votre réseau Wi-Fi :
    
    ```
    network={
      ssid="<your wifi ssid>"
      key_mgmt=WPA-PSK
      pairwise=CCMP TKIP
      group=CCMP TKIP WEP104 WEP40
      eap=TTLS PEAP TLS
      psk="<your wifi password>"
    }
    ```

8. Vous pouvez maintenant créer l’image de votre carte Edison contenant le Kit de développement logiciel (SDK) de passerelle et le client de gestion des appareils. La commande **bitbake** s’exécutera beaucoup plus rapidement qu’auparavant, car elle a seulement besoin de créer la recette et de l’ajouter à l’image :
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake edison-image
    ```

9. Finalisez la création en exécutant les commandes suivantes :
  
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

Les fichiers nécessaires pour flasher la carte Edison sont maintenant dans le dossier **~/edison-src/out/linux64/build/toFlash/**.

### Flasher votre carte Intel Edison avec l’image personnalisée

Vous pouvez maintenant flasher votre carte Edison avec votre image personnalisée qui contient le logiciel de passerelle et le client de gestion des appareils IoT Hub.

Copiez les fichiers à partir du dossier **toFlash** de l’ordinateur Ubuntu que vous avez utilisé pour créer l’image personnalisée sur l’ordinateur qui se connecte à votre carte Edison via un câble USB.

Si vous utilisez un ordinateur Windows pour vous connecter à votre carte Edison via un câble USB, vous devez exécuter le script **flashall.bat** pour flasher votre carte Edison. Si vous utilisez un ordinateur Linux pour vous connecter à votre carte Edison via un câble USB, vous devez exécuter le script **flashall.bat** pour flasher votre carte Edison.

Après avoir flashé votre carte Edison, connectez-la en utilisant une [connexion série][lnk-serial-connection] à partir de votre ordinateur hôte et connectez-vous en tant que **racine**. Vous devez vérifier que votre carte Edison est maintenant connectée à votre réseau Wi-Fi.

### Exécution de l'exemple

Vous devez configurer le client de gestion des appareils sur la carte Edison pour vous connecter en tant qu’appareil **GW-device** à votre concentrateur IoT. Utilisez un éditeur de texte (tel que **vi** ou **nano**) pour créer un fichier appelé **.cs** dans le dossier /home/root de la carte Edison. Ce fichier doit contenir uniquement la chaîne de connexion de l’appareil **GW-device**. Si vous n’avez pas noté cette chaîne de connexion précédemment, vous pouvez utiliser les outils [Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour récupérer cette chaîne de connexion d’appareil à partir du registre d’appareil IoT Hub.

Lorsque vous avez créé le fichier **.cs**, redémarrez la carte Edison à l’aide de la commande suivante :

```
reboot -h now
```

Lorsque la carte Edison redémarre, vérifiez que les services de passerelle et de gestion des appareils démarrent avec un état **OK** :

```
[  OK  ] Started Daemon to receive the wpa_supplicant event.
         Starting PulseAudio Sound System...
[  OK  ] Started WPA supplicant service.
[  OK  ] Started Azure IoT DM as a service..
[  OK  ] Started Azure Iot Gateway as a service..
         Stopping Daemon to receive the wpa_supplicant event...
[  OK  ] Stopped Daemon to receive the wpa_supplicant event.

```

Pour résoudre les problèmes de service de passerelle IoT, examinez les entrées dans le fichier **/deviceCloudUploadGatewaylog.log** sur la carte Edison. Vous pouvez également utiliser la commande suivante pour afficher les sorties du service :

```
systemctl status simulated_device_cloud_upload.service
```

Pour résoudre les problèmes de service de gestion des appareils IoT, utilisez la commande suivante pour afficher les sorties :

```
systemctl status iotdm_edison_sample.service
```

### Démarrer le travail de mise à jour du microprogramme

Une mise à jour du microprogramme sur la carte Edison demandée par le service de gestion des appareils IoT télécharge normalement un fichier zip qui contient le microprogramme à partir d’une URL. Pour simplifier ce didacticiel, vous copiez manuellement le fichier zip sur la carte Edison, puis vous utilisez une **URL file://** au lieu d’une **URL http://** lorsque vous demandez la mise à jour.

Ici encore, pour simplifier le didacticiel, la mise à jour du microprogramme réapplique la même image de microprogramme plutôt que d’utiliser une nouvelle image. Vous ne pourrez pas voir l’application de cette image à la carte Edison.

Créez un fichier appelé **edison.zip** qui contient tous les fichiers et sous-dossiers du dossier **toFlash** sur l’ordinateur Ubuntu que vous avez utilisé pour créer l’image personnalisée. Assurez-vous que les fichiers du dossier **toFlash** sont à la racine du fichier zip. Utilisez un outil tel que SCP (ou PSCP si vous utilisez Putty) pour copier le fichier **edison.zip** dans le dossier /home/root de votre carte Edison.

Pour envoyer le travail de mise à jour du microprogramme et surveiller sa progression, utilisez [l’exemple d’interface utilisateur de gestion des appareils][lnk-dm-sample-ui] Node.js. Vous pouvez exécuter cet exemple sur Windows ou Linux. Vous devez avoir [Node.js][lnk-nodejs] 6.1.0 ou une version supérieure. Pour récupérer, créer et exécuter l’exemple d’interface utilisateur de gestion des appareils sur votre ordinateur, procédez comme suit :

1. Ouvrez une **Invite de commandes**.

2. Vérifiez que vous avez installé Node.js 6.1.0 ou une version supérieure en tapant `node --version`.

3. Clonez le référentiel GitHub d’interface utilisateur de gestion des appareils Azure IoT en exécutant la commande suivante :

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. Dans le dossier racine de votre copie clonée du référentiel d’interface utilisateur de gestion des appareils Azure IoT, exécutez la commande suivante pour récupérer les packages dépendants :

    ```
    npm install
    ```

5. Une fois la commande npm install achevée, exécutez la commande suivante pour générer le code :

    ```
    npm run build
    ```

6. Utilisez un éditeur de texte pour ouvrir le fichier user-config.json dans la racine du dossier cloné. Remplacez le texte « &lt;YOUR CONNECTION STRING HERE&gt; » (VOTRE CHAÎNE DE CONNEXION ICI) par votre chaîne de connexion IoT Hub. Vous pouvez trouver cette chaîne de connexion dans le [portail Azure][lnk-azure-portal].

7. À l’invite de commandes, exécutez la commande ci-après pour démarrer l’application UX de gestion des appareils :

    ```
    npm run start
    ```

8. Lorsque l’invite de commandes signale que les services ont démarré, ouvrez un navigateur web, puis accédez à l’application de gestion des appareils en utilisant l’URL suivante pour visualiser vos appareils : <http://127.0.0.1:3003>.

    ![Interface utilisateur de gestion des appareils][img-dm-ui]

9. Sélectionnez l’appareil **GW-device**, dans la liste déroulante **Travaux d’appareil** sélectionnez **Mise à jour du microprogramme**, puis cliquez sur **Démarrer**.

10. Dans le champ **Package URI** (URI du package), entrez **file:///home/root/edison.zip** pour utiliser le fichier d’image que vous avez précédemment copié sur la carte Edison. Cliquez sur **Envoyer**, puis sur **Oui**. Ensuite, cliquez sur le **lien Historique des travaux** pour voir les nouveaux travaux parents et enfants en cours d’exécution :

    ![Lien vers l’historique des travaux][img-history-link]

11. Après quelques minutes, dans le terminal série connecté à votre carte Edison, vous verrez la carte Edison redémarrer et appliquer les modifications du microprogramme :

    ```
    reading ota_update.scr
    14747 bytes read in 17 ms (846.7 KiB/s)
    ## Executing script at 00100000
    === OTA update script ===
    Validating Ota package
    part find mmc 0 label:update u_part_num;
    ota drive is mmc 0:9

    Validating edison_ifwi-dbg-00-dfu.bin hash for boot0 and boot1 partitions

    fatload mmc 0:9 0x6400000 edison_ifwi-dbg-00-dfu.bin;
    reading edison_ifwi-dbg-00-dfu.bin
    ...
    ```

12. Après le redémarrage de la carte Edison, actualisez la page dans l’exemple d’interface utilisateur de gestion des appareils pour voir que l’état du travail est désormais **terminé** pour les deux travaux de mise à jour du microprogramme :

    ![Travail à l’état terminé][img-job-status]

Vous avez maintenant terminé le didacticiel sur l’utilisation du client de gestion des appareils et du logiciel de passerelle IoT Hub sur une carte Intel Edison. Dans le cadre de ce didacticiel :

- Vous avez créé une image de carte Intel Edison personnalisée qui inclut le logiciel de passerelle et le client de gestion des appareils IoT Hub configurés pour démarrer à chaque démarrage de la carte Edison.
- Vous avez configuré le client de gestion des appareils et la carte Edison pour vous connecter à votre concentrateur IoT.
- Vous avez démarré un travail de gestion des appareils à partir de l’exemple d’interface utilisateur qui entraîne le redémarrage de la carte Edison et l’application d’une nouvelle image de microprogramme.

## Étapes suivantes

Pour en savoir plus sur la gestion des appareils avec IoT Hub et l’exemple d’interface utilisateur, consultez l’article [Vue d’ensemble de la gestion des appareils Azure IoT Hub][lnk-device-management].

Pour savoir comment connecter des appareils physiques à votre IoT Hub, consultez l’article [Kit de développement logiciel (SDK) de passerelle IoT (version bêta) : envoyer des messages appareil vers cloud avec un appareil réel à l’aide de Linux][lnk-gateway-physical].



<!-- Links and images -->

[img-dm-ui]: media/iot-hub-gateway-sdk-device-management/image1.png
[img-history-link]: media/iot-hub-gateway-sdk-device-management/image2.png
[img-job-status]: media/iot-hub-gateway-sdk-device-management/image3.png
[img-architecture]: media/iot-hub-gateway-sdk-device-management/architecture.png

[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-device-management]: iot-hub-device-management-overview.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-serial-connection]: https://software.intel.com/setting-up-serial-terminal-intel-edison-board
[lnk-inteledison-bsp]: http://www.intel.com/content/dam/support/us/en/documents/edison/sb/edisonbsp_ug_331188007.pdf
[lnk-hackgnar]: http://www.hackgnar.com/2016/01/manually-building-yocto-images-for.html
[lnk-shawnhymel]: http://shawnhymel.com/724/creating-a-custom-linux-kernel-for-the-edison-yocto-2-1/
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-nodejs]: https://nodejs.org/
[lnk-azure-portal]: https://portal.azure.com
[lnk-dm-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-physical]: iot-hub-gateway-sdk-physical-device.md
[lnk-gateway-scenario]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md

<!---HONumber=AcomDC_0629_2016-->