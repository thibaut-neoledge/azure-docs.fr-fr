<properties linkid="develop-net-how-to-guides-new-relic" urlDisplayName="New Relic" pageTitle="Using New Relic with Azure - Azure feature guides" metaKeywords="" description="Learn how to use the New Relic service to manage and monitor your Azure application." metaCanonical="" services="" documentationCenter=".NET" title="New Relic Application Performance Management on Azure" authors="" solutions="" manager="" editor="" />

Gestion des performances des applications New Relic sur Azure
=============================================================

Ce guide explique comment ajouter la surveillance des performances New Relic de pointe à vos applications hébergées sur Azure. Nous aborderons les processus rapides et simples permettant d'ajouter New Relic à votre application et vous présenterons certaines des nouvelles fonctionnalités de New Relic. Pour plus d'informations sur l'utilisation de New Relic, consultez la page [Utilisation de New Relic](#using-new-relic).

Présentation de New Relic
-------------------------

New Relic est un outil destiné aux développeurs qui surveille vos applications de production et offre des informations approfondies sur leurs performances et leur fiabilité. Ce produit est conçu pour vous permettre de gagner du temps lors de l'identification et du diagnostic des problèmes de performances et il vous donne les informations requises pour résoudre ces problèmes en un instant.

New Relic effectue le suivi de la durée du chargement et du débit de votre transaction Web, à la fois à partir du serveur et des navigateurs des utilisateurs. Il indique le temps que vous passez dans la base de données, analyse les requêtes lentes et les requêtes Web, fournit une surveillance et des alertes en matière de temps d'activité, effectue le suivi des exceptions d'application et bien plus encore.

Tarification spéciale New Relic par l'intermédiaire d'Azure Store
-----------------------------------------------------------------

New Relic Standard est gratuit pour les utilisateurs d'Azure, New Relic Pro est fourni selon la taille d'instance d'Azure Cloud Services

Pour des informations de tarification, consultez la [page New Relic dans l'Azure Store](http://www.windowsazure.com/en-us/gallery/store/new-relic/new-relic/).

**Remarque :**

la tarification n'est répertoriée que jusqu'à 10 instances de calcul. Pour un nombre supérieur à 10, contactez New Relic (sales@newrelic.com) pour une tarification en gros.

Les clients d'Azure reçoivent un abonnement de 2 semaines gratuit à New Relic Pro lorsqu'ils déploient l'agent New Relic.

Inscription à New Relic à l'aide de l'Azure Store
-------------------------------------------------

New Relic s'intègre en toute transparence aux rôles Web et de travail Azure.

Pour vous inscrire à New Relic directement depuis l'Azure Store, procédez comme suit.

### Étape 1 : inscription par l'intermédiaire de l'Azure Store

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2.  Dans le volet inférieur du portail de gestion, cliquez sur **New**.
3.  Cliquez sur **Store**.
4.  Dans la boîte de dialogue **Choose an Add-on**, sélectionnez **New Relic**, puis cliquez sur **Next**.
5.  Dans la boîte de dialogue **Personalize Add-on**, sélectionnez le plan New Relic que vous souhaitez.
6.  Saisissez un code de promotion, le cas échéant.
7.  Saisissez un nom pour le service New Relic, qui apparaîtra dans vos paramètres Azure, ou utilisez la valeur par défaut **NewRelic**. Le nom doit être unique dans la liste des éléments de l'Azure Store auxquels vous êtes abonné.
8.  Choisissez une valeur pour la région ; par exemple, **West US**.
9.  Cliquez sur **Suivant**.
10. Dans la boîte de dialogue **Review Purchase**, passez en revue le plan et les informations tarifaires, ainsi que les conditions juridiques. Si vous acceptez ces dernières, cliquez sur **Purchase**.
11. Une fois que vous avez cliqué sur **Purchase**, votre compte New Relic commence le processus de création. Vous pouvez surveiller le statut dans le portail de gestion Azure.
12. Pour récupérer votre clé de licence New Relic, cliquez sur **Output Values**.
13. Copiez la clé de licence qui apparaît. Vous devez la saisir lorsque vous installez le package Nuget New Relic.

### Étape 2 : installation du package Nuget

1.  Ouvrez votre solution Visual Studio, ou créez-en une en sélectionnant **Fichier > Nouveau > Projet**.

    ![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2.  Si votre solution n'est pas déjà dotée d'un projet de service cloud Azure, ajoutez-en un en cliquant avec le bouton droit sur votre application dans l'Explorateur de solutions et en sélectionnant **Add Azure Cloud Service Project**.

    ![Créer le service cloud](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3.  Ouvrez la console Gestionnaire de package en sélectionnant **Outils > Gestionnaire de package de bibliothèques > Console du gestionnaire de package**. Définissez votre projet sur le Projet par défaut dans la partie supérieure de la fenêtre de la console Gestionnaire de package.

    ![Console Gestionnaire de package](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4.  À l'invite de commandes du Gestionnaire de package, tapez `Install-Package NewRelicWindowsAzure` et appuyez sur **Entrée**.

    ![installer dans gestionnaire de package](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5.  Lorsque vous y êtes invité, saisissez la clé de licence que vous avez reçue de l'Azure Store.

    ![saisir la clé de licence](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6.  Facultatif : lorsque vous y êtes invité, saisissez le nom de votre application tel qu'il apparaît dans le tableau de bord New Relic. Ou utilisez le nom de votre solution par défaut.

    ![saisir le nom de l'application](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7.  Depuis l'Explorateur de solutions, cliquez avec le bouton droit sur votre projet de service cloud Azure et sélectionnez **Publier**.

    ![publier le projet cloud](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)

**Remarque :** s'il s'agit de votre premier déploiement de cette application sur Azure, il vous sera demandé de saisir vos informations d'identification Azure. Pour plus d'informations, consultez la page [Déploiement d'une application Web ASP.NET sur un site Web Azure](/en-us/develop/net/tutorials/get-started/).

![paramètres de publication](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### Étapes 3 : vérification des performances de votre application dans New Relic

Pour consulter votre tableau de bord New Relic :

1.  Depuis le portail Azure, cliquez sur le bouton **Manage**.
2.  Connectez-vous avec l'adresse électronique et le mot de passe de votre compte New Relic.
3.  Dans la barre de menus New Relic, sélectionnez **Applications > (nom de l'application)**.

    Le tableau de bord **Monitoring > Overview** s'affiche automatiquement.

    ![Tableau de surveillance New Relic](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

    Une fois que vous avez sélectionné une application dans la liste de votre menu **Applications**, le tableau de bord **Overview** affiche les informations actuelles sur le serveur et le navigateur de l'application.

### Utilisation de New Relic

Une fois que vous avez sélectionné votre application dans la liste du menu Applications, le tableau de bord Overview affiche les informations actuelles sur le serveur et le navigateur de l'application. Pour basculer entre les deux vues, cliquez sur le bouton **App server** ou **Browser**.

En plus des fonctions de l'[interface utilisateur standard New Relic](https://newrelic.com/docs/site/the-new-relic-ui#functions) et [d'exploration au niveau du détails du tableau de bord](https://newrelic.com/docs/site/the-new-relic-ui#drilldown), le tableau de bord Overview des applications comporte des fonctions supplémentaires.

**Si vous voulez...**

**Procédez comme suit...**

Afficher les informations du tableau de bord pour le serveur ou le navigateur de l'application sélectionnée

Cliquez sur le bouton **App Server** ou **Browser**.

Afficher les niveaux de seuil du score [Apdex](https://newrelic.com/docs/site/apdex) de votre application

Pointez le curseur sur l'icône **? **du score Apdex.****

Afficher les détails Apdex du monde entier

Depuis la vue **Browser** du tableau de bord Overview, pointez le curseur n'importe où sur la carte Global Apdex.
**Conseil :** pour accéder directement au tableau de bord [Geography](https://newrelic.com/docs/site/geography) de l'application sélectionnée, cliquez sur le titre **Global Apdex** ou n'importe où dans la carte Global Apdex.

Afficher le tableau de bord [Web Transactions](https://newrelic.com/docs/applications-dashboards/web-transactions)

Cliquez sur la table Web Transactions du tableau de bord Applications Overview. Ou, pour afficher les détails d'une transaction Web spécifique (y compris [les transactions clés](https://newrelic.com/docs/site/key-transactions)), cliquez sur son nom.

Afficher le tableau de bord [Errors](https://newrelic.com/docs/site/errors)

Cliquez sur le titre du graphique Error rate du tableau de bord Applications Overview.
**Conseil :** vous pouvez également afficher le tableau de bord Errors depuis **Applications** > (votre application) > Events > Errors.

Afficher les détails du serveur de l'application

Effectuez l'une des actions suivantes :

-   Basculez entre une vue de la table des hôtes et les détails de mesure de chaque hôte.
-   Cliquez sur le nom d'un serveur spécifique.
-   Pointez le curseur sur le score Apdex d'un serveur spécifique.
-   Cliquez sur l'utilisation de l'unité centrale ou la mémoire d'un serveur spécifique.

Vous trouverez ci-dessous un exemple du tableau de bord Applications Overview lorsque vous sélectionnez la vue Browser.

![Console Gestionnaire de package](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

Étapes suivantes
----------------

Pour plus d'informations, consultez les ressources supplémentaires suivantes :

-   [Installation de l'agent .NET sur Azure](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure) : procédures d'installation de l'agent .NET New Relic
-   [Interface utilisateur New Relic](https://newrelic.com/docs/site/the-new-relic-ui) : vue d'ensemble de l'interface utilisateur New Relic, de la définition des droits et profils de l'utilisateur, de l'utilisation des fonctions standard et de l'exploration au niveau du détail du tableau de bord
-   [Vue d'ensemble des applications](https://newrelic.com/docs/site/applications-overview) : fonctionnalités accessibles à partir du tableau de bord Applications Overview de New Relic
-   [Apdex](https://newrelic.com/docs/site/apdex) : vue d'ensemble de la manière dont Apdex mesure la satisfaction des utilisateurs finaux de votre application
-   [Surveillance des utilisateurs](https://newrelic.com/docs/features/real-user-monitoring) : vue d'ensemble de la manière dont RUM détaille le temps qu'il faut aux navigateurs de vos utilisateurs pour charger vos pages Web, leur provenance et les navigateurs qu'ils utilisent.
-   [Recherche d'aide](https://newrelic.com/docs/site/finding-help) : ressources disponibles dans le centre d'aide en ligne de New Relic.

