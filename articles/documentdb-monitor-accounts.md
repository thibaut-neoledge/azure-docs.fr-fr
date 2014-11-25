<properties title="Monitor a DocumentDB Account" pageTitle="Monitor a DocumentDB account | Azure" description="Learn how to monitor your DocumentDB account for performance metrics (such as requests and server errors) and usage metrics (such as storage consumption)." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, monitor, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="brradsev" />

# Surveillance d'un compte DocumentDB

Vous pouvez surveiller vos comptes DocumentDB dans la [version préliminaire du portail Azure][version préliminaire du portail Azure]. Pour chaque compte DocumentDB, des mesures de performances (notamment les demandes et les erreurs de serveur) et des mesures d'utilisation (par exemple l'espace de stockage utilisé) sont disponibles.

## Dans cet article

-   [Affichage des mesures de performances pour un compte DocumentDB][Affichage des mesures de performances pour un compte DocumentDB]
-   [Personnalisation des mesures de performances pour un compte DocumentDB][Personnalisation des mesures de performances pour un compte DocumentDB]
-   [Création de graphiques de performances côte à côte][Création de graphiques de performances côte à côte]
-   [Affichage des mesures d'utilisation pour un compte DocumentDB][Affichage des mesures d'utilisation pour un compte DocumentDB]
-   [Configuration d'alertes de mesures de performances pour un compte DocumentDB][Configuration d'alertes de mesures de performances pour un compte DocumentDB]
-   [Étapes suivantes][Étapes suivantes]

## <span id="metrics"></span></a> Affichage des mesures de performances pour un compte DocumentDB

1.  Dans la [version préliminaire du portail Azure][version préliminaire du portail Azure], cliquez sur **Parcourir**, puis sur **Comptes DocumentDB**. Cliquez ensuite sur le nom du compte DocumentDB dont vous souhaitez voir les mesures de performances.
2.  Dans le filtre **Surveillance**, vous pouvez, par défaut, voir :

    -   le nombre total de demandes pour le jour en cours ;
    -   la moyenne des demandes par seconde pour le jour en cours.

    ![][0]

3.  Cliquez sur **Total des demandes ou Moyenne des demandes par seconde** pour ouvrir le volet **Métrique**.
4.  Le volet Métrique affiche les détails des mesures que vous avez sélectionnées. En haut du volet se trouve un graphique et en dessous un tableau qui affiche les valeurs d'agrégation des mesures sélectionnées, comme la moyenne, le minimum et le maximum. Le volet Métrique affiche également la liste des alertes définies, filtrées en fonction des mesures apparaissant dans le volet ouvert. Ainsi, si vous avez plusieurs alertes, seules celles qui sont pertinentes sont affichées ici.

    ![][1]

## <span id="custom"></span></a> Personnalisation des mesures de performances pour un compte DocumentDB

1.  Pour personnaliser les mesures qui s'affichent à un endroit particulier, cliquez avec le bouton droit sur le graphique des mesures, puis sélectionnez **Modifier le graphique**.
    ![][2]

2.  Dans le volet **Modifier le graphique**, des options permettent de modifier les mesures qui s'affichent dans le volet, ainsi que leur plage horaire.
    ![][3]

3.  Pour modifier les mesures affichées, cochez ou décochez les mesures de performances disponibles, puis cliquez sur **Enregistrer** au bas du volet.
4.  Pour modifier la plage horaire, choisissez une autre plage (ex. : **Heure précédente**), puis cliquez sur **Enregistrer** au bas du volet.
    
	![][4]

5.  La plage horaire personnalisée vous permet de choisir une période sur les deux dernières semaines.
6.  Lorsque vous avez cliqué sur **Enregistrer**, vos modifications sont conservées jusqu'à la fermeture du volet du compte DocumentDB. À votre prochaine visite, la mesure et la plage de temps d'origine sont de nouveau affichées.

## <span id="create"></span></a> Création de graphiques côte à côte

La version préliminaire du portail Azure vous permet de créer des graphiques côte à côte.

1.  Cliquez avec le bouton droit sur le graphique de départ et sélectionnez **Personnaliser**.
    ![][5]

2.  Cliquez sur **Cloner** dans le menu pour copier la partie concernée.

    ![][6]

3.  Pour terminer, cliquez sur **Done** sur la barre d'outils en haut de l'écran. Vous pouvez maintenant considérer cette partie comme n'importe quelle mesure en personnalisant les mesures et la plage horaire affichées. Ainsi, vous pouvez voir deux mesures différentes affichées en même temps côte à côte.
    
	![][7]

> Remarque : la plage horaire du graphique et les mesures choisies sont réinitialisées sur les valeurs par défaut lorsque vous quittez la version préliminaire du portail Azure.

## <span id="view"></span></a> Affichage des mesures d'utilisation pour un compte DocumentDB

1.  Dans la [version préliminaire du portail Azure][version préliminaire du portail Azure], cliquez sur **Parcourir**, puis sur **Comptes DocumentDB**. Cliquez ensuite sur le nom du compte DocumentDB dont vous souhaitez voir les mesures d'utilisation.
2.  Dans le filtre **Utilisation**, vous pouvez, par défaut, voir :

    -   la consommation du stockage sur le compte ;
    -   le stockage maximum disponible du compte ;
    -   l'utilisation de pièces jointes ;
    -   les utilisateurs et l'utilisation des autorisations ;
    -   l'affectation des unités de capacité ;
    -   l'utilisation de pièces jointes dans le compte.
        ![][8]

## <span id="setup"></span></a> Configuration d'alertes de mesures de performances pour un compte DocumentDB

1.  Dans la [version préliminaire du portail Azure][version préliminaire du portail Azure], cliquez sur **Parcourir**, puis sur **Comptes DocumentDB**. Cliquez ensuite sur le nom du compte DocumentDB pour lequel vous souhaitez configurer les alertes de mesures de performances.
2.  Dans le filtre **Opérations**, cliquez sur la zone **Règles d'alerte**.
    
	![][9]

3.  Dans le volet Règles d'alerte, cliquez sur **Ajouter une alerte**.
    
	![][10]

4.  Dans le volet **Ajouter une règle d'alerte**, indiquez :

    -   le nom de la règle d'alerte que vous configurez ;
    -   une description de la nouvelle règle d'alerte ;
    -   la mesure de la règle d'alerte ;
    -   la condition, le seuil et la période déterminant l'activation de l'alerte. Par exemple, un nombre d'erreurs de serveur supérieur à 5 au cours des 15 dernières minutes.
    -   l'envoi ou non d'un courrier électronique à l'administrateur de service ou aux coadministrateurs lorsque l'alerte se déclenche ;
    -   des adresses électroniques supplémentaires pour les notifications d'alerte.
        ![][11]

## <span id="next"></span></a>Étapes suivantes

-   Pour en savoir plus sur DocumentDB, reportez-vous à la documentation Azure DocumentDB à l'adresse [azure.com][azure.com]

<!--Anchors-->

  [version préliminaire du portail Azure]: https://portal.azure.com/
  [Affichage des mesures de performances pour un compte DocumentDB]: #metrics
  [Personnalisation des mesures de performances pour un compte DocumentDB]: #custom
  [Création de graphiques de performances côte à côte]: #create
  [Affichage des mesures d'utilisation pour un compte DocumentDB]: #view
  [Configuration d'alertes de mesures de performances pour un compte DocumentDB]: #setup
  [Étapes suivantes]: #next
  [0]: http://i.imgur.com/y7pigTT.png
  [1]: http://i.imgur.com/6rBNPBL.png
  [2]: http://i.imgur.com/tRZEHk1.png
  [3]: http://i.imgur.com/G4UTi5U.png
  [4]: ./media/documentdb-monitor-accounts/madocdb5.png
  [5]: http://i.imgur.com/vLXWftF.png
  [6]: ./media/documentdb-monitor-accounts/madocdb7.png
  [7]: ./media/documentdb-monitor-accounts/madocdb8.png
  [8]: http://i.imgur.com/sL5inOu.png
  [9]: ./media/documentdb-monitor-accounts/madocdb10.png
  [10]: ./media/documentdb-monitor-accounts/madocdb11.png
  [11]: http://i.imgur.com/Inra4Po.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
