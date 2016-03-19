<properties writer="kathydav" editor="tysonn" manager="timlt" />

Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage.

Si vous souhaitez réutiliser les données du disque, vous pouvez l’attacher à la même machine virtuelle ou à une autre.

> [AZURE.NOTE] Il n’est pas possible de détacher un disque de système d’exploitation, sauf si vous supprimez également la machine virtuelle.


## Recherche du disque

Si vous ne connaissez pas le nom du disque ou souhaitez le vérifier avant de le détacher, procédez comme suit.


1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com).

2. Cliquez sur **Machines virtuelles**, sur le nom de la machine virtuelle, puis sur **Tableau de bord**.

3. Sous **Disques**, le nom et le type de tous les disques attachés sont répertoriés. Par exemple, cet écran affiche une machine virtuelle avec un disque de système d’exploitation et un disque de données :

	![Rechercher un disque de données](./media/howto-detach-disk-windows-linux/FindDataDisks.png)


## Détachement du disque

1. Cliquez sur **Machines virtuelles**, sur le nom de la machine virtuelle qui possède le disque de données que vous souhaitez détacher, puis sur **Tableau de bord**.

2. Dans la barre de commandes, cliquez sur **Détacher le disque**.

3. Sélectionnez le disque de données, puis cliquez sur la coche pour le détacher.

	![Détails concernant le disque à détacher](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

Le disque reste dans le stockage, mais il n’est plus attaché à une machine virtuelle.

<!---HONumber=AcomDC_0211_2016-->