## <a name="view-device-telemetry"></a>Afficher la télémétrie d’appareil

Vous pouvez afficher les données de télémétrie envoyées à partir de votre périphérique sur la page **Périphériques** dans la solution.

1. Sélectionnez le périphérique que vous avez configuré dans la liste des périphériques sur la page **Périphériques**. Un panneau affiche des informations sur votre périphérique, y compris un tracé de la télémétrie du périphérique :

    ![Consultez les détails du périphérique](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Choisissez **Pression** pour modifier l’affichage des données de télémétrie :

    ![Télémétrie les données de télémétrie de la pression](media/iot-suite-visualize-connecting/devicespressure.png)

1. Pour afficher les informations de diagnostic relatives à votre périphérique, faites défiler la liste jusqu'à **Diagnostics** :

    ![Afficher les diagnostics du périphérique](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agir sur votre périphérique

Pour appeler des méthodes sur vos périphériques, utilisez la page **Périphériques** dans la solution de surveillance à distance. Par exemple, dans la solution de surveillance à distance les périphériques **Condenseur** mettent en œuvre une méthode **Redémarrer**.

1. Choisissez **Périphériques** pour accéder à la page **Périphériques** dans la solution.

1. Sélectionnez le périphérique que vous avez configuré dans la liste des périphériques sur la page **Périphériques** :

    ![Sélectionnez votre périphérique physique](media/iot-suite-visualize-connecting/devicesselect.png)

1. Pour afficher une liste des méthodes que vous pouvez appeler sur votre périphérique, choisissez **Planification**. Pour planifier une méthode à exécuter sur plusieurs périphériques, vous pouvez sélectionner plusieurs périphériques dans la liste. Le volet **Planification** affiche les types de méthodes communes à tous les périphériques que vous avez sélectionnés.

1. Choisissez **Redémarrer**, définissez le nom de la tâche **RebootPhysicalChiller**, puis choisissez **Appliquer**:

    ![Planifier le redémarrage](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Un message s’affiche dans la console exécutant votre code de périphérique lorsque le périphérique traite la méthode.

> [!NOTE]
> Pour suivre l’état de la tâche dans la solution, choisissez **Afficher**.

## <a name="next-steps"></a>Étapes suivantes

L’article [Personnaliser la solution de surveillance à distance préconfigurée](../articles/iot-suite/iot-suite-remote-monitoring-customize.md) décrit quelques méthodes permettant de personnaliser la solution préconfigurée.