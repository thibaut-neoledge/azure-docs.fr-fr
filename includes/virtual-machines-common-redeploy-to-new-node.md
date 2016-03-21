
Si vous avez été confronté à des difficultés lors de la résolution de problèmes de connexion RDP (bureau à distance) à une machine virtuelle Azure basée sur Windows ou de résolution des problèmes de connexion SSH à une machine virtuelle Azure basée sur Linux, alors cet article vous aidera à les résoudre vous-même, sans itération du support, ni redimensionnement de la machine virtuelle. Microsoft Azure se charge de redéployer votre machine virtuelle lorsque vous appelez l’opération de redéploiement via Azure PowerShell.

Veuillez noter qu’une fois cette opération terminée, les données de disque éphémère seront perdues et les adresses IP dynamiques liées à la machine virtuelle seront mises à jour.


## Utilisation de Microsoft Azure PowerShell

Assurez-vous que vous avez installé la dernière version d’Azure PowerShell 1.x. Pour plus d’informations, consultez la section [Installer et configurer Azure PowerShell](../articles/powershell-install-configure.md).

Utilisez cette commande Azure PowerShell pour redéployer votre machine virtuelle :

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


Pendant l’exécution de cette commande, vérifiez votre machine virtuelle dans le [portail Azure](https://portal.azure.com). Notez que l’**état** de la machine virtuelle change comme suit :

1. L’**état** initial est *En cours d’exécution*

	![État initial de redéploiement](./media/virtual-machines-common-redeploy-to-new-node/statusrunning1.png)

2. L’**état** devient *Mise à jour en cours*

	![État du redéploiement - Mise à jour en cours](./media/virtual-machines-common-redeploy-to-new-node/statusupdating.png)

3. L’**état** devient *Démarrage en cours*

	![État du redéploiement - Démarrage en cours](./media/virtual-machines-common-redeploy-to-new-node/statusstarting.png)

4. L’**état** redevient *En cours d’exécution*

	![État final de redéploiement](./media/virtual-machines-common-redeploy-to-new-node/statusrunning2.png)

Lorsque l’**état** redevient *En cours d’exécution*, la machine virtuelle a été redéployée.

<!------HONumber=AcomDC_0309_2016-->