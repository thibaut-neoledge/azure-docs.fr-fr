<properties
	pageTitle="Utilisation du stockage Azure pour la sauvegarde et la restauration de SQL Server | Microsoft Azure"
	description="Sauvegardez SQL Server et les bases de données SQL dans Azure Storage. Présente les avantages de la sauvegarde des bases de données SQL dans Azure Storage et les composants SQL Server et Azure Storage requis"
	services="sql-database, virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor="tysonn"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="vm-windows-sql-server"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="carlrab"/>



# Utilisation du stockage Azure pour la sauvegarde et la restauration de SQL Server

## Vue d'ensemble

La fonctionnalité qui offre la possibilité d’écrire des sauvegardes SQL Server sur le service BLOB Azure est apparue avec la version SP1 CU2 de SQL Server 2012. Vous pouvez utiliser cette fonctionnalité pour les opérations de sauvegarde et de restauration à partir du service d’objets blob Azure avec une base de données SQL Server locale ou une base de données SQL Server sur une machine virtuelle Azure. La sauvegarde dans le cloud offre les avantages de la disponibilité, du stockage hors site géo-répliqué sans limite et de la facilité de migration des données vers et depuis le cloud. Vous pouvez émettre des instructions BACKUP ou RESTORE en utilisant Transact-SQL ou SMO. En outre, quand les fichiers de base de données sont stockés dans un objet blob Azure et que vous utilisez SQL Server 2016, vous pouvez utiliser la [sauvegarde instantanée de fichiers](http://msdn.microsoft.com/library/mt169363.aspx) pour effectuer des sauvegardes quasi instantanées et des restaurations extrêmement rapides.

## Avantages de l’utilisation du service d’objets Azure pour les sauvegardes SQL Server

La gestion du stockage, le risque de défaillance du stockage, l'accès à un stockage hors site et la configuration des appareils sont des exemples des défis auxquels la sauvegarde peut être confrontée. Ces défis existent pour les instances de base de données locale et mes instances de base de données de machine virtuelle Azure. Vous trouvez ci-dessous certains des avantages clés de l’utilisation du service BLOB Azure pour les sauvegardes SQL Server :

* Stockage hors site flexible, fiable et sans limite : le stockage de vos sauvegardes dans des objets blob Azure peut représenter une option hors site pratique, flexible et facile d’accès. La création d’un stockage hors site pour vos sauvegardes SQL Server peut être aussi facile que la modification de vos scripts/tâches existants pour utiliser la syntaxe **BACKUP TO URL**. Le stockage hors site doit généralement être suffisamment éloigné de l'emplacement de la base de données de production afin d'empêcher qu'un seul sinistre touche à la fois l'emplacement hors site et la base de données de production. En choisissant de [répliquer géographiquement vos objets blob Azure](../storage/storage-redundancy.md), vous obtenez une couche de protection supplémentaire en cas de sinistre susceptible d’affecter l’ensemble de la région. 
* Archive de sauvegarde : le service BLOB Azure offre une meilleure alternative à l’option sur bande souvent utilisée pour archiver les sauvegardes. Le stockage sur bande peut nécessiter un transport physique vers une installation hors site ainsi que la prise de mesures de protection du support. Le stockage de vos sauvegardes dans le service BLOB Azure offre une option d’archivage instantané, hautement disponible et durable.
* Absence de frais de gestion du matériel : il n’y a aucun frais de gestion du matériel avec les services Azure. Ces derniers gèrent le matériel et fournissent une géo-réplication à des fins de redondance et de protection contre les défaillances matérielles.
* Actuellement, pour des instances de SQL Server s’exécutant sur une machine virtuelle Azure, la sauvegarde dans les services de stockage d’objets blob Azure peut être effectuée en créant des disques attachés. Néanmoins, le nombre de disques que vous pouvez attacher à une machine virtuelle Azure est limité pour les sauvegardes. Cette limite est de 16 disques pour une instance très volumineuse et un nombre inférieur pour les instances plus petites. En activant une sauvegarde directe dans les objets blob Azure, vous avez accès à un stockage quasi illimitée.
* Les sauvegardes stockées dans des objets blob Azure sont disponibles depuis n’importe où et à tout moment et facilement accessibles pour les restaurations sur un serveur SQL Server local ou un autre serveur SQL en cours d’exécution sur une machine virtuelle Azure, sans avoir besoin d’attacher/détacher la base de données ni télécharger et attacher le disque dur virtuel.
* Avantage en termes de coûts : ne payez que pour le service utilisé. Peut être économique comme option d’archivage hors site et de sauvegarde. Pour plus d’informations, consultez [Calcul des coûts Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calcul des coûts") et l’[article Tarification Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Article sur les coûts").
* Exploitez les instantanés de stockage : quand les fichiers de base de données sont stockés dans un objet blob Azure et que vous utilisez SQL Server 2016, vous pouvez utiliser la [sauvegarde instantanée de fichiers](http://msdn.microsoft.com/library/mt169363.aspx) pour effectuer des sauvegardes quasi instantanées et des restaurations extrêmement rapides.

Pour plus d'informations, consultez la page [Sauvegarde et restauration SQL Server avec le service de stockage d'objets blob Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Les deux sections suivantes introduisent le service BLOB Azure, ainsi que les composants SQL Server utilisés lors de la sauvegarde vers le service BLOB Azure et la restauration depuis ce dernier. Il est important de comprendre les composants et l'interaction entre SQL Server et le service BLOB Azure pour réaliser une sauvegarde ou une restauration.

La création d'un compte Azure constitue la première étape de ce processus. Pour obtenir la procédure pas à pas complète de création d’un compte de stockage et de réalisation d’une simple restauration à l’aide de SQL Server 2014, consultez [Prise en main du service de stockage Azure pour la sauvegarde et la restauration SQL Server](https://msdn.microsoft.com/library/jj720558(v=sql.120).aspx). Pour obtenir la procédure pas à pas complète de création d’un compte de stockage et de réalisation d’une simple restauration à l’aide de SQL Server 2014, consultez [Didacticiel : utilisation du service de stockage d’objets blob Microsoft Azure avec des bases de données SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## Composants du service BLOB Azure

* Compte de stockage : le compte de stockage est le point de départ de tous les services de stockage. Pour accéder au service BLOB Azure, commencez par créer un compte de stockage Azure. Pour plus d'informations sur le service BLOB Azure, consultez la page [Utilisation du service BLOB Azure](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)

* Conteneur : un conteneur fournit un regroupement contenant un nombre illimité d’objets blob. Pour écrire une sauvegarde SQL Server sur un service BLOB Azure, au moins un conteneur racine doit être créé.

* Objet blob : fichier de tout type et de toute taille. Les objets blob sont adressables à l’aide du format d’URL suivant : `https://<storage account>.blob.core.windows.net/<container>/<blob>` Pour plus d’informations sur les objets blob de pages, consultez [Présentation des objets blob de blocs et de pages](http://msdn.microsoft.com/library/azure/ee691964.aspx).

## Composants SQL Server

* URL : une URL attribue un URI (Uniform Resource Identifier) à un fichier de sauvegarde unique. L’URL fournit l’emplacement et le nom du fichier de sauvegarde SQL Server. L’URL doit pointer vers un objet blob réel, pas juste un conteneur. Si l’objet blob n’existe pas, il est créé. Si un objet blob existant est indiqué, BACKUP échoue, sauf si l’option > WITH FORMAT est indiquée. Vous trouverez ci-dessous un exemple d’URL à indiquer dans la commande BACKUP : ****`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>Remarque :</b> HTTPS n’est pas requis, mais recommandé. <b>Important :</b> si vous choisissez de copier et de charger un fichier de sauvegarde dans le service de stockage d’objets blob Azure, vous devez utiliser un type d’objet blob de pages comme option de stockage si vous prévoyez d’utiliser ce fichier pour des opérations de restauration. La commande RESTORE depuis un type d’objet blob de blocs échouera avec une erreur.

* Informations d’identification : les informations requises pour se connecter au service BLOB Azure et pour s’y authentifier sont stockées sous la forme d’informations d’identification. Vous devez créer des informations d’identification SQL Server afin que ce dernier écrive les sauvegardes sur un service BLOB Azure ou les restaure depuis celui-ci. Pour plus d’informations, consultez [Informations d’identification SQL Server](https://msdn.microsoft.com/library/ms189522.aspx).

## Sauvegarde et restauration des bases de données SQL Server avec le service BLOB Azure - Concepts et tâches :

**Concepts, considérations et exemples de code :**

[Sauvegarde et restauration SQL Server avec le service de stockage d’objets blob Azure](http://go.microsoft.com/fwlink/?LinkId=271617)

**Didacticiel de prise en main :**

[Didacticiel : utilisation du service de stockage d’objets blob Microsoft Azure avec des bases de données SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)

**Meilleures pratiques, dépannage :**

[Pratiques recommandées pour la sauvegarde et la restauration (service de stockage d’objets blob Azure)](http://go.microsoft.com/fwlink/?LinkId=272394)

<!---HONumber=Nov15_HO1-->