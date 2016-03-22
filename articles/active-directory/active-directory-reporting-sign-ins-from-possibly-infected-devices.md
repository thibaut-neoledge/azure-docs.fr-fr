<properties
	pageTitle="Connexions à partir de périphériques potentiellement infectés"
	description="Un rapport incluant des tentatives de connexion effectuées à partir d’appareils sur lesquels des logiciels malveillants peuvent être exécutés."
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


# Connexions à partir de périphériques potentiellement infectés
Ce rapport tente d’identifier les appareils des utilisateurs qui ont été infectés et font à présent partie d’un botnet. Nous mettons en corrélation les adresses IP des connexions utilisateur et les adresses IP déterminées comme étant en contact avec des serveurs botnet.

Recommandation : ce rapport suit les adresses IP et non les appareils des utilisateurs. Nous vous recommandons de contacter l'utilisateur et d'analyser tous ses appareils afin d’en acquérir la certitude. Il est également possible que les appareils personnels d'un utilisateur soient infectés, ou qu'une tierce personne utilisant la même adresse IP possède un appareil infecté.

Pour plus d'informations sur le traitement des infections de logiciels malveillants, consultez le [Centre de protection contre les programmes malveillants](http://go.microsoft.com/fwlink/?linkid=335773).

![Connexions à partir de périphériques potentiellement infectés](./media/active-directory-reporting-sign-ins-from-possibly-infected-devices/signInsFromPossiblyInfectedDevices.PNG)

<!---------HONumber=AcomDC_0309_2016-->