<!--author=v-sharos last changed: 11/06/15-->

#### <a name="to-add-a-storsimple-backup-policy"></a>Pour ajouter une stratégie de sauvegarde StorSimple
1. Sur la page **Démarrage rapide**, cliquez sur l’onglet **Stratégies de sauvegarde**. La page **Stratégies de sauvegarde** s’affiche.
2. En bas de la page, cliquez sur **Ajouter** pour démarrer l’Assistant Ajouter une stratégie de sauvegarde.
   
    ![Ajouter une stratégie de sauvegarde 1](./media/storsimple-add-backup-policy-u2/AddBackupPolicy1.png)
3. Dans la boîte de dialogue **Ajouter une stratégie de sauvegarde**, sous **Définir votre stratégie de sauvegarde**, procédez comme suit :
   
   1. Spécifiez le nom de votre stratégie de sauvegarde (entre 3 et 150 caractères).
   2. Cochez les cases pour affecter un ou plusieurs volumes à cette stratégie de sauvegarde. Notez que vous ne pouvez pas sélectionner des volumes qui utilisent des fournisseurs de services cloud différents. Si vous utilisez plusieurs fournisseurs de services cloud, la liste affiche les volumes qui appartiennent au fournisseur de services cloud sélectionné en premier. Cela vous permet de regrouper les volumes appartenant à un seul fournisseur de services cloud dans un instantané.
   3. Cliquez sur l’icône en forme de flèche  ![Icône en forme de flèche](./media/storsimple-add-backup-policy-u2/HCS_ArrowIcon-include.png) pour passer à la page suivante.
      
      ![Ajouter une stratégie de sauvegarde 2](./media/storsimple-add-backup-policy-u2/AddBackupPolicy2.png)
4. Sous **Définir une planification**, procédez comme suit :
   
   1. Dans la zone **Type de sauvegarde**, sélectionnez **Instantané cloud** ou **Instantané local** dans la liste déroulante.
   2. Indiquez la fréquence des sauvegardes (spécifiez un nombre et choisissez **Jours** ou **Semaines** dans la liste déroulante).
   3. Entrez une planification de rétention.
   4. Entrez l’heure et la date de début de la stratégie de sauvegarde.  
   5. Cliquez sur l’icône en forme de coche  ![icône en forme de coche](./media/storsimple-add-backup-policy-u2/HCS_CheckIcon-include.png) pour enregistrer la stratégie.

La stratégie ajoutée s’affiche dans le tableau de la page **Stratégies de sauvegarde** .

