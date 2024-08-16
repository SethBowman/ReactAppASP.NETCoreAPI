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
   using Microsoft.AspNetCore.Mvc;
   using System.Collections.Generic;

   namespace MyApi.Controllers
   {
       [ApiController]
       [Route("api/[controller]")]
       public class ItemsController : ControllerBase
       {
           private static readonly List<string> Items = new List<string>
           {
               "Item1",
               "Item2",
               "Item3"
           };

           [HttpGet]
           public ActionResult<IEnumerable<string>> Get()
           {
               return Ok(Items);
           }
       }
   }
   ```

   - `[ApiController]`: Marks this class as a Web API controller.
   - `[Route("api/[controller]")]`: Specifies the route for the controller. The `[controller]` token is replaced with the name of the controller, minus the "Controller" suffix, so the route becomes `api/items`.
   - `HttpGet`: This method responds to GET requests.
   - `Ok(Items)`: Returns the list of items as a 200 OK response.

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
   import React, { useState, useEffect } from "react";
   import axios from "axios";

   function App() {
     const [items, setItems] = useState([]);

     useEffect(() => {
       axios
         .get("https://localhost:5001/api/items")
         .then((response) => {
           setItems(response.data);
         })
         .catch((error) => {
           console.error("There was an error fetching the items!", error);
         });
     }, []);

     return (
       <div className="App">
         <h1>Items from API</h1>
         <ul>
           {items.map((item, index) => (
             <li key={index}>{item}</li>
           ))}
         </ul>
       </div>
     );
   }

   export default App;
   ```

   - **`useState([])`**:
     - `useState` is a function that lets you add React state to a functional component.
     - Here, `useState([])` is used to create a state variable `items` and its corresponding setter function `setItems`.
     - `items` is initialized as an empty array `[]` because it will store a list of items.
   - **`useEffect(() => {...}, [])`**:
     - `useEffect` allows you to perform side effects in your component, such as fetching data or directly interacting with the DOM.
     - The function inside `useEffect` runs after the component renders. The empty array `[]` as the second argument tells React to run this effect only once, after the first render (component mount).
   - **`axios.get('https://localhost:5001/api/items')`**:
     - `axios.get` is used to make a GET request to the specified API endpoint (`/api/items`).
     - The API returns a list of items, which is accessed via `response.data` and stored in the `items` state using `setItems`.
   - **Mapping Over `items`**:
     - The `map` function iterates over each item in the `items` array and returns a new array of list elements (`<li>`). Each list element displays an item.
     - The `key` attribute is important for helping React efficiently manage and update the list of elements when changes occur.

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
