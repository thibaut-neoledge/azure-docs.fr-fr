<properties 
	pageTitle="Surveillez votre infrastructure d’identité locale dans le cloud." 
	description="Cette page décrit Azure AD Connect Health et vous explique pourquoi l’utiliser." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Surveillance de votre infrastructure d’identité locale dans le cloud

Azure AD Connect Health vous permet de surveiller votre infrastructure d’identité locale et d’en tirer des informations. Vous pouvez ainsi consulter des alertes, en savoir plus sur les performances, connaître les modèles d’utilisation, découvrir les paramètres de configuration, maintenir une connexion fiable à Office 365 et bien plus encore. Tout ceci peut être accompli à l’aide d’un agent installé sur les serveurs cibles. Pour en savoir plus, consultez la page [Azure Active Directory Connect Health.](https://msdn.microsoft.com/library/azure/dn906722.aspx)

![Présentation d’Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Ces informations sont toutes disponibles dans le portail Azure AD Connect Health. Ce portail vous permet d’afficher des alertes, de surveiller les performances et d’analyser les utilisations. Ces informations sont présentées au même endroit pour faciliter leur utilisation et vous permettre de gagner du temps en trouvant immédiatement l’information dont vous avez besoin.

![Présentation d’Azure AD Connect Health](./media/active-directory-aadconnect-health/usage.png)

Les mises à jour prévues pour Azure AD Connect Health incluent une amélioration de la surveillance et de l’analyse d’autres composants d’identité et de services tels que les services Azure AD Connect Sync. Vous pouvez ainsi bénéficier d’un tableau de bord unique focalisé sur l’identité, vous permettant de profiter d’un environnement intégré, sécurisé et robuste que vos utilisateurs peuvent exploiter pour améliorer leur productivité.

Pour en savoir plus, consultez la page [Azure AD Connect Health.](https://msdn.microsoft.com/library/azure/dn906722.aspx)


![Présentation d’Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)




## Pourquoi utiliser Azure AD Connect Health

L’intégration de vos annuaires locaux avec Azure AD améliore la productivité de vos utilisateurs en leur fournissant une identité commune pour accéder aux ressources cloud et locales. Toutefois, avec cette intégration, vous devez vous assurer que cet environnement est sécurisé pour que les utilisateurs puissent accéder aux ressources locales et cloud en toute sécurité depuis n’importe quel appareil. Azure AD Connect Health fournit une approche basée sur le cloud pour surveiller et obtenir facilement des informations sur l’infrastructure d’identité locale que vous utilisez pour accéder à Office 365 ou à d’autres applications Azure AD. Son installation est aussi simple que celle d’un agent sur chacun de vos serveurs d’identité local.

Azure AD Connect Health pour AD FS prend en charge AD FS 2.0 dans Windows Server 2008/2008 R2, AD FS Windows Server 2012/2012 R2. Cette prise en charge inclut également tous les serveurs AD FS Proxy AD FS ou les serveurs Proxy d’application web qui permettent la prise en charge de l’authentification pour l’accès extranet. Azure AD Connect Health pour AD FS fournit les fonctionnalités clé suivantes :

- Affichage d’alertes et prises de mesures pour accéder de façon fiable aux applications AD FS protégées, y compris Azure AD
- Notifications par courrier électronique pour les alertes critiques
- Affichage des données de performances pour déterminer la planification de capacité
- Affichage détaillé de vos modèles de connexion AD FS pour déterminer les anomalies ou établir des lignes de base pour la planification de capacité



----------------------------------------------------------------------------------------------------------
## Téléchargement de l’agent Azure AD Connect Health

Pour commencer à utiliser Azure AD Connect Health, vous pouvez télécharger sa dernière version ici : [Télécharger l’agent Azure AD Connect Health](http://go.microsoft.com/fwlink/?LinkID=518973) Vérifiez que vous avez ajouté le service à partir de Marketplace avant d’installer les agents.

----------------------------------------------------------------------------------------------------------





**Ressources supplémentaires**

* [Azure AD Connect Health sur MSDN](https://msdn.microsoft.com/library/azure/dn906722.aspx)


 

<!---HONumber=62-->