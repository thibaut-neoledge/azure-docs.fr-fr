<properties urlDisplayName="Hudson Continuous Integration" pageTitle="Utilisation de Hudson avec le service BLOB Azure | Microsoft Azure" metaKeywords="Hudson, Azure storage, Azure Blob service, Azure storage, Azure hudson" description="Description de l'utilisation de la solution Hudson avec le stockage d'objets blob Azure comme référentiel pour des artefacts de build." metaCanonical="" services="storage" documentationCenter="Java" title="Using Azure Storage with a Hudson Continuous Integration solution" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

#Utilisation d'Azure Storage avec une solution d'intégration continue Hudson

*Par [Microsoft Open Technologies Inc.][ms-open-tech]*

Les informations suivantes expliquent comment utiliser le service BLOB Azure comme référentiel pour les artefacts de build créés par une solution d'intégration continue (CI) Hudson ou comme source de fichiers téléchargeables pour un processus de génération. Cela peut s'avérer utile dans plusieurs scénarios, notamment lorsque vous codez dans un environnement de développement agile (avec Java ou d'autres langages), que les builds s'exécutent sur la base d'une intégration continue et que vous avez besoin d'un référentiel pour vos artefacts de build, de manière, par exemple, à pouvoir les partager avec d'autres membres de l'organisation, vos clients, ou conserver une archive.  Il existe un autre scénario dans lequel votre tâche de build proprement dite requiert d'autres fichiers, comme des dépendances à télécharger dans le cadre de l'entrée de génération.

Dans ce didacticiel, vous allez utiliser le plug-in Azure Storage pour Hudson CI mis à disposition par Microsoft Open Technologies, Inc.

## Sommaire

-   [Présentation d'Hudson][]
-   [Avantages de l'utilisation du service BLOB][]
-   [Configuration requise][]
-   [Utilisation du service BLOB avec Hudson CI][]
-   [Installation du plug-in Azure Storage][]
-   [Configuration du plug-in Azure Storage pour l'utilisation de votre compte de stockage][]
-   [Création d'une action post-build qui télécharge les artefacts de votre build sur votre compte de stockage][]
-   [Création d'une étape de génération pour télécharger des éléments depuis un stockage d'objets blob Azure][]
-   [Composants utilisés par le service BLOB][]

## <a id="overview"></a>Présentation d'Hudson ##
Hudson rend possible l'intégration continue d'un projet de logiciel en permettant aux développeurs d'intégrer aisément les modifications de leur code et de créer automatiquement et fréquemment des builds, d'où une productivité accrue. Les versions des builds sont gérées et les artefacts de build peuvent être téléchargés dans divers référentiels. Dans cette rubrique, vous allez apprendre à utiliser le stockage d'objets blob comme référentiel des artefacts de build. Vous allez également apprendre à télécharger des dépendances depuis le stockage d'objets blob Azure.

Pour plus d'informations sur Hudson, consultez la page de [présentation d'Hudson][].

## <a id="benefits"></a>Avantages de l'utilisation du service BLOB ##

L'utilisation du service BLOB pour héberger vos artefacts de build dans un environnement agile présente les avantages suivants :

- Haute disponibilité de vos artefacts de build et/ou dépendances téléchargeables.
- Performances lorsque votre solution Hudson CI télécharge vos artefacts de build.
- Performances lorsque vos clients et partenaires téléchargent vos artefacts de build.
- Contrôle sur les stratégies d'accès utilisateur, avec choix entre accès anonyme, accès par signature d'accès partagé basé sur l'expiration, accès privé, etc.

## <a id="prerequisites"></a>Configuration requise ##

Pour utiliser le service BLOB avec votre solution Hudson CI, vous avez besoin des éléments suivants :

- Une solution d'intégration continue Hudson.

    Si vous ne disposez pas d'une solution Hudson CI, vous pouvez en exécuter une à l'aide de la technique suivante :

    1. Sur un ordinateur compatible Java, téléchargez le WAR Hudson à partir de <http://hudson-ci.org/>.
    2. Accédez au dossier contenant le WAR Hudson et exécutez ce dernier à l'aide d'une invite de commandes. Par exemple, si vous avez téléchargé la version 3.1.2 :

        `java -jar hudson-3.1.2.war`

    3. Dans votre navigateur, ouvrez " http://localhost:8080/ ". Le tableau de bord Hudson s'ouvre.

    4. Lors de la première utilisation d'Hudson, effectuez l'installation initiale à l'adresse " http://localhost:8080/ ". 

    5. Une fois l'installation initiale effectuée, annulez l'instance en cours d'exécution du WAR Hudson, redémarrez ce dernier, puis rouvrez le tableau de bord Hudson, " http://localhost:8080/ ", que vous allez utiliser pour installer et configurer le plug-in Azure Storage.

        Une solution Hudson CI type serait configurée pour s'exécuter en tant que service, mais l'exécution du WAR Hudson depuis la ligne de commande est suffisante pour les besoins de ce didacticiel.

- Un compte Azure. Pour créer un compte Azure, consultez la page <http://www.windowsazure.com>.

- Un compte de stockage Azure. Si vous ne disposez pas déjà d'un compte de stockage, vous pouvez en créer un en suivant la procédure décrite à la page [Création d'un compte de stockage][].

- Une bonne connaissance de la solution Hudson CI est recommandée, mais pas obligatoire, car le contenu suivant emploiera un exemple élémentaire pour décrire la procédure requise lorsque vous utilisez le service BLOB comme référentiel pour les artefacts de build Hudson CI.

## <a id="howtouse"></a>Utilisation du service BLOB avec Hudson CI ##

Pour utiliser le service BLOB avec Hudson, vous devez installer le plug-in Azure Storage, configurer le plug-in pour utiliser votre compte de stockage, puis créer une action post-build qui télécharge vos artefacts de build sur votre compte de stockage. Les étapes de cette procédure sont décrites dans les sections suivantes.

## <a id="howtoinstall"></a>Installation du plug-in Azure Storage ##

1. Dans le tableau de bord Hudson, cliquez sur **Gérer Hudson**.
2. Sur la page **Gérer Hudson**, cliquez sur **Gérer les plug-ins**.
3. Cliquez sur l'onglet **Disponible**.
4. Cliquez sur **Autres**.
5. Dans la section **Téléchargeurs d'artefacts**, activez la case à cocher **Plug-in Microsoft Azure Storage**.
6. Cliquez sur **Installer**.
7. Une fois l'installation terminée, redémarrez Hudson.

## <a id="howtoconfigure"></a>Configuration du plug-in Azure Storage pour l'utilisation de votre compte de stockage ##

1. Dans le tableau de bord Hudson, cliquez sur **Gérer Hudson**.
2. Sur la page **Gérer Hudson**, cliquez sur **Configurer système**.
3. Dans la section **Configuration du compte Microsoft Azure Storage** :
    1. Entrez le nom de votre compte de stockage, que vous pouvez obtenir à partir du portail Azure, <https://manage.windowsazure.com>.
    2. Entrez la clé de votre compte de stockage, que vous pouvez obtenir à partir du portail Azure.
    3. Utilisez la valeur par défaut pour **URL du point de terminaison de service BLOB** si vous vous servez du cloud public Azure. Si vous vous servez d'un autre cloud Azure, utilisez le point de terminaison spécifié dans le portail de gestion Azure pour votre compte de stockage. 
    4. Cliquez sur **Valider les informations d'identification de stockage** pour valider votre compte de stockage. 
    5. [Facultatif] Si vous avez d'autres comptes de stockage que vous souhaitez rendre disponibles pour votre solution Hudson CI, cliquez sur **Ajouter d'autres comptes de stockage**.
    6. Cliquez sur **Enregistrer** pour enregistrer vos paramètres.

## <a id="howtocreatepostbuild"></a>Création d'une action post-build qui télécharge les artefacts de votre build sur votre compte de stockage ##

Pour les besoins de la formation, nous devons d'abord créer une tâche qui créera plusieurs fichiers, puis ajouter l'action post-build pour télécharger les fichiers sur votre compte de stockage.

1. Dans le tableau de bord Hudson, cliquez sur **Nouvelle tâche**.
2. Nommez la tâche **MyJob**, cliquez sur **Générer une tâche logicielle libre**, puis sur **OK**.
3. Dans la section **Génération** de la configuration de la tâche, cliquez sur **Ajouter une étape de génération** et choisissez **Exécuter la commande par lot de Windows**.
4. Dans **Commande**, utilisez les commandes suivantes :

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. Dans la section **Actions post-build** de la configuration de la tâche, cliquez sur **Télécharger les artefacts vers le stockage d'objets blob Microsoft Azure**.
6. Pour **Nom du compte de stockage**, sélectionnez le compte de stockage à utiliser.
7. Pour **Nom du conteneur**, spécifiez le nom du conteneur. Le conteneur est créé s'il n'existe pas déjà au téléchargement des artefacts de build. Vous pouvez utiliser des variables d'environnement : pour cet exemple, entrez **${NOM_TÂCHE}** pour le nom du conteneur.

    **Conseil**
    
    Sous la section **Commande** où vous avez entré un script pour **Exécuter la commande par lot de Windows**, un lien permet d'accéder aux variables d'environnement reconnues par Hudson. Cliquez sur ce lien pour découvrir les noms des variables d'environnement avec leurs descriptions. Notez que les variables d'environnement qui contiennent des caractères spéciaux, telle la variable d'environnement **BUILD_URL**, ne sont pas autorisées comme nom de conteneur ou chemin virtuel commun.

8. Cliquez sur **Rendre le nouveau conteneur public par défaut** pour cet exemple. Si vous voulez utiliser un conteneur privé, vous devez créer une signature d'accès partagé pour autoriser l'accès. La procédure n'entre pas dans le cadre de cette rubrique. Pour en savoir plus sur les signatures d'accès partagé, consultez la page [Créer et utiliser une signature d'accès partagé](http://go.microsoft.com/fwlink/?LinkId=279889).
9. [Facultatif] Cliquez sur **Nettoyer le conteneur avant le téléchargement** si vous souhaitez que le contenu du conteneur soit effacé avant le téléchargement des artefacts de build (ne sélectionnez pas cette option si vous ne souhaitez pas effacer le contenu du conteneur).
10. Pour **Liste des artefacts à télécharger**, entrez **text/*.txt**.
11. Pour **Chemin virtuel commun pour les artefacts téléchargés**, entrez **${BUILD\_ID}/${BUILD\_NUMBER}**.
12. Cliquez sur **Enregistrer** pour enregistrer vos paramètres.
13. Dans le tableau de bord Hudson, cliquez sur **Générer maintenant** pour exécuter **MyJob**. Examinez l'état dans la sortie de la console. Les messages d'état du stockage Azure sont inclus dans la sortie de la console lorsque l'action post-build commence à télécharger les artefacts de build.
14. Une fois la tâche terminée correctement, vous pouvez examiner les artefacts de build en ouvrant l'objet blob public.
    1. Connectez-vous au portail de gestion Azure à l'adresse <https://manage.windowsazure.com>.
    2. Cliquez sur **Stockage**.
    3. Cliquez sur le nom du compte de stockage que vous avez utilisé pour Hudson.
    4. Cliquez sur **Conteneurs**.
    5. Cliquez sur le conteneur nommé **myjob**, qui correspond à la version en minuscules du nom de tâche attribué à la création de la tâche Hudson. Les noms de conteneurs et les noms d'objets blob sont en minuscules (et sensibles à la casse) dans le stockage Azure. La liste d'objets blob du conteneur nommé **myjob** contient normalement les fichiers **hello.txt** et **date.txt**. Copiez l'URL correspondant à l'un de ces éléments et ouvrez-la dans le navigateur. Le fichier texte qui a été téléchargé apparaît comme un artefact de build.

Une seule action post-build qui télécharge les artefacts dans le stockage d'objet blob Azure peut être créée par tâche. Notez que l'action post-build permettant de télécharger des artefacts sur le stockage d'objets blob Azure peut spécifier différents fichiers (y compris des caractères génériques) et chemins d'accès aux fichiers dans **Liste des artefacts à télécharger** grâce à l'ajout d'un point-virgule comme séparateur. Par exemple, si votre build Hudson produit des fichiers JAR et des fichiers TXT dans le dossier **build** de votre espace de travail et que vous souhaitez télécharger ces deux types de fichiers vers le stockage d'objets blob Azure, entrez ce qui suit dans le champ **Liste des artefacts à télécharger** : **build/\*.jar;build/\*.txt**. Vous pouvez aussi utiliser un double signe deux-points pour indiquer le chemin à utiliser dans le nom de l'objet blob. Par exemple, si vous souhaitez que les fichiers JAR soient téléchargés à l'aide de **binaries** dans le chemin d'accès des objets blob et que les fichiers TXT soient téléchargés à l'aide de **notices** dans le chemin d'accès des objets blob, entrez ce qui suit dans le champ **Liste des artefacts à télécharger** : **build/\*.jar::binaries;build/\*.txt::notices**.

## <a name="howtocreatebuildstep"></a>Création d'une étape de génération pour télécharger des éléments depuis un stockage d'objets blob Azure ##

La procédure suivante explique comment configurer une étape de génération pour télécharger des éléments depuis un stockage d'objets blob Azure. Ceci peut s'avérer utile si vous souhaitez inclure des éléments dans votre build, par exemple, des fichiers JAR à conserver dans votre stockage d'objets blob Azure.

1. Dans la section **Génération** de la configuration de la tâche, cliquez sur **Ajouter une étape de génération** et choisissez **Télécharger à partir du stockage d'objets blob Azure**.
2. Pour **Nom du compte de stockage**, sélectionnez le compte de stockage à utiliser.
3. Dans le champ **Nom du conteneur**, indiquez le nom du conteneur dans lequel se trouvent les objets blob que vous souhaitez télécharger. Vous pouvez utiliser des variables d'environnement.
4. Dans le champ **Nom de l'objet blob**, indiquez le nom de l'objet blob. Vous pouvez utiliser des variables d'environnement. Vous pouvez aussi utiliser un astérisque comme caractère générique après avoir indiqué la ou les premières lettres du nom de l'objet blob. Par exemple, **projet\*** désignera tous les objets blob dont le nom commence par **projet**.
5. [Facultatif] Dans le champ **Chemin de téléchargement**, indiquez l'emplacement de l'ordinateur Hudson où vous souhaitez télécharger les fichiers depuis le stockage d'objets blob Azure. Vous pouvez utiliser des variables d'environnement. (Si vous n'entrez rien dans le champ **Chemin de téléchargement**, les fichiers du stockage d'objets blob Azure seront téléchargés dans l'espace de travail de la tâche.)

Si vous souhaitez télécharger d'autres éléments depuis le stockage d'objets blob Azure, vous pouvez créer des étapes de génération supplémentaires.

Après avoir exécuté une build, vous pouvez vérifier la sortie de la console d'historique de build ou vérifier dans le dossier de téléchargement si les objets blob attendus ont bien été téléchargés. 

## <a id="components"></a>Composants utilisés par le service BLOB ##

La section suivante présente les composants du service BLOB.

- **Compte de stockage** : tous les accès à Azure Storage passent par un compte de stockage. Il s'agit du plus haut niveau d'espace de noms permettant d'accéder aux objets blob. Un compte peut contenir un nombre illimité de conteneurs, tant que sa taille totale ne dépasse pas 100 To.
- **Conteneur** : un conteneur regroupe un ensemble d'objets blob. Tous les objets blob doivent figurer dans un conteneur. Un compte peut contenir un nombre illimité de conteneurs. Un conteneur peut stocker un nombre illimité d'objets blob.
- **Blob** : fichier de n'importe quel type et de n'importe quelle taille. Il existe deux types d'objets blob qui peuvent être enregistrés dans un stockage Azure : les objets blob de blocs et les objets blob de pages. La plupart des fichiers sont des objets blob de blocs. Un seul objet blob de blocs peut avoir une taille maximale de 200 Go. Ce didacticiel utilise des objets blob de blocs. Les objets blob de pages, autre type d'objets blob, peuvent avoir une taille de 1 To et sont plus efficaces lorsque des plages d'octets dans un fichier sont modifiées fréquemment. Pour plus d'informations sur les objets blob, consultez la page [Présentation des objets blob de blocs et de pages](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee691964.aspx).
- **Format d'URL** : Les objets blob sont adressables à l'aide du format d'URL suivant :

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    Le format ci-dessus s'applique au cloud public Azure. Si vous utilisez un autre cloud Azure, utilisez le point de terminaison dans le portail de gestion Azure pour déterminer votre URL de point de terminaison.

    Dans le format ci-dessus, " storageaccount " représente le nom de votre compte de stockage, " container_name " représente le nom de votre conteneur et " blob_name " représente le nom de votre objet blob, respectivement. Le nom du conteneur contient plusieurs chemins d'accès, séparés par une barre oblique (**/**). Dans ce didacticiel, nous avons utilisé **MyJob** comme exemple de nom de conteneur et **${BUILD\_ID}/${BUILD\_NUMBER}** comme chemin virtuel commun. L'URL de l'objet blob a donc la forme suivante :

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

  [Présentation d'Hudson]: #overview
  [Avantages de l'utilisation du service BLOB]: #benefits
  [Configuration requise]: #prerequisites
  [Utilisation du service BLOB avec Hudson CI]: #howtouse
  [Installation du plug-in Azure Storage]: #howtoinstall
  [Configuration du plug-in Azure Storage pour l'utilisation de votre compte de stockage]: #howtoconfigure
  [Création d'une action post-build qui télécharge les artefacts de votre build sur votre compte de stockage]: #howtocreatepostbuild
  [Création d'une étape de génération pour télécharger des éléments depuis un stockage d'objets blob Azure]: #howtocreatebuildstep
  [Composants utilisés par le service BLOB]: #components
  [Création d'un compte de stockage]: http://go.microsoft.com/fwlink/?LinkId=279823
  [Présentation d'Hudson]: http://wiki.eclipse.org/Hudson-ci/Meet_Hudson
  [ms-open-tech]: http://msopentech.com


<!--HONumber=35.1-->
