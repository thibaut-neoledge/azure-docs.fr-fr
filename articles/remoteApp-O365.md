<properties title="Using Office365 with Azure RemoteApp" pageTitle="Utilisation d'Office 365 avec Azure RemoteApp" description="Découvrez les interactions entre Office 365 et Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#Utilisation d'Office 365 avec Azure RemoteApp

Vous avez deux options pour héberger les applications Office dans RemoteApp : Office 365 ProPlus ou la version d'évaluation d'Office Professionnel Plus 2013.

##Office 365 ProPlus 
Vous pouvez créer une collection RemoteApp à l'aide de l'image de modèle Office 365 ProPlus. Cette option vous permet d'étendre votre service Office 365 à RemoteApp. Vous devez déjà disposer d'un abonnement et vos utilisateurs doivent bénéficier d'une licence pour le service Office 365 ProPlus (en version autonome ou via les plans de service Office 365). Les instances RemoteApp des applications Office sont prises en compte dans le total de cinq instances d'installation autorisées pour chaque utilisateur et toutes leurs fonctionnalités sont activées. Vous pouvez gérer les licences Office 365 de vos utilisateurs dans le [portail d'administration Office 365](https://portal.office365.com/). En savoir plus sur les [plans de service Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  

L'option Office 365 ProPlus est disponible pendant la période d'évaluation gratuite de 30 jours et en mode de production, mais elle est la seule option prise en charge après l'expiration de la version d'évaluation.  

Notez que vous pouvez également créer une image de modèle personnalisée contenant Office 365 ProPlus. Pour créer cette image de modèle, suivez les [étapes de déploiement](http://technet.microsoft.com/fr-fr/library/dn782858(v=office.15).aspx) pour Office 365 ProPlus sur RDS.

##Version d'évaluation d'Office Professionnel Plus 2013 
Pendant la période d'évaluation de 30 jours de RemoteApp, vous pouvez utiliser l'image de modèle Office Professionnel Plus 2013 (version d'évaluation) pour créer une collection RemoteApp. Vous pouvez attribuer des utilisateurs à cette collection d'évaluation à l'aide de leurs comptes professionnels Azure Active Directory ou comptes Microsoft. Aucun abonnement supplémentaire n'est nécessaire.

C'est l'option idéale pour débuter et tester l'utilisation d'Office dans RemoteApp. Toutefois, cette option est destinée à des fins d'évaluation et de test uniquement. Les collections RemoteApp créées à l'aide de l'image de modèle Office Professionnel Plus 2013 (version d'évaluation) ne peuvent pas être passées en mode de production et seront désactivées à la fin de la période d'évaluation.

##Passage de la version d'évaluation au mode de production
Quand vous commencez votre période d'évaluation gratuite de 30 jours, une note dans la section RemoteApp du portail vous indique combien de temps il vous reste avant de basculer sur un compte payant. Vous pouvez activer votre compte et passer en mode de production à l'aide du lien figurant dans cette note. 

L'activation de votre compte concerne toutes les collections RemoteApp de votre compte. 

- Les collections exécutées dans Windows Server 2012 R2 ou les images de modèle Office 365 ProPlus passent naturellement en mode de production. Tous les paramètres et données utilisateur, y compris les sessions en cours, restent intacts.
- Si vous avez téléchargé des images de modèle personnalisées, les collections qui utilisent ces images sont également transférées naturellement.
- L'image de modèle Office Professionnel Plus 2013 (version d'évaluation) est destinée à des fins d'évaluation uniquement. Les collections qui s'exécutent dans cette image de modèle ne peuvent pas être passées en mode de production. Elles sont mises en état " désactivé ".


Si vous ne passez pas en mode de production à l'expiration de votre version d'évaluation, vos collections RemoteApp sont désactivées. Ne vous inquiétez pas, vos paramètres et données utilisateur sont enregistrés pendant 90 jours supplémentaires, vous pouvez donc toujours activer votre service et passer en mode de production sans perte de données.

<!--HONumber=35.2-->
