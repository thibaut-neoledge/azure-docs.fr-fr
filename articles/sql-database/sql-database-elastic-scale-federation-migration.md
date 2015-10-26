<properties 
    pageTitle="Migration de fédérations" 
    description="Décrit la procédure de migration d’une application existante créée avec la fonctionnalité Fédérations vers un modèle de base de données élastique." 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="sidneyh" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/14/2015" 
    ms.author="sidneyh"/>

# Migration de fédérations 

La fonctionnalité de fédérations de Base de données SQL Azure sera déclassée en septembre 2015 en même temps que les éditions Web/Business. À cette date, les applications qui utilisent la fonctionnalité de fédérations cesseront de s’exécuter. Pour garantir une migration réussie, il est vivement recommandé que les efforts de migration commencent dès que possible afin de permettre une planification et une exécution respectant les délais.

Si votre application n'est pas encore prête à fonctionner sans fédération, contactez le support technique Microsoft en suivant les instructions mentionnées [ici](https://support.microsoft.com/kb/3087180).

Ce document fournit le contexte, des exemples et une introduction à l’utilitaire de migration de fédérations et illustre comment migrer correctement une application de fédérations actuelle en toute transparence vers les API de la [bibliothèque cliente des bases de données élastiques](http://go.microsoft.com/?linkid=9862592) pour le partitionnement. L’objectif du document est de vous guider tout au long des étapes proposées pour migrer une application de fédérations sans aucun déplacement de données.

Il existe trois étapes principales de migration d’une application de fédérations existante vers une application qui utilise les outils de bases de données élastiques.

1. [Créer un gestionnaire de mappages de partitions à partir d’une racine de fédération](#create-a-shard-map-manager-from-a-federation-root) 
2. [Modifier l’application existante](#modify-the-existing-application)
3. [Extraire les membres de la fédération existante](#switch-out-existing-federation-members)
    

### Exemple d’outil de migration
Pour faciliter ce processus, un [utilitaire de migration de fédérations](http://go.microsoft.com/?linkid=9862613) a été créé. Il effectue les étapes 1 et 3.

## Créer un gestionnaire de mappages de partitions à partir d’une racine de fédération
La première étape de la migration d’une application de fédérations consiste à cloner les métadonnées d’une racine de fédération dans les constructions d’un gestionnaire de cartes de partitions.

![Cloner la racine de fédération dans le gestionnaire de mappages de partitions][1]
 
Commencez avec une application de fédérations existante dans un environnement de test.
 
Utilisez l’**utilitaire de migration de fédérations** pour cloner les métadonnées d’une racine de fédération dans les constructions du [gestionnaire de mappages de partitions](http://go.microsoft.com/?linkid=9862595) de la bibliothèque cliente de bases de données élastiques. Similaire à une racine de fédération, la base de données du gestionnaire des cartes de partitions est une base de données autonome qui comporte les cartes de partitions (c’est-à-dire les fédérations), les références aux partitions (c’est-à-dire, les membres de la fédération) et les mappages de plages respectifs.

Le clonage de la racine de fédération dans le gestionnaire de cartes de partitions est une copie et une traduction des métadonnées. Aucune métadonnée n’est modifiée dans la racine de fédération. Notez que le clonage de la racine de fédération avec l'utilitaire de migration de fédérations est une opération de point-à-temps, et qu'aucune modification apportée à la racine de fédération ou aux cartes de partitions n'est pas répercutée dans l'autre magasin de données respectif. Si des modifications sont apportées à la racine de fédération durant le test des nouvelles API, l’utilitaire de migration de fédérations peut servir à actualiser les cartes de partitions afin de représenter l’état actuel.

![Migrer l’application existante pour utiliser les API des outils de bases de données élastiques][2]

## Modifier l’application existante 

Une fois le gestionnaire de mappages de partitions en place et les membres et plages de la fédération inscrits auprès de ce dernier (opération effectuée via l’utilitaire de migration), vous pouvez modifier l’application de fédérations existante de telle sorte qu’elle utilise la bibliothèque cliente de bases de données élastiques. Comme indiqué dans la figure ci-dessus, les connexions de l’application via les API sont acheminées par l’intermédiaire du gestionnaire de mappages de partitions vers les membres de la fédération appropriés (désormais une partition aussi). Le mappage des membres de la fédération sur le gestionnaire des mappages de partitions active deux versions d’une application : l’une qui utilise les fédérations et l’autre qui utilise la bibliothèque cliente de bases de données élastiques. Elles doivent être exécutées côte à côte pour vérifier la fonctionnalité.

Lors de la migration de l’application, deux modifications principales doivent être apportées à l’application existante.


#### Modification 1 : instancier un objet du gestionnaire de mappages de partitions : 

Contrairement aux fédérations, les API des outils de bases de données élastiques interagissent avec le gestionnaire de mappages de partitions via la classe **ShardMapManager**. L’instanciation d’un objet **ShardMapManager** et d’un mappage de partitions peut être effectuée comme suit :
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### Modification 2 : diriger les connexions vers la partition appropriée 

Avec les fédérations, une connexion est établie avec un membre spécifique de la fédération via la commande USE FEDERATION, comme suit :

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Avec les API des outils de bases de données élastiques, une connexion à une partition spécifique est établie via le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) avec la méthode **OpenConnectionForKey** sur la classe **RangeShardMap**.

    //Connect and issue queries on the shard with key=100 
    using (SqlConnection conn = rangeShardMap.OpenConnectionForKey(100, csb))  
    { 
         using (SqlCommand cmd = new SqlCommand()) 
         { 
            cmd.Connection = conn; 
            cmd.CommandText = "SELECT * FROM customer";
     
            using (SqlDataReader dr = cmd.ExecuteReader()) 
            { 
                  //Perform action on dr 
            } 
        } 
    }

Les étapes décrites dans cette section sont nécessaires mais ne conviennent pas à tous les scénarios de migration qui se présentent. Pour plus d’informations, consultez la [Présentation conceptuelle des outils de bases de données élastiques](sql-database-elastic-scale-introduction.md) et la [Référence des API](http://go.microsoft.com/?linkid=9862604).

## Extraction des membres de la fédération existante 

![Extraire les membres de la fédération pour les partitions][3]

Une fois que l’application a été modifiée avec l’inclusion des API des outils de bases de données élastiques, la dernière étape de la migration d’une application de fédérations est d’utiliser l’instruction **SWITCH OUT** pour extraire les membres de la fédération (pour plus d’informations, consultez la référence MSDN [ALTER FEDERATION (base de données SQL Azure.aspx](http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx)). Le résultat final de l’émission d’une instruction **SWITCH OUT** pour un membre spécifique de la fédération est la suppression de toutes les contraintes et métadonnées de la fédération transformant ainsi le membre de la fédération en une base de données SQL Azure classique, similaire à toutes les autres bases de données SQL Azure.

L’émission d’une instruction **SWITCH OUT** pour un membre de la fédération est une opération unidirectionnelle qui ne peut pas être annulée. Une fois exécutée, la base de données qui en résulte ne peut pas être ajoutée à une fédération et les commandes USE FEDERATION ne fonctionnent plus pour cette base de données.

Pour exécuter l’extraction, un argument supplémentaire a été ajouté à la commande ALTER FEDERATION afin d’extraire un membre de la fédération. Bien que la commande puisse être émise pour des membres spécifiques de la fédération, l’utilitaire de migration de fédérations fournit des fonctionnalités permettant d’itérer par programmation chaque membre de la fédération et d’effectuer l’opération d’extraction.

Une fois que l’extraction a été exécutée sur tous les membres de la fédération existante, la migration de l’application est terminée. Lorsque tous les membres de fédération ont été extraits, nous suggérons l’exécution d’une opération DROP FEDERATION sur la racine. Cela n’affecte aucun des anciens membres, mais permet de facilement migrer la base de données à partir de l’édition Web et Business.
  
L’utilitaire de migration de fédérations permet d’effectuer les opérations suivantes :

1.    Cloner une racine de fédération dans un gestionnaire de cartes de partitions. Vous pouvez choisir de placer le gestionnaire de cartes de partitions existant sur une nouvelle base de données SQL Azure (recommandé) ou sur la base de données de la racine de fédération existante.
2.    D’émettre l’instruction SWITCH OUT pour tous les membres d’une fédération.


## Comparaison des fonctionnalités

Bien que les outils de bases de données élastiques offrent de nombreuses fonctionnalités supplémentaires (par exemple, [l’interrogation de plusieurs partitions](sql-database-elastic-scale-multishard-querying.md), [, le fractionnement et la fusion de partitions](sql-database-elastic-scale-overview-split-and-merge.md), [l’élasticité des partitions](sql-database-elastic-scale-elasticity.md), [la mise en cache côté client](sql-database-elastic-scale-shard-map-management.md), etc.), il existe quelques fonctionnalités intéressantes des fédérations qui ne sont pas prises en charge par les outils de bases de données élastiques.
  
- Utilisation de **FILTERING=ON**. Au lieu de cela, il est recommandé d’utiliser la sécurité au niveau des lignes pour le filtrage de ligne. Comme le filtrage de fédérations, la sécurité au niveau des lignes ajoute automatiquement un prédicat à toutes les requêtes sur une table partitionnée. Pour plus d’informations, consultez [Applications mutualisées avec les outils de bases de données élastiques et la sécurité au niveau des lignes](sql-database-elastic-tools-multi-tenant-row-level-security.md). 
 
 Une autre solution consiste à créer la logique de filtrage dans la requête émise sur la partition. Par exemple :

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

 Donne le même résultat que :

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- La fonctionnalité **Fractionnement** des outils de bases de données élastiques n’est pas entièrement en ligne. Pendant une opération de fractionnement, chaque shardlet spécifique est mis hors connexion pendant la durée du déplacement.
- Elle nécessite l’approvisionnement manuel des bases de données et la gestion des schémas.

## Considérations supplémentaires

* Les éditions Web et Business seront déclassées à l’automne 2015, tout comme la fonctionnalité de fédérations. Dans le cadre de la migration d’une application de fédérations, il est vivement recommandé d’effectuer des tests de performances sur les éditions De base, Standard et Premium. 

* L’exécution de l’instruction SWITCH OUT pour un membre de la fédération permet à la base de données qui en résulte de tirer parti de toutes les fonctionnalités des bases de données SQL Azure (par exemple, les nouvelles éditions, la sauvegarde, le PITR, l’audit, etc.)

##Vous avez besoin de plus de temps pour effectuer la migration ? 
Si votre application n'est pas encore prête à fonctionner sans fédération, contactez le support technique Microsoft en suivant les instructions mentionnées [ici](https://support.microsoft.com/kb/3087180).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
[Create Shard Map Manager from a Federation Root]: #create-shard-map-manager
[Modify the Existing Application]: #Modify-the-Existing-Application
[Switch Out Existing Federation Members]: #Switch-Out-Existing-Federation-Members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
 

<!---HONumber=Oct15_HO3-->