<properties 
	pageTitle="Gestion des performances des applications New Relic sur Azure" 
	description="Découvrez comment utiliser l'analyse des performances de New Relic sur Azure." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="stepsic"/>



#Gestion des performances des applications New Relic sur les sites Web Azure

Ce guide explique comment ajouter la surveillance des performances New Relic de pointe à votre site Web Azure. Nous aborderons les processus rapides et simples permettant d'ajouter New Relic à votre application et vous présenterons certaines des
fonctionnalités de New Relic. Pour plus d'informations sur l'utilisation de New Relic, consultez la section [Utilisation de New Relic](#using-new-relic).

Présentation de New Relic
--

New Relic est un outil destiné aux développeurs qui surveille vos applications de production et offre des informations approfondies sur leurs performances et leur fiabilité. Ce produit est conçu pour vous permettre de gagner du temps lors de l'identification et du diagnostic des problèmes de performances et il vous donne les informations requises pour résoudre ces problèmes en un instant.

New Relic effectue le suivi de la durée du chargement et du débit de votre transaction Web, à la fois à partir du serveur et des navigateurs des utilisateurs. Il indique le temps que vous passez dans la base de données, analyse les requêtes lentes et les requêtes Web, fournit une surveillance et des alertes en matière de temps d'activité, effectue le suivi des exceptions d'application et bien plus encore.

Tarification spéciale New Relic par l'intermédiaire d'Azure Store
--

New Relic Standard est gratuit pour les utilisateurs d'Azure.
New Relic Pro est fourni dans plusieurs packages selon le mode de site Web que vous utilisez et la taille d'instance si vous utilisez le mode réservé.

Pour obtenir des informations de tarification, consultez la page [New Relic dans l'Azure Store](http://azure.microsoft.com/gallery/store/new-relic/new-relic/).

> [AZURE.NOTE] la tarification n'est répertoriée que jusqu'à 10 instances de calcul. Pour un nombre supérieur à 10, contactez New Relic (sales@newrelic.com) pour une tarification en gros.

Les clients d'Azure reçoivent un abonnement de 2 semaines gratuit à New Relic Pro lorsqu'ils déploient l'agent New Relic.

Inscription à New Relic à l'aide de l'Azure Store
--

New Relic s'intègre en toute transparence aux rôles Web, aux rôles de travail ainsi qu'aux sites Web Azure.

Pour vous inscrire à New Relic directement depuis l'Azure Store, procédez comme suit.

### Étape 1. Inscription par l'intermédiaire de l'Azure Store

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Dans le volet inférieur du portail de gestion, cliquez sur **Nouveau**.
3. Cliquez sur **Store**.
4. Dans la boîte de dialogue **Choisir un module complémentaire**, sélectionnez **New Relic** puis cliquez sur **Suivant**.
5. Dans la boîte de dialogue **Personnaliser un module complémentaire**, sélectionnez le plan New Relic de votre choix.
7. Entrez un nom pour le service New Relic, qui apparaîtra dans vos paramètres Azure, ou utilisez la valeur par défaut **NewRelic**. Le nom doit être unique dans la liste des éléments de l'Azure Store auxquels vous êtes abonné.
8. Choisissez une valeur pour la région ; par exemple **Bretagne**.
9. Cliquez sur **Suivant**.
10. Dans la boîte de dialogue **Revoir l'achat**, passez en revue le plan et les informations tarifaires, ainsi que les conditions juridiques. Si vous acceptez ces dernières, cliquez sur **Acheter**.
11. Une fois que vous avez cliqué sur **Acheter**, votre compte New Relic commence le processus de création. Vous pouvez surveiller le statut dans le portail de gestion Azure.
12. Pour récupérer votre clé de licence New Relic, cliquez sur le module complémentaire que vous venez de créer, puis cliquez sur **Informations de connexion**. 
13. Copiez la clé de licence qui apparaît. Vous devez la saisir lorsque vous installez le package Nuget New Relic.

### Étape 2. Installation du package New Relic

Le nouvel agent Sites Web New Relic est distribué en tant que package NuGet, qui peut être ajouté à votre site Web grâce à Visual Studio ou WebMatrix. Si vous ne savez pas utiliser Visual Studio ou WebMatrix avec un site Web Azure, consultez les pages [Déploiement d'une application Web ASP.NET sur un site Web Azure avec Visual Studio][vswebsite] ou [Développement et déploiement d'un site Web avec Microsoft WebMatrix][webmatrixwebsite].

Procédez comme suit pour l'environnement de développement spécifique que vous utilisez :

**Visual Studio**

1. Ouvrez votre solution de site Web Visual Studio.

2. Ouvrez la console du Gestionnaire de package en sélectionnant **Outils > Gestionnaire de package de bibliothèques > Console du Gestionnaire de package**. Définissez votre projet sur le Projet par défaut dans la partie supérieure de la fenêtre Console du Gestionnaire de package.

	![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3. Dans l'invite de commandes Gestionnaire de package, utilisez la commande suivante pour installer le package :

		Install-Package NewRelic.Azure.WebSites

4. Lorsque vous y êtes invité, saisissez la clé de licence que vous avez reçue de l'Azure Store.

	![enter license key][vslicensekey]

<!--5. Facultatif : lorsque vous y êtes invité, saisissez le nom de votre application tel qu'il apparaît dans le tableau de bord New Relic. Ou utilisez le nom de votre solution par défaut.

	![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1. Ouvrez votre site Web avec WebMatrix.

2. Dans l'onglet **Accueil** du ruban, sélectionnez **NuGet**.

	![nuget buton on home tab][wmnugetbutton]

3. Dans NuGet Gallery, définissez la source sur **NuGet Official Package Source**, puis recherchez NewRelic.Azure.WebSites.

	![nuget gallery searching for NewRelic.Azure.WebSites][wmnugetgallery]

4. Sélectionnez l'entrée **New Relic for Azure Websites**, puis cliquez sur **Installer**.

5. Après l'installation du package, votre site contiendra un dossier nommé **newrelic**. Développer ce dossier et ouvrez le fichier **newrelic.config**. Dans ce fichier, remplacez la valeur **REPLACE\_WITH\_LICENSE_KEY** par la clé de licence que vous avez reçue à partir de l'Azure Store.

	![newrelic folder expanded with newrelic.conf selected][newrelicconf]

	Après l'ajout des informations de votre clé de licence, enregistrez les modifications dans le fichier **newrelic.config**.

### Étape 3. Configuration du site Web et publication de l'application.

Le package New Relic ajouté à votre application lors de l'étape précédente se configure grâce à l'option **Paramètres de l'application** ajoutée à votre site Web Azure. Effectuez les étapes suivantes pour ajouter ces paramètres.

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com) et accédez à votre site Web.

2. Sur votre site Web, sélectionnez **Configurer**. Dans la section **Developer Analytics**, sélectionnez **Module complémentaire** ou **Personnalisé**. Les deux méthodes offrent le même résultat, mais elles nécessitent une entrée légèrement différente. **Module complémentaire** répertorie vos licences New Relic en cours et vous permet d'en sélectionner une, tandis que **Personnalisé** nécessite que vous spécifiez manuellement la clé de licence.

	Si vous avez sélectionné **Module complémentaire**, utilisez le champ **Choisir un module complémentaire** pour sélectionner votre licence New Relic.

	![Image of the add-on fields][add-on]

	Si vous avez sélectionné **Personnalisé**, utilisez New Relic en tant que **Fournisseur**, puis entrez votre licence dans le champ **Clé de fournisseur**.

	![Image of the custom fields][custom]

3. Après avoir spécifié la licence dans **Developer Analytics**, cliquez sur **Enregistrer**. Une fois l'opération d'enregistrement terminée, les valeurs suivantes seront ajoutées dans la section **Paramètres de l'application** de la page pour prendre en charge New Relic :

	<table border="1">
	<thead>
	<tr>
	<td>Clé</td>
	<td>Valeur</td>
	</tr>
	</thead>
	<tbody>
	<tr>
	<td>COR\_ENABLE\_PROFILING</td><td>1</td>
	</tr>
	<tr>
	<td>COR\_PROFILER</td><td>{71DA0A04-7777-4EC6-9643-7D28B46A8A41}</td>
	</tr>
	<tr>
	<td>COR\_PROFILER\_PATH</td><td>C:\Home\site\wwwroot\newrelic\NewRelic.Profiler.dll</td>
	</tr>
	<tr>
	<td>NEWRELIC\_HOME</td><td>C:\Home\site\wwwroot\newrelic</td>
	</tr>
	<tr>
	<td>NEWRELIC\_LICENSEKEY</td><td>Votre clé de licence</td>
	</tr>
	</tbody>
	</table><br/>

	> [AZURE.NOTE] La prise d'effet des nouveaux <strong>Paramètres de l'application</strong> peut prendre jusqu'à 30 secondes. Pour forcer la prise d'effet immédiate des paramètres, redémarrez le site Web.


4. Publiez votre application grâce à Visual Studio ou WebMatrix.

### Étape 4. Vérification des performances de votre application dans New Relic.

Pour consulter votre tableau de bord New Relic :

1. Depuis le portail Azure, cliquez sur le bouton **Gérer**.
2. Connectez-vous avec l'adresse électronique et le mot de passe de votre compte New Relic.
3. Dans la barre de menus New Relic, sélectionnez **Applications > (nom de l'application)**.

	Le tableau de bord **Surveillance > Vue d'ensemble** s'affiche automatiquement.

	![New Relic monitoring dashboard](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

	Une fois que vous avez sélectionné une application dans la liste de votre menu **Applications**, le tableau de bord **Vue d'ensemble** affiche les informations actuelles sur le serveur et le navigateur de l'application.

### <a id="using-new-relic"></a>Utilisation de New Relic

Une fois que vous avez sélectionné votre application dans la liste du menu Applications, le tableau de bord Vue d'ensemble affiche les informations actuelles sur le serveur et le navigateur de l'application. Pour basculer entre les deux vues, cliquez sur le bouton **Serveur d'applications** ou **Navigateur**.

En plus des fonctions de l'<a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">interface utilisateur standard New Relic</a> et <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">d'exploration au niveau du détail du tableau de bord</a>, le tableau de bord Vue d'ensemble des applications comporte des fonctions supplémentaires.

<table border="1">
  <thead>
    <tr>
      <th><b>Si vous voulez...</b></th>
      <th><b>Procédez comme suit...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Afficher les informations du tableau de bord pour le serveur ou le navigateur de l'application sélectionnée</td>
       <td>Cliquez sur le bouton <b>App Server</b> ou <b>Navigateur</b>.</td>
    </tr>
     <tr>
       <td>Afficher les niveaux de seuil du score <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex </a>pour votre application</td>
       <td>Pointez le curseur sur l'icône<b>?<b> du score Apdex.</b></b></td>
    </tr>
    <tr>
       <td>Afficher les détails Apdex du monde entier</td>
       <td>Depuis la vue <b>Navigateur</b> de la Vue d'ensemble, pointez le curseur n'importe où sur la carte Global Apdex.<br /><b>Conseil :</b> Pour accéder directement au tableau de bord <a href="https://newrelic.com/docs/site/geography" target="_blank">Géographie</a> de l'application sélectionnée, cliquez sur le titre <b>Global Apdex</b> ou n'importe où dans la carte Global Apdex.</td>
    </tr>
    <tr>
       <td>Afficher le tableau de bord <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web Transactions</a></td>
       <td>Cliquez sur la table Web Transactions du tableau de bord Vue d'ensemble des applications. Ou, pour afficher les détails d'une transaction Web spécifique (y compris les <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">transactions clés</a>), cliquez sur son nom.</td>
    </tr>
    <tr>
       <td>Afficher le tableau de bord <a href="https://newrelic.com/docs/site/errors" target="_blank">Erreurs</a></td>
       <td>Cliquez sur le titre du graphique de taux d'erreur du tableau de bord Vue d'ensemble des applications.<br /><b>Conseil :</b> Vous pouvez également afficher le tableau de bord Erreurs depuis <b>Applications</b> &gt; (votre application) &gt; Événements &gt; Erreurs.</td>
    </tr>
    <tr>
       <td>Afficher les détails du serveur de l'application</td>
       <td><p>Effectuez l'une des actions suivantes :<p>
        <ul>
          <li>Basculez entre une vue de la table des hôtes et les détails de mesure de chaque hôte.</li>
          <li>Cliquez sur le nom d'un serveur spécifique.</li>
          <li>Pointez le curseur sur le score Apdex d'un serveur spécifique.</li>
          <li>Cliquez sur l'utilisation de l'unité centrale ou la mémoire d'un serveur spécifique.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

Vous trouverez ci-dessous un exemple du tableau de bord Vue d'ensemble des applications lorsque vous sélectionnez la vue Navigateur.

![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

## Étapes suivantes

Pour plus d'informations, consultez les ressources supplémentaires suivantes :

 * [Installation de l'agent .NET pour les sites Web Azure](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install) : procédures d'installation de l'agent .NET New Relic. 
 * [Interface utilisateur New Relic](https://newrelic.com/docs/site/the-new-relic-ui):
vue d'ensemble de l'interface utilisateur New Relic, de la définition des droits et profils de l'utilisateur, de l'utilisation des fonctions standard et de l'exploration au niveau du détail du tableau de bord.
 * [Vue d'ensemble des applications](https://newrelic.com/docs/site/applications-overview): fonctionnalités accessibles à partir du tableau de bord Vue d'ensemble des applications de New Relic.
 * [Apdex](https://newrelic.com/docs/site/apdex): vue d'ensemble de la manière dont Apdex mesure la satisfaction des utilisateurs finaux de votre application.
 * [Surveillance des utilisateurs](https://newrelic.com/docs/features/real-user-monitoring): vue d'ensemble de la manière dont RUM détaille le temps qu'il faut aux navigateurs de vos utilisateurs pour charger vos pages web, leur provenance et les navigateurs qu'ils utilisent
 * [Recherche d'aide](https://newrelic.com/docs/site/finding-help) : ressources disponibles dans le centre d'aide en ligne de New Relic.


[webmatrixwebsite]: http://azure.microsoft.com/develop/net/tutorials/website-with-webmatrix/
[vswebsite]: http://azure.microsoft.com/develop/net/tutorials/get-started/

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png


<!--HONumber=42-->
