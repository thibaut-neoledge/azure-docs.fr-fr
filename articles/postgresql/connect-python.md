---
title: "Lecture Python d’une base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Explique, sans requérir de connaissances préalables, comment exécuter un exemple de code Python pour écrire et lire des données d’une table dans une base de données Azure pour PostgreSQL."
services: postgresql
author: MightyPen
ms.author: genemi
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 42f5e8191e52d1e2a4502a961158b6fec1c061d1
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="python-reading-from-azure-database-for-postgresql"></a>Lecture Python d’une base de données Azure pour PostgreSQL


Cet article propose un court programme Python qui utilise une instruction SQL SELECT pour lire une ligne d’une table. Il explique également comment trouver et installer tous les composants requis pour exécuter le programme Python.

Notre exemple de programme Python et les outils qui s’y rattachent s’appliquent tous aussi bien à différentes plateformes, notamment Windows, Mac et Linux. 


## <a name="install-the-python-interpreter"></a>Installer l’interpréteur Python


L’exemple de code Python de cet article est écrit pour la version 2.7 de l’interpréteur Python ; il ne peut pas être exécuté avec la version 3.x.

Téléchargez et installez la version 2.7 de l’interpréteur Python en suivant ce lien :

- [Télécharger l’interpréteur Python sur python.org](https://www.python.org/downloads/)

Après l’installation, vérifiez que vous trouvez et que vous savez exécuter l’interpréteur en ligne de commande. Utilisez une commande telle que :

`python.exe -?`


## <a name="install-pipexe-the-python-module-installer"></a>Installer pip.exe, le module d’installation de Python


L’installation de l’interpréteur Python peut avoir également installé pip.exe, potentiellement dans un sous-répertoire nommé *Scripts/*. Pip.exe installe les modules Python spécialisés. Vérifiez que vous trouvez et que vous savez exécuter pip.exe.

`pip.exe`

Si vous n’arrivez pas à exécuter pip.exe, regardez si vous avez le fichier d’utilitaire Python nommé **get-pip.py**. Si c’est le cas, vous pouvez l’exécuter pour obtenir pip.exe :

`python.exe get-pip.py`


## <a name="install-psycopg-the-connection-module"></a>Installer psycopg, le module de connexion


Notre programme Python a besoin d’un module qui sait le connecter au serveur de base de données Azure pour PostgreSQL. Le nom du module de connexion est **psycopg2**. Si vous souhaitez plus de détails, consultez :

- [Site web psycopg2](http://initd.org/psycopg/)

Installez psycopg2 à l’aide de la commande d’installation pip.exe suivante :

`pip.exe install psycopg2`


## <a name="create-an-azure-database-for-postgresql-server"></a>Créer un serveur de base de données Azure pour PostgreSQL


Si vous n’avez pas encore accès à un serveur de base de données Azure pour PostgreSQL, voici une documentation qui explique comment créer un serveur :

- [Créer une base de données Azure pour PostgreSQL à l’aide du Portail Azure](quickstart-create-server-database-portal.md)
- [Créer une base de données Azure pour PostgreSQL à l’aide d’Azure CLI](quickstart-create-server-database-azure-cli.md)


## <a name="obtain-the-connection-string-values"></a>Récupérer les valeurs de la chaîne de connexion


Sur le Portail Azure, vous pouvez récupérer les valeurs de la chaîne de connexion de votre serveur de base de données PostgreSQL. Il vous faut les valeurs de paramètres décrites dans le tableau suivant.

- La colonne *Name* &ndash; affiche les identificateurs de paramètres obligatoires pour psycopg2.
- La colonne *-Symbol* &ndash; affiche les identificateurs de paramètres obligatoires pour notre exemple de programme Python, *PythonDriver.py*.


| Nom | -Symbol | Exemple de valeur |
| :--  | :--     | :--           |
| host | -h | myazurepostgresql.database.windows.net |
| user | -U | myalias@myazurepostgresql |
| dbname | -d | postgres<br />*(Tous les serveurs PostgreSQL possèdent une base de données nommée **postgres**.)* |
| port | -p | 5432 *(probablement cette valeur spécifique de 5432.)* |
| password | -P | MySecretPassword |
||||


## <a name="the-python-sample-program"></a>Exemple de programme Python


Cette section propose le code source de notre exemple de programme Python. Vous l’exécuterez plus loin dans cet article.

```python
# PythonDriver.py
# Python version 2.7

import psycopg2
import sys
import getopt

def main():

    host = ""
    user = ""
    dbname = ""
    port = ""
    password = ""

    try:
        opts, args = getopt.getopt(sys.argv[1:], "h:U:d:p:P:", [])
    except getopt.GetoptError as exc:
        print str(exc)
        usage()
        exit(2)

    for o, a in opts:
        if o == "-h": host = str(a)
        if o == "-U": user = str(a)
        if o == "-d": dbname = str(a)
        if o == "-p": port = str(a)
        if o == "-P": password = str(a)

    conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)

    try:
        # Create a connection.  Raise an exception if cannot connect.
        conn = psycopg2.connect(conn_string) 
        cursor = conn.cursor()

        #cursor.execute("DROP TABLE testpy1;")
        #conn.commit()

        cursor.execute("CREATE TABLE testpy1 (id serial PRIMARY KEY, num integer, data varchar);")

        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (100, "First'row"))
        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (110, "Second_row"))
        conn.commit()

        cursor.execute("SELECT id, num, data FROM testpy1;")
        result = cursor.fetchone()

        # Optionally, you can comment these clean-up lines to leave
        # the testpy1 table available for your inspection by pgAdmin. 
        cursor.execute("DROP TABLE testpy1;")
        conn.commit()

    except Exception as exc:
        print "FAILED ", exc
        exit(1)

    print "SUCCESS: ", result

if __name__ == "__main__":
    main()
```


## <a name="command-lines-to-run-the-sample-program"></a>Lignes de commande pour exécuter l’exemple de programme


Cette section affiche les lignes de commande utilisées pour tester l’exemple de programme Python, ainsi que la sortie réelle de confirmation.

En ce qui concerne les paramètres décrits précédemment, vous devrez remplacer les valeurs de l’exemple affichées par les valeurs réelles de votre chaîne de connexion.

La syntaxe exacte pour l’exécution de l’exemple de programme Python précédent peut légèrement varier. Elle dépend de votre système d’exploitation et du type de console que vous utilisez.


#### <a name="windows-cmdexe-console"></a>Console cmd.exe Windows


Le bloc de code suivant affiche une série de tests réels de l’exemple de programme Python. Une ligne de commande cmd.exe simple a été utilisée. Après qu’un caractère de continuation de ligne « ^ » a été tapé :

1. Touche Entrée.
2. Affichage de l’expression **More?**.
3. Saisie d’une autre portion de la ligne, et ainsi de suite.

Cette technique de continuation de ligne a été utilisée afin d’empêcher l’exemple de devenir trop large pour s’afficher correctement ici ou à l’impression.

```cmd
set Prompt=[$P\]$_$+$G$G$S

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>> .\python.exe ".\_myalias\PythonDriver.py" ^
More?  -h myazurepostgresql.database.windows.net ^
More?  -p 5432 ^
More?  -d postgres ^
More?  -U myalias@myazurepostgresql ^
More?  -P mySecretPassword
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>>
```

Vous pouvez voir la ligne **SUCCESS** qui confirme l’exécution du programme.

Autre technique, la totalité de la ligne de commande peut être intégrée dans un fichier .bat. Il est alors possible d’exécuter le fichier .bat en ligne de commande cmd.exe.


#### <a name="powershell-file"></a>Fichier PowerShell


Dans la console PowerShell, la ligne de commande ne prend pas en charge le caractère de continuation de ligne. Par conséquent, dans cette section PowerShell, nous mettons les commandes dans un fichier PowerShell. Ensuite, nous exécutons le fichier en ligne de commande PowerShell.

Copiez le code suivant dans un fichier nommé *PythonDriverRun.ps1*.

```powershell
# PythonDriverRun.ps1
cd C:\Users\myalias\AppData\Local\Programs\Python\Python27\

.\python.exe `
 .\_myalias\PythonDriver.py `
 -h myazurepostgresql.database.windows.net `
 -p 5432 `
 -d postgres `
 -U myalias@myazurepostgresql `
 -P mySecretPassword
```

Exécutez PythonDriverRun.ps1 de la façon suivante. Vous pouvez voir la ligne **SUCCESS** imprimée par le programme pour confirmer l’exécution.

```cmd
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> .\_myalias\PythonDriverRun.ps1
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >>
```

Si vous préférez exécuter PythonDriverRun.ps1 directement en ligne de commande PowerShell, vous devez ajouter un caractère « & » et un espace avant la commande. Sans le caractère de début « & », le message de confirmation disparaît trop vite pour être visible.

```
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> & .\python.exe ".\_myalias\PythonDriver.py" -h myazurepostgresql.database.windows.net -p 5432 -d postgres -U myalias@myazurepostgresql -P mySecretPassword
SUCCESS:  (1, 100, "First'row")
```


## <a name="install-pgadmin-to-inspect-your-server"></a>Installer pgAdmin, pour examiner le serveur


Lorsque le programme PythonDriverRun.ps1 se termine, il fait le ménage derrière lui en supprimant la table testpy1 créée par le programme. Vous avez la possibilité d’utiliser un « # » pour commenter la ligne de code source qui émet l’instruction **DROP TABLE**. Cette option laisserait subsister la table pour vous permettre de l’inspecter par la suite.

L’outil pgAdmin vous permet d’inspecter n’importe quel serveur PostgreSQL, ainsi que les objets qu’il contient. Les utilisateurs de Microsoft SQL Server ou d’Azure SQL Database constateraient la similarité entre SQL Server Management Studio (SSMS) et pgAdmin.

Si vous le souhaitez, vous pouvez installer **pgAdmin** pour inspecter votre serveur et votre table **testpy1**.


#### <a name="1-install-pgadmin"></a>1. Installer pgAdmin


Les instructions d’installation de pgAdmin sont disponibles à l’adresse :

- [http://www.pgadmin.org/](http://www.pgadmin.org/)

Le nom du fichier exécutable peut être **pgAdmin4.exe**, et non pgAdmin.exe simplement.

Pour exécuter pgAdmin4.exe sur un ordinateur Windows, entrez une commande de ce type en ligne de commande :

`"C:\Program Files (x86)\pgAdmin 4\v1\runtime\pgAdmin4.exe"`


#### <a name="2-connect-pgadmin-to-your-server"></a>2. Connecter pgAdmin au serveur

 
À l’affichage de l’interface utilisateur pgAdmin, recherchez le volet **Navigateur**. Ensuite, cliquez avec le bouton droit sur **Serveurs** > **Créer** > **Serveurs**. Le terme *Créer* signifie ici créer une *connexion* à tous les serveurs PostgreSQL existants, y compris aux serveurs de base de données Azure pour PostgreSQL.

Lorsque la connexion est établie, une arborescence d’objets s’affiche dans le volet **Navigateur**.


#### <a name="3-navigate-in-the-pgadmin-tree-to-your-table"></a>3. Naviguer dans l’arborescence pgAdmin jusqu’à la table


Pour voir votre table, développez les éléments de l’arborescence de la façon suivante :

- **Serveurs** &gt; *[Votre serveur]* &gt; **Bases de données** &gt; postgres &gt; **Schémas** &gt; public &gt; **tables** &gt; testpy1

![pgAdmin montrant la table testpy1 dans l’arborescence](./media/connect-python/pgAdmin-postgresql-table-testpy1.jpg)


#### <a name="4-drop-the-testpy1-table"></a>4. Supprimer la table testpy1


Pour effectuer le nettoyage final, cliquez avec le bouton droit sur le nœud testpy1, puis cliquez sur **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes

- [Connexion Python à Azure SQL Database](../sql-database/sql-database-connect-query-python.md)
- [Bibliothèques de connexions de la base de données Azure pour PostgreSQL](concepts-connection-libraries.md)

