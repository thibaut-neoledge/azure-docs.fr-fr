---
title: "Didacticiel : application web avec une base de données mutualisée à l’aide d‘Entity Framework et de la sécurité de niveau de ligne"
description: "Découvrez comment développer une application web d’ASP.NET MVC 5 avec une architecture mutualisée, base de données SQL principale, à l‘aide d’Entity Framework et une sécurité de niveau de ligne."
metakeywords: azure asp.net mvc entity framework multi tenant row level security rls sql database
services: app-service\web
documentationcenter: .net
manager: jeffreyg
author: tmullaney
ms.assetid: 8fdc47a5-6fc3-4d29-ab6a-33e79f50699f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/25/2016
ms.author: thmullan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ba1bb3d84b462dfebbb2564569517d7336bf54fd
ms.lasthandoff: 04/10/2017


---
# <a name="tutorial-web-app-with-a-multi-tenant-database-using-entity-framework-and-row-level-security"></a>Didacticiel : application web avec une base de données mutualisée à l’aide d‘Entity Framework et de la sécurité de niveau de ligne
Ce didacticiel montre comment créer une application web multi-locataire avec un modèle de location « [base de données partagée, schéma partagé](https://msdn.microsoft.com/library/aa479086.aspx) », à l’aide d’Entity Framework et de la [sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131.aspx). Dans ce modèle, une base de données simple contient les données de nombreux clients, et chaque ligne de chaque table est associée à un « ID client » La sécurité au niveau des lignes (RLS), nouvelle fonctionnalité de base de données SQL Azure est utilisée pour empêcher les autres clients d‘accéder aux données les uns des autres. Cela nécessite juste une modification mineure de l‘application. En centralisant la logique d‘accès client au sein de la base de données, RLS simplifie le code d‘application et réduit le risque de fuites accidentelles d’un utilisateur à l’autre.

Commençons par l‘application Gestionnaire de contact avec la section [Créer une application ASP.NET MVP avec l‘authentification et la base de données SQL et déployer le service d’application Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Actuellement, l’application permet à tous les utilisateurs (clients) d’afficher tous les contacts :

![Application du Gestionnaire de contacts avant d‘activer la RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Avec simplement quelques modifications mineures, nous allons ajouter la prise en charge de l’architecture mutualisée, afin que les utilisateurs puissent voir uniquement les contacts qui leur appartiennent.

## <a name="step-1-add-an-interceptor-class-in-the-application-to-set-the-sessioncontext"></a>Étape 1 : Ajouter une classe d’intercepteur dans l’application pour définir la SESSION_CONTEXT
Il faut apporter une modification à l’application. Tous les utilisateurs d‘applications se connectent à la base de données à l‘aide de la chaîne de connexion (c‘est-à-dire même connexion SQL), il n‘existe actuellement aucun moyen pour une stratégie RLS de savoir pour quel utilisateur il doit effectuer le filtrage. Cette approche est très courante dans les applications web, car elle permet un regroupement de connexions efficace. Cependant, cela signifie que nous avons besoin d‘une autre façon d’identifier l’utilisateur actuel de l’application dans la base de données.  La solution consiste à utiliser l‘application pour définir une paire clé-valeur pour le UserId actuel dans le [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806) immédiatement après l’ouverture d’une connexion et avant l’exécution d’une requête. SESSION_CONTEXT est un magasin clé-valeur session, et notre stratégie RLS utilise le nom d’utilisateur qu’il contient pour identifier l’utilisateur actuel.

Nous allons ajouter un [intercepteur](https://msdn.microsoft.com/data/dn469464.aspx) (plus particulièrement, un intercepteur [DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor)), nouvelle fonctionnalité dans Entity Framework (EF) 6, pour définir automatiquement le UserId actuel dans SESSION_CONTEXT en exécutant une instruction T-SQL à chaque fois qu’Entity Framework ouvre une connexion.

1. Ouvrez le projet ContactManager dans Visual Studio.
2. Cliquez avec le bouton droit sur le dossier Modèles dans l’Explorateur de solutions, puis sélectionnez Ajouter > Classe.
3. Nommez la nouvelle classe « SessionContextInterceptor.cs », puis cliquez sur Ajouter.
4. Remplacez le contenu de SessionContextInterceptor.cs par le code suivant :

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
            // Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }

        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

Il s’agit de la seule modification d’application requise. Continuez, réalisez et publiez l’application.

## <a name="step-2-add-a-userid-column-to-the-database-schema"></a>Étape 2 : Ajouter une colonne UserId au schéma de base de données
Ensuite, nous devons ajouter une colonne UserId à la table Contacts à associer à chaque ligne d’un utilisateur (client). Il modifie le schéma directement dans la base de données, et nous n’avons donc pas à inclure ce champ dans notre modèle de données Entity Framework.

Connectez-vous à la base de données directement en utilisant SQL Server Management Studio ou Visual Studio, puis exécutez l’instruction T-SQL suivante :

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Cela ajoute une colonne UserId à la table Contacts. Nous utilisons le type de données nvarchar (128) pour faire correspondre les ID utilisateur stockés dans la table AspNetUsers, puis nous créons une contrainte DEFAULT qui définira automatiquement le UserId des lignes nouvellement insérées au UserId actuellement stocké dans SESSION_CONTEXT.

Maintenant, le tableau ressemble à ce qui suit :

![Table de contacts SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Lors de la création de nouveaux contacts, ces derniers se verront affectés un ID d‘utilisateur correct. À des fins de démonstration, toutefois, nous allons affecter quelques-uns de ces contacts existants à un utilisateur existant.

Si vous avez déjà créé quelques utilisateurs dans l‘application (par exemple, à l‘aide de comptes locaux Google ou Facebook), vous pouvez les voir dans la table AspNetUsers. Dans la capture d‘écran ci-dessous, il n’y a jusqu‘ici qu‘un seul utilisateur.

![Tableau AspNetUsers SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Copiez l’ID de user1@contoso.com et collez-le dans l’instruction T-SQL ci-dessous. Exécutez cette instruction pour associer trois des Contacts à cet ID utilisateur.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## <a name="step-3-create-a-row-level-security-policy-in-the-database"></a>Étape 3 : créer une stratégie de sécurité de niveau de ligne dans la base de données
L’étape finale consiste à créer une stratégie de sécurité qui utilise le nom d’utilisateur dans SESSION_CONTEXT pour filtrer automatiquement les résultats renvoyés par les requêtes.

Bien que toujours connecté à la base de données, exécutez le T-SQL suivant :

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

Ce code exécute trois opérations. Tout d‘abord, il crée un nouveau schéma en tant que meilleure pratique pour la centralisation et la limitation d’accès aux objets RLS. Ensuite, il crée une fonction de prédicat qui renverra « 1 » lorsque le UserId d’une ligne correspond au UserId dans SESSION_CONTEXT. Enfin, il crée une stratégie de sécurité qui ajoute cette fonction en tant que prédicat de bloc et de filtre sur la table Contacts. Le prédicat de filtre fait en sorte que les requêtes retournent uniquement des lignes appartenant à l’utilisateur actuel et le prédicat de bloc agit comme un dispositif de protection pour empêcher l‘application d‘insérer une ligne pour le mauvais utilisateur.

Maintenant, exécutez l’application puis connectez-vous en tant que user1@contoso.com. Cet utilisateur voit à présent que les Contacts que nous avons attribués à ce UserId:

![Application du Gestionnaire de contacts avant d‘activer la RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Pour valider cette opération, essayez d‘enregistrer un nouvel utilisateur. Ils ne verront aucun contact, car aucun ne leur a été affecté. S’ils créent un nouveau contact, il leur sera affecté et ils seront les seuls à pouvoir le visualiser.

## <a name="next-steps"></a>Étapes suivantes
Et voilà ! L’application web Gestionnaire de contact a été transformée en application mutualisée où chaque utilisateur a sa propre liste de contacts. En utilisant la sécurité de niveau ligne, nous avons évité la complexité liée à l’application de la logique d‘accès aux clients dans notre code d’application. Cette transparence permet à l‘application de se concentrer sur les véritables problèmes de l’entreprise et elle réduit également le risque de fuite accidentelle des données au fur et à mesure que le code de base de l‘application augmente.

Ce didacticiel a seulement effleuré la surface de ce qui est possible avec RLS. Par exemple, il est possible d‘avoir des logiques d’accès plus sophistiquées ou granulaires, et il est possible de stocker davantage que le simple UserId dans SESSION_CONTEXT. Il est également possible d’ [intégrer RLS avec les bibliothèques client des outils de base de données élastique](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) pour prendre en charge des partitions mutualisées dans une couche de données mise à l’échelle.

Au-delà de ces possibilités, nous nous efforçons d’améliorer encore RLS. Si vous avez des questions, des idées ou des choses que vous aimeriez voir, faites-le nous savoir par le biais de vos commentaires. Nous apprécions vos commentaires !


