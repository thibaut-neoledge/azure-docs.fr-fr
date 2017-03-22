


## <a name="attach-an-empty-disk"></a>Attacher un disque vide
Pour ajouter un disque de données, le plus simple consiste à attacher un disque vide, car Azure crée le fichier de disque dur virtuel (.vhd) pour vous et le stocke dans le compte de stockage.

1. Cliquez sur **Machines virtuelles (Classic)**, puis sélectionnez la machine virtuelle appropriée.

2. Dans le panneau Paramètres, cliquez sur **Disques**.

   ![Attacher un nouveau disque vide](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. Dans la barre de commandes, cliquez sur **Attacher un nouveau disque**.  
    La boîte de dialogue **Attacher un nouveau disque** s’affiche.

    ![Attacher un nouveau disque](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Renseignez les informations suivantes :
    - Dans **Nom de fichier**, acceptez le nom par défaut ou tapez-en un autre pour le fichier .vhd. Le disque de données utilise un nom généré automatiquement, même si vous tapez un autre nom pour le fichier .vhd.
    - Sélectionnez le **Type** de disque de données. Toutes les machines virtuelles prennent en charge des disques standard. La plupart prennent également en charge les disques Premium.
    - Sélectionnez la **Taille (Go)** du disque de données.
    - Dans le champ **Mise en cache de l’hôte**, sélectionnez Aucune ou En lecture seule.
    - Cliquez sur OK pour terminer.

4. Une fois créé et attaché, le disque de données est répertorié dans la section des disques de la machine virtuelle.

   ![Nouveau disque de données vide correctement attaché](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Après avoir ajouté un disque de données, vous devez vous connecter à la machine virtuelle et initialiser le disque pour pouvoir l’utiliser.

## <a name="how-to-attach-an-existing-disk"></a>Association d'un disque existant
Pour attacher un disque existant, vous devez disposer d’un fichier .vhd dans un compte de stockage. Utilisez l’applet de commande [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) pour télécharger le fichier .vhd dans le compte de stockage. Après avoir créé et téléchargé le fichier .vhd, vous pouvez l'attacher à une machine virtuelle.

1. Cliquez sur **Machines virtuelles (Classic)**, puis sélectionnez la machine virtuelle appropriée.

2. Dans le panneau Paramètres, cliquez sur **Disques**.

3. Dans la barre de commandes, cliquez sur **Attacher un disque existant**.

    ![Attacher un disque de données](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Cliquez sur **Emplacement**. Les comptes de stockage disponibles s’affichent. Sélectionnez ensuite un compte de stockage approprié dans la liste.

    ![Fournir un compte de stockage de disques](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. Un **compte de stockage** dispose d’un ou plusieurs conteneurs qui contiennent des lecteurs de disque (VHD). Sélectionnez le conteneur approprié dans la liste.

    ![Fournir le conteneur de machines virtuelles windows](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. Le panneau **Disques durs virtuels** répertorie les lecteurs de disque du conteneur. Cliquez sur l’un des disques, puis cliquez sur Sélectionner.

    ![Fournir une image de disque pour les machines virtuelles windows](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. Le panneau **Attacher un disque existant** s’affiche à nouveau, avec l’emplacement contenant le compte de stockage, le conteneur et le disque dur sélectionné (disque dur virtuel) à ajouter à la machine virtuelle.

  Définissez **Mise en cache de l’hôte** sur Aucune ou En lecture seule, puis cliquez sur OK.

    ![Disque de données correctement attaché](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
