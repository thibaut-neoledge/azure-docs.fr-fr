




## Tableau des images couramment utilisées


| PublisherName | Offer | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7\.1 |
| CoreOS | CoreOS | Bêta |
| CoreOS | CoreOS | Stable |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Standard |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Entreprise |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Entreprise, optimisé pour l’entrepôt de données |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Entreprise, optimisé pour le traitement transactionnel en ligne |
| Canonical | UbuntuServer | 12\.04.5-LTS |
| Canonical | UbuntuServer | 14\.04.2-LTS |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer | WindowsServer | Windows Server Technical Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


## Interface de ligne de commande Azure

> [AZURE.NOTE] Cet article explique comment parcourir et sélectionner des images de machine virtuelle, à l’aide d’une installation récente d’Azure PowerShell ou de l’interface de ligne de commande Azure. Vous devez au préalable passer en mode Resource Manager. Avec l'interface CLI Azure, entrez ce mode en tapant `azure config mode arm`.

La façon la plus simple et la plus rapide de rechercher une image à utiliser avec `azure vm quick-create` ou de créer un fichier de modèle de groupe de ressources consiste à appeler la commande `azure vm image list` et de passer l'emplacement, le nom de l'éditeur (qui ne respecte pas la casse) et une offre (si vous connaissez l'offre). La liste suivante en est un petit exemple (de nombreuses listes sont assez longues) si vous savez que « Canonical » est un éditeur pour l'offre « UbuntuServer ».

    azure vm image list westus canonical ubuntuserver
    info:    Executing command vm image list
    warn:    The parameter --sku if specified will be ignored
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  12.04-DAILY  12.04.201504201  westus    canonical:ubuntuserver:12.04-DAILY:12.04.201504201
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201302250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201302250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201303250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201303250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201304150  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201304150
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305160  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305160
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305270  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305270
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306030  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306030
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306240  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306240

La colonne **Urn** aura le même format que celui que vous passez à `azure vm quick-create`.

Toutefois, il est fréquent que vous ignoriez encore ce qui est disponible. Dans ce cas, vous pouvez parcourir les images en recherchant d’abord les éditeurs à l’aide de la commande `azure vm image list-publishers` et en répondant à l’invite de localisation avec l’emplacement de centre de données que vous envisagez d’utiliser pour votre groupe de ressources. Par exemple, la liste suivante répertorie tous les éditeurs d'images présents dans l'emplacement « West US » (passez l'argument location en utilisant des minuscules et en supprimant les espaces des emplacements standard).

    azure vm image list-publishers
    info:    Executing command vm image list-publishers
    Location: westus
    + Getting virtual machine image publishers (Location: "westus")
    data:    Publisher                                       Location
    data:    ----------------------------------------------  --------
    data:    a10networks                                     westus  
    data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
    data:    alertlogic                                      westus  
    data:    AlertLogic.Extension                            westus  


Ces listes peuvent être assez longues. C’est pourquoi l’exemple de liste ci-dessus est simplement un extrait de code. Supposons que vous ayez remarqué que « Canonical » est, en fait, un éditeur d'image présent dans l'emplacement « West US ». Vous pouvez maintenant rechercher ses offres en appelant `azure vm image list-offers` et en indiquant l’emplacement et l’éditeur aux invites, comme dans l’exemple suivant :

    azure vm image list-offers
    info:    Executing command vm image list-offers
    Location: westus
    Publisher: canonical
    + Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
    data:    Publisher  Offer         Location
    data:    ---------  ------------  --------
    data:    canonical  UbuntuServer  westus  
    info:    vm image list-offers command OK

Nous savons maintenant que dans la région « West US », « Canonical »publie l'offre **UbuntuServer** sur Azure. Mais quelles sont les références SKU ? Pour les obtenir, vous appelez `azure vm image list-skus` et répondez à l’invite en indiquant l’emplacement, l’éditeur et l’offre que vous avez détectée.

    azure vm image list-skus
    info:    Executing command vm image list-skus
    Location: westus
    Publisher: canonical
    Offer: ubuntuserver
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         sku          Location
    data:    ---------  ------------  -----------  --------
    data:    canonical  ubuntuserver  12.04-DAILY  westus  
    data:    canonical  ubuntuserver  12.04.2-LTS  westus  
    data:    canonical  ubuntuserver  12.04.3-LTS  westus  
    data:    canonical  ubuntuserver  12.04.4-LTS  westus  
    data:    canonical  ubuntuserver  12.04.5-LTS  westus  
    data:    canonical  ubuntuserver  12.10        westus  
    data:    canonical  ubuntuserver  14.04-beta   westus  
    data:    canonical  ubuntuserver  14.04-DAILY  westus  
    data:    canonical  ubuntuserver  14.04.0-LTS  westus  
    data:    canonical  ubuntuserver  14.04.1-LTS  westus  
    data:    canonical  ubuntuserver  14.04.2-LTS  westus  
    data:    canonical  ubuntuserver  14.10        westus  
    data:    canonical  ubuntuserver  14.10-beta   westus  
    data:    canonical  ubuntuserver  14.10-DAILY  westus  
    data:    canonical  ubuntuserver  15.04        westus  
    data:    canonical  ubuntuserver  15.04-beta   westus  
    data:    canonical  ubuntuserver  15.04-DAILY  westus  
    info:    vm image list-skus command OK

Avec ces informations, vous pouvez maintenant trouver exactement l’image souhaitée en effectuant l’appel initial affiché en haut.

    azure vm image list westus canonical ubuntuserver 14.04.2-LTS
    info:    Executing command vm image list
    + Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "14.04.2-LTS" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201503090  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201503090
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.20150422   westus    canonical:ubuntuserver:14.04.2-LTS:14.04.20150422
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201504270  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201504270
    info:    vm image list command OK

Vous pouvez maintenant choisir précisément l'image que vous voulez utiliser. Pour créer rapidement une machine virtuelle en utilisant les informations d’URN que vous venez de trouver, ou pour utiliser un modèle avec ces informations d’URN, consultez [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management](xplat-cli-azure-resource-manager.md).

### Vidéo de procédure pas à pas

Cette vidéo illustre les étapes ci-dessus à l'aide de l'interface de ligne de commande

[AZURE.VIDEO virtual-machines-linux-cli-ps-findimage-cli]


## PowerShell

Avec PowerShell, tapez `Switch-AzureMode AzureResourceManager`. Consultez les rubriques [Utiliser l’interface de ligne de commande Azure avec Resource Manager](xplat-cli-azure-resource-manager.md) et [Utiliser Azure PowerShell avec Resource Manager](../powershell-azure-resource-manager.md) pour obtenir des informations complètes sur la mise à jour et la configuration.

> [AZURE.NOTE] L’applet de commande `Switch-AzureMode` a été supprimée dans les modules Azure PowerShell supérieurs à 1.0. Avec cette version et les plus récentes, remplacez les commandes ci-dessous par la portion `Azure` partie remplacée par `AzureRm`. Si vous utilisez les modules Azure PowerShell antérieurs à 1.0, utilisez les commandes ci-dessous. Cependant, vous devez d’abord `Switch-AzureMode AzureResourceManager`.


Lorsque vous créez une machine virtuelle avec le gestionnaire des ressources Azure, vous devez, dans certains cas, spécifier une image en combinant les propriétés d'image suivantes :

- Publisher
- Offer
- SKU

Par exemple, ces valeurs sont nécessaires pour l'applet de commande PowerShell **Set-AzureVMSourceImage** ou avec un fichier de modèle de groupe de ressources dans lequel vous devez spécifier le type de machine virtuelle à créer.

Si vous devez déterminer ces valeurs, vous pouvez parcourir les images à cette fin en procédant ainsi :

1. en répertoriant les éditeurs d’images ;
2. pour un éditeur donné, en répertoriant ses offres ;
3. pour une offre donnée, en répertoriant ses références SKU.

Pour ce faire dans PowerShell, commencez par passer en mode Gestionnaire des ressources d'Azure PowerShell.

	Switch-AzureMode AzureResourceManager

Pour la première étape ci-dessus, répertoriez les éditeurs à l'aide des commandes suivantes.

	$locName="<Azure location, such as West US>"
	Get-AzureVMImagePublisher -Location $locName | Select PublisherName

Indiquez le nom de l'éditeur choisi et exécutez les commandes suivantes.

	$pubName="<publisher>"
	Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Indiquez le nom de l'offre choisie et exécutez les commandes suivantes.

	$offerName="<offer>"
	Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

L'affichage de la commande **Get-AzureVMImageSku** donne toutes les informations dont vous avez besoin pour spécifier l'image souhaitée pour une nouvelle machine virtuelle.

Voici un exemple.

	PS C:\> $locName="West US"
	PS C:\> Get-AzureVMImagePublisher -Location $locName | Select PublisherName

	PublisherName
	-------------
	a10networks
	aiscaler-cache-control-ddos-and-url-rewriting-
	alertlogic
	AlertLogic.Extension
	Barracuda.Azure.ConnectivityAgent
	barracudanetworks
	basho
	boxless
	bssw
	Canonical
	...

Pour l'éditeur « MicrosoftWindowsServer » :

	PS C:\> $pubName="MicrosoftWindowsServer"
	PS C:\> Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

	Offer
	-----
	WindowsServer

Pour l'offre « WindowsServer » :

	PS C:\> $offerName="WindowsServer"
	PS C:\> Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

	Skus
	----
	2008-R2-SP1
	2012-Datacenter
	2012-R2-Datacenter
	Windows-Server-Technical-Preview

Dans cette liste, copiez le nom de la référence SKU choisie. Vous avez alors toutes les informations pour l’applet de commande PowerShell **Set-AzureVMSourceImage** ou pour un fichier de modèle de groupe de ressources pour lequel vous devez spécifier l’éditeur, l’offre et la référence SKU pour une image.

### Vidéo de procédure pas à pas

Cette vidéo illustre les étapes ci-dessus à l'aide de PowerShell.

[AZURE.VIDEO virtual-machines-linux-cli-ps-findimage-posh]


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_0323_2016-->