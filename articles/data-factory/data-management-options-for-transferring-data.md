<properties 
	pageTitle="Options de transfert de données stockées vers le cloud | Azure" 
	description="Conseils pour choisir la meilleure option de transfert de données depuis d'autres clouds locaux vers [JO1]Microsoft Azure afin de réaliser des analyses de données avancées." 
	services="data-factory, hdinsight, machine-learning, storage, sql-database" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="01/07/2014" 
	ms.author="cgronlun"/>

# Options de transfert de données stockées vers le cloud Azure

Dans cet article, vous trouverez des conseils qui vous permettront de faire le bon choix lors du transfert de données à partir d'un déploiement ou d'autres clouds sur site vers Microsoft Azure afin de réaliser des analyses de données avancées. Transférer de grandes quantités de données peut prendre beaucoup de temps et requiert une sécurité adéquate. 

Dans cet article :

* [Service Azure Import/Export pour le stockage d'objets blob](#blob)
* [Utilitaire AzCopy](#azcopy-utility)
* [Azure PowerShell](#ps)
* [Azure Data Factory (version préliminaire)](#data-factory)
* [Outils de migration de base de données SQL Azure](#tools)
* [Synchronisation des données SQL Azure (version préliminaire)](#data-sync)
* [Concentrateurs d'événements Azure](#event-hubs)
* [Autres options pour le transfert de données](#other)
* [Choix de l'option de transfert de données qui convient](#choose)


## Service Azure Import/Export pour le stockage d'objets blob

Vous pouvez utiliser le service Azure Import/Export pour transférer de grandes quantités de données de fichiers vers ou depuis un stockage d'objets blob Azure lorsque le coût de leur téléchargement ou de leur chargement sur le réseau est prohibitif ou lorsque l'opération n'est pas réalisable. Charger ou télécharger des jeux de données importants sur le réseau demande beaucoup de temps. Par exemple, il faut 1 mois pour télécharger 10 To avec une ligne T3 (44,7 Mbit/s). Avec le service Microsoft Azure Import/Export, les clients peuvent envoyer leur disque dur afin de réduire le temps de chargement et de téléchargement. Prévoyez plusieurs jours en prenant en compte la livraison. 

Pour transférer un important jeu de données vers un stockage d'objets blob, vous pouvez envoyer un ou plusieurs disques durs contenant ces données à un centre de données Azure, qui chargera vos données vers votre compte de stockage. De la même manière, pour exporter des données à partir d'un stockage d'objets blob, vous pouvez envoyer des disques durs vides à un centre de données Azure, qui se chargera de copier les données blob de votre compte de stockage sur vos disques durs avant de vous les renvoyer. Avant d'envoyer un lecteur contenant des données, vous devez chiffrer les données qu'il contient. Lorsque le service Import/Export exportera vos données pour vous les envoyer, celles-ci seront également chiffrées avant expédition.

Pour plus d'informations, consultez l'article [Transfert de données vers le stockage d'objets blob à l'aide du service Microsoft Azure Import/Export][import-export].


## Utilitaire AzCopy

AzCopy est un utilitaire de ligne de commande conçu pour charger, télécharger et copier des données avec efficacité vers et à partir d'un stockage de fichiers, d'objets blob et de tables Microsoft Azure. Cet utilitaire est adapté à un transfert unique de données entre un stockage Azure et un stockage local s'il est possible de transférer des données en passant par le réseau. Consultez l'article [Prise en main de l'utilitaire de ligne de commande AzCopy][azcopy].

> [AZURE.NOTE] Utilisateurs Linux : [téléchargez ACP, l'équivalent d'AzCopy pour Linux](http://www.paratools.com/acp)


## Azure PowerShell

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Vous pouvez utiliser Azure PowerShell pour charger des données vers le stockage d'objets blob, afin qu'elles soient traitées par des analyses de données avancées ou des tâches MapReduce.

Consultez également :

* [Téléchargement de données vers le stockage d'objets blob avec Azure PowerShell][upload]
* [Installation et configuration d'Azure PowerShell][install]


## Azure Data Factory (version préliminaire)

Azure Data Factory est un service entièrement géré pour composer des services de stockage, de traitement et de transfert dans des pipelines de production de données rationalisés, évolutifs et fiables.

Les développeurs peuvent créer des flux de travail pilotés par les données qui regroupent, agrègent et transforment des données structurées, semi-structurées et non structurées locales (via la passerelle de gestion des données), du cloud ou provenant de services Internet, et peuvent configurer le traitement de données complexes via un simple script JSON. Les données qui en résultent peuvent être stockées dans Azure Storage ou dans la base de données SQL Azure afin de réaliser des analyses de données avancées.

Plus précisément, un développeur peut orchestrer des activités régulières de copie entre différentes sources et destinations indiquées dans la section [Entrées et sorties prises en charge](data-factory-copy-activity.md#SupportedSourcesAndSinks)" de l'article [Copie de données avec Azure Data Factory](data-factory-copy-activity.md), qui traite également des propriétés de différents types de stockage de données, du mappage de colonnes, des formats de sérialisation et du traitement des types de données.

Ce service peut gérer les échecs grâce au redémarrage automatique et permettre la conversion de formats lors du transfert des données d'un format à un autre. Pour définir une activité de copie, consultez l'article [Prise en main de Data Factory][start]. Les procédures d'enregistrement dans un stockage de données et d'installation de la passerelle sont décrites dans l'article [Activation de vos pipelines pour utiliser des données locales][pipelines]. 

Consultez également :

* [Présentation du service Azure Data Factory][intro]

## Outils de migration de base de données SQL Azure

Il existe de nombreux outils pour migrer des bases de données SQL Server locales et des bases de données non SQL Server vers une base de données SQL Azure. L'outil qui répondra le mieux à vos besoins dépend du type, de la taille et de la complexité de la base de données en cours de migration :

* Vous pouvez migrer le schéma et les données d'une base de données SQL Azure existante en exportant la base de données, en stockant le fichier d'exportation dans un compte de stockage d'objets blob Azure, puis en l'important sous la forme d'une nouvelle base de données SQL Azure. Le fichier créé lors de cette exportation est appelé un fichier BACPAC. Pour plus d'informations, consultez l'article [Procédure : Utilisation du service d'importation et d'exportation dans la base de données SQL Azure][sql-import].
* La fonctionnalité de copie de base de données crée une nouvelle base de données dans Azure qui est une copie cohérente transactionnelle d'une base de données SQL Azure existante. Pour plus d'informations, consultez l'article [Copie de bases de données dans une base de données SQL Azure][sql-copy].
* SQL Server Integration Services (SSIS) peut être utilisé lorsque des transformations complexes de données sont nécessaires. SSIS peut être utilisé pour transférer des données vers et depuis la base de données SQL Azure. Pour plus d'informations, consultez l'article [Procédure : Integration Services pour migrer une base de données dans la base de données SQL Azure][integrate] et [SSIS pour le transfert de données Azure et hybrides][SSIS].
* L'assistant d'importation et d'exportation SQL Server est un moyen simple de créer un package SSIS pour migrer les données. Après avoir configuré la source et la destination, vous pouvez spécifier des transformations de base des données. Ces packages peuvent être enregistrés, modifiés, exécutés et planifiés en tant que tâche. Pour plus d'informations, consultez l'article [Procédure : l'Assistant Importation et Exportation pour migrer une base de données dans la base de données SQL Azure][wizard].
* L'assistant de migration de base de données SQL est un outil qui permet de migrer le schéma et les données entre une base de données SQL Server locale et une base de données SQL Azure, ainsi qu'entre différents serveurs de base de données SQL Azure. Cet outil analyse également les fichiers de suivi et les scripts pour identifier les problèmes de compatibilité avec la base de données SQL Azure. Pour plus d'informations, consultez l'article [Procédure : utilisation de l'Assistant Migration de base de données SQL][use-wizard].
* L'utilitaire bcp peut être utilisé pour importer un grand nombre de nouvelles lignes dans des tables SQL Server ou pour exporter des données hors des tables sous forme de fichiers de données. Pour plus d'informations, consultez l'article [Procédure : utilisation de bcp pour migrer une base de données vers la base de données SQL Azure][bcp].

Consultez également :

* [Migration de bases de données vers Base de données SQL Azure][migrate]
 

## Synchronisation des données SQL Azure (version préliminaire)

La synchronisation des données SQL (version préliminaire) permet de créer et de programmer des synchronisations régulières entre la base de données SQL Azure et les bases de données hébergées dans SQL Server ou la base de données SQL Azure.

La synchronisation des données SQL permet aux développeurs de données de synchroniser les changements delta entre les bases de données locales et les bases de données Azure dans le cloud. Consultez l'article [Synchronisation des données SQL][sync].

##	Concentrateurs d'événements Azure

Concentrateurs d'événements Microsoft Azure est un service dont la fonction consiste à ingérer des événements. Il fournit des entrées d'événements et de télémétrie vers le cloud à grande échelle, avec une faible latence et une grande fiabilité. Ce service, utilisé avec d'autres services en aval, est particulièrement utile pour l'instrumentation des applications, le traitement des flux de travail ou de l'expérience utilisateur et les scénarios de l'Internet des Objets.

Les développeurs peuvent écrire du code pour envoyer des données à un concentrateur d'événements, les traiter et les stocker dans des objets blob Azure ou dans une base de données SQL Azure pour continuer leur traitement.

Les développeurs peuvent également tirer profit d'Azure Stream Analytics, un service entièrement géré permettant de générer des sorties par le biais d'un traitement évolutif des événements complexes via des données de diffusion. Les développeurs peuvent choisir un flux de données à partir du concentrateur d'événements Azure, spécifier le format utilisé pour sérialiser l'événement entrant (p. ex. : JSON, CSV ou Avro), puis ajouter un emplacement de sortie, notamment des objets blob Azure ou une base de données SQL Azure.

Consultez l'article :

* [Informations sur le service de concentrateur d'événements](/services/event-hubs/)
* [Vue d'ensemble des concentrateurs d'événements][overview]
* [Présentation d'Azure Stream Analytics][stream]

## Autres options pour le transfert de données

Les connexions hybrides offrent un moyen simple et pratique de connecter Sites Web Azure et Azure Mobile Services à des ressources locales. Les développeurs peuvent créer des sites Web pour transférer des données de ressources locales vers Azure. Consultez l'article [Aperçu des connexions hybrides][hybrid] pour plus d'informations.

Avec [Virtual Network](/services/virtual-network/), vous pouvez utiliser des outils d'intégration de données en cours d'exécution dans une machine virtuelle Azure pour vous connecter de façon sécurisée aux bases de données locales SQL Server dans votre centre de données local. Seuls les machines virtuelles et les services au sein du même réseau virtuel peuvent être identifiés ou se connecter l'un à l'autre. Si vous préférez, vous pouvez même créer une connexion directe [ExpressRoute](/services/expressroute/) à Azure via votre fournisseur de services réseau ou votre fournisseur exchange et contourner totalement le réseau Internet public.

[Azure Marketplace](?../source=datamarket.md) propose des solutions de partenaires, qui permettent un transfert de données vers Azure, comme Storm Managed File Transfer.

## Choisissez l'option de transfert de données qui vous convient

### Arbre de décision

![Aide pour vous aider à décider quelle option de cloud choisir pour transférer vos données.][decision]

Remarques à propos de l'arbre de décision :

* La synchronisation des données SQL (version préliminaire) déclenche une synchronisation à partir des données modifiées.
* Data Factory (version préliminaire) prend en charge la copie de données entre Azure Storage, la base de données SQL Azure et le SQL Server local. 
* Outre l'utilisation de Data Factory, vous pouvez étendre les packages SSIS existants pour transférer des données vers le cloud de manière programmée.

### Transférer des Go de données

<table border="1">
<tr>
<th>Transfert de données</th>
<th>Transfert unique</th>
<th>Programmé</th>
<th>Avis</th>
</tr>

<tr>
<td><p>Fichier vers Azure Storage</p>
</td>
<td><ul>
<li><a href="/documentation/articles/storage-use-azcopy/">AzCopy</a></li>
<li><a href="http://www.paratools.com/acp" target="_blank">acp</a></li>
<li><a href="/documentation/articles/install-configure-powershell/">Azure PowerShell</a></li>
<li><a href="/documentation/articles/storage-import-export-service/">Azure Import/Export</a></li>
</ul>
</td>
<td><p>N/A</p>
</td>
<td><p>Utilisez Azure Import/Export au lieu d'AzCopy, d'acp et d'Azure PowerShell lorsque le transfert de données via le réseau n'est pas pratique. Ce service peut prendre plusieurs jours en plus de l'expédition du disque au centre de données.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server vers la base de données SQL Azure</li>
<li>Base de données SQL Azure vers SQL Server</li>
</ul>
</td>
<td><ul>
<li><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></li>
<li><a href="http://msdn.microsoft.com/library/azure/ee730904.aspx">Outils de Migration de base de données SQL</a></li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p>Nous vous suggérons d'utiliser Azure Data Factory (version préliminaire) au lieu des outils de migration, car il permet de convertir les formats et de bénéficier de tâches de récupération automatique dans des cas exceptionnels. Si SQL Server est un serveur local, une passerelle de gestion des données est nécessaire pour se connecter à SQL Server.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Azure Storage vers base de données SQL Azure</li>
<li>Base de données SQL Azure vers Azure Storage</li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p>Azure Data Factory (version préliminaire) permet de convertir les formats et de bénéficier de tâches de récupération automatique dans des cas exceptionnels. Si SQL Server est un serveur local, une passerelle de gestion des données est nécessaire pour se connecter à SQL Server.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server vers synchronisation des données SQL</li>
<li>Synchronisation des données SQL vers base de données Azure SQL</li>
</ul>
</td>
<td><p>N/A</p>
</td>
<td><p><a href="http://msdn.microsoft.com/library/azure/hh456371.aspx">Synchronisation des données SQL</a></p>
</td>
<td><p>La synchronisation des données SQL (version préliminaire) synchronise vos données en groupes de synchronisation qui définissent les bases de données, tables et colonnes à synchroniser, ainsi que la planification de synchronisation.</p>
</td>
</tr>

</table>



### Transfert de To de données

<table border="1">
<tr>
<th>Transfert de données</th>
<th>Transfert unique</th>
<th>Programmé</th>
<th>Avis</th>
</tr>

<tr>
<td><p>Fichier vers Azure Storage</p>
</td>
<td><p><a href="/documentation/articles/storage-import-export-service/">Azure Import/Export</a></p>
</td>
<td><p>N/A</p>
</td>
<td><p>Ce service peut prendre jusqu'à plusieurs jours en plus du temps nécessaire au disque pour arriver au centre de données.</p>
</td>
</tr>

</table>
<br>


<!--Anchors-->
[Service Azure Import/Export pour le stockage d'objets blob]: #blob
[Utilitaire AzCopy]: #azcopy-utility
[Azure PowerShell]: #ps
[Azure Data Factory (version préliminaire)]: #data-factory
[Outils de migration de base de données SQL Azure]: #tools
[Synchronisation des données SQL Azure (version préliminaire)]: #data-sync
[Concentrateurs d'événements Azure]: #event-hubs
[Autres options pour le transfert de données]: #other
[Choix de l'option de transfert de données qui convient]: #choose

<!--Image references-->
[decision]: ./media/data-management-options-for-transferring-data/data-transfer-decision-tree.png


<!--Link references-->
[import-export]: ../storage-import-export-service.md
[azcopy]: ../storage-use-azcopy.md
[upload]: ../hdinsight-upload-data.md#powershell
[install]: ../install-configure-powershell.md
[start]: data-factory-get-started.md
[pipelines]: data-factory-use-onpremises-datasources.md
[copy]: data-factory-copy-activity.md
[intro]: data-factory-introduction.md
[sql-import]: http://msdn.microsoft.com/library/azure/hh335292.aspx
[sql-copy]: http://msdn.microsoft.com/library/azure/ff951624.aspx
[integrate]: http://msdn.microsoft.com/library/azure/jj156150.aspx
[SSIS]: http://msdn.microsoft.com/library/jj901708.aspx
[wizard]: http://msdn.microsoft.com/library/azure/jj156152.aspx
[use-wizard]: http://msdn.microsoft.com/library/azure/jj156144.aspx
[bcp]: http://msdn.microsoft.com/library/azure/jj156153.aspx
[migrate]: http://msdn.microsoft.com/library/azure/ee730904.aspx
[overview]: http://msdn.microsoft.com/library/dn836025.aspx
[stream]: ../stream-analytics-introduction.md
[sync]: http://msdn.microsoft.com/library/azure/hh456371.aspx
[hybrid]: ../integration-hybrid-connection-overview.md



<!--HONumber=52--> 