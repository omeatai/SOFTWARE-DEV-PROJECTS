## Project 1 - Mood Based Recipe App [JS,HTML,CSS,Tailwindcss,Express,Node.js,SQLite]

###

<a href=""><img src="https://github.com/user-attachments/assets/a517bc92-5dd1-4823-8015-de88face98d3" width="720" height="400" /></a>

###

<img src="https://github.com/user-attachments/assets/35150d83-5547-4985-a9d9-b5fa8ddda1c2" width="420" height="620" />

# 1 - Mood Based Recipe App [JS,HTML,CSS,Tailwindcss,Express,Node.js,SQLite]✅

## **Overview**

- [ ] Create a web application that recommends recipes based on user's mood
- [ ] Use Express.js for the backend server
- [ ] Implement SQLite for recipe storage
- [ ] Build a responsive frontend with Tailwind CSS
- [ ] Allow users to get new recipe suggestions for their selected mood

## **Prerequisites**

- [ ] Install Node.js from nodejs.org
- [ ] Basic understanding of JavaScript, HTML, and CSS
- [ ] A code editor (VS Code recommended)

## **Task 1: Project Setup**

<details>
  <summary>Create a new project directory</summary>
  
  ```
  mkdir mood-recipes
  cd mood-recipes
  ```
</details>

<details>
  <summary>Initialize a new Node.js project</summary>
  
  ```
  npm init -y
  ```
</details>

<details>
  <summary>Install required dependencies</summary>
  
  ```
  npm install express sqlite3 tailwindcss nodemon
  ```
</details>

## **Task 2: Database Setup**

- [ ] Create populate-db.js file:

<details>
  <summary>Import SQLite3 and Create a new database connection to recipes.db</summary>
  
  ```js
  // Import SQLite3 and enable verbose mode for detailed error messages
  const sqlite3 = require("sqlite3").verbose();
  
  // Create a new database connection to recipes.db
  // If the file doesn't exist, it will be created
  const db = new sqlite3.Database("recipes.db");
  ```
</details>  

<details>
  <summary>Create an Array of recipe objects</summary>
  
  ```js
  // Array of recipe objects, each containing:
  // - name: The recipe title
  // - ingredients: Comma-separated list of ingredients
  // - instructions: Step-by-step cooking instructions
  // - mood: The emotional state associated with the recipe
  const recipes = [
    {
      name: "Chocolate Chip Cookies",
      ingredients:
        "2 1/4 cups flour, 1 cup butter, 3/4 cup sugar, 3/4 cup brown sugar, 2 eggs, 1 tsp vanilla, 1 tsp baking soda, 1/2 tsp salt, 2 cups chocolate chips",
      instructions:
        "Preheat oven to 375°F. Cream butter and sugars. Add eggs and vanilla. Mix in dry ingredients. Stir in chocolate chips. Drop spoonfuls onto baking sheet. Bake for 9-11 minutes.",
      mood: "happy",
    },
    // ... more recipes for different moods
    {
      name: "Mac and Cheese",
      ingredients:
        "8 oz elbow macaroni, 2 cups cheddar cheese, 2 cups milk, 2 tbsp butter, 2 tbsp flour, salt, pepper",
      instructions:
        "Cook pasta. Make roux with butter and flour. Add milk and cheese. Stir until smooth. Combine with pasta. Bake at 350°F for 20 minutes.",
      mood: "comfort",
    },
  ];
  ```
</details>  

<details>
  <summary>Create Helper Wrapper function to convert SQLite's callback-based operations into Promises</summary>
  
  ```js
  // Helper function to convert SQLite's callback-based operations into Promises
  // This makes it easier to use async/await syntax
  function runQuery(query, params = []) {
    return new Promise((resolve, reject) => {
      db.run(query, params, function (err) {
        // If there's an error, reject the promise with the error
        if (err) reject(err);
        // Otherwise, resolve with 'this' context (contains lastID, changes, etc.)
        else resolve(this);
      });
    });
  }
  ```
</details>  

<details>
  <summary>Create Function to Populate SQLite Database with recipes</summary>
  
  ```js
  async function populateDatabase() {
    try {
      // Step 1: Create the recipes table if it doesn't exist
      // Define columns: id, name, ingredients, instructions, mood
      await runQuery(`CREATE TABLE IF NOT EXISTS recipes (
        id INTEGER PRIMARY KEY AUTOINCREMENT,  // Unique identifier for each recipe
        name TEXT NOT NULL,                    // Recipe name cannot be null
        ingredients TEXT NOT NULL,             // List of ingredients
        instructions TEXT NOT NULL,            // Cooking instructions
        mood TEXT NOT NULL                     // Associated mood
      )`);
  
      // Step 2: Clear any existing data from the table
      // This ensures we don't have duplicate recipes
      await runQuery("DELETE FROM recipes");
  
      // Step 3: Prepare an SQL statement for inserting recipes
      // Using prepared statements prevents SQL injection and improves performance
      const stmt = db.prepare(
        "INSERT INTO recipes (name, ingredients, instructions, mood) VALUES (?, ?, ?, ?)"
      );
  
      // Step 4: Insert each recipe into the database
      for (const recipe of recipes) {
        // Wait for each insert to complete before moving to the next
        await new Promise((resolve, reject) => {
          stmt.run(
            recipe.name,
            recipe.ingredients,
            recipe.instructions,
            recipe.mood,
            function (err) {
              if (err) reject(err);
              else resolve(this);
            }
          );
        });
      }
      // Step 5: Finalize the prepared statement to release resources
      stmt.finalize();
  
      console.log("Database populated with sample recipes!");
    } catch (err) {
      // Handle any errors that occur during the process
      console.error("Error:", err);
    } finally {
      // Always close the database connection, even if there's an error
      db.close();
    }
  }
  ```
</details>  

<details>
  <summary>Execute the Function to Populate SQLite Database</summary>

  ```js
  // Call the async function to start populating the database
  populateDatabase();
  ```
</details>  

<details>
  <summary>populate-db.js</summary>
  
  ```js
  const sqlite3 = require("sqlite3").verbose();
  const db = new sqlite3.Database("recipes.db");
  
  // Create a promise-based wrapper for database operations
  function runQuery(query, params = []) {
    return new Promise((resolve, reject) => {
      db.run(query, params, function (err) {
        if (err) reject(err);
        else resolve(this);
      });
    });
  }
  
  async function populateDatabase() {
    try {
      // Create table
      await runQuery(`CREATE TABLE IF NOT EXISTS recipes (
                  id INTEGER PRIMARY KEY AUTOINCREMENT,
                  name TEXT NOT NULL,
                  ingredients TEXT NOT NULL,
                  instructions TEXT NOT NULL,
                  mood TEXT NOT NULL
              )`);
  
      // Clear existing data
      await runQuery("DELETE FROM recipes");
  
      // Insert sample recipes
      const recipes = [
        {
          name: "Chocolate Chip Cookies",
          ingredients:
            "2 1/4 cups flour, 1 cup butter, 3/4 cup sugar, 3/4 cup brown sugar, 2 eggs, 1 tsp vanilla, 1 tsp baking soda, 1/2 tsp salt, 2 cups chocolate chips",
          instructions:
            "Preheat oven to 375°F. Cream butter and sugars. Add eggs and vanilla. Mix in dry ingredients. Stir in chocolate chips. Drop spoonfuls onto baking sheet. Bake for 9-11 minutes.",
          mood: "happy",
        },
        // ... (other recipes)
      ];
  
      const stmt = db.prepare(
        "INSERT INTO recipes (name, ingredients, instructions, mood) VALUES (?, ?, ?, ?)"
      );
      for (const recipe of recipes) {
        await new Promise((resolve, reject) => {
          stmt.run(
            recipe.name,
            recipe.ingredients,
            recipe.instructions,
            recipe.mood,
            function (err) {
              if (err) reject(err);
              else resolve(this);
            }
          );
        });
      }
      stmt.finalize();
  
      console.log("Database populated with sample recipes!");
    } catch (err) {
      console.error("Error:", err);
    } finally {
      db.close();
    }
  }
  
  populateDatabase();
  ```
</details>



## **Task 3: Backend Server Setup**

- [ ] Create server.js file:

<details>
  <summary>Import Express.js, SQLite3 and Node.js path Dependencies</summary>
  
  ```js
  // Express.js - Web application framework
  const express = require("express");
  
  // SQLite3 database driver with verbose mode for detailed errors
  const sqlite3 = require("sqlite3").verbose();
  
  // Node.js path module for handling file paths
  const path = require("path");
  ```
</details>

<details>
  <summary>Initialize Express and DB Configurations</summary>
  
  ```js
  // Create an Express application instance
  const app = express();
  
  // Define the port number for the server
  const port = 3000;
  
  // Initialize SQLite database connection
  // This will create recipes.db if it doesn't exist
  const db = new sqlite3.Database("recipes.db");
  ```
</details>

<details>
  <summary>Create Database Table</summary>
  
  ```js
  // Use serialize to ensure database operations occur sequentially
  db.serialize(() => {
    // Create the recipes table if it doesn't already exist
    db.run(`CREATE TABLE IF NOT EXISTS recipes (
          id INTEGER PRIMARY KEY AUTOINCREMENT,  // Unique identifier
          name TEXT NOT NULL,                    // Recipe name
          ingredients TEXT NOT NULL,             // List of ingredients
          instructions TEXT NOT NULL,            // Cooking steps
          mood TEXT NOT NULL                     // Associated mood
      )`);
  });
  ```
</details>

<details>
  <summary>Setup Middleware Configuration</summary>
  
  ```js
  // Enable parsing of JSON bodies in requests
  app.use(express.json());
  
  // Serve static files from the 'public' directory
  // This allows serving HTML, CSS, and JavaScript files
  app.use(express.static("public"));
  ```
</details>

<details>
  <summary>API Route Handler</summary>
  
  ```js
  // Handle GET requests for recipes by mood
  app.get("/api/recipes/:mood", (req, res) => {
    // Extract mood parameter from URL
    const mood = req.params.mood;
  
    // Query database for recipes matching the mood
    db.all("SELECT * FROM recipes WHERE mood = ?", [mood], (err, rows) => {
      // Handle database errors
      if (err) {
        res.status(500).json({ error: err.message });
        return;
      }
  
      // Handle case when no recipes are found
      if (rows.length === 0) {
        res.status(404).json({ message: "No recipes found for this mood" });
        return;
      }
  
      // Select a random recipe from the results
      const randomRecipe = rows[Math.floor(Math.random() * rows.length)];
      
      // Send the recipe as JSON response
      res.json(randomRecipe);
    });
  });
  ```
</details>

<details>
  <summary>Main Page Route: Handle GET requests to Root</summary>
  
  ```js
  // Handle GET requests to the root URL
  app.get("/", (req, res) => {
    // Serve the index.html file from the public directory
    res.sendFile(path.join(__dirname, "public", "index.html"));
  });
  ```
</details>

<details>
  <summary>Start Express Server</summary>
  
  ```js
  // Start the Express server and listen for connections
  app.listen(port, () => {
    // Log a message when the server starts successfully
    console.log(`Server running at http://localhost:${port}`);
  });
  ```
</details>

<details>
  <summary>server.js</summary>

```js
const express = require("express");
const sqlite3 = require("sqlite3").verbose();
const path = require("path");

const app = express();
const port = 3000;

// Initialize SQLite database
const db = new sqlite3.Database("recipes.db");

// Create tables if they don't exist
db.serialize(() => {
  db.run(`CREATE TABLE IF NOT EXISTS recipes (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            ingredients TEXT NOT NULL,
            instructions TEXT NOT NULL,
            mood TEXT NOT NULL
        )`);
});

// Middleware
app.use(express.json());
app.use(express.static("public"));

// API Routes
app.get("/api/recipes/:mood", (req, res) => {
  const mood = req.params.mood;
  db.all("SELECT * FROM recipes WHERE mood = ?", [mood], (err, rows) => {
    if (err) {
      res.status(500).json({ error: err.message });
      return;
    }
    if (rows.length === 0) {
      res.status(404).json({ message: "No recipes found for this mood" });
      return;
    }
    const randomRecipe = rows[Math.floor(Math.random() * rows.length)];
    res.json(randomRecipe);
  });
});

// Serve the main page
app.get("/", (req, res) => {
  res.sendFile(path.join(__dirname, "public", "index.html"));
});

// Start server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```
</details> 


## **Task 4: Frontend Development**

- [ ] Create public/index.html:

<details>
  <summary>Document Setup and Meta Information</summary>
  
  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <!-- Character encoding for proper text rendering -->
      <meta charset="UTF-8" />
      <!-- Responsive viewport settings for mobile devices -->
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <!-- Page title shown in browser tab -->
      <title>Mood Recipes</title>
      
      <!-- External Resources -->
      <!-- Tailwind CSS from CDN for styling -->
      <script src="https://cdn.tailwindcss.com"></script>
      <!-- Custom CSS file for additional styles -->
      <link rel="stylesheet" href="styles.css" />
    </head>
  
  </html>
  ```
</details>

<details>
  <summary>Setup Main Container and Header Title</summary>
  
  ```html
<body class="bg-gray-100 min-h-screen">
    <!-- Main container with responsive padding and margin -->
    <div class="container mx-auto px-4 py-8">
        <!-- Main heading with decorative styling -->
        <h1 class="text-4xl font-bold text-center mb-8 text-indigo-600">
            Mood Recipes
        </h1>
    </div>
</body>
  ```
</details>

<details>
  <summary>Setup Mood Selection Card</summary>
  
  ```html
  <!-- Card container with white background and shadow -->
  <div class="max-w-md mx-auto bg-white rounded-lg shadow-md p-6">
      <!-- Mood selection prompt -->
      <h2 class="text-xl font-semibold mb-4">How are you feeling today?</h2>
  
      <!-- Grid layout for mood buttons -->
      <div class="grid grid-cols-2 gap-4 mb-6">
          <!-- Happy mood button -->
          <button
              class="mood-btn bg-blue-100 hover:bg-blue-200 text-blue-800 py-2 px-4 rounded"
              data-mood="happy"
          >
              😊 Happy
          </button>
          
          <!-- Energetic mood button -->
          <button
              class="mood-btn bg-green-100 hover:bg-green-200 text-green-800 py-2 px-4 rounded"
              data-mood="energetic"
          >
              ⚡ Energetic
          </button>
          
          <!-- Cozy mood button -->
          <button
              class="mood-btn bg-yellow-100 hover:bg-yellow-200 text-yellow-800 py-2 px-4 rounded"
              data-mood="cozy"
          >
              🏠 Cozy
          </button>
          
          <!-- Comfort mood button -->
          <button
              class="mood-btn bg-red-100 hover:bg-red-200 text-red-800 py-2 px-4 rounded"
              data-mood="comfort"
          >
              ❤️ Comfort
          </button>
      </div>
  </div>
  ```
</details>

<details>
  <summary>Setup Recipe Display Section</summary>
  
  ```html
  <!-- Hidden container for recipe details -->
  <div id="recipe-container" class="hidden">
      <div class="border-t pt-4">
          <!-- Recipe title placeholder -->
          <h3 class="text-lg font-semibold mb-2" id="recipe-name"></h3>
          
          <!-- Ingredients section -->
          <div class="mb-4">
              <h4 class="font-medium text-gray-700">Ingredients:</h4>
              <!-- Dynamic list of ingredients -->
              <ul id="ingredients" class="list-disc pl-5 text-gray-600"></ul>
          </div>
          
          <!-- Instructions section -->
          <div class="mb-4">
              <h4 class="font-medium text-gray-700">Instructions:</h4>
              <!-- Dynamic instructions text -->
              <p id="instructions" class="text-gray-600"></p>
          </div>
          
          <!-- Currently commented out "Get Another Recipe" button -->
          <!-- <button
              id="new-recipe"
              class="w-full bg-indigo-600 text-white py-2 px-4 rounded hover:bg-indigo-700"
          >
              Get Another Recipe
          </button> -->
      </div>
  </div>
  ```
</details>

<details>
  <summary>Script Integration and Closing Tags</summary>
  
  ```html
      <!-- Link to JavaScript file for functionality -->
      <script src="script.js"></script>
    </body>
  </html>
  ```
</details>

<details>
  <summary>index.html</summary>
  
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Mood Recipes</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="styles.css" />
  </head>
  <body class="bg-gray-100 min-h-screen">
    <div class="container mx-auto px-4 py-8">
      <h1 class="text-4xl font-bold text-center mb-8 text-indigo-600">
        Mood Recipes
      </h1>

      <div class="max-w-md mx-auto bg-white rounded-lg shadow-md p-6">
        <h2 class="text-xl font-semibold mb-4">How are you feeling today?</h2>

        <div class="grid grid-cols-2 gap-4 mb-6">
          <button
            class="mood-btn bg-blue-100 hover:bg-blue-200 text-blue-800 py-2 px-4 rounded"
            data-mood="happy"
          >
            😊 Happy
          </button>
          <button
            class="mood-btn bg-green-100 hover:bg-green-200 text-green-800 py-2 px-4 rounded"
            data-mood="energetic"
          >
            ⚡ Energetic
          </button>
          <button
            class="mood-btn bg-yellow-100 hover:bg-yellow-200 text-yellow-800 py-2 px-4 rounded"
            data-mood="cozy"
          >
            🏠 Cozy
          </button>
          <button
            class="mood-btn bg-red-100 hover:bg-red-200 text-red-800 py-2 px-4 rounded"
            data-mood="comfort"
          >
            ❤️ Comfort
          </button>
        </div>

        <div id="recipe-container" class="hidden">
          <div class="border-t pt-4">
            <h3 class="text-lg font-semibold mb-2" id="recipe-name"></h3>
            <div class="mb-4">
              <h4 class="font-medium text-gray-700">Ingredients:</h4>
              <ul id="ingredients" class="list-disc pl-5 text-gray-600"></ul>
            </div>
            <div class="mb-4">
              <h4 class="font-medium text-gray-700">Instructions:</h4>
              <p id="instructions" class="text-gray-600"></p>
            </div>
            <button
              id="new-recipe"
              class="w-full bg-indigo-600 text-white py-2 px-4 rounded hover:bg-indigo-700"
            >
              Get Another Recipe
            </button>
          </div>
        </div>
      </div>
    </div>
    <script src="script.js"></script>
  </body>
</html>
```
</details>

- [ ] Create public/styles.css:
```css
    .mood-btn {
        transition: all 0.3s ease;
        cursor: pointer;
    }

    .mood-btn:hover {
        transform: translateY(-2px);
    }

    #recipe-container {
        transition: opacity 0.3s ease;
    }

    .hidden {
        display: none;
    }
```

- [ ] Create public/script.js:
```js
    document.addEventListener('DOMContentLoaded', () => {
        const moodButtons = document.querySelectorAll('.mood-btn');
        const recipeContainer = document.getElementById('recipe-container');
        const recipeName = document.getElementById('recipe-name');
        const ingredientsList = document.getElementById('ingredients');
        const instructions = document.getElementById('instructions');
        const newRecipeBtn = document.getElementById('new-recipe');

        let currentMood = null;

        moodButtons.forEach(button => {
            button.addEventListener('click', () => {
                currentMood = button.dataset.mood;
                getRecipe(currentMood);
            });
        });

        newRecipeBtn.addEventListener('click', () => {
            if (currentMood) {
                getRecipe(currentMood);
            }
        });

        async function getRecipe(mood) {
            try {
                const response = await fetch(`/api/recipes/${mood}`);
                if (!response.ok) {
                    throw new Error('No recipes found for this mood');
                }
                const recipe = await response.json();
                
                recipeName.textContent = recipe.name;
                ingredientsList.innerHTML = recipe.ingredients.split(',').map(ingredient => 
                    `<li>${ingredient.trim()}</li>`
                ).join('');
                instructions.textContent = recipe.instructions;
                
                recipeContainer.classList.remove('hidden');
            } catch (error) {
                alert(error.message);
            }
        }
    });
```

## **Task 5: Running the Application**

- [ ] Populate the database:
```
node populate-db.js
```

- [ ] Start the server:
```
node server.js
```

- [ ] Open your browser and navigate to:
```
http://localhost:3000
```

## **Task 6: Testing the Application**

- [ ] Click on different mood buttons to see recipe recommendations
- [ ] Verify that ingredients and instructions are displayed correctly
- [ ] Check that the UI is responsive and works well on different screen sizes

## **Conclusion**

✅ Successfully created a mood-based recipe recommendation app! 🎉
