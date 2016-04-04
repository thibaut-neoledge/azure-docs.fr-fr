

Cet article explique comment attacher des disques nouveaux et existants à une machine virtuelle via le portail Azure. Avant cela, passez en revue les conseils suivants :

- La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).
- Pour utiliser le stockage de Premium, vous avez besoin d’une machine virtuelle de série DS ou GS. Vous pouvez utiliser des disques de comptes de stockage Premium et Standard avec ces machines virtuelles. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage-preview-portal.md).
- Les disques attachés aux machines virtuelles sont en fait des fichiers .vhd dans un compte de stockage Azure. Pour en savoir plus, voir la section [À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md).
- Pour un nouveau disque, vous n’avez pas besoin de le créer au préalable, car Azure le crée lorsque vous l’attachez.
- Pour un disque existant, le fichier .vhd doit être disponible dans un compte de stockage Azure. Vous pouvez utiliser un fichier .vhd présent, s’il n’est attaché à aucune autre machine virtuelle, ou charger votre propre fichier .vhd sur le compte de stockage.

## Recherchez la machine virtuelle.

1. Connectez-vous au portail Azure.

2. Dans le menu Hub, cliquez sur **Machines virtuelles**.

3.	Sélectionnez la machine virtuelle dans la liste.

4. À droite, sous **Essentials**, cliquez sur **Tous les paramètres**, puis sur **Disques**.

	![Ouverture des paramètres d’un disque](./media/virtual-machines-common-attach-disk-portal/find-disk-settings.png)

Continuez en suivant les instructions pour attacher un disque nouveau ou existant.

## Option 1 : attacher un nouveau disque

1.	Dans le panneau **Disques**, cliquez sur **Attacher un nouveau disque**.

2.	Passez en revue les paramètres par défaut, mettez-les à jour en fonction des besoins, puis cliquez sur **OK**.

 	![Examen des paramètres d’un disque](./media/virtual-machines-common-attach-disk-portal/attach-new.png)

3.	Après qu’Azure a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

## Option 2 : attacher un disque existant

1.	Dans le panneau **Disques**, cliquez sur **Attacher un disque existant**.

2.	Sous **Attacher un disque existant**, cliquez sur **Fichier VHD**.

	![Attachement d’un disque existant](./media/virtual-machines-common-attach-disk-portal/attach-existing.png)

3.	Sous **Comptes de stockage**, sélectionnez le compte et le conteneur dans lequel figure le fichier .vhd.

	![Recherche d’emplacement VHD](./media/virtual-machines-common-attach-disk-portal/find-storage-container.png)

4.	Sélectionnez le fichier .vhd.

5.	Sous **Attacher un disque existant**, le fichier que vous venez de sélectionner est répertorié sous **Fichier VHD**. Cliquez sur **OK**.

6.	Après qu’Azure a attaché le disque à la machine virtuelle, le disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

<!---HONumber=AcomDC_0323_2016-->