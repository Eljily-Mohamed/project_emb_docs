# IHM System Overview

This document provides an overview of the IHM (Interface Homme-Machine) system we built using Electron.js. The IHM is designed to interact with an STM32 microcontroller, facilitating data communication and display via a user-friendly interface. The system consists of a browser-side application built with React.js and Tailwind CSS, and a server-side application built with Node.js to manage the database and handle serial communication using Socket.io.

## Architecture

The IHM system is composed of the following components:

1. **Browser-Side Application**: Built with React.js and styled with Tailwind CSS.
2. **Server-Side Application**: Built with Node.js, manages the database, and handles serial communication with the STM32 microcontroller.
3. **Electron.js**: Wraps the browser-side application to create a cross-platform desktop application.

### Browser-Side Application

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/1280px-React-icon.svg.png" alt="react" width="150" height="150">
<img src="https://seeklogo.com/images/T/tailwind-css-logo-5AD4175897-seeklogo.com.png" alt="tailwind-css" width="150" height="150">

### Server-Side Application

The server-side application is built using Node.js and handles the following tasks:

1. **Database Management**: Manages the storage and retrieval of sensor data in a database (sqlite3).
2. **Serial Communication**: Establishes communication with the STM32 microcontroller via a serial connection and processes the received data.
3. **Socket.io Integration**: Integrates Socket.io to facilitate real-time communication with the browser-side application.

<img src="https://upload.wikimedia.org/wikipedia/commons/d/d9/Node.js_logo.svg" alt="node js" width="150" height="150">
<img src="https://upload.wikimedia.org/wikipedia/commons/3/38/SQLite370.svg" alt="SQLite3" width="150" height="150">
<img src="https://upload.wikimedia.org/wikipedia/commons/9/96/Socket-io.svg" alt="socket js" width="150" height="150">

## Features

1. **User Interface**: Provides a user-friendly interface to display temperature and humidity data received from the STM32 microcontroller.
2. **Real-Time Updates**: Uses Socket.io to receive real-time data updates from the server and display them in the UI.
3. **Data Visualization**: Displays the sensor data in a clear and visually appealing manner, using charts.

