<properties linkid="develop-java-tutorials-jenkins-continuous-integration" urlDisplayName="Jenkins Continuous Integration" pageTitle="Using Azure Storage with a Jenkins Continuous Integration Solution | Microsoft Azure" metaKeywords="" description="This tutorial show how to use the Azure blob service as a repository for build artifacts created by a Jenkins continuous integration solution." metaCanonical="" services="storage" documentationCenter="Java" title="Using Azure Storage with a Jenkins Continuous Integration solution" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Utilisation d'Azure Storage avec une solution d'intégration continue Jenkins
============================================================================

*Par [Microsoft Open Technologies Inc.](http://msopentech.com)*

Les informations suivantes expliquent comment utiliser le service BLOB Azure comme référentiel d'artefacts de build créés par une solution d'intégration continue Jenkins (Jenkins CI). Cela peut s'avérer utile dans plusieurs scénarios, notamment lorsque vous codez dans un environnement de développement agile (avec Java ou d'autres langages), que les builds s'exécutent sur la base d'une intégration continue et que vous avez besoin d'un référentiel pour vos artefacts de build, de manière, par exemple, à pouvoir les partager avec d'autres membres de l'organisation, vos clients, ou conserver une archive.

Dans ce didacticiel, vous allez utiliser le plug-in Azure Storage pour Jenkins CI mis à disposition par Microsoft Open Technologies, Inc.

Sommaire
--------

-   [Présentation de Jenkins](#overview)
-   [Avantages de l'utilisation du service BLOB](#benefits)
-   [Configuration requise](#prerequisites)
-   [Utilisation du service BLOB avec Jenkins CI](#howtouse)
-   [Installation du plug-in Azure Storage](#howtoinstall)
-   [Configuration du plug-in Azure Storage pour l'utilisation de votre compte de stockage](#howtoconfigure)
-   [Création d'une action post-build qui télécharge les artefacts de votre build sur votre compte de stockage](#howtocreatepostbuild)
-   [Composants utilisés par le service BLOB](#components)

PrésentationPrésentation de Jenkins
-----------------------------------

Jenkins rend possible l'intégration continue d'un projet de logiciel en permettant aux développeurs d'intégrer aisément les modifications de leur code, et de créer automatiquement et fréquemment des builds, d'où une productivité accrue. Les versions des builds sont gérées et les artefacts de build peuvent être téléchargés dans divers référentiels. Dans cette rubrique, vous allez apprendre à utiliser le stockage d'objets blob comme référentiel des artefacts de build.

Pour plus d'informations sur Jenkins, consultez la page de [présentation de Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

AvantagesAvantages de l'utilisation du service BLOB
---------------------------------------------------

L'utilisation du service BLOB pour héberger vos artefacts de build dans un environnement agile présente les avantages suivants :

-   Haute disponibilité de vos artefacts de build.
-   Performances lorsque votre solution Jenkins CI télécharge vos artefacts de build.
-   Performances lorsque vos clients et partenaires téléchargent vos artefacts de build.
-   Contrôle sur les stratégies d'accès utilisateur, avec choix entre accès anonyme, accès par signature d'accès partagé basé sur l'expiration, accès privé, etc.

Configuration requiseConfiguration requise
------------------------------------------

Pour utiliser le service BLOB avec votre solution Jenkins CI, vous avez besoin des éléments suivants :

-   Une solution d'intégration continue Jenkins.

    Si vous ne disposez pas d'une solution Jenkins CI, vous pouvez en exécuter une à l'aide de la technique suivante :

    1.  Sur une machine compatible Java, téléchargez le fichier jenkins.war à partir de <http://jenkins-ci.org>.
    2.  Accédez au dossier contenant le fichier jenkins.war, ouvrez une invite de commandes et exécutez la commande suivante :

        `java -jar jenkins.war`

    3.  Dans votre navigateur, ouvrez `http://localhost:8080/`. Le tableau de bord Jenkins s'ouvre ; vous allez vous en servir pour installer et configurer le plug-in Azure Storage.

        Une solution Jenkins CI type serait configurée pour s'exécuter en tant que service, mais l'exécution du fichier .war Jenkins depuis la ligne de commande est suffisante pour les besoins de ce didacticiel.

-   Un compte Azure. Pour vous inscrire afin d'obtenir un compte Azure, consultez la page <http://www.windowsazure.com>.

-   Un compte de stockage Azure. Si vous ne disposez pas déjà d'un compte de stockage, vous pouvez en créer un en suivant la procédure décrite à la page [Création d'un compte de stockage](http://go.microsoft.com/fwlink/?LinkId=279823).

-   Une bonne connaissance de la solution Jenkins CI est recommandée, mais pas obligatoire, car le contenu suivant emploiera un exemple élémentaire pour décrire la procédure requise lorsque vous utilisez le service BLOB comme référentiel pour les artefacts de build Jenkins CI.

Utilisation du service BLOBUtilisation du service BLOB avec Jenkins CI
----------------------------------------------------------------------

Pour utiliser le service BLOB avec Jenkins, vous devez installer le plug-in Azure Storage, le configurer pour utiliser votre compte de stockage, puis créer une action post-build qui télécharge vos artefacts de build sur votre compte de stockage. Les étapes de cette procédure sont décrites dans les sections suivantes.

InstallationInstallation du plug-in Azure Storage
-------------------------------------------------

1.  Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins**.
2.  Sur la page **Manage Jenkins**, cliquez sur **Manage Plugins**.
3.  Cliquez sur l'onglet **Available**.
4.  Dans la section **Artifact Uploaders**, activez la case à cocher **Azure Storage plugin**.
5.  Cliquez sur **Install without restart** ou sur **Download now and install after restart**.
6.  Redémarrez Jenkins.

ConfigurationConfiguration du plug-in Azure Storage pour l'utilisation de votre compte de stockage
--------------------------------------------------------------------------------------------------

1.  Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins**.
2.  Sur la page **Manage Jenkins**, cliquez sur **Configure System**.
3.  Dans la section **Azure Storage Account Configuration** :
    1.  Entrez le nom de votre compte de stockage, que vous pouvez obtenir à partir du portail Azure, <https://manage.windowsazure.com>.
    2.  Entrez la clé de votre compte de stockage, que vous pouvez obtenir à partir du portail Azure.
    3.  Utilisez la valeur par défaut pour **Blob Service Endpoint URL** si vous vous servez du cloud public Azure. Si vous vous servez d'un autre cloud Azure, utilisez le point de terminaison spécifié dans le portail de gestion Azure pour votre compte de stockage.
    4.  Cliquez sur **Validate Storage Credentials** pour valider votre compte de stockage.
    5.  [Facultatif] Si vous avez d'autres comptes de stockage que vous souhaitez rendre disponibles pour votre solution Jenkins CI, cliquez sur **Add more Storage Accounts**.
    6.  Cliquez sur **Save** pour enregistrer vos paramètres.

Création d'une action post-buildCréation d'une action post-build qui télécharge vos artefacts de build sur votre compte de stockage
-----------------------------------------------------------------------------------------------------------------------------------

Pour les besoins de la formation, nous devons d'abord créer une tâche qui créera plusieurs fichiers, puis ajouter l'action post-build pour télécharger les fichiers sur votre compte de stockage.

1.  Dans le tableau de bord Jenkins, cliquez sur **New Job**.
2.  Nommez la tâche **MyJob**, cliquez sur **Build a free-style software project**, puis sur **OK**.
3.  Dans la section **Build** de la configuration de la tâche, cliquez sur **Add build step** et choisissez **Execute Windows batch command**.
4.  Dans **Command**, utilisez les commandes suivantes :

         md text
         cd text
         echo Hello Azure Storage from Jenkins > hello.txt
         date /t > date.txt
         time /t >> date.txt

5.  Dans la section **Post-build Actions** de la configuration de la tâche, cliquez sur **Add post-build action** et choisissez **Upload artifacts to Azure Blob storage**.
6.  Pour **Storage Account Name**, sélectionnez le compte de stockage à utiliser.
7.  Pour **Container Name**, spécifiez le nom du conteneur. Le conteneur est créé s'il n'existe pas déjà au téléchargement des artefacts de build. Vous pouvez utiliser des variables d'environnement : pour cet exemple, entrez **${JOB\_NAME}** comme nom de conteneur.

    **Conseil**

    Sous la section **Command** où vous avez entré un script pour **Execute Windows batch command**, un lien permet d'accéder aux variables d'environnement reconnues par Jenkins. Cliquez sur ce lien pour découvrir les noms des variables d'environnement avec leurs descriptions. Notez que les variables d'environnement qui contiennent des caractères spéciaux, telle la variable d'environnement **BUILD\_URL**, ne sont pas autorisées comme nom de conteneur ou chemin virtuel commun.

8.  Cliquez sur **Make container public** pour cet exemple. Si vous voulez utiliser un conteneur privé, vous devez créer une signature d'accès partagé pour autoriser l'accès. La procédure n'entre pas dans le cadre de cette rubrique. Pour en savoir plus sur les signatures d'accès partagé, consultez la page [Créer et utiliser une signature d'accès partagé](http://go.microsoft.com/fwlink/?LinkId=279889).
9.  Pour **List of Artifacts to upload**, entrez **text/\*.txt**.
10. Pour **Common virtual path for uploaded artifacts**, entrez **${BUILD\_ID}/\${BUILD\_NUMBER}**.
11. Cliquez sur **Save** pour enregistrer vos paramètres.
12. Dans le tableau de bord Jenkins, cliquez sur **Build Now** pour exécuter **MyJob**. Examinez l'état dans la sortie de la console. Les messages d'état du stockage Azure sont inclus dans la sortie de la console lorsque l'action post-build commence à télécharger les artefacts de build.
13. Une fois la tâche terminée correctement, vous pouvez examiner les artefacts de build en ouvrant l'objet blob public.
    1.  Connectez-vous au portail de gestion Azure, <https://manage.windowsazure.com>.
    2.  Cliquez sur **Stockage**.
    3.  Cliquez sur le nom du compte de stockage que vous avez utilisé pour Jenkins.
    4.  Cliquez sur **Conteneurs**.
    5.  Cliquez sur le conteneur nommé **myjob**, qui correspond à la version en minuscules du nom de tâche attribué à la création de la tâche Jenkins. Les noms de conteneurs et les noms d'objets blob sont en minuscules (et sensibles à la casse) dans le stockage Azure. La liste d'objets blob du conteneur nommé **myjob** contient normalement les fichiers **hello.txt** et **date.txt**. Copiez l'URL correspondant à l'un de ces éléments et ouvrez-la dans le navigateur. Le fichier texte qui a été téléchargé apparaît comme un artefact de build.

Composants du service BLOBComposants utilisés par le service BLOB
-----------------------------------------------------------------

La section suivante présente les composants du service BLOB.

-   **Compte de stockage** : tous les accès à Azure Storage passent par un compte de stockage. Il s'agit du plus haut niveau de l'espace de noms permettant d'accéder aux objets blob. Un compte peut contenir un nombre illimité de conteneurs, tant que sa taille totale ne dépasse pas 100 To.
-   **Conteneur** : un conteneur regroupe un ensemble d'objets blob. Tous les objets blob doivent figurer dans un conteneur. Un compte peut contenir un nombre illimité de conteneurs. Un conteneur peut stocker un nombre illimité d'objets blob.
-   **Blob** : un fichier de n'importe quel type et de n'importe quelle taille. Deux types d'objets blob peuvent être stockés dans Azure Storage : les objets blob de blocs et les objets blob de pages. La plupart des fichiers sont des objets blob de blocs. Un seul objet blob de blocs peut avoir une taille maximale de 200 Go. Ce didacticiel utilise des objets blob de blocs. Les objets blob de pages, autre type d'objets blob, peuvent avoir une taille de 1 To et sont plus efficaces lorsque des plages d'octets dans un fichier sont modifiées fréquemment. Pour plus d’informations sur les objets blob, consultez la page [Présentation des objets blob de blocs et des objets blob de pages](http://msdn.microsoft.com/en-us/library/windowsazure/ee691964.aspx).
-   **Format d'URL** : les objets blob sont adressables à l'aide du format d'URL suivant :

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    Le format ci-dessus s'applique au cloud public Azure. Si vous utilisez un autre cloud Azure, utilisez le point de terminaison dans le portail de gestion Azure pour déterminer votre URL de point de terminaison.

    Dans le format ci-dessus, `storageaccount` représente le nom de votre compte de stockage, `container_name` représente le nom de votre conteneur et `blob_name` représente le nom de votre objet blob, respectivement. Le nom du conteneur contient plusieurs chemins d'accès, séparés par une barre oblique (**/**). Dans ce didacticiel, nous avons utilisé **MyJob** comme exemple de nom de conteneur et **${BUILD\_ID}/\${BUILD\_NUMBER}** comme chemin virtuel commun. L'URL de l'objet blob a donc la forme suivante :

    `http://example.blob.core.windows.net/myjob/2013-01-28_15-00-35/2/hello.txt`


  [Overview of Jenkins]: #overview
  [Benefits of using the Blob service]: #benefits
  [Prerequisites]: #prerequisites
  [How to use the Blob service with Jenkins CI]: #howtouse
  [How to install the Azure Storage plugin]: #howtoinstall
  [How to configure the Azure Storage plugin to use your storage account]: #howtoconfigure
  [How to create a post-build action that uploads your build artifacts to your storage account]: #howtocreatepostbuild
  [Components used by the Blob service]: #components
  [How to Create a Storage Account]: http://go.microsoft.com/fwlink/?LinkId=279823
  [Meet Jenkins]: https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins
  [ms-open-tech]: http://msopentech.com
