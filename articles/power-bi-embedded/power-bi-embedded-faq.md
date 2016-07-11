<properties
   pageTitle="Forum Aux Questions"
   description="Forum Aux Questions de Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/28/2016"
   ms.author="owend"/>

# Forum Aux Questions de Power BI Embedded

1. **Qu’avez-vous annoncé à propos de Power BI à la conférence Build ?**

    Microsoft a annoncé que la version préliminaire publique du service Microsoft Power BI Embedded est désormais disponible.

2.	**Pouvez-vous nous présenter le service Microsoft Power BI Embedded ?**

    Microsoft Power BI Embedded est un service Azure qui permet aux développeurs d’applications d’incorporer de superbes rapports et visualisations interactifs dans vos applications destinées aux clients, sans que vous ayez à créer vos propres contrôles de A à Z. Imaginez le gain de temps et d’argent que cela représente ! Vous pouvez choisir parmi un large éventail de visualisations des données prêtes à l’emploi ou créer facilement des visualisations personnalisées pour répondre aux besoins spécifiques de votre application. Power BI Embedded est conçu pour les développeurs d’applications et éditeurs de logiciels indépendants qui souhaitent fournir du contenu BI à leurs clients en l’intégrant à leur application.

3.	**À qui et en quoi Microsoft Power BI Embedded peut-il être utile ?**

    Microsoft Power BI Embedded s’adresse aux développeurs d’applications qui souhaitent permettre à leurs utilisateurs d’afficher des visualisations de données attrayantes et interactives, sur tous les types d’appareils, sans avoir à les créer eux-mêmes. Grâce aux **requêtes directes** de Power BI Embedded, les développeurs fournissent des visualisations de données toujours actualisées. Ils peuvent aussi utiliser les API d’ARM et les API de Power BI pour déployer, gérer et automatiser Power BI par programmation. Comme c’est toujours le cas avec Power BI, le service incorporé est automatiquement mis à l’échelle en fonction de l’usage et des besoins de votre application. Le service Power BI Embedded applique un modèle de tarification basé sur le paiement à l’utilisation. Pour connaître les prix appliqués, consultez la section [Comment ce service sera-t-il facturé ?](#price).

4.	**Quelle est la relation entre Power BI Embedded et le service Power BI ?**

    Power BI Embedded et le service Power BI sont des offres distinctes. Power BI Embedded applique un modèle de facturation basée sur la consommation réelle et est déployé via le portail Azure. Il est conçu pour permettre aux éditeurs de logiciels indépendants d’incorporer des visualisations de données dans les applications destinées à leurs clients. Le service Power BI, facturé et déployé via le portail O365, est une offre BI autonome à usage général, principalement destinée à une utilisation interne en entreprise. Pour plus d’informations sur le service Power BI, consultez [www.powerbi.com](www.powerbi.com).

5.	**De quelle façon Power BI Embedded améliore-t-il mon application ?**

    Votre application sera beaucoup plus utile aux utilisateurs si elle leur fournit directement des visualisations de données qui les aident à prendre les bonnes décisions. Utilisez Power BI Embedded pour enrichir votre application avec des visualisations de données attrayantes, interactives et actualisées en temps réel. Vous pourrez ainsi proposer une application offrant plus d’intérêt, et donc satisfaire et fidéliser davantage vos utilisateurs, et fournir facilement des analyses contextuelles sur tous les appareils.

6.	**L’utilisation de Power BI Embedded dans mon application est-elle soumise à certaines règles ou limitations ?**

    Vous pouvez utiliser le service Power BI Embedded dans une application que vous développez uniquement si votre application (1) ajoute des fonctionnalités importantes et majeures au service Power BI Embedded et ne constitue pas essentiellement une alternative à un service Power BI, et (2) est fournie uniquement à des utilisateurs extérieurs à votre société. Vous ne pouvez pas utiliser le service Power BI Embedded dans des applications métier internes.

7.	**Est-il possible d’utiliser Power BI Embedded pour créer des applications internes ?**

    Non, Power BI Embedded est uniquement destiné à un usage par des utilisateurs externes. Il ne peut pas être utilisé dans des applications métier internes. Pour incorporer et utiliser du contenu Power BI dans des applications professionnelles internes, vous devez utiliser le service Power BI, et toutes les personnes utilisant ce contenu doivent avoir une licence d’abonnement utilisateur valide à Power BI Gratuit ou à Power BI Pro. Pour plus d’informations sur l’incorporation interne à l’aide du service Power BI, consultez [https://dev.powerbi.com](https://dev.powerbi.com).

8.	**Ce service est-il disponible partout dans le monde ?**

    Comme nous l’avons annoncé lors de la BUILD 2016, le service Power BI Embedded est disponible en Amérique du Nord (dans le centre de données du Sud-Centre des États-Unis). Nous prévoyons de déployer ce service très prochainement dans tous les autres centres de données Azure.

9. **Quel est le contrat SLA disponible pour le service ?**

    Power BI Embedded est actuellement disponible comme service Azure en version préliminaire, sans contrat SLA particulier. Un contrat SLA sera fourni au moment de la mise en disponibilité générale de ce service.

10. <a name="price"/>**Comment ce service sera-t-il facturé ?**

    Power BI Embedded est actuellement en version préliminaire et restera disponible gratuitement jusqu’au 1er mai 2016. À partir du 1er mai 2016, le service sera facturé au rendu. Les clients pourront acheter le service via deux options de licence principales : le programme d’abonnement en ligne Microsoft (MOSP) ou le programme de licence en volume Enterprise.

    Vous pouvez utiliser le service Power BI Embedded dans une application que vous développez uniquement si votre application (1) ajoute des fonctionnalités importantes et majeures à notre service et ne constitue pas essentiellement une alternative à un service Power BI, et (2) est fournie uniquement à des utilisateurs externes. Vous ne pouvez pas utiliser le service Power BI Embedded dans des applications métier internes.


   ![](media\power-bi-embedded-faq\price.png)

11. **Qu’est-ce qu’un rendu et comment est-il facturé ?**

    Un rendu correspond à une visualisation qui s’affiche à un utilisateur final en réponse à une requête adressée au service. Le service Power BI tente systématiquement de mettre en cache le contenu rendu pour réduire le nombre de rendus pour lesquels votre application sera facturée. Toutefois, certaines actions de l’utilisateur, telles que le filtrage, peuvent déclencher l’envoi d’une requête à notre service, ce qui constitue un nouveau rendu.

    Par exemple, si un utilisateur affiche un rapport avec quatre éléments visuels, cette action peut donner lieu à quatre rendus. Si l’utilisateur actualise le rapport avec d’autres requêtes envoyées au service, il en résulte quatre rendus supplémentaires. Pour limiter l’exposition du coût et réduire les coûts dans des scénarios d’analyse de données statiques, le propriétaire du service peut contrôler le nombre de requêtes engendrant des rendus payants que les utilisateurs finaux peuvent émettre.

    Les rendus sont facturés par tranches de 1 000 unités. Pour les quantités de rendus inférieures à 1 000, la facturation est établie au pro rata. Les clients reçoivent 1 000 rendus gratuits par mois. Les clients qui achètent le service via des contrats de licence en volume doivent consulter leur partenaire ou revendeur Microsoft pour obtenir les informations de tarification.

12. **Fournissez-vous des outils ou une assistance pour m’aider à estimer le nombre de rendus ? Comment suivre le nombre de rendus réalisés ?**

    Vous trouverez sur le portail Azure la facturation détaillée des rendus comptabilisés dans le cadre de votre abonnement.

13. **Ai-je besoin d’un abonnement Power BI pour développer des applications avec Power BI Embedded ? Comment démarrer ?**

    En tant que développeur d’applications, vous n’avez pas besoin d’abonnement Power BI pour créer les rapports et les visualisations que vous souhaitez incorporer dans votre application. Vous avez besoin d’un abonnement Microsoft Azure et de l’application gratuite Power BI Desktop.

    Consultez notre documentation sur le service Power BI Embedded pour en savoir plus sur son utilisation.

14. **J’ai un abonnement Azure. Puis-je utiliser Power BI Embedded dans le cadre de cet abonnement ?**

    Oui. Vous pouvez utiliser votre abonnement Azure existant pour approvisionner et utiliser le service Microsoft Power BI Embedded.

15. **Les utilisateurs de mon application ont-ils besoin d’une licence Power BI ?**

    Non. Les utilisateurs finaux de votre application n’ont pas besoin d’acheter un abonnement Power BI spécifique pour afficher les visualisations de données incorporées à votre application. Dans le modèle Power BI Embedded, l’utilisation du service est facturée au fournisseur de l’application sur la base de l’indicateur de consommation Azure. Pour plus d’informations, consultez la [page sur les licences et la tarification](http://go.microsoft.com/fwlink/?LinkId=760527).

16. **Comment les utilisateurs sont-ils authentifiés par Power BI Embedded ?**

    Le service Power BI Embedded utilise des jetons d’application pour l’authentification et l’autorisation des utilisateurs finaux. Il n’utilise pas l’authentification explicite. Dans ce modèle de jetons d’application, votre application gère l’authentification et l’autorisation de vos utilisateurs finaux. Ensuite, quand cela est nécessaire, votre application crée et envoie les jetons d’application à notre service pour lui indiquer d’afficher le rendu du rapport demandé. Avec cette conception, votre application peut gérer l’authentification et l’autorisation des utilisateurs sans passer par Azure Active Directory. Cette option est toutefois possible. Pour plus d’informations sur les jetons d’application, consultez la page [Jetons d’application](https://azure.microsoft.com/fr-FR/documentation/articles/power-bi-embedded-get-started-sample/#key-flow) dans notre documentation.

17. **Quelles sont les sources de données actuellement prises en charge avec Power BI Embedded ?**

    Dans la version préliminaire publique du service, nous prenons en charge l’accès aux sources de données cloud qui utilisent des informations d’identification de base via une requête directe. Cela signifie que les sources telles que SQL DB, HDInsight Spark et Azure SQL DW sont prises en charge dès à présent. Nous allons étendre la prise en charge d’autres sources de données et types d’accès dans les prochains mois. Nous indiquerons les nouvelles sources de données prises en charge sur le site de développement Power BI à l’adresse [http://dev.powerbi.com](http://dev.powerbi.com/).

18. **Comment le modèle d’architecture cliente fonctionne-t-il pour Power BI Embedded ?**

    Le modèle Power BI Embedded n’impose pas explicitement que vos utilisateurs soient dans des clients Azure Active Directory (Azure AD). Vous avez le choix d’exiger ou non Azure AD pour vos clients. Vous devez donc déterminer le modèle d’architecture cliente nécessaire pour Power BI Embedded par rapport à l’architecture de votre application et de votre infrastructure.

    Les développeurs ou employés qui conçoivent ou utilisent votre application doivent avoir un compte d’utilisateur Azure AD pour pouvoir gérer votre abonnement Azure et les collections d’espaces de travail via le portail Azure. Les API de programmation utilisées par les développeurs pour importer des rapports, modifier des chaînes de connexion et obtenir des URL incorporées se servent de jetons d’application pour l’authentification. Elles ne nécessitent donc pas Azure AD. Vous trouverez plus de détails sur l’utilisation de nos API et du portail Azure sur la [page de documentation de Power BI Embedded sur Azure.com](https://azure.microsoft.com/fr-FR/documentation/services/power-bi-embedded/).

19. **Où en savoir plus ?**

    Vous pouvez consulter la [page de documentation de Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760526). Vous pouvez aussi rester informé des dernières actualités concernant ce service en consultant le [blog des développeurs Power BI](http://blogs.msdn.com/powerbidev) ou le centre de développement Power BI à l’adresse dev.powerbi.com. Vous pouvez également poser vos questions sur le site [Stackoverflow](http://stackoverflow.com/questions/tagged/powerbi).

20. **Comment démarrer ?**

    Vous pouvez démarrer gratuitement dès maintenant ! Si vous avez un abonnement Azure, vous pouvez approvisionner Power BI Embedded directement à partir du portail Azure. Si vous n’en avez pas, vous pouvez créer votre [compte Azure gratuit](https://azure.microsoft.com/free/). Une fois que vous avez approvisionné le service Power BI Embedded, vous pouvez facilement utiliser directement les API REST de Power BI ou utiliser le SDK disponible sur [GitHub](http://go.microsoft.com/fwlink/?LinkID=746472). Plusieurs exemples sont fournis pour vous aider à utiliser le Kit SDK.

## Voir aussi

- [Présentation de Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Prise en main de la version préliminaire de Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0629_2016-->