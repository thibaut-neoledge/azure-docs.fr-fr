<properties pageTitle="Work with a JavaScript backend mobile service" metaKeywords="server scripts, mobile devices, Azure, scheduler" description="Provides examples on how to define, register, and use server scripts in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Utilisation d'un service mobile principal Javascript

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

Cet article fournit des informations détaillées et des exemples sur l'utilisation d'un service principal JavaScript dans Azure Mobile Services. Cette rubrique se compose des sections suivantes :

-   [Introduction][Introduction]
-   [Opérations de table][Opérations de table]
    -   [Enregistrement pour les opérations de table][Enregistrement pour les opérations de table]
    -   [Remplacement de la réponse par défaut][Remplacement de la réponse par défaut]
    -   [Remplacement du succès d'execute][Remplacement du succès d'execute]
    -   [Remplacement de la gestion des erreurs par défaut][Remplacement de la gestion des erreurs par défaut]
    -   [Ajout de paramètres personnalisés][Ajout de paramètres personnalisés]
    -   [Fonctionnement des utilisateurs de table][Fonctionnement des utilisateurs de table]
-   [API personnalisée][API personnalisée]
    -   [Définition d'une API personnalisée][Définition d'une API personnalisée]
    -   [Implémentation des méthodes HTTP][Implémentation des méthodes HTTP]
    -   [Envoi et réception des données au format XML][Envoi et réception des données au format XML]
    -   [Fonctionnement des utilisateurs et des en-têtes dans une API personnalisée][Fonctionnement des utilisateurs et des en-têtes dans une API personnalisée]
    -   [Définition de plusieurs itinéraires dans une API personnalisée][Définition de plusieurs itinéraires dans une API personnalisée]
-   [Planificateur de travaux][Planificateur de travaux]
    -   [Définition des scripts de travail planifié][Planificateur de travaux]
-   [Contrôle du code source, code partagé et fonctions d'assistance][Contrôle du code source, code partagé et fonctions d'assistance]
    -   [Chargement des modules Node.js][Chargement des modules Node.js]
    -   [Utilisation des fonctions d'assistance][Utilisation des fonctions d'assistance]
    -   [Partage de code à l'aide du contrôle du code source][Partage de code à l'aide du contrôle du code source]
    -   [Utilisation des paramètres d'application][Utilisation des paramètres d'application]
-   [Utilisation de l'outil en ligne de commande][Utilisation de l'outil en ligne de commande]
-   [Utilisation des tables][Utilisation des tables]
    -   [Accès aux tables à partir des scripts][Accès aux tables à partir des scripts]
    -   [Insertion en bloc][Insertion en bloc]
    -   [Mappage des types JSON vers des types de base de données][Mappage des types JSON vers des types de base de données]
    -   [Accès aux tables à l'aide de Transact-SQL][Accès aux tables à l'aide de Transact-SQL]
-   [Débogage et résolution des problèmes][Débogage et résolution des problèmes]
    -   [Écriture de la sortie dans les journaux de service mobile][Écriture de la sortie dans les journaux de service mobile]

## <a name="intro"></a>Introduction

Dans un service mobile principal JavaScript, vous pouvez définir une logique métier personnalisée en tant que code JavaScript stocké et exécuté sur le serveur. Ce code de script serveur est attribué à l'une des fonctionnalités de serveur suivantes :

-   [Opérations d'insertion, de lecture, de mise à jour ou de suppression sur une table donnée][Opérations de table].
-   [Travaux planifiés][Planificateur de travaux].
-   [Méthodes HTTP définies dans une API personnalisée][API personnalisée].

La signature de la fonction principale dans le script serveur dépend du contexte de l'emplacement d'utilisation du script. Vous pouvez également définir un code de script commun comme les modules nodes.js qui sont partagés entre des scripts. Pour plus d'informations, consultez la section [Contrôle du code source et code partagé][Contrôle du code source, code partagé et fonctions d'assistance].

Pour obtenir une description des objets et des fonctions des scripts serveur individuels, consultez la page [Référence de script serveur Mobile Services][Référence de script serveur Mobile Services].

## <a name="table-scripts"></a>Opérations de table

Un script d'opération de table est un script serveur enregistré pour une opération sur une table : insertion, lecture, mise à jour ou suppression (*del*). Le nom du script doit correspondre au type d'opération pour lequel il est enregistré. Un seul script peut être enregistré pour une opération de table donnée. Le script est exécuté chaque fois qu'une opération donnée est appelée par une requête REST, par exemple, lorsqu'une requête POST est reçue pour insérer un élément dans la table. Mobile Services ne conserve pas l'état entre les exécutions du script. Comme un nouveau contexte global est créé à chaque fois qu'un script est exécuté, l'état des variables définies dans le script est réinitialisé. Si vous souhaitez stocker l'état d'une requête à une autre, créez une table dans votre service mobile, puis lisez et écrivez l'état dans la table. Pour plus d'informations, consultez la page [Accès aux tables à partir des scripts][Accès aux tables à partir des scripts].

Vous écrivez des scripts d'opération de table si vous devez appliquer une logique métier personnalisée lors de l'exécution de l'opération. Par exemple, le script suivant rejette les opérations d'insertion lorsque la longueur de la chaîne du champ `text` est supérieure à dix caractères :

    function insert(item, user, request) {
        if (item.text.length > 10) {
            request.respond(statusCodes.BAD_REQUEST, 
                'Text length must be less than 10 characters');
        } else {
            request.execute();
        }
    }

Une fonction de script de table prend toujours trois arguments.

-   Le premier argument varie selon l'opération de table.

    -   Pour les insertions et les mises à jour, il s'agit d'un objet **item**, qui est une représentation JSON de la ligne concernée par l'opération. Cela vous permet d'accéder aux valeurs des colonnes par nom, par exemple, *item.Owner*, où *Owner* est un des noms dans la représentation JSON.
    -   Pour une suppression, il s'agit de l'ID de l'enregistrement à supprimer.
    -   Et pour une lecture, il s'agit d'un [objet query][objet query] spécifiant l'ensemble de lignes à renvoyer.
-   Le deuxième argument est toujours un [objet user][objet user] représentant l'utilisateur ayant envoyé la requête.

-   Le troisième argument est toujours un [objet request][objet request], qui vous permet de contrôler l'exécution de l'opération demandée et la réponse envoyée au client.

Voici les signatures des principales fonctions canoniques pour les opérations de table :

-   [Insertion][Insertion] : `function insert (item, user, request) { ... }`
-   [Mise à jour][Mise à jour] : `function update (item, user, request) { ... }`
-   [Suppression][Suppression] : `function del (id, user, request) { ... }`
-   [Lecture][Lecture] : `function read (query, user, request) { ... }`

> [WACOM.NOTE]Une fonction enregistrée pour une opération de suppression doit être nommée *del*, car delete est un mot clé réservé en JavaScript.

Chaque script serveur dispose d'une fonction principale et peut avoir des fonctions d'assistance facultatives. Même si un script serveur peut avoir été créé pour une table spécifique, il peut également faire référence à d'autres tables dans la même base de données. Vous pouvez également définir des fonctions communes comme les modules qui sont partagés entre des scripts. Pour plus d'informations, consultez la section [Contrôle du code source et code partagé][Contrôle du code source, code partagé et fonctions d'assistance].

### <a name="register-table-scripts"></a> des scripts de table

Vous pouvez définir des scripts serveur qui sont enregistrés pour une opération de table d'une des façons suivantes :

-   Dans le [portail de gestion Azure][portail de gestion Azure]. Les scripts pour les opérations de table sont accessibles sous l'onglet **Scripts** d'une table donnée. Ce qui suit présente le code par défaut enregistré pour le script insert de la table `TodoItem`. Vous pouvez remplacer ce code par votre propre logique métier personnalisée.

    ![1][1]

    Pour plus d'informations, consultez la page [Validation et modification des données dans Mobile Services à l'aide des scripts serveur][Validation et modification des données dans Mobile Services à l'aide des scripts serveur].

-   À l'aide du contrôle de code source Lorsque le contrôle de code source est activé, créez simplement un fichier nommé *`<table>`*.*`<operation>`*.js dans le sous-dossier .\\service\\table de votre référentiel git, où *`<table>`* est le nom de la table et où *`<operation>`* est l'opération de table enregistrée. Pour plus d'informations, consultez la section [Contrôle du code source et code partagé][Contrôle du code source, code partagé et fonctions d'assistance].

-   À partir de l'invite de commandes de l'outil en ligne de commande Azure. Pour plus d'informations, consultez la section [Utilisation de l'outil en ligne de commande][Utilisation de l'outil en ligne de commande].

Un script d'opération de table doit appeler au moins une des fonctions suivantes de l'[objet request][objet request] pour que le client reçoive une réponse.

-   **Fonction execute** : l'opération est effectuée comme demandé et la réponse standard est renvoyée.

-   **Fonction respond** : une réponse personnalisée est renvoyée.

<div class="dev-callout"><strong>Important</strong>
<p>Lorsqu'un script poss&egrave;de un chemin de code dans lequel ni <b>execute</b> ni <b>respond</b> ne sont appel&eacute;es, il se peut que l'op&eacute;ration soit sans r&eacute;ponse.</p></div>

Le script suivant appelle la fonction **execute** pour effectuer l'opération de table demandée par le client :

    function insert(item, user, request) { 
        request.execute(); 
    }

Dans cet exemple, l'élément est inséré dans la base de données et le code d'état approprié est renvoyé à l'utilisateur.

Lorsque la fonction **execute** est appelée, l'élément `item`, la [requête][objet query] ou la valeur `id` qui a été transmis comme premier argument à la fonction du script est utilisé pour effectuer l'opération. Pour une opération d'insertion, de mise à jour ou de requête, vous pouvez modifier l'élément ou la requête avant d'appeler **execute** :

    function insert(item, user, request) { 
        item.scriptComment =
            'this was added by a script and will be saved to the database'; 
        request.execute(); 
    } 

    function update(item, user, request) { 
        item.scriptComment = 
            'this was added by a script and will be saved to the database'; 
        request.execute(); 
    } 

    function read(query, user, request) { 
        // Only return records for the current user         
        query.where({ userid: user.userId}); 
        request.execute(); 
    }

> [WACOM.NOTE]Dans un script de suppression, la modification de valeur de la variable userId fournie n'affecte pas le choix de l'enregistrement à supprimer.

Pour plus d'exemples, consultez les pages [Lire et écrire des données][Lire et écrire des données], [Modifier la requête][Modifier la requête] et [Valider des données][Valider des données].

### <a name="override-response"></a> de la réponse par défaut

Vous pouvez aussi utiliser un script pour implémenter la logique de validation pouvant remplacer le comportement de la réponse par défaut. Si la validation échoue, appelez simplement la fonction **respond** plutôt que la fonction **execute** et écrivez la réponse au client :

    function insert(item, user, request) {
        if (item.userId !== user.userId) {
            request.respond(statusCodes.FORBIDDEN, 
            'You may only insert records with your userId.');
        } else {
            request.execute();
        }
    }

Dans cet exemple, la requête est rejetée lorsque l'élément inséré n'a pas une propriété `userId` correspondant au `userId` de l'[objet tag][objet user] qui est fourni pour le client authentifié. Dans ce cas, une opération de base de données (*insert*) ne se produit pas ; une réponse avec un code d'état HTTP 403 et un message d'erreur personnalisé est renvoyée au client. Pour plus d'exemples, consultez la page [Modifier la réponse][Modifier la réponse].

### <a name="override-success"></a> du succès d'execute

Par défaut, dans une opération de table, la fonction **execute** écrit les réponses automatiquement. Toutefois, vous pouvez transmettre deux paramètres facultatifs à la fonction execute pour remplacer le comportement en cas de succès et/ou d'erreur.

En passant un gestionnaire **success** lorsque vous appelez execute, vous pouvez modifier les résultats d'une requête avant de les écrire dans la réponse. L'exemple suivant appelle `execute({ success: function(results) { ... })` pour effectuer des tâches supplémentaires une fois que les données sont lues à partir de la base de données, mais avant que la réponse soit écrite :

    function read(query, user, request) {
        request.execute({
            success: function(results) {
                results.forEach(function(r) {
                    r.scriptComment = 
                    'this was added by a script after querying the database';
                });
                request.respond();
            }
        });
    }

Lorsque vous fournissez un gestionnaire **success** à la fonction **execute**, vous devez aussi appeler la fonction **respond** dans le gestionnaire **success** pour que le runtime sache que le script est terminé et qu'une réponse peut être écrite. Lorsque vous appelez **respond** sans passer d'argument, Mobile Services génère la réponse par défaut.

> [WACOM.NOTE]Vous pouvez appeler **respond** sans argument pour appeler la réponse par défaut uniquement après avoir appelé la fonction **execute**.

### <a name="override-error"></a> de la gestion des erreurs par défaut

La fonction **execute** peut échouer en cas de perte de la connectivité à la base de données, d'un objet non valide ou d'une requête incorrecte. Par défaut, lorsqu'une erreur se produit, les scripts serveur consignent l'erreur et écrivent un résultat d'erreur dans la réponse. Comme Mobile Services fournit une gestion des erreurs par défaut, vous n'avez pas à gérer les erreurs qui peuvent se produire dans le service.

Vous pouvez remplacer la gestion des erreurs par défaut en implémentant une gestion des erreurs explicite si vous souhaitez une action de compensation particulière ou lorsque vous souhaitez utiliser l'objet console global pour écrire des informations détaillées dans le journal. Pour ce faire, fournissez un gestionnaire **error** dans la fonction **execute** :

    function update(item, user, request) { 
      request.execute({ 
        error: function(err) { 
          // Do some custom logging, then call respond. 
          request.respond(); 
        } 
      }); 
    }

Lorsque vous fournissez un gestionnaire des erreurs, Mobile Services renvoie un résultat d'erreur au client lorsque la fonction **respond** est appelée.

Vous pouvez aussi fournir un gestionnaire **success** et un gestionnaire **error** si vous le souhaitez.

### <a name="access-headers"></a> aux paramètres personnalisés

Lorsque vous envoyez une requête à votre service mobile, vous pouvez inclure des paramètres personnalisés dans l'URI de la requête pour indiquer à vos scripts d'opération de table comment traiter une requête donnée. Vous modifiez alors votre script pour examiner le paramètre afin de déterminer le chemin du traitement.

Par exemple, l'URI suivant pour une requête POST indique au service de ne pas autoriser l'insertion d'un nouveau *TodoItem* possédant la même valeur de texte :

        https://todolist.azure-mobile.net/tables/TodoItem?duplicateText=false

Ces paramètres de requête personnalisés sont accessibles comme des valeurs JSON à partir de la propriété **parameters** de l'[objet request][objet request]. L'objet **request** est fourni par Mobile Services aux fonctions enregistrées avec une opération de table. Le script serveur suivant pour l'opération insert vérifie la valeur du paramètre `duplicateText` avant l'exécution de l'opération insert :

        function insert(item, user, request) {
            var todoItemTable = tables.getTable('TodoItem');
            // Check the supplied custom parameter to see if
            // we should allow duplicate text items to be inserted.        
            if (request.parameters.duplicateText === 'false') {
                // Find all existing items with the same text
                // and that are not marked 'complete'. 
                todoItemTable.where({
                    text: item.text,
                    complete: false
                }).read({
                    success: insertItemIfNotComplete
                });
            } else {
                request.execute();
            }

            function insertItemIfNotComplete(existingItems) {
                if (existingItems.length > 0) {
                    request.respond(statusCodes.CONFLICT, 
                        "Duplicate items are not allowed.");
                } else {
                    // Insert the item as normal. 
                    request.execute();
                }
            }
        }

Notez que dans **insertItemIfNotComplete**, la fonction **execute** de l'[objet request][objet request] est appelée pour insérer l'élément s'il n'y a pas de texte dupliqué ; sinon, la fonction **respond** est appelée pour avertir le client du doublon.

Notez la syntaxe de l'appel à la fonction **success** dans le code ci-dessus :

                }).read({
                    success: insertItemIfNotComplete
                });

En JavaScript, c'est une version courte équivalente à la version longue :

        success: function(results) 
        { 
            insertItemIfNotComplete(results); 
        }

### <a name="work-with-users"></a> des utilisateurs

Dans Azure Mobile Services, vous pouvez utiliser un fournisseur d'identité pour authentifier les utilisateurs. Pour plus d'informations, consultez la page [Prise en main de l'authentification][Prise en main de l'authentification]. Lorsqu'un utilisateur authentifié appelle une opération de table, Mobile Services utilise l'[objet user][objet user] pour fournir les informations sur l'utilisateur à la fonction de script enregistrée. La propriété **userId** peut être utilisée pour stocker et récupérer des informations spécifiques de l'utilisateur. L'exemple suivant définit la propriété owner pour un élément basé sur l'élément userId d'un utilisateur authentifié :

    function insert(item, user, request) {
        item.owner = user.userId;
        request.execute();
    }

L'exemple suivant ajoute un filtre supplémentaire à la requête basée sur l'élément **userId** d'un utilisateur authentifié. Ce filtre restreint les résultats aux éléments appartenant à l'utilisateur actuel :

    function read(query, user, request) {
        query.where({
            owner: user.userId
        });
        request.execute();
    }

## <a name="custom-api"></a>API personnalisée

Une API personnalisée est un point de terminaison dans votre service mobile qui est accessible par une ou plusieurs méthodes HTTP standard : GET, POST, PUT, PATCH et DELETE. Une exportation de fonction distincte peut être définie pour chaque méthode HTTP prise en charge par l'API personnalisée, le tout dans un seul fichier script. Le script enregistré est appelé lorsqu'une requête pour l'API personnalisée est reçue à l'aide d'une méthode donnée. Pour plus d'informations, consultez la page [API personnalisée][2].

Lorsque les fonctions de l'API personnalisée sont appelées par le runtime Mobile Services, un objet [request][objet request] et un objet [response][response] sont fournis. Ces objets exposent la fonctionnalité de la [bibliothèque express.js][bibliothèque express.js], qui peut être utilisée par vos scripts. L'API personnalisée suivante nommée **hello** est un exemple très simple qui renvoie *Hello, world!* en réponse à une requête POST :

        exports.post = function(request, response) {
            response.send(200, "{ message: 'Hello, world!' }");
        } 

La fonction **send** de l'[objet response][response] renvoie la réponse voulue au client. Ce code est appelé en envoyant une requête POST à l'URL suivante :

        https://todolist.azure-mobile.net/api/hello  

L'état global est conservé entre les exécutions.

### <a name="define-custom-api"></a> d'une API personnalisée

Vous pouvez définir des scripts serveur qui sont enregistrés pour des méthodes HTTP dans un point de terminaison d'API personnalisée de l'une des façons suivantes :

-   Dans le [portail de gestion Azure][portail de gestion Azure]. Les scripts de l'API personnalisée sont créés et modifiés dans l'onglet **API**. Le code du script serveur se trouve dans l'onglet **Scripts** d'une API personnalisée donnée. Ce qui suit affiche le script appelé par une requête POST au point de terminaison de l'API personnalisée `CompleteAll`.

    ![2][3]

    Les autorisations d'accès aux méthodes de l'API personnalisée sont attribuées dans l'onglet Autorisations. Pour plus d'informations sur la création de cette API personnalisée, consultez la page [Appel d'une API personnalisée à partir du client][Appel d'une API personnalisée à partir du client].

-   À l'aide du contrôle de code source Lorsque le contrôle de code source est activé, créez simplement un fichier nommé *`<custom_api>`*.js dans le sous-dossier .\\service\\api de votre référentiel git, où *`<custom_api>`* est le nom de l'API personnalisée qui est enregistrée. Ce fichier de script contient une fonction *exportée* pour chaque méthode HTTP exposée par l'API personnalisée. Les autorisations sont définies dans un fichier complément .json. Pour plus d'informations, consultez la section [Contrôle du code source et code partagé][Contrôle du code source, code partagé et fonctions d'assistance].

-   À partir de l'invite de commandes de l'outil en ligne de commande Azure. Pour plus d'informations, consultez la section [Utilisation de l'outil en ligne de commande][Utilisation de l'outil en ligne de commande].

### <a name="handle-methods"></a> des méthodes HTTP

Une API personnalisée peut gérer une ou plusieurs des méthodes HTTP (GET, POST, PUT, PATCH et DELETE). Une fonction exportée est définie pour chaque méthode HTTP gérée par l'API personnalisée. Un seul fichier de code d'API personnalisée peut exporter une ou toutes les fonctions suivantes :

        exports.get = function(request, response) { ... };
        exports.post = function(request, response) { ... };
        exports.patch = function(request, response) { ... };
        exports.put = function(request, response) { ... };
        exports.delete = function(request, response) { ... };

Le point de terminaison de l'API personnalisée ne peut pas être appelé à l'aide d'une méthode HTTP qui n'est pas implémentée dans le script serveur, et une réponse indiquant une erreur 405 (Méthode non autorisée) est renvoyée. Des niveaux d'autorisation distincts peuvent être attribués à chaque méthode HTTP de prise en charge.

### <a name="api-return-xml"></a> et réception des données au format XML

Lorsque les clients stockent et récupèrent les données, Mobile Services utilise JavaScript Object Notation (JSON) pour représenter les données dans le corps du message. Toutefois, il existe des scénarios dans lesquels vous souhaitez utiliser une charge utile XML. Par exemple, les applications Windows Store intègrent une fonctionnalité de notifications périodiques qui nécessite que le service émette du code XML. Pour plus d'informations, consultez la page [Définition d'une API personnalisée prenant en charge les notifications périodiques][Définition d'une API personnalisée prenant en charge les notifications périodiques].

La fonction de l'API personnalisée **OrderPizza** renvoie un document XML simple comme charge utile de réponse :

        exports.get = function(request, response) {
          response.set('content-type', 'application/xml');
          var xml = '<?xml version="1.0"?><PizzaOrderForm><PizzaOrderForm/>';
          response.send(200, xml);
        };

Cette fonction de l'API personnalisée est appelée par une requête HTTP GET au point de terminaison suivant :

        https://todolist.azure-mobile.net/api/orderpizza

### <a name="get-api-user"></a> des utilisateurs et des en-têtes dans une API personnalisée

Dans Azure Mobile Services, vous pouvez utiliser un fournisseur d'identité pour authentifier les utilisateurs. Pour plus d'informations, consultez la page [Prise en main de l'authentification][Prise en main de l'authentification]. Lorsqu'un utilisateur authentifié appelle une API personnalisée, Mobile Services utilise l'[objet user][objet user] pour fournir les informations sur l'utilisateur permettant de personnaliser le code de l'API. L'[objet user][objet user] est accessible depuis la propriété user de l'[objet request][objet request]. La propriété **userId** peut être utilisée pour stocker et récupérer des informations spécifiques de l'utilisateur.

La fonction de l'API personnalisée **OrderPizza** définit la propriété owner pour un élément basé sur l'élément userId d'un utilisateur authentifié :

        exports.post = function(request, response) {
            var userTable = request.service.tables.getTable('user');
            userTable.lookup(request.user.userId, {
                success: function(userRecord) {
                    callPizzaAPI(userRecord, request.body, function(orderResult) {
                        response.send(201, orderResult);
                    });
                }
            });
        
        };

Cette fonction de l'API personnalisée est appelée par une requête HTTP POST au point de terminaison suivant :

        https://<service>.azure-mobile.net/api/orderpizza

Vous pouvez aussi accéder à un en-tête HTTP spécifique à partir de l'[objet request][objet request], comme dans le code suivant :

        exports.get = function(request, response) {    
            var header = request.header('my-custom-header');
            response.send(200, "You sent: " + header);
        };

Cet exemple simple lit un en-tête personnalisé nommé `my-custom-header`, puis renvoie la valeur dans la réponse.

### <a name="api-routes"></a> de plusieurs itinéraires dans une API personnalisée

Mobile Services vous permet de définir plusieurs chemins ou itinéraires dans une API personnalisée. Par exemple, des requêtes HTTP GET vers les URL suivantes dans une API personnalisée **calculator** appellent une fonction **add** ou **subtract**, respectivement :

-   `https://<service>.azure-mobile.net/api/calculator/add`
-   `https://<service>.azure-mobile.net/api/calculator/sub`

Plusieurs itinéraires sont définis en exportant une fonction **register**, à qui est transmis un objet **api** (semblable à l'[objet express dans express.js][objet express dans express.js]) qui est utilisé pour enregistrer les itinéraires sous le point de terminaison de l'API personnalisée. L'exemple suivant implémente les méthodes **add** et **sub** dans l'API personnalisée **calculator** :

        exports.register = function (api) {
            api.get('add', add);
            api.get('sub', subtract);
        }
        
        function add(req, res) {
            var result = parseInt(req.query.a) + parseInt(req.query.b);
            res.send(200, { result: result });
        }
        
        function subtract(req, res) {
            var result = parseInt(req.query.a) - parseInt(req.query.b);
            res.send(200, { result: result });
        }

L'objet **api** transmis à la fonction **register** expose une fonction pour chaque méthode HTTP (**get**, **post**, **put**, **patch** et **delete**). Ces fonctions enregistrent un itinéraire sur une fonction définie pour une méthode HTTP spécifique. Chaque fonction prend deux paramètres, le premier étant le nom de l'itinéraire, et le deuxième la fonction enregistrée pour l'itinéraire.

Les deux itinéraires de l'exemple d'API personnalisée ci-dessus peuvent être appelés par des requêtes HTTP GET comme suit (présentés avec la réponse) :

-   `https://<service>.azure-mobile.net/api/calculator/add?a=1&b=2`

        {"result":3}

-   `https://<service>.azure-mobile.net/api/calculator/sub?a=3&b=5`

        {"result":-2}

## <a name="scheduler-scripts"></a>Planificateur de travaux

Mobile Services vous permet de définir des scripts serveur qui sont exécutés comme des travaux sur une planification fixe ou à la demande à partir du portail de gestion. Les travaux planifiés sont utiles pour effectuer des tâches périodiques comme le nettoyage des données de table et le traitement par lots. Pour plus d'informations, consultez la page [Planifier les travaux][Planifier les travaux].

Les scripts qui sont enregistrés pour des travaux planifiés ont une fonction principale avec le même nom que le travail planifié. Étant donné qu'un script planifié n'est pas appelé par une requête HTTP, aucun contexte ne peut être transmis au runtime du serveur et la fonction ne prend pas de paramètre. Comme d'autres types de scripts, vous pouvez avoir des fonctions de sous-routine et des modules partagés. Pour plus d'informations, consultez la section [Contrôle du code source, code partagé et fonctions d'assistance][Contrôle du code source, code partagé et fonctions d'assistance].

### <a name="scheduler-scripts"></a> des scripts de travail planifié

Un script serveur peut être attribué à un travail qui est défini dans le planificateur Mobile Services. Ces scripts appartiennent au travail et sont exécutés selon la planification du travail. (Vous pouvez aussi utiliser le [portail de gestion][portail de gestion Azure] pour exécuter des travaux à la demande.) Un script définissant un travail planifié n'a pas de paramètre, car Mobile Services ne lui transmet aucune donnée ; il est exécuté comme une fonction JavaScript régulière et n'interagit pas directement avec Mobile Services.

Vous définissez des travaux planifiés de l'une des façons suivantes :

-   Dans le [portail de gestion Azure][portail de gestion Azure], sous l'onglet **Script** dans le planificateur :

    ![3][4]

    Pour plus d'informations, consultez la page [Planification des tâches principales dans Mobile Services][Planification des tâches principales dans Mobile Services].

-   À partir de l'invite de commandes de l'outil en ligne de commande Azure. Pour plus d'informations, consultez la section [Utilisation de l'outil en ligne de commande][Utilisation de l'outil en ligne de commande].

> [WACOM.NOTE]Lorsque le contrôle du code source est activé, vous pouvez modifier les fichiers de script du travail planifié directement dans le sous-dossier .\\service\\scheduler de votre référentiel git. Pour plus d'informations, consultez la page [Partage de code à l'aide du contrôle du code source][Partage de code à l'aide du contrôle du code source].

## <a name="shared-code"></a>Contrôle du code source, code partagé et fonctions d'assistance

Comme Mobile Services utilise Node.js sur le serveur, vos scripts ont déjà accès aux modules Node.js intégrés. Vous pouvez aussi utiliser le contrôle du code source pour définir vos propres modules ou ajouter d'autres modules Node.js à votre service.

Voici une partie des modules les plus utiles pouvant être utilisés dans vos scripts à l'aide de la fonction globale **require** :

-   **azure** : expose la fonctionnalité du Kit de développement logiciel (SDK) Azure pour Node.js.Pour plus d'informations, consultez la page [Kit de développement logiciel (SDK) Azure pour Node.js][Kit de développement logiciel (SDK) Azure pour Node.js].
-   **crypto** : fournit la fonctionnalité crypto d'OpenSSL. Pour plus d'informations, consultez la [documentation Node.js][documentation Node.js].
-   **path** : contient des utilitaires pour utiliser les chemins de fichier. Pour plus d'informations, consultez la [documentation Node.js][5].
-   **querystring** : contient des utilitaires pour utiliser les chaînes de requête. Pour plus d'informations, consultez la [documentation Node.js][6].
-   **request** : envoie des requêtes HTTP aux services REST externes, comme Twitter et Facebook. Pour plus d'informations, consultez la page [Envoyer une requête HTTP][Envoyer une requête HTTP].
-   **sendgrid** : envoie le courrier électronique à l'aide du service de messagerie Sendgrid dans Azure. Pour plus d'informations, consultez la page [Envoi de courrier électronique à partir de Mobile Services avec SendGrid][Envoi de courrier électronique à partir de Mobile Services avec SendGrid].
-   **url** : contient des utilitaires pour analyser et résoudre les URL. Pour plus d'informations, consultez la [documentation Node.js][7].
-   **util** : contient divers utilitaires, comme le formatage de chaînes et la vérification du type d'objet. Pour plus d'informations, consultez la [documentation Node.js][8].
-   **zlib** : expose la fonctionnalité de compression, comme gzip et deflate. Pour plus d'informations, consultez la [documentation Node.js][9].

### <a name="modules-helper-functions"></a> des modules

Mobile Services expose un ensemble de modules que les scripts peuvent charger à l'aide de la fonction globale **require**. Par exemple, un script peut demander **request** pour effectuer des requêtes HTTP :

    function update(item, user, request) { 
        var httpRequest = require('request'); 
        httpRequest('http://www.google.com', function(err, response, body) { 
            ... 
        }); 
    } 

### <a name="shared-code-source-control"></a> de code à l'aide du contrôle du code source

Vous pouvez utiliser le contrôle du code source avec le gestionnaire de package Node.js (npm) pour contrôler la disponibilité des modules pour votre service mobile. Il existe deux façons d'effectuer cette opération :

-   Pour les modules qui sont publiés et installés par npm, utilisez le fichier package.json pour déclarer quels packages vous souhaitez installer pour votre service mobile. Ainsi, votre service a toujours accès à la dernière version des packages requis. Le fichier package.json se trouve dans le répertoire `.\service`. Pour plus d'informations, consultez la page [Prise en charge de package.json dans Azure Mobile Services][Prise en charge de package.json dans Azure Mobile Services].

-   Pour les modules privés ou personnalisés, vous pouvez utiliser npm pour installer manuellement le module dans le répertoire `.\service\node_modules` de votre contrôle du code source. Pour un exemple de chargement manuel d'un module, consultez la page [Utilisation du code partagé et des modules Node.js dans vos scripts serveur][Utilisation du code partagé et des modules Node.js dans vos scripts serveur].

    > [WACOM.NOTE]Lorsque `node_modules` existe déjà dans la hiérarchie de répertoires, NPM crée le sous-répertoire `\node-uuid` existe déjà dans la hiérarchie de répertoires, NPM crée le sous-répertoire`node_modules` dans le référentiel. Dans ce cas, supprimez simplement le répertoire `node_modules` existant.

Après avoir validé le fichier package.json ou les modules personnalisés sur le référentiel pour votre service mobile, utilisez **require** pour référencer les modules par nom.

> [WACOM.NOTE] Les modules que vous spécifiez dans package.json ou que vous chargez sur votre service mobile sont uniquement utilisés dans le code de votre script serveur. Ils ne sont pas utilisés par le runtime Mobile Services.

### <a name="helper-functions"></a> des fonctions d'assistance

En plus de nécessiter des modules, chaque script serveur peut inclure des fonctions d'assistance. Ce sont des fonctions qui sont séparées de la fonction principale et peuvent être utilisées pour factoriser le code du script.

Dans l'exemple suivant, un script de table est enregistré pour une opération insert, qui inclut la fonction d'assistance **handleUnapprovedItem** :

    function insert(item, user, request) {
        if (!item.approved) {
            handleUnapprovedItem(item, user, request);
        } else {
            request.execute();
        }
    }

    function handleUnapprovedItem(item, user, request) {
        // Do something with the supplied item, user, or request objects.
    }

Dans un script, les fonctions d'assistance doivent être déclarées après la fonction principale. Vous devez déclarer toutes les variables dans votre script. Les variables non déclarées provoquent une erreur.

Les fonctions d'assistance peuvent aussi être définies une fois et partagées entre les scripts serveur. Pour partager une fonction entre des scripts, les fonctions doivent être exportées et le fichier de script doit exister dans le répertoire `.\service\shared\`. Ce qui suit est un modèle d'exportation d'une fonction partagée dans un fichier `.\services\shared\helpers.js` :

        exports.handleUnapprovedItem = function (tables, user, callback) {
            
            // Do something with the supplied tables or user objects and 
            // return a value to the callback function.
        };

Vous pouvez alors utiliser une fonction semblable dans un script d'opération de table :

        function insert(item, user, request) {
            var helper = require('../shared/helper');
            helper.handleUnapprovedItem(tables, user, function(result) {
                    
                    // Do something based on the result.
                    request.execute();
                }
            }
        }

Dans cet exemple, vous devez passer un [objet tables][objet tables] et un [objet user][objet user] à la fonction partagée. Ceci est dû au fait que les scripts partagés ne peuvent pas accéder à l'[objet tables][objet tables] global et que l'[objet user][objet user] existe uniquement dans le contexte d'une requête.

Les fichiers de script sont chargés sur le répertoire partagé à l'aide du [contrôle du code source][Partage de code à l'aide du contrôle du code source] ou de l'[outil en ligne de commande][Utilisation de l'outil en ligne de commande].

### <a name="app-settings"></a> des paramètres d'application

Mobile Services vous permet de stocker en toute sécurité les valeurs en tant que paramètres d'application qui seront accessibles par vos scripts serveur au moment de l'exécution. Lorsque vous ajoutez des données aux paramètres d'application de votre service mobile, les paires nom/valeur sont stockées de manière chiffrée et vous pouvez y accéder dans vos scripts serveur sans les coder en dur dans votre fichier de script. Pour plus d'informations, consultez l'article [Paramètres de l'application][Paramètres de l'application].

L'exemple d'API personnalisée suivant utilise l'[objet service][objet service] fourni pour récupérer la valeur du paramètre d'application.

        exports.get = function(request, response) {
        
            // Get the MY_CUSTOM_SETTING value from app settings.
            var customSetting = 
                request.service.config.appSettings.my_custom_setting;
                
            // Do something and then send a response.

        }

Le code suivant utilise le module de configuration pour récupérer les valeurs de jeton d'accès Twitter stockées dans les paramètres d'application et qui sont utilisées dans un script de travail planifié :

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret. 
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.    
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

Notez que ce code récupère également les valeurs de la clé Twitter stockée sous l'onglet **Identité** du portail. Comme un **objet config** n'est pas disponible dans les scripts de l'opération de la table et du travail planifié, le module de configuration est requis pour accéder aux paramètres d'application. Pour obtenir un exemple complet, consultez la page [Planification des travaux principaux dans Mobile Services][Planification des tâches principales dans Mobile Services].

## <a name="command-prompt"></a>Utilisation de l'outil en ligne de commande

Dans Mobile Services, vous pouvez créer, modifier et supprimer des scripts serveur en utilisant l'outil en ligne de commande Azure. Avant de charger vos scripts, assurez-vous que vous utilisez la structure de répertoires suivante :

![4][10]

Notez que cette structure de répertoires est identique au référentiel git lors de l'utilisation du contrôle du code source.

Lors du chargement de fichiers de script à partir de l'outil en ligne de commande, vous devez d'abord accéder au répertoire `.\services\`. La commande suivante charge un script nommé `todoitem.insert.js` à partir du sous-répertoire `table` :

        ~$azure mobile script upload todolist table/todoitem.insert.js
        info:    Executing command mobile script upload
        info:    mobile script upload command OK

La commande suivante renvoie des informations sur chaque fichier de script conservé dans votre service mobile :

        ~$ azure mobile script list todolist
        info:    Executing command mobile script list
        + Retrieving script information
        info:    Table scripts
        data:    Name                       Size
        data:    -------------------------  ----
        data:    table/channels.insert      1980
        data:    table/TodoItem.insert      5504
        data:    table/TodoItem.read        64
        info:    Shared scripts
        data:    Name              Size
        data:    ----------------  ----
        data:    shared/helper.js  62
        data:    shared/uuid.js    7452
        info:    Scheduled job scripts
        data:    Job name    Script name           Status    Interval     Last run  Next run
        data:    ----------  --------------------  --------  -----------  --------  --------
        data:    getUpdates  scheduler/getUpdates  disabled  15 [minute]  N/A       N/A
        info:    Custom API scripts
        data:    Name                    Get          Put          Post         Patch        Delete
        data:    ----------------------  -----------  -----------  -----------  -----------  -----------
        data:    completeall             application  application  application  application  application
        data:    register_notifications  application  application  user         application  application
        info:    mobile script list command OK

Pour plus d'informations, consultez la page [Commandes pour gérer Azure Mobile Services][Commandes pour gérer Azure Mobile Services].

## <a name="working-with-tables"></a>Utilisation des tables

De nombreux scénarios dans Mobile Services nécessitent des scripts serveur pour accéder aux tables dans la base de données. Par exemple, comme Mobile Services ne conserve pas l'état entre les exécutions de script, les données devant être persistantes entre les exécutions de script doivent être stockées dans des tables. Vous pouvez examiner les entrées dans une table d'autorisations ou stocker les données d'audit au lieu d'écrire simplement sur le journal dans lequel les données ont une durée limitée et ne sont pas accessibles par programme.

Il existe deux moyens d'accéder aux tables dans Mobile Services, soit en utilisant un proxy d'[objet table][objet table], soit en composant des requêtes Transact-SQL à l'aide de l'[objet mssql][objet mssql]. L'[objet table][objet table] facilite l'accès aux données des tables à partir de votre code de script serveur, mais l'[objet mssql][objet mssql] prend en charge les opérations de données plus complexes et offre le plus de flexibilité.

### <a name="access-tables"></a> aux tables à partir des scripts

Le moyen le plus simple d'accéder aux tables à partir de votre script consiste à utiliser l'[objet tables][objet tables]. La fonction **getTable** renvoie une instance d'[objet table][objet table] qui est un proxy pour accéder à la table demandée. Vous pouvez ensuite appeler des fonctions sur le proxy pour accéder aux données et les modifier.

Les scripts enregistrés sur les opérations de table et les travaux planifiés peuvent accéder à l'[objet tables][objet tables] en tant qu'objet global. Cette ligne de code permet d'obtenir un proxy pour la table *TodoItems* à partir de l'[objet tables][objet tables] global :

        var todoItemsTable = tables.getTable('TodoItems');

Les scripts d'API personnalisée peuvent accéder à l'[objet tables][objet tables] à partir de la propriété **service** de l'[objet request][objet request] fourni. Cette ligne de code permet d'obtenir un [objet tables][objet tables] à partir de la requête :

        var todoItemsTable = request.service.tables.getTable('TodoItem');

<div class="dev-callout"><strong>Remarque</strong>
<p>Les fonctions partag&eacute;es ne peuvent pas acc&eacute;der &agrave; l'objet <strong>tables</strong> directement. Dans une fonction partag&eacute;e, vous devez passer l'objet tables &agrave; la fonction.</p></div>

Une fois que vous avez un [objet table][objet table], vous pouvez appeler une ou plusieurs fonctions d'opération de table : insérer, mettre à jour, supprimer ou lire. Dans cet exemple, les autorisations de l'utilisateur sont lues à partir de la table permissions :

    function insert(item, user, request) {
        var permissionsTable = tables.getTable('permissions');

        permissionsTable
            .where({ userId: user.userId, permission: 'submit order'})
            .read({ success: checkPermissions });
            
        function checkPermissions(results) {
            if(results.length > 0) {
                // Permission record was found. Continue normal execution.
                request.execute();
            } else {
                console.log('User %s attempted to submit an order without permissions.', user.userId);
                request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
            }
        }
    }

L'exemple suivant écrit des informations d'audit dans une table **audit** :

    function update(item, user, request) {
        request.execute({ success: insertAuditEntry });
        
        function insertAuditEntry() {
            var auditTable = tables.getTable('audit');
            var audit = {
                record: 'checkins',
                recordId: item.id,
                timestamp: new Date(),
                values: JSON.stringify(item)
            };
            auditTable.insert(audit, {
                success: function() {
                    // Write to the response now that all data operations are complete
                    request.respond();
                }
            });
        }
    }

L'exemple de code ci-dessous est un autre exemple : [aux paramètres personnalisés][Ajout de paramètres personnalisés].

### <a name="bulk-inserts"></a> en bloc

Si vous utilisez une boucle **for** ou **while** pour insérer directement un grand nombre d'éléments (1 000, par exemple) dans une table, il se peut que vous rencontriez une limite de connexion SQL déclenchant l'échec de certaines insertions. Votre requête peut ne jamais se terminer ou elle peut renvoyer une erreur interne du serveur HTTP 500. Pour éviter ce problème, vous pouvez insérer les éléments par lot de 10 par exemple. Après l'insertion du premier lot, soumettez le batch suivant, et ainsi de suite.

Le script suivant vous permet de définir la taille d'un lot d'enregistrements à insérer en parallèle. Il est recommandé de conserver un petit nombre d'enregistrements. La fonction **insertItems** s'appelle de manière récursive lorsqu'une insertion de lot asynchrone a été effectuée. À la fin, la boucle for insère un enregistrement à la fois et appelle **insertComplete** en cas de succès et **errorHandler** en cas d'erreur. **insertComplete** contrôle si **insertItems** est appelé de manière récursive pour le lot suivant ou si le travail est effectué, auquel cas le script doit s'arrêter.

        var todoTable = tables.getTable('TodoItem');
        var recordsToInsert = 1000;
        var batchSize = 10; 
        var totalCount = 0;
        var errorCount = 0; 
        
        function insertItems() {        
            var batchCompletedCount = 0;  
        
            var insertComplete = function() { 
                batchCompletedCount++; 
                totalCount++; 
                if(batchCompletedCount === batchSize || totalCount === recordsToInsert) {                        
                    if(totalCount < recordsToInsert) {
                        // kick off the next batch 
                        insertItems(); 
                    } else { 
                        // or we are done, report the status of the job 
                        // to the log and don't do any more processing 
                        console.log("Insert complete. %d Records processed. There were %d errors.", totalCount, errorCount); 
                    } 
                } 
            }; 
        
            var errorHandler = function(err) { 
                errorCount++; 
                console.warn("Ignoring insert failure as part of batch.", err); 
                insertComplete(); 
            };
        
            for(var i = 0; i < batchSize; i++) { 
                var item = { text: "This is item number: " + totalCount + i }; 
                todoTable.insert(item, { 
                    success: insertComplete, 
                    error: errorHandler 
                }); 
            } 
        } 
        
        insertItems(); 

Vous trouverez l'intégralité de cet exemple de code accompagné de sa discussion dans ce [billet de blog][billet de blog]. Si vous utilisez ce code, vous pouvez l'adapter à votre situation particulière et le tester de manière approfondie.

### <a name="JSON-types"></a> des types JSON vers des types de base de données

Les collections de types de données sont différentes sur le client et dans une table de base de données Mobile Services. À certains moments, elles sont mises facilement en correspondance l'une avec l'autre, et à d'autres moments non. Mobile Services effectue de nombreuses transformations de types dans le mappage :

-   Les types propres au langage du client sont sérialisés en JSON.
-   La représentation JSON est traduite en JavaScript avant d'apparaître dans les scripts serveur.
-   Les types de données JavaScript sont convertis en types de base de données SQL lors de l'enregistrement à l'aide de l'[objet tables][objet tables].

La transformation à partir du schéma client en JSON varie selon les plateformes. JSON.NET est utilisé dans les clients Windows Phone et Windows Store. Le client Android utilise la bibliothèque gson. Le client iOS utilise la classe NSJSONSerialization. Le comportement de sérialisation par défaut de chacune de ces bibliothèques est utilisé, sauf que les objets date sont convertis en chaînes JSON contenant la date encodée à l'aide de la norme ISO 8601.

Lorsque vous écrivez des scripts serveur utilisant les fonctions [insert][Insertion], [update][Mise à jour], [read][Lecture] ou [delete][Suppression], vous pouvez accéder à la représentation JavaScript de vos données. Mobile Services utilise la fonction de désérialisation de Node.js ([JSON.parse][JSON.parse]) pour transformer JSON simultanément en objets JavaScript. Toutefois, Mobile Services effectue une transformation pour extraire les objets **Date** à partir des chaînes ISO 8601.

Lorsque vous utilisez l'[objet tables][objet tables] ou l'[objet mssql][objet mssql] ou pour simplement laisser vos scripts de table s'exécuter, les objets JavaScript désérialisés sont insérés dans une base de données SQL. Dans ce processus, les propriétés des objets sont mappées aux types T-SQL :

<table>
<tr><td> Propriété JavaScript </td><td> Type T-SQL         </td></tr>
<tr><td> Number               </td><td> Float(53)          </td></tr>
<tr><td> Boolean              </td><td> Bit                </td></tr>
<tr><td> Date                 </td><td> DateTimeOffset(3)  </td></tr>
<tr><td> String               </td><td> Nvarchar(max)      </td></tr>
<tr><td> Buffer               </td><td> Non pris en charge </td></tr>
<tr><td> Object               </td><td> Non pris en charge </td></tr>
<tr><td> Array                </td><td> Non pris en charge </td></tr>
<tr><td> Stream               </td><td> Non pris en charge </td></tr>
</table>

### <a name="TSQL"></a>Accès aux tables à l'aide de Transact-SQL

Le moyen le plus simple d'utiliser les données de table à partir de scripts serveur consiste à utiliser un proxy d'[objet table][objet table]. Toutefois, il existe des scénarios plus avancés qui ne sont pas pris en charge par l'[objet table][objet table], comme les requêtes de jonction et d'autres requêtes complexes ainsi que l'appel de procédures stockées. Dans ces cas-là, vous devez exécuter des instructions Transact-SQL directement sur la table relationnelle à l'aide de l'[objet mssql][objet mssql]. Cet objet fournit les fonctions suivantes :

-   **query** : exécute une requête, spécifiée par une chaîne TSQL ; les résultats sont renvoyés au rappel **success** sur l'objet **options**. La requête peut inclure des paramètres si le paramètre *params* est présent.
-   **queryRaw** : identique à *query* à l'exception que le jeu de résultats renvoyé à partir de la requête est dans un format « brut » (voir l'exemple ci-dessous).
-   **open** : utilisé pour obtenir une connexion à partir de votre base de données Mobile Services. Vous pouvez ensuite utiliser l'objet de connexion pour appeler les opérations de base de données comme les transactions.

Ces méthodes vous donnent progressivement un meilleur contrôle de bas niveau sur le traitement de la requête.

-   [Exécution d'une requête statique][Exécution d'une requête statique]
-   [Exécution d'une requête dynamique][Exécution d'une requête dynamique]
-   [Jonction des tables relationnelles][Jonction des tables relationnelles]
-   [Exécution d'une requête renvoyant des résultats *bruts*][Exécution d'une requête renvoyant des résultats *bruts*]
-   [Obtention de l'accès à une connexion de base de données][Obtention de l'accès à une connexion de base de données]

#### <a name="static-query"></a> d'une requête statique

La requête suivante n'a pas de paramètre et renvoie trois enregistrements à partir de la table `statusupdate`. L'ensemble de lignes est au format JSON standard.

        mssql.query('select top 3 * from statusupdates', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
            }
        });

#### <a name="dynamic-query"></a> d'une requête dynamique paramétrable

L'exemple suivant implémente l'autorisation personnalisée par des autorisations de lecture pour chaque utilisateur à partir de la table permissions. L'espace réservé (?) est remplacé par le paramètre fourni lors de l'exécution de la requête.

            var sql = "SELECT _id FROM permissions WHERE userId = ? AND permission = 'submit order'";
            mssql.query(sql, [user.userId], {
                success: function(results) {
                    if (results.length > 0) {
                        // Permission record was found. Continue normal execution. 
                        request.execute();
                    } else {
                        console.log('User %s attempted to submit an order without permissions.', user.userId);
                        request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
                    }
                },
                error: function(err) {
                    console.log("error is: " + err);
                }   
            });

#### <a name="joins"></a> des tables relationnelles

Vous pouvez joindre deux tables en utilisant la méthode **query** de l'[objet mssql][objet mssql] à passer dans le code TSQL qui implémente la jonction. Supposons qu'il y ait quelques éléments dans la table **ToDoItem** et que chacun d'eux ait une propriété **priority** qui corresponde à une colonne dans la table. Un élément peut ressembler à ceci :

        { text: 'Take out the trash', complete: false, priority: 1}

Supposons également qu'il y ait une table supplémentaire appelée **Priority** avec des lignes contenant un **number** (ordre) de priorité et une **description** sous forme de texte. Par exemple, l'ordre de priorité 1 peut avoir « Critique » pour description et l'objet ressemble à ceci :

        { number: 1, description: 'Critical'}

Nous pouvons maintenant remplacer l'ordre de **priority** dans notre élément par la description texte de l'ordre de priorité. Nous faisons cela avec une jonction relationnelle des deux tables.

        mssql.query('SELECT t.text, t.complete, p.description FROM ToDoItem as t INNER JOIN Priority as p ON t.priority = p.number', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        });

Le script joint les deux tables et écrit les résultats dans le journal. Les objets en résultant peuvent ressembler à ceci :

        { text: 'Take out the trash', complete: false, description: 'Critical'}

#### <a name="raw"></a> d'une requête renvoyant des résultats *bruts*

Cet exemple exécute la requête comme avant, mais renvoie le jeu de résultats au format « brut » qui requiert de l'analyser, ligne par ligne et colonne par colonne. Un exemple de scénario possible serait que vous ayez besoin d'accéder à des types de données non pris en charge par Mobile Services. Ce code écrit simplement la sortie dans le journal de la console pour que vous puissiez inspecter le format brut.

        mssql.queryRaw('SELECT * FROM ToDoItem', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
            }
        });

Voici la sortie apparaissant suite à l'exécution de cette requête. Elle contient des métadonnées sur chaque colonne dans la table, suivies d'une représentation des lignes et des colonnes.

        { meta: 
           [ { name: 'id',
               size: 19,
               nullable: false,
               type: 'number',
               sqlType: 'bigint identity' },
             { name: 'text',
               size: 0,
               nullable: true,
               type: 'text',
               sqlType: 'nvarchar' },
             { name: 'complete',
               size: 1,
               nullable: true,
               type: 'boolean',
               sqlType: 'bit' },
             { name: 'priority',
               size: 53,
               nullable: true,
               type: 'number',
               sqlType: 'float' } ],
          rows: 
           [ [ 1, 'good idea for the future', null, 3 ],
             [ 2, 'this is important but not so much', null, 2 ],
             [ 3, 'fix this bug now', null, 0 ],
             [ 4, 'we need to fix this one real soon now', null, 1 ],
           ] }

#### <a name="connection"></a> de l'accès à une connexion de base de données

Vous pouvez utiliser la méthode **open** pour accéder à la connexion de base de données. Une raison de le faire serait que vous deviez utiliser des transactions de base de données.

La bonne exécution de la méthode **open** entraîne la transmission de la connexion de base de données en paramètre de la fonction **success**. Vous pouvez appeler l'une des fonctions suivantes sur l'objet **connection** : *close*, *queryRaw*, *query*, *beginTransaction*, *commit* et *rollback*.

            mssql.open({
                success: function(connection) {
                    connection.query(//query to execute);
                },
                error: function(err) {
                    console.log("error is: " + err);
                }
            });

## <a name="debugging"></a>Débogage et résolution des problèmes

Écrire dans le journal de service est le moyen principal de déboguer et de résoudre les problèmes des scripts serveur. Par défaut, Mobile Services écrit des erreurs qui se produisent pendant l'exécution de script de service dans les journaux de service. Vos scripts peuvent également écrire dans les journaux. L'écriture dans les journaux est un excellent moyen de déboguer vos scripts et de valider le comportement souhaité.

### <a name="write-to-logs"></a> de la sortie dans les journaux de service mobile

Pour écrire dans les journaux, utilisez l'[objet console][objet console] global. Utilisez la fonction **log** ou **info** pour journaliser les avertissements de niveau des informations. Les fonctions **warning** et **error** journalisent leurs niveaux respectifs qui sont appelés dans les journaux.

<div class="dev-callout"><strong>Remarque</strong>
<p>Pour afficher les journaux de votre service mobile, connectez-vous au <a href="https://manage.windowsazure.com/">portail de gestion</a>, s&eacute;lectionnez votre service mobile, puis choisissez l'onglet <strong>Logs</strong>.</p></div>

Vous pouvez aussi utiliser les fonctions de journalisation de l'[objet console][objet console] pour formater vos messages à l'aide de paramètres. L'exemple suivant fournit un objet JSON en tant que paramètre à la chaîne du message :

    function insert(item, user, request) {
        console.log("Inserting item '%j' for user '%j'.", item, user);  
        request.execute();
    }

Remarquez que la chaîne `%j` est utilisée en tant qu'espace réservé pour un objet JSON et que les paramètres sont fournis dans l'ordre séquentiel.

Pour éviter de surcharger votre journal, il est conseillé de supprimer ou de désactiver les appels à console.log() qui ne sont pas nécessaires pour l'utilisation en production.

  [Introduction]: #intro
  [Opérations de table]: #table-scripts
  [Enregistrement pour les opérations de table]: #register-table-scripts
  [Remplacement de la réponse par défaut]: #override-response
  [Remplacement du succès d'execute]: #override-success
  [Remplacement de la gestion des erreurs par défaut]: #override-error
  [Ajout de paramètres personnalisés]: #access-headers
  [Fonctionnement des utilisateurs de table]: #work-with-users
  [API personnalisée]: #custom-api
  [Définition d'une API personnalisée]: #define-custom-api
  [Implémentation des méthodes HTTP]: #handle-methods
  [Envoi et réception des données au format XML]: #api-return-xml
  [Fonctionnement des utilisateurs et des en-têtes dans une API personnalisée]: #get-api-user
  [Définition de plusieurs itinéraires dans une API personnalisée]: #api-routes
  [Planificateur de travaux]: #scheduler-scripts
  [Contrôle du code source, code partagé et fonctions d'assistance]: #shared-code
  [Chargement des modules Node.js]: #modules-helper-functions
  [Utilisation des fonctions d'assistance]: #helper-functions
  [Partage de code à l'aide du contrôle du code source]: #shared-code-source-control
  [Utilisation des paramètres d'application]: #app-settings
  [Utilisation de l'outil en ligne de commande]: #command-prompt
  [Utilisation des tables]: #working-with-tables
  [Accès aux tables à partir des scripts]: #access-tables
  [Insertion en bloc]: #bulk-inserts
  [Mappage des types JSON vers des types de base de données]: #JSON-types
  [Accès aux tables à l'aide de Transact-SQL]: #TSQL
  [Débogage et résolution des problèmes]: #debugging
  [Écriture de la sortie dans les journaux de service mobile]: #write-to-logs
  [Référence de script serveur Mobile Services]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554226.aspx
  [objet query]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj613353.aspx
  [objet user]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554220.aspx
  [objet request]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554218.aspx
  [Insertion]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554229.aspx
  [Mise à jour]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554214.aspx
  [Suppression]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554215.aspx
  [Lecture]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554224.aspx
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-how-to-use-server-scripts/1-mobile-insert-script-users.png
  [Validation et modification des données dans Mobile Services à l'aide des scripts serveur]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
  [Lire et écrire des données]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj631640.aspx
  [Modifier la requête]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj631635.aspx
  [Valider des données]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj631638.aspx
  [Modifier la réponse]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj631631.aspx
  [Prise en main de l'authentification]: http://go.microsoft.com/fwlink/p/?LinkId=287177
  [2]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn280974.aspx
  [response]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn303373.aspx
  [bibliothèque express.js]: http://go.microsoft.com/fwlink/p/?LinkId=309046
  [3]: ./media/mobile-services-how-to-use-server-scripts/2-mobile-custom-api-script.png
  [Appel d'une API personnalisée à partir du client]: /fr-fr/develop/mobile/tutorials/call-custom-api-dotnet/#define-custom-api
  [Définition d'une API personnalisée prenant en charge les notifications périodiques]: /fr-fr/develop/mobile/tutorials/create-pull-notifications-dotnet/
  [objet express dans express.js]: http://expressjs.com/api.html#express
  [Planifier les travaux]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj860528.aspx
  [4]: ./media/mobile-services-how-to-use-server-scripts/3-mobile-schedule-job-script.png
  [Planification des tâches principales dans Mobile Services]: /fr-fr/develop/mobile/tutorials/schedule-backend-tasks/
  [documentation Node.js]: http://go.microsoft.com/fwlink/p/?LinkId=288802
  [5]: http://go.microsoft.com/fwlink/p/?LinkId=288803
  [6]: http://go.microsoft.com/fwlink/p/?LinkId=288804
  [Envoyer une requête HTTP]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj631641.aspx
  [Envoi de courrier électronique à partir de Mobile Services avec SendGrid]: /fr-fr/develop/mobile/tutorials/send-email-with-sendgrid/
  [7]: http://go.microsoft.com/fwlink/p/?LinkId=288805
  [8]: http://go.microsoft.com/fwlink/p/?LinkId=288806
  [9]: http://go.microsoft.com/fwlink/p/?LinkId=288807
  [Prise en charge de package.json dans Azure Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=391036
  [Utilisation du code partagé et des modules Node.js dans vos scripts serveur]: /fr-fr/develop/mobile/tutorials/store-scripts-in-source-control/#use-npm
  [objet tables]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj614364.aspx
  [Paramètres de l'application]: http://msdn.microsoft.com/fr-fr/library/dn529070.aspx
  [objet service]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn303371.aspx
  [10]: ./media/mobile-services-how-to-use-server-scripts/4-mobile-source-local-cli.png
  [Commandes pour gérer Azure Mobile Services]: /fr-fr/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services/#Mobile_Scripts
  [objet table]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554210.aspx
  [objet mssql]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554212.aspx
  [billet de blog]: http://blogs.msdn.com/b/jpsanders/archive/2013/03/20/server-script-to-insert-table-items-in-windows-azure-mobile-services.aspx
  [JSON.parse]: http://es5.github.io/#x15.12
  [Exécution d'une requête statique]: #static-query
  [Exécution d'une requête dynamique]: #dynamic-query
  [Jonction des tables relationnelles]: #joins
  [Obtention de l'accès à une connexion de base de données]: #connection
  [objet console]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554209.aspx
