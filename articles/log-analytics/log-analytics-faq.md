---
title: Log Analytics - FAQ | Microsoft Docs
description: "Réponses aux questions fréquemment posées sur le service Log Analytics."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e24e6f9cc383be77dc631a0dd67db099906dccc0


---
# <a name="log-analytics-faq"></a>FAQ sur Log Analytics
Ce FAQ Microsoft est une liste des questions fréquemment posées concernant Log Analytics dans Microsoft Operations Management Suite (OMS). Si vous avez d’autres questions sur Log Analytics, veuillez accéder au [forum de discussion](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) et publier vos questions. Un membre de notre communauté vous aidera à obtenir vos réponses. Si une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle puisse être trouvée rapidement et facilement.

## <a name="general"></a>Généralités
**Q. Quels sont les contrôles effectués par les solutions d’évaluation AD et SQL ?**

A. La requête suivante comporte une description de tous les contrôles effectués actuellement :

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Les résultats peuvent ensuite être exportés vers Excel pour être examinés.

**Q : Pourquoi vois-je quelque chose de différent d’*OMS* dans l’administration SCOM ?**

R : Selon le correctif cumulatif SCOM que vous utilisez, vous pouvez voir un nœud pour *System Center Advisor*, *Operational Insights* ou *Log Analytics*.

La mise à jour de la chaîne de texte vers *OMS* est incluse dans un pack d’administration, qui doit être importé manuellement. Suivez les instructions du dernier article de la base de connaissances sur le correctif cumulatif de SCOM et actualisez la console OMS pour voir les dernières mises à jour du nœud *OMS* .

**Q : existe-t-il une version *locale* d’OMS ?**

R : Non. Log Analytics traite et stocke de grandes quantités de données. En tant que service cloud, Log Analytics peut évoluer si nécessaire et éviter tout impact sur les performances de votre environnement.

Cela signifie également que vous n’avez pas besoin de maintenir l’infrastructure Log Analytics opérationnelle et que vous pouvez recevoir fréquemment des mises à jour et des correctifs.

## <a name="configuration"></a>Configuration
**Q. Puis-je modifier le nom du conteneur d’objets blob ou de tables utilisé pour lire à partir d’Azure Diagnostics (WAD) ?**  

R.    Non, cela n’est pas possible actuellement, mais est prévu pour une version ultérieure.

**Q. Quelles adresses IP les services OMS utilisent-ils ? Comment vérifier que mon pare-feu autorise uniquement le trafic vers les services OMS ?**  

A. Le service Log Analytics s’appuie sur Azure et les points de terminaison reçoivent des adresses IP se trouvant dans les [plages IP des centres de données Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653).

Les adresses IP réelles des services OMS changent au fil de la création des déploiements de services Les noms DNS autorisés par votre pare-feu sont documentées à l’adresse [Configurer les paramètres de pare-feu et de proxy dans Log Analytics](log-analytics-proxy-firewall.md).

**Q. J’utilise ExpressRoute pour me connecter à Azure. Mon trafic Log Analytics utilisera-t-il ma connexion ExpressRoute ?**  

R. Les différents types de trafic ExpressRoute sont décrits dans la [documentation ExpressRoute](../expressroute/expressroute-faqs.md#supported-services).

Le trafic vers Log Analytics utilise le circuit ExpressRoute d’homologation publique.

**Q. Existe-t-il un moyen simple de déplacer un espace de travail Log Analytics existant vers un autre espace de travail ou abonnement Azure Log Analytics ?**  Nous avons des espaces de travail OMS de plusieurs client que nous avons testés et évalués dans notre abonnement Azure. Ils passent à présent en production et nous voulons les déplacer vers leur propre abonnement Azure/OMS.  

A. L’applet de commande `Move-AzureRmResource` vous permet de déplacer un espace de travail Log Analytics et également un compte Automation d’un abonnement Azure à un autre. Pour plus d’informations, consultez [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Cette modification peut également être effectuée dans le portail Azure.

Vous ne pouvez pas déplacer les données d’un espace de travail Log Analytics vers un autre ou modifier la région de données dans laquelle les données Log Analytics sont stockées.

**Q : Comment ajouter OMS à SCOM ?**

R : La mise à jour vers le dernier correctif cumulatif et l’importation des packs d’administration permettent de connecter SCOM à Log Analytics.

Notez que la connexion de SCOM à Log Analytics est uniquement disponible pour SCOM 2012 SP1 et versions ultérieures.

**Q : Comment puis-je vérifier qu’un agent est en mesure de communiquer avec Log Analytics ?**

R : Pour s’assurer que l’agent peut communiquer avec OMS, accédez à : Panneau de configuration, Security & Settings (Sécurité et paramètres), **Microsoft Monitoring Agent**.

Sous l’onglet **Azure Log Analytics (OMS)** , recherchez une coche verte. Une icône de coche verte confirme que l’agent est en mesure de communiquer avec le service OMS.

Une icône d’avertissement jaune signifie que l’agent rencontre des problèmes de communication avec OMS. Une raison courante est que le service Microsoft Monitoring Agent a été interrompu et doit être redémarré.

**Q : Comment interrompre la communication d’un agent avec Log Analytics ?**

R : Dans SCOM, supprimez l’ordinateur de la liste gérée par OMS. Cela a pour effet d’arrêter toutes les communications via SCOM pour cet agent. Pour les agents connectés directement à OMS, vous pouvez interrompre leur communication avec OMS via : Panneau de configuration, Security & Settings (Sécurité et paramètres), **Microsoft Monitoring Agent**.
Sous **Azure Log Analytics (OMS)**, supprimez tous les espaces de travail répertoriés.

## <a name="agent-data"></a>Données de l’agent
**Q. Quelle quantité de données puis-je envoyer via l’agent à Log Analytics ? Existe-t-il une quantité maximale de données par client ?**  
R. Le forfait gratuit définit une limite quotidienne de 500 Mo par espace de travail. Les forfaits standard et premium ne présentent aucune limite concernant la quantité de données chargées. Comme un service cloud, Log Analytics dans OMS est conçu pour monter en puissance automatiquement afin de traiter le volume provenant d’un client, même si cela se chiffre en téraoctets par jour.

L’agent Log Analytics a été conçu pour garantir un faible encombrement et effectuer une compression des données de base. Un de nos clients a écrit un blog sur les tests effectués avec notre agent et a expliqué dans quelle mesure il a été impressionné. Le volume de données varie selon les solutions utilisées par vos clients. Vous pouvez trouver des informations détaillées sur le volume de données et voir la répartition par solution sous la vignette **Utilisation** de la page de présentation d’OMS.

Pour en savoir plus, vous pouvez consulter le [blog d’un client](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sur le faible encombrement de l’agent OMS.

**Q. Quelle quantité de bande passante réseau est utilisée par Microsoft Management Agent (MMA) lors de l’envoi de données à Log Analytics ?**

R. La bande passante est fonction de la quantité de données envoyées. Les données sont compressées à mesure de leur envoi sur le réseau

**Q. Quelle quantité de données est envoyée par agent ?**

R. Cela dépend en grande partie des éléments suivants :

* des solutions que vous avez activées
* du nombre de journaux et de compteurs de performances collectés
* du volume de données des journaux

Le niveau tarifaire gratuit est un bon moyen d’intégrer plusieurs serveurs et de mesurer le volume de données classique. L’utilisation globale est indiquée sur la page **Utilisation** .
Pour les ordinateurs en mesure d’exécuter l’agent WireData, vous pouvez voir la quantité de données en cours d’envoi à l’aide de la requête suivante :

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Étapes suivantes
* [Familiarisez-vous avec Log Analytics](log-analytics-get-started.md) pour en savoir plus sur Log Analytics et être opérationnel en quelques minutes.




<!--HONumber=Nov16_HO3-->


