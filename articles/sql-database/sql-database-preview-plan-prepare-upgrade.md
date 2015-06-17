<properties 
	pageTitle="Planification et préparation de l'installation de la mise à niveau V12 de la base de données SQL" 
	description="Décrit les préparations et les limitations relatives à la mise à niveau vers la base de données SQL Azure V12." 
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


# Planification et préparation de l'installation de la mise à niveau V12 de la base de données SQL Azure


<!--
GeneMi , jeudi 5 mars 2015, 20 h 06
Supprimer 'Azure' du titre.
-->


Cette rubrique décrit la planification et la préparation nécessaires pour mettre à jour vos bases de données SQL Azure de la version V11 vers la V12.


Un nouveau [portail Azure](http://portal.azure.com/) est disponible pour prendre en charge la mise à jour vers la V12.


> [AZURE.NOTE]
> Les bases de données de test, les copies de base de données ou les nouvelles bases de données sont parfaitement adaptées à la mise à jour vers la version préliminaire. Il est préférable d'attendre la fin de la version préliminaire avant de mettre à jour les bases de données dont dépend votre activité.<br/><br/>
> Pour la famille de version V12, vous pouvez déterminer si votre zone géographique est en statut d'aperçu ou de disponibilité générale en examinant le tableau des régions dans [Nouveautés de la base de données SQL V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable).


Le tableau suivant répertorie et décrit les autres rubriques d'aide relatives à la V12.


| Titre et lien | Description du contenu |
| :--- | :--- |
| [Nouveautés de la base de données SQL V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/) | Décrit les détails du rapprochement de la parité complète de la base de données SQL Azure avec Microsoft SQL Server 2014 grâce à la V12. |
| [Procédure pas à pas : inscription à la dernière mise à jour V12 de la base de données SQL (version préliminaire)](http://azure.microsoft.com/documentation/articles/sql-database-preview-sign-up/) | Décrit la procédure de mise à jour de vos bases de données SQL Azure vers la dernière version préliminaire. |
| [Création d'une base de données dans la dernière mise à jour V12 de la base de données SQL (version préliminaire)](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) | Décrit la procédure de création d'une base de données SQL Azure qui inclut les nouvelles fonctionnalités de la dernière version préliminaire. Cette section décrit également les différentes options disponibles à partir d'une simple base de données vide. |
| [Installation de la dernière mise à niveau V12 de la base de données SQL (version préliminaire)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/) | Décrit la procédure de mise à niveau de vos bases de données SQL Azure vers la dernière version préliminaire. Cette section vous indique de commencer par créer une sauvegarde de votre base de données, car la mise à jour est définitive et ne peut pas être annulée. |


## A. Planification


Les sous-sections suivantes décrivent les connaissances nécessaires et les décisions à prendre avant d'effectuer la mise à jour de votre base de données SQL Azure vers la V12.

### A.1 Clarification de version


Ce document concerne la mise à jour de la base de données SQL Microsoft Azure de la version V11 à V12. De manière plus formelle, les numéros de version sont proches des deux valeurs suivantes, comme indiqué par l'instruction Transact-SQL **SELECT @@version;** :


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(ou légèrement supérieure, V12)*


### A.2 Planification du niveau de service


À partir de la V12, la base de données SQL Azure prend en charge uniquement les niveaux de service De base, Standard et Premium. Les niveaux de service Web et Business ne sont pas pris en charge dans la V12. Par conséquent, si vous prévoyez de mettre à jour votre base de données SQL Azure actuellement définie sur le niveau de service Web ou Business, vous devez choisir un niveau de service.


Pour des informations détaillées sur les niveaux de service De base, Standard et Premium, consultez :


- [Mise à niveau des bases de données SQL des éditions Web et Business vers les nouveaux niveaux de service](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)
- [Tarification des bases de données SQL Azure](http://azure.microsoft.com/pricing/details/sql-database/)


### A.3 Examen de la configuration de géo-réplication


Si votre base de données SQL Azure est configurée pour la géo-réplication, vous devez documenter la configuration actuelle et arrêter la géo-réplication avant de commencer à préparer la mise à jour. Une fois la mise à jour terminée, vous devez reconfigurer votre base de données pour la géo-réplication.


La stratégie consiste à laisser la source intacte et à effectuer les tests sur une copie de la base de données.


### A.4 Mise à jour sur place et copie de la base de données sur un nouveau serveur


Vous pourrez choisir entre deux techniques pour mettre à niveau votre base de données SQL Azure de V11 à V12 :


- Mise à jour sur place : à la fin de ce processus, votre base de données V11 n'existe plus. La version du serveur de base de données SQL est V12.
- Copie de la base de données sur le serveur V12 : ce processus ne modifie pas votre base de données V11 et crée une copie mise à jour de votre base de données V11 sur un serveur de base de données SQL Azure V12. <br/> Notez que les informations DNS utilisées par votre code client pour accéder à la base de données n'auront plus accès à la version V11 et que des modifications devront être apportées aux informations DNS pour accéder à la base de données V11.


## B. Préparation


Une fois que vous avez terminé votre planification, vous pouvez effectuer les actions décrites dans les sous-sections suivantes pour vous préparer à la phase finale de la mise à jour.


Une description détaillée de la dernière phase de la mise à jour est disponible à partir des liens vers les rubriques d'aide en haut de cette page.


### B.1 Actions relatives au niveau de service


Les niveaux de tarification de service Web et Business ne sont pas pris en charge dans la V12.


Si votre base de données SQL Azure V11 est une base de données Web ou Business, le processus de mise à jour vous propose de basculer votre base de données sur un niveau pris en charge. La mise à jour recommande un niveau qui correspond à l'historique de la charge de travail de votre base de données. Toutefois, vous pouvez choisir n'importe quel niveau pris en charge.


Vous pouvez réduire le nombre d'étapes nécessaires pour la mise à jour en changeant le niveau Web et Business de votre base de données V11 avant de commencer le processus. Pour ce faire, utilisez le portail Azure.


Si n'êtes pas sûr du niveau de service vers lequel basculer, choisissez le niveau S0 de l'édition Standard.


### B.2 Suspension de la géo-réplication pendant la mise à jour


La mise à jour vers la V12 ne peut pas s'exécuter si la géo-réplication est active sur votre base de données. Vous devez d'abord reconfigurer votre base de données pour qu'elle n'utilise plus la géo-réplication.


Une fois la mise à jour terminée, vous pouvez configurer votre base de données pour qu'elle utilise à nouveau la géo-réplication.


## <a id="limitations"></a>C. Limitations pendant et après la mise à jour vers la V12


Cette section décrit les limitations associées à la mise à niveau vers la base de données SQL Azure V12.


### C.1 Portail d'aperçu pour la V12


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


Vous pouvez également utiliser SQL Server Management Studio (SSMS) 2014 avec [CU6](http://support.microsoft.com/kb/3031047/) pour se connecter à la base de données SQL Azure. Plus de détails dans ce billet de blog :<br/>[Mises à jour des outils clients pour la base de données SQL Azure](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### C.2 Limitation *during* la mise à niveau vers la V12

Le tableau suivant décrit les limitations en vigueur pendant le processus de mise à niveau.


| Limitation | Description |
| :--- | :--- |
| Durée de la mise à niveau | Pour une base de données très volumineuse (éventuellement supérieure à 50 Go), la mise à niveau peut prendre jusqu'à 24 heures. |
| Délai de mise à jour de l'entrée DNS | Une fois la mise à niveau terminée, le système a besoin de plusieurs minutes pour mettre à jour l'entrée DNS de votre base de données V12 pour assurer la connectivité à partir de votre application cliente. |
| Aucune création de base de données | Pendant la mise à jour, les actions suivantes de création de base de données ne sont pas disponibles sur le serveur de base de données SQL Azure V12 de destination : <p></p> * Création d'une base de données <br/> * Copie d'une base de données sur le serveur <br/> * Restauration d'une base de données supprimée <br/> * Restauration d'une base de données jusqu'à une date et heure <br/><br/> Toutefois, la prise en charge de la restauration jusqu'à une date et heure pendant la mise à jour est une fonctionnalité qui peut être activée avant la fin de la période de la version préliminaire V12. |
| Aucune géo-réplication | La géo-réplication n'est pas prise en charge sur un serveur V12 impliqué dans une mise à niveau à partir de V11. |
| Règles d'alerte | Impossible d'ajouter des règles d'alerte à une base de données V12. |


### C.3 Limitation *after* la mise à niveau vers la V12

Le tableau suivant décrit les limitations qui en vigueur après le processus de mise à niveau.


| Limitation | Description |
| :--- | :--- |
| DNS vers la base de données V11 | À la fin de la mise à niveau par copie de votre base de données V11 vers un nouveau serveur V12, les informations DNS utilisées par votre code client pour se connecter font référence à la base de données V12. <p> </p> Votre code client doit utiliser des informations DNS modifiées pour refaire référence à la base de données V11. |
| Impossible de rétablir la V11 | Après une mise à niveau sur place, le résultat ne peut pas être restauré ou annulé. |
| Niveau Web ou Business | Une fois que la mise à niveau démarre, le serveur de la nouvelle base de données V12 ne peut plus reconnaître ou accepter le niveau de service Web ou Business. |
| Réduction de 50 % n'apparaissant pas dans les cartes de niveau de tarification dans le portail Azure | Pendant la durée de la version préliminaire, une remise de 50 %* sur les bases de données est proposée avec la dernière mise à jour vers la version préliminaire de la base de données SQL Azure (V12). La remise est appliquée même si elle n'est pas affichée dans le volet du niveau de tarification de service de la version préliminaire du portail.<br/><br/> La remise de 50 % reste en vigueur dans toutes les régions géographiques jusqu'au **31 mars 2015**, qui est la date d'expiration pour toutes les régions. La remise est effective même dans les régions dans lesquelles la disponibilité générale été annoncée.<br/><br/> (* L'utilisation de la fonctionnalité la plus récente de mise à jour de base de données V12 de SQL Azure est soumise aux conditions de votre contrat de licence (par exemple, l'accord Entreprise, l'accord Microsoft Azure ou le contrat d'abonnement Microsoft Online), ainsi qu'à toutes les [Conditions d'utilisation supplémentaires de versions préliminaires de Microsoft Azure](http://azure.microsoft.com/support/legal/preview-supplemental-terms/) applicables.  Pendant la durée de la version d'évaluation, Microsoft vous facturera (ou votre revendeur, le cas échéant) pour toutes les bases de données inscrites dans cette version d'évaluation à la moitié du prix de la disponibilité générale, soit une remise de 50 %. Microsoft vous informera par courrier électronique 30 jours avant l'expiration de la période d'évaluation et de la remise.) |


### C.4 Exportation et importation *after* la mise à niveau vers la V12


Vous pouvez exporter ou importer une base de données V12 à l'aide du [portail web Azure](http://portal.azure.com/). Vous pouvez également l'exporter ou l'importer à l'aide des outils suivants :


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Infrastructure d'application de la couche Données (DacFx)


Toutefois, pour utiliser les outils, vous devez d'abord installer leurs dernières mises à jour pour vous assurer qu'ils prennent en charge les nouvelles fonctionnalités de la V12 :


- [Mise à jour cumulative 6 pour SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Mise à jour de février 2015 pour les outils de base de données SQL Server dans Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Infrastructure d'application de la couche Données (DacFx) de février 2015 pour la base de données SQL Azure V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Les liens des outils précédents ont été mis à jour le 2 mars 2015 ou après. Nous vous recommandons d'utiliser les mises à jour plus récentes de ces outils.


#### C.4.1 Problème temporaire lors de l'importation avec les fichiers bacpac


 (Daté du mercredi 18 février 2015)


Il existe un problème connu avec l'exportation de fichiers bacpac à partir d'un serveur de base de données SQL Azure qui a été mis à niveau vers la V12. Les fichiers bacpac exportés contiennent un objet errant nommé script_deployment_databases. Les fichiers bacpac contenant cet objet errant ne peuvent pas être importés en utilisant les outils SQL Server Management Studio (SSMS), SqlPackage.exe ou l'API Infrastructure d'application de la couche Données (DacFx).


Toutefois, le portail web Azure permet d'importer un fichier bacpac affecté vers la base de données SQL Azure. Un correctif pour résoudre ce problème de manière permanente sera publié aux alentours du vendredi 27 février 2015. Il comprendra notamment une mise à jour pour les outils affectés. En attendant, contactez le support Microsoft si vous avez besoin d'aide pour récupérer un fichier déjà exporté avec une extension .bacpac.


Outre le problème d'exportation, un nombre limité de serveurs et de clients ayant récemment effectué la mise à niveau vers la version V12 peut rencontrer une autre erreur lors de l'importation d'un fichier bacpac. Cette erreur relatives aux autorisations est temporaire et est généralement résolue sur un serveur touché en une journée. Nous espérons apporter une solution définitive à ce problème pendant la semaine du 23 février 2015 également. En attendant, une nouvelle tentative de l'opération d'importation peut réussir. Contactez le support Microsoft si vous avez besoin d'une aide supplémentaire lors de l'importation d'un fichier bacpac vers la base de données SQL Azure.


Si nécessaire, vous pouvez suivre ces étapes pour contacter le support technique de Microsoft :


1. Accédez au portail Azure.
2. Cliquez avec le bouton droit sur le nom de compte, qui se trouve dans le coin supérieur droit.
3. Dans le menu contextuel qui s'affiche, cliquez sur l'élément pour le support.
 - L'élément est probablement intitulé **Contacter le support technique Microsoft** ou **Aide + support**.


> [AZURE.NOTE] (lundi 2 mars 2015)
> 
> Le problème décrit dans la section C.4.1 a été résolu. Aucune connaissance ou action particulière n'est nécessaire pour que les clients puissent procéder comme ils le souhaitent.
> 
> L'importation/exportation automatique fonctionne normalement, quel que soit l'ancienneté votre fichier bacpac.
> 
> Les clients qui traitent leurs fichiers bacpac à l'aide de DacFx doivent télécharger les mises à jour des outils. Les liens de téléchargement sont disponibles dans la section C.4.


### C.5 Restauration vers V12 d'une base de données V11 supprimée

Le scénario suivant explique qu'une base de données SQL Azure V11 supprimée peut être restaurée sur un serveur de base de données SQL Azure V12.

1. Supposons que vous disposez d'un serveur de base de données SQL Azure V11. <br/> Sur le serveur, votre base de données est configurée sur le niveau de service Web et Business obsolète.
2. Vous supprimez la base de données.
3. Vous mettez à jour le serveur vers V12.
4. Ensuite, vous restaurez la base de données sur le serveur. <br/> La base de données est ainsi mise à jour vers V12, au niveau S0 de l'édition Standard du service.
5. Vous pouvez basculer la base de données vers n'importe quel niveau de service pris en charge, si S0 ne vous convient pas.



## D. Résolution des problèmes

Si la mise à jour échoue pour une raison quelconque, votre base de données V11 reste active et disponible comme d'habitude.


## Liens connexes

- [Aperçu des fonctionnalités ](http://azure.microsoft.com/services/preview/) de Microsoft Azure


<!--Anchors-->
[Sous-titre 1]: #subheading-1
<!--HONumber=47-->
 