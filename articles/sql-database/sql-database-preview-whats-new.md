<properties 
	pageTitle="Nouveautés de la base de données SQL V12" 
	description="Décrit les dernières améliorations de la base de données Azure SQL, qui ont été ajoutées en décembre 2014 ou plus tard." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


# Nouveautés de la base de données SQL V12


<!--
GeneMi, jeudi 5 mars 2015, 20 h 06
Supprimer 'Azure' du titre de la rubrique, ajouter " V12 " (par J.G.).
-->



La version V12 de la base de données Azure SQL a été présentée en disponibilité générale en janvier 2015. Cette mise à jour majeure permet à la base de données SQL de fournir désormais une compatibilité presque totale avec le moteur Microsoft SQL Server. La base de données SQL apporte davantage de performances Premium aux clients. Ces améliorations aident à rationaliser les migrations d'application SQL Server sur une base de données SQL et aident les clients dont les charges de travail de base de données sont importantes.


Deux régions géographiques sont toujours à l'état de version préliminaire et ne sont pas encore en disponibilité générale. Trouvez votre région sur le [tableau de disponibilité générale par région](../sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable). Jusqu'à ce que votre région se trouve dans la disponibilité générale, la V12 convient mieux aux bases de données de test qu'aux bases de données de production.


**[Inscrivez-vous](https://portal.azure.com) à la base de données SQL pour tirer parti de cette nouvelle génération sur Microsoft Azure. Tout d'abord, vous avez besoin d'un abonnement à Microsoft Azure. Vous pouvez vous inscrire pour une [version d'évaluation gratuite Azure](http://azure.microsoft.com/pricing/free-trial) et obtenir des informations sur la [tarification](http://azure.microsoft.com/pricing/details/sql-database).**


Votre chemin vers la planification et la mise à niveau de vos bases de données V11 vers la V12 commence par la [Planification et préparation de la mise à niveau vers la dernière mise à jour (V12) de la base de données SQL (version préliminaire)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


### Points clés


Les points les plus importants de la base de données SQL Azure V12 sont les suivants :


- **Sécurité** : profitez de la nouvelle fonctionnalité des [utilisateurs dans des bases de données à relation contenant-contenu](../sql-database-preview-whats-new/#UsersInContainedDatabases). Deux autres fonctionnalités sont la [sécurité au niveau des lignes](../sql-database-preview-whats-new/#RowLevelSecurity) et le [masquage des données dynamiques](../sql-database-preview-whats-new/#DynamicDataMasking), même si celles-ci sont toujours en version préliminaire et ne sont pas encore en disponibilité générale.


- **Facilité de gestion** des bases de données volumineuses pour prendre en charge des charges de travail plus importantes avec des requêtes en parallèle (Premium uniquement), [partitionnement des tables](http://msdn.microsoft.com/library/ms187802.aspx), [indexation en ligne](http://msdn.microsoft.com/library/ms188388.aspx), recréation d'index volumineux simplifiée grâce à la suppression de la limite de 2 Go et davantage d'options sur l'instruction [alter database](http://msdn.microsoft.com/library/bb522682.aspx).


- **Prise en charge des fonctions de programmabilité clés** pour renforcer la conception des applications avec [intégration du CLR](http://msdn.microsoft.com/library/ms189524.aspx), les [fonctions de fenêtre](http://msdn.microsoft.com/library/ms189461.aspx) T-SQL, l'[index XML](http://msdn.microsoft.com/library/bb934097.aspx) et le [suivi des modifications](http://msdn.microsoft.com/library/bb933875.aspx) pour les données.


- **Performances fiables** avec prise en charge des requêtes [columnstore](http://msdn.microsoft.com/library/gg492153.aspx) en mémoire (niveau Premium uniquement) pour les charges de travail analytiques des mini-Data Warehouse et taille inférieure.


- **La surveillance et la résolution des problèmes** ont été améliorées avec une visibilité sur plus de 100 nouvelles vues de table dans un ensemble étendu de vues de gestion dynamique([DMV](http://msdn.microsoft.com/library/ms188754.aspx))


- **Nouveau niveau de performance S3 dans le niveau Standard :** offre davantage de souplesse de tarification entre les niveaux Standard et Premium. Le niveau S3 fournira davantage d'unités de débit de base de données (UDBD) et toutes les fonctionnalités disponibles du niveau Standard.


## 1. Détails des améliorations de la nouvelle version V12


Cette section cite et explique les nouvelles fonctionnalités de chaque catégorie.


### 1.1 Catégorie : Gestion étendue de base de données


| Fonctionnalité | Description |
| :--- | :--- |
| . | ***Décembre 2014 :*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Utilisateurs dans des bases de données à relation contenant-contenu | Vous pouvez maintenant créer des utilisateurs dans vos bases de données à relation contenant-contenu sans avoir une connexion correspondante dans la base de données master. Cela simplifie grandement le déplacement de votre base de données vers un autre serveur. Le code de connexion dans vos applications clientes est le même, que vous utilisiez des utilisateurs dans des bases de données à relation contenant-contenu ou non.<br/><br/>L'utilisation de cette fonctionnalité peut être requise pour les clients qui souhaitent profiter de contrats de niveau de service garanti supérieurs.<br/><br/>Nous vous encourageons généralement à utiliser cette fonctionnalité. Toutefois, certains clients peuvent avoir des scénarios spécifiques qui font de la paire *login+user* traditionnelle le meilleur choix pour eux à ce stade.<br/><br/>Pour plus d'informations, consultez les pages suivantes :<br/>- [Consignes et limitations de sécurité de base de données Azure SQL](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>[Gestion des bases de données et des connexions dans la base de données Azure SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [Bases de données à relation contenant-contenu](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Partitionnement de table | Les limites précédentes du [partitionnement de table](http://msdn.microsoft.com/library/ms190787.aspx) sont éliminées. |
| Transactions plus volumineuses | Avec la V12, vous n'êtes plus limité à un maximum de 2 Go de modifications de données dans une transaction unique. <br/><br/> L'un des avantages est que la recréation d'un index volumineux n'est plus limitée à une taille de transaction maximale de 2 Go. Pour plus d'informations, consultez [Limites des ressources de base de données SQL Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Création et recréation d'index en ligne | Avant la V12, la base de données SQL Azure prenait généralement en charge la clause (ONLINE=ON) de l'instruction ALTER INDEX, mais ce n'était pas le cas pour les index sur une colonne de type BLOB. La V12 prend désormais en charge la clause (ONLINE=ON), même pour les index sur des colonnes BLOB.<br/><br/> La fonctionnalité EN LIGNE permet aux requêtes d'utiliser un index même s'il est en cours de recréation. |
| Prise en charge de CHECKPOINT | Avec la V12, vous pouvez utiliser l'instruction CHECKPOINT T-SQL pour votre base de données. |
| Amélioration de l'instruction ALTER TABLE | Permet d'effecteur de nombreuses actions de modification de colonne tandis que la table reste disponible. Pour plus d'informations, consultez la rubrique [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| Amélioration de l'instruction TRUNCATE TABLE | Autorise la troncation de partitions spécifiques. Pour plus d'informations, consultez la rubrique [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx) |
| Plus d'options pour ALTER DATABASE | La V12 prend en charge davantage des options disponibles sur l'instruction ALTER DATABASE. <br/><br/> Pour plus d'informations, consultez [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx) ou [Référence Transact-SQL de base de données SQL Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Plus de commandes DBCC | Plusieurs autres commandes DBCC sont désormais disponibles dans la V12. Pour plus d'informations, consultez la rubrique [Guide de référence Transact-SQL dans la Base de données SQL Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


### 1.2 Catégorie : Programmation et prise en charge des applications


| Fonctionnalité | Description |
| :--- | :--- |
| . | ***Février 2015 :*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Aperçu du masquage des données dynamiques | Lorsqu'un ensemble de lignes est généré à partir d'une requête, une stratégie de masquage des données établie peut remplacer une partie des données par des caractères " X " afin de protéger les informations sensibles. Une fois l'opération de masquage terminée, l'ensemble de lignes modifié est envoyé au client.<br/><br/>Il est possible de l'utiliser pour masquer tous les chiffres d'un numéro de carte de crédit, sauf les derniers.<br/><br/>**REMARQUE :** Cette fonctionnalité est à l'état de version préliminaire et n'a pas encore été annoncée pour une disponibilité générale pour la production.<br/><br/>Pour plus d'informations, consultez la rubrique [Prise en main du masquage de données dynamiques de base de données SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/). |
| . | ***Janvier 2015 :*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Aperçu de la sécurité au niveau des lignes (RLS) | **Attention :** La fonctionnalité RLS est actuellement en *preview* uniquement, même dans les zones géographiques où la V12 est en disponibilité générale. Tant que RLS n'est pas en disponibilité générale, elle n'est pas encore appropriée pour une utilisation dans une base de données de production critique.<br/><br/>La nouvelle instruction CREATE SECURITY POLICY dans T-SQL vous permet d'implémenter la RLS. RLS pousse le serveur de base de données à ajouter des conditions qui filtrent certaines lignes de données avant qu'un ensemble de lignes ne soit retourné à l'appelant.<br/><br/>Dans le secteur, RLS est parfois également appelé contrôle d'accès affiné.<br/><br/>Pour un exemple de code et plus encore, consultez [Aperçu de la sécurité au niveau des lignes (RLS)](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx). |
| . | ***Décembre 2014 :*** |
| Fonctions de fenêtre dans les requêtes T-SQL | Les fonctions de fenêtre ANSI sont accessibles avec la [clause OVER](http://msdn.microsoft.com/library/ms189461.aspx). <br/><br/> Itzik Ben-Gan a rédigé un excellent [billet de blog](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) qui explique les fonctions de fenêtre et la clause OVER. |
| Intégration du CLR .NET | Le CLR (common language runtime) du .NET Framework est intégré à la V12. <br/><br/> Seuls les assemblys SAFE entièrement compilés en code binaire sont pris en charge. Pour plus d'informations, consultez [Restrictions du modèle de programmation de l'intégration du CLR](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> Vous trouverez des informations sur cette fonctionnalité dans les rubriques suivantes : <br/> * [Présentation de l'intégration du CLR de SQL Server](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Suivi des modifications pour les données | Le suivi des modifications pour les données peut maintenant être configuré au niveau de la base de données ou de la table. <br/><br/> Pour plus d'informations sur le suivi des modifications, consultez [À propos du suivi des modifications (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| Index XML | La V12 vous permet d'utiliser les instructions T-SQL CREATE XML INDEX et CREATE SELECTIVE XML INDEX. <br/><br/> Pour plus d'informations sur les index XML, consultez : <br/> * [CREATE XML INDEX (Transact-SQL)](http://msdn.microsoft.com/library/bb934097.aspx) <br/> * [Création, modification et annulation d'index XML sélectifs](http://msdn.microsoft.com/library/jj670109.aspx) |
| Table en tant que segment | La V12 vous permet de créer une table qui ne comporte aucun index en cluster. <br/><br/> Cette fonctionnalité est particulièrement utile pour la prise en charge de l'instruction T-SQL SELECT...INTO qui crée une table à partir d'un résultat de requête. |
| Rôles d'application | Pour le contrôle de la sécurité et des autorisations, la V12 vous permet d'utiliser les instructions GRANT - DENY - REMOVE sur [les rôles d'application](http://msdn.microsoft.com/library/ms190998.aspx). |


### 1.3 Catégorie : Meilleures informations sur les clients


| Fonctionnalité | Description |
| :--- | :--- |
| . | ***Décembre 2014 :*** |
| DMV (vues de gestion dynamique) | Plusieurs vues de gestion dynamique sont ajoutées dans la V12. Pour plus d'informations, consultez la rubrique [Guide de référence Transact-SQL dans la Base de données SQL Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Suivi des modifications | La V12 prend entièrement en charge le suivi des modifications. <br/><br/> Pour plus d'informations sur cette fonctionnalité, consultez [Activation et désactivation du suivi des modifications (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |


### 1.4 Améliorations des performances au niveau du service Premium


Ces améliorations de performances s'appliquent aux niveaux P2 et P3 du niveau de service Premium.


| Fonctionnalité | Description |
| :--- | :--- |
| . | ***Décembre 2014 :*** |
| Traitement en parallèle des requêtes | La V12 offre un degré plus élevé de parallélisme pour les requêtes qui peuvent en bénéficier. |
| Latence d'E/S plus courte | La V12 a une latence considérablement plus courte pour les opérations d'entrée/sortie. |
| Augmentation des IOPS | La V12 peut traiter une plus grande quantité d'opérations E/S par seconde (IOPS). |
| Taux d'enregistrement | La V12 peut enregistrer davantage de modifications de données par seconde. |


### 1.5 Résumé des améliorations


La V12 offre à la base de données SQL une compatibilité quasi totale des fonctionnalités avec notre produit SQL Server. La V12 se concentre sur les fonctionnalités les plus demandées et sur la prise en charge de la programmabilité. Le développement s'en trouve plus efficace et plus agréable.  Vous pouvez désormais déplacer plus facilement vos applications de base de données SQL dans le cloud.


Par ailleurs, le niveau Premium de la V12 apporte des améliorations de performances considérables. Pour certaines applications, les requêtes s'exécutent beaucoup plus rapidement. Les applications avec d'importantes charges de travail bénéficient d'un débit fiable.


## <a name="V12AzureSqlDbPreviewGaTable"></a>2. État de disponibilité générale de la V12 par région


La version V12 de base de données SQL Azure a été lancée uniquement comme version préliminaire et de test en décembre 2014, dans les régions indiquées dans le tableau suivant. Le passage de la version d'évaluation à la version de disponibilité générale complète (GA) se produit à des dates différentes selon les régions. Le tableau suivant affiche le statut actuel de la V12 pour chaque région.


> [AZURE.NOTE]
> [La tarification](http://azure.microsoft.com/pricing/details/sql-database/) au cours de l'évaluation était à prix réduit. La tarification retourne au niveau de disponibilité générale pour toutes les régions le 31 mars 2015.


| Région Azure | Version actuelle<br/>état de la V12 | Date de promotion<br/>vers la disponibilité générale |
| :--- | :--- | :--- |
| Sud du centre des États-Unis | Disponibilité générale | 9 février 2015 |
| Centre des États-Unis | Disponibilité générale | 9 février 2015 |
| Nord du centre des États-Unis | Disponibilité générale | 9 février 2015 |
| Ouest des États-Unis | Disponibilité générale | 9 février 2015 |
| Est des États-Unis | Disponibilité générale | 9 février 2015 |
| Est des États-Unis 2 | Disponibilité générale | 9 février 2015 |
| Asie de l'Est | Disponibilité générale | 24 février 2015 |
| Asie du Sud-Est | Disponibilité générale | 24 février 2015 |
| Ouest du Japon | Disponibilité générale | 24 février 2015 |
| Est du Japon | Disponibilité générale | 24 février 2015 |
| Europe du Nord | Disponibilité générale | 29 janvier 2015 |
| Europe de l'Ouest | Disponibilité générale | 29 janvier 2015 |
| Sud du Brésil | Non disponible | Troisième trimestre 2015, estimation |
| Est de l'Australie | VERSION PRÉLIMINAIRE | Deuxième trimestre 2015, estimation |
| Sud-est de l'Australie | VERSION PRÉLIMINAIRE | Deuxième trimestre 2015, estimation |


Pour toute région ayant atteint la disponibilité générale, tous les nouveaux abonnements et les bases de données suivantes utilisent l'architecture de service V12 et ont par conséquent accès aux fonctionnalités plus récentes. Cet article répertorie les nouvelles fonctionnalités de la V12.


Lors de la disponibilité générale, les clients avec des serveurs et des bases de données antérieurs à la V12 peuvent choisir de mettre à niveau (ou de déplacer) leurs bases de données vers l'architecture de service V12 afin d'utiliser ces nouvelles fonctionnalités pour la production. Les bases de données de la V12 doivent être au [niveau de prix](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) De base, Standard ou Premium.


## 3. Comment procéder


Découvrez les étapes de mise à jour de vos bases de données Azure SQL vers la V12 dans la section [Planification et préparation de la mise à niveau vers la version la plus récente de la base de données SQL Azure (version préliminaire)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


Les numéros de version comme V12 font référence à la valeur retournée par l'instruction Transact-SQL suivante :<br/>
**SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(ou légèrement supérieure, V12)*


*Remise :* Pendant la durée de la version préliminaire V12, vous bénéficiez d'un prix réduit. Le nouveau niveau S3 offre 100 UDBD et toutes les fonctionnalités disponibles dans le niveau Standard au prix de 0,2016 $/heure. Le prix est réduit à 0,1008 $/heure pour la version préliminaire V12. Pour plus d'informations, consultez [Tarification de la base de données SQL](http://azure.microsoft.com/pricing/details/sql-database/).


## 4. Précautions pour la version préliminaire V12


Il convient de noter les précautions suivantes concernant la mise à niveau vers la mise à jour V12 de base de données SQL Azure (aperçu) et ses conséquences.


### 4.1 Portail d'aperçu pour la V12


Seul le premier des deux portails de gestion Azure suivants prend en charge les bases de données V12 :


- [http://portal.azure.com/](http://portal.azure.com/)
 - Ce portail plus récent est à l'état de version préliminaire et n'est pas encore en disponibilité générale.<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - L'ancien portail ne sera pas mis à jour pour prendre en charge la V12.


Nous encourageons les clients à se connecter à leurs bases de données SQL Azure avec Visual Studio 2013 (VS2013). VS2013 peut être utilisé pour les tâches suivantes :


- Pour exécuter une instruction T-SQL.
- Pour concevoir un schéma.
- Pour développer une base de données en ligne ou hors connexion.


Vous pouvez également vous connecter avec [Visual Studio Community 2013](https://www.visualstudio.com/fr-fr/news/vs2013-community-vs.aspx/), qui est une version gratuite et complète de VS2013.


Dans l'ancien portail de gestion Azure, sur la page des bases de données, vous pouvez cliquer sur **Ouvrir dans Visual Studio** pour lancer VS2013 sur votre ordinateur pour la connexion à votre base de données SQL Azure.


Vous pouvez également utiliser SQL Server Management Studio (SSMS) 2014 avec [CU6](http://support.microsoft.com/kb/3031047/) pour vous connecter à la base de données SQL Azure. Plus de détails dans ce billet de blog :<br/>[Mises à jour des outils clients pour la base de données SQL Azure](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### 4.2 Précautions *during* la mise à niveau vers la V12


> [AZURE.NOTE]
> La base de données pre-V12 *remains available* pour accéder aux données pendant la mise à niveau vers la V12.


- Pour les bases de données de plus de 50 Go, la mise à niveau vers la V12 peut prendre jusqu'à 24 heures.
- Au cours de la mise à niveau d'une base de données vers un serveur V12 de la base de données SQL Azure, vous ne pouvez pas effectuer les actions suivantes sur le serveur jusqu'à la fin de la mise à niveau :
 - Créer une nouvelle base de données.
 - Copier une base de données sur le serveur.
 - Restaurer une base de données supprimée.
 - Restaurer une base de données à un point dans le temps.
 - Utiliser la géo-réplication.
- À partir du moment où la mise à niveau vers la V12 est terminée, le système a besoin de plusieurs minutes pour mettre à jour l'entrée DNS sur votre base de données de la V12. Votre application cliente peut se connecter à votre base de données après la mise à jour DNS.
- Les niveaux de tarification de service Web et Business ne sont pas pris en charge dans la V12. Ils ne seront pris en charge dans aucune version à venir.


### 4.3 Précautions *after* la mise à niveau vers la V12


- Aucune base de données mise à jour vers la V12 ne peut être restaurée vers la version antérieure.
- La remise de 50 % sur le coût de la base de données SQL Azure V12 est en vigueur dans toutes les régions jusqu'au mardi 31 mars 2015. La remise s'applique aux régions, pour l'aperçu et la disponibilité générale.


### 4.4 Exportation et importation *after* la mise à niveau vers la V12


Vous pouvez exporter ou importer une base de données V12 à l'aide du [portail web Azure](http://portal.azure.com/). Vous pouvez également l'exporter ou l'importer à l'aide des outils suivants :


- SQL Server Management Studio (SSMS) 2014
- Visual Studio 2013
- Infrastructure d'application de la couche Données (DacFx)


Toutefois, pour utiliser les outils, vous devez d'abord installer leurs dernières mises à jour pour vous assurer qu'ils prennent en charge les nouvelles fonctionnalités de la V12 :


- [Mise à jour cumulative 6 pour SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Mise à jour de février 2015 pour les outils de base de données SQL Server dans Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Infrastructure d'application de la couche Données (DacFx) de février 2015 pour la base de données SQL Azure V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Les liens des outils précédents ont été mis à jour le 2 mars 2015 ou après. Nous vous recommandons d'utiliser les mises à jour plus récentes de ces outils.


[2. État de disponibilité générale de la V12 par région]:#V12AzureSqlDbPreviewGaTable


<!-- EndOfFile -->


<!--HONumber=47-->
 