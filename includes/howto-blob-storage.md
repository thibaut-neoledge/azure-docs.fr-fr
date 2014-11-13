## <a name="what-is"> </a>Présentation du stockage d’objets blob

Le stockage d'objets blob Azure est un service permettant de stocker de gros volumes de
données non structurées, telles que du texte ou des données binaires, accessibles depuis n'importe où dans le monde via
HTTP ou HTTPS. Vous pouvez utiliser le stockage d'objets blob pour exposer les données publiquement dans le monde ou
pour le stockage privé de données d'applications.

Voici quelques utilisations courantes du stockage d’objets blob :

-   Mise à disposition d'images ou de documents directement dans un navigateur
-   Stockage de fichiers pour un accès distribué
-   Diffusion en continu de vidéo et d'audio
-   Sauvegarde sécurisée et récupération d'urgence
-   Stockage des données pour l'analyse par un service
    local ou hébergé par Azure

## <a name="concepts"> </a>Concepts

Le service BLOB contient les composants suivants :

![Blob1][Blob1]

-   **Compte de stockage :** tous les accès à Azure Storage passent par un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure][Objectifs de performance et évolutivité du stockage Azure].

-   **Conteneur :**un conteneur regroupe un ensemble d'objets blob. Tous les objets blob doivent figurer dans un conteneur. Un compte peut contenir un
    nombre illimité de conteneurs. Un conteneur peut stocker un nombre illimité
    d'objets blob.

-   **Blob :** fichier de n'importe quel type et de n'importe quelle taille. Deux types d'objets blob peuvent être stockés dans Azure Storage : objets blob de blocs et de pages.
    La plupart des fichiers sont des objets blob de blocs. La taille d'un objet blob de blocs peut atteindre
    200 Go. Ce didacticiel utilise des objets blob de blocs. La taille des objets blob de pages,
    autre type d'objets blob, peut atteindre 1 To et ceux-ci sont plus efficaces lorsque les plages
    d'octets d'un fichier sont modifiées fréquemment. Pour plus d'informations
    sur les objets blob, consultez la page [Présentation des objets blob de blocs et des objets blob de pages][Présentation des objets blob de blocs et des objets blob de pages].

-   **Format d'URL :** Les objets blob sont adressables à l'aide du format d'URL
    suivant :
    http://`<storage account>`.blob.core.windows.net/`<container>`/`<blob>`

    L'URL de l'exemple suivant peut être utilisée pour traiter l'un des objets blob dans le
    schéma ci-dessus :
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`

  [Blob1]: ./media/howto-blob-storage/blob1.jpg
  [Objectifs de performance et évolutivité du stockage Azure]: http://msdn.microsoft.com/fr-fr/library/dn249410.aspx
