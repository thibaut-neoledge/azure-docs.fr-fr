Après la propagation des enregistrements de votre nom de domaine, vous devez être en mesure d’utiliser votre navigateur pour vérifier que votre domaine personnalisé peut être utilisé pour accéder à votre application web dans Azure App Service.

> [AZURE.NOTE]La propagation de l'enregistrement CNAME dans le système DNS peut prendre du temps. Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l’enregistrement CNAME est disponible.

Si vous n’avez pas déjà ajouté votre application web en tant que point de terminaison de Traffic Manager, vous devez effectuer cette action pour faire fonctionner la résolution de nom, car le nom de domaine personnalisé assure l’acheminement vers Traffic Manager. Traffic Manager effectue ensuite l’acheminement vers votre application web. Utilisez les informations de la rubrique [Ajout ou suppression de points de terminaison](../traffic-manager/traffic-manager-endpoints.md) pour ajouter votre application web en tant que point de terminaison dans votre profil Traffic Manager.

> [AZURE.NOTE]Si votre application web n’est pas répertoriée lors de l’ajout d’un point de terminaison, vérifiez qu’elle est configurée pour le mode de plan App Service **Standard**. Vous devez utiliser le mode **Standard** pour votre application web si vous voulez utiliser Traffic Manager.

1. Dans votre navigateur, ouvrez le [portail Azure](https://portal.azure.com).

2. Sous l’onglet **Web Apps**, cliquez sur le nom de votre application web et sélectionnez **Paramètres**, puis **Domaines personnalisés et SSL**.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Dans le panneau **Domaines personnalisés et SSL**, cliquez sur **Apporter des domaines externes**.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. Utilisez les zones de texte **NOMS DE DOMAINE** pour saisir le nom de domaine Traffic Manager (contoso.trafficmanager.net) à associer à cette application web.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. Cliquez sur **Enregistrer** pour enregistrer la configuration du nom de domaine.

	Une fois la configuration terminée, le nom de domaine personnalisé est répertorié dans la section **Noms de domaine** de votre application web.

À ce stade, vous devez pouvoir entrer le nom de domaine Traffic Manager (contoso.trafficmanager.net) dans votre navigateur et vérifier que vous êtes bien dirigé vers votre application web.

<!---HONumber=August15_HO9-->