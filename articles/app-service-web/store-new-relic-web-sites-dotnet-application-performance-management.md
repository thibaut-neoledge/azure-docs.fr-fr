<properties 
	pageTitle="Application web .NET dans Azure App Service avec gestion des performances des applications New Relic"
	description="Apprenez à utiliser la surveillance des performances de New Relic pour les applications ASP.NET s'exécutant sur Azure App Service."
	services="app-service\web"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags 
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/30/2015"
	ms.author="stepsic"/>



# Application web .NET dans Azure App Service avec gestion des performances des applications New Relic

Ce guide explique comment ajouter la surveillance des performances New Relic de pointe à votre application web [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Nous aborderons les processus rapides et simples permettant d'ajouter New Relic à votre application et vous présenterons certaines des nouvelles fonctionnalités de New Relic. Pour plus d'informations sur l'utilisation de New Relic, consultez la section [Utilisation de New Relic](#using-new-relic).

## Présentation de New Relic

New Relic est un outil destiné aux développeurs qui surveille vos applications de production et offre des informations approfondies sur leurs performances et leur fiabilité. Ce produit est conçu pour vous permettre de gagner du temps lors de l'identification et du diagnostic des problèmes de performances et il vous donne les informations requises pour résoudre ces problèmes en un instant.

New Relic effectue le suivi de la durée du chargement et du débit de votre transaction Web, à la fois à partir du serveur et des navigateurs des utilisateurs. Il indique le temps que vous passez dans la base de données, analyse les requêtes lentes et les requêtes Web, fournit une surveillance et des alertes en matière de temps d'activité, effectue le suivi des exceptions d'application et bien plus encore.

## Tarification spéciale New Relic par l'intermédiaire d'Azure Store

New Relic Standard est gratuit pour les utilisateurs d'Azure. New Relic Pro est fourni dans plusieurs packages selon le mode de site Web que vous utilisez et la taille d'instance si vous utilisez le mode réservé.

Pour obtenir des informations de tarification, consultez la [page New Relic dans Azure Marketplace](/marketplace/partners/newrelic/newrelic).

> [AZURE.NOTE]la tarification n'est répertoriée que jusqu'à 10 instances de calcul. Pour un nombre supérieur à 10, contactez New Relic (sales@newrelic.com) pour une tarification en gros.

Les clients d'Azure reçoivent un abonnement de 2 semaines gratuit à New Relic Pro lorsqu'ils déploient l'agent New Relic.

Inscription à New Relic à l'aide d'Azure Marketplace

New Relic s'intègre en toute transparence aux rôles Web Azure, aux rôles de travail ainsi qu'à Azure App Service.

Pour vous inscrire à New Relic directement depuis Azure Marketplace, procédez comme suit.

## Étape 1. Créer un compte New Relic

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com) et cliquez sur **Nouveau** dans le coin.
3. Cliquez sur **Services de développement** > **New Relic APM**.
4. Configurez votre compte New Relic en spécifiant ce qui suit, puis cliquez sur **Créer**.
	- **Name**
	- **Niveau de tarification**
	- **Groupe de ressources**
	- **Abonnement**
	- **Emplacement**
	- **Mentions légales**

11. Une fois que vous avez cliqué sur **Créer**, votre compte New Relic démarre le processus de création. Vous pouvez en surveiller l'état en cliquant sur le bouton **Notifications**. Une fois créé, le panneau du compte New Relic s'affiche.

12. Pour récupérer votre clé de licence New Relic, consultez le volet **Essentials** en haut du panneau. L'instance de vos applications web inscrit automatiquement cette clé de licence dans ses paramètres d'application quand vous intégrez votre application web dans le compte New Relic.

## Étape 2. Configurer l'intégration de New Relic pour votre application web

1. Ouvrez le panneau de votre application web dans le [portail Azure en version préliminaire](https://portal.azure.com).
2. Dans le menu « ... », situé en haut du panneau, cliquez sur **Ajouter des vignettes**.
3. Dans l’onglet **Analyse**, sélectionnez**Résumé des applications** et faites-le glisser vers l'emplacement désiré sur le panneau de votre application web.
4. Cliquez sur Terminer pour terminer l'ajout de vignettes.
5. Cliquez sur la vignette **Analyse des applications** et sélectionnez **New Relic**.
6. Sélectionnez le compte que vous avez créé à l'étape précédente, puis cliquez sur **OK**. 

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/configure-new-relic-integration.png)

	Une fois la sauvegarde terminée, cliquez sur **Tous les paramètres** dans le panneau de l'application web, puis cliquez sur **Paramètres de l'application**. Le paramètre **NEWRELIC\_LICENSEKEY** doit être ajouté à la section **Paramètres de l'application** du panneau pour la prise en charge de New Relic :

	>[AZURE.NOTE]La prise d'effet des nouveaux paramètres de l'application peut prendre jusqu'à 30 secondes. Pour forcer la prise d'effet immédiate des paramètres, redémarrez l'application web.

## Étape 3. Publier l'application web ASP.NET

Publiez votre application web à l’aide de Visual Studio. Si vous avez précédemment publié votre application web, publiez-la à nouveau pour que l'instance des applications web ajoute le package NuGet New Relic requis afin d'activer la surveillance New Relic.

## Étape 4. Vérification des performances de votre application dans New Relic.

Pour consulter votre tableau de bord New Relic :

2. Ouvrez le panneau de votre application web dans le [portail Azure en version préliminaire](https://portal.azure.com).
3. Cliquez sur **Surveillance des applications** > **nom de l'application** > **Afficher dans New Relic**.

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/view-new-relic-data.png)

3. S'il s'agit de la première fois que vous utilisez votre compte, configurez ses informations.
3. Dans la barre de menus New Relic, sélectionnez **Applications > (nom de l'application)**.

	Le tableau de bord **Surveillance > Présentation** s'affiche automatiquement.

	![Tableau de surveillance New Relic](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app.png)

	Une fois que vous avez sélectionné une application dans la liste de votre menu **Applications**, le tableau de bord **Présentation** affiche les informations actuelles sur le serveur et le navigateur de l'application.

### <a id="using-new-relic"></a>Utilisation de New Relic

Une fois que vous avez sélectionné votre application dans la liste du menu Applications, le tableau de bord Présentation affiche les informations actuelles sur le serveur et le navigateur de l'application. Pour basculer entre les deux vues, cliquez sur le bouton **App server** ou **Navigateur**.

En plus des fonctions de l'<a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">interface utilisateur standard New Relic</a> et d'<a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">exploration au niveau du détail du tableau de bord</a>, le tableau de bord Vue d'ensemble des applications comporte des fonctions supplémentaires.

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
       <td>Afficher les niveaux de seuil du score <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> de votre application</td>
       <td>Pointez le curseur sur l'icône <b>?<b> du score Apdex.</b></b></td>
    </tr>
    <tr>
       <td>Afficher les détails Apdex du monde entier</td>
       <td>Depuis la vue <b>Navigateur</b> du tableau de bord Présentation, pointez le curseur n'importe où sur la carte Global Apdex.<br /><b>Conseil&#160;:</b> pour accéder directement au tableau de bord <a href="https://newrelic.com/docs/site/geography" target="_blank">Géographie</a> de l'application sélectionnée, cliquez sur le titre <b>Global Apdex</b> ou n'importe où dans la carte Global Apdex.</td>
    </tr>
    <tr>
       <td>Afficher le tableau de bord <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web Transactions</a></td>
       <td>Cliquez sur la table Web Transactions du tableau de bord Vue d'ensemble des applications. Ou, pour afficher les détails d'une transaction Web spécifique (y compris <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">les transactions clés</a>), cliquez sur son nom.</td>
    </tr>
    <tr>
       <td>Afficher le tableau de bord <a href="https://newrelic.com/docs/site/errors" target="_blank">Erreurs</a></td>
       <td>Cliquez sur le titre du graphique de taux d'erreur du tableau de bord Vue d'ensemble des applications.<br /><b>Conseil&#160;:</b> vous pouvez également afficher le tableau de bord Erreurs depuis <b>Applications</b>&#160;> (votre application)&#160;> Événements&#160;> Erreurs.</td>
    </tr>
    <tr>
       <td>Afficher les détails du serveur de l'application</td>
       <td><p>Effectuez l'une des actions suivantes&#160;:<p>
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

![Console Gestionnaire de package](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app_browser.png)

## Étapes suivantes

Pour plus d'informations, consultez les ressources supplémentaires suivantes :

 * [Installation de l'agent .NET sur des sites web Azure](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-websites#manual) : procédures d'installation de l'agent .NET New Relic 
 * [Interface utilisateur New Relic](https://newrelic.com/docs/site/the-new-relic-ui) : vue d'ensemble de l'interface utilisateur New Relic, de la définition des droits et profils de l'utilisateur, de l'utilisation des fonctions standard et de l'exploration au niveau du détail du tableau de bord
 * [Vue d'ensemble des applications](https://newrelic.com/docs/site/applications-overview) : fonctionnalités accessibles à partir du tableau de bord Vue d'ensemble des applications de New Relic
 * [Apdex](https://newrelic.com/docs/site/apdex) : vue d'ensemble de la manière dont Apdex mesure la satisfaction des utilisateurs finaux de votre application
 * [Surveillance des utilisateurs](https://newrelic.com/docs/features/real-user-monitoring) : vue d'ensemble de la manière dont RUM détaille le temps qu'il faut aux navigateurs de vos utilisateurs pour charger vos pages web, leur provenance et les navigateurs qu'ils utilisent
 * [Recherche d'aide](https://newrelic.com/docs/site/finding-help) : ressources disponibles dans le centre d'aide en ligne de New Relic

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page : [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre le portail Azure et le portail Azure en version préliminaire, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).


[vswebsite]: web-sites-dotnet-get-started.md

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
 

<!---HONumber=August15_HO9-->