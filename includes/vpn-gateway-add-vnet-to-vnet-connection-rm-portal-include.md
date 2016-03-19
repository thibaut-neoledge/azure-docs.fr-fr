Lors de l'ajout d'une connexion de réseau virtuel à réseau virtuel, vérifiez que vos deux réseaux virtuels ont une passerelle de réseau virtuel et que les plages d'adresses de vos réseaux virtuels ne se chevauchent pas.

1. Dans le volet **Réseaux virtuels**, recherchez votre réseau virtuel, puis cliquez pour ouvrir le volet. Dans le volet, votre passerelle est répertoriée comme un *Périphérique connecté*. Vous pouvez également configurer les paramètres directement à partir de votre passerelle de réseau virtuel sans développer au préalable le réseau virtuel.
2. Dans les paramètres de passerelle de réseau virtuel, cliquez sur **Connexions**, puis sur **Ajouter**.
3. **Nommez** votre connexion. 
4. Pour le **Type de connexion**, sélectionnez **Réseau virtuel à réseau virtuel**.
5. Pour **Passerelle de réseau virtuel**, la valeur est fixe, car vous vous connectez à partir de cette passerelle.
6. Pour **Seconde passerelle de réseau virtuel**, sélectionnez la passerelle à laquelle vous souhaitez créer une connexion à partir de cette passerelle.
8. Les valeurs restantes pour **Abonnement**, **Groupe de ressources** et **Emplacement** sont fixes.
9. Cliquez sur **OK** pour créer votre connexion. Le message *Création de la connexion* clignote à l'écran.
10. Une fois la connexion terminée, elle s'affiche dans le volet **Connexions** de votre passerelle.

<!---HONumber=AcomDC_0107_2016-->