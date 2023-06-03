# Glucose Chart

The `GlucoseChart` component displays a line chart that visualizes historic glucose data. It allows the user to view and edit glucose data points, add new data points, and delete existing data points.

## API calls

| Route | Values | function |
|--- |--- |--- |
| monitor/{date}/{id} | date : The selected datestamp | Gets the Glucose data from the DB corresponding with the user
|-|id: The user_id of the logged in |  |
| monitor/py/update/{id} | id: The user_id of the logged in| Updates the Glucose data with the updated Json array in the DB
| - | | Can also be used for the `handleDeleteData` function |
| monitor/py/prediction |  | Get a BG prediction from the Python API through the JavaSpring API
| - | | Uses the last know point in the JSON data Array |

## State Variables

- `glucoseData`: An array that holds the historic glucose data.
- `selectedDate`: A string representing the selected date for displaying the glucose data.
- `predictionData`: A variable to store the predicted glucose data.
- `timestampToDelete`: A timestamp of the data point to delete.
- `valueCarb`: The value of the carbohydrates input in the form.
- `timestampButtons`: An array of timestamps to populate the dropdown for selecting a specific glucose data point.
- `valueInsulin`: The value of the insulin input in the form.
- `Bgvalue`: The value of the historic glucose input in the form.
- `timeStampChosen`: The chosen timestamp from the dropdown.
- `selectedTimestamp`: The selected timestamp for editing or adding a glucose data point.
- `formOpen`: A boolean flag indicating whether the form for adding/editing data is open.

## ChangeHandelers

- `handleOpenForm`:
```
    const handleOpenForm = (data) => {
        setBgValue(data.historicGluc);
        setValue(data.carbs);
        setInsulinValue(data.FastInsulineUnit);
        setSelectedTimestamp(data.Tijdstempel);
        setFormOpen(true);
        setChosenTimestamp("");
    };
```
This method handleOpenForm is responsible for opening the form to add new glucose data. It takes a data parameter, which contains the existing data to pre-fill the form inputs. The function sets the state variables BgValue, setValue, setInsulinValue, and setSelectedTimestamp with the corresponding values from the data object. It also sets setFormOpen to true to display the form and setChosenTimestamp to an empty string. This method is typically called when editing glucose data or selecting data to delete.

## The next handlers only handel events or values

- `handleChangeCarb`: function is an event handler for the input field of the form. It updates the valueCarb state variable with the new value entered by the user.
- `handleChangeIn`: handles the input change for the fast insulin unit field. It updates the valueInsulin state variable with the new value entered by the user.
- `handleChangeBg`: handles the input change for the historic glucose field in the form. It updates the Bgvalue state variable with the new value entered by the user.
- `handleCloseForm`: responsible for closing the form without adding or editing glucose data. It sets the setFormOpen state variable to false to hide the form and also resets the setSelectedTimestamp state variable to an empty string.
- `handleChangeDate`: Handles the change event for the date input.

## Methods

- `fetchData`: This useEffect hook is responsible for fetching the historic glucose data from the server when the component is mounted or when the selectedDate state variable changes. It calls the fetchData function, which performs an asynchronous request to the server using the axios library. The fetched data is then stored in the `glucoseData` state variable using the setGlucoseData method. It also calls the `replaceScannedGlucWithHistoricGluc` and `showGlucoseDataObjects` functions to update the glucose data based on specific conditions.
```
useEffect(() => {
  const fetchData = async () => {
    try {
      const response = await axios.get(
        `http://localhost/api/monitor/${selectedDate}/` + id,
        {
          headers: {
            // add the token to the Authorization header
          },
        }
      );
      //set JSON data object after replacing scannedgluc with HistoricGluc
      setGlucoseData(response.data);
      replaceScannedGlucWithHistoricGluc(response.data);
      showGlucoseDataObjects(response.data);
    } catch (error) {
      console.error("Error fetching data: ", error);
    }
  };

  fetchData();
}, [selectedDate]);
```

- `handleAddData`: Adds a new glucose data point to the JSON array and checks if the timestamp is present. If it is it updates the Array with the updated object data.
If it is not present it attaches the update object to the original JSON as the last entry and then makes an update call to the API to update the BG object in the DB:
```
const handleAddData = async (event, time) => {
    event.preventDefault();
    const selectedTime = moment.tz(time, "HH:mm", "Europe/Amsterdam");
    const newGlucoseData = {
      Tijdstempel: selectedTime.format("HH:mm"),
      historicGluc: event.target.historicGluc.value,
      FastInsulineUnit: event.target.FastInsulineUnit.value,
      carbs: event.target.carbs.value,

    };
    const existingIndex = glucoseData.findIndex(
      (d) => d.Tijdstempel === newGlucoseData.Tijdstempel
    );
    if (existingIndex >= 0) {
      const updatedData = [...glucoseData];
      updatedData[existingIndex] = newGlucoseData;
      setGlucoseData(updatedData);
```

- `handleDeleteData`: The handleDeleteData method is responsible for deleting a specific glucose data point from the server. It makes a PUT request to the server's API endpoint /api/monitor/py/update/{datestamp} just as the handleAddData except we filter the JSON data array to delete the entry with the selected timestamp before requesting the PUT action :
```
 if (selectedTimestamp) {
      const newData = glucoseData.filter((d) => d.Tijdstempel !== selectedTimestamp);
      setGlucoseData(newData);
```
The new JSON array wil be update to the database. With the API call : 
```
      try {
        axios.put(
          `http://localhost/api/monitor/py/update/${selectedDate}/` + id,
          newData,
          config
        );
        console.log("Data sent successfully!");
      } catch (error) {
        console.error("Error sending data: ", error);
      }
```
The call :
```
 await axios.put(
          `http://localhost/api/monitor/py/update/${selectedDate}/` + id,
          updatedData,
          config
        );
        console.log("Data sent successfully!");
      } catch (error) {
        console.error("Error sending data: ", error);
      }
```

- `getPointBackgroundColor`: Determines the background color of a data point on the chart based on its value or presence of a value in Insuline and Carbs.
- `replaceScannedGlucWithHistoricGluc`: Replaces the scanned glucose values with historic glucose values in the data, to create consistency between used JSON keys.

```
  const replaceScannedGlucWithHistoricGluc = (data) => {
    const newData = data.map((obj) => {
      if (obj.scannedGlucose) {
        obj.historicGluc = obj.scannedGlucose;
        delete obj.scannedGlucose;
      }
      return obj;
    });
    setGlucoseData(newData);
  };
```

- `showGlucoseDataObjects`: Sets the timestamp buttons based on the available glucose data so they can be selected in the selector.
- `showSpecificGlucoseObjectAndEdit`: Displays and edits a specific glucose data point, finds a timestamp in array and opens form with the values of that timestamp.
- `fetchPredictionData`: Fetches the predicted glucose data from the server, from the last known data entry point. and makes a new Object to attach to the Array.

```
const latestEntry = glucoseData[glucoseData.length - 1];
const newTijdstempel = moment(latestEntry.Tijdstempel, "HH:mm").add(15, "minutes").format("HH:mm");
      const newGlucoseData = {
        Tijdstempel: newTijdstempel,
        historicGluc: response.data,
        FastInsulineUnit: 0,
        carbs: 0,
        predictionData: response.data
      };
      const updatedData = [...glucoseData, newGlucoseData];
      setGlucoseData(updatedData);
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
- The form for adding/editing data when the formOpen state is true.

The component utilizes various event handlers and lifecycle methods, such as `useEffect`, to fetch data, update the state, and interact with the server.

