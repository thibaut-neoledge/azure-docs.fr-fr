---
title: "Tester et déboguer des travaux U-SQL à l’aide d’une exécution locale et du Kit de développement logiciel (SDK) Azure Data Lake U-SQL | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio et le Kit de développement logiciel (SDK) Azure Data Lake U-SQL pour tester et déboguer des travaux U-SQL sur votre station de travail locale."
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
ms.sourcegitcommit: c43fb7b513136bb3ea964419d68f58585bba48fd
ms.openlocfilehash: 348971e07169ae9c155753c7943124e96e6e9597


---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Tester et déboguer des travaux U-SQL à l’aide d’une exécution locale et du Kit de développement logiciel (SDK) Azure Data Lake U-SQL

Vous pouvez utiliser Azure Data Lake Tools pour Visual Studio et le Kit de développement logiciel (SDK) U-SQL pour exécuter des travaux U-SQL sur votre station de travail, comme vous pouvez le faire dans le service Azure Data Lake. Ces deux fonctionnalités d’exécution locale accélèrent les procédures de test et de débogage de vos travaux U-SQL.

Configuration requise :

- Un compte Azure Data Lake Analytics. Voir [Prise en main d'Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Azure Data Lake Tools pour Visual Studio. Voir [Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- L’expérience de développement de script U-SQL. Voir [Prise en main d'Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).


## <a name="understand-the-data-root-folder-and-the-file-path"></a>Comprendre le dossier racine de données et le chemin d’accès

L’exécution locale et le Kit de développement logiciel (SDK) U-SQL nécessitent un dossier racine de données. Ce dossier est un « magasin local » pour le compte de calcul local. Il est équivalent au compte Azure Data Lake Store d’un compte Data Lake Analytics dans Azure. Basculer vers un dossier racine de données différent revient à basculer vers un autre compte de magasin. Si vous souhaitez accéder aux données généralement partagées avec des dossiers racines de données différents, vous devez utiliser des chemins d’accès absolus dans vos scripts. Vous pouvez aussi créer des liens symboliques de système de fichiers (par exemple, **mklink** sur NTFS) sous le dossier racine de données, afin de pointer vers les données partagées.

Le dossier racine de données est utilisé pour :

- Stocker les métadonnées, notamment des bases de données, des tables, des fonctions table (TVF) et des assemblys.
- Rechercher les chemins d’entrée et de sortie qui sont définis comme des chemins relatifs dans U-SQL. Les chemins relatifs facilitent le déploiement de vos projets U-SQL dans Azure.

Vous pouvez utiliser un chemin d’accès relatif et un chemin d’accès absolu local dans des scripts SQL-U. Le chemin d’accès relatif dépend du chemin d’accès au dossier racine de données spécifié. Il est recommandé d’utiliser « / » en tant que séparateur de chemin afin de rendre vos scripts compatibles avec le côté serveur. Voici quelques exemples de chemins relatifs, avec leurs chemins absolus équivalents. Dans ces exemples, le chemin C:\LocalRunDataRoot correspond à la racine de données.

|Chemin relatif|Chemin absolu|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Utiliser l’exécution locale depuis Visual Studio

Data Lake Tools pour Visual Studio fournit une expérience d’exécution locale U-SQL dans Visual Studio. À l’aide de cette fonctionnalité, vous pouvez :

- Exécuter des scripts U-SQL en local, ainsi que des assemblys C#.
- Déboguer un assembly C# en local.
- Créer, afficher et supprimer des catalogues U-SQL (bases de données locales, assemblys, schémas et tables) de l’Explorateur de serveurs. Le catalogue local est également accessible depuis l’Explorateur de serveurs.

    ![Data Lake Tools pour Visual Studio exécution locale catalogue local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Le programme d’installation de Data Lake Tools crée un dossier C:\LocalRunRoot, qui est utilisé en tant que dossier racine de données par défaut. Le parallélisme d’exécution locale par défaut est 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Pour configurer l’exécution locale dans Visual Studio

1. Ouvrez Visual Studio.
2. Ouvrez l’**Explorateur de serveurs**.
3. Développez **Azure** > **Data Lake Analytics**.
4. Cliquez sur le menu **Data Lake**, puis sur **Options et paramètres**.
5. Dans l’arborescence de gauche, développez **Azure Data Lake**, puis **Général**.

    ![Data Lake Tools pour Visual Studio exécution locale configurer paramètres](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Un projet U-SQL Visual Studio est requis pour exécuter l’exécution locale. Cette partie diffère de l'exécution de scripts U-SQL à partir d'Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Pour exécuter un script U-SQL localement
1. Dans Visual Studio, ouvrez votre projet U-SQL.   
2. Cliquez avec le bouton droit sur un script U-SQL dans l'Explorateur de solutions, puis cliquez sur **Soumettre le script**.
3. Sélectionnez **(Local)** en tant que compte Analytics pour exécuter votre script en local.
Vous pouvez également cliquer sur le compte **(Local)** en haut de la fenêtre de script, puis sur **Envoyer** (ou utiliser le raccourci clavier Ctrl + F5).

    ![Data Lake Tools pour Visual Studio exécution locale soumettre travaux](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Utiliser une exécution locale à partir du Kit de développement logiciel (SDK) U-SQL Data Lake

Outre l’exécution locale de scripts U-SQL à l’aide de Visual Studio, vous pouvez également utiliser le Kit de développement logiciel (SDK) U-SQL Azure Data Lake pour exécuter les scripts U-SQL en local, avec la ligne de commande et les interfaces de programmation. Par ce biais, vous pouvez mettre votre test local U-SQL à l’échelle.

### <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Le Kit de développement logiciel (SDK) U-SQL Data Lake requiert les dépendances suivantes :

- [Microsoft .NET Framework 4.6 ou une version ultérieure](https://www.microsoft.com/en-us/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 et Windows SDK 10.0.10240.0 ou une version ultérieure. Vous pouvez obtenir cet élément de deux manières :

    - Installez [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Vous devez disposer d’un sous-dossier \Windows Kits\10 dans le dossier Program Files, par exemple C:\Program Files (x86)\Windows Kits\10\.. Vous trouverez également la version du Kit de développement logiciel (SDK) Windows 10 sous \Windows Kits\10\Lib. Si vous ne voyez pas ces dossiers, réinstallez Visual Studio et veillez à sélectionner le Kit de développement logiciel (SDK) Windows 10 lors de l’installation. Le script de compilation local U-SQL trouvera automatiquement ces dépendances.

    ![Data Lake Tools pour Visual Studio exécution locale Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Installez [Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs). Vous trouverez les fichiers Visual C++ et le Kit de développement logiciel (SDK) Windows préconfigurés sous C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Dans ce cas, le compilateur U-SQL local ne trouve pas ces dépendances automatiquement. Vous devez spécifier le chemin d’accès CppSDK pour lui. Vous pouvez copier les fichiers vers un autre emplacement ou les utiliser tels quels. Vous pouvez ensuite choisir de définir une variable d’environnement **SCOPE_CPP_SDK** vers le répertoire, ou spécifier l’argument **-CppSDK** avec ce répertoire sur la ligne de commande de l’application d’assistance d’exécution locale.

Après avoir installé le Kit de développement logiciel (SDK), vous devez effectuer les étapes de configuration suivantes :

- Définissez la variable d’environnement **SCOPE_CPP_SDK**.

    Si vous obtenez Microsoft Visual C++ et le Kit de développement logiciel (SDK) Windows en installant Data Lake Tools pour Visual Studio, vérifiez que vous disposez du dossier suivant :

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Définissez une nouvelle variable d’environnement appelée **SCOPE_CPP_SDK**, qui pointe vers ce répertoire. Vous pouvez aussi copier le dossier vers un autre emplacement et spécifiez **SCOPE_CPP_SDK**.

    Outre la définition de la variable d’environnement, vous pouvez également spécifier l’argument **-CppSDK** lorsque vous utilisez la ligne de commande. Cet argument remplace votre variable d’environnement CppSDK par défaut.

- Définissez la variable d’environnement **LOCALRUN_DATAROOT**.

    Définissez une variable d’environnement appelée **LOCALRUN_DATAROOT**, qui pointe vers la racine de données.

    Outre la définition de la variable d’environnement, vous pouvez spécifier l’argument **-DataRoot** avec le chemin vers la racine de données lorsque vous utilisez une ligne de commande. Cet argument remplace votre variable d’environnement de racine de données par défaut. Vous devez ajouter cet argument à chaque ligne de commande que vous exécutez, afin de pouvoir remplacer la variable d’environnement de racine de données par défaut, pour toutes les opérations.

### <a name="use-the-sdk-from-the-command-line"></a>Utiliser le Kit de développement logiciel (SDK) depuis la ligne de commande

#### <a name="command-line-interface-of-the-helper-application"></a>Interface de ligne de commande de l’application d’assistance

Dans le Kit de développement logiciel (SDK), le fichier LocalRunHelper.exe correspond à l’application d’assistance de ligne de commande qui fournit des interfaces pour la plupart des fonctions d’exécution locale couramment utilisées. Notez que la commande et les commutateurs d’arguments respectent la casse. Pour l’appeler :

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Exécutez le fichier LocalRunHelper.exe sans argument ou avec le commutateur **help** pour afficher les informations d’aide :

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

-  Le paramètre **Commande** fournit le nom de la commande.  
-  Le paramètre **Argument obligatoire** répertorie les arguments qui doivent être fournis.  
-  Le paramètre **Argument facultatif** répertorie les arguments facultatifs, avec des valeurs par défaut.  Les arguments booléens facultatifs n’incluent aucun paramètre, et leur valeur est négative par défaut.

#### <a name="return-value-and-logging"></a>Valeur de retour et journalisation

L’application d’assistance renvoie la valeur **0** (réussite) et **-1** (échec). Par défaut, l’assistant envoie tous les messages vers la console active. Toutefois, la plupart des commandes prennent en charge l’argument facultatif **-MessageOut path_to_log_file** qui redirige les sorties vers un fichier journal.


### <a name="sdk-usage-samples"></a>Exemples d’utilisation du Kit de développement logiciel (SDK)

#### <a name="compile-and-run"></a>Compilation et exécution

La commande **run** sert à compiler le script, puis à exécuter les résultats compilés. Ses arguments de ligne de commande sont une combinaison des commandes **compile** et **run**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Voici un exemple :

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

En plus de combiner **compile** et **run**, vous pouvez compiler et exécuter séparément les fichiers exécutables compilés.

#### <a name="compile-a-u-sql-script"></a>Compiler un script U-SQL

La commande **compile** permet de compiler un script U-SQL vers des exécutables.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Voici des arguments facultatifs pour la compilation :

|Argument|Description|
|--------|-----------|
|-CppSDK [valeur par défaut '']|Répertoire CppSDK.|
|-DataRoot [valeur par défaut '']|Racine de données pour les données et les métadonnées. La valeur par défaut correspond à la variable d’environnement **LOCALRUN_DATAROOT**.|
|-MessageOut [valeur par défaut '']|Videz les messages de la console dans un fichier.|
|-Shallow [valeur par défaut 'False']|Compilation superficielle. Vérifie uniquement la syntaxe du script et du retour.|
|-WorkDir [valeur par défaut 'D:\localrun\t\ScopeWorkDir']|Répertoire des sorties et de l’utilisation du compilateur. Pour en savoir plus, consultez la section « Répertoire de travail » de l’annexe.|

Voici les arguments facultatifs pour les assemblys et le code-behind :

|Argument|Description|
|--------|-----------|
|-CodeBehind [valeur par défaut 'False']|Indique que le script comporte un code-behind au format .cs, qui sera compilé et enregistré automatiquement en tant qu’objet défini par l’utilisateur (UDO).|
|-References [valeur par défaut '']|Liste des chemins d’accès à des assemblys de référence supplémentaires ou des fichiers de données de code-behind, séparés par « ; ».|
|-UseDatabase [valeur par défaut 'master']|Base de données à utiliser pour l’inscription temporaire des assemblys et du code-behind.|
|-UdoRedirect [valeur par défaut 'False']|Une configuration de redirection d’assembly UDO qui indique au runtime .Net de sonder d’abord les assemblys dépendants du répertoire de sortie compilé lorsqu’UDO est appelé.|

Voici quelques exemples d’utilisation.

Compilez un script U-SQL :

    LocalRunHelper compile -Script d:\test\test1.usql

Compilez un script SQL-U et définissez le dossier racine de données. Notez que cette opération remplace la variable d’environnement définie.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compilez un script U-SQL et définissez un répertoire de travail, un assembly de référence et une base de données :

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Exécuter les résultats compilés

La commande **execute** est utilisée pour exécuter les résultats compilés.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Voici des arguments facultatifs :

|Argument|Description|
|--------|-----------|
|-DataRoot [valeur par défaut '']|Racine de données pour l’exécution des métadonnées. La valeur par défaut correspond à la variable d’environnement **LOCALRUN_DATAROOT**.|
|-MessageOut [valeur par défaut '']|Videz les messages de la console dans un fichier.|
|-Parallel [valeur par défaut '1']|Indicateur de lancement des étapes d’exécution locale générées avec le niveau de parallélisme spécifié.|
|-Verbose [valeur par défaut 'False']|Indicateur affichant les sorties détaillées du runtime.|

Voici un exemple d’utilisation :

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="use-the-sdk-with-programming-interfaces"></a>Utiliser le Kit de développement logiciel (SDK) avec des interfaces de programmation

Les interfaces de programmation se trouvent toutes dans l’assembly Microsoft.Analytics.LocalRun. Vous pouvez les utiliser pour intégrer les fonctionnalités du Kit de développement logiciel (SDK) U-SQL, et l’infrastructure de test C# pour mettre à l’échelle votre test local du script SQL-U. Pour en savoir plus sur les interfaces, consultez l’annexe.

## <a name="appendix"></a>Annexe

### <a name="working-directory"></a>Répertoire de travail

Lors d’une exécution locale du script U-SQL, un répertoire de travail est créé pendant la compilation. Outre les sorties de compilation, les fichiers exécutables nécessaires à l’exécution locale seront copiés sous forme de clichés instantanés dans ce répertoire de travail. Si l’argument **-WorkDir** n’est pas saisi dans la ligne de commande, le répertoire de travail par défaut ScopeWorkDir sera créé dans le répertoire actuel. Les fichiers du répertoire de travail sont affichés comme décrit ci-dessous :

|Répertoire/fichier|Définition|Description|
|--------------|----------|-----------|
|ScopeWorkDir|Répertoire de travail|Dossier racine|
|C6A101DDCB470506|Chaîne de hachage de la version exécutable|Cliché instantané des fichiers exécutables nécessaires à l'exécution locale|
|\.\Script_66AE4909AA0ED06C|Nom de script + chaîne de hachage du chemin d’accès au script|Résultats de la compilation et journalisation des étapes d'exécution|
|\.\.\\_\_script\_\_.abr|Sortie du compilateur|Fichier algèbre|
|\.\.\\_\_ScopeCodeGen\_\_.*|Sortie du compilateur|Code géré généré|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|Sortie du compilateur|Code natif généré|
|\.\.\referenced_assemblies|Référence d’assembly|Fichiers d’assembly de référence|
|\.\.\deployed_resources|Déploiement de ressources|Fichiers de déploiement de ressources|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|Journal d’exécution|Journal des étapes d'exécution|

### <a name="programming-interfaces-for-the-azure-data-lake-u-sql-sdk"></a>Interfaces de programmation pour le Kit de développement logiciel (SDK) U-SQL Azure Data Lake

Les interfaces de programmation se trouvent toutes dans l’assembly Microsoft.Analytics.LocalRun.

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Classe de paramètres de configuration de la compilation.

**Constructeur**

public Configuration(string rootPath)

|Paramètre|Type|Description|
|---------|----|-----------|
|rootPath|System.String|Chemin d’accès au répertoire actuel du contexte de travail. Si l’élément WorkingDirectory n’est pas défini, le répertoire de travail par défaut sera rootPath + ScopeWorkDir.|

**Propriétés**

|Nom|Description|
|----|-----------|
|CppSDK|Emplacement de l’élément CppSDK, s’il ne s’agit pas de la configuration système par défaut. |
|DataDirectory|Emplacement d’enregistrement des tables, des assemblys et des données d’entrée/sortie. La valeur par défaut est ScopeWorkDir\DataDir. |
|GenerateUdoRedirect|Indique si l’utilisateur souhaite générer une configuration de remplacement de redirection d’un chargement d’assembly.|
|WorkingDirectory|Répertoire de travail du compilateur. La valeur par défaut est ScopeWorkDir.|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
Classe du compilateur local U-SQL.

**Constructeur**

public LocalCompiler(Configuration configuration)

|Paramètre|Type|Description|
|---------|----|-----------|
|configuration|Microsoft.Analytics.LocalRun.Configuration||

**Méthode**

public bool Compile( string script, string filePath, bool shallow, out CommonCompileResult result )

|Paramètre|Type|Description|
|---------|----|-----------|
|script|System.String|Chaîne du script d’entrée.|
|filePath|System.String|Chemin d’accès au fichier de script. Il est défini sur null, afin d’utiliser la valeur par défaut.|
|shallow|System.Boolean|Compilation superficielle (vérification de la syntaxe uniquement) ou compilation complète.|
|result|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|Résultats détaillés de la compilation.|
|Valeur de retour|System.Boolean|True : aucune erreur grave ne figure dans la compilation. <br>False : erreur grave dans la compilation.|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
Il s’agit de la classe de l’exécuteur local U-SQL.

**Constructeur**

public LocalRunner( string algebraFilePath, string dataRoot, Action<string> postMessage = null )

|Paramètre|Type|Description|
|---------|----|-----------|
|algebraFilePath|System.String|Chemin au fichier algèbre|
|dataRoot|System.String|Chemin vers DataRoot|
|postMessage (facultatif)|System.Action<String>|Gestionnaire de journalisation pour la progression|

public LocalRunner( string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null )

|Paramètre|Type|Description|
|---------|----|-----------|
|algebraFilePath|System.String|Chemin d’accès au fichier algèbre.|
|dataRoot|System.String|Chemin d’accès à DataRoot.|
|cachePath|System.String|Chemin d’accès au répertoire du résultat de la compilation. Il est défini sur le chemin par défaut, auquel le fichier algèbre se trouve.|
|runtimePath|System.String|Chemin d’accès au répertoire du cliché instantané du runtime. Il est défini sur null, c’est-à-dire le chemin par défaut, auquel le répertoire parent de cachePath se trouve.|
|tempPath|System.String|Chemin du stockage temporaire, à usage interne uniquement. Il est défini sur null.|
|logPath|System.String|Chemin d’écriture des fichiers journaux d’exécution. Il est défini sur null, afin d’utiliser cachePath en tant que valeur par défaut.|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|Gestionnaire de notification des événements associé aux changements d’état de l’exécution.|
|eventContext|System. Object|Contexte du gestionnaire d’événements.|
|postMessage (facultatif)|System.Action<String>|Gestionnaire de journalisation pour la progression.|

**Propriétés**

|Nom|Description|
|----|-----------|
|AlgebraPath|Chemin d’accès au fichier algèbre.|
|CachePath|Chemin d’accès au cache des résultats du compilateur, auquel se trouvent les fichiers binaires générés.|
|CompletedSteps|Nombre d’étapes effectuées.|
|DataRoot|Élément DataRoot des métadonnées.|
|LastErrorMessage|(hérité de ExecutionStatusBase.)|
|LogPath|Emplacement de stockage du fichier journal. La méthode Setter crée le répertoire, le cas échéant. Le chemin d’accès au journal créé précédemment n’est pas nettoyé.|
|OutputHeader|En-tête du schéma de vidage dans les sorties textuelles.|
|Parallélisme|Parallélisme. Correspond par défaut à des processeurs logiques : 1. Si vous modifiez cet élément après le démarrage, une exception est générée.|
|Progression|Progression de l’exécution, sur une échelle de 0 à 100 %.|
|RuntimePath|Emplacement des fichiers runtime. Il doit correspondre à un répertoire au-dessus de CachePath lorsqu’il s’agit d’un cliché instantané créé par le compilateur.|
|État|État de l’exécution. <br><br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized, // Initialisé. <br>Running,     // En cours d’exécution.  WaitOne vérifie uniquement l’événement présentant cet état. <br>Success,     // Exécution terminée correctement. <br>Error,       // Échec. <br>}|
|TotalSteps|Nombre total d’étapes à exécuter. Une valeur valable n’est disponible qu’une fois le DAG de vertex généré.|
|Détaillé|Détails de l’exécution.|

**Méthode**

|Méthode|Description|
|------|-----------|
|Cancel()|Annule l’algèbre en cours d’exécution. <br><br>Le type de valeur de retour est la valeur booléenne. <br><br>Valeur de retour de false : annulation impossible en raison d’une erreur ; consultez LastErrorMessage pour en savoir plus.|
|Start()|À démarrer pour lancer l’algèbre. <br><br>Le type de valeur de retour est la valeur booléenne. <br><br>Valeur de retour de false : démarrage impossible en raison d’une erreur ; consultez LastErrorMessage pour en savoir plus.|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|Attend la fin du travail. Voir WaitHandle.WaitOne.|
|Dispose()||


## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une vue d'ensemble de Data Lake Analytics, consultez [Vue d'ensemble de Data Lake Analytics Azure](data-lake-analytics-overview.md).
* Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
* Pour consigner les informations de diagnostic, voir [Accès aux journaux de diagnostic d’Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pour consulter une requête plus complexe, voir [Analyse de journaux des sites web à l’aide d’Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour afficher les détails d’un travail, voir [Utilisation de l’Explorateur de travaux et de la Vue des travaux pour les travaux Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Pour utiliser la vue d’exécution du vertex, voir [Utilisation de la vue d’exécution du vertex dans Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).



<!--HONumber=Dec16_HO1-->


