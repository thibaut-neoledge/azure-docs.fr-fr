<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel vous montre comment ajouter un service principal cloud &agrave; une application HTML en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez cr&eacute;er un service mobile et une simple application <em>To do list</em> qui stocke les donn&eacute;es d'application dans le nouveau service mobile. Vous pouvez regarder une version vid&eacute;o de ce didacticiel en cliquant sur le clip &agrave; droite.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">03:51:00</span></div>

</div>

Voici une capture d'écran de l'application terminée :

![][]

Vous devez suivre ce didacticiel avant de pouvoir suivre les autres didacticiels Mobile Services pour les applications HTML.

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

### Conditions supplémentaires

-   L'un des serveurs Web suivants doit être exécuté sur votre ordinateur local pour pouvoir suivre ce didacticiel :

    -   **Sur Windows** : IIS Express. IIS Express est installé par le [programme d'installation de la plate-forme Web Microsoft][].
    -   **Sur Mac OS X** : Python, qui doit déjà être installé.
    -   **Sur Linux** : Python. Vous devez installer la [dernière version de Python][].

    Vous pouvez utiliser n'importe quel serveur Web pour héberger l'application, mais les serveurs précédents sont les seuls pris en charge par les scripts téléchargés.

-   Un navigateur Web qui prend en charge le HTML5

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## 

## <span class="short-header">Création d'une application</span>Création d'une application HTML

</h2>
Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application HTML connectée à votre appareil mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Windows** sous **Choisissez une plateforme** et développez **Créer une application HTML**.

    ![][1]

    Ceci affiche les trois étapes pour créer et héberger une application HTML connecté à votre service mobile.

    ![][2]

3.  Cliquez sur **Create TodoItems table** pour créer une table permettant de stocker les données d'application.

4.  Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**.

    Ceci entraîne le téléchargement des fichiers de site Web pour l'exemple d'application *To do list* connectée à votre service mobile. Enregistrez le fichier compressé sur votre ordinateur local, et notez où vous l'enregistrez.

5.  Sous l'onglet **Configurer** vérifiez que `localhost` figure déjà dans la liste **Autoriser les demandes à partir des noms d'hôte** sous **Partage des ressources cross-origin (CORS)**. Si cela n'est pas le cas, entrez `localhost` dans le champ **Nom d'hôte**, puis cliquez sur **Enregistrer**.

    ![][3]

    <div class="dev-callout"><b>Remarque</b>
    <p>Si vous d&eacute;ployez l'application de d&eacute;marrage rapide sur un autre serveur que localhost, vous devez ajouter le nom d'h&ocirc;te du serveur Web &agrave; la liste <strong>Autoriser les demandes &agrave; partir des noms d'h&ocirc;te</strong>. Pour plus d'informations, consultez la page <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dn155871.aspx" target="_blank">Partage des ressources cross-origin</a>.</p>
</div>

## Hébergement et exécution de votre application HTML

La dernière étape de ce didacticiel consiste à héberger et exécuter votre nouvelle application sur votre ordinateur local.

1.  Accédez à l'emplacement sur lequel vous avez enregistré les fichiers du projet compressés, décompressez-les sur votre ordinateur, puis exécutez l'un des fichiers de commandes suivants à partir du sous-dossier **serveur**.

    -   **launch-windows** (pour les ordinateurs Windows)
    -   **launch-mac.command** (pour les ordinateurs Mac OS X)
    -   **launch-linux.sh** (pour les ordinateurs Linux)

    <div class="dev-callout"><b>Remarque</b>
    <p>Sur un ordinateur Windows, appuyez sur la touche &laquo;&nbsp;R&nbsp;&raquo; lorsque PowerShell vous demande de confirmer l'ex&eacute;cution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas ex&eacute;cuter le script, car il a &eacute;t&eacute; t&eacute;l&eacute;charg&eacute; depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer &agrave; charger le script.</p>
</div>

    Ceci démarre un serveur Web sur votre ordinateur local pour pouvoir héberger la nouvelle application.

2.  Ouvrez l'URL <http://localhost:8000/> dans un navigateur Web pour démarrer l'application.

3.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, dans **Entrer une nouvelle tâche**, puis cliquez sur **Ajouter**.

    ![][4]

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la deuxième colonne de l'application.

    <div class="dev-callout"> 
<b>Remarque</b> 
<p>Vous pouvez v&eacute;rifier le code qui se trouve dans le fichier app.js et permet d'acc&eacute;der au service mobile pour ex&eacute;cuter une requ&ecirc;te et ins&eacute;rer des donn&eacute;es.</p> 
</div>

4.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

    ![][5]

    Cela vous permet de parcourir les données insérées par l'application dans la table.

    ![][6]

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   **[Prise en main des données][]**

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   **[Appel d'une API personnalisée à partir d'une application HTML][]**

    Connectez votre application HTML à l'aide d'une API personnalisée hébergée sur Mobile Services.

-   **[Prise en main de l'authentification][]**

    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   **[Guide de fonctionnement Mobile Services HTML/JavaScript][]**
     
    : découvrez plus en détail comment utiliser Mobile Services avec le HTML/JavaScript

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [regarder le didacticiel]: http://go.microsoft.com/fwlink/?LinkId=287040
  []: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F
  [programme d'installation de la plate-forme Web Microsoft]: http://go.microsoft.com/fwlink/p/?LinkId=286333
  [dernière version de Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
  [2]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png
  [3]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
  [Partage des ressources cross-origin]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn155871.aspx
  [4]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
  [5]: ./media/mobile-services-html-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-html-get-started/mobile-data-browse.png
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-html
  [Appel d'une API personnalisée à partir d'une application HTML]: /fr-fr/documentation/articles/mobile-services-html-call-custom-api
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-html
  [Guide de fonctionnement Mobile Services HTML/JavaScript]: /fr-fr/develop/mobile/how-to-guides/work-with-html-js-client
