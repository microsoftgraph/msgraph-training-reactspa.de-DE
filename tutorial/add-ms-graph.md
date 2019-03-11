<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="86f53-101">In dieser Übung integrieren Sie Microsoft Graph in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="86f53-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="86f53-102">Für diese Anwendung verwenden Sie die [Microsoft-Graph-Client-](https://github.com/microsoftgraph/msgraph-sdk-javascript) Bibliothek, um Aufrufe von Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="86f53-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="86f53-103">Abrufen von Kalenderereignissen aus Outlook</span><span class="sxs-lookup"><span data-stu-id="86f53-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="86f53-104">Beginnen Sie mit dem Hinzufügen einer neuen `./src/GraphService.js` Methode zur Datei, um die Ereignisse aus dem Kalender abzurufen.</span><span class="sxs-lookup"><span data-stu-id="86f53-104">Start by adding a new method to the `./src/GraphService.js` file to get the events from the calendar.</span></span> <span data-ttu-id="86f53-105">Fügen Sie die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="86f53-105">Add the following function.</span></span>

```js
export async function getEvents(accessToken) {
  const client = getAuthenticatedClient(accessToken);

  const events = await client
    .api('/me/events')
    .select('subject,organizer,start,end')
    .orderby('createdDateTime DESC')
    .get();

  return events;
}
```

<span data-ttu-id="86f53-106">Überlegen Sie sich, was dieser Code tut.</span><span class="sxs-lookup"><span data-stu-id="86f53-106">Consider what this code is doing.</span></span>

- <span data-ttu-id="86f53-107">Die URL, die aufgerufen wird, `/me/events`lautet.</span><span class="sxs-lookup"><span data-stu-id="86f53-107">The URL that will be called is `/me/events`.</span></span>
- <span data-ttu-id="86f53-108">Die `select` -Methode schränkt die für jedes Ereignis zurückgegebenen Felder auf nur die ein, die die Ansicht tatsächlich verwendet.</span><span class="sxs-lookup"><span data-stu-id="86f53-108">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="86f53-109">Die `orderby` -Methode sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu denen Sie erstellt wurden, wobei das neueste Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="86f53-109">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="86f53-110">Erstellen Sie nun eine reaktionskomponente, um die Ergebnisse des Anrufs anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="86f53-110">Now create a React component to display the results of the call.</span></span> <span data-ttu-id="86f53-111">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `Calendar.js` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="86f53-111">Create a new file in the `./src` directory named `Calendar.js` and add the following code.</span></span>

```JSX
import React from 'react';
import { Table } from 'reactstrap';
import moment from 'moment';
import config from './Config';
import { getEvents } from './GraphService';

// Helper function to format Graph date/time
function formatDateTime(dateTime) {
  return moment.utc(dateTime).local().format('M/D/YY h:mm A');
}

export default class Calendar extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      events: []
    };
  }

  async componentDidMount() {
    try {
      // Get the user's access token
      var accessToken = await window.msal.acquireTokenSilent(config.scopes);
      // Get the user's events
      var events = await getEvents(accessToken);
      // Update the array of events in state
      this.setState({events: events.value});
    }
    catch(err) {
      this.props.showError('ERROR', JSON.stringify(err));
    }
  }

  render() {
    return (
      <pre><code>{JSON.stringify(this.state.events, null, 2)}</code></pre>
    );
  }
}
```

<span data-ttu-id="86f53-112">Im Moment wird dadurch nur das Array von Ereignissen in JSON auf der Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="86f53-112">For now this just renders the array of events in JSON on the page.</span></span> <span data-ttu-id="86f53-113">Fügen Sie diese neue Komponente der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="86f53-113">Add this new component to the app.</span></span> <span data-ttu-id="86f53-114">Öffnen `./src/App.js` Sie und fügen Sie `import` die folgende Anweisung am Anfang der Datei.</span><span class="sxs-lookup"><span data-stu-id="86f53-114">Open `./src/App.js` and add the following `import` statement to the top of the file.</span></span>

```js
import Calendar from './Calendar';
```

<span data-ttu-id="86f53-115">Fügen Sie dann die folgende Komponente direkt nach dem `<Route>`vorhandenen hinzu.</span><span class="sxs-lookup"><span data-stu-id="86f53-115">Then add the following component just after the existing `<Route>`.</span></span>

```JSX
<Route exact path="/calendar"
  render={(props) =>
    <Calendar {...props}
      showError={this.setErrorMessage.bind(this)} />
  } />
```

<span data-ttu-id="86f53-116">Speichern Sie die Änderungen, und starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="86f53-116">Save your changes and restart the app.</span></span> <span data-ttu-id="86f53-117">Melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="86f53-117">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="86f53-118">Wenn alles funktioniert, sollte ein JSON-Dump von Ereignissen im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="86f53-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="86f53-119">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="86f53-119">Display the results</span></span>

<span data-ttu-id="86f53-120">Jetzt können Sie die `Calendar` Komponente aktualisieren, um die Ereignisse benutzerfreundlicher anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="86f53-120">Now you can update the `Calendar` component to display the events in a more user-friendly manner.</span></span> <span data-ttu-id="86f53-121">Ersetzen Sie die `render` vorhandene Funktion `./src/Calendar.js` in durch die folgende Funktion.</span><span class="sxs-lookup"><span data-stu-id="86f53-121">Replace the existing `render` function in `./src/Calendar.js` with the following function.</span></span>

```JSX
render() {
  return (
    <div>
      <h1>Calendar</h1>
      <Table>
        <thead>
          <tr>
            <th scope="col">Organizer</th>
            <th scope="col">Subject</th>
            <th scope="col">Start</th>
            <th scope="col">End</th>
          </tr>
        </thead>
        <tbody>
          {this.state.events.map(
            function(event, index){
              return(
                <tr>
                  <td>{event.organizer.emailAddress.name}</td>
                  <td>{event.subject}</td>
                  <td>{formatDateTime(event.start.dateTime)}</td>
                  <td>{formatDateTime(event.end.dateTime)}</td>
                </tr>
              );
            })}
        </tbody>
      </Table>
    </div>
  );
}
```

<span data-ttu-id="86f53-122">Dadurch wird die Auflistung der Ereignisse durchlaufen und für jeden eine Tabellenzeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="86f53-122">This loops through the collection of events and adds a table row for each one.</span></span> <span data-ttu-id="86f53-123">Speichern Sie die Änderungen, und starten Sie die APP neu.</span><span class="sxs-lookup"><span data-stu-id="86f53-123">Save the changes and restart the app.</span></span> <span data-ttu-id="86f53-124">Klicken Sie auf den Link **Kalender** , und die APP sollte jetzt eine Tabelle mit Ereignissen rendern.</span><span class="sxs-lookup"><span data-stu-id="86f53-124">Click on the **Calendar** link and the app should now render a table of events.</span></span>

![Screenshot der Ereignistabelle](./images/add-msgraph-01.png)