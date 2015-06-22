<properties 
	pageTitle="Sécurité Operational Insights" 
	description="Operational Insights est un service d'analyse qui permet aux administrateurs d'analyser en profondeur les environnements locaux et cloud. Il permet d'interagir avec les données machine historiques et en temps réel afin de développer rapidement des analyses personnalisées, et il fournit des modèles d'analyse de données développés par Microsoft et la communauté." 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>





<h1 id="">Sécurité Operational Insights</h1>


Microsoft s'engage à protéger votre vie privée et à sécuriser vos données, tout en proposant des logiciels et des services favorisant la gestion de l'infrastructure informatique de votre organisation. Nous reconnaissons que lorsque vous confiez vos données à d'autres personnes, cette confiance exige une sécurité rigoureuse. Microsoft adhère aux recommandations strictes de conformité et de sécurité, du codage jusqu'à l'exploitation d'un service.

La sécurisation et la protection des données constituent une priorité de premier plan pour Microsoft. N'hésitez pas à nous contacter pour toute question, suggestion ou remarque concernant les informations fournies ci-dessous, y compris notre politique de sécurité : <a href="mailto:scdata@microsoft.com" target="_blank">scdata@microsoft.com</a>.

Cet article explique comment les données sont collectées, traitées et sécurisées dans Microsoft Azure Operational Insights. Vous pouvez utiliser les agents pour vous connecter directement au service web ou vous pouvez utiliser System Center Operations Manager pour collecter des données opérationnelles pour le service Operational Insights. Les données collectées sont envoyées via Internet au service Operational Insights, qui est hébergé dans Microsoft Azure.

Le service Operational Insights gère vos données en toute sécurité en utilisant les méthodes suivantes :

**Ségrégation des données :** les données client sont maintenues séparées logiquement sur chaque composant, dans l'ensemble du service Operational Insights. Toutes les données sont marquées en fonction de l'organisation. Ce balisage est conservé tout au long du cycle de vie des données, et il est appliqué dans chaque couche du service. 

Chaque client possède un objet blob Azure dédié qui héberge les données à long terme. Cet objet blob est chiffré à l'aide de clés uniques propres au client, qui sont modifiées tous les 90 jours.

**Conservation des données :** des métriques agrégées pour chaque pack d'analyse décisionnelle sont stockées dans une base de données SQL hébergée par Microsoft Azure. Ces données sont stockées 390 jours. Les données indexées de recherche sont stockées pendant 10 jours en moyenne avant d'être nettoyées. Si la limite supérieure de 20 millions d'enregistrements pour chaque type de données est atteinte plus tôt, Operational Insights nettoie les données avant 10 jours. Si la limite de données n'est pas atteinte dans les 10 jours, Operational Insights attend que la limite soit atteinte pour les nettoyer.

**Sécurité physique :** le fonctionnement du service Operational Insights est assuré par le personnel de Microsoft et toutes les activités sont consignées et peuvent faire l'objet d'un audit. Le service Operational Insights s'exécute entièrement dans Azure et satisfait aux critères de conception communs d'Azure. Vous pouvez afficher plus d'informations sur la sécurité physique des ressources Azure à la page 18 du document  <a href="http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf" target="_blank">Vue d'ensemble de la sécurité de Microsoft Azure</a>.

**Conformité et certifications :** l'équipe de développement et de maintenance d'Operational Insights travaille en étroite collaboration avec les équipes Microsoft chargées des aspects juridiques et de conformité, ainsi qu'avec d'autres partenaires du secteur, afin d'acquérir un éventail de certifications, notamment ISO, avant la mise sur le marché globale du service Operational Insights.

Les normes de sécurité suivantes sont actuellement satisfaites :

- Critères de conception communs Windows
- Certification Trustworthy Computing de Microsoft



<!--HONumber=45--> 
 