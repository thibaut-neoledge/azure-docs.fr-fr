
Les problèmes de diagnostic avec un service cloud Microsoft Azure nécessite la collecte des fichiers journaux de service sur les machines virtuelles lorsque les problèmes surviennent. Vous pouvez utiliser l’extension AzureLogCollector à la demande pour exécuter une collecte unique de journaux depuis une ou plusieurs machines virtuelles de Service Cloud (à partir des rôles web et de travail) et transférer les fichiers collectés dans un compte de stockage Azure, le tout sans connexion à distance à une machine virtuelle quelconque.

> [AZURE.NOTE]Vous trouverez des descriptions de la plupart des informations consignées sur http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.

Il existe deux modes de collecte dépendant des types de fichier à recueillir.
- Journaux des agents invités Azure uniquement (GA). Ce mode de collecte inclut tous les journaux liés aux agents invités Azure et d’autres composants Azure.
- Tous les journaux (complète). Ce mode de collecte recueille tous les fichiers en mode GA plus :

  - journaux des événements système et d’application
  
  - Journaux d’erreurs HTTP
  
  - Journaux IIS
  
  - Fichiers journaux d’installation
  
  - autres journaux système.

Dans les deux modes de collecte, les dossiers de collecte de données supplémentaires peuvent être spécifiés à l’aide d’une collection de la structure suivante :

- **Nom** : nom de la collection qui sera utilisé comme nom de sous-dossier dans le fichier zip à collecter.

- **Emplacement** : chemin d’accès au dossier sur l’ordinateur virtuel dans lequel les fichiers seront collectés.

- **Modèle de recherche** : modèle des noms de fichiers à collecter. « * » constitue la valeur par défaut.

- **Récursive** : si les fichiers sont collectés de façon récursive sous le dossier.

<!------HONumber=AcomDC_0323_2016-->