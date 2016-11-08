> [!div class="op_single_selector"]
> * [C sur Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C sur Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [C sur mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
> * [Node.JS](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> 
> 

## Présentation du scénario
Dans ce scénario, vous allez créer un appareil qui envoie la télémétrie suivante à la [solution préconfigurée][lnk-what-are-preconfig-solutions] de surveillance à distance :

* Température externe
* Température interne
* Humidité

Par souci de simplicité, le code sur l’appareil génère des valeurs d’exemple, mais nous vous encourageons à étendre l'exemple en connectant des capteurs réels à votre appareil et en envoyant une télémétrie réelle.

Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk-free-trial].

## Avant de commencer
Avant d’écrire du code pour votre appareil, vous devez approvisionner votre solution préconfigurée de surveillance à distance, puis approvisionner un nouvel appareil personnalisé dans cette solution.

### Approvisionner la solution préconfigurée de surveillance à distance
L’appareil que vous créez dans ce didacticiel envoie des données à une instance de la solution préconfigurée de [surveillance à distance][lnk-remote-monitoring]. Si vous n’avez pas déjà approvisionné la solution préconfigurée de surveillance à distance dans votre compte Azure, procédez comme suit :

1. Sur la page <https://www.azureiotsuite.com/>, cliquez sur **+** pour créer une solution.
2. Cliquez sur **Sélectionner** dans le panneau **Surveillance à distance** pour créer votre solution.
3. Sur la page **Créer une solution de surveillance à distance**, entrez un **nom de solution**, sélectionnez la **région** où vous souhaitez déployer la solution, puis choisissez l’abonnement Azure à utiliser. Cliquez ensuite sur **Créer la solution**.
4. Attendez la fin du processus de configuration.

> [!WARNING]
> Les solutions préconfigurées utilisent des services Azure facturables. Veillez à supprimer la solution préconfigurée de votre abonnement lorsque vous avez terminé pour éviter toute facturation inutile. Vous pouvez supprimer complètement une solution préconfigurée de votre abonnement en vous rendant à la page <https://www.azureiotsuite.com/>.
> 
> 

Au terme du processus d’approvisionnement de la solution de surveillance à distance, cliquez sur **Lancer** pour ouvrir le tableau de bord de la solution dans votre navigateur.

![][img-dashboard]

### Configurer votre appareil dans la solution de surveillance à distance
> [!NOTE]
> Si vous avez déjà approvisionné un appareil dans votre solution, vous pouvez ignorer cette étape. Vous devez connaître les informations d'identification de l’appareil lorsque vous créez l'application cliente.
> 
> 

Pour qu’un appareil puisse se connecter à la solution préconfigurée, il doit s’identifier auprès d’IoT Hub à l’aide d’informations d’identification valides. Vous pouvez récupérer les informations d’identification de l’appareil à partir du tableau de bord de la solution. Les informations d’identification de l’appareil seront ajoutées dans votre application cliente dans la suite de ce didacticiel.

Pour ajouter un nouvel appareil à votre solution de surveillance à distance, procédez comme suit dans le tableau de bord de la solution :

1. Dans le coin inférieur gauche du tableau de bord, cliquez sur **Ajouter un périphérique**.
   
   ![][1]
2. Dans le panneau **Périphérique personnalisé**, cliquez sur **Ajouter nouveau**.
   
   ![][2]
3. Choisissez **Me laisser définir mon propre ID d’appareil**, saisissez un ID d’appareil comme **monappareil**, cliquez sur **Vérifier l’ID** pour vous assurer que ce nom n’est pas déjà utilisé, puis cliquez sur **Créer** pour approvisionner l’appareil.
   
   ![][3]
4. Notez les informations d’identification de l’appareil (ID de l’appareil, nom d’hôte IoT Hub et clé de l’appareil). Votre application cliente en a besoin pour se connecter à la solution de surveillance à distance. Cliquez ensuite sur **Terminé**.
   
    ![][4]
5. Assurez-vous que votre appareil s’affiche dans la section Appareils. L’état de l’appareil est **En attente** jusqu’à ce que l’appareil puisse établir une connexion à la solution de surveillance à distance.
   
    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0720_2016-->