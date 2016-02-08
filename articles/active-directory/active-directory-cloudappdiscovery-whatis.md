<properties
	pageTitle="Détection des applications cloud non gérées avec Cloud App Discovery | Microsoft Azure"
	description="Fournit une vue d'ensemble de la recherche et de la gestion d'applications avec Cloud App Discovery, ainsi que des informations sur ses avantages et son fonctionnement."
	services="active-directory"
	keywords="détection d'applications cloud, gestion d'applications"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="markvi"/>

# Détection des applications cloud non gérées avec Cloud App Discovery

## Vue d’ensemble
Dans les entreprises modernes, les services informatiques n’ont souvent pas connaissance de toutes les applications cloud utilisées par les membres de l’organisation pour effectuer leur travail. Il est facile de comprendre pourquoi les administrateurs s’inquiètent d’un accès non autorisé aux données d'entreprise, de possibles fuites de données et autres risques de sécurité. Négliger cet aspect peut sérieusement compliquer l’élaboration d'un plan visant à gérer ces risques de sécurité.

Cloud App Discovery est une fonctionnalité Premium d’Azure Active Directory (AD) qui vous permet de détecter les applications cloud utilisées par les membres de votre organisation.

**Avec Cloud App Discovery, vous pouvez :**

- détecter les applications cloud utilisées et mesurer l’utilisation par nombre d’utilisateurs, volume de trafic ou nombre de demandes web à l’application ;
- identifier les utilisateurs d’une application ;
- exporter des données pour effectuer une analyse hors ligne ;
- confier le contrôle de ces applications au service informatique et activer l'authentification unique pour la gestion des utilisateurs.

## Fonctionnement
1. Des agents d'utilisation des applications sont installés sur les ordinateurs de l'utilisateur.
2. Les informations sur l’utilisation des applications capturées par les agents sont envoyées via un canal sécurisé et chiffré au service Cloud App Discovery.
3. Celui-ci évalue les données et génère des rapports.

![Diagramme Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)

Pour prendre en main Cloud App Discovery, consultez [Prise en main de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).

## Articles connexes
- [Considérations relatives à la confidentialité et à la sécurité de Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
- [Guide de déploiement d’une stratégie de groupe Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)
- [Guide de déploiement de Cloud App Discovery System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)
- [Paramètres de Registre de Cloud App Discovery pour les services de proxy avec ports personnalisés](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
- [Cloud App Discovery - Agent Changelog ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
- [Cloud App Discovery - Forum aux Questions](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)

<!---HONumber=AcomDC_0128_2016-->