Notre cache de jeton doit fonctionner dans une situation normale, mais que se passe-t-il si le jeton arrive à expiration ou est révoqué ? Nous devons être en mesure de détecter un jeton expiré. Le jeton peut expirer lorsque l'application n'est pas en cours d'exécution. Cela signifie alors que le cache de jeton n'est pas valide. Le jeton peut également expirer lors de l'exécution de l'application lors d'un appel effectué directement par l'application ou par la bibliothèque Mobile Services. S'affiche alors le code d'état HTTP 401 Non autorisé. Nous devons donc trouver un moyen de détecter cela et d'actualiser le jeton. Pour ce faire, nous utilisons un [ServiceFilter][ServiceFilter] à partir de la [bibliothèque cliente Android][bibliothèque cliente Android].

Dans cette section, vous allez définir un ServiceFilter qui détecte un code d'état HTTP 401 et déclenche une actualisation du jeton et du cache associé. En outre, ce ServiceFilter bloque les autres demandes sortantes lors de l'authentification afin qu'elles puissent utiliser le jeton actualisé.

1.  Dans Eclipse, ouvrez le fichier ToDoActivity.java et ajoutez les instructions import suivantes :

        import java.util.concurrent.atomic.AtomicBoolean;

2.  Dans le fichier ToDoActivity.java, ajoutez les membres suivants à la classe ToDoActivity.

        public boolean bAuthenticating = false;
        public final Object mAuthenticationLock = new Object();

    Ils permettront de synchroniser l'authentification de l'utilisateur. Nous voulons que l'authentification n'ait lieu qu'une seule fois. Les appels lors de l'authentification doivent attendre et utiliser le nouveau jeton provenant de l'authentification en cours.

3.  Dans le fichier ToDoActivity.java, ajoutez la méthode suivante à la classe ToDoActivity qui permettra de bloquer les appels sortants sur les autres threads lorsque l'authentification est en cours.

        /**
         * Detects if authentication is in progress and waits for it to complete. 
         * Returns true if authentication was detected as in progress. False otherwise.
         */
        public boolean detectAndWaitForAuthentication()
        {
            boolean detected = false;
            synchronized(mAuthenticationLock)
            {
                do
                {
                    if (bAuthenticating == true)
                        detected = true;
                    try
                    {
                        mAuthenticationLock.wait(1000);
                    }
                    catch(InterruptedException e)
                    {}
                }
                while(bAuthenticating == true);
            }
            if (bAuthenticating == true)
                return true;

            return detected;
        }

4.  Dans le fichier ToDoActivity.java, ajoutez la méthode suivante à la classe ToDoActivity. Cette méthode déclenche l'attente, puis met à jour le jeton sur les demandes sortantes lorsque l'authentification est terminée.

        /**
         * Waits for authentication to complete then adds or updates the token 
         * in the X-ZUMO-AUTH request header.
         * 
         * @param request
         *            The request that receives the updated token.
         */
        private void waitAndUpdateRequestToken(ServiceFilterRequest request)
        {
            MobileServiceUser user = null;
            if (detectAndWaitForAuthentication())
            {
                user = mClient.getCurrentUser();
                if (user != null)
                {
                    request.removeHeader("X-ZUMO-AUTH");
                    request.addHeader("X-ZUMO-AUTH", user.getAuthenticationToken());
                }
            }
        }

5.  Dans le fichier ToDoActivity.java, mettez à jour la méthode `authenticate` de la classe ToDoActivity afin qu'elle accepte un paramètre booléen pour autoriser le fait de forcer l'actualisation du jeton et du cache associé. Nous devons également informer les threads bloqués de la fin de l'authentification afin qu'ils puissent récupérer le nouveau jeton.

        /**
         * Authenticates with the desired login provider. Also caches the token. 
         * 
         * If a local token cache is detected, the token cache is used instead of an actual 
         * login unless bRefresh is set to true forcing a refresh.
         * 
         * @param bRefreshCache
         *            Indicates whether to force a token refresh. 
         */
        private void authenticate(boolean bRefreshCache) {

            bAuthenticating = true;

            if (bRefreshCache || !loadUserTokenCache(mClient))
            {
                // New login using the provider and update the token cache.
                mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
                        new UserAuthenticationCallback() {
                            @Override
                            public void onCompleted(MobileServiceUser user,
                                    Exception exception, ServiceFilterResponse response) {

                                synchronized(mAuthenticationLock)
                                {
                                    if (exception == null) {
                                        cacheUserToken(mClient.getCurrentUser());
                                        createTable();
                                    } else {
                                        createAndShowDialog(exception.getMessage(), "Login Error");
                                    }
                                    bAuthenticating = false;
                                    mAuthenticationLock.notifyAll();
                                }
                            }
                        });
            }
            else
            {
                // Other threads may be blocked waiting to be notified when 
                // authentication is complete.
                synchronized(mAuthenticationLock)
                {
                    bAuthenticating = false;
                    mAuthenticationLock.notifyAll();
                }
                createTable();
            }
        }   

6.  Dans le fichier ToDoActivity.java, ajoutez la définition suivante pour la classe `RefreshTokenCacheFilter` de la classe ToDoActivity :

        /**
         * The RefreshTokenCacheFilter class filters responses for HTTP status code 401. 
         * When 401 is encountered, the filter calls the authenticate method on the 
         * UI thread. Out going requests and retries are blocked during authentication. 
         * Once authentication is complete, the token cache is updated and 
         * any blocked request will receive the X-ZUMO-AUTH header added or updated to 
         * that request.   
         */
        private class RefreshTokenCacheFilter implements ServiceFilter {

            AtomicBoolean mAtomicAuthenticatingFlag = new AtomicBoolean();

            /**
             * The AuthenticationRetryFilterCallback class is a wrapper around the response 
             * callback that encapsulates the request and other information needed to enable 
             * a retry of the request when HTTP status code 401 is encountered. 
             */
            private class AuthenticationRetryFilterCallback implements ServiceFilterResponseCallback
            {
                // Data members used to retry the request during the response.
                ServiceFilterRequest mRequest;
                NextServiceFilterCallback mNextServiceFilterCallback;
                ServiceFilterResponseCallback mResponseCallback;

                public AuthenticationRetryFilterCallback(ServiceFilterRequest request, 
                        NextServiceFilterCallback nextServiceFilterCallback, 
                        ServiceFilterResponseCallback responseCallback)
                {
                    mRequest = request;
                    mNextServiceFilterCallback = nextServiceFilterCallback;
                    mResponseCallback = responseCallback;
                }

                @Override
                public void onResponse(ServiceFilterResponse response, Exception exception) {

                    // Filter out the 401 responses to update the token cache and 
                    // retry the request
                    if ((response != null) && (response.getStatus().getStatusCode() == 401))
                    { 
                        // Two simultaneous requests from independent threads could get HTTP 
                        // status 401. Protecting against that right here so multiple 
                        // authentication requests are not setup to run on the UI thread.
                        // We only want to authenticate once. Other requests should just wait 
                        // and retry with the new token.
                        if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                           
                        {
                            // Authenticate on UI thread
                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    // Force a token refresh during authentication.
                                    authenticate(true);
                                }
                            });
                        }

                        // Wait for authentication to complete then 
                        // update the token in the request.
                        waitAndUpdateRequestToken(this.mRequest);
                        mAtomicAuthenticatingFlag.set(false);                       

                        // Retry recursively with a new token as long as we get a 401.
                        mNextServiceFilterCallback.onNext(this.mRequest, this);
                    }

                    // Responses that do not have 401 status codes just pass through.
                    else if (this.mResponseCallback != null)  
                      mResponseCallback.onResponse(response, exception);
                }
            }


            @Override
            public void handleRequest(final ServiceFilterRequest request, 
                final NextServiceFilterCallback nextServiceFilterCallback,
                ServiceFilterResponseCallback responseCallback) {

                // In this example, if authentication is already in progress we block the request
                // until authentication is complete to avoid unnecessary authentications as 
                // a result of HTTP status code 401. 
                // If authentication was detected, add the token to the request.
                waitAndUpdateRequestToken(request);

                // Wrap the request in a callback object that will facilitate retries.
                AuthenticationRetryFilterCallback retryCallbackObject = 
                    new AuthenticationRetryFilterCallback(request, nextServiceFilterCallback,
                          responseCallback); 

                // Send the request down the filter chain.
                nextServiceFilterCallback.onNext(request, retryCallbackObject);         
            }
        }

    Ce filtre de service recherche dans chaque réponse le code d'état HTTP 401 Non autorisé. S'il rencontre ce code, une nouvelle demande de connexion pour obtenir un nouveau jeton est configurée dans le thread d'interface utilisateur. Les autres appels sont bloqués jusqu'à la fin de la connexion. Une fois le nouveau jeton obtenu, la demande qui a déclenché le code 401 et les appels bloqués font l'objet d'une nouvelle tentative avec le nouveau jeton.

7.  Dans le fichier ToDoActivity.java, mettez à jour la méthode `onCreate` comme suit :

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            setContentView(R.layout.activity_to_do);
            mProgressBar = (ProgressBar) findViewById(R.id.loadingProgressBar);

            // Initialize the progress bar
            mProgressBar.setVisibility(ProgressBar.GONE);

            try {
                // Create the Mobile Service Client instance, using the provided
                // Mobile Service URL and key
                mClient = new MobileServiceClient(
                        "https://<YOUR MOBILE SERVICE>.azure-mobile.net/",
                        "<YOUR MOBILE SERVICE KEY>", this)
                           .withFilter(new ProgressFilter())
                           .withFilter(new RefreshTokenCacheFilter());

                // Authenticate passing false to load the current token cache if available.
                authenticate(false);

            } catch (MalformedURLException e) {
                createAndShowDialog(new Exception("Error creating the Mobile Service. " +
                    "Verify the URL"), "Error");
            }
        }

    Dans ce code, le filtre RefreshTokenCacheFilter est utilisé en plus du filtre ProgressFilter. En outre, lors de l'application de la méthode `onCreate`, nous voulons charger le cache de jeton. La valeur false est alors transmise à la méthode `authenticate`.

  [ServiceFilter]: http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html
  [bibliothèque cliente Android]: http://dl.windowsazure.com/androiddocs/
