<properties
    pageTitle="Affichage du contenu Javadoc dans Eclipse pour le package de bibliothèques Azure pour Java"
    description="Comment afficher le contenu Javadoc pour les bibliothèques Azure dans Eclipse."
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
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# Affichage du contenu Javadoc dans Eclipse pour le package de bibliothèques Azure pour Java #

Vous pouvez afficher le contenu Javadoc pour les bibliothèques Azure pour Java dans votre environnement Eclipse en associant ce contenu aux bibliothèques Azure pour Java. Les étapes suivantes montrent comment utiliser cette fonctionnalité dans Eclipse.

Cette procédure part du principe que vous avez déjà ajouté la bibliothèque Azure pour Java à votre chemin de build.

## Pour afficher le contenu Javadoc dans Eclipse pour les bibliothèques Azure pour Java ##

* Dans l’Explorateur de projets d’Eclipse, dans la section **Bibliothèques référencées** de votre projet, ouvrez le menu contextuel de la bibliothèque Azure pour Java JAR. Par exemple, **microsoft-Microsoft Azure-api-0.1.0.jar** (le numéro de version peut différer en fonction de la version que vous avez installée).
* Cliquez sur **Propriétés**.
* Dans la boîte de dialogue **Propriétés**, dans le volet gauche, cliquez sur **Emplacement Javadoc**. La boîte de dialogue **Emplacement Javadoc** s’affiche.
* Vous pouvez spécifier une **URL Javadoc** ou un **Javadoc dans archive**.
    * Si vous choisissez de spécifier une **URL Javadoc**, utilisez une URL telle que ****http://dl.windowsazure.com/javadoc** ou ****http://dl.windowsazure.com/storage/javadoc**.
* Si vous choisissez d’utiliser **Javadoc dans archive**, vous pouvez spécifier un fichier externe ou un fichier d’espace de travail. Faites votre choix et parcourez/validez en fonction des besoins. L’exemple suivant associe les bibliothèques Azure pour Java au JAR Javadoc correspondant téléchargé localement dans un dossier nommé **c:\\MyAzureJARs**. ![][ic553487]
* *Facultatif* : Cliquez sur **Valider**. Des problèmes potentiels avec le JAR Javadoc peuvent apparaître ici.
* Cliquez sur **OK**.

Une fois associé à la bibliothèque, le contenu Javadoc doit s’afficher dans votre IDE Eclipse. Par exemple, si `blob` est défini comme étant de type `CloudBlockBlob` dans votre code, ce qui suit est un exemple de contenu Javadoc qui apparaît lorsque vous tapez `blob.acquireLease` dans le code :

![][ic553488]

## Voir aussi ##

[Kit de ressources Azure pour Eclipse][]

[Création d’une application « Hello World » pour Azure dans Eclipse][]

[Installation de la Boîte à outils Azure pour Eclipse][]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure][].

<!-- URL List -->

[Centre de développement Java pour Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une application « Hello World » pour Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation de la Boîte à outils Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png

<!---HONumber=AcomDC_1210_2015-->