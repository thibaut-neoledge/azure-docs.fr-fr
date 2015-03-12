
1. Cliquez avec le bouton droit sur le projet Windows Store, cliquez sur **Définir comme projet de démarrage**, puis appuyez sur la touche F5 pour exécuter l'application Windows Store.
	
	Une fois l'application démarrée, l'appareil est enregistré pour les notifications Push.

2. Arrêtez l'application Windows Store et répétez l'étape précédente pour exécuter l'application Windows Phone Store.

	À cette étape, les deux appareils sont enregistrés pour recevoir des notifications Push.

3. Exécutez à nouveau l'application Windows Store, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push1.png)

   	Notez qu'une fois l'insertion terminée, l'application Windows Store et l'application Windows Phone reçoivent toutes les deux une notification Push de WNS.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push2.png)

	La notification est affichée sur le Windows Phone, même si l'application n'est pas en cours d'exécution.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push5-wp8.png)


\<!--HONumber=42-->
