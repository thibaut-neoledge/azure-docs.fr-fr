# Création d'une machine virtuelle exécutant Linux

La création d'une machine virtuelle qui exécute le système
d'exploitation Linux est facilitée lorsque vous utilisez la galerie
d'images du portail de gestion Azure. Ce guide part du principe que vous
n'avez pas d'expérience en tant qu'utilisateur d'Azure. Vous pouvez
créer une machine virtuelle exécutant le système d'exploitation Linux
dans le cloud, à laquelle vous pouvez accéder et que vous pouvez
personnaliser.

Vous apprendrez à effectuer les opérations suivantes :

* [À propos des machines virtuelles dans Azure](#virtualmachine)
* [Création de la machine virtuelle](#custommachine)
* [Connexion à la machine virtuelle une fois celle-ci créée](#logon)
* [Association d'un disque de données avec la nouvelle machine
  virtuelle](#attachdisk)

**Remarque** : ce didacticiel permet de créer une machine virtuelle qui
n'est pas connectée à un réseau virtuel. Si vous voulez qu'une machine
virtuelle utilise un réseau virtuel, lorsque vous la créez, vous devez
indiquer le réseau virtuel. Pour plus d'informations sur les réseaux
virtuels, consultez la page [Présentation du réseau virtuel Azure][1].
## <a  id="virtualmachine"> </a>À propos des machines virtuelles dans Azure

Une machine virtuelle dans Azure est un serveur que vous pouvez
contrôler et gérer dans le cloud. Une fois la machine virtuelle créée
dans Azure, vous pouvez la supprimer et la recréer à votre convenance et
y accéder tout comme vous le feriez avec un serveur situé dans votre
bureau. Les fichiers de disque dur virtuel permettent de créer une
machine virtuelle. Les types suivants sont disponibles :

* **Image** - Fichier de disque dur utilisé comme modèle pour créer une
  nouvelle machine virtuelle. Une image est considérée comme un modèle,
  car elle n'inclut aucun paramètre spécifique, tel que ceux liés au nom
  d'ordinateur et au compte d'utilisateur, contrairement à une machine
  virtuelle en cours d'exécution. Si vous créez une machine virtuelle à
  l'aide d'une image, un disque de système d'exploitation est créé
  automatiquement pour la nouvelle machine virtuelle.
* **Disque** - Un disque est un fichier de disque dur virtuel que vous
  pouvez démarrer ou monter comme version en cours d'exécution d'un
  système d'exploitation. Une fois l'image déployée, elle devient un
  disque. Un disque est toujours créé lorsque vous vous servez d'une
  image pour créer une machine virtuelle. Un fichier de disque dur
  virtuel lié à un matériel virtualisé et qui s'exécute dans le cadre
  d'un service est un disque.

Les options suivantes sont disponibles dans le cadre de l'utilisation
d'images pour créer une machine virtuelle :

* Créez une machine virtuelle en utilisant une image fournie dans la
  galerie d'images du portail de gestion Azure.
* Créez et téléchargez un fichier .vhd contenant une image dans Azure,
  puis créez une machine virtuelle à l'aide de l'image. Pour plus
  d'informations sur la création et le téléchargement d'une image
  personnalisée, consultez la page [Création et téléchargement d'un
  disque dur virtuel contenant le système d'exploitation
  Linux](/en-us/manage/linux/common-tasks/upload-a-vhd/).

Chaque machine virtuelle réside dans un service cloud, soit seule, soit
avec d'autres machines virtuelles. Vous pouvez placer les machines
virtuelles dans le même service cloud pour leur permettre de communiquer
les unes avec les autres, pour équilibrer la charge du trafic réseau et
pour garantir leur haute disponibilité. Pour plus d'informations sur les
services cloud et les machines virtuelles, consultez la section
<< Modèles d'exécution >> de la page [Présentation d'Azure][2].
## <a  id="custommachine"> </a>Création de la machine virtuelle

La méthode **À partir de la galerie** permet de créer une machine
virtuelle personnalisée dans le portail de gestion. Elle propose des
options supplémentaires pour la configuration de la machine virtuelle
lors de sa création, comme les ressources connectées, le nom DNS et la
connectivité réseau, le cas échéant.

1.  Connectez-vous au [portail de gestion][3] Azure. Dans la barre de
    commandes, cliquez sur **New**.

2.  Cliquez sur **Virtual Machine**, puis sur **From Gallery**.

3.  Dans **Choose an Image**, sélectionnez une image dans l'une des
    listes. (Les images disponibles dépendent de l'abonnement que vous
    utilisez.) Cliquez sur la flèche pour continuer.

4.  Si plusieurs versions de l'image sont disponibles, dans **Version
    Release Date**, choisissez la version à utiliser.

5.  Dans **Nom de la machine virtuelle**, tapez le nom que vous
    souhaitez utiliser. Pour cette machine virtuelle, tapez
    **MonTestMV1**.

6.  Dans **Taille**, sélectionnez la taille à utiliser pour la machine
    virtuelle. Celle-ci dépend du nombre de cœurs nécessaires à votre
    application. Pour cette machine virtuelle, sélectionnez la taille la
    plus petite.

7.  Dans **Nouveau nom d'utilisateur**, tapez le nom du compte qui
    servira à administrer la machine virtuelle. Vous ne pouvez pas
    utiliser le nom d'utilisateur racine. Pour cette machine virtuelle,
    tapez **NouvelUtilisateur1**.

8.  Sous Authentification, cochez **Fournir un mot de passe**.
    Fournissez ensuite les informations requises, puis cliquez sur la
    flèche pour continuer.

9.  Bien qu'il soit possible de rassembler plusieurs machines virtuelles
    dans le service cloud, vous n'allez en créer qu'une seule dans le
    cadre de ce didacticiel. Pour ce faire, sélectionnez **Create a new
    cloud service**.

10. Dans **Cloud Service DNS Name**, tapez un nom comprenant entre 3 et
    24 caractères (minuscules et chiffres uniquement). Ce nom fait alors
    partie de l'URI servant à contacter la machine virtuelle via le
    service cloud. Pour cette machine virtuelle, tapez **MonService1**.

11. Dans **Region/Affinity Group/Virtual Network**, sélectionnez
    l'emplacement de la machine virtuelle.

12. Vous pouvez sélectionner le compte sur lequel le fichier de disque
    dur virtuel est stocké. Pour ce didacticiel, acceptez le paramètre
    par défaut **Use an Automatically Generated Storage Account**.

13. Sous **Availability Set**, utilisez le paramètre par défaut
    **None**. Cliquez sur la coche pour créer la machine virtuelle, puis
    sur la flèche pour continuer.

14. Sous **VM Agent**, indiquez si vous souhaitez installer l'agent MV.
    Cet agent fournit l'environnement dans lequel vous installez les
    extensions qui permettent d'interagir avec la machine virtuelle.
    Pour plus d'informations, consultez la page [Utilisation
    d'extensions][4].

15. Sous **Points de terminaison**, vérifiez le point de terminaison
    créé automatiquement pour autoriser les connexions SSH (Secure
    Shell) à la machine virtuelle. Les points de terminaison permettent
    aux ressources disponibles sur Internet ou aux autres réseaux
    virtuels de communiquer avec une machine virtuelle. Vous pouvez
    ajouter d'autres points de terminaison maintenant ou en créer
    ultérieurement. Pour obtenir des instructions sur leur création à
    une date ultérieure, consultez la page [Configuration de la
    communication avec une machine virtuelle][5].

Une fois la machine virtuelle et le service cloud créés, le portail de
gestion répertorie la nouvelle machine virtuelle sous **Virtual
Machines** et le service cloud sous **Cloud Services**. Ces derniers
démarrent automatiquement.
## <a  id="logon"> </a>Connexion à la machine virtuelle une fois celle-ci créée

Pour gérer les paramètres de la machine virtuelle et des applications
associées, vous pouvez utiliser un client SSH. Pour ce faire, vous devez
installer ce dernier sur l'ordinateur utilisé pour accéder à la machine
virtuelle. Vous pouvez opérer votre choix parmi de nombreux clients SSH,
dont :

* Si vous utilisez un ordinateur qui exécute un système d'exploitation
  Windows, vous pouvez utiliser un client SSH tel que PuTTY. Pour plus
  d'informations, consultez la page [Téléchargement PuTTY][6].
* Si vous utilisez un ordinateur qui exécute un système d'exploitation
  Linux, vous pouvez utiliser un client SSH comme OpenSSH. Pour plus
  d'informations, consultez la page [OpenSSH][7].

Ce didacticiel montre comment utiliser le programme PuTTY pour accéder à
la machine virtuelle.

1.  Recherchez le **Nom d'hôte** et les **Informations sur le port**
    dans le portail de gestion. Les informations dont vous avez besoin
    sont disponibles dans le tableau de bord de la machine virtuelle.
    Cliquez sur le nom de la machine virtuelle, puis recherchez
    **Détails SSH** dans la section **Aperçu rapide** du tableau de
    bord.
    
    ![Rechercher les détails
    SSH](./media/CreateVirtualMachineLinuxTutorial/SSHdetails.png)

2.  Ouvrez le programme PuTTY.

3.  Entrez le **Nom d'hôte** et les **Informations sur le port**
    disponibles dans le tableau de bord, puis cliquez sur **Ouvrir**.
    
    ![Entrer le nom d’hôte et les informations sur le
    port](./media/CreateVirtualMachineLinuxTutorial/putty.png)

4.  Connectez-vous à la machine virtuelle à l'aide du compte
    NouvelUtilisateur1 spécifié à la création de la machine.
    
    ![Se connecter à la nouvelle machine
    virtuelle](./media/CreateVirtualMachineLinuxTutorial/sshlogin.png)
    
    Vous pouvez désormais utiliser la machine virtuelle tout comme vous
    le feriez avec un serveur.
## <a  id="attachdisk"> </a>Association d'un disque de données avec la nouvelle machine virtuelle

Il se peut que votre application ait besoin de stocker des données. Pour
ce faire, associez un disque de données à la machine virtuelle créée
précédemment. Le moyen le plus simple consiste à associer un disque de
données vide à la machine.

**Remarque : disque de données et disque de ressources**  
 Les disques de données résident sur le stockage Azure et sont utilisés
pour le stockage permanent des fichiers et des données de l'application.

Chaque machine virtuelle créée est également associée à un *disque de
ressources* local temporaire. Étant donné que les données qui y figurent
risquent de ne pas résister aux redémarrages, le disque de ressources
est souvent utilisé par les applications et les processus exécutés dans
la machine virtuelle pour le stockage temporaire des données, ainsi que
pour stocker les fichiers de pagination ou d'échange du système
d'exploitation.

Sous Linux, le disque de ressources est habituellement géré par l'agent
Linux Azure et monté automatiquement dans **/mnt/resource** (ou **/mnt**
pour les images Ubuntu). Pour plus d'informations, consultez le [guide
d'utilisation de l'agent Linux Azure][8].

1.  Si ce n'est pas déjà fait, connectez-vous au portail de gestion
    Azure.

2.  Cliquez sur **Machines virtuelles**, puis sélectionnez la machine
    virtuelle **MonTestMV1** créée précédemment.

3.  Dans la barre de commandes, cliquez sur **Attach**, puis sur
    **Attach Empty Disk**.
    
    La boîte de dialogue **Attach Empty Disk** apparaît.
    
    ![Définir les détails du
    disque](./media/CreateVirtualMachineLinuxTutorial/attachnewdisklinux.png)

4.  Les options **Nom de la machine virtuelle**, **Emplacement de
    stockage** et **Nom de fichier** sont déjà définies. Il vous suffit
    d'entrer la taille de disque souhaitée. Tapez **5** dans le champ
    **Size**.
    
    **Remarque :** tous les disques sont créés à partir d'un fichier de
    disque dur virtuel dans le stockage Azure. Vous pouvez fournir le
    nom du fichier de disque dur virtuel ajouté au stockage. Quant au
    nom du disque, il est généré automatiquement.

5.  Cliquez sur la coche pour attacher le disque de données à la machine
    virtuelle.

6.  Pour vérifier si le disque de données a été correctement attaché à
    la machine virtuelle, reportez-vous au tableau de bord. Pour
    afficher ce dernier, cliquez sur le nom de la machine virtuelle.
    
    La machine virtuelle inclut désormais deux disques et le disque que
    vous venez d'attacher est répertorié dans le tableau**Disques**.
    
    ![Disque correctement
    attaché](./media/CreateVirtualMachineLinuxTutorial/attachemptysuccess.png)

Une fois ajouté, le disque de données que vous venez d'attacher à la
machine virtuelle est hors connexion et n'est pas initialisé. Vous devez
vous connecter à la machine et initialiser le disque pour l'utiliser à
des fins de stockage des données.

1.  Connectez-vous à la machine virtuelle en suivant les étapes
    répertoriées ci-dessus dans **Connexion à la machine virtuelle une
    fois celle-ci créée**.

2.  Dans la fenêtre SSH, entrez la commande suivante, puis le mot de
    passe du compte :
    
    `sudo grep SCSI /var/log/messages`
    
    L'identificateur du dernier disque de données ajouté est disponible
    dans les messages qui s'affichent.
    
    ![Identifier le
    disque](./media/CreateVirtualMachineLinuxTutorial/diskmessages.png)

3.  Dans la fenêtre SSH, tapez la commande suivante pour créer un
    appareil, puis entrez le mot de passe du compte :
    
    `sudo fdisk /dev/sdc`
    
    > [WACOM.NOTE] Dans cet exemple, vous pouvez devoir utiliser `sudo
    > -i` avec certaines distributions si /sbin ou /usr/sbin n'est pas
    > dans votre `$PATH`.

4.  Tapez **n** pour créer une partition.
    
    ![Créer un
    appareil](./media/CreateVirtualMachineLinuxTutorial/diskpartition.png)

5.  Tapez **p** pour définir la partition comme partition principale,
    tapez **1** pour la définir comme première partition, puis appuyez
    sur Entrée pour accepter la valeur par défaut du cylindre.
    
    ![Créer une
    partition](./media/CreateVirtualMachineLinuxTutorial/diskcylinder.png)

6.  Tapez **p** pour afficher les détails relatifs au disque faisant
    l'objet de la partition.
    
    ![Répertorier les informations sur le
    disque](./media/CreateVirtualMachineLinuxTutorial/diskinfo.png)

7.  Tapez **w** pour écrire les paramètres du disque.
    
    ![Écrire les modifications apportées au
    disque](./media/CreateVirtualMachineLinuxTutorial/diskwrite.png)

8.  Vous devez créer le système de fichiers sur la nouvelle partition.
    Par exemple, tapez la commande suivante pour créer le système de
    fichiers, puis entrez le mot de passe du compte :
    
    `sudo mkfs -t ext4 /dev/sdc1`
    
    ![Créer le système de
    fichiers](./media/CreateVirtualMachineLinuxTutorial/diskfilesystem.png)
    
    > [WACOM.NOTE] Notez que les systèmes SUSE Linux Enterprise 11
    > n'offrent que l'accès en lecture seule aux systèmes de fichiers
    > ext4. Pour ces systèmes, il est recommandé de formater le nouveau
    > système de fichiers en ext3 plutôt que ext4.

9.  Vous devez ensuite avoir un répertoire disponible pour monter le
    nouveau système de fichiers. Par exemple, tapez la commande suivante
    pour créer un répertoire de montage du lecteur, puis entrez le mot
    de passe du compte :
    
    `sudo mkdir /datadrive`

10. Tapez la commande suivante pour monter le lecteur :
    
    `sudo mount /dev/sdc1 /datadrive`
    
    Le disque de données est désormais prêt à être utilisé en tant que
    **/datadrive**.

11. Ajoutez le nouveau lecteur à /etc/fstab :
    
    Pour vous assurer que le lecteur est remonté automatiquement après
    un redémarrage, vous devez l'ajouter au fichier /etc/fstab. En
    outre, il est vivement recommandé d'utiliser l'UUID (identificateur
    global unique) dans /etc/fstab comme référence au lecteur, plutôt
    que le nom d'appareil uniquement (par exemple, /dev/sdc1). Pour
    rechercher l'UUID du nouveau lecteur, vous pouvez vous servir de
    l'utilitaire **blkid** :
    
        `sudo -i blkid`
    
    Le résultat ressemble à ce qui suit :
    
        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`
    
    > [WACOM.NOTE] il se peut que blkid ne nécessite pas
    > systématiquement un accès sudo. Toutefois, il peut s'avérer plus
    > simple à exécuter avec `sudo -i` dans certaines distributions si
    > /sbin ou /usr/sbin ne sont pas situés dans `$PATH`.
    
    **Attention :** si vous ne modifiez pas correctement le fichier
    /etc/fstab, il se peut que le système ne puisse plus démarrer. En
    cas de doute, référez-vous à la documentation de la distribution
    pour obtenir des informations sur la modification adéquate de ce
    fichier. Il est également fortement recommandé de créer une
    sauvegarde du fichier /etc/fstab avant de le modifier.
    
    Dans un éditeur de texte, entrez les informations relatives au
    nouveau système de fichiers à la fin du fichier /etc/fstab. Dans cet
    exemple, nous utilisons la valeur UUID pour le nouvel appareil
    **/dev/sdc1** créé lors des étapes précédentes et le point de
    montage **/datadrive** :
    
        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`
    
    Ou, sur les systèmes basés sur SUSE Linux, vous pouvez êtes amené à
    devoir utiliser un format légèrement différent :
    
        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`
    
    Si des lecteurs de données ou partitions supplémentaires sont créés,
    vous devez également les ajouter séparément au fichier /etc/fstab.
    
    Vous pouvez désormais vérifier si le système de fichiers est monté
    correctement en le démontant puis en le remontant, par exemple en
    utilisant l'exemple de point de montage `/datadrive` créé lors des
    étapes précédentes :
    
        `sudo umount /datadrive`
        `sudo mount /datadrive`
    
    Si la deuxième commande génère une erreur, vérifiez que la syntaxe
    utilisée dans le fichier /etc/fstab est correcte.
    
    > [WACOM.NOTE] la suppression ultérieure d'un disque de données
    > sans modifier fstab pourrait entraînter l'échec du démarrage de la
    > machine virtuelle. S'il s'agit d'une occurrence courante, la
    > plupart des distributions proposent les options fstab `nofail`
    > et/ou `nobootwait` qui permettent à un système de démarrer même si
    > le disque n'est pas présent. Pour plus d'informations sur ces
    > paramètres, consultez la documentation de votre distribution.
## Étapes suivantes

Pour en savoir plus sur Linux sous Azure, consultez les articles
suivants :

* [Présentation de Linux sous Azure][9]

* [Utilisation des outils en ligne de commande Azure pour Mac et
  Linux][10]



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://go.microsoft.com/fwlink/p/?LinkId=311926
[3]: http://manage.windowsazure.com
[4]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[5]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/setup-endpoints/
[6]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[7]: http://www.openssh.org/
[8]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/linux-agent-guide/
[9]: http://www.windowsazure.com/en-us/documentation/articles/introduction-linux/
[10]: http://www.windowsazure.com/en-us/documentation/articles/xplat-cli/
