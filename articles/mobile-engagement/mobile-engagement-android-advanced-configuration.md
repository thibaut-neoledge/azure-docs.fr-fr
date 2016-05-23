<properties
	pageTitle="Configuration avancée avec le Kit de développement logiciel (SDK) Mobile Engagement pour Android"
	description="Définition d’options de configuration avancées pour Android à l’aide du Kit de développement logiciel (SDK) Azure Mobile Engagement"
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
	ms.date="05/10/2016"
	ms.author="piyushjo;ricksal" />

# Configuration avancée avec le Kit de développement logiciel (SDK) Mobile Engagement pour Android

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-logging.md)

Cette procédure explique comment définir différentes options de configuration avancées pour les applications Engagement pour Android.

## Configuration requise

[AZURE.INCLUDE [Conditions préalables](../../includes/mobile-engagement-android-prereqs.md)]

## Autorisations requises
Un certain nombre d’options requièrent des autorisations spécifiques, qui sont toutes répertoriées ici pour référence, ainsi qu’en ligne dans la fonctionnalité spécifique. Ajoutez ces autorisations au fichier AndroidManifest.xml de votre projet, juste avant ou après la balise `<application>` :

Le code d’autorisation doit ressembler à ce qui suit, en renseignant l’autorisation appropriée du tableau ci-dessous.

		<uses-permission android:name="android.permission.[specific permission]"/>



| Autorisation | Quand l’utiliser |
| ---------- | --------- |
| INTERNET | Génération de rapports de base |
| ACCESS\_NETWORK\_STATE" | Génération de rapports de base |
| WRITE\_EXTERNAL\_STORAGE | Génération de rapports de base |
| RECEIVE\_BOOT\_COMPLETED | Génération de rapports de base |
| VIBRATE | Génération de rapports de base |
| DOWNLOAD\_WITHOUT\_NOTIFICATION | Notification de la vue d’ensemble |
| WAKE\_LOCK | Collecte de statistiques quand le Wi-Fi est utilisé ou l’écran est désactivé |
| RECEIVE\_BOOT\_COMPLETED | Activation des rapports en arrière-plan |
| ACCESS\_COARSE\_LOCATION | Rapports d’emplacement en temps réel |
| ACCESS\_FINE\_LOCATION | Rapports GPS |



À partir d’Android M, [certaines autorisations sont gérées lors de l’exécution](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Si vous utilisez déjà ``ACCESS_FINE_LOCATION``, vous n’avez pas besoin d’utiliser aussi ``ACCESS_COARSE_LOCATION``.

## Options de configuration du fichier de manifeste

### Rapport d'incidents

Si vous voulez désactiver les rapports d'incidents, ajoutez ce qui suit (entre les balises `<application>` et `</application>`) :

		<meta-data android:name="engagement:reportCrash" android:value="false"/>

### Seuil du mode rafale

Par défaut, le service Engagement génère des journaux en temps réel. Si votre application crée des journaux très fréquemment, il est préférable de les mettre en mémoire tampon et de les rassembler dans un rapport à intervalle régulier (on parle dans ce cas de « mode rafale »). Pour cela, ajoutez ce code entre les balises `<application>` et `</application>` :

		<meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Le mode rafale accroît légèrement l'autonomie de la batterie, mais il affecte aussi Engagement Monitor. En effet, la durée des sessions et des tâches est arrondie au seuil de rafale (les sessions et les tâches plus courtes que le seuil de rafale ne sont donc pas visibles). Il est recommandé d'utiliser un seuil de rafale inférieur à 30 000 (30 s).

### Délai d'expiration de session

Par défaut, une session est terminée dans les 10 s qui suivent la fin de la dernière activité (ce qui se produit généralement quand vous appuyez sur la touche Accueil ou Retour, quand vous mettez le téléphone en mode veille ou quand vous passez à une autre application). Cela permet d'éviter un fractionnement de session chaque fois que l'utilisateur quitte une application puis la rouvre très rapidement (ce qui peut se produire quand il ouvre une image ou une notification, etc.). Il est possible de modifier ce paramètre. Pour cela, ajoutez ce qui suit (entre les balises `<application>` et `</application>`) :

		<meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## Désactiver les rapports de suivi

### En appelant une méthode

Si vous voulez qu'Engagement arrête d'envoyer des journaux, vous pouvez appeler la méthode suivante :

			EngagementAgent.getInstance(context).setEnabled(false);

Cet appel est persistant : il utilise un fichier de préférences partagées.

Si le contrat est actif quand vous appelez cette fonction, l'arrêt du service peut prendre une minute. Toutefois, le service ne sera pas lancé la prochaine fois que vous lancerez l'application.

Vous pouvez activer à nouveau la création de rapports de journaux en appelant la même fonction avec `true`.

### Intégration dans votre propre `PreferenceActivity`

Au lieu d'appeler cette fonction, vous pouvez également intégrer ce paramètre directement dans votre `PreferenceActivity`.


Vous pouvez configurer Engagement pour qu'il utilise votre fichier de préférences (avec le mode souhaité) dans le fichier `AndroidManifest.xml` avec `application meta-data` :

-   La clé `engagement:agent:settings:name` est utilisée pour définir le nom du fichier de préférences partagées.
-   La clé `engagement:agent:settings:mode` est utilisée pour définir le mode du fichier de préférences partagées. Vous devez utiliser le même mode que dans votre `PreferenceActivity`. Le mode doit être passé comme un nombre. Si vous utilisez une combinaison d'indicateurs de constante dans votre code, vérifiez la valeur totale.

Engagement utilise toujours la clé booléenne `engagement:key` dans le fichier de préférences pour la gestion de ce paramètre.

L'exemple de suivant de `AndroidManifest.xml` montre les valeurs par défaut :

			<application>
			    [...]
			    <meta-data
			      android:name="engagement:agent:settings:name"
			      android:value="engagement.agent" />
			    <meta-data
			      android:name="engagement:agent:settings:mode"
			      android:value="0" />

Vous pouvez ensuite ajouter un `CheckBoxPreference` dans votre disposition préférée, comme celle qui suit :

			<CheckBoxPreference
			  android:key="engagement:enabled"
			  android:defaultValue="true"
			  android:title="Use Engagement"
			  android:summaryOn="Engagement is enabled."
			  android:summaryOff="Engagement is disabled." />

<!---HONumber=AcomDC_0511_2016-->