<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-add-a-storage-account-in-storsimple-8000-series-update-10"></a>Pour ajouter un compte de stockage dans StorSimple 8000 Series Update 1.0
1. Sur la page d’accueil du service StorSimple Manager, sélectionnez votre service et double-cliquez dessus. La page **Démarrage rapide** s’affiche. Sélectionnez la page **Configurer** .
2. Cliquez sur **Ajouter/modifier un compte de stockage**.
3. Dans la boîte de dialogue **Ajouter/modifier un compte de stockage**, cliquez sur **Ajouter**.
4. Dans le champ **Fournisseur** , sélectionnez le fournisseur de services cloud approprié. Les fournisseurs pris en charge sont Azure, Amazon S3, Amazon S3 avec RRS, HP et OpenStack. Indiquez les informations d’identification et l’emplacement associés au compte de stockage de vos fournisseurs de services cloud. Les champs proposés pour les informations d’identification peuvent différer en fonction du fournisseur de services cloud indiqué. 
   
   * Si vous avez sélectionné Azure comme fournisseur de services cloud, indiquez le **Nom** et la **Clé d’accès** principale pour votre compte de stockage Microsoft Azure. Pour un compte Azure, l’emplacement est automatiquement renseigné.
     
        ![Ajout d’un compte Stockage Azure](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)
   * Si vous avez sélectionné Amazon S3 ou Amazon S3 avec RRS, indiquez un **Nom de compte de stockage** convivial, une **Clé d’accès** et une **Clé secrète**. Pour Amazon S3 et Amazon S3 avec RRS, les emplacements suivants sont pris en charge :
     
     * Standard États-Unis
     * Ouest des États-Unis (Oregon)
     * Ouest des États-Unis (Californie du Nord)
     * UE (Irlande)
     * Asie-Pacifique (Singapour)
     * Asie-Pacifique (Sydney)
     * Asie-Pacifique (Tokyo)
     * Amérique du Sud (Sao Paulo)
       
       ![Ajout d’un compte de stockage Amazon](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
   * Si vous avez sélectionné HP comme fournisseur de services cloud, indiquez un **Nom de compte de stockage** convivial, un **ID de locataire**, un **Nom d’utilisateur** et un **Mot de passe**. Pour HP, les emplacement suivants sont pris en charge :
     
     * Est des États-Unis
     * Ouest des États-Unis
       
       ![Ajout d’un compte de stockage HP](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
   * Si vous avez sélectionné **Openstack** comme fournisseur de services cloud, indiquez un **Nom d’hôte**, une **Clé d’accès** et une **Clé secrète**.
     
     > [!NOTE]
     > Un nom convivial est autorisé pour tous les fournisseurs de service cloud, à l'exception d'Azure. Vous pouvez utiliser des noms conviviaux différents et créer plus d'un compte de stockage avec le même ensemble d'informations d'identification.
     > 
     > 
     
        ![Ajout du compte de stockage Openstack](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)
5. Sélectionnez **Activer le mode SSL** afin de créer un canal sécurisé pour la communication réseau entre votre appareil et le cloud. Décochez la case **Activer le mode SSL** uniquement si vous utilisez l’appareil dans un cloud privé.
   
   > [!NOTE]
   > Si vous utilisez HP comme fournisseur, SSL est toujours activé.
   > 
   > 
6. Cliquez sur l’icône en forme de coche  ![icône en forme de coche](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png). Un message s’affiche pour vous informer une fois le compte de stockage correctement créé.
7. Le compte de stockage qui vient d’être créé est affiché dans la page **Configurer**, sous **Comptes de stockage**. Cliquez sur **Enregistrer** pour enregistrer le nouveau compte de stockage. Cliquez sur **OK** lorsque vous êtes invité à confirmer l’opération.



<!--HONumber=Nov16_HO2-->


