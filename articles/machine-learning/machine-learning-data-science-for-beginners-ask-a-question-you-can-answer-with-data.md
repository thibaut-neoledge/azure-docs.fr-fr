<properties
   pageTitle="Poser une question à laquelle les données permettent de répondre | Microsoft Azure"
   description="Découvrez comment formuler une question de science des données dans la vidéo Science des données pour les débutants nº 3. Inclut une comparaison des questions de classification et de régression."
   keywords="choisir une question,questions de science des données, formuler une question,formuler des questions,questions de régression,questions de classification,question précise"
   services="machine-learning"
   documentationCenter="na"
   authors="brohrer-ms"
   manager="paulettm"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/28/2016"
   ms.author="cgronlun;brohrer;garye"/>

# Poser une question à laquelle les données permettent de répondre : vidéo Science des données pour les débutants nº 3 

Découvrez comment formuler une question de science des données dans la vidéo Science des données pour les débutants nº 3. Cette vidéo inclut une comparaison des questions faisant appel aux algorithmes de classification et de régression.

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## Transcription : Poser une question à laquelle les données permettent de répondre

Bienvenue dans la troisième vidéo de la série « Science des données pour les débutants ».

Dans celle-ci, nous allons vous donner des conseils pour formuler une question à laquelle les données permettent de répondre.

Pour tirer pleinement parti de cette vidéo, nous vous recommandons de regarder les deux premières vidéos de cette série, « Les 5 questions auxquelles la science des données peut répondre » et « Vos données sont-elles prêtes pour la science des données ? ».

## Posez une question précise

Comme nous l’avons vu, la science des données est le processus consistant à utiliser des noms (également appelés catégories ou étiquettes) et des nombres pour prédire la réponse à une question. Mais la question ne peut pas avoir n’importe quelle forme ; il doit s’agir d’une *question précise*.

Une question vague n’appelle pas une réponse par un nom ou un nombre. Une question précise si.

Imaginons que vous avez trouvé une lampe magique renfermant un génie qui répond correctement à toutes les questions que vous lui posez. Mais ce génie est espiègle, et il fait en sorte de rendre sa réponse la plus vague et confuse possible. Vous devez le coincer avec une question si bien formulée qu’il sera obligé de vous dire ce que vous voulez savoir.

Si vous lui posez une question vague comme « Que va-t-il arriver à mon action ? », le génie pourra très bien répondre « Son cours va changer ». C’est une réponse correcte, mais pas vraiment utile.

Mais si vous lui posez une question précise comme « Quel sera le cours de mon action la semaine prochaine ? », le génie n’aura pas d’autre choix que de vous donner une réponse spécifique et de prédire un cours.

## Exemples de votre réponse : données cibles

Une fois que vous avez formulé votre question, vérifiez si vous avez des exemples de la réponse dans vos données.

Si votre question est « Quel sera le cours de mon action la semaine prochaine ? », vous devez vous assurer que vos données incluent l’historique du cours de l’action.

Si votre question est « Quelle voiture de mon parc va tomber en panne en premier ? », vous devez vous assurer que vos données incluent des informations concernant les pannes précédentes.

![Données cibles : exemples de votre réponse. Formulez une question de science des données.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

Ces exemples de réponses sont appelés des cibles. Une cible correspond à ce que nous essayons de prédire sur les points de données futurs, qu’il s’agisse d’une catégorie ou d’un nombre.

Si vous n’avez pas de données cibles, vous devez en collecter. Sans cela, vous ne pourrez pas obtenir de réponse à votre question.

## Reformulez votre question

Il est parfois possible de reformuler la question pour obtenir une réponse plus utile.

La question « Ce point de données est-il le point A ou B ? » prédit la catégorie (ou nom ou étiquette) d’un élément. Pour y répondre, nous utilisons un *algorithme de classification*.

La question « Combien ? » prédit une quantité. Pour y répondre, nous utilisons un *algorithme de régression*.

Pour voir comment nous pouvons transformer ces questions, examinons la question suivante : « Quel article est le plus intéressant pour ce lecteur ? ». Cette question appelle une prédiction d’un seul choix parmi de nombreuses possibilités, en d’autres termes, « S’agit-il de A, B, C ou D ? », et utiliserait un algorithme de classification.

Mais il serait peut-être plus facile de répondre à cette question si elle était reformulée ainsi : « Quel intérêt présente chacun des articles de cette liste pour ce lecteur ? ». Vous pouvez maintenant attribuer un score à chaque article, et il sera ensuite facile d’identifier l’article dont le score est le plus élevé. Il s’agit là d’une reformulation d’une question de classification en question de régression (ou « Combien ? »).

![Reformulez votre question. Question de classification et question de régression.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

La manière dont vous posez une question est une indication de l’algorithme qui peut vous donner une réponse.

Vous constaterez que certaines familles d’algorithmes, telles que celles de notre exemple sur les articles, sont étroitement liées. Vous pouvez reformuler votre question de manière à utiliser l’algorithme qui vous donnera la réponse la plus utile.

Mais surtout, vous devez poser une question précise, à laquelle les données permettent de répondre. Sans oublier de vous assurer que vous disposez des données appropriées pour obtenir une réponse.

Vous connaissez maintenant certains principes de base à observer pour poser une question à laquelle les données permettent de répondre.

Nous vous invitons à consulter les autres vidéos de la série « Science des données pour les débutants » de Microsoft Azure Machine Learning.

## Autres vidéos de la série

La série *Science des données pour les débutants* offre une introduction rapide à la science des données en cinq petites vidéos.


  * Vidéo 1 : [Les 5 questions auxquelles la science des données répond](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md). **Disponible dès à présent.**
  * Vidéo 2 : [Vos données sont-elles prêtes pour la science des données ?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) **Disponible dès à présent.**
  * Vidéo 4 : [Prédire une réponse à l’aide d’un modèle simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md). **Disponible dès à présent.**
  * Vidéo 5 : [Copier le travail d’autres personnes pour des projets de science des données](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md). Disponible le 30 juin.

## Étapes suivantes

  * [Menez votre première expérience de science des données avec Azure Machine Learning](machine-learning-create-experiment.md)
  * [Consultez la présentation de l’apprentissage automatique sur Microsoft Azure](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0629_2016-->