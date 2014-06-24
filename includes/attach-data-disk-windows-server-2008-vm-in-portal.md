Pour attacher un disque de données, procédez comme suit :

1.  Dans le [portail de gestion Azure][1], cliquez sur **Virtual
    Machines**, puis sélectionnez la machine virtuelle créée
    précédemment (**testwinvm**).

2.  Dans la barre de commandes, cliquez sur **Attach**, puis sur
    **Attach Empty Disk**.
    
    La boîte de dialogue **Attach empty disk to virtual machine**
    s'affiche.

3.  Les options **Nom de la machine virtuelle**, **Emplacement de
    stockage** et **Nom de fichier** sont déjà définies. Il vous suffit
    d'entrer la taille de disque souhaitée. Tapez **5** dans le champ
    **Size**.
    
    ![Attacher un disque
    vide](./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM2.png)
    
    **Remarque :** tous les disques sont créés à partir d'un fichier de
    disque dur virtuel dans le stockage Azure. Vous pouvez fournir le
    nom du fichier de disque dur virtuel ajouté au stockage. Quant au
    nom du disque, Azure le génère automatiquement.

4.  Cliquez sur la coche pour attacher le disque de données à la machine
    virtuelle.

5.  Cliquez sur le nom de la machine virtuelle pour afficher le tableau
    de bord et vérifier que le disque de données a été correctement
    attaché à la machine virtuelle.
    
    La machine virtuelle dispose désormais de deux disques. Le disque
    que vous avez attaché est répertorié dans la table Disks.
    
    ![Attacher un disque
    vide](./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM3.png)
    
    Une fois le disque de données attaché à la machine virtuelle, il
    apparaît hors connexion et n'est pas initialisé. Vous devez vous
    connecter à la machine virtuelle et initialiser le disque avant de
    pouvoir l'utiliser pour stocker des données.
## Connexion à la machine virtuelle à l'aide du Bureau à distance et fin de l'installation

1.  Une fois la machine virtuelle déployée, dans le portail de gestion,
    cliquez sur **Virtual Machines**, puis sur la nouvelle machine
    virtuelle. Les informations relatives à la machine virtuelle sont
    affichées.

2.  En bas de la page, cliquez sur **Connect**. Ouvrez le fichier .rpd à
    l'aide du programme Bureau à distance Windows
    (*%windir%\\system32\\mstsc.exe*).

3.  Dans la boîte de dialogue **Sécurité de Windows**, indiquez le mot
    de passe du compte **Administrateur**. (Il se peut que vous soyez
    invité à vérifier les informations d'identification de la machine
    virtuelle.) Lors de la première connexion à cette machine virtuelle,
    il est possible que plusieurs processus doivent s'effectuer, par
    exemple, la configuration de votre bureau, les mises à jour de
    Windows et l'achèvement des tâches de configuration initiales de
    Windows. Une fois que vous êtes connecté à la machine virtuelle avec
    le Bureau à distance Windows, celle-ci fonctionne comme un autre
    ordinateur.

4.  Une fois que vous êtes connecté à la machine virtuelle, ouvrez
    **Server Manager**. Dans le volet gauche, développez **Storage**,
    puis cliquez sur **Disk Management**.
    
    ![Gestionnaire de
    serveur](./media/attach-data-disk-windows-server-2008-vm-in-portal/servermanager.png)

5.  La fenêtre **Initalize Disk** s'affiche. Cliquez sur **OK**.
    
    ![Initialiser le
    disque](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializedisk0.png)

6.  Cliquez avec le bouton droit sur la zone d'allocation d'espace pour
    le disque 2, cliquez sur **New Simple Volume**, puis terminez
    l'Assistant en utilisant les valeurs par défaut.
    
    ![Nouveau volume
    simple](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializediskvolume.png)
    
    Le disque est à présent accessible en ligne et prêt à être utilisé
    avec une nouvelle lettre de lecteur.
    
    ![Initialisation
    réussie](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializesuccess.png)



[1]: http://manage.windowsazure.com
