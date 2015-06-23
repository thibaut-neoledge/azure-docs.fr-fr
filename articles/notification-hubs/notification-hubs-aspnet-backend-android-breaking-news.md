<properties 
	pageTitle="Didacticiel Utilisation de Notification Hubs pour diffuser les dernières nouvelles - Android" 
	description="Découvrez comment utiliser Azure Service Bus Notification Hubs pour envoyer des notifications de dernières nouvelles aux appareils Android." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="ricksal"/>


# Utilisation de Notification Hubs pour diffuser les dernières nouvelles
<div class="dev-center-tutorial-selector sublanding">     	
	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a>
	<a href="/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android" class="current">Android</a>
</div>

Cette rubrique vous présente l'utilisation d'Azure Notification Hubs pour diffuser des notifications relatives aux dernières nouvelles vers une application Android. Lorsque vous aurez terminé, vous pourrez vous inscrire aux catégories de dernières nouvelles qui vous intéressent et recevoir uniquement des notifications Push pour ces catégories. Ce scénario est un modèle courant pour de nombreuses applications pour lesquelles des notifications doivent être envoyées à des groupes d'utilisateurs qui ont signalé antérieurement un intérêt, par exemple, lecteur RSS, applications pour fans de musique, etc. 

Les scénarios de diffusion sont activés en incluant une ou plusieurs _balises_ lors de la création d'une inscription dans le concentrateur de notification. Lorsque des notifications sont envoyées à une balise, tous les appareils pour lesquels cette balise est inscrite reçoivent la notification. Les balises étant de simples chaînes, il n'est pas nécessaire de les mettre en service à l'avance. Pour plus d'informations sur les balises, voir [Vue d'ensemble de Notification Hubs]. 

Ce didacticiel vous familiarise avec les étapes de base pour activer ce scénario :

1. [Ajouter une sélection de catégories à l'application]
2. [Inscription à des notifications]
3. [Envoyer des notifications à partir de votre serveur principal]
4. [Exécution de l'application et génération de notifications]

Cette rubrique s'appuie sur l'application que vous avez créée dans [Prise en main de Notification Hubs][get-started]. Avant de commencer ce didacticiel, vous devez suivre celui intitulé [Prise en main de Notification Hubs][get-started].

##<a name="adding-categories"></a>Ajouter une sélection de catégories à l'application

La première étape consiste à ajouter des éléments de l'interface utilisateur à l'activité principale existante qui permettent à l'utilisateur de sélectionner des catégories auxquelles s'inscrire. Les catégories sélectionnées par un utilisateur sont stockées sur l'appareil. Lorsque l'application démarre, une inscription d'appareil est créée dans votre concentrateur de notification avec les catégories sélectionnées sous forme de balises. 

1. Ouvrez votre fichier res/layout/activity_main.xml et remplacez le contenu par ce qui suit :
			
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:tools="http://schemas.android.com/tools"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:paddingBottom="@dimen/activity_vertical_margin"
		    android:paddingLeft="@dimen/activity_horizontal_margin"
		    android:paddingRight="@dimen/activity_horizontal_margin"
		    android:paddingTop="@dimen/activity_vertical_margin"
		    tools:context="com.example.breakingnews.MainActivity"
		    android:orientation="vertical">
		
		        <CheckBox
		            android:id="@+id/worldBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_world" />
		        <CheckBox
		            android:id="@+id/politicsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_politics" />
		        <CheckBox
		            android:id="@+id/businessBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_business" />
		        <CheckBox
		            android:id="@+id/technologyBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_technology" />
		        <CheckBox
		            android:id="@+id/scienceBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_science" />
		        <CheckBox
		            android:id="@+id/sportsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_sports" />
			    <Button
			        android:layout_width="wrap_content"
			        android:layout_height="wrap_content"
			        android:onClick="subscribe"
			        android:text="@string/button_subscribe" />
		</LinearLayout>

2. Ouvrez votre fichier res\values\string.xml et ajoutez les lignes suivantes :

	    <string name="button_subscribe">Subscribe</string>
	    <string name="label_world">World</string>
	    <string name="label_politics">Politics</string>
	    <string name="label_business">Business</string>
	    <string name="label_technology">Technology</string>
	    <string name="label_science">Science</string>
	    <string name="label_sports">Sports</string>

	La présentation graphique de votre fichier main_activity.xml doit ressembler à ceci :

	![][A1]

3. À présent, créez une classe **Notifications** dans le même package que votre classe **MainActivity**.

		import java.util.HashSet;
		import java.util.Set;
		
		import android.content.Context;
		import android.content.SharedPreferences;
		import android.os.AsyncTask;
		import android.util.Log;
		import android.widget.Toast;
		
		import com.google.android.gms.gcm.GoogleCloudMessaging;
		import com.microsoft.windowsazure.messaging.NotificationHub;		
		
		public class Notifications {
			private static final String PREFS_NAME = "BreakingNewsCategories";
			private GoogleCloudMessaging gcm;
			private NotificationHub hub;
			private Context context;
			private String senderId;
			
			public Notifications(Context context, String senderId) {
				this.context = context;
				this.senderId = senderId;
				
				gcm = GoogleCloudMessaging.getInstance(context);
		        hub = new NotificationHub(<hub name>, <connection string with listen access>, context);
			}
			
			public void storeCategoriesAndSubscribe(Set<String> categories)
			{
				SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			    settings.edit().putStringSet("categories", categories).commit();
			    subscribeToCategories(categories);
			}
			
			public void subscribeToCategories(final Set<String> categories) {
				new AsyncTask<Object, Object, Object>() {
					@Override
					protected Object doInBackground(Object... params) {
						try {
							String regid = gcm.register(senderId);
					        hub.register(regid, categories.toArray(new String[categories.size()]));
						} catch (Exception e) {
							Log.e("MainActivity", "Failed to register - " + e.getMessage());
							return e;
						}
						return null;
					}
		
					protected void onPostExecute(Object result) {
						String message = "Subscribed for categories: "
								+ categories.toString();
						Toast.makeText(context, message,
								Toast.LENGTH_LONG).show();
					}
				}.execute(null, null, null);
			}
			
		}

	Cette classe utilise le stockage local pour stocker les catégories de nouvelles que cet appareil doit recevoir. Elle comporte également des méthodes pour s'inscrire à ces catégories.

4. Dans le code ci-dessus, remplacez les espaces réservés `<hub name>` et `<connection string with listen access>` par le nom de votre concentrateur de notification et par la chaîne de connexion pour *DefaultListenSharedAccessSignature* obtenue précédemment.

	> [AZURE.NOTE] Les informations d'identification distribuées avec une application cliente n'étant généralement pas sécurisées, vous ne devez distribuer que la clé d'accès d'écoute avec votre application cliente. L'accès d'écoute permet à votre application de s'inscrire à des notifications, mais les inscriptions existantes ne peuvent pas être modifiées et les notifications ne peuvent pas être envoyées. La clé d'accès complet est utilisée dans un service de serveur principal sécurisé pour l'envoi de notifications et la modification d'inscriptions existantes.

4. Dans la classe **MainActivity**, supprimez les champs privés pour **NotificationHub** et **GoogleCloudMessaging**, puis ajoutez un champ pour **Notifications** :

		// private GoogleCloudMessaging gcm;
		// private NotificationHub hub;
		private Notifications notifications;

5. Ensuite, dans la méthode **onCreate**, supprimez l'initialisation du champ **hub** et de la méthode **registerWithNotificationHubs**. Ajoutez ensuite les lignes suivantes, qui initialisent une instance de la classe **Notifications**. La méthode doit contenir les lignes qui suivent :

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
	
			NotificationsManager.handleNotifications(this, SENDER_ID,
					MyHandler.class);
	
			notifications = new Notifications(this, SENDER_ID);
		}

6. Ajoutez ensuite la méthode suivante :
	
	    public void subscribe(View sender) {
			final Set<String> categories = new HashSet<String>();
	
			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			if (world.isChecked())
				categories.add("world");
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			if (politics.isChecked())
				categories.add("politics");
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			if (business.isChecked())
				categories.add("business");
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			if (technology.isChecked())
				categories.add("technology");
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			if (science.isChecked())
				categories.add("science");
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			if (sports.isChecked())
				categories.add("sports");
	
			notifications.storeCategoriesAndSubscribe(categories);
	    }
	
	Cette méthode crée une liste de catégories et utilise la classe **Notifications** pour stocker la liste dans le stockage local et inscrire les balises correspondantes auprès de votre concentrateur de notification. Lorsque des catégories sont modifiées, l'inscription est à nouveau créée avec les nouvelles catégories.

Votre application peut désormais stocker un ensemble de catégories dans le stockage local sur l'appareil et s'inscrire auprès du concentrateur de notification lorsque l'utilisateur modifie la sélection des catégories. 

##<a name="register"></a>S'inscrire à des notifications

Les étapes suivantes permettent de s'inscrire auprès du concentrateur de notification au démarrage à l'aide des catégories qui ont été stockées dans le stockage local. 

> [AZURE.NOTE] Comme la valeur de registrationId affectée par Google Cloud Messaging (GCM) peut changer à n'importe quel moment, vous devez vous inscrire fréquemment aux notifications afin d'éviter les défaillances. Cet exemple s'inscrit aux notifications chaque fois que l'application démarre. Pour les applications exécutées fréquemment, plus d'une fois par jour, vous pouvez probablement ignorer l'inscription afin de préserver la bande passante si moins d'un jour s'est écoulé depuis l'inscription précédente.

1. Ajoutez le code ci-après à la classe **Notifications** :

		public Set<String> retrieveCategories() {
			SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			return settings.getStringSet("categories", new HashSet<String>());
		}

	Ainsi, les catégories définies dans la classe sont renvoyées.

2. Ajoutez maintenant ce code à la fin de la méthode **onCreate** dans la classe **MainActivity** :

		notifications.subscribeToCategories(notifications.retrieveCategories());

	Cette opération garantit que chaque fois que l'application démarre, elle récupère les catégories du stockage local et demande une inscription pour ces catégories. La méthode **InitNotificationsAsync** a été créée dans le cadre du didacticiel [Prise en main de Notification Hubs], mais n'est pas requise dans cette rubrique.

3. Ajoutez ensuite la méthode suivante à **MainActivity** :

		@Override
		protected void onStart() {
			super.onStart();
			
			Set<String> categories = notifications.retrieveCategories();
			
			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			world.setChecked(categories.contains("world"));
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			politics.setChecked(categories.contains("politics"));
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			business.setChecked(categories.contains("business"));
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			technology.setChecked(categories.contains("technology"));
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			science.setChecked(categories.contains("science"));
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			sports.setChecked(categories.contains("sports"));
		}

	Cette opération met l'activité principale à jour en fonction du statut des catégories enregistrées précédemment. 

L'application est désormais terminée et peut stocker un ensemble de catégories dans le stockage local de l'appareil utilisé pour s'inscrire auprès du concentrateur de notification lorsque l'utilisateur modifie la sélection des catégories. Ensuite, nous allons définir un serveur principal qui peut envoyer des notifications de catégorie à cette application.

<h2><a name="send"></a>Envoyer des notifications à partir de votre serveur principal</h2>

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>Exécuter l'application et générer des notifications

1. Dans Eclipse, générez l'application et lancez-la sur un appareil ou un émulateur.
	
	Notez que l'interface utilisateur de l'application fournit un ensemble de bascules qui vous permet de choisir les catégories auxquelles vous abonner. 

2. Activez une ou plusieurs bascules de catégories, puis cliquez sur **S'abonner**.

	L'application convertit les catégories sélectionnées en balises et demande une nouvelle inscription de l'appareil pour les balises sélectionnées depuis le concentrateur de notification. Les catégories inscrites sont renvoyées et affichées dans une boîte de dialogue.

4. Envoyez une nouvelle notification depuis le serveur principal de l'une des manières suivantes :

	+ **Application console .NET :** démarre l'application console.

	+ **Java/PHP:** exécuter votre application/script.

	Les notifications pour les catégories sélectionnées apparaissent comme notifications toast.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons appris à diffuser les dernières nouvelles par catégorie. Envisagez de suivre un des didacticiels suivants qui soulignent d'autres scénarios avancés Notification Hubs :

+ [Utilisation de Notification Hubs pour diffuser les dernières nouvelles localisées]

	Apprenez à développer l'application relative aux dernières nouvelles pour permettre l'envoi de notifications localisées. 

+ [Notification des utilisateurs avec Notification Hubs]

	Apprenez comment transmettre des notifications à des utilisateurs authentifiés spécifiques. Il s'agit d'une solution appropriée pour l'envoi de notifications uniquement vers des utilisateurs spécifiques.


<!-- Anchors. -->
[Ajouter une sélection de catégories à l'application]: #adding-categories
[Inscription à des notifications]: #register
[Envoyer des notifications à partir de votre serveur principal]: #send
[Exécution de l'application et génération de notifications]: #test-app
[Étapes suivantes]: #next-steps

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-get-started.md
[Utilisation de Notification Hubs pour diffuser les dernières nouvelles localisées]: /manage/services/notification-hubs/breaking-news-localized-dotnet/ 
[Notification des utilisateurs avec Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Vue d'ensemble de Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Vue d'ensemble de Notification Hubs pour Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Portail de gestion Azure]: https://manage.windowsazure.com/
[wns (objet)]: http://go.microsoft.com/fwlink/p/?LinkId=260591

<!--HONumber=49--> 