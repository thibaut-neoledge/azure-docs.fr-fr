Même s'il est possible de coller un URI MongoLab dans votre code, il est recommandé de le configurer dans l'environnement pour faciliter la gestion. De cette façon, si l'URI change, vous pouvez le modifier via le portail de gestion Azure sans devoir modifier le code.


1. Dans le portail Azure, sélectionnez **Sites Web**.
1. Cliquez sur le nom du site web dans la liste.  
![WebSiteEntry][entry-website]  
Le tableau de bord du site Web s'affiche.

1. Cliquez sur **configurer** dans la barre de menus.  
![WebSiteDashboardConfig][focus-mongolab-websitedashboard-config]

1. Faites défiler jusqu'à la section Chaînes de connexion.  
![WebSiteConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Dans **Nom**, saisissez MONGOLAB_URI.
1. Dans **Value**, collez la chaîne de connexion obtenue dans la section précédente.
1. Sélectionnez **Personnalisé** dans la liste déroulante **Type** (au lieu de la valeur par défaut, **SQLAzure**).
1. Cliquez sur **Enregistrer** dans la barre d'outils.  
![SaveWebSite][button-website-save]

**Remarque :** Azure ajoute le préfixe **CUSTOMCONNSTR\_** à cette variable, ce qui explique que le code ci-dessus fasse référence à **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
<!--HONumber=42-->
