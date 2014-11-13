## <span id="logon"></span> </a>Connexion à la machine virtuelle une fois celle-ci créée

Pour gérer les paramètres de la machine virtuelle et des applications associées, vous pouvez utiliser un client SSH. Pour ce faire, vous devez installer ce dernier sur l’ordinateur utilisé pour accéder à la machine virtuelle. Vous pouvez opérer votre choix parmi de nombreux clients SSH, dont :

-   Si vous utilisez un ordinateur qui exécute un système d’exploitation Windows, vous pouvez utiliser un client SSH tel que PuTTY. Pour plus d’informations, consultez la page [Téléchargement PuTTY][Téléchargement PuTTY].
-   Si vous utilisez un ordinateur qui exécute un système d’exploitation Linux, vous pouvez utiliser un client SSH comme OpenSSH. Pour plus d’informations, consultez la page [OpenSSH][OpenSSH].

Ce didacticiel montre comment utiliser le programme PuTTY pour accéder à la machine virtuelle.

1.  Recherchez le **Nom d’hôte** et les **Informations sur le port** dans le portail de gestion. Les informations dont vous avez besoin sont disponibles dans le tableau de bord de la machine virtuelle. Cliquez sur le nom de la machine virtuelle, puis recherchez **Détails SSH** dans la section **Aperçu rapide** du tableau de bord.

    ![Rechercher les détails SSH][Rechercher les détails SSH]

2.  Ouvrez le programme PuTTY.

3.  Entrez le **Nom d’hôte** et les **Informations sur le port** disponibles dans le tableau de bord, puis cliquez sur **Ouvrir**.

    ![Entrer le nom d’hôte et les informations sur le port][Entrer le nom d’hôte et les informations sur le port]

4.  Connectez-vous à la machine virtuelle à l'aide du compte NouvelUtilisateur1 ajouté lors de la création de la machine.

    ![Se connecter à la nouvelle machine virtuelle][Se connecter à la nouvelle machine virtuelle]

    Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

## <span id="attachdisk"></span> </a>Association d’un disque de données avec la nouvelle machine virtuelle

Il se peut que votre application ait besoin de stocker des données. Pour ce faire, associez un disque de données à la machine virtuelle créée précédemment. Le moyen le plus simple consiste à associer un disque de données vide à la machine.

Sous Linux, le disque de ressources est habituellement géré par l’agent Linux Azure et monté automatiquement dans **/mnt/resource** (ou **/mnt** pour les images Ubuntu). Par contre, sous Linux, le disque de données peut être nommé par le noyau `/dev/sdc`, et les utilisateurs doivent partitionner, formater et monter cette ressource. Pour plus d’informations, consultez le [guide d’utilisation de l’agent Linux Azure][guide d’utilisation de l’agent Linux Azure].

> [WACOM.NOTE] Ne stockez pas de données sur le disque de ressources. Ce disque assure le stockage temporaire des applications et des processus et permet de stocker les données que vous n'avez pas besoin de garder, telles que les fichiers d'échange. Les disques de données sont stockés sur Azure Storage en tant que fichiers .vhd dans les objets blob de pages et assurent la redondance du stockage pour protéger vos données. Pour plus d'informations, consultez la page [À propos des disques et des images dans Azure][À propos des disques et des images dans Azure].

1.  Si ce n’est pas déjà fait, connectez-vous au portail de gestion Azure.

2.  Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle **MonTestMV1** créée précédemment.

3.  Dans la barre de commandes, cliquez sur **Attach**, puis sur **Attach Empty Disk**.

    La boîte de dialogue **Attach Empty Disk** apparaît.

    ![Définir les détails du disque][Définir les détails du disque]

4.  Les options **Nom de la machine virtuelle**, **Emplacement de stockage** et **Nom de fichier** sont déjà définies. Il vous suffit d’entrer la taille de disque souhaitée. Tapez **5** dans le champ **Size**.

    **Remarque :** tous les disques sont créés à partir d’un fichier de disque dur virtuel dans le stockage Azure. Vous pouvez fournir le nom du fichier de disque dur virtuel ajouté au stockage. Quant au nom du disque, il est généré automatiquement.

5.  Cliquez sur la coche pour attacher le disque de données à la machine virtuelle.

6.  Pour vérifier si le disque de données a été correctement attaché à la machine virtuelle, reportez-vous au tableau de bord. Pour afficher ce dernier, cliquez sur le nom de la machine virtuelle.

    La machine virtuelle inclut désormais deux disques et le disque que vous venez d’attacher est répertorié dans le tableau**Disques**.

    ![Disque correctement attaché][Disque correctement attaché]

Une fois ajouté, le disque de données que vous venez d’attacher à la machine virtuelle est hors connexion et n’est pas initialisé. Vous devez vous connecter à la machine et initialiser le disque pour l’utiliser à des fins de stockage des données.

1.  Connectez-vous à la machine virtuelle en suivant les étapes répertoriées ci-dessus dans **Connexion à la machine virtuelle une fois celle-ci créée**.

2.  Dans la fenêtre SSH, entrez la commande suivante, puis le mot de passe du compte :

    `sudo grep SCSI /var/log/messages`

    L’identificateur du dernier disque de données ajouté est disponible dans les messages qui s’affichent.

    ![Identifier le disque][Identifier le disque]

3.  Dans la fenêtre SSH, tapez la commande suivante pour créer un appareil, puis entrez le mot de passe du compte :

    `sudo fdisk /dev/sdc`

    > [WACOM.NOTE] Dans cet exemple, vous pouvez devoir utiliser `sudo -i` avec certaines distributions si /sbin ou /usr/sbin n’est pas dans votre `$PATH`.

4.  Tapez **n** pour créer une partition.

    ![Créer un appareil][Créer un appareil]

5.  Tapez **p** pour définir la partition comme partition principale, tapez **1** pour la définir comme première partition, puis appuyez sur Entrée pour accepter la valeur par défaut du cylindre.

    ![Créer une partition][Créer une partition]

6.  Tapez **p** pour afficher les détails relatifs au disque faisant l’objet de la partition.

    ![Répertorier les informations sur le disque][Répertorier les informations sur le disque]

7.  Tapez **w** pour écrire les paramètres du disque.

    ![Écrire les modifications apportées au disque][Écrire les modifications apportées au disque]

8.  Vous devez créer le système de fichiers sur la nouvelle partition. Par exemple, tapez la commande suivante pour créer le système de fichiers, puis entrez le mot de passe du compte :

    `sudo mkfs -t ext4 /dev/sdc1`

    ![Créer le système de fichiers][Créer le système de fichiers]

    > [WACOM.NOTE] Notez que les systèmes SUSE Linux Enterprise 11 n'offrent que l'accès en lecture seule aux systèmes de fichiers ext4. Pour ces systèmes, il est recommandé de formater le nouveau système de fichiers en ext3 plutôt que ext4.

9.  Créez un répertoire pour monter le nouveau système de fichiers. Par exemple, entrez la commande suivante, puis le mot de passe du compte :

    `sudo mkdir /datadrive`

10. Tapez la commande suivante pour monter le lecteur :

    `sudo mount /dev/sdc1 /datadrive`

    Le disque de données est désormais prêt à être utilisé en tant que **/datadrive**.

11. Ajoutez le nouveau lecteur à /etc/fstab :

    Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, vous devez l’ajouter au fichier /etc/fstab. En outre, il est vivement recommandé d’utiliser l’UUID (identificateur global unique) dans /etc/fstab comme référence au lecteur, plutôt que le nom d’appareil uniquement (par exemple, /dev/sdc1). Pour rechercher l’UUID du nouveau lecteur, vous pouvez vous servir de l’utilitaire **blkid** :

        `sudo -i blkid`

    Le résultat ressemble à ce qui suit :

        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

    > [WACOM.NOTE] il se peut que blkid ne nécessite pas systématiquement un accès sudo. Toutefois, il peut s’avérer plus simple à exécuter avec `sudo -i` dans certaines distributions si /sbin ou /usr/sbin ne sont pas situés dans `$PATH`.

    **Attention :** si vous ne modifiez pas correctement le fichier /etc/fstab, il se peut que le système ne puisse plus démarrer. En cas de doute, reportez-vous à la documentation de la distribution pour obtenir des informations sur la modification adéquate de ce fichier. Il est par ailleurs vivement recommandé de créer une sauvegarde du fichier /etc/fstab avant de le modifier.

    Dans un éditeur de texte, entrez les informations relatives au nouveau système de fichiers à la fin du fichier /etc/fstab. Dans cet exemple, nous utilisons la valeur UUID pour le nouvel appareil **/dev/sdc1** créé lors des étapes précédentes et le point de montage **/datadrive** :

        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

    Ou, sur les systèmes basés sur SUSE Linux, vous pouvez êtes amené à devoir utiliser un format légèrement différent :

        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

    Si des lecteurs de données ou partitions supplémentaires sont créés, vous devez également les ajouter séparément au fichier /etc/fstab.

    Vous pouvez désormais vérifier si le système de fichiers est monté correctement en le démontant puis en le remontant, par exemple en utilisant l’exemple de point de montage `/datadrive` créé lors des étapes précédentes :

        `sudo umount /datadrive`
        `sudo mount /datadrive`

    Si la deuxième commande génère une erreur, vérifiez que la syntaxe utilisée dans le fichier /etc/fstab est correcte.

    > [WACOM.NOTE] La suppression ultérieure d'un disque de données sans modifier fstab pourrait entraîner l'échec du démarrage de la machine virtuelle. S’il s’agit d’une occurrence courante, la plupart des distributions proposent les options fstab `nofail` et/ou `nobootwait` qui permettent à un système de démarrer même si le disque n’est pas présent. Pour plus d’informations sur ces paramètres, consultez la documentation de votre distribution.

  [Téléchargement PuTTY]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [OpenSSH]: http://www.openssh.org/
  [Rechercher les détails SSH]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/SSHdetails.png
  [Entrer le nom d’hôte et les informations sur le port]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/putty.png
  [Se connecter à la nouvelle machine virtuelle]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/sshlogin.png
  [guide d’utilisation de l’agent Linux Azure]: http://www.windowsazure.com/fr-fr/manage/linux/how-to-guides/linux-agent-guide/
  [À propos des disques et des images dans Azure]: http://msdn.microsoft.com/fr-fr/library/jj672979.aspx
  [Définir les détails du disque]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/attachnewdisklinux.png
  [Disque correctement attaché]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/attachemptysuccess.png
  [Identifier le disque]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskmessages.png
  [Créer un appareil]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskpartition.png
  [Créer une partition]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskcylinder.png
  [Répertorier les informations sur le disque]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskinfo.png
  [Écrire les modifications apportées au disque]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskwrite.png
  [Créer le système de fichiers]: ./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskfilesystem.png
