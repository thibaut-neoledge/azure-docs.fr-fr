<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Dashboard, Monitor and Scale tabs" pageTitle="Dashboard, Monitor, and Scale in Biztalk Services | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

Onglets Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services
===============================================================================

La première fois que vous ouvrez le portail de gestion Azure, l'onglet **Tous les éléments** s'affiche automatiquement. Les colonnes de cet onglet peuvent être triées. Pour afficher votre service BizTalk, sélectionnez-le dans l'onglet **Tous les éléments**, ou cliquez sur l'onglet **BIZTALK SERVICES**, puis sur le nom de votre service BizTalk.

Une nouvelle fenêtre apparaît avec les options suivantes :

-   [Démarrage rapide](#QuickStart)

-   [Tableau de bord](#Dashboard)

-   [Surveiller](#Monitor)

-   [Mettre à l'échelle](#Scale)

La présente rubrique décrit ces onglets.

Démarrage rapide
----------------

L'onglet Démarrage rapide vous permet d'effectuer ce qui suit :

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Option</strong></td>
        <td><strong>Description</strong></td>
</tr>
    <tr>
        <td>Obtenir les outils</td>

        <td>Téléchargez le Kit de développement logiciel (SDK) de <strong>BizTalk Services</strong> pour installer les modèles de projet Visual Studio sur votre ordinateur de développement local. Ces modèles créent BizTalk Services (pont) et les projets Visual Studio des <strong>artefacts de service BizTalk</strong> (Transformation) qui sont déployés sur votre service BizTalk.

        <br/><br/>
		Les pages <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335"> Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services </a> et <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Installation du Kit de développement logiciel (SDK) Azure BizTalk Services</a> présentent les procédures de prise en main.
        </td>
    </tr>

    <tr>
        <td>Créer des accords partenaires</td>

        <td>Ouvre le portail Azure BizTalk Services hébergé sur Azure, qui vous permet d'ajouter des partenaires et de créer des contrats EDI X12 et AS2.

        <br/><br/>

        La rubrique <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration de composants pour la messagerie EDI sur le portail BizTalk Services</a> reprend la procédure de mise en route.
        </td>
    </tr>

<tr>
        <td>En savoir plus sur les services BizTalk</td>

        <td>Accédez à l'Espace formations pour en savoir plus sur Azure BizTalk Services.</td>
</tr>

</table>

Dans la barre des tâches en bas, vous pouvez **Gérer** le service BizTalk, afficher les **Informations de connexion** de l'espace de noms Access Control, afficher les **Clés de synchronisation** du compte de stockage ou **Supprimer** le service BizTalk :

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Option</strong></td>
        <td><strong>Description</strong></td>
</tr>
<tr>
<td>Gérer</td>
<td>Lorsque vous cliquez sur Gérer, le portail Azure BizTalk Services s'ouvre. Celui-ci représente l'accès à la configuration de l'échange de données informatisé (EDI, Electronic Data Interchange), y compris en ce qui concerne l'ajout de partenaires et la création de contrats AS2 et X12.
<br/><br/>
Cela revient à utiliser <strong>Créer des accords partenaires</strong> dans l'onglet <strong>Démarrage rapide</strong>.
<br/><br/>
La rubrique <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration de composants pour la messagerie EDI sur le portail BizTalk Services</a> fournit des informations supplémentaires sur le portail BizTalk Services.</td>
</tr>

<tr>
<td>Informations de connexion</td>
<td>Lorsque vous cliquez sur Informations de connexion, l'espace de noms Access Control, l'émetteur par défaut et la clé par défaut s'affichent. Vous pouvez copier ces valeurs.
<br/><br/>
Vous pouvez également ouvrir le portail de gestion de contrôle d'accès. Utiliser ce portail équivaut à utiliser l'option Active Directory dans le volet de navigation de gauche.
<br/><br/>
La rubrique <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gestion de votre espace de noms ACS</a> fournit des informations supplémentaires sur le portail de gestion de contrôle d'accès</td>
</tr>

<tr>
<td>Clés de synchronisation</td>
<td>Lorsque vous créez un compte de stockage, une clé primaire et une clé secondaire sont automatiquement créées. Ces clés contrôlent l'accès à votre compte de stockage. Votre service BizTalk utilise automatiquement la clé primaire. Les <strong>Clés de synchronisation</strong> permettent aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le service BizTalk.
<br/><br/>
Supposons que vous vouliez que le service BizTalk utilise une nouvelle clé primaire pour le compte de stockage. Pour ce faire :
<br/><br/>
<ol>
<li>Cliquez sur votre service BizTalk, puis sur <strong>Clés de synchronisation</strong>. Sélectionnez la clé secondaire. Une fois que c'est fait, le service BizTalk commence à utiliser la clé secondaire.</li>
<li>Dans le portail de gestion Azure, cliquez sur votre compte de stockage et régénérez la clé primaire. Rappelez-vous que votre service BizTalk utilise la clé secondaire.</li>
<li>Cliquez sur votre service BizTalk, puis sur <strong>Clés de synchronisation</strong>. À présent, sélectionnez la clé primaire. Il s'agit de la nouvelle clé primaire que vous avez régénérée.</li>
<li>Sur le portail de gestion Azure, cliquez sur votre compte de stockage et régénérez la clé secondaire.</li>
</ol>
<br/>
Ce processus est appelé « clés de substitution ». L'objectif est de permettre aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le service BizTalk.</td>
</tr>

<tr>
<td>Supprimer</td>
<td>Lorsque vous cliquez sur Supprimer, votre service BizTalk et tous les éléments qui y ont été déployés sont supprimés.</td>
</tr>
</table>

Tableau de bord
---------------

Lorsque vous cliquez sur le nom de votre service BizTalk, l'onglet Tableau de bord apparaît. Le tableau de bord affiche les informations suivantes :

#### Metric Graph

Graphique illustrant une liste fixe de mesures de performances. Ces mesures fournissent des valeurs en temps réel concernant l'intégrité de votre service BizTalk. Ce sont les suivantes :

-   Utilisation du processeur
-   Défaillances à la source
-   Défaillances dans le processus
-   Messages traités
-   Messages reçus
-   Latence de traitement

Pour une description de ces mesures de performances, accédez à la section [Mesures disponibles](#Metrics) dans cette rubrique.

##### Relative ou Absolue

Le graphique indique les tendances, affichant uniquement la valeur actuelle de chaque mesure ; ce qui correspond à l'option **Relative**. Pour afficher un axe Y afin de voir les valeurs absolues, sélectionnez **Absolue**.

##### Intervalle

Modifie l'intervalle de temps pour l'affichage des mesures dans le graphique. Les options incluent :

-   1 heure
-   1 jour
-   7 jours

#### Aperçu rapide

Indique les propriétés de votre service BizTalk, y compris les suivantes :

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Option</strong></td>
        <td><strong>Description</strong></td>
</tr>
<tr>
<td>Mettre à jour les informations d'identification de la base de données de suivi</td>
<td>Modifie le nom d'utilisateur et le mot de passe utilisés pour se connecter à la base de données de suivi.<br/><br/>
Lorsque vous déployez le service BizTalk, vous entrez un nom d'utilisateur et un mot de passe pour vous connecter à la base de données de suivi. Au moyen de cette option, vous pouvez modifier votre service BizTalk de façon à utiliser un nom d'utilisateur et un mot de passe différents pour la connexion à la base de données de suivi.<br/><br/>
La rubrique <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk Services : approvisionnement à l'aide du portail de gestion Azure</a> indique la procédure d'approvisionnement d'un service BizTalk.</td>
</tr>
<tr>
<td>Mettre à jour le certificat SSL</td>
<td>Permet d'entrer un autre certificat SSL.<br/><br/>
Lorsque vous approvisionnez le service BizTalk, un certificat SSL auto-signé est automatiquement créé. Au moyen de cette option, vous pouvez modifier votre service BizTalk de façon à utiliser un autre certificat SSL.<br/><br/>
La rubrique <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk Services : approvisionnement à l'aide du portail de gestion Azure</a> indique la procédure d'approvisionnement d'un service BizTalk.</td>
</tr>
<tr>
<td>Télécharger le certificat</td>
<td>Au moyen de cette option, vous pouvez télécharger le certificat SSL utilisé par votre service BizTalk.<br/><br/>
La rubrique <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk Services : approvisionnement à l'aide du portail de gestion Azure</a> indique la procédure d'approvisionnement d'un service BizTalk.</td>
</tr>
<tr>
<td>Statut</td>
<td>Affiche le statut actuel de votre service BizTalk.</td>
</tr>
<tr>
<td>URL du service</td>
<td>Cette URL vous permet d'accéder à votre service BizTalk. Elle est identique à l' <strong>URL de domaine</strong> entrée lors de l'approvisionnement de votre service BizTalk. </td>
</tr>
<tr>
<td>Adresse IP virtuelle (VIP) publique</td>
<td>Cette adresse IP est attribuée à votre service BizTalk. Elle est utilisée pour tous les points de terminaison d’entrée et représente l'adresse source pour le trafic sortant. L'adresse IP appartient à votre service BizTalk tant qu'il est approvisionné. Si vous supprimez le service BizTalk, l'adresse IP est attribuée à un autre approvisionnement de service.</td>
</tr>
<tr>
<td>Espace de noms ACS</td>
<td>Permet de s'authentifier auprès du service BizTalk. Il est identique à l'<strong>Espace de noms ACS</strong> entré lors de l'approvisionnement du service BizTalk.</td>
</tr>
<tr>
<td>Édition</td>
<td>Indique l'édition. Options possibles : Développeur, De base, Standard et Premium. Elle est identique à l'édition entrée lors de l'approvisionnement du service BizTalk. <br/><br/>
La rubrique <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302281">Tableau comparatif des éditions Développeur, De base, Standard et Premium de BizTalk Services</a> indique les différences entre les éditions, y compris les coûts.</td>
</tr>
<tr>
<td>Emplacement</td>
<td>Indique la région géographique hébergeant votre service BizTalk. Elle est identique à la <strong>Région</strong> entrée lors de l'approvisionnement du service BizTalk.</td>
</tr>
<tr>
<td>Création</td>
<td>Indique la date et l'heure de l'approvisionnement du service BizTalk.</td>
</tr>
<tr>
<td>Base de données des suivis</td>
<td>Nom de la base de données SQL Azure stockant les tables de suivi utilisées par votre service BizTalk. Elle est identique à la <strong>Base de données des suivis</strong> entrée lors de l'approvisionnement du service BizTalk.</td>
</tr>
<tr>
<td>Stockage de surveillance/archivage</td>
<td>Nom du compte de stockage Azure stockant la sortie de la surveillance de votre service BizTalk. Il est identique au <strong>Compte de stockage de surveillance/archivage</strong> entré lors de l'approvisionnement du service BizTalk.</td>
</tr>
<tr>
<td>Nom d'abonnement</td>
<td>L'abonnement régit l'accès au portail de gestion Azure. Il est identique au nom de l'<strong>Abonnement</strong> sélectionné lors de l'approvisionnement du service BizTalk.</td>
</tr>
<tr>
<td>ID d'abonnement</td>
<td>L'abonnement régit l'accès au portail de gestion Azure. Lorsqu'un abonnement est créé, un ID d'abonnement est automatiquement généré. Lors de l'utilisation d'API REST, il vous faudra peut-être entrer l'ID d'abonnement.</td>
</tr>
</table>

La rubrique [BizTalk Services : approvisionnement à l'aide du portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280) indique la procédure d'approvisionnement d'un service BizTalk.

#### Gérer, Informations de connexion, Clés de synchronisation et Supprimer

Dans la barre des tâches en bas, vous pouvez **Gérer** le service BizTalk, afficher les **Informations de connexion** de l'espace de noms Access Control, afficher les **Clés de synchronisation** du compte de stockage ou **Supprimer** le service BizTalk :

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Option</strong></td>
        <td><strong>Description</strong></td>
</tr>
<tr>
<td>Gérer</td>
<td>Lorsque vous cliquez sur Gérer, le portail Azure BizTalk Services s'ouvre. Celui-ci représente l'accès à la configuration de l'échange de données informatisé (EDI, Electronic Data Interchange), y compris en ce qui concerne l'ajout de partenaires et la création de contrats AS2 et X12.
<br/><br/>
Cela revient à utiliser <strong>Créer des accords partenaires</strong> dans l'onglet <strong>Démarrage rapide</strong>.
<br/><br/>
La rubrique <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration de composants pour la messagerie EDI sur le portail BizTalk Services</a> fournit des informations supplémentaires sur le portail BizTalk Services.</td>
</tr>
<tr>
<td>connexion</td>
<td>Lorsque vous cliquez sur Informations de connexion, l'espace de noms Access Control, l'émetteur par défaut et la clé par défaut s'affichent. Vous pouvez copier ces valeurs.
<br/><br/>
Vous pouvez également ouvrir le portail de gestion de contrôle d'accès. Utiliser ce portail équivaut à utiliser l'option Active Directory dans le volet de navigation de gauche.
<br/><br/>
La rubrique <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gestion de votre espace de noms ACS</a> fournit des informations supplémentaires sur le portail de gestion de contrôle d'accès.</td>
</tr>
<tr>
<td>Clés de synchronisation</td>
<td>Lorsque vous créez un compte de stockage, une clé primaire et une clé secondaire sont automatiquement créées. Ces clés contrôlent l'accès à votre compte de stockage. Votre service BizTalk utilise automatiquement la clé primaire. Les <strong>Clés de synchronisation</strong> permettent aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le service BizTalk.
<br/><br/>
Supposons que vous vouliez que le service BizTalk utilise une nouvelle clé primaire pour le compte de stockage. Pour ce faire :
<br/><br/>
<ol>
<li>Cliquez sur votre service BizTalk, puis sur <strong>Clés de synchronisation</strong>. Sélectionnez la clé secondaire. Une fois que c'est fait, le service BizTalk commence à utiliser la clé secondaire.</li>
<li>Dans le portail de gestion Azure, cliquez sur votre compte de stockage et régénérez la clé primaire. Rappelez-vous que votre service BizTalk utilise la clé secondaire.</li>
<li>Cliquez sur votre service BizTalk, puis sur <strong>Clés de synchronisation</strong>. À présent, sélectionnez la clé primaire. Il s'agit de la nouvelle clé primaire que vous avez régénérée.</li>
<li>Sur le portail de gestion Azure, cliquez sur votre compte de stockage et régénérez la clé secondaire.</li>
</ol>
<br/>
Ce processus est appelé « clés de substitution ». L'objectif est de permettre aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le service BizTalk.</td>
</tr>

<tr>
<td>Supprimer</td>
<td>Lorsque vous cliquez sur Supprimer, votre service BizTalk et tous les éléments qui y ont été déployés sont supprimés.</td>
</tr>
</table>

Surveiller
----------

L'onglet Surveiller affiche les informations suivantes :

#### Metric Graph

Graphique affichant les mesures de performances sélectionnées. Ces mesures fournissent des valeurs en temps réel concernant l'intégrité de votre service BizTalk. Vous choisissez les mesures de performances devant s'afficher (jusqu'à six mesures de performances simultanément).

##### Relative ou Absolue

Le graphique indique les tendances, affichant uniquement la valeur actuelle de chaque mesure ; ce qui correspond à l'option **Relative**. Pour afficher un axe Y afin de voir les valeurs absolues, sélectionnez **Absolue**.

##### Intervalle

Modifie l'intervalle de temps pour l'affichage des mesures dans le graphique. Les options incluent :

-   1 heure
-   1 jour
-   7 jours

#### Pour supprimer ou afficher des mesures dans le graphique

> 1.  Cliquez sur l'onglet **Surveiller**.
> 2.  Cliquez sur **Ajouter des métriques** dans la barre des tâches :

>   ![Cliquez sur Ajouter des métriques](./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png)
> 3.  Vérifiez les mesures de performances devant s'afficher dans l'onglet **Surveiller**.
> 4.  Cliquez sur la coche pour revenir à l'onglet **Surveiller**.
> 5.  Cliquez sur le cercle en regard de la mesure pour afficher la valeur associée dans le graphique.

>      Par exemple, la mesure **Utilisation du processeur** apparaît en grisé ; son résultat n'apparaît pas dans le graphique :
>      
>      ![La mesure Utilisation du processeur apparaît en grisé](./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png)
>      
>      Cliquez sur le cercle en grisé pour permettre à la mesure **Utilisation du processeur** d'afficher son résultat dans le graphique :
>      
>      ![La mesure Utilisation du processeur est activée](./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png)

> 1.  Pour supprimer une mesure du graphique affiché et de la liste, cliquez sur **Supprimer une métrique** dans la barre des tâches. Cliquer sur **Supprimer une métrique** permet de supprimer la mesure dans l'onglet Surveiller. Pour réintégrer la mesure dans la liste, cliquez sur **Ajouter des métriques** dans la barre des tâches, vérifiez la mesure et cliquez sur la coche pour revenir à l'onglet **Surveiller**. Cliquez sur le cercle en grisé pour activer la mesure dans le graphique.

Mesures disponibles
-------------------

Les mesures/compteurs de performances suivants sont disponibles :

<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Mesure</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr>
<td>Latence aller-retour</td>
<td>Exprimée en millisecondes (ms), cette mesure de performances affiche le délai moyen nécessaire au traitement d'un message depuis le moment de sa réception jusqu'à son traitement complet par le service BizTalk sur tous les ponts. Seuls les messages correctement traités sont comptabilisés.<br/><br/>
Lorsque les événements suivants se produisent, un horodatage est créé : 
<ul>
<li>Le message accède à la passerelle</li>
<li>Le message est acheminé vers la destination </li>
<li>Une réponse de la destination est reçue </li>
<li>Un accusé de réception est envoyé à la passerelle par la destination </li>
</ul>
<br/>
Cette mesure affiche le résultat du calcul suivant :
<br/><br/>
[Accusé de réception envoyé à la passerelle par la destination] - [Le message accède à la passerelle]</td>
</tr>
<tr>
<td>Défaillances à la source</td>
<td>Mesure de performances affichant le nombre total de messages n'ayant pas pu être traités par le service BizTalk lors de l'extraction de messages à partir des points de terminaison sources.</td>
</tr>
<tr>
<td>Utilisation du processeur</td>
<td>Indique le pourcentage de temps processeur de toutes les instances de rôle.</td>
</tr>
<tr>
<td>Latence de traitement</td>
<td>Exprimée en millisecondes (ms), cette mesure de performances indique le délai moyen requis pour traitement d'un message par le service BizTalk sur tous les ponts, à l'exclusion du temps passé au niveau des destinations. Seuls les messages correctement traités sont comptabilisés.
<br/><br/>
Lorsque l'un des événements suivants se produit, un horodatage est créé : 

<ul>
<li>Le message accède à la passerelle </li>
<li>Le message est acheminé vers la destination </li>
<li>Une réponse de la destination est reçue </li>
<li>Un accusé de réception est envoyé à la passerelle par la destination</li>
</ul>
<br/>Cette mesure affiche le résultat du calcul suivant :<br/><br/>
[Un accusé de réception est envoyé à la passerelle par la destination] - [Le message accède à la passerelle] - [Une réponse de la destination est reçue] + [Le message est acheminé vers la destination]</td>
</tr>
<tr>
<td>Défaillances dans le processus</td>
<td>Mesure de performances affichant le nombre total de messages ayant échoué pendant le traitement par le service BizTalk sur tous les ponts pendant un intervalle de temps.</td>
</tr>
<tr>
<td>Messages envoyés</td>
<td>Mesure de performances affichant le nombre total de messages envoyés par le service BizTalk sur tous les ponts pendant un intervalle de temps. Cette mesure est incrémentée lorsqu'un message envoyé à partir d'un pipeline atteint la destination d'itinéraire. Cette mesure n'indique pas qu'un message a été traité correctement.<br/><br/>
Dans un scénario de réponse à la demande, la mesure est incrémentée lorsque la destination d'itinéraire renvoie un accusé de réception au pipeline..</td>
</tr>
<tr>
<td>Messages reçus</td>
<td>Mesure de performances affichant le nombre total de messages reçus par le service BizTalk sur tous les ponts pendant un intervalle de temps. Cette mesure est incrémentée lorsqu'un nouveau message est reçu par le pipeline.</td>
</tr>
<tr>
<td>Messages en cours de traitement</td>
<td>Mesure de performances affichant le nombre total de messages en cours de traitement par le service BizTalk pendant un intervalle de temps.</td>
</tr>
<tr>
<td>Messages traités</td>
<td>Mesure de performances affichant le nombre total de messages correctement traités par le service BizTalk sur tous les ponts pendant un intervalle de temps. Cette mesure est incrémentée lorsqu'un message est correctement reçu par le pipeline et acheminé vers la destination.</td>
</tr>
</table>

Mettre à l'échelle
------------------

Dans l'onglet Mettre à l'échelle, vous pouvez ajouter ou soustraire le nombre d'unités utilisées par votre service BizTalk. Par défaut, une seule unité est configurée. Vous pouvez ajouter des unités supplémentaires afin de mettre à l'échelle votre service BizTalk. Lorsque vous augmentez la mise à l'échelle, vous augmentez le débit. La quantité de ressources augmente également, y compris les ponts déployés, les contrats, les connexions métier et la puissance de traitement. Par exemple, vous augmentez la mise à l'échelle de 1 à 2 unités. Dans ce cas, vous pouvez doubler le nombre de ponts, les contrats, les connexions métier et la puissance de traitement.

Certaines éditions BizTalk n'offrent pas de possibilité de mise à l'échelle. Dans ce cas, une seule unité est autorisée. Pour déterminer le nombre d'unités auquel votre édition peut être mise à l'échelle, consultez la page [Tableau comparatif des éditions Développeur, De base, Standard et Premium de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279).

L'augmentation du nombre d'unités peut avoir un impact sur les tarifs. Si vous augmentez les unités, cliquez sur **Enregistrer** pour afficher un message indiquant s'il y a un impact sur la facturation. Vous choisissez ensuite de continuer. Lorsque vous augmentez le nombre d'unités, le statut du service BizTalk passe d'Actif à Mis à jour. En état de mise à jour, votre service BizTalk continue de s'exécuter.

Suivant
-------

À présent que vous connaissez bien les différents onglets, vous pouvez accéder à davantage d'informations sur les fonctionnalités Azure BizTalk Services :

-   [Limitation BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Nom et clé de l'émetteur dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Sauvegarde et restauration de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329873)

Voir aussi
----------

-   [Tableau comparatif des éditions Développeur, De base, Standard et Premium de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Approvisionnement de BizTalk Services avec le portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [BizTalk Services : tableau comparatif des états du service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

