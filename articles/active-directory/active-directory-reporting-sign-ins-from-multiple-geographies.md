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
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# Connexions depuis plusieurs zones géographiques

Ce rapport inclut les connexions réussies d’un utilisateur durant lesquelles deux connexions semblent être issues de régions différentes, mais dont le laps de temps constaté entre ces connexions ne permet pas à l’utilisateur de se rendre d’une région à une autre. Les causes possibles sont :

- L’utilisateur partage son mot de passe avec d’autres utilisateurs

- L’utilisateur utilise un bureau à distance pour lancer un navigateur web pour la connexion

- Un pirate s’est connecté au compte d’un utilisateur à partir d’un autre pays

- L’utilisateur utilise une connexion VPN ou proxy

- L’utilisateur est connecté à partir de plusieurs appareils en même temps, par exemple un ordinateur de bureau et un téléphone mobile, et l’adresse IP du téléphone mobile est inhabituelle.

Les résultats de ce rapport vous indiqueront les événements de connexion réussie, ainsi que le laps de temps entre les connexions, les régions d’où les connexions semblent être issues et le temps de trajet estimé entre ces régions. Le temps de trajet indiqué n’est qu’une estimation et peut être différent de la durée de voyage réelle entre les emplacements.


![Connexions depuis plusieurs zones géographiques](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!------HONumber=AcomDC_0309_2016-->