---
title: "Tester et déboguer des travaux U-SQL à l’aide d&quot;une exécution locale et du Kit de développement logiciel (SDK) Azure Data Lake U-SQL | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio et le Kit de développement logiciel (SDK) Azure Data Lake U-SQL pour tester et déboguer des travaux U-SQL sur votre station de travail locale."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 9d088a3afd8830c80462099cc869f1493d09fb41
ms.openlocfilehash: e0385315434ac7a10e121f88469aaaec45160b86


---
# <a name="test-and-debug-u-sql-jobs-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Tester et déboguer des travaux U-SQL à l’aide d'une exécution locale et du Kit de développement logiciel (SDK) Azure Data Lake U-SQL

Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio et le Kit de développement logiciel (SDK) Azure Data Lake U-SQL pour tester et déboguer des travaux U-SQL sur votre station de travail locale.  Ces deux fonctionnalités exécutées localement permettent d’exécuter des travaux U-SQL sur votre station de travail comme vous le faites dans le service Azure Data Lake. Ces fonctionnalités accélèrent les procédures de test et de débogage de vos travaux U-SQL.

Configuration requise : 

- Un compte Azure Data Lake Analytics. Voir [Prise en main d'Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md). 
- Les outils Azure Data Lake pour Visual Studio.  Voir [Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). 
- L’expérience de développement de script U-SQL. Voir [Prise en main d'Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md). 


## <a name="understand-data-root-and-file-path"></a>Comprendre le chemin d’accès à la racine de données et aux fichiers

L'exécution locale et le SDK U-SQL nécessitent un dossier racine de données. La racine de données est un « magasin local » pour le compte de calcul local. Il est équivalent au compte Data Lake Store d’un compte Data Lake Analytics dans Azure. Basculer vers un dossier racine de données différent revient à basculer vers un autre compte de magasin. Si vous souhaitez accéder aux données généralement partagées avec différents dossiers racine de données, vous devez utiliser des chemins d’accès absolus dans vos scripts, ou pour créer, sous ce dossier racine de données, des liens symboliques de système de fichiers (par exemple, mklink sur NTFS) qui pointent vers les données partagées. 

Le dossier racine de données est utilisé pour :

- Stocker les métadonnées, notamment des bases de données, des tables, des fonctions à valeurs de tables (TVF), des assemblys, etc.
- Rechercher les chemins d’entrée et de sortie qui sont définis comme des chemins relatifs dans U-SQL. Les chemins relatifs facilitent le déploiement de vos projets U-SQL dans Azure.

Vous pouvez utiliser un chemin relatif et un chemin absolu local dans des scripts U-SQL, et le chemin relatif est relatif par rapport au chemin vers le dossier racine de données spécifié. Il est recommandé d’utiliser « / » comme séparateur de chemin d’accès afin de rendre vos scripts compatibles avec le côté serveur. Voici quelques exemples de chemins relatifs et leur chemin absolu équivalent. Dans ces exemples, « C:\LocalRunDataRoot » est la racine de données :

|Chemin relatif|Chemin absolu|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Utiliser l'exécution locale depuis Visual Studio

Data Lake Tools pour Visual Studio fournit une expérience d'exécution locale U-SQL dans Visual Studio. À l’aide de cette fonctionnalité, vous pouvez :

- Exécuter des scripts U-SQL localement, ainsi que des assemblys C#.
- Déboguer un assembly C# localement.
- Créer, afficher et supprimer des catalogues U-SQL (bases de données locales, assemblys, schémas et tables) de l’Explorateur de serveurs. Le catalogue local est également accessible depuis l’Explorateur de serveurs.

    ![Data Lake Tools pour Visual Studio exécution locale catalogue local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Le programme d’installation de Data Lake Tools crée un dossier « C:\LocalRunRoot » à utiliser comme dossier racine de données par défaut. Le parallélisme d’exécution locale par défaut est 1. 

### <a name="to-configure-local-run-in-visual-studio"></a>Pour configurer l’exécution locale dans Visual Studio

1. Ouvrez Visual Studio.
2. Ouvrez l’**Explorateur de serveurs**.
3. Développez **Azure**, **Data Lake Analytics**.
4. Cliquez sur le menu **Data Lake**, puis sur **Options et paramètres**. 
5. Dans l’arborescence de gauche, développez **Azure Data Lake**, puis développez **Général**.

    ![Data Lake Tools pour Visual Studio exécution locale configurer paramètres](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Un projet Visual Studio U-SQL est requis pour effectuer l’exécution locale. Cette partie diffère de l'exécution de scripts U-SQL à partir d'Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Pour exécuter un script U-SQL localement
1. Dans Visual Studio, ouvrez votre projet U-SQL.   
2. Cliquez avec le bouton droit sur un script U-SQL dans l'Explorateur de solutions, puis cliquez sur **Soumettre le script**. Sélectionnez (local) comme compte Analytics pour exécuter votre script localement.
3. Vous pouvez également cliquer sur le compte (local) en haut de la fenêtre de script, puis sur **Envoyer** (ou utiliser le raccourci clavier**CTRL + F5**).

    ![Data Lake Tools pour Visual Studio exécution locale soumettre travaux](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-data-lake-u-sql-sdk"></a>Utiliser une exécution locale à partir du Kit de développement logiciel (SDK) U-SQL Data Lake
 
Outre l’exécution locale de scripts U-SQL-U à l’aide de Visual Studio, vous pouvez également utiliser le Kit de développement logiciel (SDK) U-SQL Azure Data Lake pour exécuter les scripts U-SQL localement avec la ligne de commande et les interfaces de programmation, ce qui vous permet de faire évoluer votre test local U-SQL.

### <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Le Kit de développement logiciel (SDK) U-SQL Data Lake requiert les dépendances suivantes :

- [Microsoft .NET Framework 4.6 ou une version ultérieure](https://www.microsoft.com/en-us/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 et Windows SDK 10.0.10240.0 ou une version ultérieure. Il existe 2 façons d'obtenir ces éléments :

    - Installer Visual Studio ([Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou)). Vous devez disposer d'un sous-dossier « \Windows Kits\10 » dans le dossier Program Files, par exemple, C:\Program Files (x86)\Windows Kits\10\" ; vous trouverez également la version du Kit de développement logiciel (SDK) Windows 10 sous « \Windows Kits\10\Lib ». Si vous ne voyez pas ces dossiers, réinstallez Visual Studio et assurez-vous que vous avez coché Windows 10 SDK lors de l’installation. De cette façon, le script de compilation local U-SQL trouvera automatiquement ces dépendances.

    ![Data Lake Tools pour Visual Studio exécution locale Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)
 
    - Vous pouvez également installer [Data Lake Tools pour Visual Studio](http://aka.ms/adltoolsvs). Vous trouverez les fichiers VC++ et du Kit de développement logiciel (SDK) Windows préconfigurés sous C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Dans ce cas, le compilateur local U-SQL ne peut pas trouver automatiquement les dépendances et vous devez donc spécifier le chemin CppSDK correspondant. Vous pouvez copier les fichiers vers un autre emplacement ou les utiliser tels quels. Vous pouvez ensuite choisir de définir une variable d’environnement « SCOPE_CPP_SDK » vers le répertoire, ou spécifier l'argument « -CppSDK » avec ce répertoire sur la ligne de commande de l’application d’assistance d’exécution locale. 

Après avoir installé le Kit de développement logiciel (SDK), vous devez effectuer les étapes de configuration suivantes :

- Définir la variable d'environnement **SCOPE_CPP_SDK** 

    Si vous obtenez Microsoft Visual C++ et le Kit de développement logiciel (SDK) Windows en installant Data Lake Tools pour Visual Studio, vérifiez que vous disposez du dossier suivant :

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Définissez une nouvelle variable d’environnement appelée « SCOPE_CPP_SDK » qui pointe vers ce répertoire. Ou copiez le dossier vers un autre emplacement et spécifiez « SCOPE_CPP_SDK ».

    Outre la définition de la variable d’environnement, vous pouvez également spécifier l'argument « -CppSDK » lorsque vous utilisez une ligne de commande. Cet argument remplace votre variable d’environnement CppSDK par défaut. 

- Définir la variable d'environnement **LOCALRUN_DATAROOT** 

    Définissez une variable d’environnement appelée « LOCALRUN_DATAROOT » qui pointe vers la racine de données. 

    Outre la définition de la variable d’environnement, vous pouvez également spécifier l'argument « -DataRoot » avec le chemin vers la racine de données lorsque vous utilisez une ligne de commande. Cet argument remplace votre variable d’environnement de racine de données par défaut. Et vous devez ajouter cet argument à chaque ligne de commande que vous exécutez afin de pouvoir utiliser la même nouvelle racine de données pour toutes les opérations. 

### <a name="using-the-sdk-from-command-line"></a>Utilisation du SDK à partir de la ligne de commande

#### <a name="the-command-line-interface-of-the-helper-application"></a>L’interface de ligne de commande de l’application d’assistance

Le fichier LocalRunHelper.exe du Kit de développement logiciel (SDK) est l’application d’assistance de ligne de commande qui fournit des interfaces pour la plupart des fonctions d’exécution locale couramment utilisées. Notez que la commande et les commutateurs d’arguments respectent la casse. Pour l’appeler :

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Exécutez « LocalRunHelper.exe » sans argument ou avec le commutateur **help** pour afficher les informations d’aide :

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

Dans les informations d’aide : 

-  **Commande** fournit le nom de la commande.  
-  **Argument obligatoire** répertorie les arguments qui doivent être fournis.  
-  **Argument facultatif** répertorie les arguments facultatifs et avec les valeurs par défaut.  Les arguments bool facultatifs n’ont pas de paramètre et leur valeur est négative par défaut.

#### <a name="return-value-and-logging"></a>Valeur de retour et journalisation

L’application d’assistance renvoie **0** en cas de réussite et **-1** en cas de défaillance. Par défaut, l’assistant affiche tous les messages dans la console active.  Toutefois, la plupart des commandes prennent en charge l'argument facultatif **-MessageOut path_to_log_file** qui redirige les sorties vers un fichier journal.


### <a name="the-sdk-usage-samples"></a>Exemples d’utilisation du SDK

#### <a name="compile-and-run"></a>Compilation et exécution

La commande « run » sert à compiler le script puis à exécuter les résultats compilés. Ses arguments de ligne de commande sont une combinaison de ces commandes « compile » et « run ».

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Voici un exemple :

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

En plus de combiner les commandes « compile » et « run », vous pouvez compiler et exécuter séparément les fichiers exécutables compilés. 

#### <a name="compile-u-sql-script"></a>Compilation de script U-SQL

La commande « compile » sert à compiler un script U-SQL avec des exécutables. 

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Arguments facultatifs pour la compilation, vous trouverez plus d’informations sur le répertoire de travail (-WorkDir) dans l’annexe.

|Argument|Description|
|--------|-----------|
|-CppSDK [valeur par défaut '']|Répertoire CppSDK|
|-DataRoot [valeur par défaut '']|DataRoot pour données et métadonnées, il s'agit par défaut de la variable d’environnement « LOCALRUN_DATAROOT » |
|-MessageOut [valeur par défaut '']|Vidage des messages de la console dans un fichier|
|-Shallow [valeur par défaut 'False']|Compilation superficielle, vérifie uniquement la syntaxe du script et du retour.|
|-WorkDir [valeur par défaut 'D:\localrun\t\ScopeWorkDir']|Répertoire pour l’utilisation du compilateur et les résultats, vous trouverez plus d'informations dans la rubrique Répertoire de travail de l'annexe.|

Arguments facultatifs pour les assemblys et le code-behind :

|Argument|Description|
|--------|-----------|
|-CodeBehind [valeur par défaut 'False']|Le script comporte un code-behind .cs qui sera compilé et enregistré automatiquement en tant qu’objet UDO|
|-References [valeur par défaut '']|Liste des chemins d’accès vers des assemblys de référence supplémentaires ou des fichiers de données de code-behind, séparés par ';'|
|-UseDatabase [valeur par défaut 'master']|Base de données à utiliser pour l’inscription temporaire des assemblys et du code-behind|
|-UdoRedirect [valeur par défaut 'False']|Générer une configuration de redirection d'assembly Udo qui indique au runtime .Net de sonder d'abord les assemblys dépendants du répertoire de sortie compilé lorsqu'UDO est appelé|

Voici quelques exemples d'utilisation :

Compilation de script U-SQL :

    LocalRunHelper compile -Script d:\test\test1.usql

Compilez le script U-SQL et définissez le dossier racine de données. Notez que cette opération remplacera la variable d’environnement définie.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compilez le script U-SQL et définissez le répertoire de travail, l'assembly de référence et la base de données.

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-result"></a>Exécution du résultat compilé

La commande « execute » est utilisée pour exécuter les résultats compilés.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Arguments facultatifs :

|Argument|Description|
|--------|-----------|
|-DataRoot [valeur par défaut '']|DataRoot pour l'exécution des métadonnées, il s'agit par défaut de la variable d’environnement « LOCALRUN_DATAROOT » |
|-MessageOut [valeur par défaut '']|Vidage des messages de la console dans un fichier|
|-Parallel [valeur par défaut '1']|Exécuter les étapes d'exécution locale générées avec le niveau de parallélisme spécifié|
|-Verbose [valeur par défaut 'False']|Afficher les sorties détaillées du runtime|

Voici un exemple d’utilisation :

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="using-the-sdk-with-programming-interface"></a>Utilisation de l'interface de programmation du Kit de développement logiciel (SDK) 

Les interfaces de programmation se trouvent toutes dans l’assembly « Microsoft.Analytics.LocalRun » et vous permettent d'intégrer les fonctionnalités du Kit de développement logiciel (SDK) U-SQL ainsi que l'infrastructure de test C# pour faire évoluer votre test local du script U-SQL (un nouveau document dans ce sens sera bientôt disponible). Pour plus d'informations sur les interfaces, consultez l'annexe.

## <a name="appendix"></a>Annexe

### <a name="working-directory"></a>Répertoire de travail

Lors d'une exécution locale du script U-SQL, un répertoire de travail est créé pendant la compilation.  Outre les sorties de compilation, les fichiers exécutables nécessaires à l'exécution locale seront également copiés sous forme de clichés instantanés dans ce répertoire de travail.   Si l'argument **-WorkDir** n’est pas fourni dans la ligne de commande, le répertoire de travail par défaut « ScopeWorkDir » sera créé dans le répertoire actuel. Les fichiers dans le répertoire de travail sont affichés comme décrit ci-dessous.

|Directory/File|Définition|Description|
|--------------|----------|-----------|
|ScopeWorkDir|Le répertoire de travail|dossier racine|
|C6A101DDCB470506|Chaîne de hachage de la version exécutable|Cliché instantané des fichiers exécutables nécessaires à l'exécution locale|
|\.\Script_66AE4909AA0ED06C|Nom de script + chaîne de hachage du chemin du script|Résultats de la compilation et journalisation des étapes d'exécution|
|\.\.\\_\_script\_\_.abr|Sortie du compilateur|Le fichier algèbre|
|\.\.\\_\_ScopeCodeGen\_\_.*|Sortie du compilateur|Code géré généré|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|Sortie du compilateur|Code natif généré|
|\.\.\referenced_assemblies|Référence d’assembly|Fichiers d’ASSEMBLY DE RÉFÉRENCE|
|\.\.\deployed_resources|Déploiement de ressources|Fichiers de DÉPLOIEMENT DE RESSOURCES|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|Journal d'exécution|Journal des étapes d'exécution|

### <a name="programming-interfaces-for-azure-data-lake-u-sql-sdk"></a>Interfaces de programmation pour le SDK U-SQL Azure Data Lake

Les interface de programmation se trouvent toutes dans l’assembly « Microsoft.Analytics.LocalRun ».

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Classe de paramètre de configuration de compilation

**Constructeur**

public Configuration(string rootPath)

|Paramètre|Type|Description|
|---------|----|-----------|
|rootPath|System.String|Chemin au répertoire actuel du contexte de travail. Si WorkingDirectory n’est pas défini, le répertoire de travail par défaut sera rootPath + « ScopeWorkDir ».|

**Propriétés**

|Nom|Description|
|----|-----------|
|CppSDK|Emplacement du SDK Cpp, utilise par défaut la configuration système |
|DataDirectory|Emplacement où les données d’entrée/sortie des tables et des assemblys sont enregistrées, par défaut ScopeWorkDir\DataDir |
|GenerateUdoRedirect|Si vous souhaitez générer une configuration de remplacement de redirection de chargement d’assembly|
|WorkingDirectory|Répertoire de travail du compilateur, ScopeWorkDir par défaut s'il n'est pas défini|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
Classe du compilateur local U-SQL

**Constructeur**

public LocalCompiler(Configuration configuration)

|Paramètre|Type|Description|
|---------|----|-----------|
|configuration|Microsoft.Analytics.LocalRun.Configuration||

**Méthode**

public bool Compile( string script, string filePath, bool shallow, out CommonCompileResult result )

|Paramètre|Type|Description|
|---------|----|-----------|
|script|System.String|chaîne du script d’entrée|
|filePath|System.String|chemin du fichier de script, choisir une valeur null pour utiliser le chemin par défaut|
|shallow|System.Boolean|compilation superficielle (vérification de la syntaxe uniquement) ou compilation complète|
|result|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|Résultats détaillés de la compilation|
|Valeur de retour|System.Boolean|True : aucune erreur grave dans la compilation. <br>False : erreur grave dans la compilation|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
Classe de l’exécuteur local U-SQL

**Constructeur**

public LocalRunner( string algebraFilePath, string dataRoot, Action<string> postMessage = null )

|Paramètre|Type|Description|
|---------|----|-----------|
|algebraFilePath|System.String|Chemin vers le fichier algèbre|
|dataRoot|System.String|Chemin vers DataRoot|
|postMessage (facultatif)|System.Action<String>|gestionnaire de journalisation pour la progression|

public LocalRunner( string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null )

|Paramètre|Type|Description|
|---------|----|-----------|
|algebraFilePath|System.String|Chemin vers le fichier algèbre|
|dataRoot|System.String|Chemin vers DataRoot|
|cachePath|System.String|Chemin au répertoire du résultat de la compilation, choisir la valeur null pour utiliser le chemin par défaut où se trouve le fichier algèbre|
|runtimePath|System.String|Chemin au répertoire des clichés instantanés d’exécution, choisir la valeur null pour utiliser par défaut le répertoire parent de cachePath|
|tempPath|System.String|Chemin du stockage temporaire, à usage interne uniquement, choisir la valeur null|
|logPath|System.String|Chemin où seront consignés les journaux d’exécution, choisir la valeur null pour utiliser par défaut cachePath|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|exécution état changer événement notification gestionnaire|
|eventContext|System. Object|contexte du gestionnaire d’événements|
|postMessage (facultatif)|System.Action<String>|gestionnaire de journalisation pour la progression|

**Propriétés**

|Nom|Description|
|----|-----------|
|AlgebraPath|Chemin au fichier algèbre|
|CachePath|Chemin du cache des résultats du compilateur où figurent les fichiers binaires générés|
|CompletedSteps|Nombre d'étapes effectuées|
|DataRoot|DataRoot des métadonnées|
|LastErrorMessage|(hérité de ExecutionStatusBase.)|
|LogPath|Emplacement de stockage des fichiers. Setter créera le répertoire si le chemin d’accès du journal n'existe pas. Les chemins créés précédemment ne seront pas effacés|
|OutputHeader|En-tête de schéma de vidage dans les sorties textuelles|
|Parallélisme|Parallélisme, valeur par défaut des processeurs logiques - 1. Toute modification de cette valeur entraînera une exception|
|Progression|Progression de l’exécution sur une échelle de 0 à 100 % |
|RuntimePath|Emplacement des fichiers exécutables, doit être un répertoire au-dessus de CachePath lorsqu’il s'agit d'un cliché instantané créé par le compilateur|
|État|État d'exécution <br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized, // initialiser <br>Running,     // en cours d'exécution,  WaitOne vérifie uniquement l'événement dans cet état <br>Success,     // terminé avec succès <br>Error,       // échec <br>}|
|TotalSteps|Nombre total d’étapes à exécuter, valeurs valides disponibles uniquement après avoir généré le vertex DAG|
|Détaillé|Détails de l'exécution|

**Méthode**

|Méthode|Description|
|------|-----------|
|Cancel()|Annuler l’algèbre en cours d’exécution <br>Valeur de retour <br>Type : booléen <br>false : annulation impossible en raison d’une erreur, consultez LastErrorMessage pour plus de détails|
|Start()|Démarrer l'exécution de l’algèbre <br>Valeur de retour <br>Type : booléen <br>false : démarrage impossible en raison d’une erreur, consultez LastErrorMessage pour plus de détails|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|Attendre la fin, consultez WaitHandle.WaitOne|
|Dispose()||


## <a name="see-also"></a>Voir aussi

* Pour obtenir une vue d’ensemble de Data Lake Analytics, consultez [Présentation d’Azure Data Lake Analytics](data-lake-analytics-overview.md).
* Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
* Pour consigner les informations de diagnostic, consultez [Accès aux journaux de diagnostic d’Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour afficher les détails d’un travail, consultez [Utilisation de l’Explorateur de travaux et de la Vue des travaux pour les travaux Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
* Pour afficher la vue d’exécution du vertex, consultez [Utilisation de la vue d’exécution du vertex dans Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)


<!--HONumber=Nov16_HO4-->


