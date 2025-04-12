## Project 1 - Mood Based Recipe App

###

<a href=""><img src="https://github.com/user-attachments/assets/039dee23-0b87-43e9-afeb-9f58f592e7d2" width="720" height="400" /></a>

###

<img src="https://github.com/user-attachments/assets/35150d83-5547-4985-a9d9-b5fa8ddda1c2" width="420" height="620" />

# 1 - Mood Based Recipe App ✅

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

- [ ] Create a new project directory:

```
mkdir mood-recipes
cd mood-recipes
```

- [ ] Initialize a new Node.js project:

```
npm init -y
```

- [ ] Install required dependencies:

```
npm install express sqlite3 tailwindcss nodemon
```

## **Task 2: Database Setup**

- [ ] Create populate-db.js file with the following content:

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

## **Task 3: Backend Server Setup**

- [ ] Create server.js file:

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

## **Task 4: Frontend Development**

- [ ] Create public/index.html:

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

## **Task 5: Testing the Load Balancer**

- [ ] Navigate to **Load Balancers** and copy DNS name from **Web-server-LB**.
- [ ] Paste the DNS name in a browser to confirm round-robin response from `server A` and `server B`.
- [ ] Also SSH into the Bastion-server and use curl to send a GET request to the DNS name to confirm round-robin response from `server A` and `server B`.

## **Task 7: Block All other IPs with Security Group: Only Allow specific IP or Group of IPs**

- [ ] Navigate to **Security Group** in **EC2 (Compute)**.
- [ ] Change the InBound rule of the Load Balancer Security Group from All IPs (0.0.0.0/0) to `Public IP of Bastion Host` or `Your IP address`.
- [ ] Navigate to **Load Balancers** and copy again the DNS name from **Web-server-LB**.
- [ ] Paste the DNS name in a browser to confirm round-robin response from `server A` and `server B` is NOT working anymore.
- [ ] SSH into the Bastion-server and use curl to send a GET request to the DNS name to confirm round-robin response from `server A` and `server B` is still working.

## **Task 8: Creating an IP Set**

- [ ] Navigate to **WAF & Shield** → **IP Sets**.
- [ ] Click **Create IP sets** and configure:
  - **Name:** `MyIPset`
  - **Description:** `IP set to block my public IP`
  - **Region:** `US EAST (N. Virginia)`
  - **IP Version:** `IPv4`
  - **IP address:** The Public IP of the Bastion Host or Your `Public IP/32`
- [ ] Click **Create IP set**.

## **Task 9: Creating a Web ACL**

- [ ] Navigate to **WAF dashboard** → **Web ACLs** → Click **Create web ACL**.
- [ ] Configure:
  - **Resource type:** `Regional resources`
  - **Region:** `US EAST (N. Virginia)`
  - **Name:** `MywebACL`
- [ ] Associate **ALB** by Add **AWS resources**.
- [ ] Under **Rules**, **Add rule**:
  - **Rule type:** `IP set`
  - **Name:** `MywebACL-rule`
  - **IP set:** `MyIPset`
  - **Action:** `Block`
- [ ] Click **Create web ACL**.

## **Task 10: Testing the Working of the WAF**

- [ ] Attempt to access ALB DNS name using Curl from Bastion Host.
- [ ] Expect a **403 Forbidden error** showing WAF is functioning.

## **Task 11: Unblocking the IP**

- [ ] Navigate to **WAF & Shield** → **IP Sets** → `MyIPset`.
- [ ] Delete your public IP from the set.
- [ ] Wait, and then retry accessing the ALB DNS name.
- [ ] Confirm access is again available from Bastion Host.

## **Conclusion**

✅ Successfully blocked and managed web traffic using AWS WAF and Security Group! 🎉
