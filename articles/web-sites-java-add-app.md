<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java web site" pageTitle="Add an application to your Java web site" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java web site on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java web site" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

Ajout d'une application à votre site Web Java sur Azure
=======================================================

Ce didacticiel explique comment ajouter une page Web ou une application à un site Web Java créé préalablement à l'aide de la galerie Azure ou de l'IU de configuration Azure.

Notez que vous pouvez utiliser le contrôle de code source pour télécharger votre application ou vos pages Web, y compris dans des scénarios d'intégration continue. Pour les instructions d'utilisation du contrôle de code source avec votre site Web, consultez la rubrique [Publication à partir du contrôle de code source dans Sites Web Azure](../web-sites-publish-source-control). Cet article vous montre également comment utiliser FTP plutôt que le contrôle de code source pour télécharger l'application.

Ce didacticiel suppose que vous ayez déjà suivi la procédure décrite dans [Prise en main de Sites Web Microsoft Azure et Java](../web-sites-java-get-started).

Personnalisation d'un site Web à l'aide de FTP
==============================================

Vous devez déterminer vos informations d'identification FTP et les utiliser pour accéder au contenu du site Web. Ensuite, vous pourrez modifier le contenu afin d'exécuter votre application. L'exemple présenté dans cet article utilise l'Explorateur de fichiers pour accéder au FTP, mais vous pouvez également utiliser d'autres techniques FTP.

Utilisation des informations d'identification FTP pour accéder au contenu de votre site Web
-------------------------------------------------------------------------------------------

1.  Dans le portail de gestion Azure, accédez à l'écran **Sites Web**.
2.  Dans l'écran **Sites Web**, cliquez sur le nom de votre site Web.
3.  Cliquez sur **Tableau de bord**.
4.  À l'écran **Tableau de bord**, sous **Aperçu rapide**, cliquez sur **Télécharger le profil de publication**. Enregistrez ce fichier localement. Veillez à l'enregistrer dans un emplacement sécurisé, car il contient le nom d'utilisateur et le mot de passe qui vous permettent de publier du contenu sur votre site (et de copier le contenu de votre site).
5.  Ouvrez le fichier de paramètres de publication téléchargé à l'aide d'un éditeur de texte. Dans ce fichier, prenez note des valeurs **userName** et **userPwd**. Elles correspondent respectivement au nom d'utilisateur et au mot de passe que vous devrez utiliser pour accéder aux fichiers du site.
6.  Accédez aux fichiers de votre site Web en entrant le nom d'utilisateur et le mot de passe lorsque vous y êtes invité. Cet exemple utilise FTP depuis l'Explorateur de fichiers, mais vous pouvez également utiliser d'autres techniques. Pour utiliser FTP, cliquez sur l'URL indiquée sous **Nom de l'hôte FTP** à l'écran **Tableau de bord**. (Vous pouvez également déterminer le nom de l'hôte FTP à partir du fichier de paramètres de publication. Dans ce fichier, il est indiqué sous **publishUrl**.)
7.  Lorsque vous y êtes invité, entrez le nom d'utilisateur et le mot de passe. Il s'agit des valeurs indiquées sous **userName** et **userPwd** dans le fichier de paramètres de publication.
8.  Toujours dans l'Explorateur de fichiers, pour passer à la vue Explorateurs de fichiers, cliquez sur **Affichage**, puis sur **Ouvrir le site FTP dans l'Explorateur de fichiers**.

Accès au dossier webapps de votre site Web
------------------------------------------

Une fois dans la vue de l'Explorateur de fichiers de votre site Web sur Azure, vous pouvez personnaliser votre site Web. Pour cela, vous devez copier votre application dans le dossier **webapps** de votre site Web. Le chemin d'accès à ce dossier varie en fonction de la configuration de votre site Web.

-   Si vous avez configuré votre site Web à l'aide de la galerie d'applications Azure, dans l'Explorateur de fichiers, double-cliquez sur **site**, double-cliquez sur **wwwroot**, double-cliquez sur **bin**, double-cliquez sur la version du serveur d'applications que votre site Web utilise, puis double-cliquez sur **webapps**.
-   Si vous avez configuré votre site Web à l'aide de l'IU de configuration Azure, dans l'Explorateur de fichiers, double-cliquez sur **site**, double-cliquez sur **wwwroot**, puis double-cliquez sur **webapps**.
-   Si vous avez configuré votre site Web à l'aide d'un téléchargement personnalisé, accédez au dossier **webapps** comme requis.

Pour ajouter un fichier WAR à votre site Web à l'aide de FTP
------------------------------------------------------------

1.  Accédez au dossier **webapps** à l'aide de la technique appropriée pour votre site Web, comme décrit ci-dessus.
2.  Copiez le fichier WAR dans le dossier **webapps**.

Le serveur d'applications détecte que vous avez ajouté un fichier WAR et le charge automatiquement. Vous pouvez ensuite exécuter votre application dans le navigateur, via l'URL de votre site Web à laquelle vous aurez ajouté le nom du fichier WAR en suffixe.

Par exemple, accédez à http://*nomdemonsite*.azurewebsites.net/*monwar*, où *nomdemonsite* correspond au nom que vous avez indiqué pour votre URL et *monwar* au nom (sensible à la casse) du fichier WAR que vous avez copié (sans l'extension **.war**).

Pour ajouter une page Web à votre site Web à l'aide de FTP
----------------------------------------------------------

1.  Accédez au dossier **webapps**.
2.  Créez un dossier dans le dossier **webapps**.
3.  Ouvrez le nouveau dossier.
4.  Ajoutez votre page Web au nouveau dossier.

Le serveur d'applications détecte que vous avez ajouté un dossier et un fichier Web et les charge automatiquement. Ensuite, exécutez le fichier JSP à l'aide de l'URL sous la forme http://*nomdemonsite*.azurewebsites.net/*mondossier*/*monfichier.jsp*, où *nomdemonsite* correspond au nom que vous avez indiqué pour votre URL, *mondossier* au dossier que vous avez créé dans le dossier **webapps** et *monfichier.jsp* au nom du fichier JSP que vous avez créé.

Notez que si vous copiez des fichiers (autres que des fichiers WAR) dans le répertoire ROOT, vous devez redémarrer le serveur d'applications avant d'utiliser ces fichiers. La fonctionnalité de chargement automatique des sites Web Java exécutés sur Azure est basée sur l'ajout d'un fichier WAR ou sur les fichiers ou répertoires ajoutés au dossier **webapps**.

