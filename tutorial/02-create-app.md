<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="aaba1-101">Erstellen Sie zunächst ein neues SWIFT-Projekt.</span><span class="sxs-lookup"><span data-stu-id="aaba1-101">Begin by creating a new Swift project.</span></span>

1. <span data-ttu-id="aaba1-102">Öffnen Sie Xcode.</span><span class="sxs-lookup"><span data-stu-id="aaba1-102">Open Xcode.</span></span> <span data-ttu-id="aaba1-103">Wählen Sie im Menü **Datei** die Option **neu**und dann **Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-103">On the **File** menu, select **New**, then **Project**.</span></span>
1. <span data-ttu-id="aaba1-104">Wählen Sie die Vorlage **einzelne Ansicht-App** aus, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-104">Choose the **Single View App** template and select **Next**.</span></span>

    ![Screenshot des Dialogfelds "Xcode-Vorlagenauswahl"](./images/xcode-select-template.png)

1. <span data-ttu-id="aaba1-106">Legen Sie den **Produktnamen** auf `GraphTutorial` und die **Sprache** auf **Objective-C**fest.</span><span class="sxs-lookup"><span data-stu-id="aaba1-106">Set the **Product Name** to `GraphTutorial` and the **Language** to **Objective-C**.</span></span>
1. <span data-ttu-id="aaba1-107">Füllen Sie die restlichen Felder aus, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-107">Fill in the remaining fields and select **Next**.</span></span>
1. <span data-ttu-id="aaba1-108">Wählen Sie einen Speicherort für das Projekt aus, und wählen Sie **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-108">Choose a location for the project and select **Create**.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="aaba1-109">Installieren von Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="aaba1-109">Install dependencies</span></span>

<span data-ttu-id="aaba1-110">Installieren Sie vor dem Verschieben einige zusätzliche Abhängigkeiten, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="aaba1-110">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="aaba1-111">[Microsoft Authentication Library (MSAL) für IOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) zur Authentifizierung mit Azure AD.</span><span class="sxs-lookup"><span data-stu-id="aaba1-111">[Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) for authenticating to with Azure AD.</span></span>
- <span data-ttu-id="aaba1-112">[Microsoft Graph-SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) zum tätigen von Anrufen an Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="aaba1-112">[Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="aaba1-113">[Microsoft Graph-Modell SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) für stark typisierte Objekte, die Microsoft Graph-Ressourcen wie Benutzer oder Ereignisse darstellen.</span><span class="sxs-lookup"><span data-stu-id="aaba1-113">[Microsoft Graph Models SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) for strongly-typed objects representing Microsoft Graph resources like users or events.</span></span>

1. <span data-ttu-id="aaba1-114">Beenden Sie Xcode.</span><span class="sxs-lookup"><span data-stu-id="aaba1-114">Quit Xcode.</span></span>
1. <span data-ttu-id="aaba1-115">Öffnen Sie Terminal, und ändern Sie das Verzeichnis in den Speicherort Ihres **GraphTutorial** -Projekts.</span><span class="sxs-lookup"><span data-stu-id="aaba1-115">Open Terminal and change the directory to the location of your **GraphTutorial** project.</span></span>
1. <span data-ttu-id="aaba1-116">Führen Sie den folgenden Befehl aus, um eine Podfile zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aaba1-116">Run the following command to create a Podfile.</span></span>

    ```Shell
    pod init
    ```

1. <span data-ttu-id="aaba1-117">Öffnen Sie die Podfile, und fügen Sie die folgenden Zeilen `use_frameworks!` unmittelbar nach der Zeile hinzu.</span><span class="sxs-lookup"><span data-stu-id="aaba1-117">Open the Podfile and add the following lines just after the `use_frameworks!` line.</span></span>

    ```Ruby
    pod 'MSAL', '~> 1.1.1'
    pod 'MSGraphClientSDK', ' ~> 1.0.0'
    pod 'MSGraphClientModels', '~> 1.3.0'
    ```

1. <span data-ttu-id="aaba1-118">Speichern Sie die Podfile, und führen Sie dann den folgenden Befehl aus, um die Abhängigkeiten zu installieren.</span><span class="sxs-lookup"><span data-stu-id="aaba1-118">Save the Podfile, then run the following command to install the dependencies.</span></span>

    ```Shell
    pod install
    ```

1. <span data-ttu-id="aaba1-119">Nachdem der Befehl abgeschlossen ist, öffnen Sie das neu erstellte **GraphTutorial. xcworkspace.** in Xcode.</span><span class="sxs-lookup"><span data-stu-id="aaba1-119">Once the command completes, open the newly created **GraphTutorial.xcworkspace** in Xcode.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="aaba1-120">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="aaba1-120">Design the app</span></span>

<span data-ttu-id="aaba1-121">In diesem Abschnitt werden die Ansichten für die App erstellt: eine Anmeldeseite, ein Registerkartenleisten-Navigator, eine Willkommensseite und eine Kalender Seite.</span><span class="sxs-lookup"><span data-stu-id="aaba1-121">In this section you will create the views for the app: a sign in page, a tab bar navigator, a welcome page, and a calendar page.</span></span> <span data-ttu-id="aaba1-122">Außerdem erstellen Sie ein Aktivitäts Indikator-Overlay.</span><span class="sxs-lookup"><span data-stu-id="aaba1-122">You'll also create an activity indicator overlay.</span></span>

### <a name="create-sign-in-page"></a><span data-ttu-id="aaba1-123">Anmeldeseite erstellen</span><span class="sxs-lookup"><span data-stu-id="aaba1-123">Create sign in page</span></span>

1. <span data-ttu-id="aaba1-124">Erweitern Sie den Ordner **GraphTutorial** in Xcode, und wählen Sie dann die Datei **ViewController. m** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-124">Expand the **GraphTutorial** folder in Xcode, then select the **ViewController.m** file.</span></span>
1. <span data-ttu-id="aaba1-125">Ändern Sie `SignInViewController.m`im **Datei Inspektor**den **Namen** der Datei in.</span><span class="sxs-lookup"><span data-stu-id="aaba1-125">In the **File Inspector**, change the **Name** of the file to `SignInViewController.m`.</span></span>

    ![Screenshot des Datei Inspektors](./images/rename-file.png)

1. <span data-ttu-id="aaba1-127">Öffnen Sie **SignInViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="aaba1-127">Open **SignInViewController.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="aaba1-128">Wählen Sie die Datei **ViewController. h** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-128">Select the **ViewController.h** file.</span></span>
1. <span data-ttu-id="aaba1-129">Ändern Sie `SignInViewController.h`im **Datei Inspektor**den **Namen** der Datei in.</span><span class="sxs-lookup"><span data-stu-id="aaba1-129">In the **File Inspector**, change the **Name** of the file to `SignInViewController.h`.</span></span>
1. <span data-ttu-id="aaba1-130">Öffnen Sie **SignInViewController. h** , und ändern Sie `ViewController` alle `SignInViewController`Instanzen von in.</span><span class="sxs-lookup"><span data-stu-id="aaba1-130">Open **SignInViewController.h** and change all instances of `ViewController` to `SignInViewController`.</span></span>

1. <span data-ttu-id="aaba1-131">Öffnen Sie die Datei **Main. Storyboard** .</span><span class="sxs-lookup"><span data-stu-id="aaba1-131">Open the **Main.storyboard** file.</span></span>
1. <span data-ttu-id="aaba1-132">Erweitern Sie **View Controller Scene**, und wählen Sie dann **View Controller**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-132">Expand **View Controller Scene**, then select **View Controller**.</span></span>

    ![Screenshot von Xcode mit ausgewähltem Ansichts Controller](./images/storyboard-select-view-controller.png)

1. <span data-ttu-id="aaba1-134">Wählen Sie den **Identitäts Inspektor**aus, und ändern Sie dann das Dropdownmenü **Klasse** in **SignInViewController**.</span><span class="sxs-lookup"><span data-stu-id="aaba1-134">Select the **Identity Inspector**, then change the **Class** dropdown to **SignInViewController**.</span></span>

    ![Screenshot des Identitäts Inspektors](./images/change-class.png)

1. <span data-ttu-id="aaba1-136">Wählen Sie die **Bibliothek**aus, und ziehen Sie dann eine **Schaltfläche** auf den **Anmelde Ansichts Controller**.</span><span class="sxs-lookup"><span data-stu-id="aaba1-136">Select the **Library**, then drag a **Button** onto the **Sign In View Controller**.</span></span>

    ![Ein Screenshot der Bibliothek in Xcode](./images/add-button-to-view.png)

1. <span data-ttu-id="aaba1-138">Wählen Sie mit ausgewählter Schaltfläche den **Attributes Inspector** aus, und ändern Sie den `Sign In` **Titel** der Schaltfläche in.</span><span class="sxs-lookup"><span data-stu-id="aaba1-138">With the button selected, select the **Attributes Inspector** and change the **Title** of the button to `Sign In`.</span></span>

    ![Screenshot des titelfelds im Attributes inspector in Xcode](./images/set-button-title.png)

1. <span data-ttu-id="aaba1-140">Klicken Sie auf die Schaltfläche ausgewählt, und wählen Sie am unteren Rand des Storyboards die Schaltfläche **Ausrichten** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-140">With the button selected, select the **Align** button at the bottom of the storyboard.</span></span> <span data-ttu-id="aaba1-141">Wählen Sie sowohl **horizontal im Container** als auch **vertikal im Container** Constraints, lassen Sie Ihre Werte als 0, und wählen Sie dann **2 Abhängigkeiten hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-141">Select both the **Horizontally in container** and **Vertically in container** constraints, leave their values as 0, then select **Add 2 constraints**.</span></span>

    ![Ein Screenshot der Einstellungen für die Ausrichtungs Einschränkungen in Xcode](./images/add-alignment-constraints.png)

1. <span data-ttu-id="aaba1-143">Wählen Sie den **Controller anmelden**aus, und wählen Sie dann den **Verbindungs Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-143">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="aaba1-144">Ziehen Sie unter **empfangene Aktionen**den ungefüllten Kreis neben **SignIn** auf die Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="aaba1-144">Under **Received Actions**, drag the unfilled circle next to **signIn** onto the button.</span></span> <span data-ttu-id="aaba1-145">Wählen Sie im Popup Menü die Option nach **oben innen berühren** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-145">Select **Touch Up Inside** on the pop-up menu.</span></span>

    ![Ein Screenshot des Ziehens der SignIn-Aktion auf die Schaltfläche in Xcode](./images/connect-sign-in-button.png)

### <a name="create-tab-bar"></a><span data-ttu-id="aaba1-147">Registerkartenleiste erstellen</span><span class="sxs-lookup"><span data-stu-id="aaba1-147">Create tab bar</span></span>

1. <span data-ttu-id="aaba1-148">Wählen Sie die **Bibliothek**aus, und ziehen Sie dann einen **Registerkartenleisten-Controller** auf das Storyboard.</span><span class="sxs-lookup"><span data-stu-id="aaba1-148">Select the **Library**, then drag a **Tab Bar Controller** onto the storyboard.</span></span>
1. <span data-ttu-id="aaba1-149">Wählen Sie den **Controller anmelden**aus, und wählen Sie dann den **Verbindungs Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-149">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="aaba1-150">Ziehen Sie unter **ausgelöste Segues**den ungefüllten Kreis neben **manuell** auf den **Registerkartenleisten-Controller** auf dem Storyboard.</span><span class="sxs-lookup"><span data-stu-id="aaba1-150">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Tab Bar Controller** on the storyboard.</span></span> <span data-ttu-id="aaba1-151">Wählen Sie im Popup Menü **Modal** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-151">Select **Present Modally** in the pop-up menu.</span></span>

    ![Ein Screenshot des Ziehens einer manuellen segue auf den neuen Registerkartenleisten-Controller in Xcode](./images/add-segue.png)

1. <span data-ttu-id="aaba1-153">Wählen Sie das soeben hinzugefügte segue aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-153">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="aaba1-154">Legen Sie **Identifier** das Feldbezeichner `userSignedIn`auf fest, und legen Sie die **Präsentation** auf **Vollbildmodus**fest.</span><span class="sxs-lookup"><span data-stu-id="aaba1-154">Set the **Identifier** field to `userSignedIn`, and set **Presentation** to **Full Screen**.</span></span>

    ![Screenshot des Felds "Bezeichner" im Attributes inspector in Xcode](./images/set-segue-identifier.png)

1. <span data-ttu-id="aaba1-156">Wählen Sie die **Szene Element 1**aus, und wählen Sie dann den **Verbindungs Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-156">Select the **Item 1 Scene**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="aaba1-157">Ziehen Sie unter **ausgelöste Segues**den ungefüllten Kreis neben **manuell** auf den **Anmelde Ansichts Controller** auf dem Storyboard.</span><span class="sxs-lookup"><span data-stu-id="aaba1-157">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Sign In View Controller** on the storyboard.</span></span> <span data-ttu-id="aaba1-158">Wählen Sie im Popup Menü **Modal** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-158">Select **Present Modally** in the pop-up menu.</span></span>
1. <span data-ttu-id="aaba1-159">Wählen Sie das soeben hinzugefügte segue aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-159">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="aaba1-160">Legen Sie **Identifier** das Feldbezeichner `userSignedOut`auf fest, und legen Sie die **Präsentation** auf **Vollbildmodus**fest.</span><span class="sxs-lookup"><span data-stu-id="aaba1-160">Set the **Identifier** field to `userSignedOut`, and set **Presentation** to **Full Screen**.</span></span>

### <a name="create-welcome-page"></a><span data-ttu-id="aaba1-161">Willkommensseite erstellen</span><span class="sxs-lookup"><span data-stu-id="aaba1-161">Create welcome page</span></span>

1. <span data-ttu-id="aaba1-162">Wählen Sie die Datei **Assets. xcassets** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-162">Select the **Assets.xcassets** file.</span></span>
1. <span data-ttu-id="aaba1-163">Wählen Sie im Menü **Editor** die Option **Objekte hinzufügen**und dann **neuer Bildsatz**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-163">On the **Editor** menu, select **Add Assets**, then **New Image Set**.</span></span>
1. <span data-ttu-id="aaba1-164">Wählen Sie das neue **Image** -Objekt aus, und verwenden Sie den **Attribut Inspektor** , um seinen **Namen** auf `DefaultUserPhoto`festzulegen.</span><span class="sxs-lookup"><span data-stu-id="aaba1-164">Select the new **Image** asset and use the **Attribute Inspector** to set its **Name** to `DefaultUserPhoto`.</span></span>
1. <span data-ttu-id="aaba1-165">Fügen Sie ein beliebiges Bild hinzu, das Sie als Standardbenutzer Profilfoto dienen möchten.</span><span class="sxs-lookup"><span data-stu-id="aaba1-165">Add any image you like to serve as a default user profile photo.</span></span>

    ![Screenshot der Ansicht "ImageSet Asset" in Xcode](./images/add-default-user-photo.png)

1. <span data-ttu-id="aaba1-167">Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `WelcomeViewController`Ordner mit dem Namen.</span><span class="sxs-lookup"><span data-stu-id="aaba1-167">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `WelcomeViewController`.</span></span> <span data-ttu-id="aaba1-168">Wählen Sie **ulviewcontroller** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="aaba1-168">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="aaba1-169">Öffnen Sie **WelcomeViewController. h** , und fügen Sie den folgenden `@interface` Code innerhalb der Deklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="aaba1-169">Open **WelcomeViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    @property (nonatomic) IBOutlet UIImageView *userProfilePhoto;
    @property (nonatomic) IBOutlet UILabel *userDisplayName;
    @property (nonatomic) IBOutlet UILabel *userEmail;
    ```

1. <span data-ttu-id="aaba1-170">Öffnen Sie **WelcomeViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="aaba1-170">Open **WelcomeViewController.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="aaba1-171">Öffnen Sie **Main. Storyboard**.</span><span class="sxs-lookup"><span data-stu-id="aaba1-171">Open **Main.storyboard**.</span></span> <span data-ttu-id="aaba1-172">Wählen Sie die **Szene Element 1**aus, und wählen Sie dann den **Identitäts Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-172">Select the **Item 1 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="aaba1-173">Ändern Sie den Wert der **Klasse** in **WelcomeViewController**.</span><span class="sxs-lookup"><span data-stu-id="aaba1-173">Change the **Class** value to **WelcomeViewController**.</span></span>
1. <span data-ttu-id="aaba1-174">Fügen Sie mithilfe der **Bibliothek**die folgenden Elemente zur **Szene Element 1**hinzu.</span><span class="sxs-lookup"><span data-stu-id="aaba1-174">Using the **Library**, add the following items to the **Item 1 Scene**.</span></span>

    - <span data-ttu-id="aaba1-175">Eine **Bildansicht**</span><span class="sxs-lookup"><span data-stu-id="aaba1-175">One **Image View**</span></span>
    - <span data-ttu-id="aaba1-176">Zwei **Beschriftungen**</span><span class="sxs-lookup"><span data-stu-id="aaba1-176">Two **Labels**</span></span>
    - <span data-ttu-id="aaba1-177">Eine **Schaltfläche**</span><span class="sxs-lookup"><span data-stu-id="aaba1-177">One **Button**</span></span>

1. <span data-ttu-id="aaba1-178">Stellen Sie über den **Connections Inspector**folgende Verbindungen her.</span><span class="sxs-lookup"><span data-stu-id="aaba1-178">Using the **Connections Inspector**, make the following connections.</span></span>

    - <span data-ttu-id="aaba1-179">Verknüpfen Sie die **User** -Steckdose mit dem ersten Etikett.</span><span class="sxs-lookup"><span data-stu-id="aaba1-179">Link the **userDisplayName** outlet to the first label.</span></span>
    - <span data-ttu-id="aaba1-180">Verknüpfen Sie die **userEmail** -Steckdose mit der zweiten Bezeichnung.</span><span class="sxs-lookup"><span data-stu-id="aaba1-180">Link the **userEmail** outlet to the second label.</span></span>
    - <span data-ttu-id="aaba1-181">Verknüpfen Sie die **userProfilePhoto** -Steckdose mit der Bildansicht.</span><span class="sxs-lookup"><span data-stu-id="aaba1-181">Link the **userProfilePhoto** outlet to the image view.</span></span>
    - <span data-ttu-id="aaba1-182">Verknüpfen Sie die Aktion " **abgemeldete** empfangen" mit dem Touch der Schaltfläche nach **innen**.</span><span class="sxs-lookup"><span data-stu-id="aaba1-182">Link the **signOut** received action to the button's **Touch Up Inside**.</span></span>

1. <span data-ttu-id="aaba1-183">Wählen Sie die Ansicht Bild aus, und wählen Sie dann den **Größen Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-183">Select the image view, then select the **Size Inspector**.</span></span>
1. <span data-ttu-id="aaba1-184">Legen Sie die **Breite** und **Höhe** auf 196 fest.</span><span class="sxs-lookup"><span data-stu-id="aaba1-184">Set the **Width** and **Height** to 196.</span></span>
1. <span data-ttu-id="aaba1-185">Verwenden Sie die Schaltfläche **Ausrichten** , um die Constraint **horizontal in Container** mit dem Wert 0 hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="aaba1-185">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="aaba1-186">Verwenden Sie die Schaltfläche **neue Einschränkungen hinzufügen** (neben der Schaltfläche **Ausrichten** ), um die folgenden Einschränkungen hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="aaba1-186">Use the **Add New Constraints** button (next to the **Align** button) to add the following constraints:</span></span>

    - <span data-ttu-id="aaba1-187">Oben ausrichten an: sicherer Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="aaba1-187">Align Top to: Safe Area, value: 0</span></span>
    - <span data-ttu-id="aaba1-188">Untere Fläche für: Benutzeranzeige Name, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="aaba1-188">Bottom Space to: User Display Name, value: Standard</span></span>
    - <span data-ttu-id="aaba1-189">Höhe, Wert: 196</span><span class="sxs-lookup"><span data-stu-id="aaba1-189">Height, value: 196</span></span>
    - <span data-ttu-id="aaba1-190">Breite, Wert: 196</span><span class="sxs-lookup"><span data-stu-id="aaba1-190">Width, value: 196</span></span>

    ![Ein Screenshot der neuen Einschränkungseinstellungen in Xcode](./images/add-new-constraints.png)

1. <span data-ttu-id="aaba1-192">Wählen Sie die erste Bezeichnung aus, und verwenden Sie dann die Schaltfläche **Ausrichten** , um die Einschränkung **horizontal in Container** mit dem Wert 0 hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="aaba1-192">Select the first label, then use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="aaba1-193">Verwenden Sie die Schaltfläche **neue Einschränkungen hinzufügen** , um die folgenden Einschränkungen hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="aaba1-193">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="aaba1-194">Oberer Bereich für: Benutzerprofil Foto, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="aaba1-194">Top Space to: User Profile Photo, value: Standard</span></span>
    - <span data-ttu-id="aaba1-195">Bottom Space to: Benutzer-e-Mail, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="aaba1-195">Bottom Space to: User Email, value: Standard</span></span>

1. <span data-ttu-id="aaba1-196">Wählen Sie die zweite Bezeichnung aus, und wählen Sie dann den **Attribut Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-196">Select the second label, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="aaba1-197">Ändern Sie die **Farbe** in **dunkelgrau**und ändern Sie die **Schriftart** in **System 12,0**.</span><span class="sxs-lookup"><span data-stu-id="aaba1-197">Change the **Color** to **Dark Gray Color**, and change the **Font** to **System 12.0**.</span></span>
1. <span data-ttu-id="aaba1-198">Verwenden Sie die Schaltfläche **Ausrichten** , um die Constraint **horizontal in Container** mit dem Wert 0 hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="aaba1-198">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="aaba1-199">Verwenden Sie die Schaltfläche **neue Einschränkungen hinzufügen** , um die folgenden Einschränkungen hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="aaba1-199">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="aaba1-200">Oberer Speicherplatz für: Benutzeranzeige Name, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="aaba1-200">Top Space to: User Display Name, value: Standard</span></span>
    - <span data-ttu-id="aaba1-201">Bottom Space to: abmelden, Wert: 14</span><span class="sxs-lookup"><span data-stu-id="aaba1-201">Bottom Space to: Sign Out, value: 14</span></span>

1. <span data-ttu-id="aaba1-202">Wählen Sie die Schaltfläche aus, und wählen Sie dann den **Attribut Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-202">Select the button, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="aaba1-203">Ändern Sie **Title** den Titel `Sign Out`in.</span><span class="sxs-lookup"><span data-stu-id="aaba1-203">Change the **Title** to `Sign Out`.</span></span>
1. <span data-ttu-id="aaba1-204">Verwenden Sie die Schaltfläche **Ausrichten** , um die Constraint **horizontal in Container** mit dem Wert 0 hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="aaba1-204">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="aaba1-205">Verwenden Sie die Schaltfläche **neue Einschränkungen hinzufügen** , um die folgenden Einschränkungen hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="aaba1-205">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="aaba1-206">Oberer Bereich für: Benutzer-e-Mail, Wert: 14</span><span class="sxs-lookup"><span data-stu-id="aaba1-206">Top Space to: User Email, value: 14</span></span>

1. <span data-ttu-id="aaba1-207">Wählen Sie das Element der Registerkartenleiste am unteren Rand der Szene aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-207">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="aaba1-208">Ändern Sie **Title** den Titel `Me`in.</span><span class="sxs-lookup"><span data-stu-id="aaba1-208">Change the **Title** to `Me`.</span></span>
1. <span data-ttu-id="aaba1-209">Wählen Sie im Menü **Editor** die Option **Probleme beim automatischen Layout auflösen**aus, und wählen Sie dann **fehlende Abhängigkeiten** unterhalb **aller Ansichten im Begrüßungs Ansichts Controller**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-209">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="aaba1-210">Die Begrüßungs Szene sollte wie folgt aussehen, wenn Sie fertig sind.</span><span class="sxs-lookup"><span data-stu-id="aaba1-210">The welcome scene should look similar to this once you're done.</span></span>

![Screenshot des Layouts für die Begrüßungs Szene](./images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a><span data-ttu-id="aaba1-212">Seite "Kalender erstellen"</span><span class="sxs-lookup"><span data-stu-id="aaba1-212">Create calendar page</span></span>

1. <span data-ttu-id="aaba1-213">Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `CalendarViewController`Ordner mit dem Namen.</span><span class="sxs-lookup"><span data-stu-id="aaba1-213">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `CalendarViewController`.</span></span> <span data-ttu-id="aaba1-214">Wählen Sie **ulviewcontroller** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="aaba1-214">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="aaba1-215">Öffnen Sie **CalendarViewController. h** , und fügen Sie den folgenden `@interface` Code innerhalb der Deklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="aaba1-215">Open **CalendarViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    @property (nonatomic) IBOutlet UITextView *calendarJSON;
    ```

1. <span data-ttu-id="aaba1-216">Öffnen Sie **CalendarViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="aaba1-216">Open **CalendarViewController.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="aaba1-217">Öffnen Sie **Main. Storyboard**.</span><span class="sxs-lookup"><span data-stu-id="aaba1-217">Open **Main.storyboard**.</span></span> <span data-ttu-id="aaba1-218">Wählen Sie die **Szene Element 2**aus, und wählen Sie dann den **Identitäts Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-218">Select the **Item 2 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="aaba1-219">Ändern Sie den Wert der **Klasse** in **CalendarViewController**.</span><span class="sxs-lookup"><span data-stu-id="aaba1-219">Change the **Class** value to **CalendarViewController**.</span></span>
1. <span data-ttu-id="aaba1-220">Fügen Sie mithilfe der **Bibliothek**eine **Text Ansicht** zur **Szene "Element 2**" hinzu.</span><span class="sxs-lookup"><span data-stu-id="aaba1-220">Using the **Library**, add a **Text View** to the **Item 2 Scene**.</span></span>
1. <span data-ttu-id="aaba1-221">Wählen Sie die soeben hinzugefügte Textansicht aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-221">Select the text view you just added.</span></span> <span data-ttu-id="aaba1-222">Wählen Sie im **Editor** **Einbetten in**und dann **Bildlaufansicht**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-222">On the **Editor**, choose **Embed In**, then **Scroll View**.</span></span>
1. <span data-ttu-id="aaba1-223">Verbinden Sie die **calendarJSON** -Steckdose mithilfe des **Verbindungs Inspektors**mit der Textansicht.</span><span class="sxs-lookup"><span data-stu-id="aaba1-223">Using the **Connections Inspector**, connect the **calendarJSON** outlet to the text view.</span></span>
1. <span data-ttu-id="aaba1-224">Wählen Sie das Element der Registerkartenleiste am unteren Rand der Szene aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-224">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="aaba1-225">Ändern Sie **Title** den Titel `Calendar`in.</span><span class="sxs-lookup"><span data-stu-id="aaba1-225">Change the **Title** to `Calendar`.</span></span>
1. <span data-ttu-id="aaba1-226">Wählen Sie im Menü **Editor** die Option **Probleme beim automatischen Layout auflösen**aus, und wählen Sie dann **fehlende Abhängigkeiten** unterhalb **aller Ansichten im Begrüßungs Ansichts Controller**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="aaba1-226">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="aaba1-227">Die Kalender Szene sollte wie folgt aussehen, wenn Sie fertig sind.</span><span class="sxs-lookup"><span data-stu-id="aaba1-227">The calendar scene should look similar to this once you're done.</span></span>

![Screenshot des Kalender Szenen Layouts](./images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a><span data-ttu-id="aaba1-229">Aktivitäts Indikator erstellen</span><span class="sxs-lookup"><span data-stu-id="aaba1-229">Create activity indicator</span></span>

1. <span data-ttu-id="aaba1-230">Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `SpinnerViewController`Ordner mit dem Namen.</span><span class="sxs-lookup"><span data-stu-id="aaba1-230">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `SpinnerViewController`.</span></span> <span data-ttu-id="aaba1-231">Wählen Sie **ulviewcontroller** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="aaba1-231">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="aaba1-232">Öffnen Sie **SpinnerViewController. h** , und fügen Sie den folgenden `@interface` Code innerhalb der Deklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="aaba1-232">Open **SpinnerViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    - (void) startWithContainer:(UIViewController*) container;
    - (void) stop;
    ```

1. <span data-ttu-id="aaba1-233">Öffnen Sie **SpinnerViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="aaba1-233">Open **SpinnerViewController.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/SpinnerViewController.m" id="SpinnerViewSnippet":::

## <a name="test-the-app"></a><span data-ttu-id="aaba1-234">Testen der App</span><span class="sxs-lookup"><span data-stu-id="aaba1-234">Test the app</span></span>

<span data-ttu-id="aaba1-235">Speichern Sie Ihre Änderungen, und starten Sie die app.</span><span class="sxs-lookup"><span data-stu-id="aaba1-235">Save your changes and launch the app.</span></span> <span data-ttu-id="aaba1-236">Sie sollten mit den Schaltflächen **Anmelden** und **Abmelden** und der Registerkartenleiste zwischen den Bildschirmen navigieren können.</span><span class="sxs-lookup"><span data-stu-id="aaba1-236">You should be able to move between the screens using the **Sign In** and **Sign Out** buttons and the tab bar.</span></span>

![Screenshots der Anwendung](./images/app-screens.png)
