<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e67de-101">In dieser Übung werden Sie die Microsoft-Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="e67de-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="e67de-102">Für diese Anwendung verwenden Sie die [microsoft-graph-client-Bibliothek,](https://github.com/microsoftgraph/msgraph-sdk-javascript) um Anrufe an Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e67de-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="e67de-103">Abrufen von Kalenderereignissen von Outlook</span><span class="sxs-lookup"><span data-stu-id="e67de-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="e67de-104">Öffnen `./src/GraphService.ts` Sie die folgende Funktion, und fügen Sie sie hinzu.</span><span class="sxs-lookup"><span data-stu-id="e67de-104">Open `./src/GraphService.ts` and add the following function.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getUserWeekCalendarSnippet":::

    <span data-ttu-id="e67de-105">Überlegen Sie sich, was dieser Code macht.</span><span class="sxs-lookup"><span data-stu-id="e67de-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="e67de-106">Die URL, die aufgerufen wird, lautet `/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="e67de-106">The URL that will be called is `/me/calendarview`.</span></span>
    - <span data-ttu-id="e67de-107">Die Methode fügt der Anforderung den Header hinzu, sodass sich die Zeiten in der Antwort in der bevorzugten Zeitzone des Benutzers `header` `Prefer: outlook.timezone=""` befinden.</span><span class="sxs-lookup"><span data-stu-id="e67de-107">The `header` method adds the `Prefer: outlook.timezone=""` header to the request, causing the times in the response to be in the user's preferred time zone.</span></span>
    - <span data-ttu-id="e67de-108">Die -Methode fügt die Parameter and hinzu und definiert das `query` `startDateTime` `endDateTime` Zeitfenster für die Kalenderansicht.</span><span class="sxs-lookup"><span data-stu-id="e67de-108">The `query` method adds the `startDateTime` and `endDateTime` parameters, defining the window of time for the calendar view.</span></span>
    - <span data-ttu-id="e67de-109">Die Methode beschränkt die für jedes Ereignis zurückgegebenen Felder auf die Felder, die `select` von der Ansicht tatsächlich verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e67de-109">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="e67de-110">Die Methode sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der sie erstellt wurden, und das letzte `orderby` Element ist zuerst.</span><span class="sxs-lookup"><span data-stu-id="e67de-110">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>
    - <span data-ttu-id="e67de-111">Die `top` Methode beschränkt die Ergebnisse einer einzelnen Seite auf 25 Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="e67de-111">The `top` method limits the results in a single page to 25 events.</span></span>
    - <span data-ttu-id="e67de-112">Wenn die Antwort einen Wert enthält, der angibt, dass mehr Ergebnisse verfügbar sind, wird ein Objekt verwendet, um die Auflistung zu pagen, um `@odata.nextLink` `PageIterator` alle Ergebnisse zu erhalten. [](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript)</span><span class="sxs-lookup"><span data-stu-id="e67de-112">If the response contains an `@odata.nextLink` value, indicating there are more results available, a `PageIterator` object is used to [page through the collection](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript) to get all of the results.</span></span>

1. <span data-ttu-id="e67de-113">Erstellen Sie React Komponente zum Anzeigen der Ergebnisse des Anrufs.</span><span class="sxs-lookup"><span data-stu-id="e67de-113">Create a React component to display the results of the call.</span></span> <span data-ttu-id="e67de-114">Erstellen Sie eine neue Datei im `./src` Verzeichnis `Calendar.tsx` namens, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="e67de-114">Create a new file in the `./src` directory named `Calendar.tsx` and add the following code.</span></span>

    ```typescript
    import React from 'react';
    import { NavLink as RouterNavLink } from 'react-router-dom';
    import { Table } from 'reactstrap';
    import moment, { Moment } from 'moment-timezone';
    import { findIana } from "windows-iana";
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
        if (this.props.user && !this.state.eventsLoaded)
        {
          try {
            // Get the user's access token
            var accessToken = await this.props.getAccessToken(config.scopes);

            // Convert user's Windows time zone ("Pacific Standard Time")
            // to IANA format ("America/Los_Angeles")
            // Moment needs IANA format
            var ianaTimeZones = findIana(this.props.user.timeZone);

            // Get midnight on the start of the current week in the user's timezone,
            // but in UTC. For example, for Pacific Standard Time, the time value would be
            // 07:00:00Z
            var startOfWeek = moment.tz(ianaTimeZones![0].valueOf()).startOf('week').utc();

            // Get the user's events
            var events = await getUserWeekCalendar(accessToken, this.props.user.timeZone, startOfWeek);

            // Update the array of events in state
            this.setState({
              eventsLoaded: true,
              events: events,
              startOfWeek: startOfWeek
            });
          }
          catch (err) {
            this.props.setError('ERROR', JSON.stringify(err));
          }
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

    <span data-ttu-id="e67de-115">Damit wird derzeit nur das Array von Ereignissen in JSON auf der Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="e67de-115">For now this just renders the array of events in JSON on the page.</span></span>

1. <span data-ttu-id="e67de-116">Fügen Sie diese neue Komponente der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="e67de-116">Add this new component to the app.</span></span> <span data-ttu-id="e67de-117">Öffnen `./src/App.tsx` Sie die folgende `import` Anweisung, und fügen Sie sie am oberen Rand der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="e67de-117">Open `./src/App.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import Calendar from './Calendar';
    ```

1. <span data-ttu-id="e67de-118">Fügen Sie die folgende Komponente direkt hinter der vorhandenen `<Route>` hinzu.</span><span class="sxs-lookup"><span data-stu-id="e67de-118">Add the following component just after the existing `<Route>`.</span></span>

    ```typescript
    <Route exact path="/calendar"
      render={(props) =>
        this.props.isAuthenticated ?
          <Calendar {...props} /> :
          <Redirect to="/" />
      } />
    ```

1. <span data-ttu-id="e67de-119">Speichern Sie die Änderungen, und starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="e67de-119">Save your changes and restart the app.</span></span> <span data-ttu-id="e67de-120">Melden Sie sich an, **und** klicken Sie in der Navigationsleiste auf den Link Kalender.</span><span class="sxs-lookup"><span data-stu-id="e67de-120">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="e67de-121">Wenn alles funktioniert, sollte ein JSON-Abbild von Ereignissen im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="e67de-121">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="e67de-122">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="e67de-122">Display the results</span></span>

<span data-ttu-id="e67de-123">Jetzt können Sie die Komponente so aktualisieren, dass die Ereignisse `Calendar` benutzerfreundlicher angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="e67de-123">Now you can update the `Calendar` component to display the events in a more user-friendly manner.</span></span>

1. <span data-ttu-id="e67de-124">Erstellen Sie eine neue Datei im `./src` Verzeichnis `Calendar.css` namens, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="e67de-124">Create a new file in the `./src` directory named `Calendar.css` and add the following code.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/Calendar.css":::

1. <span data-ttu-id="e67de-125">Erstellen Sie React Komponente, um Ereignisse an einem einzelnen Tag als Tabellenzeilen zu rendern.</span><span class="sxs-lookup"><span data-stu-id="e67de-125">Create a React component to render events in a single day as table rows.</span></span> <span data-ttu-id="e67de-126">Erstellen Sie eine neue Datei im `./src` Verzeichnis `CalendarDayRow.tsx` namens, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="e67de-126">Create a new file in the `./src` directory named `CalendarDayRow.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/CalendarDayRow.tsx" id="CalendarDayRowSnippet":::

1. <span data-ttu-id="e67de-127">Fügen Sie die folgenden `import` Anweisungen oben in **Calendar.tsx hinzu.**</span><span class="sxs-lookup"><span data-stu-id="e67de-127">Add the following `import` statements to the top of **Calendar.tsx**.</span></span>

    ```typescript
    import CalendarDayRow from './CalendarDayRow';
    import './Calendar.css';
    ```

1. <span data-ttu-id="e67de-128">Ersetzen Sie die `render` vorhandene Funktion in durch die folgende `./src/Calendar.tsx` Funktion.</span><span class="sxs-lookup"><span data-stu-id="e67de-128">Replace the existing `render` function in `./src/Calendar.tsx` with the following function.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    <span data-ttu-id="e67de-129">Dadurch werden die Ereignisse in ihre jeweiligen Tage aufgeteilt und ein Tabellenabschnitt für jeden Tag gerendert.</span><span class="sxs-lookup"><span data-stu-id="e67de-129">This splits the events into their respective days and renders a table section for each day.</span></span>

1. <span data-ttu-id="e67de-130">Speichern Sie die Änderungen, und starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="e67de-130">Save the changes and restart the app.</span></span> <span data-ttu-id="e67de-131">Klicken Sie auf den **Link Kalender,** und die App sollte nun eine Tabelle mit Ereignissen rendern.</span><span class="sxs-lookup"><span data-stu-id="e67de-131">Click on the **Calendar** link and the app should now render a table of events.</span></span>

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/add-msgraph-01.png)
