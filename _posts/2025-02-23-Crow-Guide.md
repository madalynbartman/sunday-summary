How to Create an API with Crow

### Intro
I recently became interested in learning AI development in C++ and Java. I thought translating my first post, a guide on writing an API using FastAPI, into Java would be a great place to start. The goal is to notice patterns and trends in API development across languages. The ideal outcome would be to be able to think of development in a language agnostic sense. This blog post contains the C++ version. Let's get into it! 

### Definitions
API: An interface that allows us to programmatically interact with an application.

Web API: An API that uses HTTP (Hypertext Transfer Protocol) to transport data.

HTTP: A communication protocol used to exchange different media types over a network. This guide will cover the four most common HTTP methods:

GET: Returns info about the requested resource.

POST: Creates a new resource.

PUT: Performs a full update by replacing a resource.

DELETE: Removes a resource.

### Why Crow?
Crow is chosen for this guide due to its:

Lightweight and fast nature (C++ efficiency).

Easy-to-use interface, similar to Python's Flask and FastAPI.

Support for modern C++ features.

Setting Up the Project
Install Crow: Ensure you have Crow library installed. You can add it as a submodule in your project or use the single-header version.

Project Structure: Create the main file main.cpp for your API.

Code Implementation
Here's the complete implementation for our CRUD API using Crow.

```cpp
#include <crow.h>
#include <map>
#include <string>
#include <optional>

// Initialize our API by creating an app object by calling the Crow::SimpleApp constructor
crow::SimpleApp app;

// Define the Item structure with name, price, and optional description
struct Item {
    std::string name;
    double price;
    std::optional<std::string> description;
};

// In-memory data store
std::map<int, crow::json::wvalue> inventory;

// GET /get-item/{item_id}
crow::response get_item(const crow::request& req, int item_id, const std::string& name) {
    if (inventory.find(item_id) == inventory.end()) {
        return crow::response(404, "Item ID not found.");
    }
    if (!name.empty() && inventory[item_id]["name"].s() != name) {
        return crow::response(404, "Item name not found.");
    }
    return crow::response(inventory[item_id].dump());
}

// POST /create-item/{item_id}
crow::response create_item(const crow::request& req, int item_id) {
    auto item = crow::json::load(req.body);
    if (!item) {
        return crow::response(400, "Invalid JSON.");
    }
    if (inventory.find(item_id) != inventory.end()) {
        return crow::response(400, "Item ID already exists.");
    }
    inventory[item_id] = item;
    return crow::response(inventory[item_id].dump());
}

// PUT /update-item/{item_id}
crow::response update_item(const crow::request& req, int item_id) {
    auto item = crow::json::load(req.body);
    if (!item) {
        return crow::response(400, "Invalid JSON.");
    }
    if (inventory.find(item_id) == inventory.end()) {
        return crow::response(404, "Item ID does not exist.");
    }
    if (!item["name"].s().empty()) {
        inventory[item_id]["name"] = item["name"];
    }
    if (!item["price"].t().is_null()) {
        inventory[item_id]["price"] = item["price"];
    }
    if (!item["description"].s().empty()) {
        inventory[item_id]["description"] = item["description"];
    }
    return crow::response(inventory[item_id].dump());
}

// DELETE /delete-item/{item_id}
crow::response delete_item(int item_id) {
    if (inventory.find(item_id) == inventory.end()) {
        return crow::response(404, "Item ID does not exist.");
    }
    inventory.erase(item_id);
    return crow::response(200, "Success: Item deleted!");
}

int main() {
    CROW_ROUTE(app, "/get-item/<int>").methods("GET"_method)([](const crow::request& req, int item_id) {
        auto name = req.url_params.get("name");
        return get_item(req, item_id, name ? std::string(name) : "");
    });

    CROW_ROUTE(app, "/create-item/<int>").methods("POST"_method)([](const crow::request& req, int item_id) {
        return create_item(req, item_id);
    });

    CROW_ROUTE(app, "/update-item/<int>").methods("PUT"_method)([](const crow::request& req, int item_id) {
        return update_item(req, item_id);
    });

    CROW_ROUTE(app, "/delete-item/<int>").methods("DELETE"_method)([](int item_id) {
        return delete_item(item_id);
    });

    // Run the app on port 8080, multithreaded
    app.port(8080).multithreaded().run();
}
```
### Explanation
Dependencies: Includes the necessary Crow headers and standard C++ libraries.

Initialize API: Create an app object using Crow::SimpleApp.

Define Item Structure: Define the Item struct with name, price, and optional description.

Data Store: Create an in-memory data store using std::map to store items.

Endpoints:

GET: Retrieve an item by ID and optionally by name.

POST: Create a new item.

PUT: Update an existing item.

DELETE: Remove an item by ID.

Route Definitions: Define the routes for each HTTP method and map them to the corresponding handler functions.

Run the Server: Run the Crow app on port 8080 with multithreading enabled.

### Running the Application
Compile your main.cpp file with the Crow library.

Run the compiled executable to start the server.

Access the API endpoints using tools like curl or Postman.