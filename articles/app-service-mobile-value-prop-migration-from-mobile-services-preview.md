<properties
	pageTitle="J'utilise Mobile Services. Comment App Service peut-il m'aider ?"
	description="Découvrez les avantages qu'App Service apporte dans le cadre de vos projets Mobile Services existants."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>J'utilise Mobile Services. Comment App Service peut-il m'aider ?

##Présentation
Votre service Mobile Service existant est sécurisé et restera pris en charge. Toutefois, un grand nombre d'avantages fournis par la plateforme *Azure App Service* pour votre application mobile ne sont pas disponibles aujourd'hui avec Mobile Services :  

- Offre plus simple, plus facile et plus économique pour les applications qui incluent à la fois des clients web et mobiles
- Nouvelles fonctionnalités d'hôte, y compris les tâches web, les enregistrements CName personnalisés et une meilleure analyse
- Intégration clé en main avec Office 365, Dynamics CRM, Salesforce et d'autres API SaaS stratégiques
- Prise en charge du code de backend Java et PHP, outre Node.js et .NET 
- Intégration clé en main avec Traffic Manager
- Connectivité à vos ressources locales et réseaux privés virtuels à l'aide de VNet outre les connexions hybrides
- Surveillance et dépannage de votre application à l'aide de NewRelic ou d'AppInsights, ainsi que des alertes
- Plus riche éventail de ressources de calcul sous-jacentes, p. ex. de tailles de machines virtuelles 
- Fonctionnalités intégrées de mise à l'échelle automatique, d'équilibrage de la charge et d'analyse des performances
- Fonctionnalités intégrées intermédiaires, de sauvegarde, de restauration et de test en production 

## Nouvelles fonctionnalités d'hébergement
Dans *Azure App Service*, le code de backend *Mobile App* s'exécute dans le même conteneur que l'application web et l'application API. Par conséquent, vous pouvez tirer parti de toutes les fonctionnalités dans ce conteneur, y compris de certaines qui ne figurent pas actuellement dans Mobile Services : 

- ajoutez une logique de backend qui s'exécute en permanence via les tâches web ; 
- assurez-vous que votre code de backend est toujours en cours d'exécution ;
- utilisez des enregistrements CName personnalisés pour fournir des noms conviviaux et stables aux points de terminaison de votre backend mobile ;
- dimensionnez géographiquement votre application avec Traffic Manager.


##Connectez votre application *Mobile App* à une API SaaS
*Azure App Service* facilite la connexion de votre application Mobile App à des API SaaS, dont notamment Office 365, Dynamics, Salesforce, SAP. *Azure App Service* propose une authentification clé en main de la part de l'utilisateur et vous permet d'effectuer une véritable authentification unique sur l'ensemble des API SaaS que vous utilisez en associant des jetons pour les API SaaS individuelles à votre identité principale.

##Accédez aux données locales à l'aide de VNet
Aujourd'hui, avec Mobile Services, vous pouvez déjà utiliser des connexions hybrides pour accéder à des ressources locales. Toutefois, dans certaines situations, une solution VPN est préférée. Avec *Azure App Service*, vous pouvez utiliser Azure VNet pour votre code de backend Mobile App.

##Utilisez votre langage de backend favori
*Azure App Service* offre une prise en charge plus large et plus riche pour les plateformes de développement, notamment Java, PHP et Python, en plus de .NET et Node.js pris en charge dans Mobile Services.

##Configurez la mise à l'échelle automatique
Avec Mobile Services, toutes les instances de votre code de backend s'exécutaient sur de petites machines virtuelles. *Azure App Service* vous permet de sélectionner la taille des machines virtuelles parmi un ensemble d'options beaucoup plus riche. Vous pouvez également augmenter rapidement la taille ou le nombre de vos machines virtuelles pour vous adapter à n'importe quelle charge cliente entrante, en fonction de diverses mesures de performances. 

##Restez dans la connaissance
Réagissez en temps réel aux problèmes avec la surveillance et les alertes qui vous informent automatiquement vous et votre équipe. Intégrez les fonctionnalités avancées de surveillance et d'analyse d'application de New Relic et AppInsights afin d'enrichir encore votre vision des performances de votre application mobile. Avec *Azure App Service*, vous pouvez maintenant configurer par programmation et via le portail des alertes basées sur diverses mesures de performances

##Protégez vos actifs
Sauvegardez automatiquement votre backend et votre base de données. Votre code et vos données sont protégés contre une éventuelle catastrophe et peuvent être restaurées facilement, ce qui vous permet de gérer votre entreprise en toute confiance.

##À vos marques, prêt, partez !
 *Azure App Service* vous permet désormais de créer plusieurs environnements intermédiaires et de test privés pour vos applications mobiles. Utilisez-les pour réaliser des tests avant un déploiement. Basculez en production sans interruption de service. Les applications web sont préchargées, ce qui garantit une expérience client optimale.



D'ici à ce que la fonctionnalité *Azure App Service Mobile App* devienne disponible de manière générale, nous vous fournirons une expérience de migration transparente pour vos applications Mobile Services vers App Service, si vous choisissez de migrer. En attendant, vous pouvez commencer à explorer *App Service* et tirer parti d' *App Service* pour votre application Mobile Service existante en suivant ce didacticiel. [tutorial](app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md).



<!--HONumber=49-->