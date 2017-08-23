La prise en charge de deux fonctionnalités de débogage est désormais disponible dans Azure : les supports de Console Output et Screenshot pour le modèle de déploiement Azure Virtual Machines Resource Manager. 

Lorsque vous importez votre propre image dans Azure, ou même lorsque vous démarrez une des images de plateforme, il peut y avoir de nombreuses raisons pour lesquelles une Machine Virtuelle passe en état non démarrable. Ces fonctionnalités vous permettent de facilement diagnostiquer des échecs de démarrage et de récupérer vos Machines virtuelles.

Dans le cas des Machines Virtuelles Linux, vous pouvez facilement afficher la sortie de votre journal de console à partir du Portail :

![Portail Azure](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
Toutefois, pour les Machines Virtuelles Windows et Linux, Azure vous permet également de voir une capture d’écran de la machine virtuelle à partir de l’hyperviseur :

![Erreur](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

Ces deux fonctionnalités sont prises en charge par les Machines Virtuelles Azure dans toutes les régions. Notez que des captures d’écran ainsi que des sorties peuvent prendre jusqu'à 10 minutes pour apparaître dans votre compte de stockage.

## <a name="common-boot-errors"></a>Erreurs de démarrage courantes

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Système d’exploitation introuvable.](https://support.microsoft.com/help/4010142)
- [Échec de démarrage ou INACCESSIBLE_BOOT_DEVICE.](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Activer la fonction de diagnostic sur une nouvelle machine virtuelle
1. Lorsque vous créez une Machine virtuelle à partir de la préversion du Portail, sélectionnez **Azure Resource Manager** dans la liste déroulante du modèle de déploiement :
 
    ![Gestionnaire de ressources](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. Configurez l’option de surveillance afin de sélectionner le compte de stockage dans lequel vous souhaitez placer ces fichiers de diagnostic.
 
    ![Créer une machine virtuelle](./media/virtual-machines-common-boot-diagnostics/screenshot4.jpg)

3. Si vous déployez à partir d’un modèle Azure Resource Manager, accédez à votre ressource de machine virtuelle et ajoutez la section de profil des diagnostics. Pensez à utiliser l’en-tête de version d’API « 2015-06-15 ».

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Le profil de diagnostics vous permet de sélectionner le compte de stockage dans lequel vous souhaitez placer ces journaux.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Pour déployer un exemple de machine virtuelle alors que les diagnostics de démarrage sont activés, consultez notre référentiel ici.

## <a name="update-an-existing-virtual-machine"></a>Mettre à jour une machine virtuelle existante ##

Pour activer les diagnostics de démarrage via le Portail, vous pouvez également mettre à jour une machine virtuelle existante à partir du Portail. Sélectionnez l’option de diagnostics de démarrage et enregistrez. Redémarrez la machine virtuelle pour appliquer les modifications.

![Mettre à jour une machine virtuelle existante](./media/virtual-machines-common-boot-diagnostics/screenshot5.png)

