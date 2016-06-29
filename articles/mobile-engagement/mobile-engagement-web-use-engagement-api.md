<properties
	pageTitle="API du SDK web Azure Mobile Engagement | Microsoft Azure"
	description="Dernières mises à jour et procédures du Kit de développement logiciel (SDK) web pour Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="piyushjo" />

# Utiliser l’API Engagement dans une application web

Ce document est un complément de la procédure [Intégration d’Engagement à une application web](mobile-engagement-web-integrate-engagement.md). Il fournit des informations détaillées sur l'utilisation de l'API Engagement pour signaler les statistiques de votre application.

L’API Engagement est fournie par l’objet `engagement.agent`. `engagement` est l’alias du Kit de développement logiciel (SDK) Engagement par défaut. Il peut être redéfini à partir de la configuration du SDK.

## Concepts liés à Engagement

Les sections qui suivent affinent les [concepts Mobile Engagement](mobile-engagement-concepts.md) courants pour la plateforme web.

### `Session` et `Activity`

Si l'utilisateur reste inactif plus de quelques secondes entre deux *activités*, sa séquence d'*activités* est fractionnée en deux *sessions* distinctes. Ces quelques secondes constituent le *délai d’expiration de session*.

Si votre application web ne déclare pas la fin des activités de l’utilisateur par elle-même (en appelant la fonction `engagement.agent.endActivity`), le serveur Engagement terminera automatiquement la session de l’utilisateur dans les trois minutes qui suivront la fermeture de la page d’application. Ce comportement est appelé *délai d’expiration de session* du serveur.

### `Crash`

Il n’existe pas de rapport automatisé d’exceptions JavaScript non interceptées. Toutefois, vous pouvez signaler les incidents manuellement à l’aide de la fonction `sendCrash` (voir ci-dessous).

## Rapports d'activités

### L'utilisateur démarre une nouvelle activité

	engagement.agent.startActivity("MyUserActivity");

Vous devez appeler `startActivity()` chaque fois que l'activité utilisateur change. Le premier appel à cette fonction démarre une nouvelle session utilisateur.

### L'utilisateur met fin à l'activité en cours

	engagement.agent.endActivity();

Vous devez appeler `endActivity()` au moins une fois quand l'utilisateur termine sa dernière activité. Cela indique au SDK Engagement que l'utilisateur est inactif et que la session utilisateur doit être fermée à la fin du délai d'expiration de session (si vous appelez `startActivity()` avant l'expiration de la session, la session est simplement reprise).

Il est souvent difficile, voire impossible, d’intercepter la fin des activités de l’utilisateur dans des environnements web (aucun appel fiable lors de la fermeture de la fenêtre du navigateur). C’est pourquoi le serveur Engagement termine automatiquement la session de l’utilisateur dans un délai de 3 minutes après la fermeture de la page de l’application.

## Rapports d'événements

### Événements de session

Les événements de session servent généralement à signaler les actions effectuées par un utilisateur lors de sa session.

**Exemple sans données supplémentaires :**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login');
	  // [...]
	}

**Exemple avec des données supplémentaires :**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login', {user: 'alice'});
	  // [...]
	}

### Événements autonomes

Contrairement aux événements de session, les événements autonomes peuvent se produire en dehors du contexte d'une session.

Pour cela, utilisez ``engagement.agent.sendEvent`` au lieu de ``engagement.agent.sendSessionEvent``.

## Rapports d'erreurs

### Erreurs de session

Les erreurs de session servent généralement à signaler les erreurs affectant l'utilisateur lors de sa session.

**Exemple sans données supplémentaires :**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short');
	  }
	  // [...]
	}

**Exemple avec des données supplémentaires :**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short', {length: 4});
	  }
	  // [...]
	}

### Erreurs autonomes

Contrairement aux erreurs de session, les erreurs autonomes peuvent se produire en dehors du contexte d'une session.

Pour cela, utilisez `engagement.agent.sendError` au lieu de `engagement.agent.sendSessionError`.

## Rapports de travaux

### Exemple

Supposons que vous souhaitiez analyser une requête Ajax :
			
	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	  // [...]
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### Signaler les erreurs lors d’un travail

Les erreurs peuvent être associées à un travail en cours d'exécution plutôt qu'à la session utilisateur en cours.

**Exemple :**

Supposons que vous vouliez signaler une erreur si une requête Ajax échoue :

	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	    // [...]
	    if (xhr.status == 0 || xhr.status >= 400) {
	      engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
	    }
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### Rapports d'événements pendant une tâche

Les événements peuvent être associés à un travail en cours d’exécution au lieu de se rapporter à la session utilisateur en cours grâce à la fonction `engagement.agent.sendJobEvent`.

Cette fonction fonctionne exactement comme `engagement.agent.sendJobError`.

### Rapports d'incidents

La fonction `sendCrash` est utilisée pour signaler des incidents manuellement.

L’argument `crashid` est une chaîne utilisée pour identifier le type de l’incident. L’argument `crash` correspond généralement à l’arborescence des appels de procédure de l’incident sous forme de chaîne.

	engagement.agent.sendCrash(crashid, crash);

## Paramètres supplémentaires

Des données arbitraires peuvent être associées à un événement, à une erreur, à une activité ou à un travail.

Ces données peuvent être n’importe quel objet JSON (et non un tableau ou des types primitifs).

**Exemple**

	var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
	engagement.agent.sendEvent("video_clicked", extras);

### Limites

#### de clés symétriques

Chaque clé de l'objet doit correspondre à l'expression régulière suivante :

	^[a-zA-Z][a-zA-Z_0-9]*

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### Valeurs

Les valeurs sont limitées aux types chaîne, nombre et booléen.

#### Taille

Les données supplémentaires sont limitées à **1024** caractères par appel (une fois codées au format JSON par le SDK).

## Rapports d'informations sur l'application

Vous pouvez signaler manuellement les informations de suivi (ou toutes autres informations spécifiques aux applications) à l'aide de la fonction `sendAppInfo()`.

Notez que ces informations peuvent être envoyées de façon incrémentielle : seule la dernière valeur d'une clé donnée sera conservée pour un périphérique donné.

À l’instar des paramètres supplémentaires des événements, un objet JSON peut être utilisé pour récupérer les informations de l’application. Notez que les tableaux ou les sous-objets seront considérés comme des chaînes plates (à l’aide de la sérialisation JSON).

### Exemple

Voici un exemple de code pour envoyer des informations sur la date de naissance et le sexe de l'utilisateur :

	var appInfos = {"birthdate":"1983-12-07","gender":"female"};
	engagement.agent.sendAppInfo(appInfos);

### Limites

#### de clés symétriques

Chaque clé de l'objet doit correspondre à l'expression régulière suivante :

	^[a-zA-Z][a-zA-Z_0-9]*

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### Taille

Les informations sur l’application sont limitées à **1024** caractères par appel (une fois codées au format JSON par le SDK).

Dans l'exemple précédent, le JSON envoyé au serveur fait 44 caractères :

	{"birthdate":"1983-12-07","gender":"female"}
 

<!---HONumber=AcomDC_0615_2016-->