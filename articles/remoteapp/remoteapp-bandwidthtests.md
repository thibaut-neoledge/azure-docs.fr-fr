<properties 
    pageTitle="Azure RemoteApp : test de votre bande passante réseau avec quelques scénarios courants | Microsoft Azure"
	description="Découvrez les scénarios d’utilisation courants qui peuvent vous aider à déterminer vos besoins en bande passante réseau pour Azure RemoteApp."
	services="remoteapp"
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/27/2016" 
    ms.author="elizapo" />
    
# Azure RemoteApp : test de l’utilisation de votre bande passante réseau avec quelques scénarios courants

Comme expliqué dans [Estimation de l’utilisation de la bande passante réseau Azure RemoteApp](remoteapp-bandwidth.md), la meilleure façon de comprendre l’impact d’Azure RemoteApp sur votre réseau est d’exécuter des tests d’utilisation. Exécutez ces tests pour une période donnée et mesurez la bande passante nécessaire pour chaque scénario. Si vous en avez la possibilité, vous pouvez également mesurer la perte de paquets réseau et l’instabilité du réseau pour comprendre les modèles de réseau qui seront créés dans votre environnement spécifique.

    
Lors de l’évaluation de l’utilisation de la bande passante, n’oubliez pas que l’utilisation varie entre les différents utilisateurs de votre entreprise. Par exemple, les lecteurs et rédacteurs de texte consomment généralement moins de bande passante que les utilisateurs qui utilisent la vidéo. Pour obtenir de meilleurs résultats, étudiez les besoins de vos utilisateurs et créez une combinaison des scénarios suivants qui représente au mieux les utilisateurs de votre entreprise. N’oubliez pas de [consulter les facteurs qui ont une influence sur l’utilisation de la bande passante et l’expérience des utilisateurs](remoteapp-bandwidthexperience.md) pour vous aider à identifier les tests idéaux.

Commencez par lire la description des tests, choisissez votre combinaison et exécutez-la. Vous pouvez utiliser le tableau suivant pour faciliter le suivi des performances.

>[AZURE.NOTE] Si vous ne pouvez pas faire vos propres tests de réseau ou si vous n’avez pas le temps de le faire, consultez nos [recommandations et estimations de base relatives à la bande passante réseau](remoteapp-bandwidthguidelines.md). Néanmoins, comme votre consommation peut varier, *exécutez* vos propres tests si vous en avez la possibilité.


## Tests de l’utilisation
Chaque test est exécuté pour une période différente et teste différentes fonctions/fonctionnalités qui consomment de la bande passante réseau. N’oubliez pas de choisir la combinaison de tests qui correspond le mieux aux utilisateurs de votre entreprise.
 
### Présentation PowerPoint complexe avec animations, exécutée pendant une durée allant de 900 et 1 000 secondes

Un utilisateur présente entre 45 et 50 diapositives haute fidélité en utilisant Microsoft Office PowerPoint en mode plein écran. Les diapositives doivent contenir des images, des transitions (avec des animations) et des arrière-plans avec dégradé de couleur typiques de votre entreprise. L’utilisateur doit passer au moins 20 secondes sur chaque diapositive.
    
Ce scénario crée un trafic immense lorsqu’une diapositive passe à la diapositive suivante de la présentation.
    
### Présentation PowerPoint simple exécutée pendant environ 620 secondes

Un utilisateur présente un simple fichier PowerPoint incluant environ 30 diapositives en utilisant Microsoft Office PowerPoint en mode plein écran. Les diapositives ont plus de texte que dans le scénario précédent et leurs arrière-plans et images (diagrammes noirs) sont plus simples.
    
### Internet Explorer exécuté pendant environ 250 secondes

Un utilisateur parcourt le web à l’aide d’Internet Explorer. Il consulte et fait défiler un mélange de texte, d’images et de schémas. Les pages web sont stockées sur le disque local du serveur hôte de session Bureau à distance en tant que fichier MHT. L’utilisateur fait défiler à l’aide des touches Pg préc, Pg suiv, Haut et Bas, avec des intervalles différents pour chaque type de défilement :
    
    - Down - 250 keystrokes very 500 ms
    - Page Up - 36 keystrokes every 1000 ms
    - Down - 75 keystrokes every 100 ms
    - Page Down - 20 keystrokes every 500 ms
    - Up - 120 keystrokes every 300 ms
    
### Simple document PDF exécuté pendant environ 610 secondes
Un utilisateur lit un document PDF dans lequel il effectue des recherches de différentes manières à l’aide d’Adobe Acrobat Reader. Le document doit être composé de tableaux, de graphiques simples et de plusieurs polices de texte. Le document est constitué de 35 à 40 pages. L’utilisateur fait défiler à deux vitesses différentes d’avant en arrière, à quatre différents niveaux de zoom (Ajuster à la page, Ajuster à la largeur, 100 % et un autre niveau de votre choix). Le zoom permet de s’assurer que le texte (la police) peut être restitué dans différentes tailles. Le défilement est effectué à l’aide des touches Pg préc, Pg suiv, Haut et Bas, avec des intervalles différents pour chaque défilement.

### Document PDF (mixte) exécuté pendant environ 320 secondes
Un utilisateur lit un document PDF dans lequel il effectue des recherches de différentes manières à l’aide d’Adobe Acrobat Reader. Le document se compose d’images de haute qualité (y compris des photographies), de tableaux, de graphiques simples et de plusieurs polices de texte. L’utilisateur fait défiler à deux vitesses différentes d’avant en arrière, à quatre différents niveaux de zoom (Ajuster à la page, Ajuster à la largeur, 100 % et un autre niveau de votre choix). Le zoom permet de s’assurer que le texte (la police) peut être restitué dans différentes tailles. Le défilement est effectué à l’aide des touches Pg préc, Pg suiv, Haut et Bas, avec des intervalles différents pour chaque défilement.

### Lecture d’une vidéo au format Flash pendant environ 180 secondes
Un utilisateur consulte une vidéo au format Adobe Flash incorporée dans une page web. La page web est stockée dans le disque dur local du serveur hôte de session Bureau à distance. La vidéo est lue dans Internet Explorer par un plug-in de lecteur incorporé.

Ce scénario présente des utilisateurs qui consultent le contenu multimédia de pages web. La plupart des données doivent être lues via VOBR.

### Saisie à distance sur Word pendant environ 1 800 secondes
Un utilisateur tape un document au moyen d’une session Bureau à distance. Les séquences de touches sont envoyées de la session Bureau à distance du client vers un document dans Microsoft Word exécuté à distance. La vitesse de frappe est de 1 caractère toutes les 250 ms (7 050 caractères au total).

Il s’agit d’un des scénarios les plus courants pour un travailleur du savoir. Ce scénario teste la réactivité d’un utilisateur entrant du contenu dans un traitement de texte moderne. Ce scénario est sensible aux changements même mineurs dans l’utilisation de la bande passante.

## Suivi des résultats des tests

Vous pouvez utiliser le tableau suivant pour évaluer les scénarios dans votre environnement. Les données ci-dessous sont fournies uniquement à titre d’illustration : elles peuvent être différentes de ce que vous observez.

Pour plus de simplicité, nous partons du principe que tous les scénarios sont testés dans l’environnement suivant : résolution d’écran de 1920 x 1080 pixels, transports TCP sur un réseau avec une latence (délai) inférieure à 200 ms et instabilité réseau de 120 ms à plus ou moins 1 %.

À propos du tableau :
- **Expérience moyenne** contient la bande passante du réseau, où la productivité des utilisateurs n’est pas affectée de manière significative, mais peut rencontrer des problèmes ponctuels de signal audio ou vidéo. Le système est en mesure de récupérer rapidement en tirant parti de la logique dynamique. Les estimations de la bande passante réseau tentent de garantir la qualité de l’expérience utilisateur.
 - **Problèmes notables (point d’arrêt)** contient la bande passante réseau, où les utilisateurs peuvent remarquer des problèmes importants dans leur expérience et où leur productivité est impactée pendant des délais mesurables. À ce stade, les algorithmes du Bureau à distance sont en difficulté et ne peuvent pas garantir la qualité de l’expérience utilisateur en raison d’une bande passante réseau insuffisante.
 - **Bande passante réseau recommandée** contient la bande passante recommandée pour une expérience utilisateur bonne ou excellente. Cette bande passante est généralement supérieure d’un cran à la valeur correspondante dans la colonne **Expérience moyenne**.
 - **Notes** inclut les observations et commentaires.
 
| Test | Expérience moyenne | Problèmes notables (point d’arrêt) | Bande passante réseau recommandée | Remarques |
|-----------------------|--------------------|---------------------------------|-------------------------------|--------------------------------------------------------------------|
| PowerPoint complexe avec animations | 10 Mo/s | 1 Mo/s | > 10 Mo/s, de préférence 100 Mo/s | À une vitesse de 1 Mo/s, de nombreuses animations sont perdues |
| Simple PowerPoint | 5 Mo/s | 256 Ko/s | 10 Mo/s | À une vitesse de 256 Ko/s, le chargement des diapositives rencontre un retard notable |
| Internet Explorer | 10 Mo/s | 1 Mo/s | > 10 Mo/s, de préférence 100 Mo/s | À une vitesse de 1 Mo/s, les vidéos web sont floues et hachées et le défilement rapide rencontre des problèmes |
| PowerPoint simple | 1 Mo/s | 256 Ko/s | 5 Mo/s | À une vitesse de 256 Ko/s, il faut du temps pour charger la page |
| PDF mixte | 1 Mo/s | 256 Ko/s | 5 Mo/s | À une vitesse de 256 Ko/s, le chargement de la page prend beaucoup de temps |
| Lecture de vidéo au format Flash | 10 Mo/s | 1 Mo/s | > 10 Mo/s, de préférence 100 Mo/s | À une vitesse de 1 Mo/s, la vidéo est granuleuse et certaines images sont supprimées |
| Saisie à distance sur Word | 256 Ko/s | 128 Ko/s | 1 Mo/s | À une vitesse de 256 Ko/s, l’utilisateur peut remarquer un délai entre les séquences de touches |

Pour évaluer la bande passante réseau par utilisateur, créez une combinaison des scénarios ci-dessus et la proportion correspondante de bande passante réseau requise. Choisissez le nombre le plus élevé requis pour vos scénarios. Étant donné que les utilisateurs n’utilisent presque jamais le système seuls, prévoyez certaines réserves pour les utilisateurs qui travaillent simultanément sur le même réseau.
     
## En savoir plus
- [Estimation de l’utilisation de la bande passante réseau Azure RemoteApp](remoteapp-bandwidth.md)

- [Azure RemoteApp : quelle est la corrélation entre la bande passante réseau et la qualité de l’expérience d’utilisation ?](remoteapp-bandwidthexperience.md)

- [Bande passante réseau Azure RemoteApp : instructions générales (si vous ne pouvez pas tester votre propre bande passante)](remoteapp-bandwidthguidelines.md)

<!---HONumber=AcomDC_0629_2016-->