L’authentification est déjà activée dans le projet de serveur téléchargé à partir du portail.

Dans votre projet ASP.NET, vous devez voir les éléments suivants :

* Le package NuGet `Microsoft.Azure.Mobile.Server.Authentication` est installé.

* Dans WebApiConfig.cs, la méthode `UseDefaultConfiguration()` est appelée sur l'objet MobileAppConfiguration. Cela appelle alors la méthode d'extension `AddAppServiceAuthentication()` fournie par le package NuGet ci-dessus. Cela enregistre également un intergiciel (middleware) OWIN nécessaire pour l'authentification, en appelant `app.UseAppServiceAuthentication()` lors du démarrage d’OWIN.

<!---HONumber=August15_HO6-->