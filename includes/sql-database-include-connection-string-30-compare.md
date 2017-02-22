
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Comparer la chaîne de connexion
Le tableau suivant compare la chaîne de connexion dont a besoin votre programme C# pour se connecter à votre serveur SQL Server local à celle utilisée par votre base de données Azure SQL Database dans le cloud. Les différences apparaissent en gras.

| Chaîne de connexion pour<br/>Azure SQL Database | Chaîne de connexion pour<br/>Microsoft SQL Server |
|:--- |:--- |
| Server=**tcp:**{your_serverName_here}**.database.windows.net,1433**;<br/>User ID={your_loginName_here}**@{your_serverName_here}**;<br/>Password={your_password_here};<br/>**Database={your_databaseName_here};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; |Server={your_serverName_here};<br/>User ID={your_loginName_here};<br/>Password={your_password_here}; |

Le paramètre **Database=** est facultatif pour SQL Server, mais obligatoire pour SQL Database.

[Propriétés de SqlConnectionStringBuilder ADO .NET](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) décrit tous les paramètres.

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Jan17_HO3-->


