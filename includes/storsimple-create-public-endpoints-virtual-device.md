#### Création de points de terminaison publics sur un appareil virtuel

1. Connectez-vous à la version classique du portail Azure.

- Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle utilisée comme appareil virtuel.

- Cliquez sur **Endpoints**. La page **Points de terminaison** répertorie tous les points de terminaison de la machine virtuelle.

- Cliquez sur **Add**. La boîte de dialogue **Add Endpoint** s’affiche. Cliquez sur la flèche pour continuer.

- Pour le **Nom**, tapez le nom de point de terminaison suivant : **WinRMHttps**.

- Pour le **Protocole**, indiquez **TCP**.

- Pour le **Port Public**, tapez les numéros de port que vous souhaitez utiliser pour la connexion.

- Pour le **Port privé**, indiquez **5986**.

- Cliquez sur la coche pour créer le point de terminaison.

Une fois le point de terminaison créé, vous pouvez afficher les détails correspondants pour déterminer l’adresse IP virtuelle publique. Enregistrez cette adresse.

<!---HONumber=AcomDC_0128_2016-->