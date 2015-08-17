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

-   **Objet blob :** fichier de tout type et de toute taille. Azure Storage propose trois types d’objets blob : les objets blob de blocs, les objets blob d’ajouts et les objets blob de pages.
    
	Les *objets blob de blocs* sont parfaits pour le stockage des fichiers texte ou binaires, tels que les documents et les fichiers multimédias. Les *objets blob d’ajout* sont similaires aux objets blob de blocs dans la mesure où ils sont composés de blocs. Cependant, ils sont optimisés pour les opérations d’ajouts et sont donc utiles pour les scénarios de journalisation. Un objet blob de blocs ou d’ajouts peut contenir jusqu’à 50 000 blocs de 4 Mo chacun, soit une taille totale légèrement supérieure à 195 Go (4 Mo X 50 000).
    
	La taille maximale des *objets blob de pages* peut atteindre 1 To. Ces objets sont plus efficaces pour les opérations fréquentes de lecture/écriture. Azure Virtual Machines utilise des objets blob de pages comme disques de données et disques du système d’exploitation.

	Pour plus d’informations sur les objets blob, consultez [Présentation des objets blob de blocs, des objets blob d’ajout et des objets blob de pages](https://msdn.microsoft.com/library/azure/ee691964.aspx).

## Désignation et référencement des conteneurs et des objets BLOB

Vous pouvez traiter un objet BLOB dans votre compte de stockage à l’aide du format d’URL suivant :
   
    http://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>  
      
Par exemple, voici une URL qui traite l’un des objets BLOB dans le diagramme ci-dessous :

    http://sally.blob.core.windows.net/movies/MOV1.AVI

### Règles d’affectation des noms de conteneurs

Un conteneur doit posséder un nom DNS valide et conforme aux règles suivantes :

- L’ensemble des lettres doivent être minuscules.
- Les noms de conteneur doivent commencer par une lettre ou un chiffre, et peuvent comporter uniquement des lettres, des chiffres et des tirets (-).
- Chaque tiret (-) doit être immédiatement précédé et suivi d’une lettre ou d’un chiffre ; les tirets consécutifs sont interdits.
- Les noms de conteneurs doivent comporter entre 3 et 63 caractères.

### Règles d’affectation des noms d’objets BLOB

Les noms des objets BLOB doivent être conformes aux règles suivantes :

- Un nom d’objet BLOB peut contenir diverses combinaisons de caractères.
- Les noms des objets BLOB doivent comporter entre 1 et 1 024 caractères.
- Les noms des objets BLOB sont sensibles à la casse.
- Les caractères réservés d’URL doivent être placés dans des séquences d’échappement appropriées.
- Les noms des objets BLOB ne peuvent pas comporter plus de 254 segments de tracé. Un segment de tracé est la chaîne positionnée entre deux caractères consécutifs de délimitation (*par exemple*, la barre oblique), qui correspond au nom d’un répertoire virtuel.

Le service BLOB est basé sur un schéma de stockage plat. Vous pouvez créer une hiérarchie virtuelle en définissant un caractère ou un délimiteur de chaîne dans le nom d’objet BLOB. Par exemple, la liste suivante représente quelques noms d’objets BLOB valides et uniques :

	/a
	/a.txt
	/a/b
	/a/b.txt

Vous pouvez utiliser le caractère de délimitation pour hiérarchiser les objets BLOB.


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!---HONumber=August15_HO6-->