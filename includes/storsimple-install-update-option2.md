<!--author=SharS last changed: 9/17/15-->

#### Pour installer Update 1.2 à partir du portail de gestion Azure

1. Dans le portail de gestion, accédez à la page **Appareils** et sélectionnez votre appareil.
 
2. Accédez à **Appareils** > **Configurer**.

3. Sous **Interfaces réseau**, vérifiez d’abord que vous disposez d’au moins une interface réseau compatible iSCSI. Ensuite, recherchez l’interface réseau (autre que DATA 0) à laquelle est affectée une passerelle.

4. Désactivez l’interface réseau à laquelle est affectée une passerelle et enregistrez la nouvelle configuration. Les paramètres d’interface réseau étant conservés, le portail retrouve les paramètres d’origine quand vous réactivez cette interface réseau ultérieurement.

7. Vous pouvez maintenant [utiliser le Portail de gestion pour installer Update 1.2](#use-the-management-portal-to-install-update-1). Suivez les instructions à partir de l’étape 3 de cette procédure. Après avoir installé toutes les mises à jour, vous pouvez réactiver l’interface réseau que vous avez désactivée.

<!---HONumber=Oct15_HO3-->