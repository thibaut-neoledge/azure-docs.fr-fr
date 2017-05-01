<!--author=SharS last changed: 1/7/2016-->

#### <a name="to-add-a-volume-container"></a>Pour ajouter un conteneur de volumes
1. Sur la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**.
2. Cliquez sur **Ajouter** en bas de la page. Dans la boîte de dialogue **Créer un conteneur de volumes** , procédez comme suit :
   
   1. Indiquez un **Nom** unique pour votre conteneur de volumes. Ce nom peut contenir un maximum de 32 caractères.
   2. Sélectionnez un **Compte de stockage** à associer à ce conteneur de volumes. Vous pouvez choisir un compte de stockage existant au sein du même abonnement ou sélectionner **Ajouter** pour en choisir un au sein d’un autre abonnement. Vous pouvez également choisir le compte de stockage généré lors de la création du service.
   3. Indiquez une bande passante **Illimitée** si vous souhaitez consommer toute la bande passante disponible, ou **Personnalisée** pour utiliser des contrôles de bande passante. Pour une bande passante personnalisée, indiquez une valeur entre 1 et 1 000 Mbit/s. Pour allouer la bande passante suivant une planification, vous pouvez **sélectionner un modèle de bande passante**.
   4. Nous vous recommandons de laisser l’option **Activer le chiffrement du stockage cloud** sélectionnée pour chiffrer les données envoyées vers le cloud. Ne désactivez le chiffrement que si vous utilisez une autre méthode pour chiffrer vos données. Vous ne pouvez pas modifier le paramètre de chiffrement après avoir créé le conteneur de volumes.
   5. Indiquez une **clé de chiffrement du stockage cloud** contenant entre 8 et 32 caractères. L’appareil utilise cette clé pour accéder aux données chiffrées. Dans le champ **Confirmer la clé de chiffrement du stockage cloud** , entrez de nouveau la clé de chiffrement du stockage cloud pour la confirmer. 
   6. Cliquez sur la flèche pour passer à la page suivante.
      
      ![Créer un conteneur de volumes avec le modèle de bande passante 1](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png) 
3. Si vous avez spécifié **Sélectionner un modèle de bande passante**, choisissez un modèle de bande passante existant dans la liste déroulante. Vérifiez les paramètres de planification et cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).
   
    ![Créer un conteneur de volumes avec le modèle de bande passante 2](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png) 

Le conteneur de volumes est enregistré et le nouveau conteneur de volumes apparaît sur la page **Conteneur de volumes** .

