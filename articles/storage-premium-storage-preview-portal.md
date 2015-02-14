<properties 
	pageTitle="Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure" 
	description="En savoir plus sur Azure Premium Storage pour les disques. Création d'un compte Premium Storage." 
	services="storage" 
	documentationCenter="" 
	authors="Selcin" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="selcint"/>


# Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure

Bienvenue dans la version préliminaire publique du **stockage Azure Premium pour disques** !

Avec l'introduction du nouveau stockage Premium, Microsoft Azure offre désormais deux types de stockage durable : **le stockage Premium** et le **stockage Standard**. Le stockage Premium stocke les données sur des disques SSD bénéficiant de la dernière technologie, tandis que le stockage Standard stocke les données sur des disques durs. 

Le stockage Premium offre une prise en charge très performante et à faible latence des disques pour les charges de travail intensives d'E/S exécutées sur les machines virtuelles Azure. Vous pouvez attacher plusieurs disques de stockage Premium à une machine virtuelle. Avec le stockage Premium, vos applications peuvent utiliser jusqu'à 32 To de stockage et atteindre 50 000 IOPS (opérations d'E/S par seconde) par machine virtuelle, avec une latence extrêmement faible pour les opérations de lecture. Le stockage Premium est actuellement disponible uniquement pour stocker des données sur les disques utilisés par les machines virtuelles Azure. 

Pour vous inscrire à la version préliminaire du stockage Azure Premium, consultez la page [Azure Preview](http://azure.microsoft.com/services/preview/).

Cet article offre une présentation détaillée du stockage Azure Premium.

**Sommaire :**


* [Considérations sur le stockage Premium](#important)

* [Utilisation du stockage Premium pour disques](#using)

* [Objectifs d'extensibilité et de performances avec le stockage Premium](#scale)	

* [Limitation du stockage Premium](#throttling)	

* [Captures instantanées et copie d'objets blob avec le stockage Premium](#restapi)	

* [Tarification et facturation du stockage Premium](#pricing)	

* [Création et utilisation du compte de stockage Premium pour disques](#howto1)	

* [Ressources supplémentaires](#see)

###<a id="important">Considérations sur le stockage Premium</a>


Voici une liste des points importants à prendre en compte avant ou pendant l'utilisation du stockage Premium :

- Pour utiliser le stockage Premium, vous devez disposer d'un compte de stockage Premium. Pour savoir comment créer un compte de stockage Premium, consultez [Création et utilisation d'un compte de stockage Premium pour disques](#howto1).

- Le stockage Premium est actuellement disponible dans le [portail Microsoft Azure Preview](https://portal.azure.com/) et accessible via les bibliothèques SDK suivantes : [API REST de stockage](http://msdn.microsoft.com//library/azure/dd179355.aspx) version 2014-02-14 ou ultérieure. [API REST Service Management](http://msdn.microsoft.com/library/azure/ee460799.aspx) version 2014-10-01 ou ultérieure et [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) version 0.8.10 ou ultérieure. 

- Le stockage Premium est disponible en version préliminaire limitée dans les régions suivantes : Ouest des États-Unis, Est des États-Unis 2 et Europe de l'Ouest.

- Le stockage Premium prend uniquement en charge les objets blob de pages Azure, utilisés pour stocker les disques persistants des machines virtuelles Azure. Pour plus d'informations sur les objets blob de pages Azure, consultez [Présentation des objets blob de blocs et des objets blob de pages](http://msdn.microsoft.com/library/azure/ee691964.aspx). Le stockage Premium ne prend pas en charge les objets blob de blocs Azure, les fichiers Azure, les tables Azure ou les files d'attente Azure.

- Un compte de stockage Premium est localement redondant (LRS) et conserve trois copies des données dans une même région.  Concernant la géo-réplication pendant l'utilisation du stockage Premium, consultez la section [Captures instantanées et copie d'objets blob avec le stockage Premium](#restapi) de cet article.

- Si vous souhaitez utiliser un compte de stockage Premium pour vos disques de machine virtuelle, vous devez utiliser des machines virtuelles de série DS. Vous pouvez utiliser des disques de stockage Standard et Premium avec les machines virtuelles de série DS. Toutefois, vous ne pouvez pas utiliser de disques de stockage Premium avec des machines virtuelles qui ne sont pas de série DS. Pour plus d'informations sur les tailles et les types de disque de machine virtuelle Azure disponibles, consultez [Tailles de machine virtuelle et de service cloud pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 

- Le processus de configuration des disques de stockage Premium pour une machine virtuelle est semblable aux disques de stockage Standard. Vous devez choisir l'option de stockage Premium qui convient le mieux à vos disques et votre machine virtuelle Azure. La taille de la machine virtuelle doit être adaptée à votre charge de travail en fonction des caractéristiques de performances de l'offre Premium. Pour plus d'informations, consultez [Objectifs d'extensibilité et de performances avec le stockage Premium](#scale).
 
- Un compte de stockage Premium ne peut pas être mappé à un nom de domaine personnalisé.

- L'analyse de stockage n'est pas prise en charge pour le stockage Premium pour l'instant.

###<a id="using">Utilisation du stockage Premium pour disques</a>
Vous pouvez utiliser le stockage Premium pour disques de deux manières :

- Commencez par créer un nouveau compte de stockage Premium et utilisez-le pour créer la machine virtuelle.
- Créez une machine virtuelle de série DS. Pendant la création de la machine virtuelle, vous pouvez sélectionner un compte de stockage Premium créé précédemment, en créer un ou laisser le portail Azure créer un compte Premium par défaut.

Azure utilise le compte de stockage comme conteneur de votre système d'exploitation et des disques de données. En d'autres termes, si vous créez une machine virtuelle Azure de série DS et sélectionnez un compte de stockage Azure Premium, votre système d'exploitation et les disques de données sont stockés dans ce compte de stockage.

Pour bénéficier des avantages du stockage Premium, créez d'abord un compte de stockage Premium à l'aide du type de compte *Premium_LRS*. Pour ce faire, vous pouvez utiliser le [portail Microsoft Azure Preview](https://portal.azure.com/), [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/), ou l'[API REST de gestion de service](http://msdn.microsoft.com/library/azure/ee460799.aspx). Pour des instructions détaillées, consultez [Création et utilisation du compte de stockage Premium pour disques](#howto1).

<h4>Remarques importantes :</h4>

- pour connaître les limites de la version préliminaire en termes de capacité et de bande passante pour les comptes de stockage Premium, consultez la section [Objectifs d'extensibilité et de performances avec le stockage Premium](#scale) . Si les besoins de votre application dépassent les objectifs d'extensibilité d'un compte de stockage unique, générez votre application pour qu'elle utilise plusieurs comptes de stockage et partitionnez vos données sur ces comptes. Par exemple, si vous souhaitez attacher des disques de 51 To sur un certain nombre de machines virtuelles, répartissez-les sur deux comptes de stockage, la limite d'un compte de stockage Premium étant définie sur 32 To.
- Par défaut, la stratégie de mise en cache est " Lecture seule " pour tous les disques de données Premium et " Lecture-écriture " pour le disque du système d'exploitation Premium attaché à la machine virtuelle. Ce paramètre de configuration est recommandé pour optimiser les performances des E/S de votre application. Pour les disques de données en écriture seule ou avec d'importantes opérations d'écriture (par ex., les fichiers journaux de SQL Server), désactivez la mise en cache du disque pour de meilleures performances de l'application.
- Assurez-vous que la bande passante disponible est suffisante sur votre machine virtuelle pour gérer le trafic du disque. Pour connaître la bande passante disponible du disque pour chaque taille de machine virtuelle, consultez [Tailles de machine virtuelle et de service cloud pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).
- Vous pouvez utiliser des disques de stockage Premium et Standard dans la même machine virtuelle de série DS.
- Avec le stockage Premium, vous pouvez configurer une machine virtuelle de série DS et attacher plusieurs disques de données persistants sur une machine virtuelle. Si nécessaire, vous pouvez répartir les données sur les disques pour augmenter la capacité et les performances du volume. Si vous équilibrez les disques de données de stockage Premium à l'aide des [espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx), vous devez le configurer avec une colonne pour chaque disque utilisé. Dans le cas contraire, les performances globales du volume agrégé par bandes peuvent être limitées, en raison d'une distribution inégale du trafic sur les disques. Par défaut, l'interface utilisateur (IU) du Gestionnaire de serveur vous permet de créer des colonnes jusqu'à 8 disques. Mais si vous avez plus de 8 disques, vous devez utiliser PowerShell pour créer le volume et spécifier manuellement le nombre de colonnes. Dans le cas contraire, l'IU du Gestionnaire de serveur continue d'utiliser 8 colonnes, même si vous disposez de plusieurs disques. Par exemple, si vous disposez de 32 disques dans un agrégat unique, vous devez spécifier 32 colonnes. Vous pouvez utiliser le paramètre *NumberOfColumns* du cmdlet [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell pour spécifier le nombre de colonnes utilisées par le disque virtuel. Pour plus d'informations, consultez [Vue d'ensemble des espaces de stockage](http://technet.microsoft.com/library/jj822938.aspx) et [Forum aux Questions relatif aux espaces de stockage](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).


###<a id="scale">Objectifs d'extensibilité et de performances avec le stockage Premium</a>
Quand vous configurez un disque sur un compte de stockage Premium, le nombre d'opération d'E/S par seconde (IOPS) et le débit (bande passante) qu'il peut recevoir dépend de la taille du disque. Actuellement, il existe trois types de disques de stockage Premium : P10, P20 et P30. Chaque type présente des limites spécifiques en matière d'IOPS et de débit, comme indiqué dans le tableau suivant :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Type de disque de stockage Premium</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>Taille du disque</strong></td>
	<td>128 Go</td>
	<td>512 Go</td>
	<td>1024 Go (1 To)</td>
</tr>
<tr>
	<td><strong>IOPS par disque</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>Débit par disque</strong></td>
	<td>100 Mo par seconde</td>
	<td>150 Mo par seconde</td>
	<td>200 Mo par seconde</td>
</tr>
</tbody>
</table>

Azure mappe la taille du disque (arrondie à la valeur supérieure) à l'option de disque de stockage Premium la plus proche, comme indiqué dans le tableau. Par exemple, un disque de 100 Go correspond à une option P10 et peut prendre en charge jusqu'à 500 IOPS et un débit de 100 Mo par seconde. De la même façon, un disque de 400 Go correspond à une option P20 et peut prendre en charge jusqu'à 2300 IOPS et un débit de 150 Mo par seconde.

La taille d'une unité d'E/S est 256 Ko. Si la taille des données transférées est inférieure à 256 Ko, elles sont considérées comme une seule unité d'E/S. Les tailles d'E/S supérieures sont divisées en plusieurs unités d'E/S de 256 Ko. Par exemple, 1 100 Ko d'E/S correspond à cinq unités d'E/S.

La limite de débit inclut les opérations de lecture et d'écriture sur le disque. Par exemple, la somme des opérations de lecture et d'écriture doit être inférieure ou égale à 100 Mo par seconde pour un disque P10.  De la même façon, un seul disque P10 peut avoir 100 Mo par seconde d'opérations de lecture ou 100 Mo par seconde d'opérations d'écriture ou encore 60 Mo par seconde d'opérations de lecture avec 40 Mo par seconde d'opérations d'écriture, par exemple.
 
Quand vous créez vos disques dans Azure, sélectionnez l'offre de stockage Premium qui convient le mieux aux besoins de votre application en termes de capacité, de performances, d'extensibilité et de charge maximale.

Le tableau suivant décrit les objectifs d'extensibilité des comptes de stockage Premium :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Capacité totale des comptes</strong></td>
	<td><strong>Bande passante totale pour un compte de stockage localement redondant</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Capacité du disque : 32 To</li>
       <li type=round>Capacité de captures instantanées : 10 To</li>
    </ul>
	</td>
	<td>Jusqu'à 50 Go par seconde pour les données entrantes/sortantes</td>
</tr>
</tbody>
</table>

- Les données entrantes sont toutes les données (demandes) envoyées à un compte de stockage.
- Les données sortantes sont toutes les données (réponses) reçues d'un compte de stockage.

Pour plus d'informations, consultez [Objectifs d'extensibilité et de performances d'Azure Storage](http://msdn.microsoft.com/library/dn249410.aspx).


###<a id="throttling">Limitation du stockage Premium</a>
Vous pouvez rencontrer une limitation si les IOPS ou le débit de votre application dépassent les limites allouées à un disque de stockage Premium. Pour éviter la limitation, nous vous recommandons de définir le nombre de demandes d'E/S en attente en fonction des objectifs d'extensibilité et de performances du disque configuré.  

Quand votre application est conçue pour éviter les limitations, elle bénéficie d'une latence moindre. En revanche, si le nombre de demandes d'E/S en attente est trop faible, votre application ne peut pas bénéficier des niveaux maximum de débit et d'IOPS disponibles sur le disque.

Les exemples suivants montrent comment calculer les niveaux de limitation :

<h4>Exemple 1 :</h4>
Votre application a effectué 495 E/S de 16 Ko (soit 495 unités d'E/S) en une seconde sur un disque P10. Si vous tentez une E/S de 2 Mo dans la même seconde, le total d'unités d'E/S est égal à 495 + 8. En effet, une E/S de 2 Mo équivaut à 2 048 Ko / 256 Ko = 8 unités d'E/S, avec une taille d'unité d'E/S égale à 256 Ko. Étant donné que la somme de 495 + 8 dépasse la limite de 500 IOPS du disque, il se produit une limitation. 

<h4>Remarque :</h4> 
tous les calculs sont basés sur une taille d'unité d'E/S de 256 Ko.

<h4>Exemple 2 :</h4>
Votre application a effectué 400 E/S de 256 Ko sur un disque P10. La bande passante totale consommée est (400 * 256) / 1 024 = 100 Mo/s. Notez que la limite de débit du disque P10 est de 100 Mo par seconde. Si votre application tente d'effectuer davantage d'E/S dans la même seconde, elle sera limitée, car elle dépassera la limite allouée.

<h4>Remarque :</h4>
si le trafic du disque consiste essentiellement en des E/S de petite taille, il est très probable que votre application atteindra la limite d'IOPS avant la limite de débit. En revanche, si le trafic du disque consiste essentiellement en des E/S de grande taille, il est très probable que votre application atteindra la limite de débit avant la limite d'IOPS. Vous pouvez optimiser la capacité de votre application en termes d'IOPS et de débit en utilisant des tailles d'E/S optimales et en limitant le nombre de demandes d'E/S en attente pour le disque.


###<a id="restapi">Captures instantanées et copie d'objets blob avec le stockage Premium</a>
Vous pouvez créer une capture instantanée pour le stockage Premium de la même façon que pour le stockage Standard. Le stockage Premium étant localement redondant, nous vous recommandons de créer des captures instantanées et de les copier sur un compte de stockage Standard géo-redondant. Pour plus d'informations, consultez [Options de redondance du stockage Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx).

Si un disque est attaché à une machine virtuelle, certaines opérations d'API ne sont pas autorisées sur l'objet blob de pages utilisé par le disque. Par exemple, vous ne pouvez pas effectuer une opération [Copy blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) sur cet objet blob tant que le disque est attaché à une machine virtuelle. À la place, créez d'abord une capture instantanée de cet objet blob à l'aide de la méthode de l'API REST [Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx), puis exécutez l'opération [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) de la capture instantanée pour copier le disque attaché. Vous pouvez également détacher le disque et effectuer les opérations nécessaires sur l'objet blob sous-jacent.

<h4>Remarques importantes :</h4>

- si l'opération de copie d'objet blob sur le stockage Premium remplace un objet blob existant dans la destination, l'objet blob remplacé ne doit contenir aucune capture instantanée.  Une copie dans un compte de stockage Premium ou entre plusieurs comptes de ce type requiert que l'objet blob de destination ne contienne aucune capture instantanée pendant le lancement de la copie.
- Le nombre de captures instantanées est limité à 100 par objet blob. Une capture instantanée peut être prise toutes les 10 minutes au maximum.
- La capacité maximale de captures instantanées par compte de stockage Premium est de 10 To. Notez que la capacité de captures instantanées représente les seules données contenues dans les captures instantanées. En d'autres termes, la capacité de captures instantanées n'inclut pas la taille de l'objet blob de base.
- Vous pouvez copier des captures instantanées d'un compte de stockage Premium vers un compte de stockage Standard géo-redondant à l'aide des opérations AzCopy ou Copy Blob. Pour plus d'informations, consultez [Utilisation d'AzCopy avec Microsoft Azure Storage](http://azure.microsoft.com/documentation/articles/storage-use-azcopy/) et [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).
- Pour plus d'informations sur l'exécution d'opérations REST sur les objets blob de pages dans les comptes de stockage Premium, consultez [Utilisation des opérations de service blob avec le stockage Azure Premium](http://go.microsoft.com/fwlink/?LinkId=521969) dans MSDN library.


###<a id="pricing">Tarification et facturation du stockage Premium</a>
Les considérations de facturation suivantes s'appliquent à l'utilisation du stockage Premium :

- La facturation pour un disque de stockage Premium dépend de la taille configurée du disque. Azure mappe la taille du disque (arrondie à la valeur supérieure) à l'option de disque de stockage Premium la plus proche, comme indiqué dans le tableau de la section [Objectifs d'extensibilité et de performances avec le stockage Premium](#scale) . La facturation de n'importe quel disque configuré est calculée au prorata horaire sur la base du tarif mensuel de l'offre de stockage Premium. Par exemple, si vous configurez un disque P10 et le supprimez au bout de 20 heures, vous êtes facturé 20 heures pour l'offre P10. Le montant facturé est indépendant de la quantité de données écrites sur le disque ou de la quantité de débit/IOPS utilisés.
- Les captures instantanées sur le stockage Premium sont facturées en fonction de la capacité supplémentaire utilisée par les captures instantanées. Pour plus d'informations sur les captures instantanées, consultez [Création d'une capture instantanée d'un objet blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).
- [Les transferts de données sortantes](http://azure.microsoft.com/pricing/details/data-transfers/) (données sortant des centres de données Azure) sont facturés selon l'utilisation de la bande passante.

Pour plus d'informations sur la tarification du stockage Premium et des machines virtuelles de série DS, consultez :

- [Tarification d'Azure Storage](http://azure.microsoft.com/pricing/details/storage/)
- [Tarification d'Azure Virtual Machines](http://azure.microsoft.com/pricing/details/virtual-machines/)

###<a id="howto1">Création et utilisation du compte de stockage Premium pour disques</a>
Cette section montre comment créer un compte de stockage Premium à l'aide du portail Azure Preview et d'Azure PowerShell. Par ailleurs, elle montre un exemple de cas d'utilisation des comptes de stockage Premium : la création d'une machine virtuelle et l'attachement d'un disque de données à une machine virtuelle avec un stockage Premium.

Dans cette section :

* [Portail Azure Preview : création d'un compte de stockage](#howto3)	

* [Azure PowerShell : création d'un compte de stockage Premium et son utilisation pour des opérations de base de machine virtuelle](#howto2)	
 
###<a id="howto3">Portail Azure Preview : création d'un compte de stockage</a>
Cette section décrit comment créer un compte de stockage Premium à l'aide du portail Azure Preview.

1.	Connectez-vous au [portail Azure Preview](https://portal.azure.com/). Consultez la [Version d'évaluation gratuite](http://www.windowsazure.com/pricing/free-trial/) si vous ne possédez pas encore d'abonnement. 


    >[AZURE.NOTE] Si vous vous connectez au portail de gestion Azure, cliquez sur nom d'utilisateur en haut à droite du portail. Ensuite, cliquez sur **Basculer vers le nouveau portail**.
        

2.	Dans le menu Hub, cliquez sur **Nouveau**.

3.	Sous **Nouveau**, cliquez sur **Tout**. Sélectionnez **Stockage, cache, +sauvegarde**. Ensuite, cliquez sur **Stockage**, puis sur **Créer**.

4.	Dans le volet Compte de stockage, tapez un nom pour votre compte de stockage. Cliquez sur **Niveau de tarification**. Dans le volet **Niveaux de tarification recommandés**, cliquez sur **Parcourir tous les niveaux de tarification**. Dans le volet **Choisir votre niveau de tarification**, choisissez **Premium localement redondant**. Cliquez sur **Sélectionner**. Notez que le volet **Compte de stockage** affiche **Standard-GRS** comme **Niveau de tarification** par défaut. Après avoir cliqué sur **Sélectionner**, le **Niveau de tarification** affiché est **Premium-LRS**.
	
	![Pricing Tier][Image1]

	
5.	Dans le volet **Compte de stockage**, conservez les valeurs par défaut des champs **Groupe de ressources**, **Abonnement**, **Emplacement** et **Diagnostics**. Cliquez sur **Créer**.

Pour une description complète de la procédure pas à pas dans un environnement Azure, consultez [Création d'une machine virtuelle exécutant Windows dans le portail Azure Preview](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/).

###<a id="howto2">Azure PowerShell : création d'un compte de stockage Premium et son utilisation pour des opérations de base de machine virtuelle</a>
Cette section décrit comment créer un compte de stockage Premium et l'utiliser pour créer une machine virtuelle et attacher un disque de données à une machine virtuelle à l'aide d'Azure PowerShell.

1. Configurez l'environnement PowerShell en suivant les étapes indiquées à la section [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/fr-FR/documentation/articles/install-configure-powershell/).
2. Démarrez la console PowerShell, connectez-vous à votre abonnement et exécutez l'applet de commande PowerShell suivante dans la fenêtre de console. Comme l'indique cette instruction PowerShell, vous devez définir le paramètre **Type** sur **Premium_LRS** quand vous créez un compte de stockage Premium.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. Ensuite, créez une machine virtuelle de série DS et spécifiez que vous voulez un stockage Premium en exécutant les applets de commande PowerShell suivantes dans la fenêtre de console :

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

4. Si vous voulez davantage d'espace disque pour votre machine virtuelle, créez et attachez un disque de données à une machine virtuelle de série DS existante en exécutant les applets de commande PowerShell suivantes dans la fenêtre de console :

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

###<a id="see">Ressources supplémentaires</a>
[Utilisation des opérations de service blob avec le stockage Azure Premium](http://go.microsoft.com/fwlink/?LinkId=521969)

[Création d'une machine virtuelle exécutant Windows](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)

[Tailles de machine virtuelle et de service cloud pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

[Documentation du stockage](http://azure.microsoft.com/documentation/services/storage/)

[Référence MSDN](http://msdn.microsoft.com/library/azure/gg433040.aspx)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png
<!--HONumber=42-->
