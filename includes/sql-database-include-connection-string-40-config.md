
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### <a name="example-config-file-for-connection-string-security"></a>Exemple de fichier de configuration pour la sécurité des chaînes de connexion
Il est incorrect de placer la chaîne de connexion en tant que littéraux dans votre code C#. Il est préférable de placer la chaîne de connexion dans un fichier de configuration. Vous pouvez y modifier la chaîne à tout moment sans avoir à recompiler.

Supposons que votre programme C# compilé se nomme **ConsoleApplication1.exe**, et que ce fichier .exe se trouve dans un répertoire **bin\debug\**.

Dans cet exemple, la majeure partie de votre chaîne de connexion est stockée dans un fichier de configuration nommé précisément **ConsoleApplication1.exe.config**. Ce fichier de configuration doit également se trouver dans ***bin\debug\**.

Dans le code XML du fichier de configuration suivant se trouve une chaîne de connexion nommée **ConnectionString4NoUserIDNoPassword**. Le code C# recherche cette chaîne.

Vous devez modifier les noms réels dans les espaces réservés :

* {your_serverName_here}
* {your_databaseName_here}

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>

            <connectionStrings>
                <clear />
                <add name="ConnectionString4NoUserIDNoPassword"
                providerName="System.Data.ProviderName"

                connectionString=
                "Server=tcp:{your_serverName_here}.database.windows.net,1433;
                Database={your_databaseName_here};
                Connection Timeout=30;
                Encrypt=True;
                TrustServerCertificate=False;" />
            </connectionStrings>
        </configuration>



Dans cet exemple, nous avons choisi d'omettre deux paramètres :

* User ID={your_userName_here};
* Password={your_password_here};

Vous pouvez les inclure, mais il est parfois préférable de laisser votre programme récupérer ces valeurs à partir de la saisie clavier de l'utilisateur. Cela dépend.

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Jan17_HO3-->


