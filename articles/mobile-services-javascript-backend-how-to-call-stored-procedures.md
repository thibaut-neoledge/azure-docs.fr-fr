<properties pageTitle="Calling SQL stored procedures with a JavaScript back end" metaKeywords="stored procedures, SQL, mobile devices, Azure" description="explains how to use SQL stored procedures in a mobile services JavaScript backed." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Calling SQL stored procedures with a JavaScript back end" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Appel de procédures stockées SQL avec un serveur principal JavaScript

Vous pouvez appeler des procédures stockées SQL Server pour les raisons suivantes :

-   votre service mobile utilise des procédures stockées existantes auxquelles vous devez accéder ;
-   l'architecture de votre application vous impose l'utilisation de procédures stockées pour l'accès à la base de données ;
-   par préférence personnelle.

Cette rubrique vous explique les procédures suivantes :

-   [Appel d'une simple procédure stockée][Appel d'une simple procédure stockée]
-   [Appel d'une procédure stockée avec paramètres][Appel d'une procédure stockée avec paramètres]
-   [Plus d'informations][Plus d'informations]

Cet article traite des procédures stockées dans un service mobile avec un serveur principal JavaScript.

Vous pouvez cependant créer des services mobiles avec un serveur principal .Net, qui a une architecture absolument différente basée sur Entity Framework. Pour plus d'informations, consultez la page [Fonctionnement du serveur principal Azure Mobile Services .Net][Fonctionnement du serveur principal Azure Mobile Services .Net].

## Appel d'une simple procédure stockée

**« Ma base de données utilise des procédures stockées : comment les appeler à partir d'un service mobile ? »**

Utilisez l'[objet mssql][objet mssql], qui vous permet d'exécuter des instructions *Transact-SQL* (T-SQL) qui appellent la procédure.

### Écriture et test du code Transact SQL

Prenons la procédure stockée *GetAll* et votre service mobile **todolist**.

1.  Sélectionnez l'icône **Bases de données** dans la barre de navigation du portail Azure, puis sélectionnez votre base de données, qui porte le nom de votre service mobile avec le suffixe « **\_db** ».

2.  En bas de la page Démarrage rapide de la base de données, appuyez sur le lien **Gérer**.

3.  Connectez-vous à la base de données.

4.  Appuyez sur **Nouvelle requête** dans la barre supérieure et ajoutez le code suivant :

            EXEC todolist.GetAll

5.  Cliquez sur **Exécuter** et vérifiez les résultats.

Notez que le nom de la procédure a comme préfixe le nom du schéma de base de données, qui par défaut est le nom de votre service mobile.

### Où placer l'appel à l'objet mssql ?

Si vous devez appeler directement la procédure stockée, le moyen le plus simple consiste à écrire puis appeler une [API personnalisée][API personnalisée].

1.  Sur votre Tableau de bord de service mobile, cliquez sur **API**, puis sur **CRÉER**, nommez votre script ***getall***, puis ajoutez le code suivant pour appeler la procédure stockée :

        exports.get = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "EXEC todolist.GetAll";
            mssql.query(sql, {
                success: function(results) {                          
                        response.send(200, results); 
                }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

2.  Testez-le dans un navigateur en copiant cette URL dans la ligne d'adresse :

        https://todolist.azure-mobile.net/api/getall

Vous pouvez également écrire un code similaire dans le script serveur standard *read*, *insert*, *update* ou *delete* d'une table pour que votre code remplace le comportement par défaut et appelle la procédure stockée. Un code similaire peut être ajouté au script *Scheduler*. Cependant, créer une API personnalisée reste la solution la plus simple.

### Appel de code depuis le client

Modifiez le code client pour appeler la méthode **invokeApi** sur l'objet **MobileServiceClient**. La syntaxe exacte du code est spécifique de l'appareil de votre client et est expliquée dans les rubriques suivantes :

-   [Windows Store C\#][Windows Store C\#]
-   [Windows Store JavaScript][Windows Store JavaScript]
-   [Windows Phone][Windows Phone]
-   [iOS][iOS]
-   [Android][Android]
-   [HTML][HTML]

## <a name="parameters"></a>Appel d'une procédure stockée avec paramètres

Prenons une procédure stockée *ItemsByStatus* qui a un seul paramètre, *Status*, et que nous devons appeler depuis une API personnalisée.

1.  Suivez les étapes de la section précédente pour écrire et tester le code T-SQL en utilisant le portail de base de données Azure, mais en utilisant ce code T-SQL à la place :

        EXEc todolist.ItemsByStatus @Status = 1

2.  Cliquez sur **Exécuter** et vérifiez les résultats.

3.  Comme dans l'étape précédente, créez une API personnalisée nommée **completeall** avec le code suivant qui appelle la procédure stockée. Dans le code ci-dessous, notez que vous remplacez la valeur réelle par le paramètre **@Status** que vous avez utilisé pour le test avec un **« ? »**, que vous remplacerez au moment de l'exécution par le paramètre fourni.

        exports.get = function(request, response) { 
            var mssql = request.service.mssql;
            var param1 = parseInt(request.query.status);
            var sql = "EXEC todolist.ItemsByStatus @Status = ?";
            mssql.query(sql, [param1], {
                success: function(results) { 
                         response.send(200, results[0]); 
                    }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

4.  Testez l'API avec cette URL dans un navigateur :

        https://todolist.azure-mobile.net/api/completeall?status=1

5.  Appelez l'API depuis le client comme décrit dans la section précédente.

### Signatures avec des paramètres plus complexes

La procédure stockée de la section précédente avait un seul paramètre. Voici la syntaxe à utiliser pour une signature plus longue :

        function read(query, user, request) {
            var sql = 'EXEC MySProc @Param1 = ?, @Param2 = ?, @Param3 = ?';
            var param1 = request.parameters.first;
            var param2 = request.parameters.second;
            var param3 = request.parameters.third;
            mssql.query(sql, [param1, param2, param3], {
                success: function(results) {
                    request.respond(200, results);
                }
            });
        }

Notez que dans l'instruction **EXEC**, les paramètres sont appelés par nom et non par position. Il s'agit de la syntaxe recommandée, qui isole votre code des changements mineurs dans la procédure stockée, comme le changement de l'ordre des paramètres ou l'ajout de paramètres facultatifs.

## <a name="more"></a>Plus d'informations

Cette rubrique ne fait qu'aborder le sujet.

Pour des références détaillées sur le code, consultez les rubriques suivantes :

-   [objet mssql] : informations de référence concernant l'objet central utilisé pour appeler les procédures stockées dans les services mobiles
-   [Travailler avec un service mobile principal JavaScript] : informations plus générales concernant le serveur principal Javascript, y compris des documents sur l'objet mssql

Voici d'autres scénarios possibles :

-   [Exécution de plusieurs opérations de lecture par script][Exécution de plusieurs opérations de lecture par script] décrit la lecture conditionnelle d'une table ou l'appel d'une procédure stockée. Ce blogueur, Carlos Figueira, publie fréquemment des articles sur Azure Mobile Services et les bases de données.

-   [Accès à une procédure stockée depuis un autre schéma][Accès à une procédure stockée depuis un autre schéma] décrit les solutions aux problèmes d'accès aux procédures stockées qui résident dans différents schémas de la même base de données Mobile Services

Vous pouvez également utiliser le portail Azure pour gérer et créer des procédures stockées.

<!-- Anchors. -->
<!-- URLs. -->

  [Appel d'une simple procédure stockée]: #simple
  [Appel d'une procédure stockée avec paramètres]: #parameters
  [Plus d'informations]: #more
  [Fonctionnement du serveur principal Azure Mobile Services .Net]: http://curah.microsoft.com/64518/how-the-azure-mobile-services-net-backend-works
  [objet mssql]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554212.aspx
  [API personnalisée]: http://msdn.microsoft.com/en-us/library/windowsazure/dn280974.aspx
  [Windows Store C\#]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Windows Store JavaScript]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-windows-store-javascript-call-custom-api/
  [Windows Phone]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-windows-phone-call-custom-api/
  [iOS]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-ios-call-custom-api/
  [Android]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-android-call-custom-api/
  [HTML]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-html-call-custom-api/
  [Exécution de plusieurs opérations de lecture par script]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/fccf4ae7-f43c-4c2d-8518-32e2df84a824/how-do-i-do-more-than-1-read-operation-by-scripting?forum=azuremobile
  [Accès à une procédure stockée depuis un autre schéma]: http://blogs.msdn.com/b/jpsanders/archive/2013/05/02/windows-azure-mobile-services-accessing-a-stored-procedure-from-a-different-schema.aspx
  [Travailler avec un service mobile principal JavaScript]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-how-to-use-server-scripts/
  [mssql object]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554212.aspx