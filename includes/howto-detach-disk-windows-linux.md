<properties writer="kathydav" editor="tysonn" manager="timlt" />



#Détachement d'un disque de données d'une machine virtuelle 

- [Étape 1 : recherche du disque](#finddisks)
- [Étape 2 : Détachement d'un disque de données](#detachdisk)

Lorsque vous n'avez plus besoin d'un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage. Si vous souhaitez réutiliser les données du disque, vous pouvez l'attacher à la même machine virtuelle ou à une autre.  

> [WACOM.NOTE] Dans Azure, une machine virtuelle utilise différents types de disque, comme le disque du système d'exploitation, un disque temporaire local et des disques de données facultatifs. Les disques de données sont le mode de stockage recommandé pour les données des machines virtuelles. Pour plus d'informations sur les disques, consultez [À propos des disques et des images] []. Pour obtenir des instructions, consultez [Comment attacher un disque de données à une machine virtuelle] [attachdisk].

## <a id="finddisks"> </a>Étape 1 : recherche du disque


Procédez comme suit si vous ne savez pas comment vérifier le nom du disque avant de le détacher ou si vous ne souhaitez pas effectuer cette vérification. 

> [WACOM.NOTE] Azure attribue automatiquement un nom au disque lorsque vous l'attachez. Il est généré à partir du nom du service cloud, du nom de la machine virtuelle et d'un numéro.

1. Si ce n'est pas déjà fait, connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com). 

2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée. Le tableau de bord de la machine virtuelle s'affiche.

3. Sous **Disques**, le tableau indique le nom et le type de tous les disques attachés. Par exemple, cet écran affiche une machine virtuelle avec un disque de système d'exploitation et un disque de données :
		
	![Find data disk](./media/howto-detach-disk-windows-linux/FindDataDisks.png)	


## <a id="detachdisk"> </a>Étape 2 : détachement du disque

Une fois le nom du disque trouvé, vous pouvez détacher le disque :

1. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle dotée du du disque de données à détacher.
2. Dans la barre de commandes, cliquez sur **Détacher le disque**.

2. Sélectionnez le disque de données, puis cliquez sur la coche pour le détacher.


	![Detach disk details](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

Le disque reste dans le stockage, mais il n'est plus attaché à une machine virtuelle.



[attachdisk]:/fr-fr/manage/windows/how-to-guides/attach-a-disk/

[À propos des disques et des images] :http://go.microsoft.com/fwlink/p/?LinkId=263439


<!--HONumber=35_1-->
