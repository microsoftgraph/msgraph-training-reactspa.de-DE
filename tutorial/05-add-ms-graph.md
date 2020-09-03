<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="05289-101">In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="05289-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="05289-102">Für diese Anwendung verwenden Sie die [Microsoft-Graph-Client-](https://github.com/microsoftgraph/msgraph-sdk-javascript) Bibliothek, um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="05289-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="05289-103">Abrufen von Kalenderereignissen von Outlook</span><span class="sxs-lookup"><span data-stu-id="05289-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="05289-104">Öffnen `./src/GraphService.ts` Sie und fügen Sie die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="05289-104">Open `./src/GraphService.ts` and add the following function.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getUserWeekCalendarSnippet":::

    <span data-ttu-id="05289-105">Überlegen Sie sich, was dieser Code macht.</span><span class="sxs-lookup"><span data-stu-id="05289-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="05289-106">Die URL, die aufgerufen wird, lautet `/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="05289-106">The URL that will be called is `/me/calendarview`.</span></span>
    - <span data-ttu-id="05289-107">Die `header` -Methode fügt die `Prefer: outlook.timezone=""` Kopfzeile der Anforderung hinzu, wodurch sich die Zeiten in der Antwort in der bevorzugten Zeitzone des Benutzers befinden.</span><span class="sxs-lookup"><span data-stu-id="05289-107">The `header` method adds the `Prefer: outlook.timezone=""` header to the request, causing the times in the response to be in the user's preferred time zone.</span></span>
    - <span data-ttu-id="05289-108">Die `query` -Methode fügt die `startDateTime` Parameter and hinzu und `endDateTime` definiert das Zeitfenster für die Kalenderansicht.</span><span class="sxs-lookup"><span data-stu-id="05289-108">The `query` method adds the `startDateTime` and `endDateTime` parameters, defining the window of time for the calendar view.</span></span>
    - <span data-ttu-id="05289-109">Die `select` -Methode schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf genau diejenigen ein, die die Ansicht tatsächlich verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="05289-109">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="05289-110">Die `orderby` Methode sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="05289-110">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>
    - <span data-ttu-id="05289-111">Die `top` Methode schränkt die Ergebnisse auf die ersten 50-Ereignisse ein.</span><span class="sxs-lookup"><span data-stu-id="05289-111">The `top` method limits the results to the first 50 events.</span></span>
    - <span data-ttu-id="05289-112">Wenn die Antwort einen `@odata.nextLink` Wert enthält, der angibt, dass weitere Ergebnisse zur Verfügung stehen, wird ein `PageIterator` Objekt verwendet, um [durch die Auflistung zu blättern](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript) , um alle Ergebnisse abzurufen.</span><span class="sxs-lookup"><span data-stu-id="05289-112">If the response contains an `@odata.nextLink` value, indicating there are more results available, a `PageIterator` object is used to [page through the collection](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript) to get all of the results.</span></span>

1. <span data-ttu-id="05289-113">Erstellen Sie eine reaktionskomponente, um die Ergebnisse des Anrufs anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="05289-113">Create a React component to display the results of the call.</span></span> <span data-ttu-id="05289-114">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `Calendar.tsx` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="05289-114">Create a new file in the `./src` directory named `Calendar.tsx` and add the following code.</span></span>

    ```typescript
    import React from 'react';
    import { NavLink as RouterNavLink } from 'react-router-dom';
    import { Table } from 'reactstrap';
    import moment from 'moment-timezone';
    import { findOneIana } from "windows-iana";
    import { Event } from 'microsoft-graph';
    import { config } from './Config';
    import { getUserWeekCalendar } from './GraphService';
    import withAuthProvider, { AuthComponentProps } from './AuthProvider';

    interface CalendarState {
      eventsLoaded: boolean;
      events: Event[];
      startOfWeek: Moment | undefined;
    }

    class Calendar extends React.Component<AuthComponentProps, CalendarState> {
      constructor(props: any) {
        super(props);

        this.state = {
          eventsLoaded: false,
          events: [],
          startOfWeek: undefined
        };
      }

      async componentDidUpdate() {
        try {
          // Get the user's access token
          var accessToken = await this.props.getAccessToken(config.scopes);
          // Convert user's Windows time zone ("Pacific Standard Time")
          // to IANA format ("America/Los_Angeles")
          // Moment needs IANA format
          var ianaTimeZone = findOneIana(this.props.user.timeZone);

          // Get midnight on the start of the current week in the user's timezone,
          // but in UTC. For example, for Pacific Standard Time, the time value would be
          // 07:00:00Z
          var startOfWeek = moment.tz(ianaTimeZone!.valueOf()).startOf('week').utc();

          // Get the user's events
          var events = await getUserWeekCalendar(accessToken, this.props.user.timeZone, startOfWeek);

          // Update the array of events in state
          this.setState({
            eventsLoaded: true,
            events: events,
            startOfWeek: startOfWeek
          });
        }
        catch(err) {
          this.props.setError('ERROR', JSON.stringify(err));
        }
      }

      render() {
        return (
          <pre><code>{JSON.stringify(this.state.events, null, 2)}</code></pre>
        );
      }
    }

    export default withAuthProvider(Calendar);
    ```

    <span data-ttu-id="05289-115">Im Moment wird dadurch nur das Array von Ereignissen in JSON auf der Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="05289-115">For now this just renders the array of events in JSON on the page.</span></span>

1. <span data-ttu-id="05289-116">Fügen Sie diese neue Komponente zur APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="05289-116">Add this new component to the app.</span></span> <span data-ttu-id="05289-117">Öffnen `./src/App.tsx` Sie und fügen Sie die folgende `import` Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="05289-117">Open `./src/App.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import Calendar from './Calendar';
    ```

1. <span data-ttu-id="05289-118">Fügen Sie die folgende Komponente unmittelbar nach dem vorhandenen hinzu `<Route>` .</span><span class="sxs-lookup"><span data-stu-id="05289-118">Add the following component just after the existing `<Route>`.</span></span>

    ```typescript
    <Route exact path="/calendar"
      render={(props) =>
        this.props.isAuthenticated ?
          <Calendar {...props} /> :
          <Redirect to="/" />
      } />
    ```

1. <span data-ttu-id="05289-119">Speichern Sie die Änderungen, und starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="05289-119">Save your changes and restart the app.</span></span> <span data-ttu-id="05289-120">Melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="05289-120">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="05289-121">Wenn alles funktioniert, sollte ein JSON-Abbild von Ereignissen im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="05289-121">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="05289-122">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="05289-122">Display the results</span></span>

<span data-ttu-id="05289-123">Jetzt können Sie die `Calendar` Komponente aktualisieren, um die Ereignisse auf benutzerfreundlichere Weise anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="05289-123">Now you can update the `Calendar` component to display the events in a more user-friendly manner.</span></span>

1. <span data-ttu-id="05289-124">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `Calendar.css` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="05289-124">Create a new file in the `./src` directory named `Calendar.css` and add the following code.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/Calendar.css":::

1. <span data-ttu-id="05289-125">Erstellen Sie eine reaktionskomponente zum Rendern von Ereignissen an einem einzelnen Tag als Tabellenzeilen.</span><span class="sxs-lookup"><span data-stu-id="05289-125">Create a React component to render events in a single day as table rows.</span></span> <span data-ttu-id="05289-126">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `CalendarDayRow.tsx` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="05289-126">Create a new file in the `./src` directory named `CalendarDayRow.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/CalendarDayRow.tsx" id="CalendarDayRowSnippet":::

1. <span data-ttu-id="05289-127">Fügen Sie die folgenden `import` Anweisungen am Anfang von **Calendar. TSX**hinzu.</span><span class="sxs-lookup"><span data-stu-id="05289-127">Add the following `import` statements to the top of **Calendar.tsx**.</span></span>

    ```typescript
    import CalendarDayRow from './CalendarDayRow';
    import './Calendar.css';
    ```

1. <span data-ttu-id="05289-128">Ersetzen Sie die vorhandene `render` Funktion in `./src/Calendar.tsx` durch die folgende Funktion.</span><span class="sxs-lookup"><span data-stu-id="05289-128">Replace the existing `render` function in `./src/Calendar.tsx` with the following function.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    <span data-ttu-id="05289-129">Dadurch werden die Ereignisse in ihre jeweiligen Tage aufgeteilt und ein Tabellenabschnitt für jeden Tag gerendert.</span><span class="sxs-lookup"><span data-stu-id="05289-129">This splits the events into their respective days and renders a table section for each day.</span></span>

1. <span data-ttu-id="05289-130">Speichern Sie die Änderungen, und starten Sie die APP neu.</span><span class="sxs-lookup"><span data-stu-id="05289-130">Save the changes and restart the app.</span></span> <span data-ttu-id="05289-131">Klicken Sie auf den Link **Kalender** , und die APP sollte jetzt eine Tabelle mit Ereignissen rendern.</span><span class="sxs-lookup"><span data-stu-id="05289-131">Click on the **Calendar** link and the app should now render a table of events.</span></span>

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/add-msgraph-01.png)
