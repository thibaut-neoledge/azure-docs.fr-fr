> Vous ne pouvez pas utiliser un réseau Azure Virtual Network v1 (classique) avec HDInsight sur Linux. Le réseau virtuel doit être v2 (Azure Resource Manager), pour être affiché en tant qu’option pendant le processus de création du cluster HDInsight dans le portail Azure en version préliminaire, ou pour être utilisable pendant la création d’un cluster à partir de l’interface de ligne de commande Azure ou d’Azure PowerShell.
> 
> Si vous disposez de ressources sur un réseau v1 et que vous souhaitez rendre HDInsight directement accessible à ces ressources via un réseau virtuel, voir [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../articles/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) pour en savoir plus sur la connexion d’un réseau virtuel v2 à un réseau virtuel v1. Une fois cette connexion établie, vous pouvez créer le cluster HDInsight dans le réseau virtuel v2.
> 
> 



<!--HONumber=Jan17_HO3-->


