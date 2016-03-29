Vous devez créer un point de terminaison avec équilibrage de charge pour chaque machine virtuelle qui héberge un réplica Azure. Si vous avez des réplicas dans plusieurs régions, chaque réplica de cette région doit se trouver dans le même service cloud sur le même réseau virtuel. La création de réplicas de groupe de disponibilité couvrant plusieurs régions Azure nécessite de configurer plusieurs réseaux virtuels. Pour plus d'informations sur la configuration de la connectivité de réseau virtuel intersites, consultez [Configuration de la connectivité de réseau virtuel à réseau virtuel](../articles/vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Dans le portail Azure, accédez à chaque machine virtuelle hébergeant un réplica et consultez les détails.

1. Cliquez sur l'onglet **Points de terminaison** de chacune des machines virtuelles.

1. Vérifiez que le **Nom** et le **Public Port** du point de terminaison de l'écouteur ne sont pas déjà utilisés. Dans l'exemple ci-dessous, le nom est « MyEndpoint » et le port est « 1433 ».

1. Sur votre client local, téléchargez et installez [le dernier module PowerShell](https://azure.microsoft.com/downloads/).

1. Lancez **Azure PowerShell**. Une nouvelle session PowerShell s'ouvre avec les modules d'administration Azure chargés.

1. Exécutez **Get-AzurePublishSettingsFile**. Cette applet de commande vous dirige vers un navigateur de façon à télécharger un fichier de paramètres de publication dans un répertoire local. Vous serez peut-être invité à entrer les informations d'identification de connexion de votre abonnement Azure.

1. Exécutez la commande **Import-AzurePublishSettingsFile** avec le chemin d'accès du fichier de paramètres de publication que vous avez téléchargé :

		Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

	Une fois le fichier de paramètres de publication importé, vous pouvez gérer votre abonnement Azure dans la session PowerShell.
<!---HONumber=AcomDC_0128_2016-->