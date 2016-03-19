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
<p>Ce rapport inclut les connexions réussies d'un utilisateur durant lesquelles deux connexions semblent être issues de régions différentes, mais dont le laps de temps constaté entre ces connexions ne permet pas à l'utilisateur de se rendre d’une région à une autre. Les causes possibles sont :</p><ul><li>L’utilisateur partage son mot de passe avec d’autres utilisateurs</li><li>L’utilisateur utilise un bureau à distance pour se connecter via un navigateur Web</li><li>Un pirate s'est connecté au compte d'un utilisateur depuis un autre pays.</li><li>L’utilisateur se sert d’un VPN ou d’un proxy</li><li>L’utilisateur est connecté depuis plusieurs appareils en même temps, comme un ordinateur de bureau et un téléphone mobile et l’adresse IP du téléphone mobile est inhabituelle.</li></ul><p>Les résultats de ce rapport vous indiqueront les événements de connexion réussie, ainsi que le laps de temps constaté entre les connexions, les régions d’où semblent provenir les connexions et le temps qu’il faut à l’utilisateur pour se rendre d’une région à une autre.</p><p>Cette durée indiquée n’est qu'une estimation et elle peut être différente de la durée réelle pour se rendre d’un emplacement à un autre.</p>


![Connexions depuis plusieurs zones géographiques](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=Oct15_HO3-->