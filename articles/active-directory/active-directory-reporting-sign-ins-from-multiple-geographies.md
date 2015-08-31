<properties
	pageTitle="Connexions depuis plusieurs zones géographiques"
	description="Un rapport qui indique les utilisateurs lorsque deux connexions semblent être issues de régions différentes, alors que le laps de temps constaté entre ces connexions ne permet pas à l'utilisateur de se rendre d'une région à une autre."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Connexions depuis plusieurs zones géographiques

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| <p>Ce rapport inclut les activités de connexion réussie d'un utilisateur durant lesquelles deux connexions semblent être issues de régions différentes, mais dont le laps de temps constaté entre ces connexions ne permet pas à l'utilisateur de se rendre d’une région à une autre. Les causes possibles sont :</p><ul><li>L’utilisateur partage son mot de passe</li><li>L’utilisateur utilise un bureau à distance pour se connecter via un navigateur Web</li><li>Un pirate s'est connecté au compte d'un utilisateur depuis un autre pays.</li></ul><p>Les résultats de ce rapport vous indiqueront les événements de connexion réussie, ainsi que le laps de temps constaté entre les connexions, les régions d’où semblent provenir les connexions et le temps qu’il faut à l’utilisateur pour se rendre d’une région à une autre.</p><p>Cette durée indiquée n’est qu'une estimation et elle peut être différente de la durée réelle entre ces emplacements. De plus, aucun événement n'est généré pour les connexions constatées dans différentes régions voisines.</p> | Répertoire > onglet Rapports |

![Connexions depuis plusieurs zones géographiques](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=August15_HO8-->