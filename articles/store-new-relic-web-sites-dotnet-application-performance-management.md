<properties linkid="develop-net-how-to-guides-new-relic-app" urlDisplayName="New Relic App Performance Management" pageTitle="New Relic App Performance Management on Azure" metaKeywords="new relic Azure, performance azure" description="Learn how to use New Relic's performance monitoring on Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="New Relic Application Performance Management on Azure Websites" authors="new relic" solutions="" manager="dwrede" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="new="" relic" />

# Gestion des performances des applications New Relic sur les sites Web Azure

Ce guide explique comment ajouter la surveillance
des performances New Relic de pointe à votre site Web Azure. Nous aborderons les processus
rapides et simples permettant d'ajouter New Relic à votre application et vous présenterons certaines des nouvelles
fonctionnalités de New Relic. Pour plus d'informations sur l'utilisation de New Relic, consultez la section [Utilisation de New Relic][Utilisation de New Relic].

## Présentation de New Relic

New Relic est un outil destiné aux développeurs qui surveille vos applications de production
et offre des informations approfondies sur leurs performances et leur fiabilité. Ce produit
est conçu pour vous permettre de gagner du temps lors de l'identification et du diagnostic des problèmes de performances et
il vous donne les informations requises pour résoudre ces problèmes en un instant.

New Relic effectue le suivi de la durée du chargement et du débit de votre transaction Web, à la fois à partir
du serveur et des navigateurs des utilisateurs. Il indique le temps que vous passez dans la
base de données, analyse les requêtes lentes et les requêtes Web, fournit une surveillance et
des alertes en matière de temps d'activité, effectue le suivi des exceptions d'application et bien plus encore.

## Tarification spéciale New Relic par l'intermédiaire d'Azure Store

New Relic Standard est gratuit pour les utilisateurs d'Azure.
New Relic Pro est fourni dans plusieurs packages selon le mode de site Web que vous utilisez et la taille d'instance si vous utilisez le mode réservé.

Pour des informations de tarification, consultez la [page New Relic dans l'Azure Store][page New Relic dans l'Azure Store].

<div class="dev-callout"> 
<strong>Remarque&nbsp;:</strong>
<p>la tarification n'est r&eacute;pertori&eacute;e que jusqu'&agrave;&nbsp;10 instances de calcul. Pour un nombre sup&eacute;rieur &agrave; 10, contactez New&nbsp;Relic (sales@newrelic.com) pour une tarification en gros.</p>
</div>

Les clients d'Azure reçoivent un abonnement de 2 semaines gratuit à New Relic Pro lorsqu'ils déploient l'agent New Relic.

## Inscription à New Relic à l'aide de l'Azure Store

New Relic s'intègre en toute transparence aux rôles Web, aux rôles de travail ainsi qu'aux sites Web Azure.

Pour vous inscrire à New Relic directement depuis l'Azure Store, procédez comme suit.

### Étape 1 : inscription par l'intermédiaire de l'Azure Store

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Dans le volet inférieur du portail de gestion, cliquez sur **Nouveau**.
3.  Cliquez sur **Store**.
4.  Dans la boîte de dialogue **Choisir un module complémentaire**, sélectionnez **New Relic**, puis cliquez sur **Suivant**.
5.  Dans la boîte de dialogue **Personnaliser un module complémentaire**, sélectionnez le plan New Relic de votre choix.
6.  Saisissez un nom pour le service New Relic, qui apparaîtra dans vos paramètres
     Azure, ou utilisez la valeur par défaut **NewRelic**. Le nom doit être unique dans
     la liste des éléments de l'Azure Store auxquels vous êtes abonné.
7.  Choisissez une valeur pour la région ; par exemple, **West US**.
8.  Cliquez sur **Suivant**.
9.  Dans la boîte de dialogue **Revoir l'achat**, passez en revue le plan et les informations tarifaires,
    ainsi que les conditions juridiques. Si vous acceptez ces dernières, cliquez sur **Acheter**.
10. Une fois que vous avez cliqué sur **Acheter**, votre compte New Relic commence le processus de création. Vous pouvez surveiller le statut dans le portail de gestion Azure.
11. Pour récupérer votre clé de licence New Relic, cliquez sur le module complémentaire que vous venez de créer, puis cliquez sur **Informations sur la connexion**.
12. Copiez la clé de licence qui apparaît. Vous devez la saisir lorsque vous installez le package Nuget New Relic.

### Étape 2 : installation du package New Relic

Ouvrez votre solution de site Web Visual Studio.

Procédez comme suit pour l'environnement de développement spécifique que vous utilisez :

**Visual Studio**

1.  Ouvrez votre solution de site Web Visual Studio.

2.  Ouvrez la console du Gestionnaire de package en sélectionnant **Outils \> Gestionnaire de package de bibliothèques \>
     Console du Gestionnaire de package**. Définissez votre projet comme Projet par défaut dans la
     partie supérieure de la fenêtre Console du Gestionnaire de package.

    ![Console Gestionnaire de package][Console Gestionnaire de package]

3.  Dans l'invite de commandes Gestionnaire de package, utilisez la commande suivante pour installer le package :

        Install-Package NewRelic.Azure.WebSites

4.  Lorsque vous y êtes invité, saisissez la clé de licence que vous avez reçue de l'Azure Store.

    ![saisir la clé de licence][saisir la clé de licence]

<!--5. Optional: At the application name prompt, enter your app's name as it will    appear in New Relic's dashboard. Or, use your solution name as the default.      ![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1.  Ouvrez votre site Web avec WebMatrix.

2.  Dans l'onglet **Accueil** du ruban, sélectionnez **NuGet**.

    ![Bouton Nuget dans l'onglet Accueil][Bouton Nuget dans l'onglet Accueil]

3.  Dans NuGet Gallery, définissez la source sur **NuGet Official Package Source**, puis recherchez NewRelic.Azure.WebSites.

    ![Nuget Gallery, recherche de NewRelic.Azure.WebSites][Nuget Gallery, recherche de NewRelic.Azure.WebSites]

4.  Sélectionnez l'entrée **New Relic for Azure Websites**, puis cliquez sur **Installer**.

5.  Après l'installation du package, votre site contiendra un dossier nommé **newrelic**. Développez ce dossier et ouvrez le fichier **newrelic.config**. Dans ce fichier, remplacez la valeur **REPLACE\_WITH\_LICENSE\_KEY** par la clé de licence que vous avez reçue à partir de l'Azure Store.

    ![Dossier newrelic développé avec newrelic.conf sélectionné][Dossier newrelic développé avec newrelic.conf sélectionné]

    Après l'ajout des informations de votre clé de licence, enregistrez les modifications dans le fichier **newrelic.config**.

### Étape 3 : configuration du site Web et publication de l'application.

Le package New Relic ajouté à votre application lors de l'étape précédente se configure grâce à l'option **Paramètres de l'application** ajoutée à votre site Web Azure. Effectuez les étapes suivantes pour ajouter ces paramètres.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure] et accédez à votre site Web.

2.  Sur votre site Web, sélectionnez **Configurer**. Dans la section **Analyse développeur**, sélectionnez **Module complémentaire** ou **Personnalisé**. Les deux méthodes offrent le même résultat, mais elles nécessitent une entrée légèrement différente. **Module complémentaire** répertorie vos licences New Relic en cours et vous permet d'en sélectionner une, tandis que **Personnalisé** nécessite que vous spécifiiez manuellement la clé de licence.

    Si vous avez sélectionné **Module complémentaire**, utilisez la zone **Choisir un module complémentaire** pour sélectionner votre licence New Relic.

    ![Image des champs de modules complémentaires][Image des champs de modules complémentaires]

    Si vous avez sélectionné **Personnalisé**, utilisez New Relic en tant que **Fournisseur**, puis entrez votre licence dans la zone **Clé de fournisseur**.

    ![Image des champs personnalisés][Image des champs personnalisés]

3.  Après avoir spécifié la licence dans **Analyse développeur**, cliquez sur **Enregistrer**. Une fois l'opération d'enregistrement terminée, les valeurs suivantes seront ajoutées dans la section **Paramètres de l'application** de la page pour prendre en charge New Relic :

    <table border="1">
    <thead>
    <tr>
    <td>
    Clé

    </td>
    <td>
    Valeur

    </td>
    </tr>
    </thead>
    <tbody>
    <tr>
    <td>
    COR_ENABLE_PROFILING

    </td>
    <td>
    1

    </td>
    </tr>
    <tr>
    <td>
    COR_PROFILER

    </td>
    <td>
    {71DA0A04-7777-4EC6-9643-7D28B46A8A41}

    </td>
    </tr>
    <tr>
    <td>
    COR_PROFILER_PATH

    </td>
    <td>
    C:\Home\site\wwwroot\newrelic\NewRelic.Profiler.dll

    </td>
    </tr>
    <tr>
    <td>
    NEWRELIC_HOME

    </td>
    <td>
    C:\Home\site\wwwroot\newrelic

    </td>
    </tr>
    <tr>
    <td>
    NEWRELIC_LICENSEKEY

    </td>
    <td>
    Votre clé de licence

    </td>
    </tr>
    </tbody>
    </table>

    <div class="dev-callout"> 
<strong>Remarque</strong> 
<p>La prise d'effet des nouveaux <strong>Param&egrave;tres de l'application</strong> peut prendre jusqu'&agrave; 30&nbsp;secondes. Pour forcer la prise d'effet imm&eacute;diate des param&egrave;tres, red&eacute;marrez le site Web.</p> 
</div>

4.  Publiez votre application grâce à Visual Studio ou WebMatrix.

### Étape 4 : vérification des performances de votre application dans New Relic

Pour consulter votre tableau de bord New Relic :

1.  Depuis le portail Azure, cliquez sur le bouton **Gérer**.
2.  Connectez-vous avec l'adresse électronique et le mot de passe de votre compte New Relic.
3.  Dans la barre de menus New Relic, sélectionnez **Applications \> (nom de l'application)**.

    Le tableau de bord **Surveillance \> Présentation** s'affiche automatiquement.

    ![Tableau de surveillance New Relic][Tableau de surveillance New Relic]

    Une fois que vous avez sélectionné une application dans la liste de votre menu **Applications**, le tableau de bord **Présentation** affiche les informations actuelles sur le serveur et le navigateur de l'application.

### <span id="using-new-relic"></span></a>Utilisation de New Relic

Une fois que vous avez sélectionné votre application dans la liste du menu Applications, le tableau de bord Présentation affiche les informations actuelles sur le serveur et le navigateur de l'application. Pour basculer entre les deux vues, cliquez sur le bouton **App server** ou **Navigateur**.

En plus des fonctions de l'[interface utilisateur standard New Relic][interface utilisateur standard New Relic] et [d'exploration au niveau du détail du tableau de bord][d'exploration au niveau du détail du tableau de bord], le tableau de bord Vue d'ensemble des applications comporte des fonctions supplémentaires.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="center"><strong>Si vous voulez...</strong></th>
<th align="center"><strong>Procédez comme suit...</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Afficher les informations du tableau de bord pour le serveur ou le navigateur de l'application sélectionnée</td>
<td align="left">Cliquez sur le bouton <strong>App Server</strong> ou <strong>Navigateur</strong>.</td>
</tr>
<tr class="even">
<td align="left">Afficher les niveaux de seuil du score <a href="https://newrelic.com/docs/site/apdex">Apdex</a> de votre application</td>
<td align="left">Pointez le curseur sur l'icône <strong>? <strong>du score Apdex.</strong></strong></td>
</tr>
<tr class="odd">
<td align="left">Afficher les détails Apdex du monde entier</td>
<td align="left">Depuis la vue <strong>Navigateur</strong> du tableau de bord Présentation, pointez le curseur n'importe où sur la carte Global Apdex.<br /><strong>Conseil :</strong> pour accéder directement au tableau de bord <a href="https://newrelic.com/docs/site/geography">Géographie</a> de l'application sélectionnée, cliquez sur le titre <strong>Global Apdex</strong> ou n'importe où dans la carte Global Apdex.</td>
</tr>
<tr class="even">
<td align="left">Afficher le tableau de bord <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard">Web Transactions</a></td>
<td align="left">Cliquez sur la table Web Transactions du tableau de bord Vue d'ensemble des applications. Ou, pour afficher les détails d'une transaction Web spécifique (y compris <a href="https://newrelic.com/docs/site/key-transactions">les transactions clés</a>), cliquez sur son nom.</td>
</tr>
<tr class="odd">
<td align="left">Afficher le tableau de bord <a href="https://newrelic.com/docs/site/errors">Erreurs</a></td>
<td align="left">Cliquez sur le titre du graphique de taux d'erreur du tableau de bord Vue d'ensemble des applications.<br /><strong>Conseil :</strong> vous pouvez également afficher le tableau de bord Erreurs depuis <strong>Applications</strong> &gt; (votre application) &gt; Événements &gt; Erreurs.</td>
</tr>
<tr class="even">
<td align="left">Afficher les détails du serveur de l'application</td>
<td align="left"><p>Effectuez l'une des actions suivantes :</p>
<p></p>
<ul>
<li>Basculez entre une vue de la table des hôtes et les détails de mesure de chaque hôte.</li>
<li>Cliquez sur le nom d'un serveur spécifique.</li>
<li>Pointez le curseur sur le score Apdex d'un serveur spécifique.</li>
<li>Cliquez sur l'utilisation de l'unité centrale ou la mémoire d'un serveur spécifique.</li>
</ul>
</p>
</p></td>
</tr>
</tbody>
</table>

Vous trouverez ci-dessous un exemple du tableau de bord Vue d'ensemble des applications lorsque vous sélectionnez la vue Navigateur.

![Console Gestionnaire de package][1]

## Étapes suivantes

Pour plus d'informations, consultez les ressources supplémentaires suivantes :

-   [Installation de l'agent .NET pour les sites Web Azure][Installation de l'agent .NET pour les sites Web Azure] : procédures d'installation de l'agent .NET New Relic.
-   [Interface utilisateur New Relic][Interface utilisateur New Relic] :
    vue d'ensemble de l'interface utilisateur New Relic, de la définition des droits et profils de l'utilisateur, de l'utilisation des fonctions standard et de l'exploration au niveau du détail du tableau de bord.
-   [Vue d'ensemble des applications][Vue d'ensemble des applications] : fonctionnalités accessibles à partir du tableau de bord Vue d'ensemble des applications de New Relic.
-   [Apdex][Apdex] : vue d'ensemble de la manière dont Apdex mesure la satisfaction des utilisateurs finaux de votre application.
-   [Surveillance des utilisateurs][Surveillance des utilisateurs] : vue d'ensemble de la manière dont RUM détaille le temps qu'il faut aux navigateurs
    de vos utilisateurs pour charger vos pages Web, leur provenance et les navigateurs qu'ils utilisent.
-   [Recherche d'aide][Recherche d'aide] : ressources disponibles dans le centre d'aide en ligne de New Relic.

  [Utilisation de New Relic]: #using-new-relic
  [page New Relic dans l'Azure Store]: http://www.windowsazure.com/fr-fr/gallery/store/new-relic/new-relic/
  [portail de gestion Azure]: https://manage.windowsazure.com
  [Console Gestionnaire de package]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png
  [saisir la clé de licence]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
  [Bouton Nuget dans l'onglet Accueil]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
  [Nuget Gallery, recherche de NewRelic.Azure.WebSites]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png
  [Dossier newrelic développé avec newrelic.conf sélectionné]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
  [Image des champs de modules complémentaires]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
  [Image des champs personnalisés]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
  [Tableau de surveillance New Relic]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png
  [interface utilisateur standard New Relic]: https://newrelic.com/docs/site/the-new-relic-ui#functions
  [d'exploration au niveau du détail du tableau de bord]: https://newrelic.com/docs/site/the-new-relic-ui#drilldown
  [Apdex]: https://newrelic.com/docs/site/apdex
  [1]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png
  [Installation de l'agent .NET pour les sites Web Azure]: https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install
  [Interface utilisateur New Relic]: https://newrelic.com/docs/site/the-new-relic-ui
  [Vue d'ensemble des applications]: https://newrelic.com/docs/site/applications-overview
  [Surveillance des utilisateurs]: https://newrelic.com/docs/features/real-user-monitoring
  [Recherche d'aide]: https://newrelic.com/docs/site/finding-help
