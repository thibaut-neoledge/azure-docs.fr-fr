<properties urlDisplayName="Scale mobile services backed by Azure SQL Database" pageTitle="Mise à l'échelle des services mobiles soutenus par Base de données SQL Azure et Azure Mobile Services" metaKeywords="" description="Découvrez comment diagnostiquer et résoudre les problèmes d'extensibilité de vos services mobiles sous-tendus par une base de données SQL" metaCanonical="" services="" documentationCenter="Mobile" title="Scale mobile services backed by Azure SQL Database" authors="donnam" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/11/2014" ms.author="donnam" />
# Mise à l'échelle des services mobiles soutenus par une base de données SQL Azure

Azure Mobile Services facilite le démarrage et la création d'une application qui se connecte à un serveur principal hébergé dans le cloud qui stocke des données dans une base de données SQL. Au fil de l'évolution de votre application, pour mettre à l'échelle vos instances de service, il suffit d'ajuster les paramètres de mise à l'échelle dans le portail pour ajouter des capacités de calcul et de mise en réseau. Cependant, la mise à l'échelle de la base de données SQL qui soutient votre service exige une planification et une surveillance proactives au fil de l'accroissement de la charge du service. Ce document examine un ensemble de meilleures pratiques pour assurer en permanence l'excellente performance de vos services mobiles soutenus par SQL.

Cette rubrique vous familiarise avec les sections de base suivantes :

1. [Diagnostic des problèmes](#Diagnosing)
2. [Indexation](#Indexing)
3. [Conception d'un schéma](#Schema)
4. [Conception d'une requête](#Query)
5. [Architecture du service](#Architecture)
6. [Résolution avancée des problèmes](#Advanced)

<a name="Diagnosing"></a>
## Diagnostic des problèmes

Si vous pensez que votre service mobile rencontre des problèmes en condition de charge, commencez par ouvrir l'onglet **Tableau de bord** de votre service dans le [portail de gestion Azure][]. Voici quelques points à vérifier :

- Vos mesures d'utilisation, dont les **Appels API** et les **Périphériques actifs**, ne dépassent pas le quota.
L'état - **Surveillance de point de terminaison** indique que le service fonctionne (uniquement disponible si le service utilise le niveau Standard et que la surveillance de point de terminaison est activée).

Si l'un des points ci-dessus n'est pas confirmé, ajustez vos paramètres de mise à l'échelle sous l'onglet *Échelle*. Si cela ne résout pas le problème, passez à l'étape suivante et cherchez si la base de données SQL Azure peut être à l'origine du problème. Les prochaines sections proposent différentes approches pour diagnostiquer le problème.

### Choix du niveau de base de données SQL adapté 

Il est important de comprendre les différents niveaux de bases de données à votre disposition pour faire le bon choix en fonction des besoins de votre application. La base de données SQL Azure propose deux éditions différentes de base de données avec différents niveaux :

- Éditions Web et Business (supprimées)
- Édition Basic, Standard et Premium 

La fin des éditions Web et Business, bien que celles-ci soient entièrement prises en charge, est programmée pour le 24 avril 2015 comme indiqué dans le [Forum aux questions sur la disparition des éditions Web et Business](http://msdn.microsoft.com/en-US/library/azure/dn741330.aspx). Nous encourageons les nouveaux clients à commencer à utiliser l'édition De base, Standard et Premium en prévision de cette modification. Cette nouvelle édition propose de nouveaux niveaux et de nouvelles capacités de surveillance, qui facilitent la compréhension et la résolution des performances des bases de données. Tous les nouveaux services mobiles sont créés à l'aide de la nouvelle édition.

Pour convertir un service mobile utilisant l'édition Web ou Business en édition De base, Standard et Premium, procédez comme suit.

1. Lancez le [Portail de gestion Azure][].
2. Sélectionnez **+NOUVEAU** dans la barre d'outils, puis **Services de données**, **Base de données SQL**, **Création rapide**.
3. Entrez un nom de base de données et sélectionnez **Nouveau serveur de base de données SQL** dans le champ **Serveur**. Un serveur est créé, qui utilise la nouvelle édition Basic, Standard et Premium. 
4. Renseignez les autres champs et sélectionnez **Créer une base de données SQL**. Une base de données de 100 Mo est créée avec le niveau De base.
5. Configurez votre service mobile pour utiliser la base de données que vous venez de créer. Accédez à l'onglet **Configurer** de ce service et sélectionnez ensuite **Changer la base de données** dans la barre d'outils. Dans l'écran suivant, sélectionnez **Utiliser une base de données SQL existante** dans le champ **Base de données SQL**, puis **Suivant**. Dans l'écran suivant, sélectionnez la base de données que vous avez créée à l'étape 5, puis **OK**.

Voici quelques recommandations pour sélectionner le niveau pour votre base de données :

- **De base** : à utiliser au moment du développement ou pour les petits services de production, lorsque vous pensez interroger une seule base de données à la fois.
- **Standard** : à utiliser pour les services de production, lorsque vous pensez interroger plusieurs bases de données simultanément.
- **Premium** : à utiliser pour les grands services de production avec plusieurs requêtes simultanées, des pics de charge élevés et une faible latence attendue pour chaque demande.

Pour plus d'informations sur l'utilisation de chaque niveau, consultez la rubrique [Raisons d'utiliser les nouveaux niveaux de service](http://msdn.microsoft.com/en-US/library/azure/dn369873.aspx#Reasons).

### Analyse des mesures de base de données

Maintenant que vous connaissez les différents niveaux de bases de données, nous pouvons explorer les mesures de performances des bases de données pour vous aider à comprendre la mise à l'échelle dans et entre niveaux.

1. Lancez le [Portail de gestion Azure][].
2. Sous l'onglet Mobile Services, sélectionnez le service que vous souhaitez utiliser.
3. Sélectionnez l'onglet **Configurer**.
4. Sélectionnez le nom de la **Base de données SQL** dans la section **Paramètres de base de données**. Cela ouvre l'onglet de la base de données SQL Azure dans le portail.
5. Accédez à l'onglet **Surveiller**.
6. Vérifiez que les mesures appropriées sont affichées en utilisant le bouton **Ajouter des métriques**. Ajoutez les éléments suivants :
    - *Pourcentage UC* (disponible uniquement dans les niveaux De base/Standard/Premium)
    - *Pourcentage de lectures de données physiques* (disponible uniquement dans les niveaux De base/Standard/Premium)
    - *Pourcentage d'écritures dans les journaux* (disponible uniquement dans les niveaux De base/Standard/Premium)
    - *Stockage* 
7. Inspectez les mesures dans la plage horaire correspondant aux problèmes de votre service. 

    ![Azure Management Portal - SQL Database Metrics][PortalSqlMetrics]

If any metriSi des mesures dépassent 80 % d'utilisation pendant une longue période, cela peut être révélateur d'un problème de performances. Pour plus d'informations sur l'utilisation des bases de données, consultez la rubrique [Présentation de l'utilisation des ressources](http://msdn.microsoft.com/en-US/library/azure/dn369873.aspx#Resource)..

Si les mesures indiquent que votre base de données supporte une utilisation intensive, envisagez comme première mesure de **mettre à l'échelle la base de données vers un niveau de service plus élevé**. Pour résoudre les problèmes immédiatement et mettre à l'échelle la base de données, vous pouvez utiliser l'onglet **Mise à l'échelle** de votre base de données. Cela entraînera une augmentation de votre facture.
![Azure Management Portal - SQL Database Scale][PortalSqlScale]

Dès que possible, pensez à ces autres mesures d'atténuation :

- **Adaptez votre base de données.**
 Souvent, l'optimisation de la base de données permet de réduire l'utilisation de la base de données et d'éviter la mise à l'échelle vers un niveau supérieur. 
- **Repensez l'architecture de votre service.**
   Bien souvent, votre charge de service n'est pas distribuée de façon rationnelle sur la durée et contient des pics de demandes. Au lieu de mettre à l'échelle la base de données pour gérer ces pics et de connaître des périodes de sous-utilisation, il est souvent possible d'ajuster l'architecture du service pour éviter ces pics ou les gérer sans affecter les accès à la base de données.

Les sections suivantes de ce document contiennent une aide adaptée pour la mise en œuvre de ces atténuations.


### Configuration des alertes

Il est souvent utile de configurer des alertes comme mesure proactive pour les principales mesures de base de données afin de vous laisser suffisamment de temps pour réagir en cas d'épuisement des ressources. 

1. Accédez à l'onglet **Surveillance** pour la base de données concernée par les alertes.
2. Vérifiez que les mesures appropriées sont affichées comme décrit dans la section précédente.
3. Sélectionnez la mesure pour laquelle vous souhaitez définir une alerte, puis sélectionnez **Ajouter une règle**.
    ![Azure Management Portal - SQL Alert][PortalSqlAddAlert]
4. Indiquez un nom et une description pour l'alerte.
    ![Azure Management Portal - SQL Alert Name and Description][PortalSqlAddAlert2]
5. Spécifiez la valeur à utiliser comme seuil d'alerte. Vous pouvez indiquer **80 %** pour vous ménager un temps de réaction. Veillez également à spécifier une adresse de messagerie que vous surveillez activement. 
    ![Azure Management Portal - SQL Alert Threshold and Email][PortalSqlAddAlert3]

Pour plus d'informations sur le diagnostic des problèmes SQL, consultez la section [Diagnostic avancé](#AdvancedDiagnosing)  à la fin de ce document.

<a name="Indexing"></a>
## Indexation

Lorsque vous repérez les premiers problèmes de performances des requêtes, votre première réaction doit être d'étudier la conception des index. Les index sont importants, car ils affectent directement la façon dont le moteur SQL exécute une requête. 

Par exemple, si vous devez souvent rechercher un élément dans un certain champ, vous pouvez envisager d'ajouter un index pour cette colonne. À défaut, le moteur SQL est forcé d'exécuter une analyse de table et d'accéder à tous les enregistrements physiques (ou au moins à la colonne de requête) et les enregistrements peuvent être éparpillés sur le disque.

Si vous exécutez souvent les instructions WHERE ou JOIN sur certaines colonnes, veillez donc à les indexer. Pour plus d'informations, consultez la section [Création d'index](#CreatingIndexes).

Si les index sont tellement pratiques et les analyses de table tellement incertaines, cela veut-il dire que vous devez indexer toutes les colonnes de votre table par précaution ?  Pour faire court, nous répondrons non. Les index prennent de la place et ont également des surcharges : à chaque fois qu'un ajout est fait dans une table, les structures d'index de chaque colonne indexée doivent être mises à jour. Pour obtenir des instructions sur le choix des index de colonne, consultez la section suivante.

### Instructions sur la conception des index

Comme mentionné plus haut, il n'est pas toujours préférable d'ajouter des index à une table, car ils peuvent être onéreux, autant en termes de performances que de surcharge de stockage.

#### Considérations concernant les requêtes

- Vous pouvez envisager d'ajouter des index aux colonnes qui sont fréquemment utilisées dans les prédicats (par exemple, les clauses WHERE) et les conditions de jointure, tout en relativisant avec les considérations ci-après, liées à la base de données.
- Écrivez des requêtes qui ajoutent ou modifient autant de lignes que possible dans une seule instruction, au lieu de multiplier les requêtes pour mettre à jour les mêmes lignes. Lorsqu'il y a une seule instruction, le moteur de base de données peut mieux optimiser le traitement des index.
	
#### Considérations liées à la base de données

Un nombre important d'index sur une table affecte les performances des instructions INSERT, UPDATE, DELETE et MERGE, car tous les index doivent être ajustés au fil des changements de données dans la table.

- Pour les tables **très souvent mises à jour**, évitez d'indexer les colonnes très souvent mises à jour.
- Pour les tables **peu souvent mises à jour**, mais comportant un volume de données important, utilisez de nombreux index. Cela permet d'optimiser les performances des requêtes qui ne modifient pas les données (comme les instructions SELECT), car l'optimiseur de requêtes a plus d'options pour trouver la meilleure méthode d'accès.

L'indexation de petites tables risque de ne pas être optimale, car l'optimiseur de requêtes peut mettre plus longtemps pour parcourir l'index à la recherche de données que pour analyser simplement la table. Les index risquent donc de ne jamais être utilisés sur les petites tables, mais doivent être conservés au fil des changements de données dans la table.


<a name="CreatingIndexes"></a>
### Création d'index

#### Service principal JavaScript

Pour définir l'index d'une colonne dans le service principal JavaScript, procédez comme suit :

1. Ouvrez votre service mobile dans le [portail de gestion Azure][].
2. Cliquez sur l'onglet **Données**.
3. Sélectionnez la table à modifier.
4. Cliquez sur l'onglet **Colonnes**.
5. Sélectionnez la colonne. Dans la barre de commandes, cliquez sur **Définir l'index** :

	![Mobile Services Portal - Set Index][SetIndexJavaScriptPortal]

Vous pouvez également supprimer les index dans cette vue.

#### Service principal .NET

Pour définir un index dans Entity Framework, utilisez l'attribut `[Index]` sur les champs à indexer. Par exemple :

    public class TodoItem : EntityData
    {
        public string Text { get; set; }

		[Index]
        public bool Complete { get; set; }
    }
		 
Pour plus d'informations sur les index, consultez la page traitant des [annotations d'index dans Entity Framework][]. Pour plus d'informations sur l'optimisation des index, consultez la section [Indexation avancée](#AdvancedIndexing) à la fin de ce document.

<a name="Schema"></a>
## Conception d'un schéma

Voici quelques points à surveiller lors du choix des types de données pour vos objets, qui se traduisent ensuite dans le schéma de votre base de données SQL. Ajuster le schéma permet souvent d'améliorer significativement les performances, car SQL offre différents moyens d'optimiser la gestion de l'indexation et du stockage pour différents types de données :

- **Utilisez la colonne d'ID fournie**. Chaque table de service mobile a une colonne d'ID par défaut configurée comme clé primaire avec un index défini. Il n'est pas nécessaire de créer une autre colonne d'ID.
- **Utilisez les types de données appropriés dans votre modèle.** Si vous savez qu'une propriété donnée de votre modèle sera une valeur numérique ou booléenne, veillez à la définir ainsi dans votre modèle et non comme une chaîne. Dans le serveur principal JavaScript, utilisez des littéraux tels que `true` au lieu de `"true"` et `5` au lieu de `"5"`. Dans le serveur principal .NET, utilisez les types `int` et `bool` lorsque vous déclarez les propriétés de votre modèle. Cela permet à SQL de créer le schéma adapté pour ces types, ce qui rend les requêtes plus efficaces.  

<a name="Query"></a>
## Conception d'une requête

Voici quelques instructions liées aux requêtes effectuées dans la base de données :

- **Exécutez toujours les jonctions dans la base de données.** Vous devez souvent combiner les enregistrements d'au moins deux tables lorsque ces enregistrements partagent un champ (également appelé *jointure*). Cette opération peut être inefficace si elle est mal menée, car elle peut impliquer l'extraction de toutes les entités de toutes les tables, puis l'itération sur toutes celles-ci. Il vaut mieux laisser ce type d'opération à la base de données, mais il arrive parfois qu'il soit plus facile de l'effectuer dans le code du client ou du service mobile.
    - N'effectuez pas de jointures dans le code de votre application.
    - N'effectuez pas de jointures dans le code de votre service mobile. Lorsque vous utilisez le serveur principal JavaScript, n'oubliez pas que l'[objet table](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554210.aspx) ne prend pas en charge les jointures. Pensez à utiliser directement l'[objet mssql](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554212.aspx) pour que la jointure s'effectue dans la base de données. Pour plus d'informations, consultez la page [Joindre des tables relationnelles](http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-how-to-use-server-scripts/#joins). Si vous utilisez le serveur principal .NET et que vous effectuez des requêtes via LINQ, les jointures sont automatiquement gérées au niveau de la base de données par Entity Framework.
- **Implémentez la pagination.** Effectuer des recherches dans la base de données peut aboutir au renvoi d'un grand nombre d'enregistrements au client. Pour minimiser le volume et la latence des opérations, pensez à implémenter la pagination.
    - Par défaut, votre service mobile limite la taille de la page à 50 enregistrements pour les requêtes entrantes, mais vous pouvez demander manuellement jusqu'à 1 000 enregistrements. Pour plus d'informations, consultez la section " Renvoi de données dans les pages " pour [Windows Store](http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/#paging), [iOS](http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-ios-how-to-use-client-library/#paging), [Android](http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-android-how-to-use-client-library/#paging), [HTML/JavaScript](http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library/#paging) et [Xamarin](http://azure.microsoft.com/fr-fr/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/#paging).
    - Il n'existe pas de taille de page par défaut pour les requêtes effectuées à partir du code de votre service mobile. Si votre application ne prend pas en charge la pagination ou si elle ne l'exécute pas par précaution, pensez à appliquer les limites par défaut à vos requêtes. Dans le serveur principal JavaScript, utilisez l'opérateur **take** sur l'[objet query](http://msdn.microsoft.com/fr-fr/library/azure/jj613353.aspx). Si vous utilisez le serveur principal .NET, pensez à utiliser la [méthode Take](http://msdn.microsoft.com/fr-fr/library/vstudio/bb503062(v=vs.110).aspx)  pour votre requête LINQ.  

Pour plus d'informations sur l'optimisation de la conception des requêtes, y compris sur l'analyse des plans de requête, consultez la section [Conception avancée des requêtes](#AdvancedQuery)  à la fin de ce document.

<a name="Architecture"></a>
## Architecture du service

Imaginez que vous êtes sur le point d'envoyer une notification Push à tous vos clients pour qu'ils consultent un nouveau contenu de votre application. Lorsqu'ils appuient sur la notification, l'application se lance et déclenche éventuellement un appel sur votre service mobile et l'exécution d'une requête sur votre base de données SQL. Comme des millions de clients sont susceptibles d'effectuer cette action sur une période de quelques minutes, la charge SQL fait un bond dans des proportions bien supérieures à la charge habituelle de l'application. Ce problème peut être résolu en faisant passer votre application vers un niveau SQL supérieur pendant le pic, puis en la ramenant au niveau d'origine, même si cette solution demande une intervention manuelle et génère une augmentation des coûts. Souvent, de petites modifications dans l'architecture de votre service mobile permettent de compenser efficacement la charge que font peser les clients sur votre base de données SQL et éliminent les pics problématiques de demande. Ces modifications peuvent souvent être mises en œuvre facilement et ont un impact minimal sur l'expérience de vos clients. Voici quelques exemples :

- **Répartissez la charge dans le temps.** Si vous contrôlez la planification de certains événements (par exemple, la notification push d'une diffusion) qui sont susceptibles de générer un pic des demandes et que la planification de ces événements n'est pas stratégique, pensez à les répartir dans le temps. Dans l'exemple précédent, il peut être acceptable pour les clients de votre application d'être avertis par vagues du nouveau contenu de l'application sur une journée et non pas de façon rapprochée. Pensez à regrouper vos clients pour permettre une livraison échelonnée dans chaque lot. Si vous utilisez Notification Hubs, appliquez une balise supplémentaire pour suivre le lot, puis envoyez une notification push à cette balise pour mieux implémenter cette stratégie. Pour plus d'informations sur les balises, consultez la page [Utilisation de Notification Hubs pour envoyer les dernières nouvelles](http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/).
- **Utilisez le stockage d'objets blob et de tables dès que possible.** Souvent, le contenu que voient les clients pendant le pic est relativement statique et n'a pas besoin d'être stocké dans une base de données SQL, car vous n'avez probablement pas besoin de fonctionnalités d'interrogation relationnelle sur ce contenu. Dans ce cas, pensez à stocker le contenu dans un stockage d'objets blob ou de tables. Vous pouvez accéder aux objets blob publics dans le stockage d'objets blob directement à partir de l'appareil. Pour accéder aux objets blob de façon sécurisée ou pour utiliser le stockage de tables, accédez à une API personnalisée Mobile Services pour protéger votre clé d'accès de stockage. Pour plus d'informations, consultez la page [Téléchargement d'images vers Azure Storage à l'aide de Mobile Services](http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/).
- **Utilisez un cache en mémoire**. Une alternative consiste à stocker les données pour qu'elles soient facilement accessibles en cas de pic de trafic, dans un cache en mémoire comme le [Cache Microsoft Azure](http://azure.microsoft.com/fr-fr/services/cache/). Les requêtes entrantes peuvent accéder aux informations dont elles ont besoin en mémoire, au lieu de lancer des requêtes de façon répétée dans la base de données.

<a name="Advanced"></a>
## Résolution avancée des problèmes
Cette section présente des tâches de diagnostic avancées, qui peuvent être utiles si les étapes effectuées jusqu'ici n'ont pas permis de résoudre complètement le problème.

### Configuration requise
Pour effectuer certaines des tâches de diagnostic traitées dans cette section, vous devez accéder à un outil de gestion pour les bases de données SQL, comme **SQL Server Management Studio** ou la fonctionnalité de gestion intégrée au **portail de gestion Azure**.

SQL Server Management Studio est une application Windows gratuite, qui offre des fonctionnalités très avancées. Si vous n'avez pas accès à un ordinateur Windows (si vous utilisez un Mac, par exemple), pensez à approvisionner une machine virtuelle dans Azure comme indiqué à la page [Création d'une machine virtuelle exécutant Windows Server](http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-windows-tutorial/) , puis à vous y connecter à distance. Si vous pensez utiliser une machine virtuelle essentiellement pour exécuter SQL Server Management Studio, une instance **De base A0** (anciennement " Très petite ") devrait suffire. 

Le portail de gestion Azure offre une expérience de gestion intégrée plus limitée, mais disponible sans installation locale.

Les étapes suivantes présentent l'obtention des informations de connexion pour la base de données SQL qui soutient votre service mobile, et l'utilisation de l'un des outils pour s'y connecter. Vous pouvez choisir l'outil qui vous convient.

#### Obtention des informations de connexion SQL 
1. Lancez le [Portail de gestion Azure][].
2. Sous l'onglet Mobile Services, sélectionnez le service que vous souhaitez utiliser.
3. Sélectionnez l'onglet **Configurer**.
4. Sélectionnez le nom de la **Base de données SQL** dans la section **Paramètres de base de données**. Cela ouvre l'onglet de la base de données SQL Azure dans le portail.
5. Sélectionnez **Configurer des règles de pare-feu Windows Azure pour cette adresse IP**.
6. Notez l'adresse de serveur dans la section **Se connecter à votre base de données**, par exemple : *mcml4otbb9.database.windows.net*.

#### SQL Server Management Studio
1. Accédez à [Éditions SQL Server - Express](http://www.microsoft.com/fr-fr/server-cloud/products/sql-server-editions/sql-server-express.aspx).
2. Recherchez la section **SQL Server Management Studio** et cliquez sur le bouton **Télécharger** en dessous.
3. Effectuez les étapes d'installation jusqu'au lancement de l'application :

    ![SQL Server Management Studio][SSMS]

4. Dans la boîte de dialogue **Se connecter au serveur**, entrez les valeurs suivantes :
    - Nom du serveur : *adresse de serveur obtenue précédemment*
    - Authentification : *authentification SQL Server*
    - Connexion : *nom de connexion que vous avez choisi lors de la création du serveur*
    - Mot de passe : *mot de passe que vous avez choisi lors de la création du serveur*
5. À ce stade, vous devez être connecté.

#### Portail de gestion de base de données SQL
1. Sous l'onglet Base de données SQL Azure pour votre base de données, cliquez sur le bouton **Gérer**. 
2. Configurez la connexion en appliquant les valeurs suivantes.
    - Serveur : *doit être déjà défini sur la valeur appropriée*
    - Base de données : *ne pas renseigner*
    - Nom d'utilisateur : *nom de connexion que vous avez choisi lors de la création du serveur*
    - Mot de passe : *mot de passe que vous avez choisi lors de la création du serveur*
3. À ce stade, vous devez être connecté.

    ![Azure Management Portal - SQL Database][PortalSqlManagement]

<a name="AdvancedDiagnosing" />
### Diagnostics avancés

De nombreuses tâches de diagnostic peuvent être effectuées directement dans le **portail de gestion Azure**, mais certaines tâches de diagnostic avancées peuvent être réalisées uniquement via **SQL Server Management Studio** ou le **portail de gestion de base de données SQL**.  Nous allons utiliser les vues de gestion dynamique, un ensemble de vues renseignées automatiquement avec des informations de diagnostic sur votre base de données. Cette section fournit un ensemble de requêtes que nous pouvons exécuter dans ces vues pour examiner différentes mesures. Pour plus d'informations, consultez la page [Contrôle de la base de données SQL à l'aide de vues de gestion dynamique][].

Après avoir effectué les étapes de la section précédente pour vous connecter à votre base de données dans SQL Server Management Studio, sélectionnez votre base de données dans l'**Explorateur d'objets**. Développez les **vues** et les **vues système** pour afficher la liste des vues de gestion. Pour exécuter les requêtes ci-dessous, sélectionnez votre base de données dans l'**Explorateur d'objets**, sélectionnez **Nouvelle requête**, collez la requête, puis sélectionnez **Exécuter**.

![SQL Server management Studio - dynamic management views][SSMSDMVs]

Si vous utilisez le portail de gestion de base de données SQL, commencez par sélectionner votre base de données, puis sélectionnez **Nouvelle requête**.

![SQL Database Management Portal - new query][PortalSqlManagementNewQuery]

Pour exécuter l'une des requêtes ci-dessous, collez-la dans la fenêtre et sélectionnez **Exécuter**.

![SQL Database Management Portal - run query][PortalSqlManagementRunQuery]

#### Mesures avancées

Le portail de gestion met certaines mesures à disposition avec les niveaux De base, Standard et Premium. Cependant, avec les niveaux Web et Business, seule la mesure de stockage est disponible via le portail. Heureusement, ces mesures, comme d'autres, sont faciles à obtenir avec la vue de gestion **[sys.resource\_stats](http://msdn.microsoft.com/fr-fr/library/dn269979.aspx)**, indépendamment du niveau que vous utilisez. Examinez la requête suivante :

    SELECT TOP 10 * 
    FROM sys.resource_stats 
    WHERE database_name = 'todoitem_db' 
    ORDER BY start_time DESC

> [WACOM.NOTE] 
> Exécutez cette requête sur la base de données **maître** sur votre serveur, car la vue **sys.resource\_stats** est présente uniquement sur cette base de données.

Le résultat contient les mesures utiles suivantes : UC (% de limite de niveau), Stockage (Mo), Lectures de données physiques (% de limite de niveau), Écritures dans les journaux (% de limite de niveau), Mémoire (% de limite de niveau), Nombre de travailleurs, Nombre de sessions, etc.

#### Événements de connectivité SQL

La vue **[sys.event\_log](http://msdn.microsoft.com/fr-fr/library/azure/jj819229.aspx)** contient les détails des événements de connectivité.

    select * from sys.event_log 
    where database_name = 'todoitem_db'
    and event_type like 'throttling%'
    order by start_time desc

> [WACOM.NOTE] 
> Exécutez cette requête sur la base de données **maître** sur votre serveur, car la vue **sys.event\_log** est présente uniquement sur cette base de données.

<a name="AdvancedIndexing" />
### Indexation avancée

Une table ou une vue peut contenir les types d'index suivants :

- **Cluster**. Un index en cluster spécifie comment les enregistrements sont physiquement stockés sur disque. Il doit y avoir un seul index en cluster par table, car les lignes de données peuvent elles-mêmes être triées suivant un seul ordre.

- **Non cluster**. Les index non cluster sont stockés séparément des lignes de données et permettent de faire une recherche basée sur la valeur de l'index. Tous les index non cluster d'une table utilisent les valeurs principales de l'index cluster comme clé de recherche.

Pour fournir une analogie concrète : pensez à un livre ou à un guide technique. Le contenu de chaque page est un enregistrement, le numéro de page est l'index cluster et l'index de rubrique à l'arrière du livre est un index non cluster. Chaque entrée de l'index de rubrique pointe vers l'index cluster, le numéro de page.

> [WACOM.NOTE] 
> Par défaut, le serveur principal JavaScript d'Azure Mobile Services définit **\_createdAt** comme index cluster. Si vous supprimez cette colonne, ou si vous souhaitez un autre index cluster, suivez les [instructions sur la conception des index](#ClusteredIndexes)  ci-dessous. Dans le serveur principal .NET, la classe `EntityData` définit `CreatedAt` comme index cluster à l'aide de l'annotation `[Index(IsClustered = true)]`.

<a name="ClusteredIndexes"></a>
#### Instructions sur la conception des index cluster

Chaque table doit avoir un index cluster sur la colonne (ou sur les colonnes, dans le cas d'une clé composite) avec les propriétés suivantes :

- Restreinte : utilise un petit datatype, ou est une [clé composite][Primary and Foreign Key Constraints]  d'un petit nombre de colonnes restreintes.
- Unique, ou presque unique
- Statique : la valeur n'est pas souvent changée
- En augmentation constante 
- (facultatif) Largeur fixe
- (facultatif) non nulle

La raison de la propriété **Restreinte** est que tous les autres index d'une table utilisent les valeurs principales de l'index cluster comme clés de recherche. Dans l'exemple d'un index de rubrique à l'arrière d'un livre, l'index cluster est un numéro de page et un petit chiffre. Si le titre du chapitre était inclus à la place dans l'index cluster, l'index de rubrique serait beaucoup plus long, car la valeur principale serait (nom de chapitre, numéro de page).

La clé serait **statique** et **en constante augmentation** pour éviter de devoir conserver l'emplacement physique des enregistrements (ce qui signifie soit déplacer les enregistrements physiquement, soit potentiellement fragmenter le stockage en fractionnant les pages dans lesquelles les enregistrements sont stockés). 

L'index cluster est très utile pour les requêtes qui exécutent les opérations suivantes :

- Renvoi d'une plage de valeurs en utilisant les opérateurs comme BETWEEN, >, >=, <, et <=. 
	- Lorsque la première ligne avec la première valeur a été trouvée en utilisant l'index cluster, les lignes avec les valeurs indexées suivantes sont systématiquement physiquement adjacentes. 
- Utilisez les clauses JOIN, en général des colonnes de clés étrangères.
- Utilisez les clauses ORDER BY ou GROUP BY.
	- Un index sur les colonnes spécifiées dans la clause ORDER BY ou GROUP BY peut supprimer la nécessité pour le moteur de base de données de trier les données, car les lignes sont déjà triées. La performance des requêtes s'en trouve améliorée.

#### Création d'index cluster dans Entity Framework

Pour définir l'index cluster dans le serveur principal .NET en utilisant Entity Framework, définissez la propriété `IsClustered` de l'annotation. Il s'agit, par exemple, de la définition de `CreatedAt` dans `Microsoft.WindowsAzure.Mobile.Service.EntityData` :

	[Index(IsClustered = true)]
	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
	[TableColumnAttribute(TableColumnType.CreatedAt)]
	public DateTimeOffset? CreatedAt { get; set; }

#### Création d'index dans le schéma de base de données

Pour le serveur principal JavaScript, vous pouvez uniquement modifier l'index cluster d'une table en changeant le schéma de base de données directement, ou via SQL Server Management Studio ou le portail de base de données SQL Azure.

Les guides suivants décrivent la définition d'un index cluster ou non cluster en modifiant le schéma de base de données directement :  

- [Création et modification des contraintes PRIMARY KEY][]
- [Création d'index non cluster][]
- [Création d'index cluster][]
- [Création d'index uniques][]

#### Recherche des N premiers index manquants 
Vous pouvez écrire des requêtes SQL sur des vues de gestion dynamique pour obtenir des informations détaillées sur l'utilisation des ressources de requêtes individuelles ou une vue globale sur les index à ajouter. La requête suivante détermine quels 10 index manquants produiraient la plus grande amélioration cumulative prévue, dans l'ordre décroissant, pour les requêtes d'utilisateurs.

    SELECT TOP 10 *
    FROM sys.dm_db_missing_index_group_stats
    ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)
    DESC;

L'exemple de requête suivant exécute une jointure sur ces tables pour obtenir la liste des colonnes qui doivent faire partie de chaque index manquant et calcule un 'avantage d'index' pour déterminer si l'index concerné doit être pris en compte :

    SELECT * from 
    (
        SELECT 
        (user_seeks+user_scans) * avg_total_user_cost * (avg_user_impact * 0.01) AS index_advantage, migs.*
        FROM sys.dm_db_missing_index_group_stats migs
    ) AS migs_adv,
      sys.dm_db_missing_index_groups mig,
      sys.dm_db_missing_index_details mid
    WHERE
      migs_adv.group_handle = mig.index_group_handle and
      mig.index_handle = mid.index_handle
      AND migs_adv.index_advantage > 10
    ORDER BY migs_adv.index_advantage DESC;

Pour plus d'informations, consultez les pages [Contrôle de la base de données SQL à l'aide de vues de gestion dynamique][] et [Vues de gestion dynamique des index manquants](sys-missing-index-stats).

<a name="AdvancedQuery" />
### Conception avancée des requêtes 

Il est souvent difficile de diagnostiquer les requêtes les plus coûteuses pour la base de données. 

#### Recherche des N premières requêtes

L'exemple suivant renvoie des informations relatives aux cinq premières requêtes, classées sur la base du temps processeur moyen. Cet exemple agrège les requêtes selon leur hachage de requête et, logiquement, les requêtes équivalentes sont regroupées sur la base de leur consommation cumulée de ressources.

	SELECT TOP 5 query_stats.query_hash AS "Query Hash", 
	    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
	    MIN(query_stats.statement_text) AS "Statement Text"
	FROM 
	    (SELECT QS.*, 
	    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
	    ((CASE statement_end_offset 
	        WHEN -1 THEN DATALENGTH(st.text)
	        ELSE QS.statement_end_offset END 
	            - QS.statement_start_offset)/2) + 1) AS statement_text
	     FROM sys.dm_exec_query_stats AS QS
	     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
	GROUP BY query_stats.query_hash
	ORDER BY 2 DESC;

Pour plus d'informations, consultez la page [Contrôle de la base de données SQL à l'aide de vues de gestion dynamique][]. En plus de l'exécution de la requête, le **portail de gestion de la base de données SQL** vous propose un raccourci utile pour consulter ces données, en sélectionnant **Résumé** pour votre base de données, puis **Performances de requête** :

![SQL Database Management Portal - query performance][PortalSqlManagementQueryPerformance]

#### Analyse du plan de requête

Lorsque vous avez identifié les requêtes coûteuses ou si vous êtes sur le point de déployer du code en utilisant les nouvelles requêtes et que vous souhaitez enquêter sur leurs performances, les outils offrent une aide appréciable pour analyser le **plan de requête**. Ce dernier vous permet de voir les opérations qui monopolisent le temps processeur et les ressources d'E/S lors de l'exécution d'une requête SQL. Pour analyser le plan de requête dans **SQL Server Management Studio**, utilisez les boutons en surbrillance de la barre d'outils.

![SQL Server Management Studio - query plan][SSMSQueryPlan]

Pour analyser le plan de requête dans le **portail de gestion de base de données SQL**, utilisez les boutons en surbrillance de la barre d'outils.

![SQL Database Management Portal - query plan][PortalSqlManagementQueryPlan]

## Voir aussi

- [Documentation de base de données SQL Azure][]
- [Performances et mise à l'échelle de la base de données SQL Azure][]
- [Résolution des problèmes de base de données SQL Azure][]

### Indexation

- [Notions fondamentales sur les index][]
- [Consignes générales pour la création d'index][]
- [Directives pour la conception d'index uniques][]
- [Instructions sur la conception des index cluster][]
- [Contraintes de clé primaire et de clé étrangère][]
- [Combien coûte cette clé ?][]

### Entity Framework
- [Considérations sur les performances d'Entity Framework 5][]
- [Annotations de données Code First][]

<!-- IMAGES -->
 
[SSMS]: ./media/mobile-services-sql-scale-guidance/1.png
[PortalSqlManagement]: ./media/mobile-services-sql-scale-guidance/2.png
[PortalSqlMetrics]: ./media/mobile-services-sql-scale-guidance/3.png
[PortalSqlScale]: ./media/mobile-services-sql-scale-guidance/4.png
[PortalSqlAddAlert]: ./media/mobile-services-sql-scale-guidance/5.png
[PortalSqlAddAlert2]: ./media/mobile-services-sql-scale-guidance/6.png
[PortalSqlAddAlert3]: ./media/mobile-services-sql-scale-guidance/7.png
[SetIndexJavaScriptPortal]: ./media/mobile-services-sql-scale-guidance/set-index-portal-ui.png
[SSMSDMVs]: ./media/mobile-services-sql-scale-guidance/8.png
[PortalSqlManagementNewQuery]: ./media/mobile-services-sql-scale-guidance/9.png
[PortalSqlManagementRunQuery]: ./media/mobile-services-sql-scale-guidance/10.png
[PortalSqlManagementQueryPerformance]: ./media/mobile-services-sql-scale-guidance/11.png
[SSMSQueryPlan]: ./media/mobile-services-sql-scale-guidance/12.png
[PortalSqlManagementQueryPlan]: ./media/mobile-services-sql-scale-guidance/13.png

<!-- LINKS -->

[Portail de gestion Azure]: http://manage.windowsazure.com

[Documentation de base de données SQL Azure]: http://azure.microsoft.com/fr-fr/documentation/services/sql-database/
[Gestion de la base de données SQL au moyen de SQL Server Management Studio]: http://go.microsoft.com/fwlink/p/?linkid=309723&clcid=0x409
[Contrôle de la base de données SQL à l'aide de vues de gestion dynamique]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
[Performances et mise à l'échelle de la base de données SQL Azure]: http://go.microsoft.com/fwlink/p/?linkid=397217&clcid=0x409
[Résolution des problèmes de base de données SQL Azure]: http://msdn.microsoft.com/fr-fr/library/azure/ee730906.aspx

<!-- MSDN -->
[Création et modification des contraintes PRIMARY KEY]: http://technet.microsoft.com/fr-fr/library/ms181043(v=sql.105).aspx
[Création d'index cluster]: http://technet.microsoft.com/fr-fr/library/ms186342(v=sql.120).aspx
[Création d'index uniques]: http://technet.microsoft.com/fr-fr/library/ms187019.aspx
[Création d'index non cluster]: http://technet.microsoft.com/fr-fr/library/ms189280.aspx

[Contraintes de clé primaire et de clé étrangère]: http://msdn.microsoft.com/fr-fr/library/ms179610(v=sql.120).aspx
[Notions fondamentales sur les index]: http://technet.microsoft.com/fr-fr/library/ms190457(v=sql.105).aspx
[Consignes générales pour la création d'index]: http://technet.microsoft.com/fr-fr/library/ms191195(v=sql.105).aspx 
[Directives pour la conception d'index uniques]: http://technet.microsoft.com/fr-fr/library/ms187019(v=sql.105).aspx
[Instructions sur la conception des index cluster]: http://technet.microsoft.com/fr-fr/library/ms190639(v=sql.105).aspx

[sys-missing-index-stats]: http://technet.microsoft.com/fr-fr/library/ms345421.aspx

<!-- EF -->
[Considérations sur les performances d'Entity Framework 5]: http://msdn.microsoft.com/fr-fr/data/hh949853
[Annotations de données Code First]: http://msdn.microsoft.com/fr-fr/data/jj591583.aspx
[Annotations d'index dans Entity Framework]:http://msdn.microsoft.com/fr-fr/data/jj591583.aspx#Index

<!-- BLOG LINKS -->
[Combien coûte cette clé ?]: http://www.sqlskills.com/blogs/kimberly/how-much-does-that-key-cost-plus-sp_helpindex9/
