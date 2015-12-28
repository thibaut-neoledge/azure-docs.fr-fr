#### Arrêt et démarrage d’un appareil virtuel
Pour arrêter un appareil virtuel, cliquez sur son nom, puis sur **Arrêter**. Lorsque l’appareil virtuel est en train de s’arrêter, son état est **Arrêt en cours**. Une fois l’appareil virtuel arrêté, son état est **Arrêté**.

Utilisez les applets de commande suivantes pour arrêter et démarrer un appareil virtuel.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`


`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    
#### Redémarrage d’un appareil virtuel

Lorsqu’un appareil virtuel est en cours d’exécution et que vous souhaitez le redémarrer, cliquez sur son nom, puis sur **Redémarrer**. Lorsque l’appareil virtuel est en cours de redémarrage, son état est **Redémarrage en cours**. Lorsque l’appareil virtuel est prêt à être utilisé, son état est **En cours d’exécution**.

Utilisez les applets de commande suivantes pour redémarrer un appareil virtuel.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

<!---HONumber=AcomDC_1217_2015-->