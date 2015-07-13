## Présentation du stockage d'objets blob

Le stockage d'objets blob Azure est un service permettant de stocker de gros volumes de données non structurées, telles que du texte ou des données binaires, accessibles depuis n'importe où dans le monde via HTTP ou HTTPS. Vous pouvez utiliser le stockage d'objets blob pour exposer les données publiquement dans le monde ou pour le stockage privé de données d'applications.

Voici quelques utilisations courantes du stockage d’objets blob :

-   Mise à disposition d’images ou de documents directement dans un navigateur
-   Stockage de fichiers pour un accès distribué
-   Diffusion en continu de vidéo et d’audio
-   Sauvegarde sécurisée et récupération d’urgence
-   Stockage des données pour l’analyse par un service local ou hébergé par Azure

## Concepts du service BLOB

Le service d’objets blob contient les composants suivants :

![Blob1][Blob1]

-   **Compte de stockage :** tout accès au stockage Azure s'effectue via un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure](storage-scalability-targets.md).

-   **Conteneur :** un conteneur regroupe un ensemble d'objets blob. Tous les objets blob doivent figurer dans un conteneur. Un compte peut contenir un nombre illimité de conteneurs. Un conteneur peut stocker un nombre illimité d’objets blob.

-   **Objet blob :** fichier de tout type et de toute taille. Il existe deux types d’objets blob qui peuvent être enregistrés dans un stockage Azure : les objets blob de blocs et les objets blob de pages. La plupart des fichiers sont des objets blob de blocs. Un objet blob de blocs peut avoir une taille maximale de 200 Go. Ce didacticiel utilise des objets blob de blocs. Les objets blob de pages, autre type d'objet blob, peuvent avoir une taille maximale de 1 To et sont plus efficaces lorsque les plages d'octets d'un fichier sont fréquemment modifiées. Pour plus d’informations sur les blobs, consultez la page [Présentation des objets BLOB de blocs et des objets BLOB de pages](https://msdn.microsoft.com/library/azure/ee691964.aspx).

-   **Format d'URL :** les objets blob sont adressables à l'aide du format d'URL suivant : http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`
      
    L'URL de l'exemple suivant peut être utilisée pour traiter l'un des fichiers dans le schéma ci-dessus : `http://sally.blob.core.windows.net/movies/MOV1.AVI`


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!---HONumber=62-->