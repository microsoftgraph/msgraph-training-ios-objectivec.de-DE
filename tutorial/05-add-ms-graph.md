<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6e27e-101">In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="6e27e-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="6e27e-102">Für diese Anwendung verwenden Sie das [Microsoft Graph-SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) , um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="6e27e-102">For this application, you will use the [Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="6e27e-103">Abrufen von Kalenderereignissen aus Outlook</span><span class="sxs-lookup"><span data-stu-id="6e27e-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="6e27e-104">In diesem Abschnitt erweitern Sie die `GraphManager` Klasse so, dass eine Funktion hinzugefügt wird, um die Ereignisse `CalendarViewController` des Benutzers abzurufen und diese neuen Funktionen zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="6e27e-104">In this section you will extend the `GraphManager` class to add a function to get the user's events and update `CalendarViewController` to use these new functions.</span></span>

1. <span data-ttu-id="6e27e-105">Öffnen Sie **graphmanager. h** , und fügen Sie den folgenden `@interface` Code oberhalb der Deklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="6e27e-105">Open **GraphManager.h** and add the following code above the `@interface` declaration.</span></span>

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSData* _Nullable data, NSError* _Nullable error);
    ```

1. <span data-ttu-id="6e27e-106">Fügen Sie der `@interface` Deklaration den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="6e27e-106">Add the following code to the `@interface` declaration.</span></span>

    ```objc
    - (void) getEventsWithCompletionBlock: (GetEventsCompletionBlock)completionBlock;
    ```

1. <span data-ttu-id="6e27e-107">Öffnen Sie **graphmanager. m** , und fügen Sie der `GraphManager` Klasse die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="6e27e-107">Open **GraphManager.m** and add the following function to the `GraphManager` class.</span></span>

    ```objc
    - (void) getEventsWithCompletionBlock:(GetEventsCompletionBlock)completionBlock {
        // GET /me/events?$select='subject,organizer,start,end'$orderby=createdDateTime DESC
        NSString* eventsUrlString =
        [NSString stringWithFormat:@"%@/me/events?%@&%@",
         MSGraphBaseURL,
         // Only return these fields in results
         @"$select=subject,organizer,start,end",
         // Sort results by when they were created, newest first
         @"$orderby=createdDateTime+DESC"];

        NSURL* eventsUrl = [[NSURL alloc] initWithString:eventsUrlString];
        NSMutableURLRequest* eventsRequest = [[NSMutableURLRequest alloc] initWithURL:eventsUrl];

        MSURLSessionDataTask* eventsDataTask =
        [[MSURLSessionDataTask alloc]
         initWithRequest:eventsRequest
         client:self.graphClient
         completion:^(NSData *data, NSURLResponse *response, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
                 return;
             }

             // TEMPORARY
             completionBlock(data, nil);
         }];

        // Execute the request
        [eventsDataTask execute];
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="6e27e-108">Überprüfen Sie, was `getEventsWithCompletionBlock` der Code in tut.</span><span class="sxs-lookup"><span data-stu-id="6e27e-108">Consider what the code in `getEventsWithCompletionBlock` is doing.</span></span>
    >
    > - <span data-ttu-id="6e27e-109">Die URL, die aufgerufen wird `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="6e27e-109">The URL that will be called is `/v1.0/me/events`.</span></span>
    > - <span data-ttu-id="6e27e-110">Der `select` Abfrageparameter schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf diejenigen ein, die von der APP tatsächlich verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6e27e-110">The `select` query parameter limits the fields returned for each events to just those the app will actually use.</span></span>
    > - <span data-ttu-id="6e27e-111">Der `orderby` Abfrageparameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="6e27e-111">The `orderby` query parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="6e27e-112">Öffnen Sie **CalendarViewController. m** , und ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="6e27e-112">Open **CalendarViewController.m** and replace its entire contents with the following code.</span></span>

    ```objc
    #import "CalendarViewController.h"
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>

    @interface CalendarViewController ()

    @property SpinnerViewController* spinner;

    @end

    @implementation CalendarViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        [GraphManager.instance
         getEventsWithCompletionBlock:^(NSData * _Nullable data, NSError * _Nullable error) {
             dispatch_async(dispatch_get_main_queue(), ^{
                 [self.spinner stop];

                 if (error) {
                     // Show the error
                     UIAlertController* alert = [UIAlertController
                                                 alertControllerWithTitle:@"Error getting events"
                                                 message:error.debugDescription
                                                 preferredStyle:UIAlertControllerStyleAlert];

                     UIAlertAction* okButton = [UIAlertAction
                                                actionWithTitle:@"OK"
                                                style:UIAlertActionStyleDefault
                                                handler:nil];

                     [alert addAction:okButton];
                     [self presentViewController:alert animated:true completion:nil];
                     return;
                 }

                 // TEMPORARY
                 self.calendarJSON.text = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                 [self.calendarJSON sizeToFit];
             });
         }];
    }

    @end
    ```

<span data-ttu-id="6e27e-113">Sie können nun die app ausführen, sich anmelden und auf das Navigationselement **Kalender** im Menü tippen.</span><span class="sxs-lookup"><span data-stu-id="6e27e-113">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="6e27e-114">Es sollte ein JSON-Dump der Ereignisse in der App angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="6e27e-114">You should see a JSON dump of the events in the app.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="6e27e-115">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="6e27e-115">Display the results</span></span>

<span data-ttu-id="6e27e-116">Nun können Sie das JSON-Dump durch etwas ersetzen, um die Ergebnisse auf eine benutzerfreundliche Weise anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="6e27e-116">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="6e27e-117">In diesem Abschnitt ändern Sie die `getEventsWithCompletionBlock` Funktion so, dass stark typisierte Objekte zurückgegeben werden, `CalendarViewController` und ändern, um die Ereignisse mithilfe einer Tabellenansicht zu rendern.</span><span class="sxs-lookup"><span data-stu-id="6e27e-117">In this section, you will modify the `getEventsWithCompletionBlock` function to return strongly-typed objects, and modify `CalendarViewController` to use a table view to render the events.</span></span>

### <a name="update-getevents"></a><span data-ttu-id="6e27e-118">Aktualisieren von GetEvents</span><span class="sxs-lookup"><span data-stu-id="6e27e-118">Update getEvents</span></span>

1. <span data-ttu-id="6e27e-119">Öffnen Sie **graphmanager. h**.</span><span class="sxs-lookup"><span data-stu-id="6e27e-119">Open **GraphManager.h**.</span></span> <span data-ttu-id="6e27e-120">Ändern Sie `GetEventsCompletionBlock` die Typdefinition in Folgendes.</span><span class="sxs-lookup"><span data-stu-id="6e27e-120">Change the `GetEventsCompletionBlock` type definition to the following.</span></span>

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSArray<MSGraphEvent*>* _Nullable events, NSError* _Nullable error);
    ```

1. <span data-ttu-id="6e27e-121">Öffnen Sie **graphmanager. m**.</span><span class="sxs-lookup"><span data-stu-id="6e27e-121">Open **GraphManager.m**.</span></span> <span data-ttu-id="6e27e-122">Ersetzen Sie `completionBlock(data, nil);` die- `getEventsWithCompletionBlock` Funktion durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="6e27e-122">Replace the `completionBlock(data, nil);` line in the `getEventsWithCompletionBlock` function with the following code.</span></span>

    ```objc
    NSError* graphError;

    // Deserialize to an events collection
    MSCollection* eventsCollection = [[MSCollection alloc] initWithData:data error:&graphError];
    if (graphError) {
        completionBlock(nil, graphError);
        return;
    }

    // Create an array to return
    NSMutableArray* eventsArray = [[NSMutableArray alloc]
                                initWithCapacity:eventsCollection.value.count];

    for (id event in eventsCollection.value) {
        // Deserialize the event and add to the array
        MSGraphEvent* graphEvent = [[MSGraphEvent alloc] initWithDictionary:event];
        [eventsArray addObject:graphEvent];
    }

    completionBlock(eventsArray, nil);
    ```

### <a name="update-calendarviewcontroller"></a><span data-ttu-id="6e27e-123">CalendarViewController aktualisieren</span><span class="sxs-lookup"><span data-stu-id="6e27e-123">Update CalendarViewController</span></span>

1. <span data-ttu-id="6e27e-124">Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `CalendarTableViewCell`Projekt mit dem Namen.</span><span class="sxs-lookup"><span data-stu-id="6e27e-124">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewCell`.</span></span> <span data-ttu-id="6e27e-125">Wählen Sie **UITableViewCell** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="6e27e-125">Choose **UITableViewCell** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="6e27e-126">Öffnen Sie **CalendarTableViewCell. h** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="6e27e-126">Open **CalendarTableViewCell.h** and replace its contents with the following code.</span></span>

    ```objc
    #import <UIKit/UIKit.h>

    NS_ASSUME_NONNULL_BEGIN

    @interface CalendarTableViewCell : UITableViewCell

    @property (nonatomic) NSString* subject;
    @property (nonatomic) NSString* organizer;
    @property (nonatomic) NSString* duration;

    @end

    NS_ASSUME_NONNULL_END
    ```

1. <span data-ttu-id="6e27e-127">Öffnen Sie **CalendarTableViewCell. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="6e27e-127">Open **CalendarTableViewCell.m** and replace its contents with the following code.</span></span>

    ```objc
    #import "CalendarTableViewCell.h"

    @interface CalendarTableViewCell()

    @property (nonatomic) IBOutlet UILabel *subjectLabel;
    @property (nonatomic) IBOutlet UILabel *organizerLabel;
    @property (nonatomic) IBOutlet UILabel *durationLabel;

    @end

    @implementation CalendarTableViewCell

    - (void)awakeFromNib {
        [super awakeFromNib];
        // Initialization code
    }

    - (void)setSelected:(BOOL)selected animated:(BOOL)animated {
        [super setSelected:selected animated:animated];

        // Configure the view for the selected state
    }

    - (void) setSubject:(NSString *)subject {
        _subject = subject;
        self.subjectLabel.text = subject;
        [self.subjectLabel sizeToFit];
    }

    - (void) setOrganizer:(NSString *)organizer {
        _organizer = organizer;
        self.organizerLabel.text = organizer;
        [self.organizerLabel sizeToFit];
    }

    - (void) setDuration:(NSString *)duration {
        _duration = duration;
        self.durationLabel.text = duration;
        [self.durationLabel sizeToFit];
    }

    @end
    ```

1. <span data-ttu-id="6e27e-128">Öffnen Sie **Main. Storyboard** , und suchen Sie die **Kalender Szene**.</span><span class="sxs-lookup"><span data-stu-id="6e27e-128">Open **Main.storyboard** and locate the **Calendar Scene**.</span></span> <span data-ttu-id="6e27e-129">Wählen Sie die **Ansicht** in der **Kalender Szene** aus, und löschen Sie Sie.</span><span class="sxs-lookup"><span data-stu-id="6e27e-129">Select the **View** in the **Calendar Scene** and delete it.</span></span>

    ![Screenshot der Ansicht in der Kalender Szene](./images/view-in-calendar-scene.png)

1. <span data-ttu-id="6e27e-131">Hinzufügen einer **Tabellenansicht** aus der **Bibliothek** zur **Kalender Szene**.</span><span class="sxs-lookup"><span data-stu-id="6e27e-131">Add a **Table View** from the **Library** to the **Calendar Scene**.</span></span>
1. <span data-ttu-id="6e27e-132">Wählen Sie die Tabellenansicht aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="6e27e-132">Select the table view, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="6e27e-133">Legen Sie **Prototype Cells** auf **1**fest.</span><span class="sxs-lookup"><span data-stu-id="6e27e-133">Set **Prototype Cells** to **1**.</span></span>
1. <span data-ttu-id="6e27e-134">Verwenden Sie die **Bibliothek** , um der Zelle Prototyp drei **Beschriftungen** hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="6e27e-134">Use the **Library** to add three **Labels** to the prototype cell.</span></span>
1. <span data-ttu-id="6e27e-135">Wählen Sie die Zelle Prototyp aus, und wählen Sie dann den **Identitäts Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="6e27e-135">Select the prototype cell, then select the **Identity Inspector**.</span></span> <span data-ttu-id="6e27e-136">Ändern Sie die **Klasse** in **CalendarTableViewCell**.</span><span class="sxs-lookup"><span data-stu-id="6e27e-136">Change **Class** to **CalendarTableViewCell**.</span></span>
1. <span data-ttu-id="6e27e-137">Wählen Sie den **Attributes Inspector** aus, `EventCell`und legen Sie **Identifier** auf fest.</span><span class="sxs-lookup"><span data-stu-id="6e27e-137">Select the **Attributes Inspector** and set **Identifier** to `EventCell`.</span></span>
1. <span data-ttu-id="6e27e-138">Wählen Sie im Menü **Editor** die Option **Probleme beim automatischen Layout auflösen**aus, und wählen Sie dann **fehlende Abhängigkeiten** unterhalb **aller Ansichten im Begrüßungs Ansichts Controller**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="6e27e-138">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>
1. <span data-ttu-id="6e27e-139">Wählen Sie mit ausgewähltem **EventCell** den **Verbindungs Inspektor** aus, `durationLabel`und `organizerLabel`verbinden `subjectLabel` Sie die Beschriftungen, die Sie der Zelle im Storyboard hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="6e27e-139">With the **EventCell** selected, select the **Connections Inspector** and connect `durationLabel`, `organizerLabel`, and `subjectLabel` to the labels you added to the cell on the storyboard.</span></span>

    ![Screenshot des Zellenlayouts für Prototypen](./images/prototype-cell-layout.png)

1. <span data-ttu-id="6e27e-141">Öffnen Sie **CalendarViewController. h** , und `calendarJSON` entfernen Sie die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6e27e-141">Open **CalendarViewController.h** and remove the `calendarJSON` property.</span></span>
1. <span data-ttu-id="6e27e-142">Ändern Sie `@interface` die Deklaration in Folgendes.</span><span class="sxs-lookup"><span data-stu-id="6e27e-142">Change the `@interface` declaration to the following.</span></span>

    ```objc
    @interface CalendarViewController : UITableViewController
    ```

1. <span data-ttu-id="6e27e-143">Öffnen Sie **CalendarViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="6e27e-143">Open **CalendarViewController.m** and replace its contents with the following code.</span></span>

    ```objc
    #import "WelcomeViewController.h"
    #import "SpinnerViewController.h"
    #import "AuthenticationManager.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>

    @interface WelcomeViewController ()

    @property SpinnerViewController* spinner;

    @end

    @implementation WelcomeViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        self.userProfilePhoto.image = [UIImage imageNamed:@"DefaultUserPhoto"];

        [GraphManager.instance
         getMeWithCompletionBlock:^(MSGraphUser * _Nullable user, NSError * _Nullable error) {
            dispatch_async(dispatch_get_main_queue(), ^{
                [self.spinner stop];

                if (error) {
                    // Show the error
                    UIAlertController* alert = [UIAlertController
                                                alertControllerWithTitle:@"Error getting user profile"
                                                message:error.debugDescription
                                                preferredStyle:UIAlertControllerStyleAlert];

                    UIAlertAction* okButton = [UIAlertAction
                                               actionWithTitle:@"OK"
                                               style:UIAlertActionStyleDefault
                                               handler:nil];

                    [alert addAction:okButton];
                    [self presentViewController:alert animated:true completion:nil];
                    return;
                }

                // Set display name
                self.userDisplayName.text = user.displayName ? : @"Mysterious Stranger";
                [self.userDisplayName sizeToFit];

                // AAD users have email in the mail attribute
                // Personal accounts have email in the userPrincipalName attribute
                self.userEmail.text = user.mail ? : user.userPrincipalName;
                [self.userEmail sizeToFit];
            });
         }];
    }

    - (IBAction)signOut {
        [AuthenticationManager.instance signOut];
        [self performSegueWithIdentifier: @"userSignedOut" sender: nil];
    }

    @end
    ```

1. <span data-ttu-id="6e27e-144">Führen Sie die APP aus, melden Sie sich an, und tippen Sie auf die Registerkarte **Kalender** . Die Liste der Ereignisse sollte angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="6e27e-144">Run the app, sign in, and tap the **Calendar** tab. You should see the list of events.</span></span>

    ![Ein Screenshot der Ereignistabelle](./images/calendar-list.png)
