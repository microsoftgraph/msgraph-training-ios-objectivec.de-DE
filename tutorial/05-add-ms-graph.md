<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b86ab-101">In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="b86ab-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="b86ab-102">Für diese Anwendung verwenden Sie das [Microsoft Graph-SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) , um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="b86ab-102">For this application, you will use the [Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="b86ab-103">Abrufen von Kalenderereignissen von Outlook</span><span class="sxs-lookup"><span data-stu-id="b86ab-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="b86ab-104">In diesem Abschnitt erweitern Sie die `GraphManager` Klasse so, dass eine Funktion hinzugefügt wird, um die Ereignisse `CalendarViewController` des Benutzers abzurufen und diese neuen Funktionen zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b86ab-104">In this section you will extend the `GraphManager` class to add a function to get the user's events and update `CalendarViewController` to use these new functions.</span></span>

1. <span data-ttu-id="b86ab-105">Öffnen Sie **graphmanager. h** , und fügen Sie den folgenden `@interface` Code oberhalb der Deklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="b86ab-105">Open **GraphManager.h** and add the following code above the `@interface` declaration.</span></span>

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSData* _Nullable data, NSError* _Nullable error);
    ```

1. <span data-ttu-id="b86ab-106">Fügen Sie der `@interface` Deklaration den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="b86ab-106">Add the following code to the `@interface` declaration.</span></span>

    ```objc
    - (void) getEventsWithCompletionBlock: (GetEventsCompletionBlock)completionBlock;
    ```

1. <span data-ttu-id="b86ab-107">Öffnen Sie **graphmanager. m** , und fügen Sie der `GraphManager` Klasse die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="b86ab-107">Open **GraphManager.m** and add the following function to the `GraphManager` class.</span></span>

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
    > <span data-ttu-id="b86ab-108">Überprüfen Sie, was `getEventsWithCompletionBlock` der Code in tut.</span><span class="sxs-lookup"><span data-stu-id="b86ab-108">Consider what the code in `getEventsWithCompletionBlock` is doing.</span></span>
    >
    > - <span data-ttu-id="b86ab-109">Die URL, die aufgerufen wird, lautet `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="b86ab-109">The URL that will be called is `/v1.0/me/events`.</span></span>
    > - <span data-ttu-id="b86ab-110">Der `select` Abfrageparameter schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf diejenigen ein, die von der APP tatsächlich verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b86ab-110">The `select` query parameter limits the fields returned for each events to just those the app will actually use.</span></span>
    > - <span data-ttu-id="b86ab-111">Der `orderby` Abfrageparameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="b86ab-111">The `orderby` query parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="b86ab-112">Öffnen Sie **CalendarViewController. m** , und ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="b86ab-112">Open **CalendarViewController.m** and replace its entire contents with the following code.</span></span>

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

1. <span data-ttu-id="b86ab-113">Führen Sie die APP aus, melden Sie sich an, und tippen Sie im Menü auf das Navigationselement **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="b86ab-113">Run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="b86ab-114">Es sollte ein JSON-Dump der Ereignisse in der App angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="b86ab-114">You should see a JSON dump of the events in the app.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="b86ab-115">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="b86ab-115">Display the results</span></span>

<span data-ttu-id="b86ab-116">Nun können Sie das JSON-Dump durch etwas ersetzen, um die Ergebnisse auf eine benutzerfreundliche Weise anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b86ab-116">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="b86ab-117">In diesem Abschnitt ändern Sie die `getEventsWithCompletionBlock` Funktion so, dass stark typisierte Objekte zurückgegeben werden, `CalendarViewController` und ändern, um die Ereignisse mithilfe einer Tabellenansicht zu rendern.</span><span class="sxs-lookup"><span data-stu-id="b86ab-117">In this section, you will modify the `getEventsWithCompletionBlock` function to return strongly-typed objects, and modify `CalendarViewController` to use a table view to render the events.</span></span>

### <a name="update-getevents"></a><span data-ttu-id="b86ab-118">Aktualisieren von GetEvents</span><span class="sxs-lookup"><span data-stu-id="b86ab-118">Update getEvents</span></span>

1. <span data-ttu-id="b86ab-119">Öffnen Sie **graphmanager. h**.</span><span class="sxs-lookup"><span data-stu-id="b86ab-119">Open **GraphManager.h**.</span></span> <span data-ttu-id="b86ab-120">Ändern Sie `GetEventsCompletionBlock` die Typdefinition in Folgendes.</span><span class="sxs-lookup"><span data-stu-id="b86ab-120">Change the `GetEventsCompletionBlock` type definition to the following.</span></span>

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSArray<MSGraphEvent*>* _Nullable events, NSError* _Nullable error);
    ```

1. <span data-ttu-id="b86ab-121">Öffnen Sie **graphmanager. m**.</span><span class="sxs-lookup"><span data-stu-id="b86ab-121">Open **GraphManager.m**.</span></span> <span data-ttu-id="b86ab-122">Ersetzen Sie `completionBlock(data, nil);` die- `getEventsWithCompletionBlock` Funktion durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="b86ab-122">Replace the `completionBlock(data, nil);` line in the `getEventsWithCompletionBlock` function with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphManager.m" id="GetEventsSnippet" highlight="24-43":::

### <a name="update-calendarviewcontroller"></a><span data-ttu-id="b86ab-123">CalendarViewController aktualisieren</span><span class="sxs-lookup"><span data-stu-id="b86ab-123">Update CalendarViewController</span></span>

1. <span data-ttu-id="b86ab-124">Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `CalendarTableViewCell`Projekt mit dem Namen.</span><span class="sxs-lookup"><span data-stu-id="b86ab-124">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewCell`.</span></span> <span data-ttu-id="b86ab-125">Wählen Sie **UITableViewCell** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="b86ab-125">Choose **UITableViewCell** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="b86ab-126">Öffnen Sie **CalendarTableViewCell. h** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="b86ab-126">Open **CalendarTableViewCell.h** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.h" id="CalendarTableCellSnippet":::

1. <span data-ttu-id="b86ab-127">Öffnen Sie **CalendarTableViewCell. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="b86ab-127">Open **CalendarTableViewCell.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.m" id="CalendarTableCellSnippet":::

1. <span data-ttu-id="b86ab-128">Öffnen Sie **Main. Storyboard** , und suchen Sie die **Kalender Szene**.</span><span class="sxs-lookup"><span data-stu-id="b86ab-128">Open **Main.storyboard** and locate the **Calendar Scene**.</span></span> <span data-ttu-id="b86ab-129">Wählen Sie die **Ansicht** in der **Kalender Szene** aus, und löschen Sie Sie.</span><span class="sxs-lookup"><span data-stu-id="b86ab-129">Select the **View** in the **Calendar Scene** and delete it.</span></span>

    ![Screenshot der Ansicht in der Kalender Szene](./images/view-in-calendar-scene.png)

1. <span data-ttu-id="b86ab-131">Hinzufügen einer **Tabellenansicht** aus der **Bibliothek** zur **Kalender Szene**.</span><span class="sxs-lookup"><span data-stu-id="b86ab-131">Add a **Table View** from the **Library** to the **Calendar Scene**.</span></span>
1. <span data-ttu-id="b86ab-132">Wählen Sie die Tabellenansicht aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="b86ab-132">Select the table view, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="b86ab-133">Legen Sie **Prototype Cells** auf **1**fest.</span><span class="sxs-lookup"><span data-stu-id="b86ab-133">Set **Prototype Cells** to **1**.</span></span>
1. <span data-ttu-id="b86ab-134">Verwenden Sie die **Bibliothek** , um der Zelle Prototyp drei **Beschriftungen** hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b86ab-134">Use the **Library** to add three **Labels** to the prototype cell.</span></span>
1. <span data-ttu-id="b86ab-135">Wählen Sie die Zelle Prototyp aus, und wählen Sie dann den **Identitäts Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="b86ab-135">Select the prototype cell, then select the **Identity Inspector**.</span></span> <span data-ttu-id="b86ab-136">Ändern Sie die **Klasse** in **CalendarTableViewCell**.</span><span class="sxs-lookup"><span data-stu-id="b86ab-136">Change **Class** to **CalendarTableViewCell**.</span></span>
1. <span data-ttu-id="b86ab-137">Wählen Sie den **Attributes Inspector** aus, `EventCell`und legen Sie **Identifier** auf fest.</span><span class="sxs-lookup"><span data-stu-id="b86ab-137">Select the **Attributes Inspector** and set **Identifier** to `EventCell`.</span></span>
1. <span data-ttu-id="b86ab-138">Wählen Sie mit ausgewähltem **EventCell** den **Verbindungs Inspektor** aus, `durationLabel`und `organizerLabel`verbinden `subjectLabel` Sie die Beschriftungen, die Sie der Zelle im Storyboard hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="b86ab-138">With the **EventCell** selected, select the **Connections Inspector** and connect `durationLabel`, `organizerLabel`, and `subjectLabel` to the labels you added to the cell on the storyboard.</span></span>
1. <span data-ttu-id="b86ab-139">Legen Sie die Eigenschaften und Einschränkungen für die drei Beschriftungen wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="b86ab-139">Set the properties and constraints on the three labels as follows.</span></span>

    - <span data-ttu-id="b86ab-140">**Betreff-Bezeichnung**</span><span class="sxs-lookup"><span data-stu-id="b86ab-140">**Subject Label**</span></span>
        - <span data-ttu-id="b86ab-141">Add Constraint: Leading Space to Content View Leading Margin, Value: 0</span><span class="sxs-lookup"><span data-stu-id="b86ab-141">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="b86ab-142">Add Constraint: nachgestellter Raum zur Inhaltsansicht nach folgender Rand, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="b86ab-142">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="b86ab-143">Hinzufügen von Einschränkungen: oberer Bereich für oberer Rand der Inhaltsansicht, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="b86ab-143">Add constraint: Top space to Content View Top Margin, value: 0</span></span>
    - <span data-ttu-id="b86ab-144">**Organizer-Bezeichnung**</span><span class="sxs-lookup"><span data-stu-id="b86ab-144">**Organizer Label**</span></span>
        - <span data-ttu-id="b86ab-145">Schriftart: System 12,0</span><span class="sxs-lookup"><span data-stu-id="b86ab-145">Font: System 12.0</span></span>
        - <span data-ttu-id="b86ab-146">Add Constraint: Leading Space to Content View Leading Margin, Value: 0</span><span class="sxs-lookup"><span data-stu-id="b86ab-146">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="b86ab-147">Add Constraint: nachgestellter Raum zur Inhaltsansicht nach folgender Rand, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="b86ab-147">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="b86ab-148">Add Constraint: Top Space to subject Label Bottom, Value: Standard</span><span class="sxs-lookup"><span data-stu-id="b86ab-148">Add constraint: Top space to Subject Label Bottom, value: Standard</span></span>
    - <span data-ttu-id="b86ab-149">**Dauer Bezeichnung**</span><span class="sxs-lookup"><span data-stu-id="b86ab-149">**Duration Label**</span></span>
        - <span data-ttu-id="b86ab-150">Schriftart: System 12,0</span><span class="sxs-lookup"><span data-stu-id="b86ab-150">Font: System 12.0</span></span>
        - <span data-ttu-id="b86ab-151">Farbe: dunkle graue Farbe</span><span class="sxs-lookup"><span data-stu-id="b86ab-151">Color: Dark Gray Color</span></span>
        - <span data-ttu-id="b86ab-152">Add Constraint: Leading Space to Content View Leading Margin, Value: 0</span><span class="sxs-lookup"><span data-stu-id="b86ab-152">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="b86ab-153">Add Constraint: nachgestellter Raum zur Inhaltsansicht nach folgender Rand, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="b86ab-153">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="b86ab-154">Add Constraint: Top Space to Organizer Label Bottom, Value: Standard</span><span class="sxs-lookup"><span data-stu-id="b86ab-154">Add constraint: Top space to Organizer Label Bottom, value: Standard</span></span>
        - <span data-ttu-id="b86ab-155">Add Constraint: Bottom Space to Content View Bottom Margin, Value: 8</span><span class="sxs-lookup"><span data-stu-id="b86ab-155">Add constraint: Bottom space to Content View Bottom Margin, value: 8</span></span>

    ![Screenshot des Zellenlayouts für Prototypen](./images/prototype-cell-layout.png)

1. <span data-ttu-id="b86ab-157">Öffnen Sie **CalendarViewController. h** , und `calendarJSON` entfernen Sie die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b86ab-157">Open **CalendarViewController.h** and remove the `calendarJSON` property.</span></span>
1. <span data-ttu-id="b86ab-158">Ändern Sie `@interface` die Deklaration in Folgendes.</span><span class="sxs-lookup"><span data-stu-id="b86ab-158">Change the `@interface` declaration to the following.</span></span>

    ```objc
    @interface CalendarViewController : UITableViewController
    ```

1. <span data-ttu-id="b86ab-159">Öffnen Sie **CalendarViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="b86ab-159">Open **CalendarViewController.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.m" id="CalendarViewSnippet":::

1. <span data-ttu-id="b86ab-160">Führen Sie die APP aus, melden Sie sich an, und tippen Sie auf die Registerkarte **Kalender** . Die Liste der Ereignisse sollte angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="b86ab-160">Run the app, sign in, and tap the **Calendar** tab. You should see the list of events.</span></span>

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/calendar-list.png)
