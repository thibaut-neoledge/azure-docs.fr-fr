
Ensuite, vous devez changer la façon dont les notifications Push sont inscrites pour qu'un utilisateur soit authentifié avant de tenter une inscription.

1. Dans **QSAppDelegate.m**, supprimez complètement l'implémentation de **didFinishLaunchingWithOptions**.

2. Ouvrez **QSTodoListViewController.m**, puis ajoutez le code suivant à la fin de la méthode **viewDidLoad** :

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```

<!---HONumber=August15_HO6-->