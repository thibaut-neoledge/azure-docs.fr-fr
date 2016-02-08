<properties 
	pageTitle="Utilisation d’Azure Storage avec une solution d’intégration continue Jenkins | Microsoft Azure" 
	description="Ce didacticiel décrit l’utilisation du service BLOB Azure comme référentiel d’artefacts de build créés par une solution d’intégration continue Jenkins." 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

# Utilisation d’Azure Storage avec une solution d’intégration continue Jenkins

## Vue d’ensemble

Les informations suivantes expliquent comment utiliser Blob Storage comme dépôt pour les artefacts de build créés par une solution d’intégration continue (CI) Jenkins ou comme source de fichiers téléchargeables dans un processus de génération. Cela peut s'avérer utile dans plusieurs scénarios, notamment lorsque vous codez dans un environnement de développement agile (avec Java ou d'autres langages), que les builds s'exécutent sur la base d'une intégration continue et que vous avez besoin d'un référentiel pour vos artefacts de build, de manière, par exemple, à pouvoir les partager avec d'autres membres de l'organisation, vos clients, ou conserver une archive. Il existe un autre scénario dans lequel votre tâche de build proprement dite requiert d'autres fichiers, comme des dépendances à télécharger dans le cadre de l'entrée de génération.

Dans ce didacticiel, vous allez utiliser le plug-in Azure Storage pour Jenkins CI mis à disposition par Microsoft.

## Présentation de Jenkins ##

Jenkins rend possible l’intégration continue d’un projet de logiciel en permettant aux développeurs d’intégrer aisément les modifications de leur code, et de créer automatiquement et fréquemment des builds, d’où une productivité accrue. Les versions des builds sont gérées et les artefacts de build peuvent être téléchargés dans divers référentiels. Dans cette rubrique, vous allez apprendre à utiliser le stockage d'objets blob comme référentiel des artefacts de build. Vous allez également apprendre à télécharger des dépendances depuis le stockage d'objets blob Azure.

Pour plus d'informations sur Jenkins, consultez la page de [présentation de Jenkins][].

## Avantages de l’utilisation du service BLOB ##

L’utilisation du service BLOB pour héberger vos artefacts de build dans un environnement agile présente les avantages suivants :

- Haute disponibilité de vos artefacts de build et/ou dépendances téléchargeables.
- Performances lorsque votre solution Jenkins CI télécharge vos artefacts de build.
- Performances lorsque vos clients et partenaires téléchargent vos artefacts de build.
- Contrôle sur les stratégies d’accès utilisateur, avec choix entre accès anonyme, accès par signature d’accès partagé basé sur l’expiration, accès privé, etc.

## Configuration requise ##

Pour utiliser le service BLOB avec votre solution Jenkins CI, vous avez besoin des éléments suivants :

- Une solution d'intégration continue Jenkins.

    Si vous ne disposez pas d’une solution Jenkins CI, vous pouvez en exécuter une à l’aide de la technique suivante :

    1. Sur un ordinateur compatible Java, téléchargez le fichier jenkins.war à l'adresse <http://jenkins-ci.org>.
    2. Accédez au dossier contenant le fichier jenkins.war, ouvrez une invite de commandes et exécutez la commande suivante :

        `java -jar jenkins.war`

    3. Dans votre navigateur, ouvrez `http://localhost:8080/`. Le tableau de bord Jenkins s’ouvre ; vous allez vous en servir pour installer et configurer le plug-in Azure Storage.

        Une solution Jenkins CI type serait configurée pour s'exécuter en tant que service, mais l'exécution du fichier .war Jenkins depuis la ligne de commande est suffisante pour les besoins de ce didacticiel.

- Un compte Azure. Pour créer un compte Azure, consultez la page <http://www.azure.com>.

- Un compte de stockage Azure. Si vous ne disposez pas déjà d'un compte de stockage, vous pouvez en créer un en suivant la procédure décrite à la page [Création d'un compte de stockage][].

- Une bonne connaissance de la solution Jenkins CI est recommandée, mais pas obligatoire, car le contenu suivant emploiera un exemple élémentaire pour décrire la procédure requise lorsque vous utilisez le service BLOB comme référentiel pour les artefacts de build Jenkins CI.

## Utilisation du service BLOB avec Jenkins CI ##

Pour utiliser le service BLOB avec Jenkins, vous devez installer le plug-in Azure Storage, le configurer pour utiliser votre compte de stockage, puis créer une action post-build qui télécharge vos artefacts de build sur votre compte de stockage. Les étapes de cette procédure sont décrites dans les sections suivantes.

## Comment installer le plug-in de stockage Azure ##

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins**.
2. Sur la page **Manage Jenkins**, cliquez sur **Manage Plugins**.
3. Cliquez sur l'onglet **Available**.
4. Dans la section **Téléchargeurs d'artefacts**, activez la case à cocher **Plug-in Microsoft Azure Storage**.
5. Cliquez sur **Install without restart** ou sur **Download now and install after restart**.
6. Redémarrez Jenkins.

## Comment configurer le plug-in de stockage Azure pour utiliser votre compte de stockage ##

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins**.
2. Sur la page **Manage Jenkins**, cliquez sur **Configure System**.
3. Dans la section **Configuration du compte Microsoft Azure Storage** :
    1. Entrez le nom de votre compte de stockage, que vous pouvez obtenir à partir du [portail Azure](https://portal.azure.com).
    2. Entrez la clé de votre compte de stockage, que vous pouvez obtenir à partir du [portail Azure](https://portal.azure.com).
    3. Utilisez la valeur par défaut pour **Blob Service Endpoint URL** si vous vous servez du cloud public Azure. Si vous vous servez d’un autre cloud Azure, utilisez le point de terminaison spécifié dans le [portail Azure](https://portal.azure.com) pour votre compte de stockage. 
    4. Cliquez sur **Valider les informations d'identification de stockage** pour valider votre compte de stockage. 
    5. [Facultatif] Si vous avez d'autres comptes de stockage que vous souhaitez rendre disponibles pour votre solution Jenkins CI, cliquez sur **Add more Storage Accounts**.
    6. Cliquez sur **Save** pour enregistrer vos paramètres.

## Création d’une action post-build qui télécharge les artefacts de votre build sur votre compte de stockage ##

Pour les besoins de la formation, nous devons d’abord créer une tâche qui créera plusieurs fichiers, puis ajouter l’action post-build pour télécharger les fichiers sur votre compte de stockage.

1. Dans le tableau de bord Jenkins, cliquez sur **Nouvel élément**.
2. Nommez la tâche **MyJob**, cliquez sur **Build a free-style software project**, puis sur **OK**.
3. Dans la section **Build** de la configuration de la tâche, cliquez sur **Add build step** et choisissez **Execute Windows batch command**.
4. Dans **Command**, utilisez les commandes suivantes :

        md text
        cd text
        echo Hello Azure Storage from Jenkins > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. Dans la section **Post-build Actions** de la configuration de la tâche, cliquez sur **Add post-build action** et choisissez **Upload artifacts to Azure Blob storage**.
6. Dans le champ **Nom du compte de stockage**, sélectionnez le compte de stockage à utiliser.
7. Dans le champ **Nom du conteneur**, indiquez le nom du conteneur. Le conteneur est créé s’il n’existe pas déjà au téléchargement des artefacts de build. Vous pouvez utiliser des variables d'environnement : pour cet exemple, entrez **${JOB\_NAME}** comme nom de conteneur.

    **Conseil**
    
    Sous la section **Command** où vous avez entré un script pour **Execute Windows batch command**, un lien permet d'accéder aux variables d'environnement reconnues par Jenkins. Cliquez sur ce lien pour découvrir les noms des variables d’environnement avec leurs descriptions. Notez que les variables d'environnement qui contiennent des caractères spéciaux, telle la variable d'environnement **BUILD\_URL**, ne sont pas autorisées comme nom de conteneur ou chemin virtuel commun.

8. Cliquez sur **Rendre le nouveau conteneur public par défaut** pour cet exemple. Si vous voulez utiliser un conteneur privé, vous devez créer une signature d'accès partagé pour autoriser l'accès. La procédure n’entre pas dans le cadre de cette rubrique. Pour en savoir plus sur les signatures d'accès partagé, consultez la page [Créer et utiliser une signature d'accès partagé](http://go.microsoft.com/fwlink/?LinkId=279889).
9. [Facultatif] Cliquez sur **Nettoyer le conteneur avant le téléchargement** si vous souhaitez que le contenu du conteneur soit effacé avant le téléchargement des artefacts de build (ne sélectionnez pas cette option si vous ne souhaitez pas effacer le contenu du conteneur).
10. Pour **List of Artifacts to upload**, entrez **text/*.txt**.
11. Dans le cadre de ce didacticiel, entrez **${BUILD\_ID}/${BUILD\_NUMBER}** pour **Chemin virtuel commun pour les artefacts téléchargés**.
12. Cliquez sur **Save** pour enregistrer vos paramètres.
13. Dans le tableau de bord Jenkins, cliquez sur **Build Now** pour exécuter **MyJob**. Examinez l'état dans la sortie de la console. Les messages d'état du stockage Azure sont inclus dans la sortie de la console lorsque l'action post-build commence à télécharger les artefacts de build.
14. Une fois la tâche terminée correctement, vous pouvez examiner les artefacts de build en ouvrant l’objet blob public.
    1. Connectez-vous au [portail Azure](https://portal.azure.com).
    2. Cliquez sur **Stockage**.
    3. Cliquez sur le nom du compte de stockage que vous avez utilisé pour Jenkins.
    4. Cliquez sur **Conteneurs**.
    5. Cliquez sur le conteneur nommé **myjob**, qui correspond à la version en minuscules du nom de tâche attribué à la création de la tâche Jenkins. Les noms de conteneurs et les noms d’objets blob sont en minuscules (et sensibles à la casse) dans le stockage Azure. La liste d'objets blob du conteneur nommé **myjob** contient normalement les fichiers **hello.txt** et **date.txt**. Copiez l’URL correspondant à l’un de ces éléments et ouvrez-la dans le navigateur. Le fichier texte qui a été téléchargé apparaît comme un artefact de build.

Une seule action post-build qui télécharge les artefacts dans le stockage d'objet blob Azure peut être créée par tâche. Notez que l'action post-build permettant de télécharger des artefacts sur le stockage d'objets blob Azure peut spécifier différents fichiers (y compris des caractères génériques) et chemins d'accès aux fichiers dans **Liste des artefacts à télécharger** grâce à l'ajout d'un point-virgule comme séparateur. Par exemple, si votre build Jenkins produit des fichiers JAR et des fichiers TXT dans le dossier **build** de votre espace de travail et que vous souhaitez télécharger ces deux types de fichiers sur le stockage d’objets blob Azure, entrez ce qui suit dans le champ **Liste des artefacts à télécharger** : **build/*.jar;build/*.txt**. Vous pouvez aussi utiliser un double signe deux-points pour indiquer le chemin à utiliser dans le nom de l’objet blob. Par exemple, si vous souhaitez que les fichiers JAR soient téléchargés à l’aide de fichiers **binaires** dans le chemin d’accès des objets blob et que les fichiers TXT soient téléchargés à l’aide de **notices** dans le chemin d’accès des objets blob, entrez ce qui suit dans le champ **Liste des artefacts à télécharger** : **build/*.jar::binaries;build/*.txt::notices**

## Création d’une étape de génération pour télécharger des éléments depuis un stockage d’objets blob Azure ##

La procédure suivante explique comment configurer une étape de génération pour télécharger des éléments depuis un stockage d’objets blob Azure. Ceci peut s'avérer utile si vous souhaitez inclure des éléments dans votre build, par exemple, des fichiers JAR à conserver dans votre stockage d'objets blob Azure.

1. Dans la section **Build** de la configuration de la tâche, cliquez sur **Ajouter une étape de build** et choisissez **Télécharger à partir du stockage d'objets blob Azure**.
2. Dans le champ **Nom du compte de stockage**, sélectionnez le compte de stockage à utiliser.
3. Dans le champ **Nom du conteneur**, indiquez le nom du conteneur dans lequel se trouvent les objets blob que vous souhaitez télécharger. Vous pouvez utiliser des variables d'environnement.
4. Dans le champ **Nom d'objet blob**, indiquez le nom de l'objet blob. Vous pouvez utiliser des variables d'environnement. Vous pouvez aussi utiliser un astérisque comme caractère générique après avoir indiqué la ou les premières lettres du nom de l’objet blob. Par exemple, **projet*** désignera tous les objets blob dont le nom commence par **projet**.
5. [Facultatif] Dans le champ **Chemin de téléchargement**, indiquez l'emplacement de l'ordinateur Jenkins où vous souhaitez télécharger les fichiers depuis le stockage d'objets blob Azure. Vous pouvez utiliser des variables d’environnement. Si vous n'entrez rien dans le champ **Chemin de téléchargement**, les fichiers du stockage d'objets blob Azure seront téléchargés dans l'espace de travail de la tâche.

Si vous souhaitez télécharger d’autres éléments depuis le stockage d’objets blob Azure, vous pouvez créer des étapes de génération supplémentaires.

Après avoir exécuté une build, vous pouvez vérifier la sortie de la console d’historique de build ou vérifier dans le dossier de téléchargement si les objets blob attendus ont bien été téléchargés.

## Composants utilisés par le service BLOB ##

La section suivante présente les composants du service BLOB.

- **Compte de stockage :** tout accès au stockage Azure s'effectue via un compte de stockage. Il s’agit du plus haut niveau d’espace de noms permettant d’accéder aux objets blob. Un compte peut contenir un nombre illimité de conteneurs, tant que sa taille totale ne dépasse pas 100 To.
- **Conteneur** : conteneur regroupant un ensemble d’objets blob. Tous les objets blob doivent figurer dans un conteneur. Un compte peut contenir un nombre illimité de conteneurs. Un conteneur peut stocker un nombre illimité d’objets blob.
- **Objet blob** : fichier de tout type et de toute taille. Il existe deux types d’objets blob qui peuvent être enregistrés dans un stockage Azure : les objets blob de blocs et les objets blob de pages. La plupart des fichiers sont des objets blob de blocs. Un seul objet blob de blocs peut avoir une taille maximale de 200 Go. Ce didacticiel utilise des objets blob de blocs. Les objets blob de pages, autre type d’objets blob, peuvent avoir une taille de 1 To et sont plus efficaces lorsque des plages d’octets dans un fichier sont modifiées fréquemment. Pour plus d’informations sur les blobs, consultez la page [Présentation des objets BLOB de blocs et des objets BLOB de pages](http://msdn.microsoft.com/library/azure/ee691964.aspx).
- **Format d'URL** : les objets blob sont adressables à l'aide du format d'URL suivant :

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    Le format ci-dessus s’applique au cloud public Azure. Si vous utilisez un autre cloud Azure, utilisez le point de terminaison dans le [portail Azure](https://portal.azure.com) pour déterminer votre URL de point de terminaison.

    Dans le format ci-dessus, `storageaccount` représente le nom de votre compte de stockage, `container_name` représente le nom de votre conteneur et `blob_name` représente le nom de votre objet blob, respectivement. Le nom du conteneur contient plusieurs chemins d'accès, séparés par une barre oblique (**/**). Dans ce didacticiel, nous avons utilisé **MyJob** comme exemple de nom de conteneur et **${BUILD\_ID}/${BUILD\_NUMBER}** comme chemin virtuel commun. L'URL de l'objet blob a donc la forme suivante :

    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## Étapes suivantes

  [Création d'un compte de stockage]: http://go.microsoft.com/fwlink/?LinkId=279823
  [présentation de Jenkins]: https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins
 

<!---HONumber=AcomDC_0128_2016-->