## <a name="view-the-solution-dashboard"></a>Afficher le tableau de bord de solution

Grâce au tableau de bord de solution, vous pouvez gérer la solution déployée. Par exemple, vous pouvez afficher les données de télémétrie, ajouter des appareils et appeler des méthodes.

1. Une fois que l’approvisionnement est terminé et que la vignette de votre solution préconfigurée indique **Prêt**, choisissez **Lancer** pour ouvrir le portail de votre solution de surveillance à distance dans un nouvel onglet.

    ![Lancer la solution préconfigurée][img-launch-solution]

1. Par défaut, le portail de solution affiche le *tableau de bord*. Vous pouvez accéder à d’autres zones du portail de solution à l’aide du menu sur le côté gauche de la page.

    ![Tableau de bord de solution préconfigurée de surveillance à distance][img-menu]

## <a name="add-a-device"></a>Ajout d’un appareil

Pour qu’un appareil puisse se connecter à la solution préconfigurée, il doit s’identifier auprès d’IoT Hub à l’aide d’informations d’identification valides. Vous pouvez récupérer les informations d’identification de l’appareil à partir du tableau de bord de la solution. Les informations d’identification de l’appareil seront ajoutées dans votre application cliente dans la suite de ce didacticiel.

Si vous ne l’avez pas déjà fait, ajoutez un appareil personnalisé à votre solution de surveillance à distance. Effectuez les étapes suivantes dans le tableau de bord de la solution :

1. Dans le coin inférieur gauche du tableau de bord, cliquez sur **Ajouter un périphérique**.

   ![Ajout d’un appareil][1]

1. Dans le panneau **Appareil personnalisé**, cliquez sur **Ajouter nouveau**.

   ![Ajout d’un appareil personnalisé][2]

1. Choisissez **Me laisser définir mon propre ID d'appareil**. Entrez un ID d’appareil comme **rasppi**, cliquez sur **Vérifier l’ID** pour vous assurer que ce nom n’est pas déjà utilisé dans votre solution, puis cliquez sur **Créer** pour approvisionner l’appareil.

   ![Ajouter ID d’appareil][3]

1. Prenez note des informations d’identification de l’appareil (**ID d’appareil**, **nom d’hôte IoT Hub** et **clé d’appareil**). Votre application cliente sur Raspberry Pi a besoin de ces valeurs pour se connecter à la solution de surveillance à distance. Cliquez ensuite sur **Terminé**.

    ![Afficher les informations d’identification d’un appareil][4]

1. Sélectionnez votre appareil dans la liste d’appareils du tableau de bord de la solution. Ensuite, dans le panneau **Détails de l’appareil**, cliquez sur **Activer l’appareil**. L’état de votre appareil est maintenant **En cours d’exécution**. La solution de surveillance à distance peut désormais recevoir des données de télémétrie à partir de votre appareil et appeler des méthodes sur l’appareil.

[img-launch-solution]: media/iot-suite-v1-raspberry-pi-kit-view-solution/launch.png
[img-menu]: media/iot-suite-v1-raspberry-pi-kit-view-solution/menu.png
[1]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite0.png
[2]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite1.png
[3]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite2.png
[4]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite3.png