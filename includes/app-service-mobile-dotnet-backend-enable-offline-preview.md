Les fonctionnalités de données sont déjà activées dans le projet de serveur téléchargé à partir du portail.

Dans votre projet ASP.NET, vous devez voir les éléments suivants :

* Les packages NuGet `Microsoft.Azure.Mobile.Server.Tables` et `Microsoft.Azure.Mobile.Server.Entity` sont installés.

* Dans WebApiConfig.cs, la méthode `UseDefaultConfiguration()` est appelée sur l'objet MobileAppConfiguration. Cela appelle alors la méthode d'extension `AddTablesWithEntityFramework()` fournie par le package NuGet ci-dessus.

<!---HONumber=July15_HO4-->