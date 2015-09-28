> [AZURE.IMPORTANT]Pour recevoir des notifications Push de Mobile Engagement, vous devez activer `Silent Remote Notifications` dans votre application. Vous devez ajouter la valeur de notification à distance dans le tableau UIBackgroundModes de votre fichier Info.plist.

1. Ouvrez le fichier `info.plist` du projet
2. Cliquez avec le bouton droit sur le premier élément de la liste (`Information Property List`), puis ajoutez une ligne

	![][1]

3. Sur la nouvelle ligne, entrez `Required background modes`

	![][2]

4. Cliquez sur la flèche gauche pour développer la ligne
5. Ajoutez la valeur suivante à l’élément 0 `App downloads content in response to push notifications`

	![][3]

Une fois la modification apportée, le code XML du fichier info.plist doit contenir la clé et la valeur ci-dessous :

    <key>UIBackgroundModes</key>
        <array>
            <string>remote-notification</string>
        </array>
    ...
    
6. Si vous utilisez Xcode 7 et iOS 9, vous devez effectuer les étapes supplémentaires suivantes : • Affectez à **Activer Bitcode** la valeur **Non** sous Cibles > Paramètres de build >, affectez à Activer Bitcode la valeur Oui ou Non. (Veillez à sélectionner TOUT dans la barre supérieure.) • Activez **Notifications push** dans Cibles > Nom de votre cible > Fonctionnalités.

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png
[2]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png
[3]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png

<!---HONumber=Sept15_HO3-->