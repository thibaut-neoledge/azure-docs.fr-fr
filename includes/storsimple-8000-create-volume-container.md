<!--author=alkohli last changed: 06/22/17-->

#### Pour créer un conteneur de volumes
<a id="to-create-a-volume-container" class="xliff"></a>
1. Accédez à votre service StorSimple Device Manager et cliquez sur **Appareils**. Dans la liste tabulaire des appareils, sélectionnez un appareil et cliquez dessus. 

    ![Panneau du conteneur de volumes](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. Dans le tableau de bord de l’appareil, cliquez sur **+ Ajouter un conteneur de volumes**.

    ![Panneau du conteneur de volumes](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. Dans le panneau **Ajouter un conteneur de volumes** :
   
   1. L’appareil est automatiquement sélectionné.
   2. Saisissez un **Nom** pour votre conteneur de volume. Le nom doit contenir de 3 à 32 caractères.
   3. Sélectionnez **Activer le chiffrement de stockage cloud** pour activer le chiffrement des données envoyées à partir de l’appareil vers le cloud.
   4. Indiquez et confirmez une **Clé de chiffrement de stockage cloud** contenant de 8 à 32 caractères. Cette clé est utilisée par l’appareil pour accéder aux données chiffrées.
   5. Sélectionnez un **Compte de stockage** à associer à ce conteneur de volume. Vous pouvez choisir un compte de stockage existant ou un compte par défaut qui est généré au moment de la création du service. Vous pouvez également utiliser l’option **Ajouter nouveau** pour spécifier un compte de stockage qui n’est pas lié à cet abonnement au service.
   6. Sélectionnez **Illimitée** dans la liste déroulante **Spécifier la bande passante** si vous souhaitez consommer toute la bande passante disponible. Vous pouvez également définir cette option sur **Personnalisé** pour utiliser les contrôles de bande passante et spécifier une valeur comprise entre 1 et 1 000 Mbits/s.
      Si vos informations d’utilisation de la bande passante sont disponibles, vous pouvez allouer de la bande passante selon une planification en spécifiant **Sélectionner un modèle de bande passante**. Pour une procédure pas à pas, consultez la page [Ajouter un modèle de bande passante](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Panneau du conteneur de volumes](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. Cliquez sur **Create**.

        ![Panneau du conteneur de volumes](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       Un message s’affiche une fois le conteneur de volumes créé.

       ![Notification de la création du conteneur de volumes](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   Le conteneur de volumes nouvellement créé est répertorié dans la liste des conteneurs de volumes pour votre appareil.

   ![Panneau Ajouter un conteneur de volumes](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


