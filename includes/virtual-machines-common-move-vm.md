
Cet article vous guide tout au long du déplacement d’une machine virtuelle entre des abonnements. Cela peut être pratique si, à l’origine, vous aviez créé une machine virtuelle dans un abonnement personnel, et que vous souhaitez à présent la déplacer vers l’abonnement de votre entreprise afin de poursuivre votre travail.

> [AZURE.NOTE] De nouveaux ID de ressource seront créés dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et vos scripts pour utiliser les nouveaux ID de ressource.

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Cliquez sur **Parcourir** > **Machines virtuelles** et sélectionnez la machine virtuelle que vous souhaitez déplacer dans la liste.
	
	![Capture d’écran de la section Essentials où vous cliquez sur l’icône en forme de crayon pour ouvrir le panneau Déplacer des ressources.](./media/virtual-machines-common-move-vm/move-button.png)
	
3. Dans la section **Essentials**, cliquez sur l’icône en forme de crayon **Modifier l’abonnement** en regard du nom de l’abonnement. Le panneau **Déplacer des ressources** s’ouvre alors.
	
	![Capture d’écran du panneau Déplacer des ressources.](./media/virtual-machines-common-move-vm/move.png)
	
4. Sélectionnez chacune des ressources à déplacer. Dans la plupart des cas, vous devez déplacer toutes les ressources facultatives répertoriées.
5. Sélectionnez **l’abonnement** vers lequel vous souhaitez déplacer la machine virtuelle.
6. Sélectionnez un **groupe de ressources** existant, ou tapez un nom pour créer un groupe de ressources.
7. Lorsque vous avez terminé, sélectionnez que vous comprenez que de nouveaux ID de ressources vont être créés et qu’ils doivent être utilisés avec la machine virtuelle une fois que celle-ci a été déplacée, puis cliquez sur **OK**.



## Étapes suivantes

Vous pouvez déplacer de nombreux types de ressources différents entre les groupes de ressources et les abonnements. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](../articles/resource-group-move-resources.md).

<!---HONumber=AcomDC_0706_2016-->