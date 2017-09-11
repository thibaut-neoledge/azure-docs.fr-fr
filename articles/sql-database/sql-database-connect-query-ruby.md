---
title: "Utilisation de Ruby pour interroger Azure SQL Database | Microsoft Docs"
description: "Cette rubrique vous explique comment utiliser Ruby pour créer un programme qui se connecte à une base de données SQL Azure et l’interroger à l’aide d’instructions Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 07/14/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 25ff9a9cfaa5494dbb006c84e235099fe51e6545
ms.contentlocale: fr-fr
ms.lasthandoff: 07/17/2017

---

# <a name="use-ruby-to-query-an-azure-sql-database"></a>Utilisation de Ruby pour interroger une base de données SQL Azure

Ce didacticiel de démarrage rapide indique comment utiliser [Ruby](https://www.ruby-lang.org) pour créer un programme qui se connecte à une base de données SQL Azure et utiliser des instructions Transact-SQL pour interroger des données.

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel de démarrage rapide, vérifiez que vous disposez des éléments prérequis suivants :

- base de données SQL Azure. Ce guide de démarrage rapide utilise les ressources créées dans l’une de ces instructions de démarrage rapide : 

   - [Créer une base de données - Portail](sql-database-get-started-portal.md)
   - [Créer une base de données - CLI](sql-database-get-started-cli.md)
   - [Créer une base de données - PowerShell](sql-database-get-started-powershell.md)

- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) pour l’adresse IP publique de l’ordinateur que vous utilisez pour ce didacticiel de démarrage rapide.
- Vous avez installé Ruby et les logiciels connexes pour votre système d’exploitation.
    - **MacOS** : installez Homebrew installez rbenv et ruby-build, installez Ruby, puis installez FreeTDS. Consultez les [étapes 1.2, 1.3, 1.4 et 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
    - **Ubuntu** : installez les éléments requis pour Ruby, installez rbenv et ruby-build, installez Ruby, puis installez FreeTDS. Consultez les [étapes 1.2, 1.3, 1.4 et 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

Obtenez les informations de connexion requises pour la connexion à la base de données SQL Azure. Vous aurez besoin du nom du serveur complet, du nom de la base de données et des informations de connexion dans les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Sur la page **Aperçu** de votre base de données, vérifiez le nom complet du serveur. Vous pouvez pointer sur le nom du serveur pour afficher l’option **Cliquez pour copier**, comme illustré sur l’image suivante :

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si vous avez oublié vos informations de connexion à votre serveur Azure SQL Database, accédez à la page du serveur SQL Database pour afficher le nom de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe.

> [!IMPORTANT]
> Une règle de pare-feu doit être en place pour l’adresse IP publique de l’ordinateur sur lequel vous effectuez ce didacticiel. Si vous êtes sur un autre ordinateur ou si vous avez une autre adresse IP publique, créez une [règle de pare-feu au niveau du serveur à l’aide du portail Azure](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="insert-code-to-query-sql-database"></a>Insertion du code pour interroger la base de données SQL

1. Dans votre éditeur de texte favori, créez un nouveau fichier nommé **sqltest.rb**

2. Remplacez le contenu par le code suivant et ajoutez les valeurs appropriées pour votre serveur, base de données, utilisateur et mot de passe.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="run-the-code"></a>Exécution du code

1. Exécutez ensuite les commandes suivantes dans l’invite de commandes :

   ```bash
   ruby sqltest.rb
   ```

2. Vérifiez que les 20 premières lignes sont renvoyées, puis fermez la fenêtre d’application.


## <a name="next-steps"></a>Étapes suivantes
- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [GitHub repository for TinyTDS](https://github.com/rails-sqlserver/tiny_tds) (Référentiel GitHub pour TinyTDS)
- [Signaler des problèmes ou poser des questions sur TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby Driver for SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/) (Pilote Ruby pour SQL Server)

