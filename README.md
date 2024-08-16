## Building a React Front-End with an ASP.NET Core API Back-End

This guide will walk you through creating a simple web application using React for the front end and ASP.NET Core for the API back end. We’ll cover everything from setting up the project to making the front end and back end communicate with each other.

### Prerequisites

- **Visual Studio Code (VS Code)**
- **Node.js** (This will also install npm)
- **.NET SDK**

### Step 1: Set Up the ASP.NET Core API

1. **Open VS Code** and open a new terminal (`` Ctrl + `  ``).

2. **Create a New ASP.NET Core Web API Project**:

   ```bash
   dotnet new webapi -n MyApi
   ```

   This command creates a new Web API project in a folder called `MyApi`.

3. **Navigate to the Project Directory**:

   ```bash
   cd MyApi
   ```

4. **Run the API**:

   ```bash
   dotnet run
   ```

   You should see output indicating that the application is running on `https://localhost:5001` and `http://localhost:5000`.

5. **Folder Structure Overview**:

   Your project will have a structure like this:

   ```
   MyApi/
   ├── Controllers/
   │   └── WeatherForecastController.cs
   ├── Program.cs
   ├── Startup.cs
   └── MyApi.csproj
   ```

### Step 2: Modify the ASP.NET Core API

Create a simple API endpoint that returns a list of items.

1. **Create a New Controller**:
   Inside the `Controllers` folder, add a new file called `ItemsController.cs`.

2. **Add the following code to `ItemsController.cs`**:

   ```csharp
   using Microsoft.AspNetCore.Mvc; // Import the ASP.NET Core MVC library
   using System.Collections.Generic; // Import the Collections.Generic library for List<T>

   namespace MyApi.Controllers // Define the namespace for the controller
   {
       [ApiController] // Attribute to indicate that this class is a Web API controller
       [Route("api/[controller]")] // Attribute to define the route pattern. [controller] will be replaced with the controller's name
       public class ItemsController : ControllerBase // Define the controller class, inheriting from ControllerBase
       {
           // A static list of items. 'static' means it belongs to the class, not an instance of the class.
           private static readonly List<string> Items = new List<string>
           {
               "Item1",
               "Item2",
               "Item3"
           };

           // Method to handle HTTP GET requests to the endpoint.
           [HttpGet] // Attribute to specify that this method responds to GET requests
           public ActionResult<IEnumerable<string>> Get() // Method signature
           {
               return Ok(Items); // Return the list of items with a 200 OK status code
           }
       }
   }
   ```

   - **`using Microsoft.AspNetCore.Mvc;`**:

     - This line imports the ASP.NET Core MVC library, which contains the base classes and methods used to build API controllers.

   - **`[ApiController]`**:

     - This attribute marks the class as a Web API controller, which allows it to handle HTTP requests.

   - **`[Route("api/[controller]")]`**:

     - This attribute specifies the route template for the controller. `[controller]` is a placeholder that will be replaced with the name of the controller (without the "Controller" suffix). For example, `ItemsController` will map to `api/items`.

   - **`public class ItemsController : ControllerBase`**:

     - This line defines the `ItemsController` class, inheriting from `ControllerBase`, which provides the base functionality for API controllers.

   - **`private static readonly List<string> Items`**:

     - `Items` is a static list that holds a few string values. Being `static`, it means this list is shared across all instances of the `ItemsController` class. `readonly` indicates that once it's initialized, it cannot be modified.

   - **`[HttpGet]`**:

     - This attribute designates the `Get` method to handle HTTP GET requests, which are typically used to retrieve data.

   - **`public ActionResult<IEnumerable<string>> Get()`**:

     - `Get` is the method that will be called when an HTTP GET request is made to this endpoint. It returns an `ActionResult` that contains an enumerable list of strings.

   - **`return Ok(Items);`**:
     - `Ok` is a method that returns a response with a 200 OK status code, along with the `Items` list. This means the request was successful, and the data is returned.

3. **Run the API Again**:

   ```bash
   dotnet run
   ```

   Navigate to `https://localhost:5001/api/items` in your browser, and you should see a JSON array of items: `["Item1","Item2","Item3"]`.

### Step 3: Set Up the React Front End

1. **Create a New React App**:

   ```bash
   npx create-react-app my-react-app
   ```

   This command creates a new React application in a folder called `my-react-app`.

2. **Navigate to the Project Directory**:

   ```bash
   cd my-react-app
   ```

3. **Install Axios**:

   ```bash
   npm install axios
   ```

4. **Run the React App**:

   ```bash
   npm start
   ```

   Your React app should now be running at `http://localhost:3000`.

### Step 4: Modify the React App to Call the API

1. **Open `App.js`**:
   In the `src` folder, locate and open `App.js`.

2. **Replace the Code with the Following**:

   ```javascript
   import React, { useState, useEffect } from "react"; // Importing React and two hooks, useState and useEffect
   import axios from "axios"; // Importing Axios, a library to make HTTP requests

   function App() {
     // This is our main App component

     // useState is a React Hook that lets you add state to a functional component.
     // Here, we're creating a state variable called 'items' that starts as an empty array.
     // We also have 'setItems', a function to update the 'items' state.
     const [items, setItems] = useState([]);

     // useEffect is another React Hook that lets you run side effects in your component.
     // Side effects are things like fetching data, updating the DOM, or setting up subscriptions.
     // This effect runs once when the component mounts because of the empty dependency array [].
     useEffect(() => {
       // axios.get makes a GET request to the given URL (our API endpoint).
       // When the data is successfully fetched, we use setItems to update the 'items' state with the fetched data.
       axios
         .get("https://localhost:5001/api/items") // URL of our ASP.NET Core API
         .then((response) => {
           setItems(response.data); // Store the fetched data (response.data) in 'items'
         })
         .catch((error) => {
           console.error("There was an error fetching the items!", error); // Log any error that occurs during the request
         });
     }, []); // The empty array here means this effect runs only once, after the first render.

     // The return statement defines what gets rendered on the screen.
     // We're creating a simple unordered list (<ul>) of items.
     return (
       <div className="App">
         <h1>Items from API</h1>
         <ul>
           {items.map((item, index) => (
             <li key={index}>
               {item}{" "}
               {/* Each item is rendered inside an <li> element. The 'key' prop helps React efficiently manage and update the list items. */}
             </li>
           ))}
         </ul>
       </div>
     );
   }

   export default App; // Exporting the App component so it can be used in other parts of the project
   ```

   - **`import React, { useState, useEffect } from "react";`**:

     - This imports the React library and two hooks: `useState` and `useEffect`. `useState` is used to manage state in functional components, and `useEffect` is used to handle side effects such as data fetching.

   - **`const [items, setItems] = useState([]);`**:

     - `useState([])` initializes a state variable `items` with an empty array. `setItems` is a function used to update the `items` state.

   - **`useEffect(() => {...}, []);`**:

     - `useEffect` is used to perform side effects, like fetching data. The effect runs once after the component mounts because the dependency array is empty.

   - **`axios.get("https://localhost:5001/api/items")`**:

     - `axios.get` makes a GET request to the specified URL. This fetches data from the API endpoint. When successful, `response.data` contains the data from the API.

   - **`setItems(response.data);`**:

     - This updates the `items` state with the data fetched from the API.

   - \*\*`{items.map((

item, index) => (<li key={index}>{item}</li>))}`**:
     - This maps over the `items` array, creating a list item (`<li>`) for each entry. The `key` prop is used to give each list item a unique identifier, which helps React manage and update the list efficiently.

3. **Proxy Configuration (Optional)**:
   To avoid CORS issues during development, add a proxy to your React app. In `package.json`, add the following line:

   ```json
   "proxy": "https://localhost:5001",
   ```

   Now, API requests can be made using just the relative path:

   ```javascript
   axios.get("/api/items");
   ```

### Step 5: Run Both Projects

1. **Run the ASP.NET Core API**:

   ```bash
   dotnet run
   ```

2. **Run the React App**:

   ```bash
   npm start
   ```

3. **Visit `http://localhost:3000`**:
   You should see a list of items fetched from the ASP.NET Core API.

---
