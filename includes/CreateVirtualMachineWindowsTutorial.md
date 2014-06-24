# Création d'une machine virtuelle exécutant Windows Server

Il est facile de créer une machine virtuelle exécutant le système
d'exploitation Windows Server lorsque vous utilisez la bibliothèque
d'images dans le portail de gestion Azure. Ce didacticiel montre comment
créer une machine virtuelle exécutant Windows Server dans le cloud, à
laquelle vous pouvez accéder et que vous pouvez personnaliser. Vous
n'avez besoin d'aucune expérience préalable avec Azure pour pouvoir
suivre ce didacticiel.

Vous apprendrez à effectuer les opérations suivantes :

* [À propos des machines virtuelles dans Azure](#virtualmachine)
* [Création de la machine virtuelle](#custommachine)
* [Connexion à la machine virtuelle une fois celle-ci créée](#logon)
* [Association d'un disque de données avec la nouvelle machine
  virtuelle](#attachdisk)

**Remarque :** ce didacticiel permet de créer une machine virtuelle qui
n'est pas connectée à un réseau virtuel. Si vous voulez qu'une machine
virtuelle utilise un réseau virtuel, lorsque vous la créez, vous devez
indiquer le réseau virtuel. Pour plus d'informations sur les réseaux
virtuels, consultez la page [Présentation du réseau virtuel Azure][1].
## <a  id="virtualmachine"> </a>À propos des machines virtuelles dans Azure

Une machine virtuelle dans Azure est un serveur que vous pouvez
contrôler et gérer dans le cloud. Une fois que vous avez créé une
machine virtuelle dans Azure, vous pouvez la supprimer et la récréer
chaque fois que nécessaire, et vous pouvez y accéder de la même façon
que n'importe quel autre serveur. Pour en savoir plus, consultez
[Machines virtuelles][2].
## <a  id="custommachine"> </a>Création de la machine virtuelle

Ce didacticiel vous montre comment utiliser la méthode **From Gallery**
dans le portail de gestion pour créer une machine virtuelle
personnalisée. Cette méthode fournit davantage d'options que la méthode
**Quick Create** pour la configuration de la machine virtuelle lors de
sa création.

1.  Connectez-vous avec votre abonnement pour utiliser le [portail de
    gestion][3] Azure. Si vous n'avez pas d'abonnement, vous pouvez vous
    enregistrer pour une [évaluation gratuite][4].

2.  Dans la barre de commandes, cliquez sur **New**.

3.  Cliquez sur **Virtual Machine**, puis sur **From Gallery**.

4.  Dans **Choose an Image**, sélectionnez **Windows Server 2012 R2
    Datacenter**. (Les images disponibles dépendent de l'abonnement que
    vous utilisez.) Cliquez sur la flèche pour continuer.

5.  Si plusieurs versions de l'image sont disponibles, dans **Version
    Release Date**, choisissez la version à utiliser.

6.  Dans **Virtual Machine Name**, tapez le nom que vous souhaitez
    utiliser pour la machine virtuelle. Pour cette machine virtuelle,
    tapez **MonTestMV1**.

7.  Dans **Size**, sélectionnez la taille de la machine virtuelle. La
    taille à sélectionner dépend du nombre de cœurs requis pour exécuter
    votre application. Pour cette machine virtuelle, sélectionnez la
    taille la plus petite.

8.  Dans **New User Name**, tapez le nom du compte administratif que
    vous voulez utiliser pour gérer le serveur. Pour cette machine
    virtuelle, tapez **MonTestAdminMV1**.

9.  Dans **New Password**, tapez un mot de passe fort pour le compte
    administratif de la machine virtuelle. Dans **Confirm Password**,
    entrez de nouveau le mot de passe. Cliquez sur la flèche pour
    continuer.

10. Bien que vous puissiez placer plusieurs machines virtuelles dans un
    service cloud afin de fournir des applications robustes, dans le
    cadre de ce didacticiel, vous créez une seule machine virtuelle.
    Pour ce faire, sélectionnez **Create a new cloud service**.

11. Dans **Cloud Service DNS Name**, tapez un nom comprenant entre 3 et
    24 caractères (minuscules et chiffres uniquement). Ce nom fait alors
    partie de l'URI servant à contacter la machine virtuelle via le
    service cloud. Pour cette machine virtuelle, tapez **MonService1**.

12. Dans **Region/Affinity Group/Virtual Network**, sélectionnez
    l'emplacement de la machine virtuelle.

13. Pour stocker le fichier de disque dur virtuel qu'utilisera la
    machine virtuelle, acceptez le paramètre par défaut de **Use an
    Automatically Generated Storage Account**.

14. Sous **Availability Set**, utilisez le paramètre par défaut
    **None**. Cliquez sur la flèche pour continuer.

15. Sous **VM Agent**, indiquez si vous souhaitez installer l'agent MV.
    Cet agent fournit l'environnement dans lequel vous installez les
    extensions qui permettent d'interagir avec la machine virtuelle.
    Pour plus d'informations, consultez la page [Utilisation
    d'extensions][5].

16. Sous **Endpoints**, passez en revue les nouveaux points de
    terminaison qui seront créés pour autoriser les connexions à la
    machine virtuelle, tels que le Bureau à distance et la communication
    à distance Windows PowerShell. Vous pouvez également ajouter des
    points de terminaison maintenant ou les créer ultérieurement. Pour
    obtenir des instructions sur leur création à une date ultérieure,
    consultez la page [Configuration de la communication avec une
    machine virtuelle][6].

17. Cliquez sur la coche pour créer la machine virtuelle.
    
    Une fois la machine virtuelle et le service cloud créés, le portail
    de gestion répertorie la nouvelle machine virtuelle sous **Virtual
    Machines** et le service cloud sous **Cloud Services**. Ces derniers
    démarrent automatiquement.
## <a  id="logon"> </a>Connexion à la machine virtuelle une fois celle-ci créée

Vous pouvez vous connecter à la machine virtuelle que vous avez créée
pour gérer à la fois ses paramètres et les applications s'exécutant sur
celle-ci.

1.  Connectez-vous au [portail de gestion][3] Azure.

2.  Cliquez sur **Virtual Machines**, puis sélectionnez la machine
    virtuelle **MonTestMV1**.

3.  Dans la barre de commandes, cliquez sur **Connect**.

4.  Cliquez sur **Open** pour utiliser le fichier de protocole RDP
    (Remote Desktop Protocol) qui a été créé automatiquement pour la
    machine virtuelle.

5.  Cliquez sur **Connect**.
    
    ![Poursuivre la
    connexion](./media/CreateVirtualMachineWindowsTutorial/connectpublisher.png)

6.  Dans la zone de mot de passe, tapez le nom d'utilisateur et le mot
    de passe que vous avez spécifiés lorsque vous avez créé la machine
    virtuelle, puis cliquez sur **OK**.

7.  Cliquez sur **Yes** pour vérifier l'identité de la machine
    virtuelle.
    
    ![Vérifier l’identité de la
    machine](./media/CreateVirtualMachineWindowsTutorial/connectverify.png)
    
    Vous pouvez à présent utiliser la machine virtuelle de la même façon
    qu'un serveur de votre bureau.
## <a  id="attachdisk"> </a>Association d'un disque de données avec la nouvelle machine virtuelle

Il se peut que votre application ait besoin de stocker des données. Pour
cela, attachez un disque de données à la machine virtuelle. Le moyen le
plus simple consiste à associer un disque de données vide à la machine
virtuelle.

1.  Connectez-vous au [portail de gestion][3] Azure.

2.  Cliquez sur **Virtual Machines**, puis sélectionnez la machine
    virtuelle **MonTestMV1**.

3.  Dans la barre de commandes, cliquez sur **Attach**, puis sur
    **Attach Empty Disk**.
    
    La boîte de dialogue **Attach Empty Disk** apparaît.
    
    > [WACOM.NOTE] La page Quick Start peut s'afficher plutôt que le
    > tableau de bord et la barre de commandes. Si c'est le cas, cliquez
    > sur **Dashboard** en haut.

4.  Les options **Virtual Machine Name**, **Storage Location**, **File
    Name** et **Host Cache Preference** sont déjà définies. Il vous
    suffit d'entrer la taille de disque souhaitée. Tapez **5** dans le
    champ **Size**.
    
    **Remarque :** tous les disques sont créés à partir d'un fichier de
    disque dur virtuel dans le stockage Azure. Vous pouvez fournir le
    nom du fichier de disque dur virtuel ajouté au stockage. Quant au
    nom du disque, Azure le génère automatiquement.

5.  Cliquez sur la coche pour attacher le disque de données à la machine
    virtuelle.

6.  Cliquez sur le nom de la machine virtuelle. Le tableau de bord
    s'affiche pour vous permettre de vérifier que le disque de données a
    été correctement attaché à la machine virtuelle.
    
    La machine virtuelle dispose à présent de 2 disques. Le disque que
    vous avez attaché est répertorié dans la table **Disks**.
    
    ![Attacher un disque
    vide](./media/CreateVirtualMachineWindowsTutorial/attachemptysuccess.png)
    
    Une fois le disque de données attaché à la machine virtuelle, il
    apparaît hors connexion et n'est pas initialisé. Pour pouvoir
    l'utiliser en vue de stocker des données, vous devez vous connecter
    à la machine virtuelle et initialiser le disque.

7.  Connectez-vous à la machine virtuelle en suivant la procédure de la
    section précédente, **Connexion à la machine virtuelle une fois
    celle-ci créée**.

8.  Une fois que vous êtes connecté à la machine virtuelle, ouvrez
    **Server Manager**. Dans le volet gauche, développez **Storage**,
    puis cliquez sur **Disk Management**.
    
    ![Initialiser le disque dans le gestionnaire de
    serveur](./media/CreateVirtualMachineWindowsTutorial/servermanager.png)

9.  Cliquez avec le bouton droit sur **Disk 2**, puis cliquez sur
    **Initialize Disk**.
    
    ![Démarrer
    l’initialisation](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

10. Cliquez sur **OK** pour démarrer le processus d'initialisation.

11. Cliquez avec le bouton droit sur la zone d'allocation d'espace pour
    le disque 2, cliquez sur **New Simple Volume**, puis terminez
    l'Assistant en utilisant les valeurs par défaut.
    
    ![Créer le
    volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)
    
    Le disque est à présent accessible en ligne et prêt à être utilisé
    avec une nouvelle lettre de lecteur.
    
    ![Initialisation
    réussie](./media/CreateVirtualMachineWindowsTutorial/initializesuccess.png)
## Étapes suivantes

Pour en savoir plus sur la configuration de machines virtuelles Windows
sous Azure, consultez les articles suivants :

-[Connexion des machines virtuelles dans un service cloud][7]

-[Gestion de la disponibilité des machines virtuelles][8]



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://go.microsoft.com/fwlink/p/?LinkID=271224
[3]: http://manage.windowsazure.com
[4]: http://go.microsoft.com/fwlink/p/?LinkID=23435
[5]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[6]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/setup-endpoints/
[7]: http://www.windowsazure.com/en-us/documentation/articles/cloud-services-connect-virtual-machine/
[8]: http://www.windowsazure.com/en-us/documentation/articles/manage-availability-virtual-machines/
