
Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage. Si vous souhaitez réutiliser les données du disque, vous pouvez l’attacher à la même machine virtuelle ou à une autre.

> [AZURE.NOTE]Dans Azure, une machine virtuelle utilise différents types de disque, comme le disque du système d’exploitation, un disque temporaire local et des disques de données facultatifs. Les disques de données sont le mode de stockage recommandé pour les données des machines virtuelles. Pour en savoir plus, consultez la section [À propos des disques et VHD pour machines virtuelles](../../virtual-machines-disks-vhds.md). Il n’est pas possible de détacher un disque de système d’exploitation, sauf si vous supprimez également la machine virtuelle.

## Recherche du disque##

Si vous ne connaissez pas le nom du disque ou souhaitez le vérifier avant de le détacher, procédez comme suit.

> [AZURE.NOTE]Azure attribue automatiquement un nom au disque lorsque vous l’attachez. Il est généré à partir du nom du service cloud, du nom de la machine virtuelle et d’un numéro.

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](http://manage.windowsazure.com).

2. Cliquez sur **Machines virtuelles**, sur le nom de la machine virtuelle, puis sur **Tableau de bord**.

3. Sous **Disques**, le nom et le type de tous les disques attachés sont répertoriés. Par exemple, cet écran affiche une machine virtuelle avec un disque de système d’exploitation et un disque de données :

	![Rechercher un disque de données](./media/howto-detach-disk-windows-linux/FindDataDisks.png)


## Détachement du disque##

Après avoir trouvé le nom du disque, vous êtes prêt à le détacher :

1. Cliquez sur **Machines virtuelles**, sur le nom de la machine virtuelle qui possède le disque de données que vous souhaitez détacher, puis sur **Tableau de bord**.
2. Dans la barre de commandes, cliquez sur **Détacher le disque**.

3. Sélectionnez le disque de données, puis cliquez sur la coche pour le détacher.

	![Détails concernant le disque à détacher](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

Le disque reste dans le stockage, mais il n’est plus attaché à une machine virtuelle.

<!---HONumber=August15_HO6-->