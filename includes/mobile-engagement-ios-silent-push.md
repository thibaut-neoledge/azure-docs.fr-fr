> [AZURE.IMPORTANT]Pour recevoir des notifications Push de Mobile Engagement, vous devez activer `Silent Remote Notifications` dans votre application. Vous devez ajouter la valeur de notification à distance dans le tableau UIBackgroundModes de votre fichier Info.plist.

1. Ouvrez le fichier `info.plist` du projet
2. Cliquez avec le bouton droit sur le premier élément de la liste (`Information Property List`), puis ajoutez une ligne

	![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)

3. Sur la nouvelle ligne, entrez `Required background modes`

	![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)

4. Cliquez sur la flèche gauche pour développer la ligne
5. Ajoutez la valeur suivante à l’élément 0 `App downloads content in response to push notifications`

	![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)

6. Une fois la modification apportée, le code XML du fichier info.plist doit contenir la clé et la valeur ci-dessous :

	    <key>UIBackgroundModes</key>
	    <array>
	    <string>remote-notification</string>
	    </array>
	    ...
    
7. Si vous utilisez **Xcode 7** et **iOS 9**, vous devez effectuer les étapes supplémentaires suivantes :
	- Définissez **Activer Bitcode** sur **Non** sous Cibles > Paramètres de génération. Assurez-vous de sélectionner TOUT en haut de la barre. 
	- Activer **Notifications Push** dans Cibles > Votre nom cible > Fonctionnalités.

<!---HONumber=Oct15_HO1-->