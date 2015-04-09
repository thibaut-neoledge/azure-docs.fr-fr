## Présentation du service de Table

Le service de stockage de tables Azure permet de stocker de grandes quantités de
données structurées. Le service est un magasin de données NoSQL qui accepte
les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure
sont idéales pour le stockage des données structurées non relationnelles. Les utilisations courantes
du service de Table sont les suivantes :

-   Stockage de téraoctets de données structurées capables de servir des applications d'échelle
    web
-   Stockage des jeux de données qui ne nécessitent pas de jointures complexes, de clés étrangères ni
    de procédures stockées, et qui peuvent être dénormalisés pour un accès rapide
-   Interrogation rapide des données par requête à l'aide d'un index cluster
-   Accès aux données à l'aide du protocole OData et des requêtes LINQ à l'aide des bibliothèques .NET
    du service de données WCF

Vous pouvez utiliser le service de Table pour stocker et interroger des jeux importants de
données structurées et non relationnelles. Vos tables sont mises à l'échelle lorsque la demande
augmente.

## Concepts du service de Table

Le service de table contient les composants suivants :

![Table1][Table1]

-   **Format d'URL :** le code traite les tables dans un compte à l'aide de
    ce format d'adresse :   
    http://`<storage account>`.table.core.windows.net/`<table>`  
      
    Vous pouvez traiter les tables Azure directement à l'aide de cette adresse
    avec le protocole OData. Pour plus d'informations, consultez la rubrique [OData.org][]

-   **Compte de stockage :** tout accès au stockage Azure s'effectue
    via un compte de stockage. Pour plus d'informations sur la capacité du compte de stockage, voir [Objectifs de performance et d'évolutivité d'Azure Storage](http://msdn.microsoft.com/library/azure/dn249410.aspx).

-   **Table** : une table est une collection d'entités. Les tables n'appliquent pas
    de schéma dans les entités, ce qui signifie qu'une même table peut contenir
    des entités pourvues de différents ensembles de propriétés. Le nombre de tables qu'un 
	compte de stockage peut contenir est restreint uniquement par 
    la limite de capacité du compte de stockage.

-   **Entité **: une entité est un ensemble de propriétés similaire à une ligne
    de base de données. Une entité peut avoir une taille maximale de 1 Mo.

-   **Propriétés **: une propriété est une paire nom-valeur. Chaque entité peut
    inclure jusqu'à 252 propriétés pour stocker des données. Chaque entité possède également 3
    propriétés système qui spécifient une clé de partition, une clé de ligne et un
    horodatage. Les entités ayant la même clé de partition peuvent être interrogées plus
    rapidement, et insérées/mises à jour dans des opérations atomiques. La clé de ligne d'une entité
    est son identificateur unique au sein d'une partition.


  
  [Table1]: ./media/storage-table-concepts-include/table1.png
  [OData.org]: http://www.odata.org/

<!--HONumber=49-->