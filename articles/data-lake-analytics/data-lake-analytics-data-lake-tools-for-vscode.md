---
title: "Utilisation d’Azure Data Lake Tools pour Visual Studio Code | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code pour créer, tester et exécuter des scripts U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
translationtype: Human Translation
ms.sourcegitcommit: e79513590bb37570764f398e716182a11c74612a
ms.openlocfilehash: 59cc35bc740625ed0582c1557fac9a04bf0cb8bc

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Utilisation d’Azure Data Lake Tools pour Visual Studio Code

Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code (VSCode) pour créer, tester et exécuter des scripts U-SQL.  Ces informations sont également décrites dans la vidéo suivante :

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Composants requis

Azure Data Lake Tools peut être installé sur les plateformes prises en charge par VSCode qui incluent Windows, Linux et MacOS. Vous pouvez rechercher les conditions préalables pour les différentes plateformes

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Java SE Runtime Environment version 8 mise à jour 77 ou ultérieure](https://java.com/download/manual.jsp). Vous devez ajouter le chemin d’accès java.exe à la variable d’environnement système Path.  Pour obtenir des instructions, consultez [Comment définir ou modifier la variable système Path ?]( https://www.java.com/download/help/path.xml) Le chemin d’accès est semblable à C:\Program Files\Java\jdk1.8.0_77\jre\bin
    - [Kit de développement logiciel (SDK) .NET Core 1.0.1-version préliminaire 2 ou runtime .NET Core 1.0.1]( https://www.microsoft.com/net/download).
    
- Linux (nous recommandons Ubuntu 14.04 LTS)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx). Utilisez la commande suivante pour installer :

        sudo dpkg -i code_<numéro_version>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/). 

        - Mettez à jour la source du package deb en exécutant les commandes suivantes :

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - Installez mono en exécutant la commande :

                sudo apt-get install mono-complete

            > [!NOTE] 
            > Mono 4.6 n’est pas pris en charge.  Vous devez désinstaller entièrement la version 4.6 avant d’installer la version 4.2.x.  

        - [Java SE Runtime Environment version 8 mise à jour 77 ou ultérieure](https://java.com/download/manual.jsp). Les instructions sont disponibles [ici]( https://java.com/en/download/help/linux_x64_install.xml).
        - [Kit de développement logiciel (SDK) .NET Core 1.0.1-version préliminaire 2 ou runtime .NET Core 1.0.1]( https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Java SE Runtime Environment version 8 mise à jour 77 ou ultérieure](https://java.com/download/manual.jsp). Les instructions sont disponibles [ici](https://java.com/en/download/help/mac_install.xml).
    - [Kit de développement logiciel (SDK) .NET Core 1.0.1-version préliminaire 2 ou runtime .NET Core 1.0.1]( https://www.microsoft.com/net/download).

## <a name="install-the-data-lake-tools"></a>Installation de Data Lake Tools

Après avoir installé les composants requis, vous pouvez installer Data Lake Tools pour VSCode.

**Pour installer Data Lake Tools**

1. Ouvrez **Visual Studio Code**.
2. Appuyez sur **CTRL+P**, puis entrez :

        ext install usql-vscode-ext
    Une liste des extensions de code Visual Studio s’affiche. L’une d’elles est **Azure Data Lake Tools (version préliminaire)**.
3. Cliquez sur **Installer** en regard de **Azure Data Lake Tools (version préliminaire)**. Après quelques secondes, le bouton Installer est remplacé par le bouton Recharger.
4. Cliquez sur **Recharger** pour activer l’extension.
5. Cliquez sur **OK** pour confirmer. Azure Data Lake Tools s’affiche dans le panneau Extensions.

    ![Installation de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)


## <a name="connect-to-azure"></a>Connexion à Azure

Avant de pouvoir compiler et exécuter des scripts U-SQL, vous devez vous connecter à votre compte Azure.

**Pour vous connecter à Azure**

1.  Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**. 
2.  Entrez **ADL:Login**.

    ![Palette de commandes de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

2.  Suivez les instructions pour vous connecter à partir de la page web. Une fois connecté, le nom de votre compte s’affiche dans la barre d’état en bas de la fenêtre.

> [!NOTE] 
> Si l’authentification à deux facteurs est activée sur votre compte, nous vous recommandons d’utiliser l’authentification par téléphone au lieu du code confidentiel.

Pour vous déconnecter, utilisez la commande **ADL:Logout**

## <a name="list-data-lake-analytics-accounts"></a>Énumérer les comptes Data Lake Analytics

Pour tester la connexion, vous pouvez répertorier vos comptes Data Lake Analytics :

**Pour répertorier les comptes Data Lake Analytics dans votre abonnement Azure**

1. Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**.
2. Entrez **ADL:List Accounts**.  Les comptes s’affichent dans le panneau **Sortie**.

## <a name="work-with-u-sql"></a>Utilisation de U-SQL

Vous devez ouvrir un dossier ou un fichier U-SQL pour utiliser U-SQL.

**Pour ouvrir un dossier pour votre projet U-SQL**

1. À partir de Visual Studio Code, cliquez sur le menu **Fichier**, puis sur **Ouvrir un dossier**.
2. Spécifiez un dossier, puis cliquez sur **Sélectionner le dossier**.
3. Cliquez sur le menu **Fichier**, puis sur **Nouveau**. Un fichier **Untilted-1** est ajouté au projet.
4. Copiez et collez le code suivant dans le fichier Untitled-1 :

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO “/Output/departments.csv”

    Le script crée un fichier departments.csv avec des données dans le dossier /output.

5. Enregistrez le fichier sous **myUSQL.usql** dans le dossier ouvert. Remarque : le fichier de configuration **adltools_settings.json** est également ajouté au projet.
4. Ouvrez et configurez le fichier **adltools_settings.json** avec les propriétés suivantes :

    - Compte : un compte Data Lake Analytics dans votre abonnement Azure.
    - Paramètres facultatifs :

        - Priorité : la plage de priorité est comprise entre 1 et 1000. 1 correspond à la priorité la plus élevée. La valeur par défaut est 1000.
        - Parallélisme : la plage de parallélisme est comprise entre 1 et 150. La valeur par défaut est 150. 

        > [!NOTE] 
        > Si les paramètres ne sont pas valides, les valeurs par défaut sont utilisées.

     ![Fichier de configuration de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Un compte Data Lake Analytics de calcul est nécessaire pour la compilation et l’exécution des travaux U-SQL.  Vous devez configurer le compte d’ordinateur avant de compiler et d’exécuter des travaux U-SQL.

Par rapport à l’ouverture d’un fichier, l’ouverture d’un dossier vous permet d’effectuer les opérations suivantes :

- Utiliser un fichier code-behind.  En mode de fichier unique, code-behind n’est pas pris en charge.
- Utiliser le fichier de configuration. Lorsque vous ouvrez un dossier, les scripts dans le dossier de travail partagent un fichier de configuration.


La compilation du script U-SQL est effectuée à distance par le service Data Lake Analytics.  Lorsque vous émettez la commande de compilation, le script U-SQL est envoyé à votre compte Data Lake Analytics. Le résultat de la compilation est ensuite reçu par Visual Studio Code. En raison de la compilation à distance, Visual Studio Code nécessite les informations de connexion à votre compte Data Lake Analytics dans le fichier de configuration.

**Pour compiler un script U-SQL**

1. Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**. 
2. Entrez **ADL: Compile Script**. Les résultats de la compilation s’affichent dans la fenêtre de sortie. Vous pouvez également cliquer avec le bouton droit sur un fichier de script, puis sur **ADL:Compile Script** pour compiler un travail U-SQL. Le résultat de la compilation s’affiche dans le panneau de sortie.
 

**Pour envoyer un script U-SQL**

1. Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**. 
2. Entrez **ADL: Submit Job**.  Vous pouvez également cliquer avec le bouton droit sur un fichier de script, puis sur **ADL:Submit Job** pour envoyer un travail U-SQL. 

Après l’envoi d’un travail U-SQL, les journaux d’envoi s’affichent dans la fenêtre de sortie dans VSCode. Si l’envoi a réussi, l’URL du travail est également affichée. Vous pouvez ouvrir l’URL du travail dans un navigateur web pour suivre l’état du travail en temps réel.

Pour activer la sortie des informations sur le travail : définissez 'jobInformationOutputPath' dans le fichier **vscode for u-sql_settings.json**.
 
## <a name="use-code-behind-file"></a>Utilisation d’un fichier code-behind

Un fichier code-behind est un fichier CSharp associé à un script U-SQL. Vous pouvez définir le script dédié UDO/UDA/UDT/UDF dans le fichier code-behind. UDO/UDA/UDT/UDF peut être utilisé directement dans le script sans inscription préalable de l’assembly. Le fichier code-behind est placé dans le même dossier que le fichier de script U-SQL correspondant. Si le script est nommé xxx.usql, le fichier code-behind est nommé xxx.usql.cs. La suppression manuelle du fichier code-behind désactive la fonctionnalité code-behind pour le script U-SQL associé. Pour plus d’informations sur l’écriture de code client pour le script U-SQL, consultez [Écriture et utilisation de code personnalisé dans U-SQL - Fonctions définies par l’utilisateur]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

Pour la prise en charge de code-behind, vous devez ouvrir un dossier de travail. 

**Pour générer un fichier code-behind**

1. Ouvrez un fichier soucre. 
2. Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**.
3. Entrez **ADL: Generate Code Behind**.  Un fichier code-behind est créé dans le même dossier. 

Vous pouvez également cliquer avec le bouton droit sur un fichier de script, puis sur **ADL:Generate Code Behind** pour générer un fichier code-behind. 

Le fait de compiler et d’envoyer un script U-SQL avec code-behind est identique à la version autonome du script U-SQL.

Les deux captures d’écran suivantes illustrent un fichier code-behind et le fichier de script U-SQL associé :
 
![Data Lake Tools pour Visual Studio Code - code behind](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Data Lake Tools pour Visual Studio Code - code behind](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>Utiliser les assemblys

Pour plus d’informations sur le développement d’assemblys, consultez [Développement d’assemblys U-SQL pour les tâches d’Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

À l’aide de Data Lake Tools, vous pouvez inscrire des assemblys de code personnalisé pour le catalogue Data Lake Analytics.

**Pour inscrire un assembly**

1.  Appuyez sur **CTRL+MAJ+P** pour ouvrir la palette de commandes.
2.  Entrez **ADL:Register Assembly**.
3.  Sélectionnez un compte Data Lake Analytics.
4.  Sélectionnez une base de données.
5.  Spécifiez le chemin d’accès de l’assembly local.

Le code U-SQL suivant montre comment appeler un assembly. Dans l’exemple, le nom de l’assembly est *test*.

    REFERENCE ASSEMBLY [test];
    @a=EXTRACT Iid int,Starts DateTime,Region string,Query string,DwellTime int,Results string,ClickedUrls string 
    FROM @"ruoxin/SearchLog.txt" USING Extractors.Tsv();
    
    @d=SELECT DISTINCT Region FROM @a;
    
    @d1=PROCESS @d
        PRODUCE Region string,
                Mkt string
                USING new USQLApplication_codebehind.MyProcessor();
    
    OUTPUT @d1 TO @"ruoxin/SearchLogtest.txt" USING Outputters.Tsv();



## <a name="access-data-lake-analytics-catalog"></a>Accès au catalogue Data Lake Analytics

Une fois connecté à Azure, vous pouvez utiliser les étapes suivantes pour accéder au catalogue U-SQL :

**Pour accéder aux métadonnées U-SQL**

1.  Appuyez sur **CTRL+MAJ+P**, puis entrez **ADL:List Tables**.
2.  Cliquez sur l’un des comptes Data Lake Analytics.
3.  Cliquez sur l’une des bases de données Data Lake Analytics.
4.  Cliquez sur l’un des schémas. Les tables s’affichent.

## <a name="additional-features"></a>Fonctionnalités supplémentaires

Data Lake Tools pour VSCode prend en charge les fonctionnalités suivantes :

-   Saisie semi-automatique IntelliSense. Les suggestions sont issues d’un mot clé, d’une méthode, des variables, etc. Des icônes différentes représentent des types d’objets différents :

    - Type de données Scala
    - Type de données complexe
    - Types définis par l’utilisateur (UDT) intégrés
    - Classes et Collection .Net
    - Expressions C#
    - UDF, UDO et UDAAG C# intégrés 
    - Fonctions U-SQL
    - Fonction de fenêtrage U-SQL
 
    ![Data Lake Tools pour Visual Studio Code - Types d’objets IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Saisie semi-automatique IntelliSense des métadonnées Data Lake Analytics. Data Lake Tools télécharge en local les informations sur les métadonnées Data Lake Analytics.  La fonctionnalité IntelliSense remplit automatiquement les objets, notamment Base de données, Schéma, Table, Vue, TVF, Procédures, Assemblys C#, à partir des métadonnées Data Lake Analytics.
 
    ![Data Lake Tools pour Visual Studio Code - Métadonnées IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Marqueur d’erreurs IntelliSense. Data Lake Tools souligne les erreurs d’édition pour U-SQL et C#. 
-   Points clés de la syntaxe. Data Lake Tools utilise des couleurs différentes pour différencier les variables, les mots clés, le type de données, les fonctions, etc. 

    ![Data Lake Tools pour Visual Studio Code - Points clés de la syntaxe](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

##<a name="next-steps"></a>Étapes suivantes :

- Consultez [Didacticiel : bien démarrer avec Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md) pour obtenir des informations sur la prise en main de Data Lake Analytics.
- Consultez [Didacticiel : développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) pour obtenir des informations sur l’utilisation de Data Lake Tools pour Visual Studio.
- Pour plus d’informations sur le développement d’assemblys, consultez [Développement d’assemblys U-SQL pour les tâches d’Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).






<!--HONumber=Dec16_HO1-->


