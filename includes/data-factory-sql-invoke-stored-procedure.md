## <a name="invoking-stored-procedure-for-sql-sink"></a>Appel d’une procédure stockée pour un récepteur SQL
Quand vous copiez des données dans SQL Server ou dans une base de données SQL Azure ou SQL Server, une procédure stockée spécifiée par l’utilisateur peut être configurée et appelée avec des paramètres supplémentaires. 

Une procédure stockée peut être utilisée à la place des mécanismes de copie intégrée. C’est généralement le cas quand un traitement supplémentaire (fusion de colonnes, recherche de valeurs supplémentaires, insertion dans plusieurs tables, etc.) doit être effectué avant l’insertion finale des données sources vers la table de destination. 

Vous pouvez appeler la procédure stockée de votre choix. L’exemple suivant montre comment utiliser une procédure stockée pour effectuer une insertion simple dans une table de la base de données. 

**Jeu de données de sortie**

Dans cet exemple, le type est défini sur SqlServerTable. Affectez-lui la valeur AzureSqlTable pour l’utiliser avec une base de données SQL Azure. 

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

Définissez la section SqlSink dans l’activité de copie JSON comme suit. Pour appeler une procédure stockée lors de l’insertion des données, les deux propriétés SqlWriterStoredProcedureName et SqlWriterTableType sont requises.

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

Dans votre base de données, définissez la procédure stockée portant le même nom que SqlWriterStoredProcedureName. Elle gère les données d'entrée à partir de la source que vous avez spécifiée et les insère dans la table de sortie. Notez que le nom du paramètre de la procédure stockée doit être le même que le tableName défini dans le fichier de Table JSON.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```
Dans votre base de données, définissez le type de table portant le même nom que SqlWriterTableType. Notez que le schéma du type de table doit être identique au schéma retourné par vos données d'entrée.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```
La fonction de procédure stockée tire parti des [paramètres Table-Valued](https://msdn.microsoft.com/library/bb675163.aspx).

