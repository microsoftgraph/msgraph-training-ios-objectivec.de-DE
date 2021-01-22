<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5c500-101">Erstellen Sie zunächst ein neues Objective-C-Projekt.</span><span class="sxs-lookup"><span data-stu-id="5c500-101">Begin by creating a new Objective-C project.</span></span>

1. <span data-ttu-id="5c500-102">Öffnen Sie Xcode.</span><span class="sxs-lookup"><span data-stu-id="5c500-102">Open Xcode.</span></span> <span data-ttu-id="5c500-103">Wählen Sie **im Menü "Datei"** **"Neu"** und dann **"Projekt" aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-103">On the **File** menu, select **New**, then **Project**.</span></span>
1. <span data-ttu-id="5c500-104">Wählen Sie die **App-Vorlage** und dann **"Weiter" aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-104">Choose the **App** template and select **Next**.</span></span>

    ![Screenshot des Dialogfelds zur Auswahl der Xcode-Vorlage](./images/xcode-select-template.png)

1. <span data-ttu-id="5c500-106">Legen Sie **den Produktnamen** `GraphTutorial` auf und die Sprache **auf** **Objective-C .**</span><span class="sxs-lookup"><span data-stu-id="5c500-106">Set the **Product Name** to `GraphTutorial` and the **Language** to **Objective-C**.</span></span>
1. <span data-ttu-id="5c500-107">Füllen Sie die verbleibenden Felder aus, und wählen Sie **"Weiter" aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-107">Fill in the remaining fields and select **Next**.</span></span>
1. <span data-ttu-id="5c500-108">Wählen Sie einen Speicherort für das Projekt aus, und wählen Sie **"Erstellen" aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-108">Choose a location for the project and select **Create**.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="5c500-109">Installieren von Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="5c500-109">Install dependencies</span></span>

<span data-ttu-id="5c500-110">Installieren Sie vor dem Wechsel einige zusätzliche Abhängigkeiten, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="5c500-110">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="5c500-111">[Microsoft Authentication Library (MSAL) für iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) für die Authentifizierung bei Azure AD.</span><span class="sxs-lookup"><span data-stu-id="5c500-111">[Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) for authenticating to with Azure AD.</span></span>
- <span data-ttu-id="5c500-112">[Microsoft Graph SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) für Aufrufe an Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="5c500-112">[Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="5c500-113">[Microsoft Graph Models SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) für stark typierte Objekte, die Microsoft #A0 wie Benutzer oder Ereignisse darstellen.</span><span class="sxs-lookup"><span data-stu-id="5c500-113">[Microsoft Graph Models SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) for strongly-typed objects representing Microsoft Graph resources like users or events.</span></span>

1. <span data-ttu-id="5c500-114">Beenden Sie Xcode.</span><span class="sxs-lookup"><span data-stu-id="5c500-114">Quit Xcode.</span></span>
1. <span data-ttu-id="5c500-115">Öffnen Sie Terminal, und ändern Sie das Verzeichnis in den Speicherort Ihres **GraphTutorial-Projekts.**</span><span class="sxs-lookup"><span data-stu-id="5c500-115">Open Terminal and change the directory to the location of your **GraphTutorial** project.</span></span>
1. <span data-ttu-id="5c500-116">Führen Sie den folgenden Befehl aus, um eine Podfile zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5c500-116">Run the following command to create a Podfile.</span></span>

    ```Shell
    pod init
    ```

1. <span data-ttu-id="5c500-117">Öffnen Sie die Podfile-Datei, und fügen Sie die folgenden Zeilen direkt hinter der Zeile `use_frameworks!` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c500-117">Open the Podfile and add the following lines just after the `use_frameworks!` line.</span></span>

    ```Ruby
    pod 'MSAL', '~> 1.1.13'
    pod 'MSGraphClientSDK', ' ~> 1.0.0'
    pod 'MSGraphClientModels', '~> 1.3.0'
    ```

1. <span data-ttu-id="5c500-118">Speichern Sie die Podfile, und führen Sie dann den folgenden Befehl aus, um die Abhängigkeiten zu installieren.</span><span class="sxs-lookup"><span data-stu-id="5c500-118">Save the Podfile, then run the following command to install the dependencies.</span></span>

    ```Shell
    pod install
    ```

1. <span data-ttu-id="5c500-119">Öffnen Sie nach Abschluss des Befehls den neu erstellten **GraphTutorial.xcworkspace** in Xcode.</span><span class="sxs-lookup"><span data-stu-id="5c500-119">Once the command completes, open the newly created **GraphTutorial.xcworkspace** in Xcode.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="5c500-120">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="5c500-120">Design the app</span></span>

<span data-ttu-id="5c500-121">In diesem Abschnitt erstellen Sie die Ansichten für die App: eine Anmeldeseite, einen Registerkartenleistennavigator, eine Willkommensseite und eine Kalenderseite.</span><span class="sxs-lookup"><span data-stu-id="5c500-121">In this section you will create the views for the app: a sign in page, a tab bar navigator, a welcome page, and a calendar page.</span></span> <span data-ttu-id="5c500-122">Sie erstellen auch eine Aktivitätsindikatorüberlagerung.</span><span class="sxs-lookup"><span data-stu-id="5c500-122">You'll also create an activity indicator overlay.</span></span>

### <a name="create-sign-in-page"></a><span data-ttu-id="5c500-123">Anmeldeseite erstellen</span><span class="sxs-lookup"><span data-stu-id="5c500-123">Create sign in page</span></span>

1. <span data-ttu-id="5c500-124">Erweitern Sie **den Ordner "GraphTutorial"** in Xcode, und wählen Sie dann die Datei **"ViewController.m"** aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-124">Expand the **GraphTutorial** folder in Xcode, then select the **ViewController.m** file.</span></span>
1. <span data-ttu-id="5c500-125">Ändern Sie **im Dateiinspektor** den **Namen** der Datei in `SignInViewController.m` .</span><span class="sxs-lookup"><span data-stu-id="5c500-125">In the **File Inspector**, change the **Name** of the file to `SignInViewController.m`.</span></span>

    ![Screenshot des Dateiinspektors](./images/rename-file.png)

1. <span data-ttu-id="5c500-127">Öffnen **Sie "SignInViewController.m",** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5c500-127">Open **SignInViewController.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="5c500-128">Wählen Sie die **Datei "ViewController.h"** aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-128">Select the **ViewController.h** file.</span></span>
1. <span data-ttu-id="5c500-129">Ändern Sie **im Dateiinspektor** den **Namen** der Datei in `SignInViewController.h` .</span><span class="sxs-lookup"><span data-stu-id="5c500-129">In the **File Inspector**, change the **Name** of the file to `SignInViewController.h`.</span></span>
1. <span data-ttu-id="5c500-130">Öffnen **Sie "SignInViewController.h",** und ändern Sie alle Instanzen in `ViewController` `SignInViewController` .</span><span class="sxs-lookup"><span data-stu-id="5c500-130">Open **SignInViewController.h** and change all instances of `ViewController` to `SignInViewController`.</span></span>

1. <span data-ttu-id="5c500-131">Öffnen Sie die **Datei "Main.storyboard".**</span><span class="sxs-lookup"><span data-stu-id="5c500-131">Open the **Main.storyboard** file.</span></span>
1. <span data-ttu-id="5c500-132">Erweitern **Sie die Ansichtscontroller-Szene,** und wählen Sie dann **"Ansichtscontroller" aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-132">Expand **View Controller Scene**, then select **View Controller**.</span></span>

    ![Screenshot von Xcode mit ausgewähltem Ansichtscontroller](./images/storyboard-select-view-controller.png)

1. <span data-ttu-id="5c500-134">Wählen Sie **den Identitätsinspektor** aus, und ändern Sie dann das **Klassendropdown** in **SignInViewController**.</span><span class="sxs-lookup"><span data-stu-id="5c500-134">Select the **Identity Inspector**, then change the **Class** dropdown to **SignInViewController**.</span></span>

    ![Screenshot des Identitätsinspektors](./images/change-class.png)

1. <span data-ttu-id="5c500-136">Wählen Sie die **Bibliothek** aus, und ziehen Sie dann eine **Schaltfläche** auf den **Anmeldeansichtscontroller.**</span><span class="sxs-lookup"><span data-stu-id="5c500-136">Select the **Library**, then drag a **Button** onto the **Sign In View Controller**.</span></span>

    ![Screenshot der Bibliothek in Xcode](./images/add-button-to-view.png)

1. <span data-ttu-id="5c500-138">Wenn die Schaltfläche ausgewählt ist, wählen Sie den **Attributes Inspector** aus, und ändern **Sie** den Titel der Schaltfläche in `Sign In` .</span><span class="sxs-lookup"><span data-stu-id="5c500-138">With the button selected, select the **Attributes Inspector** and change the **Title** of the button to `Sign In`.</span></span>

    ![Screenshot des Titelfelds im Attributes Inspector in Xcode](./images/set-button-title.png)

1. <span data-ttu-id="5c500-140">Wenn die Schaltfläche ausgewählt ist, wählen Sie die Schaltfläche **"Ausrichten"** am unteren Rand des Storyboards aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-140">With the button selected, select the **Align** button at the bottom of the storyboard.</span></span> <span data-ttu-id="5c500-141">Wählen Sie **sowohl "Horizontal im Container" als auch** **"Vertikal" in** Containereinschränkungen aus, lassen Sie deren Werte 0, und wählen Sie dann **"2 Einschränkungen hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-141">Select both the **Horizontally in container** and **Vertically in container** constraints, leave their values as 0, then select **Add 2 constraints**.</span></span>

    ![Screenshot der Einstellungen für Ausrichtungseinschränkungen in Xcode](./images/add-alignment-constraints.png)

1. <span data-ttu-id="5c500-143">Wählen Sie **den Anmeldeansichtscontroller** und dann den **Connections Inspector aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-143">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="5c500-144">Ziehen **Sie unter "Empfangene** Aktionen" den nicht ausgefüllten Kreis neben **"SignIn" auf** die Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="5c500-144">Under **Received Actions**, drag the unfilled circle next to **signIn** onto the button.</span></span> <span data-ttu-id="5c500-145">Wählen **Sie im Popupmenü** "Touch Up Inside" aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-145">Select **Touch Up Inside** on the pop-up menu.</span></span>

    ![Screenshot des Ziehens der Anmeldeaktion auf die Schaltfläche in Xcode](./images/connect-sign-in-button.png)

### <a name="create-tab-bar"></a><span data-ttu-id="5c500-147">Registerkartenleiste erstellen</span><span class="sxs-lookup"><span data-stu-id="5c500-147">Create tab bar</span></span>

1. <span data-ttu-id="5c500-148">Wählen Sie **die Bibliothek** aus, und ziehen Sie dann einen **Tableistencontroller** auf das Storyboard.</span><span class="sxs-lookup"><span data-stu-id="5c500-148">Select the **Library**, then drag a **Tab Bar Controller** onto the storyboard.</span></span>
1. <span data-ttu-id="5c500-149">Wählen Sie **den Anmeldeansichtscontroller** und dann den **Verbindungsinspektor aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-149">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="5c500-150">Ziehen **Sie unter "Ausgelöste Segues"** den nicht ausgefüllten Kreis neben **manuell** auf den **Tableistencontroller** im Storyboard.</span><span class="sxs-lookup"><span data-stu-id="5c500-150">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Tab Bar Controller** on the storyboard.</span></span> <span data-ttu-id="5c500-151">Wählen **Sie im Popupmenü** "Modal präsentieren" aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-151">Select **Present Modally** in the pop-up menu.</span></span>

    ![Screenshot des Ziehens einer manuellen Segue auf den neuen Tableistencontroller in Xcode](./images/add-segue.png)

1. <span data-ttu-id="5c500-153">Wählen Sie die gerade hinzugefügte Segue aus, und wählen Sie dann den **Attributes Inspector aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-153">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="5c500-154">Legen Sie das **Feld "Bezeichner"** `userSignedIn` auf " und **"Presentation" auf** **"Vollbild" (Vollbild) festgelegt.**</span><span class="sxs-lookup"><span data-stu-id="5c500-154">Set the **Identifier** field to `userSignedIn`, and set **Presentation** to **Full Screen**.</span></span>

    ![Screenshot des Felds "Identifier" im Attributes Inspector in Xcode](./images/set-segue-identifier.png)

1. <span data-ttu-id="5c500-156">Wählen Sie **die Szene "Element 1"** und dann den Verbindungsinspektor **aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-156">Select the **Item 1 Scene**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="5c500-157">Ziehen **Sie unter "Ausgelöste Segues"** den nicht ausgefüllten Kreis neben **manuell** auf den Sign In **View Controller** im Storyboard.</span><span class="sxs-lookup"><span data-stu-id="5c500-157">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Sign In View Controller** on the storyboard.</span></span> <span data-ttu-id="5c500-158">Wählen **Sie im Popupmenü** modal "Present" aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-158">Select **Present Modally** in the pop-up menu.</span></span>
1. <span data-ttu-id="5c500-159">Wählen Sie die gerade hinzugefügte Segue aus, und wählen Sie dann den **Attributes Inspector aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-159">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="5c500-160">Legen Sie das **Feld "Bezeichner"** `userSignedOut` auf " und **"Presentation" auf** **"Vollbild" (Vollbild) festgelegt.**</span><span class="sxs-lookup"><span data-stu-id="5c500-160">Set the **Identifier** field to `userSignedOut`, and set **Presentation** to **Full Screen**.</span></span>

### <a name="create-welcome-page"></a><span data-ttu-id="5c500-161">Willkommensseite erstellen</span><span class="sxs-lookup"><span data-stu-id="5c500-161">Create welcome page</span></span>

1. <span data-ttu-id="5c500-162">Wählen Sie die **Datei Assets.xcassets** aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-162">Select the **Assets.xcassets** file.</span></span>
1. <span data-ttu-id="5c500-163">Wählen Sie **im Menü "Editor"** **"Neues Objekt hinzufügen"** und dann **"Bildsatz" aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-163">On the **Editor** menu, select **Add New Asset**, then **Image Set**.</span></span>
1. <span data-ttu-id="5c500-164">Wählen Sie das neue **Bild-Objekt** aus, und legen Sie den Namen mithilfe des Attributprüfungsinspektors **auf**  `DefaultUserPhoto` .</span><span class="sxs-lookup"><span data-stu-id="5c500-164">Select the new **Image** asset and use the **Attribute Inspector** to set its **Name** to `DefaultUserPhoto`.</span></span>
1. <span data-ttu-id="5c500-165">Fügen Sie ein beliebiges Bild hinzu, das Als Standardbenutzerprofilfoto dienen soll.</span><span class="sxs-lookup"><span data-stu-id="5c500-165">Add any image you like to serve as a default user profile photo.</span></span>

    ![Screenshot der Objektansicht "Bildsatz" in Xcode](./images/add-default-user-photo.png)

1. <span data-ttu-id="5c500-167">Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **Ordner GraphTutorial** mit dem Namen `WelcomeViewController` .</span><span class="sxs-lookup"><span data-stu-id="5c500-167">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `WelcomeViewController`.</span></span> <span data-ttu-id="5c500-168">Wählen **Sie "UIViewController"** in der **Unterklasse des Felds** aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-168">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="5c500-169">Öffnen **Sie WelcomeViewController.h,** und fügen Sie den folgenden Code in der Deklaration `@interface` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c500-169">Open **WelcomeViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    @property (nonatomic) IBOutlet UIImageView *userProfilePhoto;
    @property (nonatomic) IBOutlet UILabel *userDisplayName;
    @property (nonatomic) IBOutlet UILabel *userEmail;
    ```

1. <span data-ttu-id="5c500-170">Öffnen **Sie WelcomeViewController.m,** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5c500-170">Open **WelcomeViewController.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="5c500-171">Öffnen **Sie Main.storyboard**.</span><span class="sxs-lookup"><span data-stu-id="5c500-171">Open **Main.storyboard**.</span></span> <span data-ttu-id="5c500-172">Wählen Sie **die Szene "Element 1"** und dann den **Identitätsinspektor aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-172">Select the **Item 1 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="5c500-173">Ändern Sie **den Wert der** Klasse in **WelcomeViewController**.</span><span class="sxs-lookup"><span data-stu-id="5c500-173">Change the **Class** value to **WelcomeViewController**.</span></span>
1. <span data-ttu-id="5c500-174">Fügen Sie **mithilfe der Bibliothek** der Szene **"Element 1"** die folgenden Elemente hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c500-174">Using the **Library**, add the following items to the **Item 1 Scene**.</span></span>

    - <span data-ttu-id="5c500-175">Eine **Bildansicht**</span><span class="sxs-lookup"><span data-stu-id="5c500-175">One **Image View**</span></span>
    - <span data-ttu-id="5c500-176">Zwei **Bezeichnungen**</span><span class="sxs-lookup"><span data-stu-id="5c500-176">Two **Labels**</span></span>
    - <span data-ttu-id="5c500-177">Eine **Schaltfläche**</span><span class="sxs-lookup"><span data-stu-id="5c500-177">One **Button**</span></span>

1. <span data-ttu-id="5c500-178">Stellen Sie **mithilfe des Connections Inspector** die folgenden Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="5c500-178">Using the **Connections Inspector**, make the following connections.</span></span>

    - <span data-ttu-id="5c500-179">Verknüpfen Sie **den userDisplayName-Ausgang** mit der ersten Bezeichnung.</span><span class="sxs-lookup"><span data-stu-id="5c500-179">Link the **userDisplayName** outlet to the first label.</span></span>
    - <span data-ttu-id="5c500-180">Verknüpfen Sie **die userEmail-Filiale** mit der zweiten Bezeichnung.</span><span class="sxs-lookup"><span data-stu-id="5c500-180">Link the **userEmail** outlet to the second label.</span></span>
    - <span data-ttu-id="5c500-181">Link the **userProfilePhoto** outlets to the image view.</span><span class="sxs-lookup"><span data-stu-id="5c500-181">Link the **userProfilePhoto** outlet to the image view.</span></span>
    - <span data-ttu-id="5c500-182">Verknüpfen Sie **die empfangene Aktion "SignOut"** mit dem Touch **Up Inside der Schaltfläche.**</span><span class="sxs-lookup"><span data-stu-id="5c500-182">Link the **signOut** received action to the button's **Touch Up Inside**.</span></span>

1. <span data-ttu-id="5c500-183">Wählen Sie die Bildansicht und dann den **Größeninspektor aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-183">Select the image view, then select the **Size Inspector**.</span></span>
1. <span data-ttu-id="5c500-184">Legen Sie **die Breite** und **Höhe auf** 196 fest.</span><span class="sxs-lookup"><span data-stu-id="5c500-184">Set the **Width** and **Height** to 196.</span></span>
1. <span data-ttu-id="5c500-185">Verwenden Sie die **Schaltfläche "Ausrichten",** um die **Einschränkung "Horizontal in Container"** mit dem Wert 0 hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5c500-185">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="5c500-186">Verwenden Sie **die Schaltfläche "Neue Nebenbedingungen** hinzufügen" (neben der Schaltfläche "Ausrichten"), um die folgenden Einschränkungen hinzuzufügen: </span><span class="sxs-lookup"><span data-stu-id="5c500-186">Use the **Add New Constraints** button (next to the **Align** button) to add the following constraints:</span></span>

    - <span data-ttu-id="5c500-187">Oben ausrichten an: Sicherer Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5c500-187">Align Top to: Safe Area, value: 0</span></span>
    - <span data-ttu-id="5c500-188">Bottom Space to: User Display Name, value: Standard</span><span class="sxs-lookup"><span data-stu-id="5c500-188">Bottom Space to: User Display Name, value: Standard</span></span>
    - <span data-ttu-id="5c500-189">Höhe, Wert: 196</span><span class="sxs-lookup"><span data-stu-id="5c500-189">Height, value: 196</span></span>
    - <span data-ttu-id="5c500-190">Breite, Wert: 196</span><span class="sxs-lookup"><span data-stu-id="5c500-190">Width, value: 196</span></span>

    ![Screenshot der neuen Einschränkungseinstellungen in Xcode](./images/add-new-constraints.png)

1. <span data-ttu-id="5c500-192">Wählen Sie die erste Bezeichnung aus, und verwenden Sie dann die Schaltfläche **"Ausrichten",** um die **Einschränkung "Horizontal in Container"** mit dem Wert 0 hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5c500-192">Select the first label, then use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="5c500-193">Verwenden Sie die **Schaltfläche "Neue Einschränkungen hinzufügen",** um die folgenden Einschränkungen hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="5c500-193">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="5c500-194">Oberster Bereich für: Benutzerprofilfoto, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="5c500-194">Top Space to: User Profile Photo, value: Standard</span></span>
    - <span data-ttu-id="5c500-195">Bottom Space to: User Email, value: Standard</span><span class="sxs-lookup"><span data-stu-id="5c500-195">Bottom Space to: User Email, value: Standard</span></span>

1. <span data-ttu-id="5c500-196">Wählen Sie die zweite Bezeichnung und dann den **Attributprüfungsinspektor aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-196">Select the second label, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="5c500-197">Ändern Sie **die Farbe** in **Dunkelgrau,** und ändern Sie **die Schriftart** in **System 12.0**.</span><span class="sxs-lookup"><span data-stu-id="5c500-197">Change the **Color** to **Dark Gray Color**, and change the **Font** to **System 12.0**.</span></span>
1. <span data-ttu-id="5c500-198">Verwenden Sie die **Schaltfläche "Ausrichten",** um die **Einschränkung "Horizontal in Container"** mit dem Wert 0 hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5c500-198">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="5c500-199">Verwenden Sie die **Schaltfläche "Neue Einschränkungen hinzufügen",** um die folgenden Einschränkungen hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="5c500-199">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="5c500-200">Oberes Leerzeichen für: Anzeigename des Benutzers, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="5c500-200">Top Space to: User Display Name, value: Standard</span></span>
    - <span data-ttu-id="5c500-201">Bottom Space to: Sign Out, value: 14</span><span class="sxs-lookup"><span data-stu-id="5c500-201">Bottom Space to: Sign Out, value: 14</span></span>

1. <span data-ttu-id="5c500-202">Wählen Sie die Schaltfläche und dann den **Attributes Inspector aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-202">Select the button, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="5c500-203">Ändern Sie **den Titel** in `Sign Out` .</span><span class="sxs-lookup"><span data-stu-id="5c500-203">Change the **Title** to `Sign Out`.</span></span>
1. <span data-ttu-id="5c500-204">Verwenden Sie die **Schaltfläche "Ausrichten",** um die **Einschränkung "Horizontal in Container"** mit dem Wert 0 hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5c500-204">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="5c500-205">Verwenden Sie die **Schaltfläche "Neue Einschränkungen hinzufügen",** um die folgenden Einschränkungen hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="5c500-205">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="5c500-206">Oberster Bereich für: Benutzer-E-Mail, Wert: 14</span><span class="sxs-lookup"><span data-stu-id="5c500-206">Top Space to: User Email, value: 14</span></span>

1. <span data-ttu-id="5c500-207">Wählen Sie das Element der Registerkartenleiste am unteren Rand der Szene aus, und wählen Sie dann den **Attributes Inspector aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-207">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="5c500-208">Ändern Sie **den Titel** in `Me` .</span><span class="sxs-lookup"><span data-stu-id="5c500-208">Change the **Title** to `Me`.</span></span>
1. <span data-ttu-id="5c500-209">Wählen Sie **im Menü "Editor"** **die** Option "Probleme beim automatischen Layout beheben" und dann unter "Alle Ansichten" im **Willkommensansichtscontroller** "Fehlende Einschränkungen **hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-209">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="5c500-210">Die Willkommensszene sollte in etwa so aussehen, sobald Sie fertig sind.</span><span class="sxs-lookup"><span data-stu-id="5c500-210">The welcome scene should look similar to this once you're done.</span></span>

![Screenshot des Layouts der Willkommensszene](./images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a><span data-ttu-id="5c500-212">Kalenderseite erstellen</span><span class="sxs-lookup"><span data-stu-id="5c500-212">Create calendar page</span></span>

1. <span data-ttu-id="5c500-213">Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **Ordner GraphTutorial** mit dem Namen `CalendarViewController` .</span><span class="sxs-lookup"><span data-stu-id="5c500-213">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `CalendarViewController`.</span></span> <span data-ttu-id="5c500-214">Wählen **Sie "UIViewController"** in der **Unterklasse des Felds** aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-214">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="5c500-215">Öffnen **Sie CalendarViewController.h,** und fügen Sie den folgenden Code in der Deklaration `@interface` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c500-215">Open **CalendarViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    @property (nonatomic) IBOutlet UITextView *calendarJSON;
    ```

1. <span data-ttu-id="5c500-216">Öffnen **Sie CalendarViewController.m,** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5c500-216">Open **CalendarViewController.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="5c500-217">Öffnen **Sie Main.storyboard**.</span><span class="sxs-lookup"><span data-stu-id="5c500-217">Open **Main.storyboard**.</span></span> <span data-ttu-id="5c500-218">Wählen Sie **die Szene "Element 2"** und dann den **Identitätsinspektor aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-218">Select the **Item 2 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="5c500-219">Ändern Sie **den Wert der** Klasse in **CalendarViewController**.</span><span class="sxs-lookup"><span data-stu-id="5c500-219">Change the **Class** value to **CalendarViewController**.</span></span>
1. <span data-ttu-id="5c500-220">Fügen Sie **mithilfe der Bibliothek** eine **Textansicht zur** Szene **"Element 2" hinzu.**</span><span class="sxs-lookup"><span data-stu-id="5c500-220">Using the **Library**, add a **Text View** to the **Item 2 Scene**.</span></span>
1. <span data-ttu-id="5c500-221">Wählen Sie die gerade hinzugefügte Textansicht aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-221">Select the text view you just added.</span></span> <span data-ttu-id="5c500-222">Wählen Sie **im Editor** **"Einbetten in"** und dann **"Bildlaufansicht" aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-222">On the **Editor**, choose **Embed In**, then **Scroll View**.</span></span>
1. <span data-ttu-id="5c500-223">Verbinden Sie **mithilfe des Connections Inspectors** die **calendarJSON-Filiale** mit der Textansicht.</span><span class="sxs-lookup"><span data-stu-id="5c500-223">Using the **Connections Inspector**, connect the **calendarJSON** outlet to the text view.</span></span>
1. <span data-ttu-id="5c500-224">Wählen Sie das Element der Registerkartenleiste am unteren Rand der Szene aus, und wählen Sie dann den **Attributes Inspector aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-224">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="5c500-225">Ändern Sie **den Titel** in `Calendar` .</span><span class="sxs-lookup"><span data-stu-id="5c500-225">Change the **Title** to `Calendar`.</span></span>
1. <span data-ttu-id="5c500-226">Wählen Sie **im Menü "Editor"** **die** Option "Probleme beim automatischen Layout beheben" und dann unter "Alle Ansichten" im **Willkommensansichtscontroller** "Fehlende Einschränkungen **hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="5c500-226">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="5c500-227">Die Kalenderszene sollte wie dies aussehen, sobald Sie fertig sind.</span><span class="sxs-lookup"><span data-stu-id="5c500-227">The calendar scene should look similar to this once you're done.</span></span>

![Screenshot des Layouts der Kalenderszene](./images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a><span data-ttu-id="5c500-229">Aktivitätsindikator erstellen</span><span class="sxs-lookup"><span data-stu-id="5c500-229">Create activity indicator</span></span>

1. <span data-ttu-id="5c500-230">Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **Ordner GraphTutorial** mit dem Namen `SpinnerViewController` .</span><span class="sxs-lookup"><span data-stu-id="5c500-230">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `SpinnerViewController`.</span></span> <span data-ttu-id="5c500-231">Wählen **Sie "UIViewController"** in der **Unterklasse des Felds** aus.</span><span class="sxs-lookup"><span data-stu-id="5c500-231">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="5c500-232">Öffnen **Sie "SpinnerViewController.h",** und fügen Sie den folgenden Code in der Deklaration `@interface` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c500-232">Open **SpinnerViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    - (void) startWithContainer:(UIViewController*) container;
    - (void) stop;
    ```

1. <span data-ttu-id="5c500-233">Öffnen **Sie "SpinnerViewController.m",** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5c500-233">Open **SpinnerViewController.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/SpinnerViewController.m" id="SpinnerViewSnippet":::

## <a name="test-the-app"></a><span data-ttu-id="5c500-234">Testen der App</span><span class="sxs-lookup"><span data-stu-id="5c500-234">Test the app</span></span>

<span data-ttu-id="5c500-235">Speichern Sie Ihre Änderungen, und starten Sie die App.</span><span class="sxs-lookup"><span data-stu-id="5c500-235">Save your changes and launch the app.</span></span> <span data-ttu-id="5c500-236">Sie sollten mithilfe der Schaltflächen  "An-  und Abmelden" und der Registerkartenleiste zwischen den Bildschirmen wechseln können.</span><span class="sxs-lookup"><span data-stu-id="5c500-236">You should be able to move between the screens using the **Sign In** and **Sign Out** buttons and the tab bar.</span></span>

![Screenshots der Anwendung](./images/app-screens.png)
