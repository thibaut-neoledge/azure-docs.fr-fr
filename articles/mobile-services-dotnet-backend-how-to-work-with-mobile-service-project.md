<properties pageTitle="Work with a .NET backend mobile service" metaKeywords="Web API, mobile service, Azure, controllers" description="Provides examples on how to define, register, and use server-side components in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Utilisation d'un service mobile principal .NET

Cet article fournit des informations détaillées, accompagnées d'exemples, sur l'utilisation d'un projet principal .NET Visual Studio qui définit votre service mobile dans Azure Mobile Services. Cette rubrique se compose des sections suivantes :

-   [Introduction][Introduction]
-   [Opérations de table][Opérations de table]
    -   [Enregistrement pour les opérations de table][Enregistrement pour les opérations de table]
    -   [Remplacement de la réponse par défaut][Remplacement de la réponse par défaut]
    -   [Remplacement de la réussite d'execute][Remplacement de la réussite d'execute]
    -   [Remplacement de la gestion des erreurs par défaut][Remplacement de la gestion des erreurs par défaut]
    -   [Ajout de paramètres personnalisés][Ajout de paramètres personnalisés]
    -   [Utilisation des utilisateurs de table][Utilisation des utilisateurs de table]
-   [API personnalisée][API personnalisée]
    -   [Définition d'une API personnalisée][Définition d'une API personnalisée]
    -   [Implémentation des méthodes HTTP][Implémentation des méthodes HTTP]
    -   [Envoi et réception des données au format XML][Envoi et réception des données au format XML]
    -   [Utilisation des utilisateurs et des en-têtes dans une API personnalisée][Utilisation des utilisateurs et des en-têtes dans une API personnalisée]
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
    -   [Exécution d'insertions en bloc][Exécution d'insertions en bloc]
    -   [Mappage des types JSON vers des types de base de données][Mappage des types JSON vers des types de base de données]
    -   [Accès aux tables à l'aide de Transact-SQL][Accès aux tables à l'aide de Transact-SQL]
-   [Débogage et résolution des problèmes][Débogage et résolution des problèmes]
    -   [Écriture de la sortie dans les journaux de service mobile][Écriture de la sortie dans les journaux de service mobile]

## <a name="intro"></a>Introduction

Dans un service mobile principal .NET, vous pouvez définir une logique métier personnalisée en tant que code JavaScript stocké et exécuté sur le serveur. Ce code de script serveur est attribué à l'une des fonctions de serveur suivantes :

-   [Opérations d'insertion, de lecture, de mise à jour ou de suppression sur une table donnée][Opérations de table].
-   [Travaux planifiés][Planificateur de travaux].
-   [Méthodes HTTP définies dans une API personnalisée][API personnalisée].

La signature de la fonction principale dans le script serveur dépend du contexte de l'emplacement d'utilisation du script. Vous pouvez également définir un code de script commun comme les modules nodes.js qui sont partagés entre des scripts. Pour plus d'informations, consultez la section [Contrôle du code source et code partagé][Contrôle du code source, code partagé et fonctions d'assistance].

Pour obtenir une description des objets et des fonctions des scripts serveur individuels, consultez la page [Référence de script serveur Mobile Services].

<!-- Anchors. -->

  [Introduction]: #intro
  [Opérations de table]: #table-scripts
  [Enregistrement pour les opérations de table]: #register-table-scripts
  [Remplacement de la réponse par défaut]: #override-response
  [Remplacement de la réussite d'execute]: #override-success
  [Remplacement de la gestion des erreurs par défaut]: #override-error
  [Ajout de paramètres personnalisés]: #access-headers
  [Utilisation des utilisateurs de table]: #work-with-users
  [API personnalisée]: #custom-api
  [Définition d'une API personnalisée]: #define-custom-api
  [Implémentation des méthodes HTTP]: #handle-methods
  [Envoi et réception des données au format XML]: #api-return-xml
  [Utilisation des utilisateurs et des en-têtes dans une API personnalisée]: #get-api-user
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
  [Exécution d'insertions en bloc]: #bulk-inserts
  [Mappage des types JSON vers des types de base de données]: #JSON-types
  [Accès aux tables à l'aide de Transact-SQL]: #TSQL
  [Débogage et résolution des problèmes]: #debugging
  [Écriture de la sortie dans les journaux de service mobile]: #write-to-logs
