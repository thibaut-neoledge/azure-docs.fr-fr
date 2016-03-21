<properties
	pageTitle="Activité de connexion anormale"
	description="Un rapport qui inclut des connexions qui ont été identifiées comme « anormales » par nos algorithmes d'apprentissage automatique (Machine Learning)."
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

# Activité de connexion anormale

Les connexions irrégulières sont celles qui ont été identifiées par nos algorithmes d’apprentissage automatique, sur la base d'une condition de « déplacement impossible » associée à un emplacement et un périphérique de connexion anormaux. Cela peut signifier qu'un pirate est parvenu à se connecter à l'aide de ce compte. Nous envoyons une notification par courrier électronique aux administrateurs globaux si nous trouvons au moins 10 événements de connexion anormaux dans un intervalle de moins de 30 jours. Veillez à inclure aad-alerts-noreply@mail.windowsazure.com dans votre liste d'expéditeurs approuvés.

<!---------HONumber=AcomDC_0309_2016-->