<properties 
	pageTitle="Comment utiliser l'API Engagement sur Android" 
	description="Dernier SDK Android - Comment utiliser l'API Engagement sur Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />

#Comment utiliser l'API Engagement sur Android

Ce document est un module complémentaire de la procédure [Comment intégrer Engagement sur Android](../mobile-engagement-android-integrate-engagement/): il décrit en détail comment utiliser l'API Engagement pour créer des rapports sur les statistiques de votre application.

N'oubliez pas que si vous voulez qu'Engagement ne crée des rapports que sur les sessions, les activités, les blocages et les informations techniques de votre application, le plus simple est de faire hériter vos sous-classes `Activity` de la classe `EngagementActivity` correspondante.

Si vous voulez aller plus loin, par exemple, si vous avez besoin de créer des rapports sur les événements, les erreurs et les tâches spécifiques de l'application, ou si vous devez créer un rapport sur les activités de votre application d'une autre façon que celle utilisée dans les classes `EngagementActivity`, vous devez utiliser l'API Engagement.

L'API Engagement est fournie par la classe `EngagementAgent`. Une instance de cette classe peut être récupérée en appelant la méthode statique `EngagementAgent.getInstance(Context)` (notez que l'objet `EngagementAgent` retourné est un singleton).

##Concepts d'Engagement

Les sections suivantes décrivent les [concepts de Mobile Engagement](../mobile-engagement-concepts/), pour la plateforme Android.

### `Session` et `activité`

Si l'utilisateur reste inactif plus de quelques secondes entre deux *activités*, sa séquence d' *activités* est fractionnée en deux *sessions* distinctes. Ces quelques secondes constituent le " délai d'expiration de session ".

Une *activité* est généralement associée à un écran de l'application, c'est-à-dire que l' *activité* démarre quand l'écran s'affiche et qu'elle s'arrête quand l'écran est fermé : c'est le cas quand le SDK Engagement est intégré à l'aide des classes `EngagementActivity` classes.

Mais les *activités* peuvent également être contrôlées manuellement à l'aide de l'API Engagement. Cela permet de fractionner un écran donné en plusieurs sous-parties pour obtenir plus d'informations sur l'utilisation de l'écran (par exemple, pour connaître la fréquence et la durée d'utilisation des boîtes de dialogue dans cet écran).

##Rapports d'activités

> [AZURE.IMPORTANT] Vous n'êtes pas obligé de signaler les activités décrites dans cette section si vous utilisez la classe `EngagementActivity` et ses variantes, comme expliqué dans le document Comment intégrer Engagement sur Android.

### L'utilisateur démarre une nouvelle activité

			EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
			// Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Vous devez appeler `startActivity()` chaque fois que l'activité de l'utilisateur change. Le premier appel à cette fonction démarre une nouvelle session utilisateur.

Le meilleur moment pour appeler cette fonction est à chaque rappel `onResume` de l'activité.

### L'utilisateur met fin à l'activité en cours

			EngagementAgent.getInstance(this).endActivity();

Vous devez appeler `endActivity()` au moins une fois quand l'utilisateur termine sa dernière activité. Cela indique au SDK Engagement que l'utilisateur est inactif et que la session utilisateur doit être fermée à la fin du délai d'expiration de session (si vous appelez `startActivity()` avant l'expiration de la session, la session est simplement reprise).

Le meilleur moment pour appeler cette fonction est à chaque rappel `onPause` de l'activité.

##Rapports d'événements

### Événements de session

Les événements de session sont généralement utilisés pour signaler les actions effectuées par un utilisateur pendant sa session.

**Exemple sans données extras :**

			public MyActivity extends EngagementActivity {
			   [...]
			   @Override
			   public boolean onPrepareOptionsMenu(Menu menu) {
			      getEngagementAgent().sendSessionEvent("menu_shown", null);
			   }
			   [...]
			}

**Exemple avec données extras :**

			public MyActivity extends EngagementActivity {
			  [...]
			  @Override
			  public boolean onMenuItemSelected(int featureId, MenuItem item) {
			    Bundle extras = new Bundle();
			    extras.putInt("id", item.getItemId());
			    getEngagementAgent().sendSessionEvent("menu_selected", extras);
			  }
			  [...]
			}

### Événements autonomes

Contrairement aux événements de session, les événements autonomes peuvent se produire en dehors du contexte d'une session.

**Exemple :**

Supposons que vous voulez signaler les événements qui se produisent quand un récepteur de diffusion est déclenché :

			/** Triggered by Intent.ACTION_BATTERY_LOW */
			public BatteryLowReceiver extends BroadcastReceiver {
			  [...]
			  @Override
			  public void onReceive(Context context, Intent intent) {
			    EngagementAgent.getInstance(context).sendEvent("battery_low", null);
			  }
			  [...]
			}

##Rapports d'erreurs

### Erreurs de session

Les erreurs de session sont généralement utilisées pour signaler les erreurs qui affectent l'utilisateur pendant sa session.

**Exemple :**

			/** The user has entered invalid data in a form */
			public MyActivity extends EngagementActivity {
			  [...]
			  public void onMyFormSubmitted(MyForm form) {
			    [...]
			    /* The user has entered an invalid email address */
			    getEngagementAgent().sendSessionError("sign_up_email", null);
			    [...]
			  }
			  [...]
			}

### Erreurs autonomes

Contrairement aux erreurs de session, les erreurs autonomes peuvent se produire en dehors du contexte d'une session.

**Exemple :**

L'exemple suivant montre comment signaler une erreur chaque fois que la mémoire est insuffisante sur le téléphone pendant l'exécution de votre application.

			public MyApplication extends EngagementApplication {
			
			  @Override
			  protected void onApplicationProcessLowMemory() {
			    EngagementAgent.getInstance(this).sendError("low_memory", null);
			  }
			}

##Rapports de tâches

### Exemple

Supposons que vous voulez signaler la durée de votre processus de connexion :
			
			[...]
			public void signIn(Context context, ...) {
			
			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has started */
			  engagementAgent.startJob("sign_in", null);
			
			  [... sign in ...]
			
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### Rapport d'erreurs pendant une tâche

Les erreurs peuvent être associées à une tâche en cours au lieu de la session utilisateur active.

**Exemple :**

Supposons que vous voulez signaler une erreur pendant votre connexion :

[...]
public void signIn(Context context, ...) {

			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has been started */
			  engagementAgent.startJob("sign_in", null);
			
			  /* Try to sign in */
			  while(true)
			    try {
			      trySignin();
			      break;
			    }
			    catch(Exception e) {
			      /* Report the error to Engagement */
			      engagementAgent.sendJobError("sign_in_error", "sign_in", null);
			
			      /* Retry after a moment */
			      sleep(2000);
			    }
			  [...]
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### Rapports d'événements pendant une tâche

Les événements peuvent être associés à une tâche en cours au lieu de la session utilisateur active.

**Exemple :**

Supposons que nous disposons d'un réseau social et nous utilisons une tâche pour signaler la durée totale de connexion de l'utilisateur au serveur. L'utilisateur peut rester connecté en arrière-plan même quand il utilise une autre application ou que le téléphone est en veille, dans ce cas, il n'y a pas de session.

L'utilisateur peut recevoir des messages de ses amis, il s'agit d'un événement de tâche.
			
			[...]
			public void signin(Context context, ...) {
			  [...Sign in code...]
			  EngagementAgent.getInstance(context).startJob("connection", null);
			}
			[...]
			public void signout(Context context) {
			  [...Sign out code...]
			  EngagementAgent.getInstance(context).endJob("connection");
			}
			[...]
			public void onMessageReceived(Context context) {
			  [...Notify in status bar...]
			  EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
			}
			[...]

##Paramètres extras

Des données arbitraires peuvent être associées à des événements, des erreurs, des activités et des tâches.

Ces données peuvent être structurées, elles utilisent la classe Bundle d'Android (en fait, elles font office de paramètres extras dans les Intents Android). Notez qu'une classe Bundle peut contenir des tableaux ou d'autres instances de classe Bundle.

> [AZURE.IMPORTANT] Si vous insérez les paramètres parcelable ou serializable, vérifiez que leur méthode `toString()` est implémentée de sorte à retourner une chaîne lisible par l'utilisateur. Les classes sérialisables qui contiennent des champs non temporaires non sérialisables entraînent le blocage d'Android à l'appel de `bundle.putSerializable("key",value);`

> [AZURE.WARNING] Les matrices creuses dans les paramètres extras ne sont pas prises en charge, autrement dit, elles ne sont pas sérialisées sous forme de tableau. Vous devez les convertir en tableaux standard avant de les utiliser dans les paramètres extras.

### Exemple

			Bundle extras = new Bundle();
			extras.putString("video_id", 123);
			extras.putString("ref_click", "http://foobar.com/blog");
			EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### Limites

#### Clés

Chaque clé de la classe `Bundle` doit correspondre à l'expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les clés doivent au moins commencer par une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### Taille

Les paramètres extras sont limités à **1 024** caractères par appel (une fois encodés au format JSON par le service Engagement).

Dans l'exemple précédent, le format JSON envoyé au serveur contient 58 caractères :

			{"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##Rapports sur les informations de l'application

Vous pouvez signaler manuellement les informations de suivi (ou toute autre information propre à l'application) à l'aide de la fonction `sendAppInfo()`.

Notez que ces informations peuvent être envoyées de façon incrémentielle : seule la dernière valeur d'une clé donnée sera conservée pour un appareil donné.

À l'instar des paramètres extras des événements, la classe Bundle est utilisée pour récupérer les informations de l'application. Notez que les tableaux ou les sous-groupes seront considérés comme des chaînes plates (à l'aide de la sérialisation JSON).

### Exemple

Voici un exemple de code pour envoyer des informations sur la date de naissance et le sexe de l'utilisateur :

			Bundle appInfo = new Bundle();
			appInfo.putString("status", "premium");
			appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
			EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### Limites

#### Clés

Chaque clé de la classe `Bundle` doit correspondre à l'expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les clés doivent au moins commencer par une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### Taille

Les informations de l'application sont limitées à **1 024** caractères par appel (une fois encodées au format JSON par le service Engagement).

Dans l'exemple précédent, le format JSON envoyé au serveur contient 44 caractères :

			{"expiration":"2016-12-07","status":"premium"}

<!--HONumber=47-->
