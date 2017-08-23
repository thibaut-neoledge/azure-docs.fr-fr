---
title: "Azure Data Lake Tools - Utilisation d’Azure Data Lake Tools pour Visual Studio Code | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code pour créer, tester et exécuter des scripts U-SQL. "
Keywords: "VScode,Azure Data Lake Tools,exécution locale,débogage local,Débogage local,aperçu du fichier de stockage,charger vers le chemin de stockage"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 0ed3d7a0057eb446b3e1d16019ac74c641ae3138
ms.contentlocale: fr-fr
ms.lasthandoff: 07/17/2017

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Utilisation d’Azure Data Lake Tools pour Visual Studio Code

Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code (VSCode) pour créer, tester et exécuter des scripts U-SQL.  Ces informations sont également décrites dans la vidéo suivante :

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Composants requis

Azure Data Lake Tools peut être installé sur les plateformes prises en charge par VSCode qui incluent Windows, Linux et MacOS. Vous pouvez rechercher les conditions préalables pour les différentes plateformes

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Java SE Runtime Environment version 8 mise à jour 77 ou ultérieure](https://java.com/download/manual.jsp). Vous devez ajouter le chemin d’accès java.exe à la variable d’environnement système Path.  Pour obtenir des instructions, consultez [Comment définir ou modifier la variable système Path ?]( https://www.java.com/download/help/path.xml) Le chemin d’accès est semblable à C:\Program Files\Java\jdk1.8.0_77\jre\bin
    - [Kit SDK .NET Core 1.0.3 ou runtime .NET Core 1.1](https://www.microsoft.com/net/download).
    
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
        - [Kit SDK .NET Core 1.0.3 ou runtime .NET Core 1.1](https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Java SE Runtime Environment version 8 mise à jour 77 ou ultérieure](https://java.com/download/manual.jsp). Les instructions sont disponibles [ici](https://java.com/en/download/help/mac_install.xml).
    - [Kit SDK .NET Core 1.0.3 ou runtime .NET Core 1.1](https://www.microsoft.com/net/download).

## <a name="install-the-data-lake-tools"></a>Installation de Data Lake Tools

Après avoir installé les composants requis, vous pouvez installer Data Lake Tools pour VSCode.

**Pour installer Data Lake Tools**

1. Ouvrez **Visual Studio Code**.
2. Appuyez sur **CTRL+P**, puis entrez :
```
ext install usql-vscode-ext
```
Une liste des extensions de code Visual Studio s’affiche. L’une d’elles est **Azure Data Lake Tools**.

3. Cliquez sur **Installer** en regard de **Azure Data Lake Tools**. Après quelques secondes, le bouton Installer est remplacé par le bouton Recharger.
4. Cliquez sur **Recharger** pour activer l’extension.
5. Cliquez sur **OK** pour confirmer. Azure Data Lake Tools s’affiche dans le panneau Extensions.
    ![Installation de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>Activer Azure Data Lake Tools
Créez un fichier .USQL ou ouvrez un fichier .USQL existant pour activer l’extension. 

## <a name="connect-to-azure"></a>Connexion à Azure

Avant de pouvoir compiler et exécuter des scripts U-SQL dans Azure Data Lake Analytics, vous devez vous connecter à votre compte Azure.

**Pour vous connecter à Azure**

1.  Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**. 
2.  Entrez **ADL: Login**. Les informations de connexion s’affichent dans le volet de sortie.

    ![Palette de commandes de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Informations de connexion Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. En maintenant la touche CTRL enfoncée, cliquez sur l’URL de connexion : https://aka.ms/devicelogin pour ouvrir la page web de connexion. Copiez et collez le code G567LX42V dans la zone de texte ci-dessous, cliquez sur Continuer.

   ![Data Lake Tools pour Visual Studio Code - Coller le code de connexion](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Suivez les instructions pour vous connecter à partir de la page web. Une fois connecté, le nom de votre compte Azure s’affiche dans la barre d’état dans l’angle inférieur gauche de la fenêtre VSCode. 

    > [!NOTE] 
    > Si l’authentification à deux facteurs est activée sur votre compte, nous vous recommandons d’utiliser l’authentification par téléphone au lieu du code confidentiel.

Pour vous déconnecter, utilisez la commande **ADL: Logout**

## <a name="list-data-lake-analytics-accounts"></a>Énumérer les comptes Data Lake Analytics

Pour tester la connexion, vous pouvez répertorier vos comptes Data Lake Analytics :

**Pour répertorier les comptes Data Lake Analytics dans votre abonnement Azure**

1. Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**.
2. Tapez **ADL: List Accounts**.  Les comptes s’affichent dans le panneau **Sortie**.

## <a name="open-sample-script"></a>Ouvrir un exemple de script

Utilisez la palette de commandes (**Ctrl+Maj+P**) et choisissez **ADL: Open Sample Script**. Une autre instance s’ouvre pour cet exemple. Vous pouvez également modifier, configurer, envoyer un script sur cette instance.

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
    - Base de données : une base de données sous votre compte. La valeur par défaut est master.
    - Schéma : un schéma sous votre base de données. La valeur par défaut est dbo.
    - Paramètres facultatifs :
        - Priorité : la plage de priorité est comprise entre 1 et 1000. 1 correspond à la priorité la plus élevée. La valeur par défaut est 1000.
        - Parallélisme : la plage de parallélisme est comprise entre 1 et 150. La valeur par défaut est le parallélisme maximal autorisé dans votre compte ADLA. 
        
        > [!NOTE] 
        > Si les paramètres ne sont pas valides, les valeurs par défaut sont utilisées.

    ![Fichier de configuration de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Un compte Data Lake Analytics de calcul est nécessaire pour la compilation et l’exécution des travaux U-SQL.  Vous devez configurer le compte d’ordinateur avant de compiler et d’exécuter des travaux U-SQL.
    
    Une fois la configuration enregistrée, les informations compte|base de données|schéma s’affichent dans la barre d’état dans l’angle inférieur gauche du fichier USQL correspondant. 
 
 

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

1. Ouvrez un fichier source. 
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

Vous pouvez inscrire l’assembly via **ADL: Register Assembly** ou **ADL: Register Assembly through Configuration**.

**ADL: Register Assembly**
1.  Appuyez sur **CTRL+MAJ+P** pour ouvrir la palette de commandes.
2.  Entrez **ADL: Register Assembly**. 
3.  Spécifiez le chemin d’accès de l’assembly local. 
4.  Sélectionnez un compte Data Lake Analytics.
5.  Sélectionnez une base de données.

Résultats : le portail s’ouvre dans le navigateur et affiche le processus d’inscription de l’assembly.  

Une autre méthode pratique pour déclencher la commande **ADL: Register Assembly** consiste à cliquer avec le bouton droit sur le fichier dll dans l’Explorateur. 

**ADL: Register Assembly through Configuration**.
1.  Appuyez sur **CTRL+MAJ+P** pour ouvrir la palette de commandes.
2.  Entrez **ADL: Register Assembly through Configuration**. 
3.  Spécifiez le chemin d’accès de l’assembly local. 
4.  Le fichier json s’affiche. Examinez et modifiez, si nécessaire, les paramètres des ressources et les dépendances de l’assembly. Les instructions s’affichent dans la fenêtre de sortie. Enregistrez (**CTRL+S**) le fichier Json pour procéder à l’inscription de l’assembly.

![Data Lake Tools pour Visual Studio Code - code behind](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Dépendances de l’assembly : Azure Data Lake Tools détecte automatiquement si la DLL possède des dépendances. Une fois détectées, les dépendances s’affichent dans le fichier Json. 
>- Ressources : vous pouvez charger vos ressources DLL (par exemple, txt, png, csv, etc.) dans le cadre de l’inscription de l’assembly. 

Une autre méthode pratique pour déclencher la commande **ADL: Register Assembly through Configuration** consiste à cliquer avec le bouton droit sur le fichier dll dans l’Explorateur. 

Le code U-SQL suivant montre comment appeler un assembly. Dans l’exemple, le nom de l’assembly est *test*.

```
REFERENCE ASSEMBLY [test];

@a = 
    EXTRACT 
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string 
    FROM @"Sample/SearchLog.txt" 
    USING Extractors.Tsv();

@d =
    SELECT DISTINCT Region 
    FROM @a;

@d1 = 
    PROCESS @d
    PRODUCE 
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();

OUTPUT @d1 
    TO @"Sample/SearchLogtest.txt" 
    USING Outputters.Tsv();
```


## <a name="access-data-lake-analytics-catalog"></a>Accès au catalogue Data Lake Analytics

Une fois connecté à Azure, vous pouvez utiliser les étapes suivantes pour accéder au catalogue U-SQL :

**Pour accéder aux métadonnées Azure Data Lake Analytics**

1.  Appuyez sur **CTRL+MAJ+P**, puis tapez **ADL: List Tables**.
2.  Cliquez sur l’un des comptes Data Lake Analytics.
3.  Cliquez sur l’une des bases de données Data Lake Analytics.
4.  Cliquez sur l’un des schémas. Les tables s’affichent.

## <a name="show-data-lake-analytics-jobs"></a>Afficher les travaux Data Lake Analytics

Utilisez la palette de commandes (**Ctrl+Maj+P**) et choisissez **ADL: Show Job**. 

1.  Sélectionnez un compte local ou ADLA 
2.  Attendez que la liste des travaux pour le compte s’affiche
3.  Sélectionnez un travail dans la liste, ADL Tools ouvre les détails du travail dans le portail et affiche le fichier JobInfo dans VSCode.

![Data Lake Tools pour Visual Studio Code - Types d’objets IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-adls-integration"></a>Intégration d’Azure Data Lake Storage (ADLS)

Vous pouvez utiliser les commandes relatives à ADLS pour parcourir les ressources ADLS, afficher un aperçu du fichier ADLS et charger un fichier ADLS directement dans VSCode.  
### <a name="list-storage-path"></a>Lister le chemin de stockage

Utilisez la palette de commandes (**Ctrl+Maj+P**) et choisissez **ADL: List Storage Path**.
1.  Ouvrez la palette de commandes et entrez la commande.

    ![Data Lake Tools pour Visual Studio Code - Lister le chemin de stockage](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  Sélectionnez la façon dont vous voulez lister le chemin de stockage. Cette section utilise **Entrer un chemin d’accès** comme exemple.

    ![Data Lake Tools pour Visual Studio Code - Lister le chemin de stockage, sélection d’une méthode](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- Vscode conserve le dernier chemin d’accès auquel vous avez accédé dans chaque compte ADLA. Exemple：/tt/ss.
    >- Naviguer à partir du chemin d’accès racine : lister le chemin d’accès racine à partir duquel vous avez sélectionné le compte ADLA ou local.
    >- Entrer un chemin d’accès : lister le chemin d’accès spécifié à partir duquel vous avez sélectionné le compte ADLA ou local.
    
3. Sélectionnez un compte local ou un compte ADLA.

    ![Data Lake Tools pour Visual Studio Code - Sélectionner Plus](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Cliquez sur Plus pour lister d’autres comptes ADLA et sélectionnez un compte ADLA.

    ![Data Lake Tools pour Visual Studio Code - Sélectionner un compte](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  Entrez le chemin de stockage Azure. Par exemple : /output

       ![Data Lake Tools pour Visual Studio Code - Entrer le chemin de stockage](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  Résultats : la palette de commandes liste les informations sur le chemin d’accès en fonction des informations que vous avez saisies.

    ![Data Lake Tools pour Visual Studio Code - Lister le chemin de stockage, résultats](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

Il est plus pratique d’utiliser le menu contextuel accessible par clic droit pour lister le chemin d’accès relatif.

1.  Cliquez avec le bouton droit sur la chaîne du chemin d’accès pour sélectionner List Storage Path (Lister le chemin de stockage).

       ![Data Lake Tools pour Visual Studio Code - Menu contextuel accessible par clic droit](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)
2. Le chemin d’accès relatif sélectionné s’affiche dans la palette de commandes.

   ![Data Lake Tools pour Visual Studio Code - Lister le chemin sélectionné](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  Sélectionnez un compte local ou un compte ADLA.

       ![Data Lake Tools pour Visual Studio Code - Sélectionner un compte](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Résultats : la palette de commandes liste les dossiers et les fichiers correspondant au chemin d’accès actuel.

       ![Data Lake Tools pour Visual Studio Code - Lister le chemin actuel](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-storage-file"></a>Aperçu du fichier de stockage

Utilisez la palette de commandes (**Ctrl+Maj+P**) et choisissez **ADL: Preview Storage File**.
1.  Ouvrez la palette de commandes et entrez la commande.

       ![Data Lake Tools pour Visual Studio Code - Aperçu du fichier de stockage](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  Sélectionnez un compte local ou un compte ADLA.

       ![Data Lake Tools pour Visual Studio Code - Lister un compte](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Cliquez sur Plus pour lister d’autres comptes ADLA et sélectionnez un compte ADLA.

       ![Data Lake Tools pour Visual Studio Code - Sélectionner un compte](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Entrez un fichier ou le chemin de stockage Azure. Par exemple : /output/SearchLog.txt

       ![Data Lake Tools pour Visual Studio Code - Entrer le fichier et le chemin de stockage](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  Résultats : la palette de commandes liste les informations sur le chemin d’accès en fonction des informations que vous avez saisies.

       ![Data Lake Tools pour Visual Studio Code - Aperçu du fichier, résultats](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

Il est plus pratique d’utiliser le menu contextuel accessible par clic droit pour afficher un aperçu d’un fichier.

1.  Cliquez avec le bouton droit sur un chemin d’accès pour afficher un aperçu d’un fichier.

       ![Data Lake Tools pour Visual Studio Code - Menu contextuel accessible par clic droit](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png)

2.  Sélectionnez un compte local ou un compte ADLA.

       ![Data Lake Tools pour Visual Studio Code - Sélectionner un compte](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Résultats : VSCode affiche les résultats de l’aperçu du fichier.

       ![Data Lake Tools pour Visual Studio Code - Aperçu du fichier, résultats](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-file"></a>Charger un fichier 

Vous pouvez charger un fichier via la commande **ADL: Upload File** ou **ADL: Upload File through Configuration**.

**ADL: Upload File**
1.  Appuyez sur CTRL+MAJ+P pour ouvrir la palette de commandes ou cliquez avec le bouton droit sur l’éditeur de script, puis entrez **Upload File**.
2. Entrez un chemin local pour le chargement.

    ![Data Lake Tools pour Visual Studio Code - Entrer un chemin local](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. Sélectionnez une méthode pour lister le chemin de stockage. Cette section utilise **Entrer un chemin d’accès** comme exemple.

    ![Data Lake Tools pour Visual Studio Code - Lister le chemin de stockage](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- Vscode conserve le dernier chemin d’accès auquel vous avez accédé dans chaque compte ADLA. Exemple：/tt/ss.
    >- Naviguer à partir du chemin d’accès racine : lister le chemin d’accès racine à partir duquel vous avez sélectionné le compte ADLA ou local.
    >- Entrer un chemin d’accès : lister le chemin d’accès spécifié à partir duquel vous avez sélectionné le compte ADLA ou local.

4. Sélectionnez un compte local ou un compte ADLA.

    ![Data Lake Tools pour Visual Studio Code - Stockage accessible par clic droit](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5.  Entrez le chemin de stockage Azure. Par exemple : /output/

       ![Data Lake Tools pour Visual Studio Code - Entrer le chemin de stockage](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. Listez le chemin de stockage Azure. Sélectionnez **Choisir le dossier actuel**.

    ![Data Lake Tools pour Visual Studio Code - Sélectionner un dossier](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  Résultats : la fenêtre de sortie affiche l’état de chargement du fichier.

       ![Data Lake Tools pour Visual Studio Code - État du chargement](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**ADL: Upload File through Configuration**
1.  Appuyez sur CTRL+MAJ+P pour ouvrir la palette de commandes ou cliquez avec le bouton droit sur l’éditeur de script, puis entrez **Upload File through Configuration**.
2.  VSCode affiche un fichier json. Vous pouvez entrer des chemins de fichiers et charger plusieurs fichiers en même temps. Les instructions s’affichent dans la fenêtre de sortie. Enregistrez (**CTRL+S**) le fichier Json pour procéder au chargement du fichier.

       ![Data Lake Tools pour Visual Studio Code - Entrer un fichier](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Résultats : la fenêtre de sortie affiche l’état de chargement du fichier.

       ![Data Lake Tools pour Visual Studio Code - État du chargement](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

Vous pouvez également charger un fichier dans le stockage à l’aide du menu accessible par clic droit sur le chemin d’accès complet au fichier ou le chemin d’accès relatif au fichier dans l’éditeur de script. Entrez le chemin d’accès au fichier local et choisissez le compte, puis la fenêtre de sortie affiche l’état du chargement. 

### <a name="open-web-azure-storage-explorer"></a>Ouvrir l’Explorateur de stockage Azure web
Vous pouvez ouvrir **l’Explorateur de stockage Azure web** via la commande : **ADL: Open Web Azure Storage Explorer** ou à l’aide du menu contextuel accessible par clic droit.

1. Appuyez sur CTRL+MAJ+P pour ouvrir la palette de commandes.
2. Entrez **Open Web Azure Storage Explorer** ou cliquez avec le bouton droit sur un chemin d’accès relatif ou un chemin d’accès complet dans l’éditeur de script pour choisir **Open Web Azure Storage Explorer**.
3. Sélectionnez un compte Data Lake Analytics.

ADL Tools ouvre le chemin de stockage Azure dans le portail. Vous pouvez accéder au chemin d’accès et afficher un aperçu du fichier à partir du web.

### <a name="local-run-and-local-debug-windows-users"></a>Exécution locale et débogage local - Utilisateurs Windows
L’exécution locale U-SQL a été implémentée pour tester vos données locales, valider votre script localement avant de publier votre code dans ADLA. La fonctionnalité de débogage local vous permet de déboguer votre C# code-behind, parcourir le code, valider votre script localement avant de le soumettre à ADLA. Consultez les instructions : [Exécution locale et débogage local U-SQL avec Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

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

## <a name="next-steps"></a>Étapes suivantes

- Pour l’exécution locale et le débogage local U-SQL avec Visual Studio Code, consultez [Exécution locale et débogage local U-SQL avec Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).
- Consultez [Didacticiel : bien démarrer avec Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md) pour obtenir des informations sur la prise en main de Data Lake Analytics.
- Consultez [Didacticiel : développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) pour obtenir des informations sur l’utilisation de Data Lake Tools pour Visual Studio.
- Pour plus d’informations sur le développement d’assemblys, consultez [Développement d’assemblys U-SQL pour les tâches d’Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).




