
### Étape 1 : autoriser un appareil à modifier la clé de chiffrement des données du service dans le portail de gestion

En règle générale, l’administrateur de l’appareil demande que l’administrateur du service autorise un appareil à modifier les clés de chiffrement des données du service. Ensuite, l’administrateur du service autorise l’appareil à modifier la clé.

Cette étape s’effectue dans le portail de gestion. L’administrateur du service peut sélectionner un appareil à partir d’une liste des appareils pouvant être autorisés. Après cela, l’appareil est autorisé à démarrer le processus de modification de la clé de chiffrement des données du service.

#### Quels appareils peuvent être autorisés à modifier les clés de chiffrement des données du service ?

Un appareil doit respecter les critères suivants avant d’être autorisé à démarrer des modifications de clé de chiffrement des données du service :

- L’appareil doit être en ligne pour bénéficier de l’autorisation de modification de clé de chiffrement des données du service.

- Si le changement de clé n’a pas été démarré, vous devez attendre 30 minutes avant d’autoriser à nouveau le même appareil.

- Vous pouvez autoriser un autre appareil, sous réserve que la modification de la clé n’a pas été démarrée par l’appareil précédemment autorisé. Une fois le nouvel appareil autorisé, l’ancien appareil ne peut plus démarrer la modification.

- Vous ne pouvez pas autoriser un appareil alors que la substitution de la clé de chiffrement des données du service est en cours.

- Vous pouvez autoriser un appareil lorsque certains appareils inscrits auprès du service ont substitué le chiffrement tandis que d’autres ne l’ont pas fait. Dans ce cas, les appareils pouvant être autorisés sont ceux qui ont terminé la modification de la clé de chiffrement des données du service.

> [AZURE.NOTE]Dans le portail de gestion, les appareils virtuels StorSimple ne sont pas affichés dans la liste des appareils pouvant être autorisés à démarrer la modification de clé.

Procédez comme suit pour sélectionner et autoriser un appareil à démarrer la modification de clé de chiffrement des données du service.

#### Pour autoriser un appareil à modifier la clé

1. Dans la page Tableau de bord du service, cliquez sur **Modifier la clé de chiffrement de données du service**.

    ![Modification de la clé de chiffrement du service](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)

2. Dans la boîte de dialogue **Modifier la clé de chiffrement de données du service**, sélectionnez et autorisez un appareil à démarrer la modification de clé de chiffrement des données du service. La liste déroulante contient tous les appareils pouvant être autorisés.

3. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png).

### Étape 2 : utiliser Windows PowerShell pour StorSimple pour démarrer la modification de la clé de chiffrement des données du service

Cette étape s’effectue dans l’interface Windows PowerShell pour StorSimple, sur l’appareil StorSimple autorisé.

> [AZURE.NOTE]Aucune opération ne peut être effectuée dans le portail de gestion de votre service StorSimple Manager avant la fin de la substitution de la clé.

Si vous utilisez la console série de l’appareil pour vous connecter à l’interface Windows PowerShell, procédez comme suit.

#### Pour démarrer la modification de la clé de chiffrement des données du service

1. Sélectionnez Option 1 pour vous connecter avec un accès complet.

2.  À l’invite de commandes, tapez :

     `Invoke-HcsmServiceDataEncryptionKeyChange`

3. Une fois l’applet de commande terminée, vous obtenez une nouvelle clé de chiffrement des données du service. Copiez et enregistrez cette clé pour l’utiliser lors de l’étape 3 de cette procédure. Cette clé permet de mettre à jour tous les appareils restants inscrits auprès du service StorSimple Manager.

    > [AZURE.NOTE]Ce processus doit être démarré dans les quatre heures suivant l’autorisation d’un appareil StorSimple.

    Cette nouvelle clé est ensuite envoyée au service pour être transmise à tous les appareils inscrits auprès du service. Une alerte s’affiche alors sur le tableau de bord du service. Le service désactive toutes les opérations sur les appareils inscrits et l’administrateur de l’appareil doit alors mettre à jour la clé de chiffrement des données du service sur les autres appareils. Toutefois, les E/S (hôtes envoyant des données vers le cloud) ne sont pas interrompues.

    Si vous avez un seul appareil inscrit auprès de votre service, le processus de substitution est maintenant terminé et vous pouvez ignorer l’étape suivante. Si vous avez plusieurs appareils inscrits auprès de votre service, passez à l’étape 3.

### Étape 3 : mettre à jour la clé de chiffrement sur les autres appareils StorSimple

Si vous avez plusieurs appareils inscrits auprès du service StorSimple Manager, cette procédure s’effectue dans l’interface Windows PowerShell de votre appareil StorSimple. Utilisez la clé obtenue à l’étape 2 avec Windows PowerShell pour StorSimple afin de démarrer la modification de la clé de chiffrement des données du service pour mettre à jour tous les appareils StorSimple restant inscrits auprès du service StorSimple Manager.

Procédez comme suit pour mettre à jour le chiffrement des données du service sur votre appareil.

#### Pour mettre à jour la clé de chiffrement des données du service

1. Utilisez Windows PowerShell pour StorSimple pour vous connecter à la console. Sélectionnez Option 1 pour vous connecter avec un accès complet.

2.  À l’invite de commandes, tapez :

    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`

3. Entrez la clé de chiffrement des données du service obtenue lors de l’[Étape 2 : utiliser Windows PowerShell pour StorSimple pour démarrer la modification de la clé de chiffrement des données du service](##to-initiate-the-service-data-encryption-key-change).

<!---HONumber=August15_HO6-->