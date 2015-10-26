<properties 
	pageTitle="Comment puis-je détecter les applications cloud non approuvées utilisées au sein de mon organisation ?" 
	description="Cette rubrique explique ce qu’est Cloud App Discovery et pourquoi l’utiliser." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Comment puis-je détecter les applications cloud non approuvées utilisées au sein de mon organisation ?

Dans les entreprises modernes, les services informatiques n’ont souvent pas connaissance de toutes les applications cloud utilisées par les utilisateurs pour effectuer leur travail. Ainsi, les administrateurs ont souvent des inquiétudes quant à l’accès non autorisé aux données d’entreprise, aux fuites de données possibles et autres risques de sécurité inhérents aux applications. Comme ils ignorent le nombre et la nature des applications utilisées, la simple ébauche d’un plan visant à gérer ces risques peut sembler décourageante.

Vous pouvez résoudre ces problèmes à l’aide de Cloud App Discovery. Cloud App Discovery est une fonctionnalité Premium d’Azure Active Directory qui vous permet de détecter les applications cloud utilisées par les employés de votre organisation.


**Avec Cloud App Discovery, vous pouvez :**

* détecter les applications utilisées et mesurer l’utilisation par nombre d’utilisateurs, volume de trafic ou nombre de demandes web à l’application ; 
* identifier les utilisateurs d’une application ; 
* exporter des données pour effectuer une analyse hors ligne approfondie ; 
* Affecter une priorité aux applications à mettre sous contrôle du service Informatique et intégrer les applications en toute simplicité pour activer la gestion des utilisateurs et l’authentification unique. 

Avec Cloud App Discovery, la partie récupération des données est assurée par des agents qui s’exécutent sur les ordinateurs dans vos environnements. Les informations sur l’utilisation des applications capturées par les agents sont envoyées via un canal sécurisé et chiffré au service Cloud App Discovery. Celui-ci évalue les données et génère des rapports que vous pouvez utiliser pour analyser votre environnement.


<center>![Fonctionnement de Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)</center>

##Étapes suivantes


* Pour en savoir plus sur le fonctionnement de Cloud App Discovery, consultez [Prise en main de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx). 
* Pour plus d’informations sur les considérations relatives à la sécurité et à la confidentialité, consultez [Considérations relatives à la confidentialité et à la sécurité de Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md). 
* Pour plus d’informations sur le déploiement de l’agent Cloud App Discovery dans un environnement d’entreprise avec : 
 * la Gestion des stratégies de groupe Active Directory, consultez le [Guide de déploiement de Cloud App Discovery avec la stratégie de groupe](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx). 
 * Microsoft System Center Configuration Manager, consultez le [Guide de déploiement de Cloud App Discovery avec System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx). 
 * des serveurs proxy avec ports personnalisés, consultez [Paramètres de Registre de Cloud App Discovery pour les services de proxy avec ports personnalisés](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md). 





**Ressources supplémentaires**


* [Cloud App Discovery - Agent Changelog ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Cloud App Discovery - Forum aux Questions](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)

<!---HONumber=Oct15_HO3-->