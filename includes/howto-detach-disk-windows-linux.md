<properties writer="kathydav" editor="tysonn" manager="timlt" />



#Détachement d'un disque de données d'une machine virtuelle 

- [Étape 1 : recherche du disque](#finddisks)
- [Étape 2 : Détachement d'un disque de données](#detachdisk)

Lorsque vous n'avez plus besoin d'un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage. Si vous souhaitez réutiliser les données du disque, vous pouvez l'attacher à la même machine virtuelle ou à une autre.  

> [AZURE.NOTE] Une machine virtuelle dans Azure utilise différents types de disque : un disque de système d'exploitation, un disque local temporaire et des disques de données facultatifs. Les disques de données sont le mode de stockage recommandé pour les données des machines virtuelles. Pour plus d'informations au sujet des disques, consultez la rubrique [À propos des disques et des images] []. Pour obtenir des instructions sur l'association des disques, consultez la page [Association d'un disque de données à une machine virtuelle Windows][attachdisk].

## <a id="finddisks"> </a>Étape 1 : recherche du disque##


Si vous ne connaissez pas le nom du disque ou souhaitez le vérifier avant de le détacher, procédez comme suit. 

> [AZURE.NOTE] Azure attribue automatiquement un nom au disque lorsque vous l'attachez. Il est généré à partir du nom du service cloud, du nom de la machine virtuelle et d'un numéro.

1. Si vous ne l'avez pas déjà fait, connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com). 

2. Cliquez sur **Machines virtuelles**, cliquez sur le nom de la machine virtuelle, puis cliquez sur **Tableau de bord**.

3. Sous **Disques**, le nom et le type de tous les disques attachés sont répertoriés. Par exemple, cet écran affiche une machine virtuelle avec un disque de système d'exploitation et un disque de données :
		
	![Find data disk](./media/howto-detach-disk-windows-linux/FindDataDisks.png)	


## <a id="detachdisk"> </a>Étape 2 : détachement du disque##

Après avoir trouvé le nom du disque, vous êtes prêt à le détacher :

1. Cliquez sur **Machines virtuelles**, cliquez sur le nom de la machine virtuelle qui possède le disque de données que vous souhaitez détacher, puis cliquez sur **Tableau de bord**.
2. Dans la barre de commandes, cliquez sur **Détacher le disque**.

3. Sélectionnez le disque de données, puis cliquez sur la coche pour le détacher.


	![Detach disk details](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

Le disque reste dans le stockage, mais il n'est plus attaché à une machine virtuelle.



[attachdisk]:/fr-fr/manage/windows/how-to-guides/attach-a-disk/

[À propos des disques et des images]:http://go.microsoft.com/fwlink/p/?LinkId=263439

<!--HONumber=45--> 
