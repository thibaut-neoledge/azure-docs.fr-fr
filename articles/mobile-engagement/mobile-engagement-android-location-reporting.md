<properties
	pageTitle="Génération de rapports d’emplacement pour le SDK Azure Mobile Engagement pour Android"
	description="Décrit comment configurer la génération de rapports d’emplacement pour le SDK Azure Mobile Engagement pour Android"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/12/2016"
	ms.author="piyushjo;ricksal" />

# Génération de rapports d’emplacement pour le SDK Azure Mobile Engagement pour Android

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

Cette rubrique explique comment générer des rapports d’emplacement pour votre application Android.

## Configuration requise

[AZURE.INCLUDE [Conditions préalables](../../includes/mobile-engagement-android-prereqs.md)]

## Rapports d'emplacement

Si vous souhaitez créer des rapports concernant les emplacements, vous devez ajouter quelques lignes de configuration (entre les balises `<application>` et `</application>`).

### Rapports d'emplacement de zone différé

Le rapport d'emplacement de zone différé permet d'indiquer le pays, la région et la localité associés aux appareils. Ce type de rapport d'emplacement utilise uniquement des emplacements réseau (basés sur l'ID cellulaire ou le Wi-Fi). La zone de l'appareil est signalée une fois maximum par session. Le GPS n'est jamais utilisé, ce type de rapport d'emplacement a donc très peu d'impact (pour ne pas dire aucun) sur la batterie.

Les zones signalées sont utilisées pour calculer des statistiques géographiques relatives aux utilisateurs, aux sessions, aux événements et aux erreurs. Elles peuvent également servir de critère dans les couvertures campagne.

Pour activer le service de localisation de zone différé, vous pouvez utiliser la configuration précédemment mentionnée dans cette procédure :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l'autorisation suivante si elle manque :

	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou vous pouvez continuer à utiliser ``ACCESS_FINE_LOCATION`` si vous l’utilisez déjà dans votre application.

### Rapports d'emplacement en temps réel

Le rapport d'emplacement en temps réel permet de signaler la latitude et la longitude associées aux appareils. Par défaut, ce type de rapport d'emplacement utilise uniquement des emplacements réseau (basés sur l'ID cellulaire ou le Wi-Fi) et le rapport n'est actif que lorsque l'application s'exécute en premier plan (c'est-à-dire pendant une session).

Les emplacements en temps réel ne sont *PAS* utilisés pour calculer des statistiques. Leur seul but est de permettre l'utilisation d'un critère de géorepérage en temps réel < portée-public-gardiennage virtuel > dans les campagnes Reach.

Pour activer les rapports d’emplacement en temps réel, ajoutez une ligne de code à l’endroit où vous définissez la chaîne de connexion Engagement dans l’activité du programme de lancement. Le résultat aura l’aspect suivant :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l'autorisation suivante si elle manque :

	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou vous pouvez continuer à utiliser ``ACCESS_FINE_LOCATION`` si vous l’utilisez déjà dans votre application.

#### Rapports GPS

Par défaut, le rapport d'emplacement en temps réel utilise uniquement des emplacements réseau. Pour activer l’utilisation des localisations GPS (beaucoup plus précises), utilisez l’objet de configuration suivant :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l'autorisation suivante si elle manque :

	<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Rapports en arrière-plan

Par défaut, le rapport d'emplacement en temps réel est uniquement actif quand l'application s'exécute en premier plan (c'est-à-dire pendant une session). Pour activer la génération de rapports également en arrière-plan, utilisez l’objet de configuration suivant :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Quand l'application s'exécute en arrière-plan, seuls les emplacements réseau sont signalés, même si vous avez activé le GPS.

Le rapport d'emplacement en arrière-plan sera arrêté si l'utilisateur redémarre son appareil. Pour configurer un redémarrage automatique au moment du démarrage, ajoutez ce qui suit :

	<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
		   android:exported="false">
		<intent-filter>
		    <action android:name="android.intent.action.BOOT_COMPLETED" />
		</intent-filter>
	</receiver>

Vous devez également ajouter l'autorisation suivante si elle manque :

	<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## Autorisations Android M

À partir d’Android M, certaines autorisations sont gérées lors de l’exécution et nécessitent une approbation de l’utilisateur.

Les autorisations d’exécution seront désactivées par défaut pour les nouvelles installations d’application si vous ciblez le niveau d’API Android 23. Sinon, elles seront activées par défaut.

L’utilisateur peut activer/désactiver ces autorisations dans le menu des paramètres de l’appareil. La désactivation des autorisations à partir du menu système arrête les processus en arrière-plan de l’application. Il s’agit d’un comportement du système qui n’a aucune incidence sur la capacité à recevoir des notifications en arrière-plan.

Dans le cadre de la génération de rapports d’emplacement Mobile Engagement, les autorisations qui requièrent une approbation au moment de l’exécution sont :

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Vous devez demander les autorisations à l’utilisateur via une boîte de dialogue système standard. Si l’utilisateur approuve, vous devez indiquer à ``EngagementAgent`` de prendre enc ompte cette modification en temps réel (sinon, la modification sera traitée au prochain lancement de l’application par l’utilisateur).

Voici un exemple de code à utiliser dans une activité de votre application pour demander des autorisations et transmettre le résultat, si positif, à ``EngagementAgent`` :

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

<!---HONumber=AcomDC_0518_2016-->