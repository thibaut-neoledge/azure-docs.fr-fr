<properties 
	pageTitle="Intégration du Kit de développement logiciel (SDK) Azure Mobile Engagement pour Android" 
	description="Dernières mises à jour et procédures pour le Kit de développement logiciel (SDK) Azure Mobile Engagement pour Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" class="current">Android</a></div>


#Comment intégrer Engagement à Android

> [AZURE.IMPORTANT] Le niveau minimum de l'API SDK Android doit être égal ou supérieur à 10 (Android 2.3.3 ou version ultérieure).

Cette procédure décrit la méthode la plus simple pour activer les fonctions d'analyse et de surveillance dans votre application Android.

Les étapes suivantes suffisent à activer la création de journaux nécessaires au calcul de l'ensemble des statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les éléments techniques. Les journaux nécessaires aux autres statistiques telles que les événements, les erreurs et les tâches doivent être créés manuellement à l'aide de l'API Engagement (voir android-sdk-engagement-advanced), car ces statistiques dépendent de l'application.

##Intégrer le SDK et le service Engagement à un projet Android

Récupérez `mobile-engagement-VERSION.jar` et placez-les dans le dossier `libs` de votre projet Android (créez le dossier de la bibliothèque si celui-ci n'existe pas).

> [AZURE.IMPORTANT]
> Si vous générez votre package d'application avec ProGuard, vous devez conserver certaines classes. Vous pouvez utiliser l'extrait de configuration suivant :
>
> 
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

Spécifiez votre chaîne de connexion Engagement en appelant la méthode suivante dans l'activité du programme de lancement :

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

La chaîne de connexion de votre application s'affiche dans le portail Azure.

-   Si ce n'est pas le cas, ajoutez les autorisations Android suivantes (avant la balise `<application>`) :

			<uses-permission android:name="android.permission.INTERNET"/>
			<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Sur certains modèles, il n'est pas possible de générer un identificateur d'appareil Engagement à partir de ANDROID\_ID (il peut être incorrect ou non disponible). Dans ce cas, le Kit de développement logiciel (SDK) génère un identificateur d'appareil aléatoire et tente de l'enregistrer sur le dispositif de stockage externe de l'appareil pour que les autres applications Engagement puissent partager le même identificateur (il est également enregistré comme une préférence partagée pour garantir que l'application utilisera toujours le même identificateur d'appareil, quoi qu'il arrive au stockage externe). Pour que ce mécanisme fonctionne correctement, vous devez ajouter l'autorisation suivante, si elle manque (avant la balise `<application>`) :

			<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

-   Ajoutez la section suivante (entre les balises `<application>` et `</application>`) :

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

-   Remplacez `<Your application name>` par le nom de votre application.

> [AZURE.TIP] L'attribut  `android:label`  vous permet de choisir le nom du service Engagement tel qu'il apparaîtra aux utilisateurs finaux dans l'écran Services en cours d'exécution de leur téléphone. Il est recommandé de définir cet attribut sur `"<Your application name>Service"` (par exemple, `"AcmeFunGameService"`).

La spécification de l'attribut  `android:process` permet au service Engagement d'être exécuté dans le cadre de son propre processus (l'exécution d'Engagement dans le même processus que votre application peut rendre votre thread principal/d'interface utilisateur moins réactif).

> [AZURE.NOTE] Tout code que vous placez dans  `Application.onCreate()` et autres rappels d'application sera exécuté pour l'ensemble des processus de votre application, y compris le service Engagement. Cela peut avoir des effets indésirables (tels que des allocations de mémoire et des threads inutiles dans le processus Engagement, ou des récepteurs ou services de diffusion en double).

Si vous remplacez  `Application.onCreate()`, il est recommandé d'ajouter l'extrait de code suivant au début de la fonction  `Application.onCreate()` :

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;
			
			   ... Your code...
			 }

Vous pouvez faire la même chose pour  `Application.onTerminate()`, `Application.onLowMemory()` et  `Application.onConfigurationChanged(...)`.

Vous pouvez également étendre  `EngagementApplication` au lieu d'étendre  `Application` : le rappel  `Application.onCreate()` effectue la vérification du processus et appelle  `Application.onApplicationProcessCreate()` uniquement si le processus actuel n'est pas celui qui héberge le service Engagement. Les mêmes règles s'appliquent pour les autres rappels.

##Création de rapports de base

### Méthode recommandée : surchargez les classes  `Activity`

Pour activer la création d'un rapport à partir de tous les journaux nécessaires à Engagement pour calculer les statistiques relatives aux utilisateurs, aux sessions, aux activités, aux incidents et aux éléments techniques, il vous suffit de faire hériter toutes les sous-classes `*Activity` des classes  `Engagement*Activity` correspondantes (par exemple, si votre activité héritée étend  `ListActivity`, assurez-vous qu'elle étend `EngagementListActivity`).

**Sans Engagement :**

			package com.company.myapp;
			
			import android.app.Activity;
			import android.os.Bundle;
			
			public class MyApp extends Activity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

**Avec Engagement :**

			package com.company.myapp;
			
			import com.microsoft.azure.engagement.activity.EngagementActivity;
			import android.os.Bundle;
			
			public class MyApp extends EngagementActivity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

> [AZURE.IMPORTANT] Lorsque vous utilisez  `EngagementListActivity` ou  `EngagementExpandableListActivity`, assurez-vous que tout appel à  `requestWindowFeature(...);` est effectué avant l'appel à `super.onCreate(...);`, sinon un incident se produira.

Nous fournissons des sous-classes de `FragmentActivity` et  `MapActivity`. Toutefois, pour éviter les problèmes avec les applications qui utilisent **ProGuard**, nous ne les avons pas incluses dans  `engagement.jar`.

Vous trouverez ces classes dans le dossier  `src`. Vous pourrez les copier dans votre projet. Les classes se trouvent également dans **JavaDoc**.

### Méthode alternative : appelez  `startActivity()` et  `endActivity()` manuellement

Si vous ne pouvez pas ou ne voulez pas surcharger vos classes  `Activity`, vous pouvez démarrer et terminer vos activités en appelant les méthodes  `EngagementAgent` directement.

> [AZURE.IMPORTANT] Le Kit de développement logiciel (SDK) Android n'appelle jamais la méthode  `endActivity()`, même quand l'application est fermée (sur Android, les applications ne sont en fait jamais fermées). Il est donc  *fortement* recommandé d'appeler la méthode  `startActivity()` dans le rappel  `onResume` de  *toutes * vos activités, et la méthode  `endActivity()` dans le rappel  `onPause()` de  *toutes* vos activités. Il s'agit de la seule façon d'empêcher la fuite de sessions. Dans le cas d'une fuite de session, le service Engagement n'est jamais déconnecté du serveur principal d'Engagement (étant donné que le service reste connecté tant qu'une session est en cours).

Voici un exemple :

			public class MyActivity extends Some3rdPartyActivity
			{
			  @Override
			  protected void onResume()
			  {
			    super.onResume();
			    String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
			    EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
			  }
			
			  @Override
			  protected void onPause()
			  {
			    super.onPause();
			    EngagementAgent.getInstance(this).endActivity();
			  }
			}

Cet exemple est très similaire à la classe  `EngagementActivity` et à ses variantes, dont le code source est fourni dans le dossier  `src`.

##Test

Maintenant, vérifiez votre intégration en lisant " Comment tester l'intégration d'Engagement sur Android ".

Les sections suivantes sont facultatives.

##Rapports sur les emplacements

Si vous souhaitez créer des rapports concernant les emplacements, vous devez ajouter quelques lignes de configuration (entre les balises `<application>` et `</application>`).

### Création de rapports sur la géolocalisation en différé

La création de rapports sur la géolocalisation en différé permet d'obtenir des rapports sur les pays, régions et localités associés aux appareils. Ce type de rapports utilise uniquement les emplacements réseau (basés sur l'ID cellulaire ou le Wi-Fi). La région de l'appareil est signalée au plus une fois par session. Le GPS n'est jamais utilisé. Ce type de rapport d'emplacement n'a donc que très peu (voire aucun) impact sur la batterie.

Les zones qui font l'objet d'un rapport sont utilisées pour calculer des statistiques géographiques sur les utilisateurs, les sessions, les événements et les erreurs. Elles peuvent également servir de critères dans les couvertures campagnes. La dernière zone connue signalée pour un appareil peut être récupérée grâce à l'[API
d'appareil].

Pour activer la création de rapports sur la géolocalisation en différé, ajoutez ce qui suit :

			<meta-data android:name="engagement:locationReport:lazyArea" android:value="true"/>

Vous devez également ajouter l'autorisation suivante si elle manque :

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

### Création de rapports d'emplacement en temps réel

La création de rapports d'emplacement en temps réel permet de connaître la latitude et la longitude des appareils. Par défaut, ce type de rapports d'emplacement utilise uniquement des emplacements réseau (basés sur l'ID cellulaire ou le Wi-Fi). De plus, la création de rapports n'est active que quand l'application s'exécute en premier plan (c'est-à-dire pendant une session).

Les emplacements en temps réel ne sont  *pas* utilisés pour calculer des statistiques. Leur seul but est de permettre l'utilisation du
critère de délimitation géographique en temps réel \<Reach-Audience-geofencing\> dans les couvertures campagnes.

Pour activer la création de rapports d'emplacement en temps réel, ajoutez ce qui suit :

			<meta-data android:name="engagement:locationReport:realTime" android:value="true" />

Vous devez également ajouter l'autorisation suivante si elle manque :

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

#### Création de rapports basés sur le GPS

Par défaut, la création de rapports d'emplacement en temps réel utilise uniquement les emplacements basés sur le réseau. Pour activer l'utilisation des emplacements basés sur GPS (qui sont beaucoup plus précis), ajoutez ce qui suit :

			<meta-data android:name="engagement:locationReport:realTime:fine" android:value="true" />

Vous devez également ajouter l'autorisation suivante si elle manque :

			<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Création de rapports en arrière-plan

Par défaut, la création de rapports d'emplacement en temps réel n'est active que quand l'application s'exécute en premier plan (c'est-à-dire pendant une session). Pour activer également la création de rapports en arrière-plan, ajoutez ce qui suit :

			<meta-data android:name="engagement:locationReport:realTime:background" android:value="true" />

> [AZURE.NOTE] Quand l'application s'exécute en arrière-plan, seuls les emplacements sur le réseau sont pris en compte, même si vous avez activé le GPS.

Le rapport d'emplacement en arrière-plan sera arrêté si l'utilisateur redémarre son appareil. Pour configurer un redémarrage automatique au moment du démarrage, ajoutez ce qui suit :

			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>

Vous devez également ajouter l'autorisation suivante si elle manque :

			<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

##Création de rapports avancés

Si vous le souhaitez, vous pouvez créer des rapports concernant des événements d'application spécifiques, des erreurs et des tâches. Dans ce cas, vous devrez utiliser l'API Engagement via les méthodes de la classe  `EngagementAgent`. Un objet de cette classe peut être récupéré en appelant la méthode statique  `EngagementAgent.getInstance()`.

L'API Engagement permet d'utiliser toutes les fonctionnalités avancées d'Engagement. Pour en savoir plus sur cette API, lisez
" Comment utiliser l'API Engagement sur Android ", ainsi que la documentation technique de la classe  `EngagementAgent`.

##Configuration avancée (dans AndroidManifest.xml)

Si vous voulez être sûr que les statistiques soient envoyées en temps réel quand vous utilisez le Wi-Fi ou quand l'écran est désactivé, ajoutez l'autorisation facultative suivante :

			<uses-permission android:name="android.permission.WAKE_LOCK"/>

Si vous voulez désactiver les rapports d'incidents, ajoutez ce qui suit (entre les balises `<application>` et `</application>`) :

			<meta-data android:name="engagement:reportCrash" android:value="false"/>

Par défaut, le service Engagement crée des journaux en temps réel. Si votre application crée des journaux très fréquemment, il est préférable de les mettre en mémoire tampon et de les rassembler dans un rapport à intervalle régulier (on parle dans ce cas de " mode rafale "). Pour cela, ajoutez ce qui suit (entre les balises `<application>` et `</application>`) :

			<meta-data android:name="engagement:burstThreshold" android:value="<interval between too bursts (in milliseconds)>"/>

Le mode rafale augmente légèrement l'autonomie de la batterie, mais a un impact sur la fonctionnalité de surveillance d'Engagement. La durée de toutes les sessions et tâches sera arrondie à la valeur de seuil du mode rafale (les sessions et les tâches dont la durée est inférieure à ce seuil peuvent donc ne pas être visibles). Il est recommandé d'utiliser une valeur de seuil de rafale qui ne dépasse pas 30 000 (30 s).

Par défaut, le service Engagement établit une connexion avec nos serveurs dès que le réseau est disponible. Si vous voulez différer la connexion, ajoutez ce qui suit (entre les balises `<application>` et `</application>`) :

			<meta-data android:name="engagement:connection:delay" android:value="<delay (in milliseconds)>"/>

Par défaut, une session est terminée dans les 10 s qui suivent la fin de la dernière activité (ce qui se produit généralement quand vous appuyez sur la touche Accueil ou Retour, quand vous mettez le téléphone en mode veille ou quand vous passez à une autre application). Cela permet d'éviter un fractionnement de session chaque fois que l'utilisateur quitte une application puis la rouvre très rapidement (ce qui peut se produire quand il ouvre une image ou une notification, etc.). Il est possible de modifier ce paramètre. Pour cela, ajoutez ce qui suit (entre les balises `<application>` et `</application>`) :

			<meta-data android:name="engagement:sessionTimeout" android:value="<session timeout (in milliseconds)>"/>

##Désactiver les rapports de journaux

### À l'aide d'un appel de méthode

Si vous voulez qu'Engagement arrête d'envoyer des journaux, vous pouvez appeler :

			EngagementAgent.getInstance(context).setEnabled(false);

Cet appel est persistant : il utilise un fichier de préférences partagées.

Si le contrat est actif quand vous appelez cette fonction, l'arrêt du service peut prendre une minute. Toutefois, le service ne sera pas lancé la prochaine fois que vous lancerez l'application.

Vous pouvez de nouveau activer la création de journaux de rapports en appelant la même fonction avec  `true`.

### Intégration dans votre propre  `PreferenceActivity`

Au lieu d'appeler cette fonction, vous pouvez également intégrer ce paramètre directement dans votre  `PreferenceActivity`.

Vous pouvez configurer Engagement pour qu'il utilise votre fichier de préférences (avec le mode souhaité) dans le fichier  `AndroidManifest.xml`  `application meta-data` :

-   La clé  `engagement:agent:settings:name`  est utilisée pour définir le nom du fichier de préférences partagées.
-   La clé  `engagement:agent:settings:mode`  est utilisée pour définir le mode du fichier de préférences partagées. Vous devez utiliser le même mode que dans votre  `PreferenceActivity`. Le mode doit être passé comme un nombre. Si vous utilisez une combinaison d'indicateurs de constante dans votre code, vérifiez la valeur totale.

Engagement utilise toujours la clé booléenne  `engagement:key` dans le fichier de préférences pour la gestion de ce paramètre.

L'exemple de  `AndroidManifest.xml` suivant montre les valeurs par défaut :

			<application>
			    [...]
			    <meta-data
			      android:name="engagement:agent:settings:name"
			      android:value="engagement.agent" />
			    <meta-data
			      android:name="engagement:agent:settings:mode"
			      android:value="0" />

Vous pouvez ensuite ajouter un  `CheckBoxPreference` dans votre disposition préférée, comme celle qui suit :

			<CheckBoxPreference
			  android:key="engagement:enabled"
			  android:defaultValue="true"
			  android:title="Use Engagement"
			  android:summaryOn="Engagement is enabled."
			  android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[API d'appareil]: http://go.microsoft.com/?linkid=9876094

<!--HONumber=47-->
