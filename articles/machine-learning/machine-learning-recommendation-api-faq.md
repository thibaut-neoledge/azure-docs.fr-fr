<properties 
	pageTitle="Configuration et utilisation de l'API Machine Learning Recommendations | Microsoft Azure" 
	description="API Microsoft RECOMMANDATIONS créée avec le FAQ sur Azure Machine Learning" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="luisca"/>

#Forum aux questions relatif à la configuration et à l’utilisation de l’API de Machine Learning Recommendations


**Qu’est-ce que RECOMMENDATIONS ?**

Conçu pour les organisations et les entreprises qui se basent sur les recommandations pour leurs ventes croisées et incitatives, le programme RECOMMENDATIONS d’Azure Machine Learning est un moteur de recommandations en libre-service. C’est une implémentation du filtrage collaboratif qui utilise la factorisation de matrice comme algorithme de base. Les développeurs d’applications peuvent accéder à RECOMMENDATIONS à l’aide d’API REST.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Que puis-je faire avec RECOMMENDATIONS ?**

RECOMMENDATIONS utilise comme données d'entrée un élément ou un ensemble d'éléments et renvoie une liste de recommandations pertinentes. Par exemple : un client d’un détaillant en ligne clique sur un produit. Le détaillant en ligne envoie ce produit comme entrée à RECOMMENDATIONS, obtient une liste de produits et décide quels produits parmi ceux de la liste seront affichés au client. Vous pouvez également utiliser RECOMMENDATIONS pour optimiser votre boutique en ligne ou même pour informer votre service commercial interne ou votre centre d'appel.

**Existe-t-il des restrictions d’utilisation ?**

Recommandations présente les limitations d’utilisation suivantes : * Nombre maximal de modèles par abonnement : 10 * Nombre maximal d’éléments qu’un catalogue peut contenir : 100 000 * Le nombre maximal de points d’utilisation conservés est d’environ 5 000 000. Les plus anciens seront supprimés si de nouveaux sont téléchargés ou signalés. * La taille maximale des données qui peuvent être envoyées par courrier électronique (par exemple, importation des données de catalogue, importation des données d’utilisation) est de 200 Mo * Le nombre de transactions par seconde (TPS) pour une génération de modèle de recommandation créé mais non activé est d’environ 2 TPS. Une génération de modèle de recommandation active peut contenir jusqu’à 20 TPS.

##Achat et facturation 


**Combien coûte Recommendations pendant la période de lancement ?**

Recommendations est un service par abonnement. La facturation est effectuée en fonction du volume de transactions par mois. Vous pouvez vous rendre à la [page des offres](https://datamarket.azure.com/dataset/amla/recommendations) dans Microsoft Azure Marketplace pour accéder aux informations relatives à la tarification.

**Des coûts liés au suivi et au stockage de l’activité des utilisateurs de Recommendations me seront-ils facturés ?**

Pas pour le moment.

**Existe-t-il une version d’évaluation gratuite de Recommendations ?**

Il existe une version d’évaluation qui est limitée à 10 000 transactions par mois.

**Quand serai-je facturé pour l’utilisation de Recommendations ?**

Un abonnement payant est un abonnement pour lequel il existe des frais mensuels. Lorsque vous achetez un abonnement payant, le premier mois vous est immédiatement facturé. Le montant qui vous est facturé est celui qui correspond à l’offre sur la page d’abonnement (plus les taxes applicables). Ce prix mensuel est facturé chaque mois à la même date jusqu'à l'annulation de votre abonnement.

**Comment mettre à niveau mon abonnement vers une catégorie de services supérieure ?**

Vous pouvez acheter ou mettre à niveau votre abonnement à partir de la [page des offres](https://datamarket.azure.com/dataset/amla/recommendations) sur Microsoft Azure Marketplace.

Lorsque vous mettez à niveau un abonnement :

* Les transactions qui demeurent sur votre ancien abonnement ne sont pas reportées sur votre nouvel abonnement. 
* Vous payez le plein prix du nouvel abonnement, même s'il reste des transactions inutilisées sur votre ancien abonnement.

Procédure de mise à niveau d’un abonnement :

* Accédez à la [page des offres](https://datamarket.azure.com/dataset/amla/recommendations).
* Connectez-vous sur Marketplace si ce n’est pas déjà le cas.
* Tous les plans disponibles sont répertoriés dans le volet de droite. Cliquez sur le bouton radio pour sélectionner l’abonnement vers lequel vous souhaitez mettre à niveau.
* Si vous souhaitez mettre à niveau, cliquez sur **OK**. Si vous ne souhaitez pas mettre à niveau, cliquez sur **Annuler**.

**Important :** lisez attentivement la boîte de dialogue avant de mettre à niveau, car votre action aura des conséquences sur la facturation et l'utilisation du service.

**Quand mon abonnement à Recommendations prendra-t-il fin ?**

Votre abonnement prendra fin lorsque vous l'annulerez. Si vous souhaitez annuler vos abonnements, consultez les instructions suivantes.

**Comment annuler mon abonnement à Recommendations ?**

Pour annuler votre abonnement, procédez comme suit. Si votre abonnement actuel est un abonnement payant, il se poursuit jusqu'à la fin de la période de facturation actuelle. Si vous souhaitez que l’annulation soit immédiatement effective, contactez-nous sur la page [Aide et support Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

**Remarque** : aucun remboursement ne sera accordé si vous annulez l’abonnement avant la fin d’une période de facturation ou pour les transactions non utilisées d’une période de facturation.

* Accédez à la [page des offres](https://datamarket.azure.com/dataset/amla/recommendations).
* Connectez-vous sur Marketplace si ce n’est pas déjà le cas.
* Cliquez sur **Annuler** à droite du nom et de l’état du jeu de données. Vous pouvez utiliser cet abonnement jusqu’à la fin de la période de facturation actuelle ou jusqu’à ce que votre limite de transaction soit atteinte (selon ce qui surviendra en premier).

Si vous souhaitez annuler votre abonnement immédiatement afin d’en acheter un nouveau, complétez une demande sur la page [Aide et support Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

##Prise en main de Recommendations

**Recommendations est-il adapté à ma situation ?**

Recommendations de Machine Learning est conçu pour les organisations et les entreprises qui s'appuient sur les recommandations pour les ventes croisées et incitatives. Si vous disposez d’un site Web à destination de vos clients, d’une équipe commerciale, d’une équipe commerciale interne ou d’un centre d’appels et si vous proposez un catalogue qui contient quelques dizaines de produits ou services, l’utilisation de Recommendations pourrait être un avantage pour vos résultats financiers.

Tester Recommendations est relativement simple. La version actuelle de l’API nécessite des compétences de programmation basiques. Si vous avez besoin d’aide, contactez le fournisseur qui a développé votre site Web. Si vous avez un service informatique interne ou un développeur interne, ils doivent être en mesure de faire fonctionner Recommendations.

**Quelles sont les conditions requises pour configurer Recommendations ?**

Recommendations requiert que vous disposiez d’un journal des choix de l’utilisateur, car il est lié à votre catalogue. Si vous ne possédez pas ce type de journal mais que vous disposez d’un site Web à destination des clients, Recommendations peut collecter l’activité des utilisateurs pour vous.

Recommendations nécessite également un catalogue de vos produits ou services. Si vous n’avez pas le catalogue, Recommendations peut utiliser les données d’utilisation clients réelles et créer un catalogue. Un catalogue « implicite » n'inclut pas les éléments qui n'ont pas été « signalés » comme faisant partie des transactions utilisateur.

**Comment configurer Recommendations pour la première fois ?**

Après vous être [abonné](https://datamarket.azure.com/dataset/amla/recommendations) à Recommandations, vous devez utiliser la documentation de l’API du [Guide de démarrage rapide Azure Machine Learning Recommendations](machine-learning-recommendation-api-quick-start-guide.md) pour configurer le service.

**Où puis-je trouver la documentation de l’API ?**

La documentation de l’API se trouve dans le [Guide de démarrage rapide Azure Machine Learning Recommendations](machine-learning-recommendation-api-quick-start-guide.md).

**Quelles sont les options pour télécharger le catalogue et les données d’utilisation de Recommendations ?**

Deux options s’offrent à vous pour télécharger vos données de catalogue et d’utilisation : soit vous exportez ces données à partir de votre système CRM ou d’autres journaux et les téléchargez dans Recommendations, soit vous ajoutez des balises à votre site Web qui feront le suivi des activités de l’utilisateur. Si vous choisissez cette dernière option, les données seront stockées dans Azure.

##Maintenance et assistance

**Quelle est la taille maximale de mon jeu de données ?**

Chaque jeu de données peut contenir jusqu'à 100 000 éléments de catalogue et jusqu'à 2 048 Mo de données d'utilisation. En outre, un abonnement peut contenir jusqu'à 10 jeux de données (modèles).

**Où puis-je obtenir une aide technique pour Recommendations ?**

Une assistance technique est disponible sur le site de [Support Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**Où puis-je trouver les conditions d’utilisation ?**

[Conditions de service de l’API de Microsoft Azure Machine Learning Recommendations.](https://datamarket.azure.com/dataset/amla/recommendations#terms)



 

<!---HONumber=AcomDC_1210_2015-->