<properties urlDisplayName="New Relic" pageTitle="Utilisation de New Relic avec Azure - Guides des fonctionnalités Azure" metaKeywords="" description="Apprenez à utiliser le service New Relic pour gérer et surveiller votre application Azure." metaCanonical="" services="" documentationCenter=".NET" title="New Relic Application Performance Management on Azure" authors="" solutions="" manager="carolz" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/25/2014" ms.author="stepsic" />



#Gestion des performances des applications New Relic sur Azure

Ce guide explique comment ajouter la surveillance des performances New Relic de pointe à vos applications hébergées sur Azure. Nous aborderons les processus rapides et simples permettant d'ajouter New Relic à votre application et vous présenterons certaines des nouvelles fonctionnalités de New Relic. Pour plus d'informations sur l'utilisation de New Relic, consultez la section [Utilisation de New Relic].(#using-new-relic).

Présentation de New Relic
--

New Relic est un outil destiné aux développeurs qui surveille vos applications de production et offre des informations approfondies sur leurs performances et leur fiabilité. Ce produit est conçu pour vous permettre de gagner du temps lors de l'identification et du diagnostic des problèmes de performances et il vous donne les informations requises pour résoudre ces problèmes en un instant.

New Relic effectue le suivi de la durée du chargement et du débit de votre transaction Web, à la fois à partir du serveur et des navigateurs des utilisateurs. Il indique le temps que vous passez dans la base de données, analyse les requêtes lentes et les requêtes Web, fournit une surveillance et des alertes en matière de temps d'activité, effectue le suivi des exceptions d'application et bien plus encore.

Tarification spéciale New Relic par l'intermédiaire d'Azure Store
--

New Relic Standard est gratuit pour les utilisateurs d'Azure
New Relic Pro est fourni en fonction de la taille d'instance d'Azure Cloud Services

Pour obtenir des informations de tarification, consultez la page [New Relic dans l'Azure Store](http://www.windowsazure.com/fr-fr/gallery/store/new-relic/new-relic/).

<div class="dev-callout"> 
<strong>Remarque :</strong>
<p>la tarification n'est répertoriée que jusqu'à 10 instances de calcul. Pour un nombre supérieur à 10, contactez New Relic (sales@newrelic.com) pour une tarification en gros.</p>
</div>

Les clients d'Azure reçoivent un abonnement de 2 semaines gratuit à New Relic Pro lorsqu'ils déploient l'agent New Relic.

Inscription à New Relic à l'aide de l'Azure Store
--

New Relic s'intègre en toute transparence aux rôles Web et de travail Azure.

Pour vous inscrire à New Relic directement depuis l'Azure Store, procédez comme suit.

### Étape 1. Inscription par l'intermédiaire de l'Azure Store

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Dans le volet inférieur du portail de gestion, cliquez sur **Nouveau**.
3. Cliquez sur **Store**.
4. Dans la boîte de dialogue **Choisir un module complémentaire**, sélectionnez **New Relic**, puis cliquez sur **Suivant**.
5. Dans la boîte de dialogue **Personnaliser un module complémentaire**, sélectionnez le plan New Relic de votre choix.
6. Saisissez un code de promotion, le cas échéant.
7. Entrez un nom pour le service New Relic, qui apparaîtra dans vos paramètres Azure, ou utilisez la valeur par défaut **NewRelic**. Le nom doit être unique dans la liste des éléments de l'Azure Store auxquels vous êtes abonné.
8. Choisissez une valeur pour la région ; par exemple **Bretagne**.
9. Cliquez sur **Suivant**.
10. Dans la boîte de dialogue **Revoir l'achat**, passez en revue le plan et les informations tarifaires, ainsi que les conditions juridiques. Si vous acceptez ces dernières, cliquez sur **Acheter**.
11. Une fois que vous avez cliqué sur **Acheter**, votre compte New Relic commence le processus de création. Vous pouvez surveiller le statut dans le portail de gestion Azure.
12. Pour récupérer votre clé de licence New Relic, cliquez sur **Output Values**. 
13. Copiez la clé de licence qui apparaît. Vous devez la saisir lorsque vous installez le package Nuget New Relic.

### Étape 2. Installer le package Nuget

1. Ouvrez votre solution Visual Studio ou créez-en une en sélectionnant
   **Fichier > Nouveau > projet**.

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. Si votre solution n'est pas encore dotée d'un projet de service cloud Azure, ajoutez-en un en cliquant avec le bouton droit sur votre application dans l'Explorateur de solutions et en sélectionnant **Ajouter un projet de service cloud Azure**.

	![Create cloud service](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. Ouvrez la console du Gestionnaire de package en sélectionnant **Outils > Gestionnaire de package de bibliothèques > Console du Gestionnaire de package**. Définissez votre projet sur le Projet par défaut dans la partie supérieure de la fenêtre Console du Gestionnaire de package.

	![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. À l'invite de commandes du Gestionnaire de Package, tapez `Install-Package
   NewRelicWindowsAzure` et appuyez sur **Entrée**.

	![install in package manager](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. Lorsque vous y êtes invité, saisissez la clé de licence que vous avez reçue de l'Azure Store.

	![enter license key](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. Facultatif : lorsque vous y êtes invité, entrez le nom de votre application tel qu'il apparaît dans le tableau de bord New Relic. Ou utilisez le nom de votre solution par défaut.

	![enter application name](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. Depuis l'Explorateur de solutions, cliquez avec le bouton droit sur votre projet de service cloud Azure et sélectionnez **Publier**.

	![public the cloud project](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**Remarque :** s'il s'agit de votre premier déploiement de cette application sur Azure, il vous sera demandé d'entrer vos informations d'identification Azure. Pour plus d'informations, consultez <a href="/fr-fr/develop/net/tutorials/get-started/">Déploiement d'une application web ASP.NET vers un site web Azure</a>.

![publish settings](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### Étape 3. Vérifier les performances de votre application dans New Relic.

Pour consulter votre tableau de bord New Relic :

1. Depuis le portail Azure, cliquez sur le bouton **Gérer**.
2. Connectez-vous avec l'adresse électronique et le mot de passe de votre compte New Relic.
3. Dans la barre de menus New Relic, sélectionnez **Applications > (nom de l'application)**.

	Le tableau de bord **Surveillance > Présentation** s'affiche automatiquement.

	![New Relic monitoring dashboard](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

	Une fois que vous avez sélectionné une application dans la liste de votre menu **Applications**, le tableau de bord **Présentation** affiche les informations actuelles sur le serveur et le navigateur de l'application.

### <a id="using-new-relic"></a>Utilisation de New Relic

Une fois que vous avez sélectionné votre application dans la liste du menu Applications, le tableau de bord Présentation affiche les informations actuelles sur le serveur et le navigateur de l'application. Pour basculer entre les deux vues, cliquez sur le bouton **Serveur d'applications** ou **Navigateur**.

En plus des fonctions d'analyse de l' <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">interface utilisateur New Relic standard</a> et <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">du tableau de bord</a> , le tableau de bord Vue d'ensemble des applications offre des fonctions supplémentaires.

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
       <td>Cliquez sur le bouton <b>Serveur d'applications</b> ou <b>Navigateur</b> .</td>
    </tr>
     <tr>
       <td>Afficher les niveaux de seuil pour le score <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> de votre application</td>
       <td>Pointez le curseur sur l'icône ? du score <b>Apdex<b> .</b></b></td>
    </tr>
    <tr>
       <td>Afficher les détails Apdex du monde entier</td>
       <td>À partir de la vue <b>Navigateur</b> de Vue d'ensemble, pointez n'importe où sur la carte Global Apdex. < br / ><b>Conseil :</b> pour accéder directement au tableau de bord <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard" target="_blank">Géographie</a>de l'application sélectionnée, cliquez sur le titre <b>Global Apdex</b> ou cliquez n'importe où sur la carte Global Apdex.</td>
    </tr>
    <tr>
       <td>Afficher le tableau de bord <a href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Transactions</a> web</td>
       <td>Cliquez sur la table Web Transactions du tableau de bord Vue d'ensemble des applications. Ou, pour afficher des détails sur la transaction web spécifique (y compris les <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">transactions clés</a>), cliquez sur son nom.</td>
    </tr>
    <tr>
       <td>Afficher le tableau <a href="https://newrelic.com/docs/site/errors" target="_blank">de bord</a> Erreurs</td>
       <td>Cliquez sur le titre du graphique de taux d'erreur du tableau de bord Vue d'ensemble des applications.<br /><b>Conseil :</b> vous pouvez également afficher le tableau de bord Erreurs à partir d' <b>Applications</b> &gt; (votre application) &gt; Événements &gt; Erreurs.</td>
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

![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## Étapes suivantes

Pour plus d'informations, consultez les ressources supplémentaires suivantes :

 * [Installation de l'agent .NET sur Azure](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): procédures d'installation de l'agent .NET New Relic. 
 * [Interface utilisateur New Relic](https://newrelic.com/docs/site/the-new-relic-ui):
vue d'ensemble de l'interface utilisateur New Relic, de la définition des droits et profils de l'utilisateur, de l'utilisation des fonctions standard et de l'exploration au niveau du détail du tableau de bord.
 * [Vue d'ensemble des applications](https://newrelic.com/docs/site/applications-overview): fonctionnalités accessibles à partir du tableau de bord Vue d'ensemble des applications de New Relic.
 * [Apdex](https://newrelic.com/docs/site/apdex): vue d'ensemble de la manière dont Apdex mesure la satisfaction des utilisateurs finaux de votre application.
 * [Surveillance des utilisateurs](https://newrelic.com/docs/features/real-user-monitoring): vue d'ensemble de la manière dont RUM détaille le temps qu'il faut aux navigateurs de vos utilisateurs pour charger vos pages web, leur provenance et les navigateurs qu'ils utilisent
 * [Recherche d'aide](https://newrelic.com/docs/site/finding-help): ressources disponibles dans le centre d'aide en ligne de New Relic.

<!--HONumber=35.2-->
