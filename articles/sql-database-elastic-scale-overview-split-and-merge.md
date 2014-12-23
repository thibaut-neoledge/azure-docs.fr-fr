<properties title="Splitting and Merging with Elastic Scale" pageTitle="Fractionnement et fusion avec l'infrastructure élastique" description="Explains how to manipulate shards and move data via a self-hosted service using Elastic Scale APIs." metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Fractionnement et fusion avec l'infrastructure élastique

Les applications basées sur la base de données SQL Azure font face à des défis lorsque leurs données ou besoins de traitement ne tiennent plus sur une unité d'échelle unique dans la base de données SQL Azure. Par exemple, il s'agit des applications qui deviennent virales ou pour lesquelles un ensemble spécifique de locataires s'étend au-delà des limites d'une seule base de données SQL Azure. Le **service de fractionnement/fusion** de l'infrastructure élastique simplifie considérablement ce problème. 

Cette description du service de fractionnement/fusion gère les mises à l'échelle (réduction ou augmentation) en modifiant le nombre de bases de données Azure et en équilibrant la distribution des **shardlets** entre elles. (Pour une définition des termes, consultez le [Glossaire de l'infrastructure élastique](./sql-database-elastic-scale-glossary.md)). 

Avec les choix actuels entre les éditions de base de données SQL Azure, la capacité peut également être gérée par la mise à l'échelle vers le haut ou vers le bas de la capacité d'une seule base de données SQL Azure. La dimension de la mise à l'échelle vers le haut/bas de la gestion des capacités élastiques n'est pas abordée dans cette rubrique. Consultez à la place la rubrique [Élasticité des partitions de l'infrastructure élastique](./sql-database-elastic-scale-elasticity.md). 

## Scénarios de fractionnement/fusion 
Les applications doivent étirer la flexibilité au-delà des limites d'une seule base de données SQL Azure, comme illustré dans les scénarios suivants : 

* **Augmentation de la capacité - Plages de fractionnement** : la possibilité d'augmenter la capacité d'agrégation de la couche Données répond à des besoins de capacité croissants. Dans ce scénario, l'application fournit la capacité supplémentaire par le partitionnement de données et en la répartissant de manière incrémentielle entre plusieurs bases de données jusqu'à ce que les besoins en capacité soient satisfaits. La fonction " fractionner " du service de fractionnement/fusion de l'infrastructure élastique correspond à ce scénario. 

* **Réduction de la capacité - Plages de fusion** : la capacité varie en raison du caractère saisonnier de l'entreprise. Ce scénario souligne la nécessité d'évoluer avec moins d'unités d'échelle lorsque l'activité ralentit. La fonctionnalité " fusionner " du service de fractionnement/fusion de l'infrastructure élastique traite cette exigence. 

* **Gestion des zones réactives - Déplacement des shardlets** : avec plusieurs locataires par base de données, l'allocation de shardlets aux partitions peut entraîner des goulots d'étranglement sur certaines partitions. Cela nécessite la réaffectation des shardlets ou le déplacement des shardlets occupés vers des partitions nouvelles ou moins utilisées. 

Dans la suite de cette rubrique, nous ferons référence aux traitements du service sous la forme de demandes de **fractionnement/fusion/déplacement**. 


Figure 1 : vue d'ensemble conceptuelle du service de fractionnement/fusion


![Overview][1] 


**Remarque** :  Tous les scénarios d'**augmentation de la capacité** ne requièrent pas le service de fractionnement/fusion. Par exemple, si vous créez régulièrement de nouvelles partitions dans votre environnement pour stocker les nouvelles données avec des valeurs de clé de partitionnement, vous pouvez utiliser les API clientes de gestion des cartes de partitions pour diriger les nouvelles plages de données vers les partitions nouvellement créées. Le service de fractionnement/fusion n'est nécessaire que lorsque les données existantes doivent être également déplacées.

## Concepts et fonctionnalités clés

**Services hébergés par le client** : le service de fractionnement/fusion est fourni comme un service hébergé par le client. Vous devez déployer et héberger le service dans votre abonnement Microsoft Azure. Le package que vous téléchargez à partir de NuGet comporte un modèle de configuration à réaliser ainsi que les informations relatives à votre déploiement. Pour plus d'informations, consultez le [didacticiel du service de fractionnement/fusion](./sql-database-elastic-scale-configure-deploy-split-and-merge.md). Étant donné que le service s'exécute dans votre abonnement Azure, vous pouvez contrôler et configurer la plupart des aspects de sécurité du service. Le modèle par défaut comprend les options permettant de configurer SSL, l'authentification client basée sur certificat, la protection contre le déni de service et les restrictions d'adresse IP. Vous trouverez plus d'informations sur la sécurité dans le document suivant [Considérations de sécurité de l'infrastructure élastique](./sql-database-elastic-scale-configure-security.md).

Le service déployé par défaut s'exécute avec un rôle de travail et un rôle web. Chacun utilise la taille de machine virtuelle A1 dans Azure Cloud Services. Même si vous ne pouvez pas modifier ces paramètres lors du déploiement du package, vous pouvez les modifier après un déploiement dans le service cloud en cours d'exécution (via le portail Azure). Notez que le rôle de travail ne doit pas être configuré pour plus d'une instance unique pour des raisons techniques. 

**Intégration des cartes de partitions** : le service de fractionnement/fusion interagit avec la carte de partitions de l'application. Lorsque vous utilisez le service de fractionnement/fusion pour fractionner ou fusionner des plages ou déplacer des shardlets entre les partitions, le service conserve automatiquement la carte de partitions à jour. Pour ce faire, le service se connecte à la base de données du gestionnaire des cartes de partitions de l'application et gère les plages et les mappages au fur et à mesure de l'avancement des demandes de fractionnement/fusion/déplacement. Cela garantit que la carte de partitions présente toujours une vue à jour lorsque les opérations de fractionnement/fusion sont en cours. Les opérations de fractionnement, fusion et déplacement des shardlets sont implémentées en déplaçant un lot de shardlets à partir de la partition source vers la partition cible. Au cours de l'opération de déplacement des shardlets, les shardlets du lot en cours sont marqués comme étant hors connexion dans la carte de partitions et ne sont pas disponibles pour les connexions de routage dépendant des données utilisant l'API **OpenConnectionForKey**. 

**Connexions cohérentes des shardlets** : lorsque le déplacement des données débute pour un nouveau lot de shardlets, les connexions du routage dépendant des données à la partition stockant le shardlet sont supprimées et les connexions ultérieures à partir des API de la carte de partitions à ces shardlets sont bloquées pendant le déplacement des données afin d'éviter les incohérences. Les connexions aux autres shardlets sur la même partition seront également supprimées mais réussiront immédiatement lors d'une nouvelle tentative. Une fois le lot déplacé, les shardlets sont marqués comme étant à nouveau en ligne pour la partition cible et la source des données est supprimée de la partition source. Le service effectue ces étapes pour chaque lot jusqu'à ce que tous les shardlets aient été déplacés. Cela entraîne plusieurs opérations de suppression des connexions au cours de l'opération de fractionnement/fusion/déplacement.   

**Gestion de la disponibilité des shardlets** : la limitation de la suppression des connexions au lot en cours de shardlets comme indiqué ci-dessus restreint la portée de l'indisponibilité à un lot de shardlets à la fois. Cette approche est préférée à celle selon laquelle la partition complète reste hors connexion pour tous ses shardlets au cours d'une opération de fractionnement/fusion. La taille d'un lot, défini comme le nombre de shardlets distincts à déplacer à la fois, est un paramètre de configuration. Elle peut être définie pour chaque opération de fractionnement/fusion en fonction des besoins en termes de disponibilité et de performances de l'application. Notez que la plage verrouillée dans la carte de partitions peut être supérieure à la taille de lot spécifiée. En effet, le service sélectionne la taille de la plage de telle sorte que le nombre réel de valeurs de clé de partitionnement contenu dans les données correspond environ à la taille du lot. Ce point est important, en particulier pour les clés de partitionnement peu remplies. 

**Stockage des métadonnées** : le service de fractionnement/fusion utilise une base de données pour mettre à jour son état et conserver les journaux pendant le traitement des demandes. L'utilisateur crée cette base de données dans son abonnement et lui fournit la chaîne de connexion dans le fichier de configuration pour le déploiement du service. Les administrateurs de l'organisation de l'utilisateur peuvent également se connecter à cette base de données pour examiner la progression de la demande et pour analyser des informations détaillées sur les défaillances potentielles.

**Détection de partitionnements** : le service de fractionnement/fusion fait la distinction entre (1) les tables partitionnées, (2) les tables de référence et (3) les tables normales. La sémantique d'une opération de fractionnement/fusion/déplacement dépend du type de la table utilisée et est définie comme suit : 

* **Tables partitionnées** : les opérations de fractionnement/fusion/déplacement déplacent les shardlets de la partition source vers la partition cible. Après l'achèvement réussi de la demande générale, ces shardlets ne sont plus présents dans la partition source. Notez que les tables cibles doivent figurer sur la partition cible et ne doivent pas contenir de données dans la plage cible avant le traitement de l'opération. 

-    **Tables de référence** : pour les tables de référence, les opérations de fractionnement, fusion et déplacement copient les données de la partition source vers la partition cible. Notez, néanmoins, qu'aucune modification n'intervient sur la partition cible pour une table donnée si une ligne est déjà présente dans cette table de la cible. La table doit être vide pour qu'une opération de copie de table de référence soit traitée.

-    **Autres tables** : d'autres tables peuvent être présentes sur la source ou la cible d'une opération de fractionnement/fusion. Le fractionnement/la fusion ignore ces tables pour les opérations de copie ou de déplacement des données. Notez, toutefois, qu'elles peuvent interférer avec ces opérations en présence de contraintes.

Les informations de comparaison des tables de référence et des tables partitionnées sont fournies par les API **SchemaInfo** dans la carte de partitions. L'exemple suivant illustre l'utilisation de ces API sur un objet " smm " donné du gestionnaire des cartes de partitions : 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Les tables " region " et " nation " sont définies comme des tables de référence et sont copiées avec les opérations de fractionnement/fusion/déplacement. Les tables " customer " et " orders " sont définies en tant que tables partitionnées. C_CUSTKEY et O_CUSTKEY servent de clé de partitionnement. 

**Intégrité référentielle** : le service de fractionnement/fusion analyse les dépendances entre les tables et utilise des relations de clé primaire/clé étrangère pour préparer les opérations de déplacement des tables de référence et des shardlets. En général, les tables de référence sont d'abord copiées dans l'ordre de dépendance, puis les shardlets sont copiés dans l'ordre de leurs dépendances au sein de chaque lot. Cela est nécessaire afin que les contraintes de clé primaire/clé étrangère sur la partition cible soient respectées lorsque les nouvelles données arrivent. 

**Cohérence des cartes de partitions et achèvement éventuel** : en cas de défaillance, le service de fractionnement/fusion reprend les opérations après toute interruption et entreprend de terminer toutes les demandes en cours. Toutefois, des situations irrécupérables peuvent survenir, par exemple, lorsque la partition cible est perdue ou irréparable. Dans ces circonstances, certains shardlets qui devaient être déplacés peuvent continuer à se trouver sur la partition source. Le service garantit que les mappages de shardlets ne sont mis à jour qu'une fois que les données nécessaires ont été copiées avec succès sur la cible. Les shardlets ne sont supprimés de la source qu'une fois que toutes leurs données ont été copiées vers la cible et que les mappages correspondants ont été mis à jour. L'opération de suppression se produit en arrière-plan alors que la plage est déjà en ligne sur la partition cible. Le service de fractionnement/fusion garantit systématiquement l'exactitude des mappages stockés dans le mappage de partitions.

## Obtention des fichiers binaires du service

Les fichiers binaires du service de fractionnement/fusion sont fournis via [Nuget](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Consultez le [didacticiel étape par étape du service fusion/fractionnement](./sql-database-elastic-scale-configure-deploy-split-and-merge.md) pour plus d'informations sur le téléchargement des fichiers binaires.

## Interface utilisateur du service de fractionnement/fusion

Outre son rôle de travail, le package du service de fractionnement/fusion inclut également un rôle web qui peut être utilisé pour envoyer des demandes de fractionnement/fusion de manière interactive. Les principaux composants de l'interface utilisateur sont les suivants :

-    Type d'opération : le type d'opération est une case d'option qui contrôle le type d'opération effectué par le service pour une demande. Vous pouvez choisir entre les scénarios de fractionnement, de fusion et de déplacement présentés dans la section Concepts et fonctionnalités clés. En outre, vous pouvez également annuler une opération précédemment soumise.

-    Carte de partitions : la section suivante de paramètres de demande aborde les informations relatives à la carte de partitions et à la base de données qui l'héberge. En particulier, vous devez fournir le nom du serveur de base de données SQL Azure et de la base de données hébergeant la carte de partitions, les informations d'identification pour se connecter à la base de données de mappage et, enfin, le nom de la carte. Actuellement, l'opération n'accepte qu'un seul ensemble d'informations d'identification. Ces dernières doivent disposer d'autorisations suffisantes pour apporter des modifications à la carte de partitions ainsi qu'aux données utilisateur des partitions.

-    Plage source (fractionner/fusionner) : pour l'opération de fractionnement/fusion, une demande doit comporter les clés basse et haute de la plage source de la partition source. Actuellement, vous devez spécifier les clés exactement comme elles surviennent dans les mappages de votre carte de partitions. Vous pouvez utiliser le script PowerShell GetMappings.ps1 pour récupérer les mappages actuels dans une carte de partitions donnée.

-    Fractionner la clé et le comportement (fractionner) : pour les opérations de fractionnement, vous devez également définir le moment auquel vous souhaitez fractionner la plage source. Vous effectuez cette opération en fournissant la clé de partitionnement à l'emplacement où vous souhaitez effectuer le fractionnement. Utilisez ensuite la case d'option pour définir si vous souhaitez déplacer la partie inférieure de la plage (à l'exception de la clé de fractionnement) ou si vous souhaitez déplacer la partie supérieure (y compris la clé de fractionnement).

-    Shardlet source (déplacer) : les opérations de déplacement sont différentes des opérations de fractionnement ou de fusion car elles ne nécessitent pas de plage pour décrire la source. Une source de déplacement est simplement identifiée par la valeur de clé de partitionnement que vous souhaitez déplacer.

-    Partition cible (fractionner) : une fois que vous avez fourni les informations sur la source de l'opération de fractionnement, vous devez définir l'emplacement de copie des données en fournissant le serveur de la base de données SQL Azure et le nom de la base de donnée correspondant à la cible.

-    Plage cible (fusionner) : opérations de fusion à la place du déplacement des shardlets vers une partition existante. Vous identifiez la partition existante en fournissant les limites de la plage existante avec laquelle vous souhaitez fusionner.

-    Taille du lot : la taille du lot contrôle le nombre de shardlets qui passeront en mode hors connexion en même temps pendant le déplacement des données. Il s'agit d'une valeur entière vous permettant d'utiliser des valeurs plus petites lorsque vous êtes exposé à de longues périodes de temps mort pour les shardlets. Des valeurs plus grandes augmentent le temps pendant lequel un shardlet donné est hors connexion mais peuvent améliorer les performances.

-    ID de l'opération (annuler) : si une opération en cours n'est plus nécessaire, vous pouvez l'annuler en fournissant son ID dans ce champ. Vous pouvez récupérer l'ID de l'opération dans la table des états de demande (voir Section 8.1) ou dans la sortie du navigateur web dans lequel vous avez envoyé la demande.


## Spécifications et limitations 

L'implémentation actuelle du service de fractionnement/fusion est soumise aux spécifications et limitations suivantes : 

* Actuellement, les partitions doivent exister et être enregistrées dans la carte de partitions pour qu'une opération de fractionnement/fusion sur ces partitions puisse être effectuée. 

* Le service de fractionnement/fusion ne crée actuellement pas automatiquement des tables ou d'autres objets de base de données dans le cadre de ses opérations. Cela signifie que le schéma pour toutes les tables partitionnées et les tables de référence doit exister sur la partition cible avant toute opération de fractionnement/fusion/déplacement. Les tables partitionnées en particulier doivent être vides dans la plage où de nouveaux shardlets doivent être ajoutés par une opération de fractionnement/fusion/déplacement. Sinon, l'opération fait échouer la vérification de cohérence initiale sur la partition cible. Notez également que les données de référence ne sont copiées que si la table de référence est vide et qu'il n'existe aucune garantie de cohérence en ce qui concerne les autres opérations simultanées d'écriture sur les tables de référence. Nous recommandons, lors des opérations de fractionnement/fusion, qu'il n'y ait aucune autre opération d'écriture apportant des modifications aux tables de références.

* Actuellement, le service s'appuie sur l'identité de ligne définie par un index unique ou une clé incluant la clé de partitionnement pour améliorer les performances et la fiabilité des shardlets volumineux. Cela permet au service de déplacer des données à une granularité encore plus fine que la valeur de clé de partitionnement. Cela permet de réduire la quantité maximale de l'espace de journalisation ainsi que le nombre de verrous requis lors de l'opération. Envisagez de créer un index unique ou une clé primaire incluant la clé de partitionnement sur une table donnée si vous souhaitez utiliser cette table avec des demandes de fusion/fractionnement/déplacement. Pour des raisons de performances, la clé de partitionnement doit être la première colonne de la clé ou de l'index.

* Au cours du traitement des demandes, des données de shardlet peuvent être présentes à la fois sur la partition source et sur la partition cible. Cette présence est actuellement nécessaire à la protection contre les défaillances pendant le déplacement de shardlets. Comme expliqué ci-dessus, l'intégration du service de fractionnement/fusion au mappage de partitions de l'infrastructure élastique permet de s'assurer que les connexions via les API de routage dépendant des données utilisant la méthode **OpenConnectionForKey** sur la carte de partitions ne constatent pas d'états intermédiaires incohérents. Toutefois, lors de la connexion aux partitions source ou cible sans utiliser la méthode **OpenConnectionForKey**, des états intermédiaires incohérents peuvent être visibles lors de l'exécution de demandes de fractionnement/fusion/déplacement. Ce type de connexion peut afficher des résultats partiels ou en double en fonction de la synchronisation ou de la partition sous-jacente à la connexion. Actuellement, cette limitation inclut les connexions établies par les interrogations de plusieurs partitions de l'infrastructure élastique.

* Le service de fractionnement/fusion ne prend actuellement pas en charge plusieurs instances de rôle de travail. Cela exclut les configurations à haute disponibilité dans Azure utilisant des domaines d'erreur ou de mise à niveau qui dépendent de la possibilité d'exécuter plusieurs instances de rôle. En outre, la base de données de métadonnées de fractionnement/fusion ne doit pas être partagée entre différentes instances. Par exemple, une instance du service de fractionnement/fusion en cours d'exécution intermédiaire doit pointer vers une base de données de métadonnées différente de l'instance en cours d'exécution de production.
 

## Facturation 

Étant donné que le service de fractionnement/fusion s'exécute comme un service cloud dans votre abonnement Microsoft Azure, les frais normaux appliqués aux services cloud s'appliquent pour votre instance de fractionnement/fusion. Sauf si vous effectuez fréquemment des opérations de fractionnement/fusion/déplacement, nous vous recommandons de supprimer votre service cloud de fractionnement/fusion. Vous économiserez ainsi en coûts d'exécution ou de déploiement d'instances de service cloud. Vous pouvez redéployer et démarrer votre configuration facilement exécutable chaque fois que vous avez besoin d'effectuer des opérations de fractionnement/fusion. 
  
## Surveillance 
### Tables d'états 

Le service de fractionnement/fusion fournit la table **RequestStatus** dans la base de données de stockage des métadonnées pour la surveillance des demandes terminées et en cours. La table répertorie une ligne pour chaque demande de fractionnement/fusion qui a été soumise à cette instance du service de fractionnement/fusion. Elle donne les informations suivantes pour chaque demande :

* **Timestamp** : heure et date de début de la demande.

* **OperationId** : GUID qui identifie de façon unique la demande. Cette demande peut également servir à annuler l'opération alors qu'elle est encore en cours.

* **Status** : état actuel de la demande. Pour les demandes en cours, répertorie également la phase dans laquelle la demande se trouve.

* **CancelRequest** : indicateur signalant si la demande a été annulée.

* **Progress** : estimation du pourcentage d'achèvement de l'opération. Une valeur de 50 indique que l'opération est terminée à environ 50 %.

* **Details** : valeur XML qui fournit un rapport de progression plus détaillé. Le rapport de progression est régulièrement mis à jour lorsque des ensembles de lignes sont copiés de la source vers la cible. En cas de défaillances ou d'exceptions, cette colonne inclut également des informations plus détaillées sur l'échec.


### Azure Diagnostics 

Le modèle de service de fractionnement/fusion est préconfiguré pour utiliser le stockage WAD (Windows Azure Diagnostic) afin de fournir un stockage de diagnostics et de journalisation détaillé supplémentaire. Vous contrôlez la configuration de Windows Azure Diagnostic, par exemple le compte de stockage et les informations d'identification, via votre fichier de configuration de service du service de fractionnement/fusion. La configuration de Windows Azure Diagnostic pour le service suit les recommandations de la rubrique [Concepts de base du service cloud](http://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Elle inclut les définitions permettant d'enregistrer les compteurs de performances, les journaux IIS, les journaux des événements Windows et les journaux des événements de l'application de fractionnement/fusion. Vous pouvez accéder facilement à ces journaux à partir de l'Explorateur de serveurs Visual Studio dans la partie Azure de l'arborescence de l'Explorateur :

![Azure Diagnostics][2]   

La table WADLogsTable mise en surbrillance dans la figure ci-dessus comporte les événements détaillés du journal des applications du service de fractionnement/fusion. Notez que la configuration par défaut fournie dans le cadre du package téléchargé est destinée à un déploiement de production. Par conséquent, l'intervalle auquel les journaux et les compteurs sont extraits par les instances du service est grand (5 minutes). Pour le test et le développement, vous pouvez réduire l'intervalle en ajustant les paramètres de diagnostic du rôle web ou du rôle de travail selon vos besoins. Vous pouvez effectuer cet ajustement en cliquant avec le bouton droit sur le rôle dans l'Explorateur de serveurs Visual Studio (voir ci-dessus), puis en ajustant la période de transfert dans la boîte de dialogue des paramètres de configuration des diagnostics :

![Configuration][3]


Les différents onglets de la boîte de dialogue contrôlent les différents types de journaux, chacun d'eux possédant sa propre configuration de période de transfert. 
Les équipes Microsoft requièrent généralement l'accès aux journaux et compteurs de Windows Azure Diagnostic en cas de problèmes avec le déploiement du service de fractionnement/fusion. Vous pouvez utiliser des outils tels que l'[Explorateur du stockage Azure](http://azurestorageexplorer.codeplex.com/) pour exporter des journaux Windows Azure Diagnostic dans des fichiers CSV facilement partageables. 

## Performances

Les performances des opérations de fractionnement, de fusion et de déplacement dépendent de divers facteurs. En général, les meilleures performances proviennent de niveaux de service élevés, plus performants, de la base de données SQL Azure. Des allocations d'E/S, de processeur et de mémoire plus élevées pour les niveaux de service supérieurs seront bénéfiques aux opérations de copie et de suppression en bloc que le service de fractionnement/fusion utilise en interne. Pour cette raison, il peut être intéressant d'augmenter le niveau de service pour un ensemble donné de bases de données pour une période limitée bien définie afin d'effectuer rapidement des opérations de fractionnement/fusion planifiées pour les plages hébergées sur ces bases de données.

Notez que le service effectue également des requêtes de validation dans le cadre de ses opérations. Ces requêtes de validation, entre autres choses, recherchent la présence inattendue de données dans la plage cible et garantissent que les opérations de fractionnement/fusion/déplacement démarrent à partir d'un état cohérent. Ces requêtes fonctionnent toutes sur des plages de clés de partitionnement définies par l'étendue de l'opération de fractionnement/fusion/déplacement et la taille du lot fourni dans le cadre de la définition de la demande. Ces requêtes offrent de meilleures performances lorsqu'un index est présent et doté de la clé de partitionnement en tant que première colonne. 

En outre, une propriété d'unicité avec la clé de partitionnement en tant que première colonne permet au service d'utiliser une approche optimisée qui limite la consommation de ressources en termes d'espace de journalisation et de mémoire. Cette propriété d'unicité est requise pour déplacer des tailles de données importantes (au-dessus de 1 Go). 

## Meilleures pratiques et dépannage 
-    Envisagez de définir un locataire test et d'exercer vos opérations les plus importantes de fractionnement/fusion/déplacement avec le locataire test sur plusieurs partitions. Cela vous permet de garantir que toutes les métadonnées sont définies correctement dans la carte de partitions et que les opérations ne violent pas les contraintes ou les clés étrangères.
-    Maintenez la taille des données du locataire test au-dessus de la taille maximale des données de votre locataire le plus volumineux pour garantir que vous ne rencontrerez pas de problèmes liés à la taille des données. Cela vous aidera également à évaluer une limite supérieure relative au temps requis pour déplacer un seul locataire. 
-    Le service de fractionnement/fusion requiert la possibilité de supprimer les données de la partition source une fois que les données ont été copiées dans la partition cible. Vérifiez attentivement votre schéma pour vous assurer qu'il autorise les suppressions. Par exemple, la suppression des déclencheurs peut empêcher le service de supprimer les données de la partition source et peut provoquer l'échec des opérations.
-    Vérifiez que la clé de partitionnement est la première colonne de la définition de la clé primaire ou de l'index unique. Cela garantit les meilleures performances pour les requêtes de validation de fractionnement/fusion ainsi que pour les opérations de déplacement et suppression de données réelles qui fonctionnent systématiquement sur des plages de clés de partitionnement.
-    Pour des raisons de performances et de coûts, la colocation du service de fractionnement/fusion dans la région et le centre de données dans lesquels résident vos bases de données est généralement le meilleur choix. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

## Références 

* [Didacticiel du service de fractionnement/fusion](./sql-database-elastic-scale-configure-deploy-split-and-merge.md)

* [Considérations de sécurité de l'infrastructure élastique](./sql-database-elastic-scale-configure-security.md)  


<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-split-and-merge/diagnostics-config.png
