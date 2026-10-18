# 🏎️ F1 Circuits Database

A Java client-server app for managing a database of Formula 1 circuits. Clients connect to a multithreaded server over TCP sockets, and the server reads and writes the data in MySQL through a DAO layer, sending it back as JSON. I built it as a team project with Tomas Szabo and Petr Sulc to learn how networking, databases, and JSON fit together in Java. It is a console application, so I focused on the back-end logic rather than a graphical interface.

## 📦 Technologies

- `Java 14`
- `Maven`
- `MySQL`
- `JDBC (MySQL Connector/J)`
- `Java Sockets`
- `Gson`
- `JUnit 5`
- `IntelliJ IDEA`

## ⚙️ Features

Here's what you can do with the F1 Circuits Database:

- **Display a Circuit by ID**: Enter an ID and the server sends back that circuit's name, country, length, and number of turns.

- **Display All Circuits**: List every circuit stored in the database.

- **Add a Circuit**: Enter a name, country, length, and number of turns. The database assigns the ID, and the new circuit is sent back to confirm it was saved.

- **Delete a Circuit**: Enter an ID to remove a circuit. The deleted circuit is shown back to you.

- **Receive an Image**: Pick an image from the list the server offers (the F1 logo or a circuits image) and download it. The client saves it in its `images` folder as `image_received.png`.

- **Quit**: Disconnect from the server cleanly.

### 🧱 Architecture:

A quick look at how the pieces fit together:

- **Multithreaded Server**: The server listens on port 8888 and starts a new `ClientHandler` thread for every client, so several clients can be connected at once.
- **Simple Protocol**: The client sends a numeric command (1 to 6) and the server responds based on a `ServerRequest` enum. Data comes back as JSON.
- **DAO Pattern**: `CircuitDaoInterface` and `MySqlCircuitDao` keep all the SQL in one layer, using prepared statements. A shared `SQLConnectionDecorator` method opens and closes the connection, statement, and result set for every query.
- **JSON with Gson**: `JsonConverter` turns `Circuit` objects and lists of circuits into JSON and back.
- **Extra DAO Methods**: The DAO can also update a circuit and filter circuits with a `Predicate` (for example, all circuits in Austria). `Main` demonstrates these directly in the console.
- **File Transfer**: Images are sent as raw bytes in 4 KB chunks, preceded by the file size.

## 🔨 The Process

We started with the data access layer. We created the `Circuit` class and a DAO interface, then implemented the MySQL version with methods to get, insert, update, and delete circuits, plus a filter method that uses Java streams. A decorator method takes care of the connection logic, so each query method only has to contain its SQL.

Next, we added JSON conversion with Gson so a circuit, or a list of circuits, could be turned into text and back. At this stage we also wrote JUnit tests for the DAO methods and the JSON methods.

After that, we moved on to the client-server part. We built a server that accepts connections on a socket and hands each one to its own thread. Then we defined a simple protocol: the client sends a request number and the server answers based on it. We added the requests one by one: get one circuit, get all circuits, add, and delete.

Finally, we added file transfer so a client can download images from the server. The server sends the file size first, then the file in chunks, and the client writes the bytes to a local file.

We split the features between the three of us, and the code comments credit who wrote each method.

## 📚 What I Learned

During this project, I picked up important skills in working with databases and networks, and it improved the way I structure larger programs.

### 🗄️ Data Access Layer:

- **Separation of Concerns**: Putting all the SQL behind an interface taught me how to keep database code away from the rest of the app, so the client and server never deal with SQL directly.

### 🔌 JDBC and Prepared Statements:

- **Safe Queries**: I used prepared statements with `?` placeholders for every query and learned how to get the auto-generated ID back after an insert with `RETURN_GENERATED_KEYS`.

### 🧰 Lambdas and Functional Interfaces:

- **Reusable Code**: I wrote a decorator method that takes a lambda, so opening and closing connections happens in one place. I also used a `Predicate` with streams to filter circuits.

### 🌐 Sockets and Threads:

- **Client-Server Communication**: I learned how a server can handle many clients by giving each one its own thread, and how the client and server have to agree on the exact order of the messages they send.

### 📨 JSON with Gson:

- **Data Conversion**: I learned how to convert objects to JSON and back, including using `TypeToken` to tell Gson what type of list to expect.

### 📁 Sending Files over a Socket:

- **Working with Bytes**: I learned how to send a file by sending its size first and then reading and writing it in chunks with `DataOutputStream` and `DataInputStream`.

### 🧪 Unit Testing:

- **Testing Code**: I wrote JUnit 5 tests that compare expected and actual results for inserting, updating, deleting, filtering, and JSON conversion.

### 📈 Overall Growth:

Each part of this project helped me understand more about how real applications are put together, from the database at the bottom to the client at the top. It was more than just making a menu. It was about designing the layers, agreeing on a protocol, and working with teammates on a shared codebase.

## ✔️ How can it be improved?

- Add the update and filter options to the client menu, since they only exist in the DAO right now.
- Show a friendly message when a circuit ID is not found.
- Override `equals()` and `hashCode()` in `Circuit` so circuits can be compared by their values in tests.
- Save a downloaded image under its original name instead of always `image_received.png`.
- Move the database settings into a config file or environment variables instead of hardcoding them.
- Add a SQL script that creates the database and table with some sample circuits.
- Move the tests to `src/test/java` and run them against a separate test database.
- Use connection pooling instead of opening a new connection for every query.

## 🚚 Running the Project

To run the project in your local environment, follow these steps:

1. Clone the repository to your local machine.
2. Start MySQL and create a database named `F1` with a `Circuits` table that has these columns in order: `id` (auto-increment primary key), `circuit_name`, `country`, `length`, and `turns`. Add a few sample circuits.
3. The app connects to `jdbc:mysql://localhost:3306/F1` as `root` with an empty password. If your MySQL login is different, update the credentials in `MySqlDao.java`.
4. Open the project in IntelliJ IDEA and let Maven download the dependencies.
5. Run `Server.java` from the project root (the images are loaded from the `images` folder). It starts listening on port 8888.
6. Run `Client.java` and use the menu. You can start several clients at once.
7. Optionally, run `Main.java` to see the DAO methods in action in the console, or run `MySqlCircuitDaoTest` to run the tests. Both insert, update, and delete rows, so use a test database.

## 📽️ Video
*Link unavailable at this time.*
<!-- Add a link to a screen recording or demo of the app here -->
