## <a name="what-is"> </a>Présentation du service de table

Le service de stockage de tables Azure stocke de grandes quantités de
données structurées. Il s'agit d'une banque de données NoSQL qui accepte les
appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure
sont idéales pour le stockage des données structurées non relationnelles. Voici quelques utilisations courantes
du service de Table :

-   Stockage des téraoctets de données structurées capables de servir
    des applications web
-   Stockage des jeux de données ne nécessitant pas de jonctions complexes, de clés étrangères ni de
    procédures stockées, et pouvant être dénormalisés pour un accès rapide
-   Interrogation rapide des données par requête à l’aide d’un index cluster
-   Accès aux données avec le protocole OData et les quêtes LINQ avec les bibliothèques WCF
    Data Service .NET

Vous pouvez utiliser le service de Table pour stocker et interroger de grands ensembles de
données non relationnelles structurées. Vos tables évoluent en même temps que la
demande.

## <a name="concepts"> </a>Concepts

Le service de table contient les composants suivants :

![Table1][Table1]

-   **Format d'URL :** le code traite les tables dans un compte à l'aide du
    format d'adresse suivant :
    http://`<storage account>`.table.core.windows.net/`<table>`

    Vous pouvez traiter les tables Azure directement à l'aide de cette adresse avec le
    protocole OData. Pour plus d'informations, consultez [OData.org][OData.org]

-   **Compte de stockage :** tous les accès à Azure Storage passent par un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure][Objectifs de performance et évolutivité du stockage Azure].

-   **Table** : une table est une collection d’entités. Les tables n'appliquent pas de
    schéma sur les entités, ce qui signifie qu'une seule table peut contenir des entités
    ayant différents ensembles de propriétés. Le nombre de tables qu'un
    compte de stockage peut contenir est uniquement limité à la
    capacité du compte de stockage.

-   **Entité** : une entité est un ensemble de propriétés semblable à une ligne de
    base de données. Une entité peut avoir une taille maximale de 1 Mo.

-   **Propriétés** : une propriété est une paire nom-valeur. Chaque entité peut
    inclure jusqu'à 252 propriétés pour stocker des données. Chaque entité possède également 3
     propriétés système qui spécifient une clé de partition, une clé de ligne et un
    horodatage. Les entités ayant la même clé de partition peuvent être interrogées plus
    rapidement et insérées ou mises à jour dans des opérations atomiques. La clé de ligne
    d'une entité constitue son identificateur unique à l'intérieur d'une partition.

  [Table1]: ./media/storage-java-how-to-use-table-storage/table1.png
  [OData.org]: http://www.odata.org/
  [Objectifs de performance et évolutivité du stockage Azure]: http://msdn.microsoft.com/fr-fr/library/dn249410.aspx
