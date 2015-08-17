Les transmissions de type Push sont déjà activées dans le projet de serveur téléchargé à partir du portail.

Dans votre projet ASP.NET, vous devez voir les éléments suivants :

* Le package NuGet `Microsoft.Azure.Mobile.Server.Notifications` est installé.

* Dans WebApiConfig.cs, la méthode `UseDefaultConfiguration()` est appelée sur l'objet MobileAppConfiguration. Cela appelle alors la méthode d'extension `AddPushNotifications()` fournie par le package NuGet ci-dessus.

<!---HONumber=August15_HO6-->