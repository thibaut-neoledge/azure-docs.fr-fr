## Présentation du stockage de fichiers
Le stockage de fichiers propose un stockage partagé pour les applications utilisant le protocole SMB 2.1 ou SMB 3.0. Les machines virtuelles et les services cloud Microsoft Azure peuvent partager des données de fichiers entre plusieurs composants d'application grâce à des partages montés. Les applications locales peuvent accéder aux données de fichiers d'un partage via l'API de stockage de fichiers.

Les applications exécutées dans les machines virtuelles ou services cloud Azure peuvent monter un partage de stockage de fichiers pour accéder aux données de fichiers, tout comme une application de bureau monterait un partage SMB classique. Un nombre indéfini de machines virtuelles ou de rôles Azure peuvent monter le partage de stockage de fichiers et y accéder simultanément.

Un partage de stockage de fichiers étant un partage de fichiers standard dans Azure utilisant le protocole SMB, les applications exécutées dans Azure peuvent accéder aux données du partage grâce à des API d’E/S de fichiers. Les développeurs peuvent ainsi tirer profit de leur code et compétences actuels pour migrer les applications existantes. Les professionnels de l'informatique peuvent utiliser des applets de commande PowerShell pour créer, monter et gérer les partages de stockage de fichiers dans le cadre de l'administration des applications Azure. Vous trouverez dans ce guide des exemples de ces deux méthodes.

Voici quelques utilisations courantes du partage de fichiers :

* Migration d'applications locales reposant sur des partages de fichiers à exécuter sur des machines virtuelles ou services cloud Azure, sans réécriture coûteuse
* Stockage de paramètres d'application partagés, comme dans les fichiers de configuration
* Stockage de données de diagnostic, comme des journaux, des indicateurs de performances et des vidages sur incident dans un emplacement partagé 
* Stockage d'outils et d'utilitaires nécessaires au développement ou à l'administration de machines virtuelles ou de services cloud Azure

## Concepts du stockage de fichiers
Le stockage de fichiers est composé des éléments suivants :

![files-concepts][files-concepts]

* **Compte de stockage :** tout accès au stockage Azure s'effectue via un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure](../articles/storage/storage-scalability-targets.md).
* **Partage** : un partage de stockage de fichiers est un partage de fichiers SMB dans Azure. Tous les répertoires et fichiers doivent être créés dans un partage parent. Un compte peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans la limite de la capacité totale de 5 To du partage de fichiers.
* **Répertoire** : hiérarchie optionnelle de répertoires.
* **Fichier :** fichier du partage. Un fichier peut avoir une taille de 1 To maximum.
* **Format d’URL** : les fichiers sont adressables à l’aide du format d’URL suivant : https://`<storage
  account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`
  
  L’URL de l’exemple suivant peut être utilisée pour traiter l’un des fichiers dans le schéma ci-dessus : `http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

Pour plus d'informations sur la façon de nommer les partages, les répertoires et les fichiers, consultez la rubrique [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](http://msdn.microsoft.com/library/azure/dn167011.aspx).

[files-concepts]: ./media/storage-file-concepts-include/files-concepts.png

<!----HONumber=AcomDC_0204_2016-->