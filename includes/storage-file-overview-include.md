## <a name="overview"></a>Vue d’ensemble
Le stockage de fichiers Azure est un service qui propose des partages de fichiers dans le cloud en utilisant le [protocole SMB](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)standard. Les protocoles SMB 2.1 et SMB 3.0 sont pris en charge. Avec le stockage de fichiers Azure, vous pouvez migrer vers Azure des applications héritées qui s’appuient sur des partages de fichiers, rapidement et sans réécritures onéreuses. Les applications s’exécutant sur des machines virtuelles Azure, dans des services cloud ou à partir de clients locaux peuvent monter un partage de fichiers dans le cloud, tout comme une application de bureau monte un partage SMB standard. Un nombre illimité de composants d’application peuvent ensuite monter un partage de stockage de fichiers et y accéder simultanément.

Un partage Stockage Fichier étant un partage de fichiers SMB standard, les applications exécutées dans Azure peuvent accéder aux données du partage par le biais d’API d’E/S de système de fichiers. Les développeurs peuvent ainsi tirer profit de leur code et compétences actuels pour migrer les applications existantes. Les professionnels de l'informatique peuvent utiliser des applets de commande PowerShell pour créer, monter et gérer les partages de stockage de fichiers dans le cadre de l'administration des applications Azure.

Vous pouvez créer des partages de fichiers Azure à l’aide du [portail Azure](https://portal.azure.com), des applets de commande PowerShell pour Azure Storage, des bibliothèques clientes Azure Storage ou de l’API REST d’Azure Storage. En outre, comme ces partages de fichiers sont des partages SMB, vous pouvez y accéder via les API de système de fichiers standard et connues.



<!--HONumber=Jan17_HO3-->


