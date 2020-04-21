<!-- markdownlint-disable MD002 MD041 -->

Erstellen Sie zunächst ein neues SWIFT-Projekt.

1. Öffnen Sie Xcode. Wählen Sie im Menü **Datei** die Option **neu**und dann **Projekt**aus.
1. Wählen Sie die Vorlage **einzelne Ansicht-App** aus, und wählen Sie **weiter**aus.

    ![Screenshot des Dialogfelds "Xcode-Vorlagenauswahl"](./images/xcode-select-template.png)

1. Legen Sie den **Produktnamen** auf `GraphTutorial` und die **Sprache** auf **Objective-C**fest.
1. Füllen Sie die restlichen Felder aus, und wählen Sie **weiter**aus.
1. Wählen Sie einen Speicherort für das Projekt aus, und wählen Sie **Erstellen**aus.

## <a name="install-dependencies"></a>Installieren von Abhängigkeiten

Installieren Sie vor dem Verschieben einige zusätzliche Abhängigkeiten, die Sie später verwenden werden.

- [Microsoft Authentication Library (MSAL) für IOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) zur Authentifizierung mit Azure AD.
- [Microsoft Graph-SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) zum tätigen von Anrufen an Microsoft Graph.
- [Microsoft Graph-Modell SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) für stark typisierte Objekte, die Microsoft Graph-Ressourcen wie Benutzer oder Ereignisse darstellen.

1. Beenden Sie Xcode.
1. Öffnen Sie Terminal, und ändern Sie das Verzeichnis in den Speicherort Ihres **GraphTutorial** -Projekts.
1. Führen Sie den folgenden Befehl aus, um eine Podfile zu erstellen.

    ```Shell
    pod init
    ```

1. Öffnen Sie die Podfile, und fügen Sie die folgenden Zeilen `use_frameworks!` unmittelbar nach der Zeile hinzu.

    ```Ruby
    pod 'MSAL', '~> 1.1.1'
    pod 'MSGraphClientSDK', ' ~> 1.0.0'
    pod 'MSGraphClientModels', '~> 1.3.0'
    ```

1. Speichern Sie die Podfile, und führen Sie dann den folgenden Befehl aus, um die Abhängigkeiten zu installieren.

    ```Shell
    pod install
    ```

1. Nachdem der Befehl abgeschlossen ist, öffnen Sie das neu erstellte **GraphTutorial. xcworkspace.** in Xcode.

## <a name="design-the-app"></a>Entwerfen der App

In diesem Abschnitt werden die Ansichten für die App erstellt: eine Anmeldeseite, ein Registerkartenleisten-Navigator, eine Willkommensseite und eine Kalender Seite. Außerdem erstellen Sie ein Aktivitäts Indikator-Overlay.

### <a name="create-sign-in-page"></a>Anmeldeseite erstellen

1. Erweitern Sie den Ordner **GraphTutorial** in Xcode, und wählen Sie dann die Datei **ViewController. m** aus.
1. Ändern Sie `SignInViewController.m`im **Datei Inspektor**den **Namen** der Datei in.

    ![Screenshot des Datei Inspektors](./images/rename-file.png)

1. Öffnen Sie **SignInViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.

    ```objc
    #import "SignInViewController.h"

    @interface SignInViewController ()

    @end

    @implementation SignInViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.
    }

    - (IBAction)signIn {
        [self performSegueWithIdentifier: @"userSignedIn" sender: nil];
    }
    @end
    ```

1. Wählen Sie die Datei **ViewController. h** aus.
1. Ändern Sie `SignInViewController.h`im **Datei Inspektor**den **Namen** der Datei in.
1. Öffnen Sie **SignInViewController. h** , und ändern Sie `ViewController` alle `SignInViewController`Instanzen von in.

1. Öffnen Sie die Datei **Main. Storyboard** .
1. Erweitern Sie **View Controller Scene**, und wählen Sie dann **View Controller**aus.

    ![Screenshot von Xcode mit ausgewähltem Ansichts Controller](./images/storyboard-select-view-controller.png)

1. Wählen Sie den **Identitäts Inspektor**aus, und ändern Sie dann das Dropdownmenü **Klasse** in **SignInViewController**.

    ![Screenshot des Identitäts Inspektors](./images/change-class.png)

1. Wählen Sie die **Bibliothek**aus, und ziehen Sie dann eine **Schaltfläche** auf den **Anmelde Ansichts Controller**.

    ![Ein Screenshot der Bibliothek in Xcode](./images/add-button-to-view.png)

1. Wählen Sie mit ausgewählter Schaltfläche den **Attributes Inspector** aus, und ändern Sie den `Sign In` **Titel** der Schaltfläche in.

    ![Screenshot des titelfelds im Attributes inspector in Xcode](./images/set-button-title.png)

1. Klicken Sie auf die Schaltfläche ausgewählt, und wählen Sie am unteren Rand des Storyboards die Schaltfläche **Ausrichten** aus. Wählen Sie sowohl **horizontal im Container** als auch **vertikal im Container** Constraints, lassen Sie Ihre Werte als 0, und wählen Sie dann **2 Abhängigkeiten hinzufügen**aus.

    ![Ein Screenshot der Einstellungen für die Ausrichtungs Einschränkungen in Xcode](./images/add-alignment-constraints.png)

1. Wählen Sie den **Controller anmelden**aus, und wählen Sie dann den **Verbindungs Inspektor**aus.
1. Ziehen Sie unter **empfangene Aktionen**den ungefüllten Kreis neben **SignIn** auf die Schaltfläche. Wählen Sie im Popup Menü die Option nach **oben innen berühren** aus.

    ![Ein Screenshot des Ziehens der SignIn-Aktion auf die Schaltfläche in Xcode](./images/connect-sign-in-button.png)

### <a name="create-tab-bar"></a>Registerkartenleiste erstellen

1. Wählen Sie die **Bibliothek**aus, und ziehen Sie dann einen **Registerkartenleisten-Controller** auf das Storyboard.
1. Wählen Sie den **Controller anmelden**aus, und wählen Sie dann den **Verbindungs Inspektor**aus.
1. Ziehen Sie unter **ausgelöste Segues**den ungefüllten Kreis neben **manuell** auf den **Registerkartenleisten-Controller** auf dem Storyboard. Wählen Sie im Popup Menü **Modal** aus.

    ![Ein Screenshot des Ziehens einer manuellen segue auf den neuen Registerkartenleisten-Controller in Xcode](./images/add-segue.png)

1. Wählen Sie das soeben hinzugefügte segue aus, und wählen Sie dann den **Attributes Inspector**aus. Legen Sie **Identifier** das Feldbezeichner `userSignedIn`auf fest, und legen Sie die **Präsentation** auf **Vollbildmodus**fest.

    ![Screenshot des Felds "Bezeichner" im Attributes inspector in Xcode](./images/set-segue-identifier.png)

1. Wählen Sie die **Szene Element 1**aus, und wählen Sie dann den **Verbindungs Inspektor**aus.
1. Ziehen Sie unter **ausgelöste Segues**den ungefüllten Kreis neben **manuell** auf den **Anmelde Ansichts Controller** auf dem Storyboard. Wählen Sie im Popup Menü **Modal** aus.
1. Wählen Sie das soeben hinzugefügte segue aus, und wählen Sie dann den **Attributes Inspector**aus. Legen Sie **Identifier** das Feldbezeichner `userSignedOut`auf fest, und legen Sie die **Präsentation** auf **Vollbildmodus**fest.

### <a name="create-welcome-page"></a>Willkommensseite erstellen

1. Wählen Sie die Datei **Assets. xcassets** aus.
1. Wählen Sie im Menü **Editor** die Option **Objekte hinzufügen**und dann **neuer Bildsatz**aus.
1. Wählen Sie das neue **Image** -Objekt aus, und verwenden Sie den **Attribut Inspektor** , um seinen **Namen** auf `DefaultUserPhoto`festzulegen.
1. Fügen Sie ein beliebiges Bild hinzu, das Sie als Standardbenutzer Profilfoto dienen möchten.

    ![Screenshot der Ansicht "ImageSet Asset" in Xcode](./images/add-default-user-photo.png)

1. Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `WelcomeViewController`Ordner mit dem Namen. Wählen Sie **ulviewcontroller** in der unter **Klasse von Field aus** .
1. Öffnen Sie **WelcomeViewController. h** , und fügen Sie den folgenden `@interface` Code innerhalb der Deklaration hinzu.

    ```objc
    @property (nonatomic) IBOutlet UIImageView *userProfilePhoto;
    @property (nonatomic) IBOutlet UILabel *userDisplayName;
    @property (nonatomic) IBOutlet UILabel *userEmail;
    ```

1. Öffnen Sie **WelcomeViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.

    ```objc
    #import "WelcomeViewController.h"

    @interface WelcomeViewController ()

    @end

    @implementation WelcomeViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        // TEMPORARY
        self.userProfilePhoto.image = [UIImage imageNamed:@"DefaultUserPhoto"];
        self.userDisplayName.text = @"Default User";
        [self.userDisplayName sizeToFit];
        self.userEmail.text = @"default@contoso.com";
        [self.userEmail sizeToFit];
    }

    - (IBAction)signOut {
        [self performSegueWithIdentifier: @"userSignedOut" sender: nil];
    }

    @end
    ```

1. Öffnen Sie **Main. Storyboard**. Wählen Sie die **Szene Element 1**aus, und wählen Sie dann den **Identitäts Inspektor**aus. Ändern Sie den Wert der **Klasse** in **WelcomeViewController**.
1. Fügen Sie mithilfe der **Bibliothek**die folgenden Elemente zur **Szene Element 1**hinzu.

    - Eine **Bildansicht**
    - Zwei **Beschriftungen**
    - Eine **Schaltfläche**

1. Stellen Sie über den **Connections Inspector**folgende Verbindungen her.

    - Verknüpfen Sie die **User** -Steckdose mit dem ersten Etikett.
    - Verknüpfen Sie die **userEmail** -Steckdose mit der zweiten Bezeichnung.
    - Verknüpfen Sie die **userProfilePhoto** -Steckdose mit der Bildansicht.
    - Verknüpfen Sie die Aktion " **abgemeldete** empfangen" mit dem Touch der Schaltfläche nach **innen**.

1. Wählen Sie die Ansicht Bild aus, und wählen Sie dann den **Größen Inspektor**aus.
1. Legen Sie die **Breite** und **Höhe** auf 196 fest.
1. Verwenden Sie die Schaltfläche **Ausrichten** , um die Constraint **horizontal in Container** mit dem Wert 0 hinzuzufügen.
1. Verwenden Sie die Schaltfläche **neue Einschränkungen hinzufügen** (neben der Schaltfläche **Ausrichten** ), um die folgenden Einschränkungen hinzuzufügen:

    - Oben ausrichten an: sicherer Bereich, Wert: 0
    - Untere Fläche für: Benutzeranzeige Name, Wert: Standard
    - Höhe, Wert: 196
    - Breite, Wert: 196

    ![Ein Screenshot der neuen Einschränkungseinstellungen in Xcode](./images/add-new-constraints.png)

1. Wählen Sie die erste Bezeichnung aus, und verwenden Sie dann die Schaltfläche **Ausrichten** , um die Einschränkung **horizontal in Container** mit dem Wert 0 hinzuzufügen.
1. Verwenden Sie die Schaltfläche **neue Einschränkungen hinzufügen** , um die folgenden Einschränkungen hinzuzufügen:

    - Oberer Bereich für: Benutzerprofil Foto, Wert: Standard
    - Bottom Space to: Benutzer-e-Mail, Wert: Standard

1. Wählen Sie die zweite Bezeichnung aus, und wählen Sie dann den **Attribut Inspektor**aus.
1. Ändern Sie die **Farbe** in **dunkelgrau**und ändern Sie die **Schriftart** in **System 12,0**.
1. Verwenden Sie die Schaltfläche **Ausrichten** , um die Constraint **horizontal in Container** mit dem Wert 0 hinzuzufügen.
1. Verwenden Sie die Schaltfläche **neue Einschränkungen hinzufügen** , um die folgenden Einschränkungen hinzuzufügen:

    - Oberer Speicherplatz für: Benutzeranzeige Name, Wert: Standard
    - Bottom Space to: abmelden, Wert: 14

1. Wählen Sie die Schaltfläche aus, und wählen Sie dann den **Attribut Inspektor**aus.
1. Ändern Sie **Title** den Titel `Sign Out`in.
1. Verwenden Sie die Schaltfläche **Ausrichten** , um die Constraint **horizontal in Container** mit dem Wert 0 hinzuzufügen.
1. Verwenden Sie die Schaltfläche **neue Einschränkungen hinzufügen** , um die folgenden Einschränkungen hinzuzufügen:

    - Oberer Bereich für: Benutzer-e-Mail, Wert: 14

1. Wählen Sie das Element der Registerkartenleiste am unteren Rand der Szene aus, und wählen Sie dann den **Attributes Inspector**aus. Ändern Sie **Title** den Titel `Me`in.
1. Wählen Sie im Menü **Editor** die Option **Probleme beim automatischen Layout auflösen**aus, und wählen Sie dann **fehlende Abhängigkeiten** unterhalb **aller Ansichten im Begrüßungs Ansichts Controller**hinzufügen aus.

Die Begrüßungs Szene sollte wie folgt aussehen, wenn Sie fertig sind.

![Screenshot des Layouts für die Begrüßungs Szene](./images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a>Seite "Kalender erstellen"

1. Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `CalendarViewController`Ordner mit dem Namen. Wählen Sie **ulviewcontroller** in der unter **Klasse von Field aus** .
1. Öffnen Sie **CalendarViewController. h** , und fügen Sie den folgenden `@interface` Code innerhalb der Deklaration hinzu.

    ```objc
    @property (nonatomic) IBOutlet UITextView *calendarJSON;
    ```

1. Öffnen Sie **CalendarViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.

    ```objc
    #import "CalendarViewController.h"

    @interface CalendarViewController ()

    @end

    @implementation CalendarViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        // TEMPORARY
        self.calendarJSON.text = @"Calendar";
        [self.calendarJSON sizeToFit];
    }

    @end
    ```

1. Öffnen Sie **Main. Storyboard**. Wählen Sie die **Szene Element 2**aus, und wählen Sie dann den **Identitäts Inspektor**aus. Ändern Sie den Wert der **Klasse** in **CalendarViewController**.
1. Fügen Sie mithilfe der **Bibliothek**eine **Text Ansicht** zur **Szene "Element 2**" hinzu.
1. Wählen Sie die soeben hinzugefügte Textansicht aus. Wählen Sie im **Editor** **Einbetten in**und dann **Bildlaufansicht**aus.
1. Verbinden Sie die **calendarJSON** -Steckdose mithilfe des **Verbindungs Inspektors**mit der Textansicht.
1. Wählen Sie das Element der Registerkartenleiste am unteren Rand der Szene aus, und wählen Sie dann den **Attributes Inspector**aus. Ändern Sie **Title** den Titel `Calendar`in.
1. Wählen Sie im Menü **Editor** die Option **Probleme beim automatischen Layout auflösen**aus, und wählen Sie dann **fehlende Abhängigkeiten** unterhalb **aller Ansichten im Begrüßungs Ansichts Controller**hinzufügen aus.

Die Kalender Szene sollte wie folgt aussehen, wenn Sie fertig sind.

![Screenshot des Kalender Szenen Layouts](./images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a>Aktivitäts Indikator erstellen

1. Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `SpinnerViewController`Ordner mit dem Namen. Wählen Sie **ulviewcontroller** in der unter **Klasse von Field aus** .
1. Öffnen Sie **SpinnerViewController. h** , und fügen Sie den folgenden `@interface` Code innerhalb der Deklaration hinzu.

    ```objc
    - (void) startWithContainer:(UIViewController*) container;
    - (void) stop;
    ```

1. Öffnen Sie **SpinnerViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/SpinnerViewController.m" id="SpinnerViewSnippet":::

## <a name="test-the-app"></a>Testen der App

Speichern Sie Ihre Änderungen, und starten Sie die app. Sie sollten mit den Schaltflächen **Anmelden** und **Abmelden** und der Registerkartenleiste zwischen den Bildschirmen navigieren können.

![Screenshots der Anwendung](./images/app-screens.png)
