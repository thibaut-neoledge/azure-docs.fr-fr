---
title: "Copier des données vers/depuis SQL Server à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Apprendre à déplacer des données vers/depuis une base de données SQL Server locale ou une machine virtuelle Azure à l’aide d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2017
ms.author: jingwang
ms.openlocfilehash: f4df68a5cb240e9483cc088c620716bba207439b
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Copier des données vers et depuis SQL Server à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - GA](v1/data-factory-sqlserver-connector.md)
> * [Version 2 - Préversion](connector-sql-server.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers et depuis une base de données SQL Server. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, qui est actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale (GA), consultez [Connecteur SQL Server dans V1](v1/data-factory-sqlserver-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données à partir de toute base de données SQL Server vers toute banque de données réceptrice prise en charge, ou copier des données à partir de toute banque de données source prise en charge vers une base de données SQL Server. Pour obtenir la liste des magasins de données pris en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SQL Server prend en charge :

- SQL Server version 2016, 2014, 2012, 2008 R2, 2008 et 2005
- La copie des données à l’aide de l’authentification **SQL** ou **Windows**
- En tant que source, la récupération de données à l’aide d’une procédure stockée ou d’une requête SQL
- En tant que récepteur, l’ajout de données à une table de destination ou l’appel d’une procédure stockée avec une logique personnalisée pendant la copie.

## <a name="prerequisites"></a>Composants requis

Pour copier des données à partir d’une base de données SQL Server qui n’est pas accessible publiquement, vous devez configurer un Runtime d’intégration autohébergé. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md). Le Runtime d’intégration fournit un pilote de base de données SQL Server intégré. Ainsi, vous n’avez pas besoin d’installer manuellement un pilote lors de la copie des données vers/depuis une base de données SQL Server.

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie.

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur de base de données SQL Server.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié SQL Server :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **SqlServer** | Oui |
| connectionString |Spécifiez les informations connectionString nécessaires pour établir une connexion à la base de données SQL Server à l’aide de l’authentification SQL ou de l’authentification Windows. Marquez ce champ comme SecureString. |Oui |
| userName |Spécifiez le nom d’utilisateur si vous utilisez l’authentification Windows. Exemple : **domainname\\username**. |Non |
| password |Spécifiez le mot de passe du compte d’utilisateur que vous avez défini pour le nom d’utilisateur. Marquez ce champ comme SecureString. |Non |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser un runtime d’intégration auto-hébergé ou un runtime d’intégration Azure (si votre banque de données est accessible publiquement). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

**Exemple 1 : utilisation de l’authentification SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : utilisation de l’authentification Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
             "userName": "<domain\\username>",
             "password": {
                "type": "SecureString",
                "value": "<password>"
             }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit une liste des propriétés prises en charge par le jeu de données SQL Server.

Pour copier des données vers/depuis une base de données SQL Server, affectez la valeur **SqlServerTable** à la propriété de type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type du jeu de données doit être définie sur **SqlServerTable** | Oui |
| TableName |Nom de la table ou de la vue dans l’instance de base de données SQL Server à laquelle le service lié fait référence. | Oui |

**Exemple :**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste de propriétés prises en charge par la source et le récepteur SQL Server.

### <a name="sql-server-as-source"></a>SQL Server en tant que source

Pour copier des données à partir de SQL Server, définissez **SqlSource** comme type source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur **SqlSource** | Oui |
| SqlReaderQuery |Utiliser la requête SQL personnalisée pour lire les données. Exemple : `select * from MyTable`. |Non |
| sqlReaderStoredProcedureName |Nom de la procédure stockée qui lit les données de la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. |Non |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Valeurs autorisées : paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non |

**Points à noter :**

- Si **sqlReaderQuery** est spécifié comme SqlSource, l’activité de copie exécute cette requête sur la source SQL Server pour obtenir les données. Vous pouvez également spécifier une procédure stockée en indiquant **sqlReaderStoredProcedureName** et **storedProcedureParameters** (si la procédure stockée accepte des paramètres).
- Si vous ne spécifiez pas « sqlReaderQuery » ou « sqlReaderStoredProcedureName », les colonnes définies dans la section « Structure » du jeu de données JSON sont utilisées pour créer une requête (`select column1, column2 from mytable`) à exécuter sur SQL Server. Si la définition du jeu de données n’a pas de « structure », toutes les colonnes de la table sont sélectionnées.
- Quand vous utilisez **sqlReaderStoredProcedureName**, vous devez tout de même spécifier une propriété **tableName** factice dans le jeu de données JSON.

**Exemple : utilisation d’une requête SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemple : utilisation d’une procédure stockée**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Définition de la procédure stockée :**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-sink"></a>SQL Server en tant que récepteur

Pour copier des données vers SQL Server, définissez **SqlSink** comme type de récepteur dans l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur **SqlSink** | Oui |
| writeBatchSize |Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize<br/>Valeurs autorisées : integer (nombre de lignes). |Non (valeur par défaut : 10000) |
| writeBatchTimeout |Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br/>Valeurs autorisées : timespan. Exemple : « 00:30:00 » (30 minutes). |Non |
| sqlWriterStoredProcedureName |Nom de la procédure stockée qui met à jour/insère les données dans la table cible. |Non |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Valeurs autorisées : paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non |
| sqlWriterTableType |Spécifiez le nom du type de table à utiliser dans la procédure stockée. L’activité de copie place les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. |Non |
| preCopyScript |Spécifiez une requête SQL que l’activité de copie doit exécuter avant l’écriture des données dans SQL Server lors de chaque exécution. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. |Non |

> [!TIP]
> Lors de la copie de données vers SQL Server, l’activité de copie ajoute les données à la table réceptrice par défaut. Pour effectuer un UPSERT ou une logique métier supplémentaire, utilisez la procédure stockée dans SqlSink. Pour en savoir plus, consultez [Appel d’une procédure stockée pour un récepteur SQL](#invoking-stored-procedure-for-sql-sink).

**Exemple 1 : ajout de données**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemple 2 : appel d’une procédure stockée pendant la copie pour upsert**

Pour en savoir plus, consultez [Appel d’une procédure stockée pour un récepteur SQL](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Colonnes d’identité dans la base de données cible

Cette section fournit un exemple qui copie des données d’une table source sans colonne d’identité vers une table de destination avec une colonne d’identité.

**Table source :**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Table de destination :**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Notez que la table cible possède une colonne d’identité.

**Définition du jeu de données JSON source**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Définition de jeu de données JSON de destination**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Notez que vos tables source et cible ont des schémas différents (la cible possède une colonne supplémentaire avec identité). Dans ce scénario, vous devez spécifier la propriété **structure** dans la définition du jeu de données cible, qui n’inclut pas la colonne d’identité.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Appel d’une procédure stockée pour un récepteur SQL

Quand vous copiez des données dans une base de données SQL Server, une procédure stockée spécifiée par l’utilisateur peut être configurée et appelée avec des paramètres supplémentaires.

Une procédure stockée peut être utilisée à la place des mécanismes de copie intégrée. C’est généralement le cas quand une opération upsert (insertion + mise à jour) ou un traitement supplémentaire (fusion de colonnes, recherche de valeurs supplémentaires, insertion dans plusieurs tables, etc.) doit être effectué avant l’insertion finale des données sources dans la table de destination.

L’exemple suivant montre comment utiliser une procédure stockée pour effectuer une opération upsert simple dans une table de la base de données SQL Server. On part du principe que les données d’entrée et la table réceptrice « Marketing » ont trois colonnes : ProfileID, State et Category. On effectue une opération upsert basée sur la colonne « ProfileID » et on l’applique uniquement à une catégorie spécifique.

**Jeu de données de sortie**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Définissez la section SqlSink dans l’activité de copie comme suit.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Dans votre base de données, définissez la procédure stockée portant le même nom que SqlWriterStoredProcedureName. Elle gère les données d’entrée à partir de la source que vous avez spécifiée et les fusionne dans la table de sortie. Notez que le nom du paramètre de la procédure stockée doit être le même que le « tableName » défini dans le jeu de données.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

Dans votre base de données, définissez le type de table portant le même nom que SqlWriterTableType. Notez que le schéma du type de table doit être identique au schéma retourné par vos données d'entrée.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

La fonction de procédure stockée tire parti des [paramètres Table-Valued](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Mappage de type de données pour SQL Server

Lors de la copie des données vers/à partir de SQL Server, les mappages suivants sont utilisés entre les types de données SQL Server et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma source et le type de données au récepteur, consultez [Mappages de types de données et de schémas](copy-activity-schema-and-type-mapping.md).

| Type de données SQL Server | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binaire |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Décimal |Décimal |
| Attribut FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Décimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numérique |Décimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Décimal |
| sql_variant |Objet * |
| texte |String, Char[] |
| time |intervalle de temps |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="troubleshooting-connection-issues"></a>Résolution des problèmes de connexion

1. Configurez votre serveur SQL Server pour qu’il accepte les connexions à distance. Démarrez **SQL Server Management Studio**, cliquez avec le bouton droit de la souris sur **Serveur**, puis cliquez sur **Propriétés**. Sélectionnez **Connexions** dans la liste, puis cochez **Autoriser les accès distants à ce serveur**.

    ![Activation des connexions à distance](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Vous trouverez la procédure détaillée à la page [Configurer l’option de configuration du serveur remote access](https://msdn.microsoft.com/library/ms191464.aspx) .

2. Lancez le **Gestionnaire de configuration SQL Server**. Développez **Configuration du réseau SQL Server** pour l’instance souhaitée, puis sélectionnez **Protocoles pour MSSQLSERVER**. Les protocoles doivent s’afficher dans le volet droit. Activez TCP/IP en cliquant avec le bouton droit sur **TCP/IP** et en cliquant sur **Activer**.

    ![Activation de TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Consultez la page [Activer ou désactiver un protocole réseau de serveur](https://msdn.microsoft.com/library/ms191294.aspx) pour obtenir des détails et découvrir d’autres façons d’activer le protocole TCP/IP.

3. Dans la même fenêtre, double-cliquez sur **TCP/IP** pour lancer la fenêtre des **propriétés de TCP/IP**.
4. Allez sous l’onglet **Adresses IP** . Faites défiler l’écran vers le bas jusqu’à la section **IPAll** . Notez le **Port TCP** (le port par défaut est le **1433**).
5. Créez une **règle de Pare-feu Windows** sur l’ordinateur pour autoriser le trafic à entrer par ce port.  
6. **Vérifiez la connexion**: servez-vous de SQL Server Management Studio sur un autre ordinateur pour vous connecter à SQL Server en utilisant un nom qualifié complet. Par exemple : `"<machine>.<domain>.corp.<company>.com,1433"`.


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
