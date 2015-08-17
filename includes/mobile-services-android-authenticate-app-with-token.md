
L'exemple précédent montrait une connexion standard, qui nécessite que le client contacte le fournisseur d'identité et le service mobile à chaque démarrage de l'application. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d'utilisation si de nombreux clients tentent de lancer votre application en même temps. Une meilleure approche consiste à mettre en cache le jeton d'autorisation renvoyé par Mobile Services et à l'utiliser en premier avant de faire appel à la connexion via un fournisseur.

>[AZURE.NOTE]Vous pouvez mettre en cache le jeton émis par Mobile Services, que vous utilisiez l'authentification gérée par un client ou gérée par un service. Ce didacticiel utilise cette dernière.


1. Ouvrez le fichier ToDoActivity.java, puis ajoutez les instructions import suivantes :

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Ajouter les membres suivants à la classe `ToDoActivity`.

    	public static final String SHAREDPREFFILE = "temp";	
	    public static final String USERIDPREF = "uid";	
    	public static final String TOKENPREF = "tkn";	


3. Dans le fichier ToDoActivity.java, ajoutez la définition suivante pour la méthode `cacheUserToken`.
 
    	private void cacheUserToken(MobileServiceUser user)
	    {
    		SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
    	    Editor editor = prefs.edit();
	        editor.putString(USERIDPREF, user.getUserId());
    	    editor.putString(TOKENPREF, user.getAuthenticationToken());
	        editor.commit();
    	}	
  
    Cette méthode stocke l'ID d'utilisateur et le jeton dans un fichier de préférences marqué comme privé. Ceci doit protéger l'accès au cache afin que les autres applications de l'appareil n'aient pas accès au jeton, car les préférences sont exécutées dans le bac à sable (sandbox) de l'application. Toutefois, si une personne tente d'accéder à l'appareil, il est possible qu'elle puisse accéder au cache du jeton par d'autres moyens.

    >[AZURE.NOTE]Vous pouvez protéger davantage le jeton avec chiffrement si le jeton accès à vos données est considéré comme très sensible et que quelqu’un peut accéder au périphérique. Toutefois, une solution sécurisée complète sort du cadre de ce didacticiel et dépend de vos besoins en matière de sécurité.


4. Dans le fichier ToDoActivity.java, ajoutez la définition suivante pour la méthode `loadUserTokenCache`.

    	private boolean loadUserTokenCache(MobileServiceClient client)
	    {
	        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
    	    String userId = prefs.getString(USERIDPREF, "undefined"); 
	        if (userId == "undefined")
	            return false;
    	    String token = prefs.getString(TOKENPREF, "undefined"); 
    	    if (token == "undefined")
    	        return false;
        	    
    	    MobileServiceUser user = new MobileServiceUser(userId);
    	    user.setAuthenticationToken(token);
    	    client.setCurrentUser(user);
        	    
    	    return true;
	    }



5. Dans le fichier *ToDoActivity.java*, remplacez la méthode `authenticate` par la méthode suivante qui utilise un cache de jeton. Modifiez le fournisseur de connexion si vous souhaitez utiliser un compte autre que Microsoft.

		private void authenticate() {
			// We first try to load a token cache if one exists.
		    if (loadUserTokenCache(mClient))
		    {
		        createTable();
		    }
		    // If we failed to load a token cache, login and create a token cache
		    else
		    {
			    // Login using the Google provider.    
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
		
		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}   		
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();	
		    		}
		    	});
		    }
		}

6. Générez l'application et testez l'authentification à l'aide d'un compte valide. Exécutez ceci au moins deux fois. À la première exécution, vous devez recevoir une invite vous indiquant de vous connecter et de créer le cache de jeton. Ensuite, chaque exécution tente de charger le cache de jeton à des fins d'authentification et vous n'êtes plus invité à vous connecter.

<!---HONumber=August15_HO6-->