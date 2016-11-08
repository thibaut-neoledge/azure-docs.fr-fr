> Vous ne pouvez pas utiliser un réseau Azure Virtual Network v1 (classique) avec HDInsight sur Linux. Le réseau virtuel doit être v2 (Azure Resource Manager), pour être affiché en tant qu’option pendant le processus de création du cluster HDInsight dans le portail Azure en version préliminaire, ou pour être utilisable pendant la création d’un cluster à partir de l’interface de ligne de commande Azure ou d’Azure PowerShell.
> 
> Si vous disposez de ressources sur un réseau v1 et que vous souhaitez rendre HDInsight directement accessible à ces ressources via un réseau virtuel, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../virtual-network/virtual-networks-arm-asm-s2s.md) pour plus d'informations sur la connexion d'un réseau virtuel v2 à un réseau virtuel v1. Une fois cette connexion établie, vous pouvez créer le cluster HDInsight dans le réseau virtuel v2.
> 
> 

<!---HONumber=Oct15_HO3-->