# Historic Glucose Chart

The `Historic glucose` component displays a line chart that visualizes historic glucose data. It allows the user to view glucose data points and collect information on previous BG levels.

## API calls

| Route | Values | function |
|--- |--- |--- |
| monitor/{date}/{id} | date : The selected datestamp | Gets the Glucose data from the DB corresponding with the user
|-|id: The user_id of the logged in |  |


## State Variables

- `jsonData`: Stores the fetched data from the API.
- `buttonClicked`: Tracks whether the fetch data button has been clicked.
- `selectedDate`: Stores the selected date in the format "DD-MM-YYYY".
- `percentage`: Stores the calculated percentage value.


## handlers

- `handleButtonClick`: Event handler for button click, that then gets calls the useeffect state to get the data.
- `handleChangeDate`: handles the state of the selected date and updates whenever a new date is selected.

## Methods
- `calculatePercentageInRange`: This methode lets the user see the percentage he/she was in range with their glucose levels for that day. 
- `fetchData`: This useEffect hook is responsible for fetching the historic glucose data from the server when the component is mounted or when the selectedDate state variable changes. It calls the fetchData function, which performs an asynchronous request to the server using the axios library. The fetched data is then stored in the `fetchData` a metohde variable getting the glucose data from the database based on the date and id the user provides.
```
 useEffect(() => {
    if (buttonClicked) {
      const fetchData = async () => {
        const response = await axios.get(`http://localhost/api/monitor/${selectedDate}/`+ id, {
          headers: {
            Authorization: `Bearer ${token}`, 
          },
        });
        setJsonData(response.data);
      };
      fetchData();
    }
  }, [buttonClicked, selectedDate, id]);
```
- `getPointBackgroundColor`: Determines the background color of a data point on the chart based on its value or presence of a value in Insuline and Carbs.
``` const getPointBackgroundColor = (data) => {
    const value = data.dataset.data[data.dataIndex];
    if (jsonData[data.dataIndex]['FastInsulineUnit'] > 0) {
      return 'green';
    }
    if (value > 10 || value < 5 ) {
      return 'red';
    }
    return 'blue';
  };
  ```
- `labels`: An array of labels representing every minute in a day.
- `chartOptions`: Options for configuring the chart, including scales and tooltips.
- `data`: Data object for the chart, including labels and datasets.

## Rendered Output

The component renders the following:

- A date picker to select the desired date.
- A dropdown to select a specific glucose data point for editing.
- Buttons to fetch prediction data, add new data, and open/close the form.
- The chart component displaying the glucose data.

The component utilizes various event handlers and lifecycle methods, such as `useEffect`, to fetch data, update the state, and interact with the server.

