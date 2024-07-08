# Website-programming
### -Web Task / Creating a web interface to control the robot and display its movements using xmapp, Visual Studio Code, HTML, and CSS

#### 1. Install xmapp and Visual Studio Code

##### -I installed the xmapp program on my system, which includes Apache, MySQL, PHP, and other web essentials.
##### -I installed the Visual Studio Code program, which is a code editor, on my system.

#### 2. Created a dedicated folder for the task
##### -In the htdocs folder of xmapp, I added all the files related to the task.

#### 3. Set up the database
##### -We have the xmapp control panel and can enable the Apache and MySQL modules.
##### -From xmapp, through the Admin, I accessed the MySQL management.
##### -Created a database in the control panel.
##### -Created a table within SQL: robot_control

###### Creat_database.sql
```
CREATE DATABASE robot_control;

USE robot_control;

CREATE TABLE commands (
    id INT AUTO_INCREMENT PRIMARY KEY,
    command VARCHAR(50) NOT NULL,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```


#### 4. Create a web page for control
##### -I created a new file named index, which contains the code for the control page interface in HTML, CSS, and JavaScript.

###### index.html
```
<!DOCTYPE html>
<html>
<head>
    <title>Robot Control Panel</title>
    <style>
        .button-container {
            display: grid;
            grid-template-areas:
                " . forward . "
                "left stop right"
                " . backward . ";
            gap: 10px;
            justify-items: center;
            align-items: center;
            height: 100vh;
        }
        .button-container button {
            width: 100px;
            height: 100px;
            font-size: 16px;
            cursor: pointer;
        }
        .forward { grid-area: forward; }
        .left { grid-area: left; }
        .stop { grid-area: stop; }
        .right { grid-area: right; }
        .backward { grid-area: backward; }
    </style>
</head>
<body>
    <h1 style="text-align: center; background-color: pink; padding: 20px;">Robot Control Panel</h1>
    <div class="button-container">
        <button class="forward" onclick="sendCommand('forward')">Forward</button>
        <button class="left" onclick="sendCommand('left')">Left</button>
        <button class="stop" onclick="sendCommand('stop')">Stop</button>
        <button class="right" onclick="sendCommand('right')">Right</button>
        <button class="backward" onclick="sendCommand('backward')">Backward</button>
    </div>
    <script>
        function sendCommand(command) {
            const xhr = new XMLHttpRequest();
            xhr.open("POST", "send_command.php", true);
            xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
            xhr.onreadystatechange = function () {
                if (xhr.readyState === 4 && xhr.status === 200) {
                    alert("Command sent: " + command);
                }
            };
            xhr.send("command=" + command);
        }
    </script>

</body>
</html>
```


#### 5. Create the control code
##### I created a new file named send_command.php and added code to handle the robot's movements and update them in the database.

###### send_command.php
```
<?php
$servername = "localhost";
$username = "root";
$password = "";
$dbname = "robot_control";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $command = $_POST['command'];
    $stmt = $conn->prepare("INSERT INTO commands (command) VALUES (?)");
    $stmt->bind_param("s", $command);

    if ($stmt->execute()) {
        echo "New command inserted successfully";
    } else {
        echo "Error: " . $stmt->error;
    }

    $stmt->close();
}

$conn->close();
?>
```


#### 6. Create a web page to display the last robot movement data
##### -I created a new file named display_last_command.php and added code to retrieve the last movement the user pressed to control the robot from the database and display it.

###### display_last_command.php
```
<?php
$servername = "localhost";
$username = "root";
$password = ""; // Change if you set a password
$dbname = "robot_control";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

// Retrieve the last command
$sql = "SELECT command, timestamp FROM commands ORDER BY id DESC LIMIT 1";
$result = $conn->query($sql);

$last_command = null;
$timestamp = null;

if ($result->num_rows > 0) {
    // Output data of each row
    while($row = $result->fetch_assoc()) {
        $last_command = $row["command"];
        $timestamp = $row["timestamp"];
    }
} else {
    $last_command = "No commands found.";
}

$conn->close();
?>
<!DOCTYPE html>
<html>
<head>
    <title>Last Command</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
        }
        .command-container {
            display: inline-block;
            padding: 20px;
            background-color: #add8e6;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        .command {
            font-size: 24px;
            font-weight: bold;
        }
        .timestamp {
            font-size: 18px;
            color: #555;
        }
    </style>
</head>
<body>
    <div class="command-container">
        <div class="command">Last Command: <?php echo htmlspecialchars($last_command); ?></div>
        <?php if ($timestamp): ?>
            <div class="timestamp">Timestamp: <?php echo htmlspecialchars($timestamp); ?></div>
        <?php endif; ?>
    </div>
    
</body>
</html>
```
#### My Web page
###### 1- First we make sure Apache and MySQL it is working
<img src="https://github.com/Samar-Hamed2003/Website-programming/assets/173670288/df3aa978-8712-4932-b48a-0b0d97365182.jpg" width="50%" height="50%">

###### 2- Robot control page and its interaction while clicking on the direction
<img src="https://github.com/Samar-Hamed2003/Website-programming/assets/173670288/da5c962a-75b1-4158-bb47-e8ea43594c1e.jpg" width="50%" height="50%">
<img src="https://github.com/Samar-Hamed2003/Website-programming/assets/173670288/89306e20-d84d-49c0-ae68-68f2d7299b04.jpg" width="50%" height="50%">
###### 3-A page that displays the last clicked movement of the robot's direction
<img src="https://github.com/Samar-Hamed2003/Website-programming/assets/173670288/f35a313e-d58c-4dc2-bb75-4c73bdddfc4c.jpg" width="50%" height="50%">

###### 4-Database interaction
<img src="https://github.com/Samar-Hamed2003/Website-programming/assets/173670288/764d0762-bd77-4caf-8d08-335efd2bd34d.jpg" width="50%" height="50%">


### It is very important and useful. I learned something new and it will help me in my specialty field 😊
