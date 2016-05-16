<properties
   pageTitle="Vue d’ensemble : API Reconnaissance de l’orateur de Cognitive Services | Microsoft Azure"
   description="Découvrez comment la reconnaissance de l’orateur peut améliorer les applications. Les API Reconnaissance de l’orateur ont des algorithmes avancés pour la vérification et l’identification de l’orateur."
   services="cognitive-services"
   documentationCenter="na"
   authors="cjgronlund"
   manager="paulettem"
   editor="cjgronlund"/>

<tags
   ms.service="cognitive-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/02/2016"
   ms.author="cgronlun"/>

# Vue d’ensemble : API Reconnaissance de l’orateur d’Azure Cognitive Services

Découvrez comment les capacités de reconnaissance de l’orateur peuvent améliorer les applications. Les API Reconnaissance de l’orateur sont des API basées sur le cloud qui fournissent les algorithmes les plus avancés de vérification de l’orateur et d’identification de l’orateur. La reconnaissance de l’orateur peut être divisée en deux catégories :

  * Vérification de l’orateur
  * Identification de l’orateur

## Vérification de l’orateur

La voix possède des caractéristiques uniques qui peuvent être utilisées pour identifier une personne, comme une empreinte digitale. L’utilisation de la voix comme signal pour les scénarios d’authentification et de contrôle d’accès se présente comme un nouvel outil innovant. Elle offre essentiellement un niveau supérieur en matière de sécurité qui simplifie l’expérience d’authentification pour les clients.

Les API de vérification de l’orateur peuvent automatiquement vérifier et authentifier les utilisateurs à l’aide de leur voix ou de leur manière de parler.

### Inscription à la vérification de l’orateur

L’inscription à la vérification de l’orateur est basée sur le texte, ce qui signifie que les orateurs doivent choisir une phrase spécifique à utiliser lors des phases d’inscription et de vérification.

Lors de l’inscription, la voix de l’orateur est enregistrée lorsqu’il récite une phrase spécifique, puis un certain nombre de caractéristiques est extrait et la phrase choisie est reconnue. Ensemble, les caractéristiques extraites et la phrase choisie constituent une signature vocale unique.

### Vérification de la voix et de la phrase d’entrée

Lors de la vérification, une voix et une phrase d’entrée sont comparées à la signature vocale et à la phrase utilisées lors de l’inscription. Ceci permet de vérifier si les deux proviennent ou non de la même personne, et si elles récitent la phrase correcte.

Pour plus d’informations sur la vérification de l’orateur, consultez [Partie vérification de la documentation sur l’API Reconnaissance de l’orateur](https://dev.projectoxford.ai/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## Identification de l’orateur

Les API Identification de l’orateur peuvent identifier automatiquement la personne qui parle dans un fichier audio, en fonction d’un groupe d’orateurs potentiels. L’audio d’entrée est comparé au groupe d’orateurs donné. Et, en cas de correspondance, l’identité de l’orateur est renvoyée.

Tous les orateurs doivent d’abord passer par un processus d’inscription pour que leur voix soit enregistrée dans le système et qu’une empreinte vocale soit créée.

### Inscription à l’identification de l’orateur

L’inscription à l’identification de l’orateur est basée sur le texte, ce qui signifie qu’il n’existe aucune restriction sur le contenu récité par l’orateur dans le fichier audio. La voix de l’orateur est enregistrée et un certain nombre de caractéristiques est extrait pour former une signature vocale unique.

### Reconnaissance de l’orateur : mise en correspondance avec la voix d’entrée

Le contenu audio de l’orateur inconnu, conjointement avec le groupe d’orateurs potentiels, est fourni lors de la reconnaissance. La voix d’entrée est comparée à tous les orateurs afin de déterminer à qui appartient la voix et, en cas de correspondance, l’identité de l’orateur est retournée.

Pour plus d’informations sur l’identification de l’orateur, consultez [Partie identification de la documentation sur l’API Reconnaissance de l’orateur](https://dev.projectoxford.ai/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).

<!---HONumber=AcomDC_0504_2016-->