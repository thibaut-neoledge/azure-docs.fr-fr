<properties 
	pageTitle="Téléchargement du Kit de développement logiciel (SDK) Azure pour Java" 
	description="Découvrez comment télécharger le Kit de développement logiciel (SDK) Azure pour Java, avec l'exemple de code fourni pour les projets Maven et les étapes de l'installation de base pour le Kit de ressources Azure pour Eclipse." 
	services="" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

# Téléchargement du Kit de développement logiciel (SDK) Azure pour Java #

Cet article contient des instructions pour télécharger et installer les bibliothèques Azure pour Java.

**Remarque :** les bibliothèques Azure pour Java sont distribuées sous la [Licence Apache, Version 2.0][license] .

## Bibliothèques Azure pour Java - Téléchargement manuel ##

Pour installer manuellement les bibliothèques Azure pour Java, cliquez sur <http://go.microsoft.com/fwlink/?LinkId=690320> pour télécharger un fichier ZIP contenant l’intégralité des bibliothèques et dépendances.

Après avoir téléchargé le fichier zip sur votre ordinateur, extrayez son contenu et utilisez une des options suivantes pour ajouter les fichiers JAR à votre projet :

* Importez les fichiers JAR dans votre projet Java dans Eclipse.

* Configurez le **chemin de build** de votre projet Java dans Eclipse afin d’inclure le chemin d'accès aux fichiers JAR.

Pour plus d'informations sur la configuration des chemins d'accès de build dans Eclipse, consultez l’article [Java Build Path][] sur le site Web d'Eclipse.

**Remarque :** pour plus d’informations sur la licence, consultez les fichiers license.txt et ThirdPartyNotices.txt fournis dans le fichier .zip.

## Bibliothèques Azure pour Java - Génération avec Maven ##

### Étape 1 : Configurer votre projet afin d’utiliser Maven pour la génération ###

Pour créer des projets Maven dans Eclipse qui utilisent les bibliothèques Azure pour Java, suivez les instructions de l’article [Prise en main des bibliothèques de gestion Azure pour Java][maven-getting-started].

### Étape 2 : Configurer vos paramètres Maven avec les dépendances requises ###

Une fois votre projet configuré afin d’utiliser Maven pour la génération, vous pouvez ajouter les dépendances nécessaires au fichier pom.xml à l'aide d’une syntaxe semblable à celle de l'exemple suivant. Notez que vous ne devez pas ajouter chaque dépendance répertoriée dans l'exemple suivant, mais uniquement les dépendances spécifiques nécessaires à votre projet.

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

**Remarque** : dans chaque élément `<version>` de l’exemple précédent, remplacez les espaces réservés « n.n.n » de cet exemple par des numéros de version valides, que vous pouvez obtenir dans le [référentiel des bibliothèques Azure sur Maven][].

## Installation du kit de ressources Azure pour Eclipse ##

Cette section contient des instructions de base pour l'installation du kit de ressources Azure pour Eclipse ; pour obtenir des instructions détaillées, consultez la page [Installer le kit de ressources Azure pour Eclipse][].

### Composants requis ###

1. Systèmes d’exploitation Windows répertoriés dans l’article [Nouveautés du Kit de ressources Azure pour Eclipse][].
1. Systèmes d’exploitation Macintosh ou Linux répertoriés dans l’article [Nouveautés du Kit de ressources Azure pour Eclipse][].
1. IDE (environnement de développement intégré) Eclipse pour développeurs Java EE, Indigo ou ultérieur, Vous pouvez le télécharger à partir de <http://www.eclipse.org/downloads/>.

### Procédure d’installation de base ###

1. Dans Eclipse, à partir du menu **Aide**, sélectionnez **Installer un nouveau logiciel**.
1. Saisissez l’emplacement du site <http://dl.msopentech.com/eclipse> et appuyez sur **Entrée**.
1. Sélectionnez les éléments à installer, puis cliquez sur **Terminer**.

Le kit de ressources Azure pour Eclipse utilise la dernière version du Kit de développement logiciel (SDK) Azure. Il est possible de le télécharger avec WebPI (Web Platform Installer) à l’adresse <http://go.microsoft.com/fwlink/?LinkID=252838>. Toutefois, s’il n’est pas installé, lorsque vous créez le premier projet de déploiement Azure, le Kit de ressources Azure pour Eclipse installe automatiquement la version appropriée du Kit de développement logiciel (SDK) Azure.

## Voir aussi ##

[Kit de ressources Azure pour Eclipse][]

[Installation du kit de ressources Azure pour Eclipse][]

[Création d'une application Hello World pour Azure dans Eclipse][]

Pour plus d'informations sur l'utilisation d'Azure avec Java, consultez le [Centre de développement Java][].

<!-- URL List -->

[Centre de développement Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[référentiel des bibliothèques Azure sur Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d'une application Hello World pour Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation du kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Installer le kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Java Build Path]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Nouveautés du Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333

<!---HONumber=AcomDC_0114_2016-->