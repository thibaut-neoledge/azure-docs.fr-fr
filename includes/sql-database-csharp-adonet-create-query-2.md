
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>Exemple de programme C#

Les sections suivantes traiteront d’un programme C# qui utilise ADO.NET pour envoyer des instructions Transact-SQL à la base de données SQL. Le programme C# effectue les opérations suivantes :

1. [Connexion à la base de données SQL à l’aide de ADO.NET](#cs_1_connect).
2. [Création de tableaux](#cs_2_createtables).
3. [Entrée de données dans les tableaux, par émission d’instructions T-SQL INSERT](#cs_3_insert).
4. [Mise à jour des données à l’aide d’une jointure](#cs_4_updatejoin).
5. [Suppression de données à l’aide d’une jointure](#cs_5_deletejoin).
6. [Sélection de lignes de données à l’aide d’une jointure](#cs_6_selectrows).
7. Arrêt de la connexion (ce qui supprime tout tableau temporaire présent dans tempdb).

Le programme C# contient :

- Un code C# pour se connecter à la base de données.
- Méthodes qui renvoient le code source T-SQL.
- Deux méthodes qui envoient le code T-SQL à la base de données.

#### <a name="to-compile-and-run"></a>Compilation et exécution

Ce programme C# est, en toute logique, un fichier .cs. Dans ce cas, le programme est physiquement divisé en plusieurs blocs de code, ce qui rend chaque bloc plus facile à repérer et à interpréter. Pour compiler et exécuter ce programme, procédez comme suit :

1. Créez un projet C# dans Visual Studio.
    - Le type de projet doit être une application *console*, disposant de la hiérarchie suivante : **Modèles** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework)**.
3. Dans le fichier **Program.cs**, supprimez les lignes de codes de démarrage.
3. Dans le fichier Program.cs, copiez, puis collez chacun des blocs suivants, dans le même ordre.
4. Dans Program.cs, modifiez les valeurs suivantes dans la méthode **principale** :

   - **cb.DataSource**
   - **cd.UserID**
   - **cb.Password**
   - **InitialCatalog**

5. Vérifiez que l’assembly **System.Data.dll** est référencé. Pour vérifier, développez le nœud **Références** dans le volet **Explorateur de solutions**.
6. Pour créer le programme dans Visual Studio, cliquez sur **Créer**.
7. Pour exécuter le programme à partir de Visual Studio, cliquez sur **Démarrer**. L’emplacement du rapport s’affiche dans une fenêtre cmd.exe.

> [!NOTE]
> Vous pouvez modifier le code T-SQL et ajouter un préfixe **#** aux noms des tables, ce qui les enregistre comme tables temporaires dans **tempdb**. Cela peut être utile lors de démonstrations, si aucune base de données test n’est disponible. Les tables temporaires sont supprimées automatiquement à la déconnexion. Toutes les références (REFERENCES) de clés étrangères ne sont pas appliquées pour les tables temporaires.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>Bloc 1 C# : Connexion via ADO.NET

- [Next](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>Bloc 2 C# : Création de tables via T-SQL

- [Précédent](#cs_1_connect)&nbsp; / &nbsp;[Suivant](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>Diagramme des relations d’entité (ERD)

Les instructions CREATE TABLE affichent le mot clé **REFERENCES** pour créer une relation de *clé étrangère* (FK) entre deux tables.  Si vous utilisez tempdb, vous pouvez laisser un commentaire sur le mot clé `--REFERENCES` en le faisant précéder de deux tirets.

Ensuite, un diagramme affiche la relation entre les deux tables. Les valeurs présentes dans la colonne *enfant* #tabEmployee.DepartmentCode sont limitées par celles de la colonne *parent* #tabDepartment.Department.

![Diagramme affichant la clé étrangère](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>Bloc 3 C# : Intégration de données via T-SQL

- [Précédent](#cs_2_createtables)&nbsp; / &nbsp;[Suivant](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>Bloc 4 C# : Mise à jour de la jointure via T-SQL

- [Précédent](#cs_3_insert)&nbsp; / &nbsp;[Suivant](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>Bloc 5 C# : Suppression de la jointure via T-SQL

- [Précédent](#cs_4_updatejoin)&nbsp; / &nbsp;[Suivant](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>Bloc 6 C# : Sélection de lignes via T-SQL

- [Précédent](#cs_5_deletejoin)&nbsp; / &nbsp;[Suivant](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>Bloc 6b C# : ExecuteReader

- [Précédent](#cs_6_selectrows)&nbsp; / &nbsp;[Suivant](#cs_7_executenonquery)

Cette méthode est conçue pour exécuter l’instruction T-SQL SELECT, générée par la méthode **Build_6_Tsql_SelectEmployees**.


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>Bloc 7 C# : ExecuteNonQuery

- [Précédent](#cs_6b_datareader)&nbsp; / &nbsp;[Suivant](#cs_8_output)

Cette méthode est employée pour les opérations qui modifient le contenu des données des tableaux sans renvoyer de lignes de données.


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>Bloc 8 C# : Résultats effectifs du test de la console

- [Précédent](#cs_7_executenonquery)

Cette section capture les résultats transmis à la console. Seules les valeurs GUID varient entre les séries de tests.


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
