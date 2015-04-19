<properties 
	pageTitle="Utilisation du stockage Azure pour la sauvegarde et la restauration de SQL Server | Azure" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/30/2014" 
	ms.author="jeffreyg"/>



<h1 id="SQLServerBackupandRestoretostorage">  Utilisation du stockage Azure pour la sauvegarde et la restauration de SQL Server</h1>

La fonctionnalité qui offre la possibilité d'écrire des sauvegardes SQL Server sur le service BLOB Azure est apparue avec la version SP1 CU2 de SQL Server 2012. Vous pouvez utiliser cette fonctionnalité pour sauvegarder et restaurer à partir du service BLOB Azure depuis une base de données SQL Server locale ou une base de données SQL Server sur une machine virtuelle Azure. La sauvegarde dans le cloud offre les avantages de la disponibilité, du stockage hors site géo-répliqué sans limite et de la facilité de migration des données vers et depuis le cloud.   Dans cette version, vous pouvez émettre des instructions BACKUP ou RESTORE en utilisant T-SQL ou SMO. La sauvegarde ou la restauration depuis le service BLOB Azure en utilisant la sauvegarde SQL Server Management Studio ou l'Assistant Restauration n'est pas disponible.

<h2> Avantages de l'utilisation du service d'objets Azure pour les sauvegardes SQL Server</h2>

La gestion du stockage, le risque de défaillance du stockage, l'accès à un stockage hors site et la configuration des appareils sont des exemples des défis auxquels la sauvegarde peut être confrontée.  Pour SQL Server s'exécutant dans une machine virtuelle Azure, d'autres défis surgissent, relatifs à la configuration et à la sauvegarde d'un disque dur virtuel ou à la configuration des lecteurs attachés. Vous trouvez ci-dessous certains des avantages clés de l'utilisation du service BLOB Azure pour les sauvegardes SQL Server :

* Stockage hors site flexible, fiable et sans limite : le stockage de vos sauvegardes sur le service BLOB Azure peut représenter une option hors site pratique, flexible et facile d'accès. La création d'un stockage hors site pour vos sauvegardes SQL Server peut être aussi facile que la modification de vos scripts/tâches existants. Le stockage hors site doit généralement être suffisamment éloigné de l'emplacement de la base de données de production afin d'empêcher qu'un seul sinistre touche à la fois l'emplacement hors site et la base de données de production. En choisissant de répliquer géographiquement le stockage des objets blob, vous obtenez une couche de protection supplémentaire en cas de sinistre susceptible d'affecter l'ensemble de la région. En outre, des sauvegardes sont disponibles depuis n'importe où, à n'importe quel moment, et il est facile d'y accéder pour les restaurations.
* Archive de sauvegarde : le service BLOB Azure offre une meilleure alternative à l'option sur bande souvent utilisée pour archiver les sauvegardes. Le stockage sur bande peut nécessiter un transport physique vers une installation hors site ainsi que la prise de mesures de protection du support. Le stockage de vos sauvegardes dans le service BLOB Azure offre une option d'archivage instantané, hautement disponible et durable.
* Absence de frais de gestion du matériel : il n'y a aucun frais de gestion du matériel avec les services Azure. Ces derniers gèrent le matériel et fournissent une géo-réplication à des fins de redondance et de protection contre les défaillances matérielles.
* Actuellement, pour des instances de SQL Server s'exécutant sur une machine virtuelle Azure, la sauvegarde dans le service BLOB Azure peut être effectuée en créant des disques attachés. Néanmoins, le nombre de disques que vous pouvez attacher à une machine virtuelle Azure est limité. Cette limite est de 16 disques pour une instance très volumineuse et un nombre inférieur pour les instances plus petites. En activant une sauvegarde directe dans le service BLOB Azure, vous pouvez ignorer la limite de 16 disques.
* En outre, le fichier de sauvegarde qui est désormais stocké dans le service BLOB Azure est directement disponible sur un serveur SQL Server local ou un autre serveur SQL Server sur une machine virtuelle Azure, sans nécessiter d'attacher/de détacher la base de données ou de télécharger et d'attacher le disque dur virtuel.
* Avantage en termes de coûts : ne payez que pour le service utilisé. Peut être économique comme option d'archivage hors site et de sauvegarde. Pour plus d'informations, consultez les pages [Calcul des coûts Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Pricing Calculator") et [Article sur les tarifs Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Pricing article").

Pour plus d'informations, consultez la page Sauvegarde et restauration [SQL Server avec le service de stockage d'objets blob Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Les deux sections suivantes introduisent le service BLOB Azure, ainsi que les composants SQL Server utilisés lors de la sauvegarde vers le service BLOB Azure et la restauration depuis ce dernier. Il est important de comprendre les composants et l'interaction entre SQL Server et le service BLOB Azure pour réaliser une sauvegarde ou une restauration. 

La création d'un compte Azure constitue la première étape de ce processus. SQL Server utilise le nom du compte de stockage Azure et ses valeurs de clés d'accès pour authentifier, écrire et lire des objets blob sur le service de stockage. Les informations d'identification de SQL Server stockent ces informations d'authentification qui sont utilisées au cours des opérations de sauvegarde ou de restauration. 

Pour obtenir une procédure pas à pas complète de création d'un compte de stockage et de réalisation d'une simple restauration, consultez la page [Mise en route avec la sauvegarde et la restauration SQL Server dans le service de stockage d'objets blob Azure](http://go.microsoft.com/fwlink/?LinkId=271615) 

## Composants du service BLOB Azure 

* Compte de stockage : le compte de stockage est le point de départ de tous les services de stockage. Pour accéder au service BLOB Azure, commencez par créer un compte de stockage Azure. Le nom du compte de stockage et ses propriétés de clés d'accès sont requis pour s'authentifier auprès du service BLOB Azure et de ses composants. 
Pour plus d'informations sur le service BLOB Azure, consultez la page [Utilisation du service BLOB Azure](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)

* Conteneur : un conteneur fournit un regroupement contenant un nombre illimité d'objets blob. Pour écrire une sauvegarde SQL Server sur un service BLOB Azure, au moins un conteneur racine doit être créé. 

* Objet blob : fichier de n'importe quel type et de n'importe quelle taille. Il existe deux types d'objets blob qui peuvent être enregistrés dans le service de stockage d'objets blob Azure : les objets blob de blocs et les objets blob de pages.  La sauvegarde SQL Server utilise les objets blob de pages comme type de blob. Les objets blob sont adressables à l'aide du format d'URL suivant : `https://<storage account>.blob.core.windows.net/<container>/<blob>`
Pour plus d'informations sur les objets blob de pages, consultez la page [Présentation des objets blob de blocs et des objets blob de pages](http://msdn.microsoft.com/library/windowsazure/ee691964.aspx)

## Composants SQL Server

* URL : une URL attribue un URI (Uniform Resource Identifier) à un fichier de sauvegarde unique. L'URL fournit l'emplacement et le nom du fichier de sauvegarde SQL Server. Dans cette implémentation, la seule URL valide est celle qui pointe vers une page d'objets Blob dans le compte de stockage Azure. L'URL doit pointer vers un objet blob réel, pas juste un conteneur. Si l'objet blob n'existe pas, il est créé. Si un objet blob existant est indiqué, BACKUP échoue, sauf si l'option > WITH FORMAT est indiquée. 
Vous trouverez ci-dessous un exemple d'URL à indiquer dans la commande BACKUP : 
**`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>Remarque :</b> HTTPS n'est pas requis, mais recommandé.
<b>Important</b>
Si vous choisissez de copier et de charger un fichier de sauvegarde dans le service BLOB Azure, vous devez utiliser un type d'objet blob de pages comme option de stockage si vous prévoyez d'utiliser ce fichier pour des opérations de restauration. La commande RESTORE depuis un type d'objet blob de blocs échouera avec une erreur. 

* Informations d'identification : les informations requises pour se connecter au service BLOB Azure et pour s'y authentifier sont stockées sous la forme d'informations d'identification.  Vous devez créer des informations d'identification SQL Server afin que ce dernier écrive les sauvegardes sur un service BLOB Azure ou les restaure depuis celui-ci. Les informations d'identification stockent le nom du compte de stockage et la clé d'accès à ce dernier.  Une fois les informations d'identification créées, elles doivent être indiquées dans l'option WITH CREDENTIAL lors de l'émission des instructions BACKUP/RESTORE. Pour plus d'informations sur l'affichage, la copie ou la régénération des clés d'accès au compte de stockage, consultez la page [Clés d'accès au compte de stockage](http://msdn.microsoft.com/library/windowsazure/hh531566.aspx).
Pour des instructions pas à pas sur la création d'informations d'identification SQL Server, consultez la page [Mise en route avec la sauvegarde et la restauration SQL Server dans le service de stockage d'objets blob Azure](http://go.microsoft.com/fwlink/?LinkId=271615).

## Sauvegarde et restauration des bases de données SQL Server avec le service BLOB Azure - Concepts et tâches :

**Concepts, considérations et exemples de code :**

[Sauvegarde et restauration SQL Server avec le service de stockage d'objets blob Azure](http://go.microsoft.com/fwlink/?LinkId=271617)

**Didacticiel de prise en main :**

[Mise en route avec la sauvegarde et la restauration SQL Server dans le service de stockage d'objets blob Azure](http://go.microsoft.com/fwlink/?LinkID=271615 "Tutorial")

**Meilleures pratiques, dépannage :**
	
[Pratiques recommandées pour la sauvegarde et la restauration (service de stockage d'objets blob Azure)](http://go.microsoft.com/fwlink/?LinkId=272394)




	





\<!--HONumber=42-->
