#### Pour installer Update 1 à partir du Portail de gestion Azure

1. Dans le portail de gestion, accédez à la page **Appareils** et sélectionnez votre appareil.
 
2. Accédez à **Appareils** > **Configurer**.

3. Sous **Interfaces réseau**, recherchez l’interface réseau associée à une passerelle. Il s’agit d’une interface réseau autre que DATA 0.

4. Supprimez le paramètre de passerelle. Notez que comme les paramètres de passerelle sont requis sur une interface réseau compatible avec le cloud, vous devez désactiver l’accès de cette interface au cloud pour effacer le paramètre.

5. Répétez l’étape 4 pour toute autre interface réseau associée à une passerelle (sauf DATA 0).

6. Enregistrez la configuration modifiée.

7. Vous pouvez maintenant [utiliser le portail de gestion pour installer Update 1](#use-the-management-portal-to-install-update-1).

<!---HONumber=July15_HO2-->