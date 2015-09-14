<properties
 pageTitle="Prise en main d’Azure Scheduler dans le portail Azure | Microsoft Azure"
	description=""
	services="scheduler"
	documentationCenter=".NET"
	authors="krisragh"
	manager="dwrede"
	editor=""/>
<tags
 ms.service="scheduler"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/18/2015"
	ms.author="krisragh"/>

# Prise en main de Scheduler dans le portail Azure

## Configurer Scheduler à l’aide du portail Azure pour créer rapidement et facilement des travaux

Pour suivre ce didacticiel, vous devez disposer d'un compte Azure avec la fonctionnalité Azure Scheduler activée. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [Version d’évaluation gratuite d’Azure](https://azure.microsoft.com/fr-FR/pricing/free-trial/).

## Prise en main

Il est facile de créer des travaux et des collections de travaux dans Azure Scheduler à l’aide du portail Azure. Ce didacticiel vous guide lors de la création de la collection de travaux que vous utilisez pour stocker les travaux et la création d’un travail dans une collection de travaux. Il vous présente également les tâches de surveillance et de gestion des travaux disponibles dans le portail. Vous devez avoir une expérience préalable avec Azure pour pouvoir suivre ce didacticiel.

La première fois que vous ouvrez le portail Azure, l’onglet **Tous les éléments** s’affiche automatiquement. Les colonnes de cet onglet peuvent être triées. Pour afficher vos travaux et collections de travaux de Scheduler, cliquez sur l'onglet **SCHEDULER**.

![][1]

## Créer une collection de travaux et un travail

1.  Connectez-vous au [portail Azure](https://manage.windowsazure.com/).  

2.  Cliquez sur **App Services** > **Créer** > **Scheduler**, puis cliquez sur **Création personnalisée**. <br /><br /> ![][2]

3.  Dans **Collection de travaux**, sélectionnez le nom de la collection de travaux existants sous la liste déroulante **Collection de travaux**. Si vous n'avez de collection de travaux existante à laquelle ajouter le travail, sélectionnez **Créer** et entrez un nom pour identifier votre nouvelle collection de travaux.<br /><br /> ![][3]

4.  Dans **Région**, sélectionnez la région géographique de la collection de travaux.

5.  Cliquez sur la touche de direction pour créer la collection de travaux et passer à l'étape suivante ; la création d'un travail.

6.  Nous allons créer un travail qui atteint simplement http://www.microsoft.com/ avec une demande GET. Dans l’écran **Action du travail**, définissez les valeurs suivantes pour les champs de formulaire requis :

    1.  **Nom :** ` getmicrosoft`  

    2.  **Type d’action :** ` HTTP`

    3.  **Méthode :** ` GET`

    4.  **URI :** ` http://www.microsoft.com`

   	![][4]

7.  Après avoir créé un travail, définissez une planification. Le travail peut être défini comme un travail ponctuel, mais nous allons sélectionner une planification périodique. Certaines captures d'écran de ce didacticiel illustrent une périodicité de 1 minute uniquement à titre d'illustration, mais choisissez une périodicité de 12 heures.

    1.  **Répéter toutes les :** ` 12 Hours`  

    2.  **Début :** ` Now`

    3.  **Se terminant :** ` Select date 2 days after current day and any time`

   	![][5]

8.  Cliquez sur **OK**. La création du travail et de la collection de travaux peut prendre du temps. Pour vérifier l’état d’avancement de l’opération, vous pouvez contrôler les notifications au bas du portail.

   	![][6]

   	Une fois le travail et la collection de travaux créés, un message vous indique que l’opération a réussi. Le travail est répertorié dans la section Travaux de Scheduler et la collection de travaux est répertoriée dans la section Collections de travaux. Pour configurer des paramètres avancés supplémentaires dans le travail, reportez-vous à la section « Configurer un travail » ci-dessous.

   	![][7]

## Gérer et surveiller des collections de travaux et des travaux

Une fois une collection de travaux créée, celle-ci apparaît dans l’écran principal de gestion de Scheduler.

![][8]

Cliquez sur une collection de travaux pour ouvrir une nouvelle fenêtre avec les options suivantes :

1.  Tableau de bord  

2.  Mise à l'échelle

3.  Historique

4.  Travaux

Les rubriques suivantes décrivent ces onglets en détail.

### Tableau de bord

Quand vous cliquez sur le nom de la collection de travaux, l’onglet **Tableau de bord** apparaît. Le tableau de bord affiche les informations suivantes :

![][9]

#### Présentation de l’utilisation du travail et de l’utilisation de l’exécution

Un tableau et une série de graphiques affichant une liste fixe de mesures. Ces mesures fournissent des valeurs en temps réel concernant l'intégrité de votre collection de travaux, y compris :

1.  Travaux en cours  

2.  Travaux terminés

3.  Travaux ayant généré une erreur

4.  Travaux activés

5.  Travaux désactivés

6.  Exécutions de travaux

#### Aperçu rapide

Un tableau affichant une liste fixe des états et des mesures de paramètres. Ces mesures fournissent des valeurs en temps réel concernant l'état et les paramètres associés à votre collection de travaux, y compris :

1.  Statut  

2.  Région

3.  Nombre d'erreurs

4.  Nombre d'occurrences des pannes

5.  URI

### Mise à l'échelle

Dans l’onglet **Mise à l’échelle**, vous pouvez modifier les paramètres et le niveau de service utilisés par Scheduler.

![][10]

#### Généralités

Indique si vous êtes dans le plan **gratuit** ou **standard**.

#### Quotas

Azure Scheduler implémente des quotas en fonction de plusieurs conditions. Cette section répertorie les seuils de quota et vous permet de les modifier. Par défaut, un seul jeu de quotas est configuré. Les limites de ces paramètres de quota sont contraintes par votre plan et la modification du plan peut affecter la tarification. Les quotas peuvent être modifiés pour faire évoluer votre Scheduler. Les options incluent :

1.  Nombre maximal de travaux  

2.  Fréquence maximale

3.  Intervalle maximal

### Historique

L’onglet **Historique** affiche les informations suivantes pour le travail sélectionné :

![][11]

#### Table d’historique

Une table qui affiche des mesures sélectionnées pour chaque exécution du travail dans le système pour le travail sélectionné. Ces mesures fournissent des valeurs en temps réel concernant l'intégrité de votre Scheduler.

#### Mesures disponibles

Les mesures/compteurs de performances suivants sont disponibles :

1.  Statut  

2.  Détails

3.  Nouvelle tentative

4.  Nombre d'exécutions (1er, 2e, 3e, etc.)

5.  Horodateur de l'exécution

Vous pouvez cliquer sur **Afficher les détails de l’historique** pour examiner la réponse entière pour chaque exécution. Cette boîte de dialogue vous permet également de copier la réponse dans le Presse-papiers.

![][12]

### Travaux

L'onglet Travaux affiche les informations suivantes pour analyser l'historique d'exécution des travaux :

![][13]

#### Table des travaux

Une table qui affiche des mesures sélectionnées pour chaque travail dans le système. Ces mesures fournissent des valeurs en temps réel concernant l'intégrité de votre Scheduler.

#### Désactiver, activer ou supprimer un travail

Cliquez sur le nom d’un travail pour afficher les options permettant d’activer, de désactiver ou de supprimer le travail. Les travaux supprimés ne peuvent pas être récupérés.

#### Mesures disponibles

Les mesures et compteurs suivants sont disponibles :

1.  Nom  

2.  Dernière exécution

3.  Prochaine exécution

4.  Statut

5.  Fréquence

6.  Échecs

7.  Erreurs

8.  Exécutions

9.  Type d’action

### Configurer un travail

Cliquez sur un travail dans l’écran **Travaux** pour configurer ce travail. Cela vous permet de configurer des paramètres avancés supplémentaires au-delà de ceux disponibles dans l'Assistant de création rapide. Pour configurer un travail, cliquez sur la flèche droite en regard du nom du travail dans l’écran **Travaux**.

La page de configuration du travail vous permet de mettre à jour les paramètres du travail. La page de configuration du travail est affichée ci-dessous pour les travaux HTTP et HTTPS. Pour les types d'action de travail HTTP et HTTPS, vous pouvez modifier la méthode de tout verbe HTTP autorisé. Vous pouvez également ajouter, supprimer ou modifier les en-têtes et les informations d'authentification de base.

![][14]

La page de configuration du travail s'affiche comme indiqué ci-dessous pour les travaux de file d'attente de stockage. Pour les types d'action de file d'attente de stockage, vous pouvez modifier le compte de stockage, le nom de file d'attente, le jeton SAS et le corps. La section « Planification » (non illustrée ci-dessous) est identique à la section « Planification » pour les types d'action de travail HTTP/HTTPS.

![][15]

Enfin, pour tous les types d'actions, vous pouvez modifier la planification proprement dite et son comportement de périodicité. Vous pouvez modifier la date et l'heure de début, la planification de périodicité et la date et l'heure de fin (si le travail est périodique). Après avoir apporté des modifications, vous pouvez les enregistrer en cliquant sur **Enregistrer** ou les ignorer en cliquant sur **Ignorer**.

## Voir aussi

 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités de Scheduler](scheduler-concepts-terms.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Comment créer des planifications complexes et une périodicité avancée avec Azure Scheluler](scheduler-advanced-complexity.md)

 [Informations de référence sur l’API REST de Scheduler](https://msdn.microsoft.com/library/dn528946)

 [Informations de référence sur les applets de commande PowerShell de Scheduler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité de Scheduler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d’erreur de Scheduler](scheduler-limits-defaults-errors.md)

 [Authentification sortante de Scheduler](scheduler-outbound-authentication.md)



[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

<!---HONumber=September15_HO1-->