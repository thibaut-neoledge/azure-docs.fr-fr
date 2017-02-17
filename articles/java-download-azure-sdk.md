---
title: "Téléchargement du Kit de développement logiciel (SDK) Azure pour Java | Microsoft Docs"
description: "Découvrez comment télécharger le Kit de développement logiciel (SDK) Azure pour Java, avec l’exemple de code fourni pour les projets Maven."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 0933eabf99ef94f2c4b425f1435530edfde65e3f
ms.openlocfilehash: 9e55eb52797f1363cbb65050574e549c575ca5d8


---
# <a name="download-the-azure-sdk-for-java"></a>Téléchargement du Kit de développement logiciel (SDK) Azure pour Java
Cet article contient des instructions pour télécharger et installer les bibliothèques de gestion Azure pour Java.

> [!NOTE]
> Les bibliothèques de gestion Azure pour Java sont distribuées sous [Licence Apache, Version 2.0][license].
>

## <a name="azure-libraries-for-java---manual-download"></a>Bibliothèques Azure pour Java - Téléchargement manuel
Pour installer manuellement les bibliothèques de gestion Azure pour Java, cliquez sur <http://go.microsoft.com/fwlink/?LinkId=690320> pour télécharger un fichier ZIP contenant l’intégralité des bibliothèques et dépendances.

Après avoir téléchargé le fichier zip sur votre ordinateur, extrayez son contenu et utilisez une des options suivantes pour ajouter les fichiers JAR à votre projet :

* Importez les fichiers JAR dans votre projet Java dans Eclipse ou IntelliJ.
* Configurez les chemin de build de votre projet Java dans Eclipse ou IntelliJ afin d’inclure le chemin d'accès aux fichiers JAR.

> [!NOTE]
> Pour plus d’informations sur la licence, consultez les fichiers license.txt et ThirdPartyNotices.txt fournis dans le fichier .zip.
>

## <a name="azure-management-libraries-for-java---building-with-maven"></a>Bibliothèques de gestion Azure pour Java - Génération avec Maven
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Étape 1 : Configurer votre projet afin d’utiliser Maven pour la génération
Pour créer des projets Maven dans Eclipse, qui utilisent les bibliothèques de gestion Azure pour Java, suivez les instructions de l’article [Prise en main des bibliothèques de gestion Azure pour Java][maven-getting-started].

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Étape 2 : Configurer vos paramètres Maven avec les dépendances requises
Une fois votre projet configuré afin d’utiliser Maven pour la génération, vous pouvez ajouter les dépendances nécessaires au fichier pom.xml à l’aide d’une syntaxe semblable à celle de l’exemple suivant. Notez que vous ne devez pas ajouter chaque dépendance répertoriée dans l'exemple suivant, mais uniquement les dépendances spécifiques nécessaires à votre projet.

> [!NOTE]
> Dans chaque élément `<version>` de l’exemple suivant, remplacez les espaces réservés « n.n.n » de cet exemple par des numéros de version valides, que vous pouvez obtenir dans le [référentiel des bibliothèques Azure sur Maven].
>
>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
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

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur les boîtes à outils Azure pour les environnements de développement Java, consultez les liens suivants :

* [Kit de ressources Azure pour Eclipse]
  * [Installation du kit de ressources Azure pour Eclipse]
  * [Créer une application web « Hello World » pour Azure dans Eclipse]
  * [Nouveautés du kit de ressources Azure pour Eclipse]
* [Kit de ressources Azure pour IntelliJ]
  * [Installation du kit de ressources Azure pour IntelliJ]
  * [Créer une application web « Hello World » pour Azure dans IntelliJ]
  * [Nouveautés du Kit de ressources Azure pour IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

> [!NOTE]
> Pour plus d'informations sur la configuration des chemins d'accès de build dans Eclipse, consultez l’article [Java Build Path] sur le site Web d'Eclipse.
>

<!-- URL List -->

[Kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application web « Hello World » pour Azure dans Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application web « Hello World » pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installation du kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Nouveautés du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Nouveautés du Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java pour Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[référentiel des bibliothèques Azure sur Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Java Build Path]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998



<!--HONumber=Feb17_HO3-->


