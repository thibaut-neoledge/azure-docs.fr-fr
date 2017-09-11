---
title: "Outil Microsoft de modélisation des menaces - Azure | Microsoft Docs"
description: "En savoir plus sur les fonctionnalités disponibles dans l’Outil de modélisation des menaces"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 621ff305d7e782f85eeaae6c3fb02031673549c6
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

---

# <a name="threat-modeling-tool-feature-overview"></a>Vue d’ensemble de la fonctionnalité Outil de modélisation des menaces

Nous sommes heureux que vous ayez choisi d’utiliser l’Outil de modélisation des menaces pour vos besoins de modélisation des menaces ! Dans le cas contraire, visitez **[Bien démarrer avec l’Outil de modélisation des menaces](./azure-security-threat-modeling-tool-getting-started.md)** pour apprendre les notions de base.

> Notre outil est souvent mis à jour, reportez-vous régulièrement à ce guide pour connaitre nos dernières fonctionnalités et améliorations.

Cliquez sur le bouton « Créer un nouveau modèle » pour ouvrir une page de démarrage vierge, comme dans l’image ci-dessous :

![Page de démarrage vierge](./media/azure-security-threat-modeling-tool/tmtstart.png)

À l’aide du modèle des menaces créé par notre équipe dans l’exemple **[Bien démarrer avec](./azure-security-threat-modeling-tool-getting-started.md)**, examinons toutes les fonctionnalités disponibles dans l’outil aujourd'hui.

![Modèle de menaces de base](./media/azure-security-threat-modeling-tool/basictmt.png)

## <a name="navigation"></a>Navigation

Avant de plonger dans les fonctionnalités intégrées, attardons-nous sur les principaux composants de l’outil

### <a name="menu-items"></a>Éléments de menu

L’expérience est similaire à d’autres produits Microsoft. Nous allons commencer en passant en revue les éléments de menu de niveau supérieur :

![Éléments de menu](./media/azure-security-threat-modeling-tool/menuitems.png)

| Étiquette                               | Détails      |
| --------------------------------------- | ------------ |
| **File** | <ul><li>Ouvrir, enregistrer et fermer des fichiers</li><li>Connexion et déconnexion aux comptes OneDrive</li><li>Partager des liens (Afficher + Modifier)</li><li>Afficher les informations sur le fichier</li><li>Appliquer le nouveau modèle à des modèles existants</li></ul> |
| **Modifier** | Annuler/rétablir des actions, ainsi que copier, coller et supprimer |
| **Afficher** | <ul><li>Basculer entre les vues **Analyse** et **Conception**</li><li>Ouvrir des fenêtres fermées (par ex. gabarits, propriétés des éléments et messages)</li><li>Rétablir la disposition des paramètres par défaut</li></ul> |
| **Diagramme** | Ajouter/supprimer des diagrammes et naviguer dans les « onglets » des diagrammes |
| **Rapports** | Créer des rapports HTML à partager avec d’autres |
| **Aide** | Vous guide pour vous aider à utiliser l’outil |

Les icônes sont des raccourcis pour les menus de niveau supérieur :

| Icône                               | Détails      |
| --------------------------------------- | ------------ |
| **Ouvrir** | Ouvre un nouveau fichier |
| **Save** | Enregistre le fichier en cours |
| **Conception** | Accède au Mode création, où vous avez créé des modèles |
| **Analyser** | Indique les menaces générées et leurs propriétés |
| **Ajouter un diagramme** | Ajoute le nouveau diagramme (semblable aux nouveaux onglets dans Excel) |
| **Supprimer un diagramme** | Supprime le diagramme actuel |
| **Copier/Couper/Coller** | Copie, coupe, colle des éléments |
| **Annuler/Rétablir** | Annule/rétablit des actions |
| **Zoom avant / Zoom arrière** | Effectue un zoom avant et arrière dans le diagramme pour une meilleure vue |
| **Commentaires** | Ouvre le Forum MSDN |

### <a name="canvas"></a>Canevas

L’espace dans lequel vous faites glisser et déposez les éléments. Glisser et déplacer est le moyen le plus rapide et le plus efficace pour générer des modèles. Vous pouvez également cliquer avec le bouton droit et sélectionner dans le menu, ce qui ajoute des versions génériques des éléments que vous utilisez, comme indiqué ci-dessous.

#### <a name="dropping-the-stencil-on-the-canvas"></a>Déposer le gabarit sur le canevas

![Dépôt du canevas](./media/azure-security-threat-modeling-tool/canvasdrop1.png)

#### <a name="clicking-on-the-stencil"></a>Cliquer sur le gabarit

![Propriétés de l’élément](./media/azure-security-threat-modeling-tool/canvasdrop2.png)

### <a name="stencils"></a>Gabarits

L’endroit où vous pouvez rechercher tous les gabarits disponibles à utiliser selon le modèle sélectionné. Si vous ne trouvez pas les éléments appropriés, essayez d’utiliser un autre modèle ou modifiez-en un pour répondre à vos besoins. En règle générale, vous devez être en mesure de trouver une combinaison de catégories, telles que celles ci-dessous :

| Nom du gabarit                               | Détails      |
| --------------------------------------- | ------------ |
| **Processus** | Applications, plug-ins de navigateur, menaces, machines virtuelles |
| **Interacteur externe** | Fournisseurs d’authentification, navigateurs, utilisateurs, applications Web |
| **Banque de données** | Cache, stockage, fichiers de configuration, bases de données, registre |
| **Flux de données** | Binaire, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, canal nommé, DCOM/RPC, SMB, UDP |
| **Limites de bordure/ligne d’approbation** | Réseaux d’entreprise, Internet, machine, Sandbox, mode utilisateur/noyau |

### <a name="notesmessages"></a>Remarques/messages

| Composant                               | Détails      |
| --------------------------------------- | ------------ |
| **Messages** | Logique d’outil interne qui avertit les utilisateurs chaque fois qu’il existe une erreur, telle qu’aucun flux de données entre des éléments |
| **Remarques** | Remarques manuelles ajoutées au fichier par l’équipe des ingénieurs durant le processus de conception et de révision |

### <a name="element-properties"></a>Propriétés de l’élément

Ceux-ci varient selon les éléments sélectionnés. En dehors des limites d’approbation, tous les autres éléments contiennent 3 sélections générales :

| Propriété d’élément                               | Détails      |
| --------------------------------------- | ------------ |
| **Name** | Utile pour nommer vos processus, magasins, interacteurs et flux pour être facilement reconnu |
| **Hors de portée** | Si sélectionné, l’élément est extrait de la matrice de génération des menaces (non recommandé) |
| **Raison de l’hors de portée** | Champ de justification pour informer les utilisateurs de la raison pour laquelle l’option Hors de portée a été sélectionnée |

Les propriétés sont modifiées pour chaque catégorie d’élément. Cliquez sur chaque élément pour vérifier les options disponibles ou ouvrez le modèle pour en savoir plus. Passons maintenant aux fonctionnalités.

## <a name="welcome-screen"></a>Écran d’accueil

L’écran d’accueil est la première chose que vous voyez lorsque vous ouvrez l’application.

### <a name="open-a-model"></a>Ouvrir un modèle

Passez la souris sur le bouton « Ouvrir un modèle » pour afficher 2 options masquées : « Ouvrir depuis cet ordinateur » et « Ouvrir depuis OneDrive. » La première ouvre l’écran Fichier ouvert, tandis que la deuxième passe en revue le processus de connexion pour OneDrive, ce qui vous permet de choisir des dossiers et des fichiers après une authentification réussie.

![Ouvrir un modèle](./media/azure-security-threat-modeling-tool/openmodel.png)

![Ouvrir depuis l’ordinateur ou OneDrive](./media/azure-security-threat-modeling-tool/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Commentaires, suggestions et problèmes

Cette option vous conduira aux Forums MSDN pour les outils SDL. Il s’agit d’un excellent moyen pour extraire d’autres avis des utilisateurs sur l’outil, y compris les solutions de contournement et de nouvelles idées.

![Commentaires](./media/azure-security-threat-modeling-tool/feedback.png)

## <a name="design-view"></a>Mode création

Chaque fois que vous ouvrez ou créez un nouveau modèle, vous êtes dirigé vers le Mode création.

### <a name="adding-elements"></a>Ajout d’éléments

Il existe 2 méthodes pour ajouter des éléments dans la grille :

- **Glisser et déposer** : faites glisser l’élément souhaité dans la grille, puis utilisez les propriétés de l’élément pour fournir des informations supplémentaires.
- **Cliquer avec le bouton droit** : cliquez avec le bouton droit n’importe où sur la grille et sélectionnez dans le menu déroulant. Une représentation générique de cet élément s’affiche sur l’écran.

### <a name="connecting-elements"></a>Connexion des éléments

Il existe 2 façons de connecter des éléments dans l’outil :

- **Glisser et déposer** : faites glisser le flux de données de votre choix vers la grille et connectez les deux extrémités aux éléments appropriés.
- **Cliquer + Maj** : cliquez sur le premier élément (envoi de données), appuyez et maintenez la pression sur la touche Maj, puis sélectionnez le deuxième élément (réception de données). Cliquez avec le bouton droit, puis sélectionnez « Connecter ». Si vous utilisez un flux de données bidirectionnel, l’ordre n’est pas aussi important.

### <a name="properties"></a>Propriétés

Affiche toutes les propriétés qui peuvent être modifiées sur les gabarits placés dans le diagramme. Pour afficher les propriétés, cliquez simplement sur le gabarit et les informations seront alimentées en conséquence. L’exemple ci-dessous présente un gabarit de « base de données » avant et après être glissé vers le diagramme :

#### <a name="before"></a>Avant

![Avant](./media/azure-security-threat-modeling-tool/properties1.png)

#### <a name="after"></a>Après

![Après](./media/azure-security-threat-modeling-tool/properties2.png)

### <a name="messages"></a>Messages

Si vous créez un modèle de menaces et oubliez de connecter les flux de données aux éléments, la fenêtre de messages vous invite à agir. Vous pouvez choisir de l’ignorer ou suivre les instructions pour résoudre le problème. 

![Messages](./media/azure-security-threat-modeling-tool/messages.png)

### <a name="notes"></a>Remarques

La commutation des onglets de Messages à Remarques vous permet d’ajouter des remarques à votre diagramme afin de capturer toutes vos idées

## <a name="analysis-view"></a>Vue d’analyse

Une fois que vous avez terminé la création de votre diagramme, basculez vers la vue d’analyse en accédant aux sélections de menu supérieur et en sélectionnant la loupe à côté de la palette de peinture.

![Vue d’analyse](./media/azure-security-threat-modeling-tool/analysisview.png)

### <a name="generated-threat-selection"></a>Sélection de menaces générées

Lorsque vous cliquez sur une menace, vous pouvez utiliser trois fonctions uniques :

| Fonctionnalité                               | info      |
| --------------------------------------- | ------------ |
| **Indicateur de lecture** | <p>La menace est désormais marquée comme lue, ce qui peut facilement vous aider à effectuer le suivi des éléments que vous avez déjà suivis</p><p>![Indicateur lu/non lu](./media/azure-security-threat-modeling-tool/readmode.png)</p> |
| **Focus d’interaction** | <p>Interaction dans le diagramme qui appartient à cette menace est mise en surbrillance</p><p>![Focus d’interaction](./media/azure-security-threat-modeling-tool/interactionfocus.png)</p> |
| **Propriétés de la menace** | <p>Les informations supplémentaires sur la menace s’affichent dans la fenêtre Propriétés de la menace</p><p>![Propriétés de la menace](./media/azure-security-threat-modeling-tool/threatproperties.png)</p> |

### <a name="priority-change"></a>Modification de priorité

La modification du niveau de priorité pour chaque menace générée change également leurs couleurs pour permettre une identification des menaces à priorité haute, moyenne et basse.

![Modification de priorité](./media/azure-security-threat-modeling-tool/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Champs modifiables de propriétés de menaces

Comme indiqué dans l’image ci-dessus, les utilisateurs peuvent modifier les informations générées par l’outil et également ajouter des informations pour certains champs, tels que la justification. Ces champs sont générés par le modèle, donc, si vous avez besoin de plus d’informations pour chaque menace, vous êtes invités à apporter des modifications.

![Propriétés de la menace](./media/azure-security-threat-modeling-tool/threatproperties.png)

## <a name="reports"></a>Rapports

Une fois que vous avez terminé la modification des priorités et la mise à jour de l’état de chaque menace générée, vous pouvez enregistrer le fichier et/ou imprimer un rapport en accédant à « Rapport », puis à « Créer un rapport complet ». Vous êtes invité à nommer le rapport, et une fois cela fait, vous devez voir une image similaire à celle ci-dessous :

![Rapport](./media/azure-security-threat-modeling-tool/report.png)

## <a name="next-steps"></a>Étapes suivantes

Pour contribuer au modèle pour la communauté, accédez à notre page **[GitHub](https://github.com/Microsoft/threat-modeling-templates)**. **[Télécharger](https://aka.ms/tmtpreview)** l’outil pour bien démarrer dès aujourd'hui.

