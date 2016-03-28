<properties
	pageTitle="FAQ sur le concours Machine Learning | Microsoft Azure"
	description="Forum Aux Questions (FAQ) sur les concours Microsoft Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="haining;chlovel;garye"/>

# FAQ sur le concours Microsoft Azure Machine Learning

**Où puis-je poser des questions générales sur la science des données ?**

Veuillez utiliser notre [forum Microsoft Azure Machine Learning](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**Comment participer à un concours ?**

Créez un compte gratuit ou payant dans [Microsoft Azure Machine Learning](https://studio.azureml.net/?selectAccess=true&o=2%22%20\t%20%22_blank). Suivez le didacticiel du concours et regardez la courte vidéo.

**Dois-je être un scientifique de données pour participer ?**

Non. En fait, nous encourageons les passionnés de données, ceux qui s’intéressent aux données scientifiques et les autres scientifiques de données débutants à participer à notre concours. Nous avons conçu des documents de support pour permettre à tout le monde de participer.

**Combien de temps cela prendra-t-il ?**

Vous pouvez participer à notre concours et créer un modèle de données dans un délai de 10 à 15 minutes. L’amélioration de votre expérience prédictive peut prendre plus de 15 minutes, selon votre connaissance de Machine Learning.

**Comment mon score est-il calculé ? Est-il calculé différemment pendant le concours et une fois le concours terminé ? Que sont les scores publics et privés ?**

1.  Tout le jeu de données a été fractionné de façon aléatoire par stratification en données de formation (60 %) et données de test (les 40 % restants). Le fractionnement aléatoire est stratifié de façon géo + segment + sous-groupe, pour s’assurer que les distributions des étiquettes dans les données de formation et les données de test soient cohérentes dans chaque région.  

2.  Les données de formation ont été téléchargées et fournies dans le cadre de l’expérience de débutant (Starter Experiment) dans la configuration du module Reader.

3.  Les données de test ont été de nouveau divisées en données de test publiques et privées, à l’aide de la même stratification (60 % pour les tests publics et 40 % pour les tests privés).

4.  Les données de test publiques ont été utilisées pour l’évaluation initiale. Le résultat correspond au score public et c’est ce que vous avez obtenu dans votre historique d’envoi lorsque vous avez envoyé votre entrée. Cette opération a été effectuée à chaque fois que vous avez envoyé une entrée. Ce score public est utilisé pour vous positionner dans le classement public.

5.  Les données de test privées ont été utilisées pour la dernière évaluation après la fin du concours. Ceci est appelé le score privé.

6.  Pour chaque participant, 5 de vos entrées avec les scores publics les plus élevés ont été automatiquement sélectionnées pour calculer les scores privés. L’entrée avec le score privé le plus élevé a été sélectionnée pour entrer dans le dernier classement, ce qui finalement permis de départager les gagnants.

**Cela est-il payant ?**

Non, vous pouvez définir un espace de travail gratuit dans Microsoft Azure Machine Learning pour participer au concours. N’oubliez pas que les comptes invités ne peuvent pas enregistrer de données. Alors veillez à envoyer votre entrée avant l’expiration des 8 heures gratuites.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Comment passer à un abonnement payant ?**

Vous pouvez acheter ou mettre à niveau votre abonnement à partir de cette page sur Microsoft Azure Marketplace. Consultez [cette page](https://azure.microsoft.com/pricing/details/machine-learning/) et le Forum Aux Questions sur la tarification.

**À quoi le bouton Try Belize sert-il ?**

(Inclure les instructions du portail des services web et une capture d’écran avec les étapes pour revenir en arrière)

**Que se passe-t-il si je remporte un concours ?**

Microsoft vérifiera les résultats du classement privé, puis nous vous contacterons. Vérifiez que votre adresse de messagerie dans votre profil utilisateur est à jour.

**Comment obtenir les gains si je remporte un concours ?**

Si vous êtes le gagnant d’un concours, vous devrez signer une déclaration d’éligibilité, de licence et de non-droit. Ce formulaire réitère les règles du concours. Les gagnants devront remplir un formulaire fiscal W-9 des États-Unis ou un formulaire W-8BEN pour ceux qui ne sont pas des contribuables situés aux États-Unis.

<!---HONumber=AcomDC_0316_2016-->