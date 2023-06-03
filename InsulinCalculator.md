# Insuline Calculator

The `Insuline Calculator` component displays a from for the user to input information he/ she wishes to preform a calculation on. Displayes an option to import meals if the user has meals stored in the DB and loads the information into the form. Also contains an explaination render to help the user input the correct information in the calculation form. Then returns the results with all the information needed to assist with insuline intake.

## Dependencies

- `../css/calc.css`: custom CSS styles for the calculator component.
- `../component/CalculatorForm`: child component for rendering the calculator form.
- `../component/MealSelector`: child component for rendering the meal selector.
- `../component/MealAPI`: module for interacting with the meal API.

## Meal API calls

| Route | Values | function |
|--- |--- |--- |
| meals/allMeals/{id} | `id`: The user_id of the logged in| Gets a string array of all avaliable meal names to select from
| meals/get/{id}?name={param} | `id`: The user_id of the logged in| Gets a specific meal from the DB and all its information.
|-|`Param`: Contains the string name of the selected meal |  |
| meals/set/{id} | `id`: The user_id of the logged in | Saves a meal in the DB corresponding with the user ID.
|-|`Body`: The body contains the meal: name, fat, carbs and protien |  |

## Calculate API calls

| Route | Values | function |
|--- |--- |--- |
| monitor/calculate | `Body`: The body contains the calculation info : taget BG, current BG, Activity, carbs, fat and protien | Preforms calculation based on user input form.

## State Variables

- `selectedMealData`: an object containing the nutritional data of the selected meal
- `targetBG`: a number representing the target blood glucose value
- `totalDailyDose`: a number representing the total daily insulin dose
- `currentBG`: a number representing the current blood glucose value
- `isActivity`: a boolean indicating whether the user is engaged in physical activity
- `isf`: a number representing the insulin sensitivity factor
- `Insulin`: a number representing the calculated insulin dose
- `correction`: a number representing the correction dose
- `highFat`: a number representing the high-fat content of the meal
- `highProtien`: a number representing the high-protein content of the meal
- `carbs`: a number representing the carbohydrate content of the meal
- `insulinAfterTwoHours`: a number representing the insulin dose after two hours
- `showResults`: a boolean indicating whether to display the calculated results
- `showModel`: a boolean indicating whether to display the meal selection modal
- `mealOptions`: an array containing the available meal options
- `mealName`: a string representing the name of the selected meal

## Rendered Output

The component renders the following:

- `mealSelector`: Renders the selector and its options for the user to select a meal he/ she wishes to use for calculation
- `ShowResults`: Shows the results of the preformed calculation this screen contains:
   - `ISF`: insulin sensitivity factor.
   - `Insulin to take`: Amount of insuline for carb coverage that is adviced.
   - `Correction needed`: amount of correnction units that is adviced.
   - `Extra insuline to add over 2 hours`: Amount of extra insuline units that is adviced.
   - `Disclaimer`: insulin sensitivity factor.
   - `Save`: Option to set teh name for the meal you want to save.
- `CalculatorForm`: Renders the input form for the user to be able to input all the information the calculation is going to based on. Also renders an explaination div to help the user with inputting the right information.


