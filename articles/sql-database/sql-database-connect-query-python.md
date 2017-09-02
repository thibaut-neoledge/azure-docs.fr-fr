---
title: "Utilisation de Python pour interroger Azure SQL Database | Microsoft Docs"
description: "Cette rubrique vous explique comment utiliser Python pour créer un programme qui se connecte à une base de données SQL Azure et l’interroger à l’aide d’instructions Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: afffcb9a4938bf97626f182bb4f4d099d807d411
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---
# <a name="use-python-to-query-an-azure-sql-database"></a>Utilisation de Python pour interroger une base de données SQL Azure

 Ce didacticiel de démarrage rapide indique comment utiliser [Python](https://python.org) pour se connecter à une base de données SQL Azure et utiliser des instructions Transact-SQL pour interroger des données.

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel de démarrage rapide, vérifiez que vous disposez des éléments suivants :

- base de données SQL Azure. Ce guide de démarrage rapide utilise les ressources créées dans l’une de ces instructions de démarrage rapide : 

   - [Créer une base de données - Portail](sql-database-get-started-portal.md)
   - [Créer une base de données - CLI](sql-database-get-started-cli.md)
   - [Créer une base de données - PowerShell](sql-database-get-started-powershell.md)

- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) pour l’adresse IP publique de l’ordinateur que vous utilisez pour ce didacticiel de démarrage rapide.

- Vous avez installé Python et les logiciels connexes pour votre système d’exploitation.

    - **MacOS** : installez Homebrew et Python, installez le pilote ODBC et SQLCMD, puis installez le pilote Python pour SQL Server. Consultez les [étapes 1.2, 1.3 et 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).
    - **Ubuntu** : installez Python et les autres packages requis, puis installez le pilote Python pour SQL Server. Consultez les [étapes 1.2, 1.3 et 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/).
    - **Windows** : installez la version la plus récente de Python (la variable d’environnement est désormais configurée pour vous), installez le pilote ODBC et SQLCMD, puis installez le pilote Python pour SQL Server. Consultez les [étapes 1.2, 1.3 et 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/windows/). 

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

Obtenez les informations de connexion requises pour la connexion à la base de données SQL Azure. Vous aurez besoin du nom du serveur complet, du nom de la base de données et des informations de connexion dans les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Sur la page **Vue d’ensemble** de votre base de données, vérifiez le nom complet du serveur, comme indiqué dans l’image suivante. Vous pouvez pointer sur le nom du serveur pour afficher l’option **Cliquez pour copier**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si vous avez oublié vos informations de connexion au serveur, accédez à la page du serveur SQL Database pour afficher le nom de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe.     
    
## <a name="insert-code-to-query-sql-database"></a>Insertion du code pour interroger la base de données SQL 

1. Dans votre éditeur de texte favori, créez un nouveau fichier nommé **sqltest.py**.  

2. Remplacez le contenu par le code suivant et ajoutez les valeurs appropriées pour votre serveur, base de données, utilisateur et mot de passe.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>Exécution du code

1. Exécutez ensuite les commandes suivantes dans l’invite de commandes :

   ```Python
   python sqltest.py
   ```

2. Vérifiez que les 20 premières lignes sont renvoyées, puis fermez la fenêtre d’application.

## <a name="next-steps"></a>Étapes suivantes

- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [Python SQL Driver](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/) (Pilote SQL Python)
- [Centre de développement Python](https://azure.microsoft.com/develop/python/?v=17.23h)


