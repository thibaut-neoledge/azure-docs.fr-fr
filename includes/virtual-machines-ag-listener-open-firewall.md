Au cours de cette étape, vous allez créer une règle de pare-feu pour ouvrir le port de la sonde du point de terminaison à charge équilibrée (59999, tel que cela est indiqué précédemment), et une autre règle pour ouvrir le port de l'écouteur du groupe de disponibilité. Étant donné que vous avez créé le point de terminaison à charge équilibrée sur chacune des machines virtuelles Azure qui contiennent des réplicas du groupe de disponibilité, vous devez ouvrir le port de la sonde et le port de l'écouteur sur les machines virtuelles Azure respectives.

1. Sur les machines virtuelles hébergeant les réplicas, démarrez le **Pare-feu Windows avec fonctions de sécurité avancées**.
2. Cliquez avec le bouton droit sur **Règles de trafic entrant**, puis cliquez sur **Nouvelle règle**.
3. Sur la page **Type de règle**, sélectionnez **Port**, puis cliquez sur **Suivant**.
4. Sur la page **Protocole et ports**, sélectionnez **TCP** et tapez **59999** dans la zone **Ports locaux spécifiques**. Cliquez ensuite sur **Suivant**.
5. Dans la page **Action**, sélectionnez **Autoriser la connexion** et cliquez sur **Suivant**.
6. Dans la page **Profil**, acceptez les paramètres par défaut et cliquez sur **Suivant**.
7. Sur la page **Nom**, spécifiez un nom pour la règle, comme **Port de la sonde d’écouteur toujours actif** dans la zone de texte **Nom**, puis cliquez sur **Terminer**.
8. Répétez les étapes ci-dessus pour le port de l'écouteur du groupe de disponibilité (comme spécifié précédemment à l'aide du paramètre $EndpointPort du script), puis spécifiez un nom de règle approprié, tel que **Port de l'écouteur toujours actif**.



<!--HONumber=Nov16_HO5-->


