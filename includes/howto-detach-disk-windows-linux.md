<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />
# Détachement d'un disque de données d'une machine virtuelle

#

* [Concepts](#concepts)
* [Recherche des disques attachés à une machine virtuelle](#finddisks)
* [Détachement d'un disque de données](#detachdisk)
## <a  id="concepts"> </a>Concepts

Dans Azure, une machine virtuelle utilise différents types de disques,
comme le disque du système d'exploitation, un disque temporaire local et
des disques de données facultatifs. Vous pouvez attacher un disque de
données à une machine virtuelle pour y stocker des données
d'applications. Un disque de données est un disque dur virtuel que vous
pouvez créer localement sur votre ordinateur ou dans le cloud avec
Azure.

Vous pouvez attacher et détacher des disques de données quand vous le
souhaitez, mais le nombre de disques que vous pouvez attacher à une
machine virtuelle est limité en fonction de la taille de celle-ci.

Lorsque vous n'avez plus besoin d'un disque de données qui est attaché à
une machine virtuelle, vous pouvez le détacher facilement. Ce processus
ne supprime pas le disque du stockage. Si vous souhaitez de nouveau
utiliser les données existantes sur le disque, vous pouvez facilement
rattacher le disque à la même machine virtuelle ou à une autre.

Pour plus d'informations sur l'utilisation des disques de données,
consultez la page [Gestion des disques et des images][1].
## <a  id="finddisks"> </a>Recherche des disques attachés à une machine virtuelle

Vous pouvez rechercher les disques attachés à une machine virtuelle à
l'aide du tableau de bord ou de la page Disques des machines virtuelles.
### Utilisation du tableau de bord pour rechercher des informations concernant les disques attachés

1.  Si ce n'est pas déjà fait, connectez-vous au [portail de gestion][2]
    Azure.

2.  Cliquez sur **Virtual Machines**, puis sélectionnez la machine
    virtuelle appropriée.

3.  Cliquez sur **Dashboard**. Dans le tableau de bord de la machine
    virtuelle, vous pouvez rechercher le nombre de disques attachés et
    leur nom. L'exemple suivant présente un seul disque de données
    attaché à une machine virtuelle :
    
    ![Rechercher un disque de
    données](./media/howto-detach-disk-windows-linux/FindDataDisks.png)

**Remarque :** au moins un disque de données est attaché à toutes les
machines virtuelles. Chaque machine virtuelle dispose d'un disque de
système d'exploitation attaché, que vous ne pouvez pas détacher sans
supprimer la machine virtuelle. Le disque temporaire local n'est pas
répertorié dans la section des disques, car il n'est pas persistant.
### Utilisation de la page Disques des machines virtuelles pour rechercher des informations concernant les disques attachés

1.  Si ce n'est pas déjà fait, connectez-vous au [portail de gestion][2]
    Azure.

2.  Cliquez sur **Virtual Machines**, puis sur **Disks**. Cette page
    affiche la liste de tous les disques disponibles pouvant être
    utilisés avec les machines virtuelles, ainsi que ceux déjà utilisés.
    La liste est une combinaison de disques de système d'exploitation et
    de disques de données. Pour différencier les deux types de disques
    attachés à la machine virtuelle, utilisez le tableau de bord.
    
    **Remarque :** lorsque vous attachez un nouveau disque de données à
    une machine virtuelle, vous pouvez attribuer un nom au fichier .vhd
    utilisé pour le disque. Quant au nom du disque, il est attribué par
    Azure. Il est généré à partir du nom du service cloud, du nom de la
    machine virtuelle et d'un identificateur numérique
## <a  id="detachdisk"> </a>Détachement d'un disque de données

Après avoir trouvé le nom du disque à détacher, vous pouvez effectuer la
procédure suivante pour détacher le disque de la machine virtuelle.

1.  Si ce n'est pas déjà fait, connectez-vous au portail de gestion
    Azure.

2.  Cliquez sur **Virtual Machines**, sélectionnez la machine virtuelle
    possédant le disque de données à détacher, puis cliquez sur **Detach
    The Disk**.

3.  Sélectionnez le disque de données, puis cliquez sur la coche pour le
    détacher.
    
    ![Détails concernant le disque à
    détacher](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)
    
    Le disque reste dans le stockage, mais il n'est plus attaché à une
    machine virtuelle.

Vous pouvez maintenant rattacher le disque à la même machine virtuelle
ou à une autre. Pour plus d'informations, consultez la rubrique
[Association d'un disque de données avec une machine
virtuelle](/en-us/manage/windows/how-to-guides/attach-a-disk/).



[1]: http://go.microsoft.com/fwlink/p/?LinkId=263439
[2]: http://manage.windowsazure.com
