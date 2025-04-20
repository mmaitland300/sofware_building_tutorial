# Building a Graphical File Manager in Python

Welcome to your building software from scratch advanced guide! In this guide, you’ll build a desktop application from scratch using Python and its built-in Tkinter library. This application will allow you to create, view, edit, and delete text files. We'll cover foundational concepts, provide practical code examples, explain how software interacts with the operating system (using Linux Mint as our example environment), and introduce professional software development practices like code structure (MVC) and testing.

---

## 1. Introduction: What Are We Building and Why?

### 1.1 Project Goal

We are building a **desktop file management application** using Python and Tkinter that enables users to create, view/edit, and delete simple text (`.txt`) files within a specific project directory. This project serves as a hands-on exercise in GUI programming and file handling, while also providing a gateway to understanding how applications interact with the computer’s operating system.

### 1.2 Why a GUI? Practical vs. Command-Line Tools

We're building a Graphical User Interface (GUI) because GUIs offer immediate visual feedback and are generally easier for end-users to learn. However, it's worth noting the contrast with command-line interface (CLI) tools:

* **Command-Line Tools:** Offer flexibility, fine-grained control, and are highly efficient for automation and complex, repetitive tasks. They typically consume fewer system resources
.
* **GUIs:** Excel in ease of use for interactive tasks but can be less suitable for scripting or large-scale automation.

Understanding this trade-off helps inform design decisions. For this learning exercise, a GUI provides a tangible, interactive result.

### 1.3 Core Concepts We'll Encounter

* **File Input/Output (I/O):** Reading from and writing to files is fundamental but requires care. Errors can lead to data loss if not handled properly. We'll need to manage file encodings (we'll use UTF-8)  and handle potential errors like files not being found or permission issues.
* **Operating System Interaction:** Your Python code doesn't directly command the computer's hardware like the hard drive or network card. Instead, it interacts with the **Operating System** (OS) – Linux Mint in this tutorial's examples. When your program wants to perform an action like creating a file, reading data, or opening a window, it makes a request to the OS using a specific mechanism called a **system call**. The OS acts as a mandatory intermediary or gatekeeper; it receives these requests, validates them (e.g., checking file permissions), translates them into low-level instructions for the hardware, and manages access to ensure stability and security for the whole system. Understanding this relationship helps clarify why things like file permissions exist and why Python code behaves consistently across different hardware running the same OS.

* **Application Structure:** As applications grow, keeping code organized is crucial. We'll refactor our initial simple structure into a Model-View-Controller (MVC) pattern to separate concerns: data handling (Model), user interface (View), and application logic (Controller).
* **Development Environment:** Setting up a clean, isolated environment for each project prevents conflicts between dependencies. We'll use Python's built-in `venv` for this.
* **Testing:** How do we know our code works correctly, especially after making changes? We'll write automated tests to verify our application's logic.

Let's get started by setting up our development environment.

## 2. Setting Up Your Workshop: The Development Environment on Linux Mint

This section guides you through configuring your development environment specifically on **Linux Mint**, which uses the APT package manager. If you are using a different operating system (like Fedora, Arch Linux, macOS, or Windows), the general steps (installing Python 3, installing VS Code, creating a virtual environment) are similar, but the specific commands for installing software via the terminal will differ (e.g., using `dnf` on Fedora, `pacman` on Arch, `brew` on macOS, `winget` or `choco` on Windows, or downloading installers directly). Please refer to the documentation for your specific OS and its package manager(s).

We'll install necessary tools and set up an isolated project environment.

### 2.1 Checking and Installing Python

1.  **Open a Terminal:** Press `Ctrl+Alt+T` or find "Terminal" in your applications menu.
2.  **Check Python 3:** Run `python3 --version`. You should see `Python 3.x.x`. Most modern Linux Mint versions include it.
3.  **Install (If Needed):** If Python 3 is missing, install it using the Advanced Package Tool (APT), Linux Mint's package manager:
    ```bash
    sudo apt update
    sudo apt install python3
    ```
    `sudo` grants temporary administrator privileges for installation. If you encounter dependency issues (sometimes caused by manually installing `.deb` files with `dpkg` ), running `sudo apt --fix-broken install` can often resolve them.

### 2.2 Installing Visual Studio Code (VS Code)

VS Code is a popular, free Integrated Development Environment (IDE) with features like syntax highlighting, debugging tools, and extensions that enhance productivity.

1.  **Download:** Visit the [VS Code official website](https://code.visualstudio.com/) and download the `.deb` package for Debian/Ubuntu (Linux Mint is based on Ubuntu).
2.  **Install:**
    * **Graphical:** Double-click the downloaded `.deb` file in your file manager.
    * **Terminal:** Navigate to your Downloads folder (e.g., `cd ~/Downloads`) and run:
    ```bash
    # Navigate to your Downloads folder first, e.g., cd ~/Downloads
    sudo dpkg -i code_*.deb
    # Run this immediately after to fix potential dependencies:
    sudo apt --fix-broken install
    ```
    Replace `code_*.deb` with the actual filename. Using `dpkg` directly can sometimes lead to dependency issues, which `--fix-broken install` resolves.

### 2.3 Creating a Project Folder and Virtual Environment

A clean project structure and an isolated environment are crucial.

1.  **Create Project Folder:** In your terminal, decide where you want your project (e.g., in your Home directory):
    ```bash
    mkdir ~/FileManagerProject
    cd ~/FileManagerProject
    ```
    This creates a folder named `FileManagerProject` in your home directory (`~`) and navigates into it.
2.  **Open in VS Code:** While inside the `FileManagerProject` folder in the terminal, run:
    ```bash
    code .
    ```
    The `.` refers to the current directory. Alternatively, use VS Code's `File > Open Folder...` menu.
3.  **Create Virtual Environment:** Open VS Code's integrated terminal (`Terminal > New Terminal` or `Ctrl+\``). Run:
    ```bash
    python3 -m venv venv
    ```
    This creates a `venv` subfolder containing a project-specific Python interpreter and library space.  
4. **Why?** Virtual environments isolate project dependencies. Packages installed here won't interfere with other projects or the system's Python installation. They work by modifying the Python interpreter's search path for modules. Note: They *don't* isolate system-level dependencies (like C libraries). For that level of isolation, tools like Docker are used, but that's beyond our scope here.
5.  **Activate Virtual Environment:** In the *same* terminal:
    ```bash
    source venv/bin/activate
    ```
    Your terminal prompt should change, usually showing `(venv)` at the beginning. You need to activate the environment *each time* you work on this project in a new terminal session.
6.  **Set VS Code Interpreter:** Press `Ctrl+Shift+P`, type `Python: Select Interpreter`, and choose the one that includes `(.venv)` or points to `./venv/bin/python`. This tells VS Code to use the project's isolated environment for running and debugging code.
7.  **(Recommended) Create `.gitignore`:** If you plan to use Git version control, create a file named `.gitignore` in the project root (`FileManagerProject/`) with the following content:
    ```
    venv/
    __pycache__/
    *.pyc
    ```
    This tells Git to ignore the virtual environment folder and Python's compiled bytecode cache files.

## 3. Laying the Foundation: Python Basics for File Management

Before building the GUI, let's review the core Python concepts we'll use.

### 3.1 Variables, Strings, and Functions

* **Variables:** Names that store data. We'll use them for filenames, content, etc..
    ```python
    filename = "my_notes.txt"
    file_content = "This is the content."
    ```
* **Strings:** Sequences of characters, used for filenames, messages, and file content. Defined with single (`'`) or double (`"`) quotes.
    ```python
    message = 'File saved successfully!'
    ```
* **Functions:** Reusable blocks of code that perform specific tasks. They help organize code and make it modular.
    ```python
    def show_error_message(error_details):
        print(f"An error occurred: {error_details}")

    # Calling the function
    show_error_message("File not found.")
    ```
    We follow standard Python naming conventions (e.g., `snake_case` for variables and functions) as recommended by PEP 8.

### 3.2 Basic File I/O with `open()` and `pathlib`

Interacting with files is fundamental. Python offers built-in tools for this.

* **The `pathlib` Module:** Introduced in Python 3.4, `pathlib` provides an object-oriented way to handle filesystem paths. It makes tasks like joining paths, checking existence, and reading/writing files cleaner and less error-prone than older methods using string manipulation with the `os` module. We'll use `pathlib.Path` objects to represent file paths.

    ```python
    from pathlib import Path

    # Create a Path object (represents a potential file/directory)
    # Assumes your script is in the project root for this example
    project_dir = Path(".") # Current directory
    filename = "example.txt"
    file_path = project_dir / filename # Use '/' to join paths reliably

    print(f"Will operate on: {file_path.resolve()}") # resolve() gives the full absolute path
    ```

* **Opening Files with `open()`:** The built-in `open()` function prepares a file for reading or writing. You need to specify the file path (ideally as a string derived from a `Path` object) and a *mode*:
    * `'r'`: Read (default). Fails if the file doesn't exist.
    * `'w'`: Write. Creates the file if it doesn't exist, **overwrites it completely** if it does. Use with caution!
    * `'a'`: Append. Creates the file if it doesn't exist, adds data to the end if it does.
    * `'x'`: Exclusive creation. Creates the file, but fails if it already exists.
    * Add `b` for binary mode (e.g., `'rb'`, `'wb'`), but we'll stick to text mode (`t` is implied).

* **The `with` Statement (Context Manager):** This is the **highly recommended** way to work with files using `open()`. It automatically ensures the file is properly closed (releasing system resources) even if errors occur during processing.
    ```python
    # Writing to a file using 'with' and pathlib
    try:
        # Convert Path object to string for open() if needed, though often optional now
        # Specify encoding for reliable text handling
        with open(file_path, "w", encoding="utf-8") as f:
            f.write("Hello from pathlib!\n")
            f.write("This is a second line.")
        print("File written successfully using open().")
    except OSError as e:
        # Catch OS-level errors like permission denied, disk full, etc.
        print(f"Error writing file: {e}")

    # Reading from a file using 'with'
    try:
        with open(file_path, "r", encoding="utf-8") as f:
            content = f.read() # Reads the entire file content into a string
        print("File content read using open():")
        print(content)
    except FileNotFoundError:
        # Specific error if the file doesn't exist when opening in 'r' mode
        print(f"Error: File not found at {file_path}")
    except OSError as e:
        print(f"Error reading file: {e}")
    ```

* **`pathlib` Convenience Methods:** `Path` objects also have helpful methods like `.read_text()` and `.write_text()` that handle opening, reading/writing, and closing the file in one step, often simplifying common tasks.
    ```python
    # Writing using pathlib's write_text()
    try:
        content_to_write = "Overwritten by write_text().\nHello again!"
        file_path.write_text(content_to_write, encoding="utf-8")
        print("File written successfully using path.write_text().")
    except OSError as e:
        print(f"Error writing file with write_text(): {e}")

    # Reading using pathlib's read_text()
    try:
        content = file_path.read_text(encoding="utf-8")
        print("File content read using path.read_text():")
        print(content)
    except FileNotFoundError:
        print(f"Error: File not found at {file_path}")
    except OSError as e:
        print(f"Error reading file with read_text(): {e}")

    ```
* **Encoding:** Always specify `encoding="utf-8"` when working with text files (`open()`, `read_text()`, `write_text()`). UTF-8 supports a vast range of characters used worldwide and avoids common errors related to default system encodings.

* **Buffering:** When you write to a file (using `f.write()` or `path.write_text()`), the Operating System often uses a *buffer* – a temporary storage area in memory. Data might not be written to the physical disk immediately; it might wait until the buffer is full or the file is explicitly *flushed* or *closed*. This improves performance by reducing frequent, slow disk accesses. Reading also uses buffering. The `with` statement and the `pathlib` convenience methods automatically handle buffer flushing and file closing correctly when the block is exited or the operation completes.

### 3.3 Error Handling with `try...except`

File operations can fail for many reasons (file doesn't exist, no permission, disk full, etc.). We use `try...except` blocks to handle these potential errors gracefully instead of crashing.

    ```python
    filename = "non_existent_file.txt"
    try:
        with open(filename, "r", encoding="utf-8") as f:
            content = f.read()
            print("File read successfully.") # This won't run if error occurs
    except FileNotFoundError:
        print(f"Error: The file '{filename}' was not found.")
    except PermissionError:
        print(f"Error: Permission denied when trying to read '{filename}'.")
    except OSError as e: # Catch other potential OS-level I/O errors
        print(f"An unexpected OS error occurred while reading '{filename}': {e}")
    except Exception as e: # A fallback for truly unexpected errors (use sparingly)
        print(f"An unexpected error occurred: {e}")
    ```
* **Catch Specific Exceptions:** It's crucial to catch *specific* expected exceptions like `FileNotFoundError` or `PermissionError` first. This allows you to handle different error conditions appropriately (e.g., telling the user the file is missing vs. telling them they don't have permission). Avoid using a generic `except Exception:` unless it's a last resort fallback, as catching overly broad exceptions can hide unrelated bugs or prevent the program from exiting when it encounters a truly unexpected, unrecoverable state.

* **Using `OSError`:** Many file-related exceptions, including `FileNotFoundError`, `PermissionError`, `IsADirectoryError`, and others related to underlying operating system issues (like "disk full" or "invalid argument"), inherit from a common base class called `OSError`. Sometimes, after handling the most common specific exceptions, you might catch `OSError` to handle a broader range of potential I/O problems without listing every single possibility.

### 3.4 Importing Modules

Python's power comes from its extensive standard library and third-party packages. We use the `import` statement to bring code (functions, classes) from other modules into our script.

```python
import os         # Provides OS-level functions (less common now with pathlib)
from pathlib import Path # Modern object-oriented way to handle file paths

import tkinter    # The core Tkinter library for GUI widgets
from tkinter import ttk  # Themed Tkinter widgets (more modern look)
from tkinter import messagebox # Standard dialog boxes (info, error, etc.)
from tkinter import filedialog # Standard file open/save dialogs
# We will import simpledialog later when needed

import threading  # For running tasks concurrently (e.g., background I/O)
import queue      # For safe communication between threads
```

We'll import these as needed throughout the project.

### 3.5 Handling Concurrent Operations (Brief Introduction)

Graphical User Interfaces (GUIs like the one we're building with Tkinter) typically run in a single main thread. This thread handles drawing the interface, responding to button clicks, processing keyboard input, etc.

What happens if you perform a long-running task directly within this main thread (e.g., reading a very large file, downloading data from the internet)? The entire GUI will freeze and become unresponsive until that task completes. This creates a poor user experience.

To avoid freezing the UI, long-running tasks (especially Input/Output operations like file access or network requests) should be performed in a separate *background thread*.

* **`threading` Module:** Python's `threading` module allows you to create and manage these background threads. You can start a task in a new thread, allowing the main GUI thread to remain responsive.

* **`queue` Module:** However, there's a crucial rule in most GUI toolkits (including Tkinter): **You must not directly update GUI elements from a background thread.** Doing so can lead to unpredictable behaviour and crashes. The `queue` module provides thread-safe data structures (like `queue.Queue`) that allow safe communication *between* threads. Typically, the background thread performs its task and puts the result (or any errors) into a queue. The main GUI thread periodically checks this queue (using a mechanism like Tkinter's `root.after()`) and safely updates the GUI based on the information received from the queue.

We will use `threading` and `queue` later when implementing file loading to keep our application responsive. This brief introduction provides the necessary context for why they are important in GUI applications.

## 4. Building the Basic User Interface (View Shell)
Let's start building the visual part of our application using Tkinter. We'll create the main window and the basic layout for controls and the text area. We'll put this UI code in a file named view.py.

```Python

# view.py
import tkinter as tk
from tkinter import ttk, messagebox, filedialog, simpledialog

class FileManagerView:
    """
    Handles the graphical user interface (GUI) components and layout
    using Tkinter. It displays widgets and forwards user actions
    to the Controller (which we'll build later).
    """
    def __init__(self, root):
        """Initialize the main window and UI elements."""
        self.root = root
        self.root.title("Simple Text File Manager")
        self.root.geometry("700x500") # Set initial window size (width x height)

        # We will store references to important widgets as instance attributes
        self.filename_entry = None
        self.text_area = None

        # Call a method to create the actual UI components
        self.setup_ui()

    def setup_ui(self):
        """Create and arrange the widgets in the main window."""
        # --- Top Frame for Controls ---
        # Use a Frame widget to group related controls
        top_frame = ttk.Frame(self.root, padding="10") # padding adds space inside the frame
        # pack() is a geometry manager: place the frame at the top, expanding horizontally
        top_frame.pack(fill='x')

        # Label for the filename entry
        filename_label = ttk.Label(top_frame, text="Filename:")
        # grid() is another geometry manager: place widgets in rows/columns
        # sticky='W' aligns the widget to the West (left) side of its grid cell
        filename_label.grid(row=0, column=0, padx=5, pady=5, sticky='W')

        # Entry widget for typing the filename
        self.filename_entry = ttk.Entry(top_frame, width=40)
        self.filename_entry.grid(row=0, column=1, padx=5, pady=5, sticky='W')

        # Buttons for actions (we'll connect commands later)
        # We use ttk.Button for themed buttons
        create_button = ttk.Button(top_frame, text="Create")
        create_button.grid(row=0, column=2, padx=5, pady=5)

        open_button = ttk.Button(top_frame, text="Open")
        open_button.grid(row=0, column=3, padx=5, pady=5)

        save_button = ttk.Button(top_frame, text="Save")
        save_button.grid(row=0, column=4, padx=5, pady=5)

        delete_button = ttk.Button(top_frame, text="Delete")
        delete_button.grid(row=0, column=5, padx=5, pady=5)

        # --- Main Frame for Text Area and Scrollbar ---
        text_frame = ttk.Frame(self.root, padding="10")
        # expand=True allows the frame to grow when the window is resized
        # fill='both' makes it fill space horizontally and vertically
        text_frame.pack(expand=True, fill='both')

        # Text widget for displaying/editing file content
        # wrap='word' makes lines wrap at word boundaries
        # undo=True enables the built-in undo/redo stack
        self.text_area = tk.Text(text_frame, wrap='word', undo=True, height=15, width=60)
        # pack it on the left, allowing it to expand
        self.text_area.pack(side='left', expand=True, fill='both')

        # Scrollbar for the text area
        scrollbar = ttk.Scrollbar(text_frame, orient='vertical', command=self.text_area.yview)
        scrollbar.pack(side='right', fill='y') # Place it on the right, filling vertically

        # Link the scrollbar back to the text area
        self.text_area.config(yscrollcommand=scrollbar.set)

    def run(self):
        """Starts the Tkinter event loop."""
        self.root.mainloop()

# --- Main execution block ---
# This ensures the code runs only when the script is executed directly
if __name__ == '__main__':
    main_window = tk.Tk()      # Create the root window
    app_view = FileManagerView(main_window) # Create an instance of our View class
    app_view.run()             # Start the application's event loop

```
**Understanding Tkinter Geometry Managers (`pack`, `grid`, `place`)**

Tkinter provides three main geometry managers to control the size and position of widgets within a container (like a window or a frame):

1.  **`pack()`:** Arranges widgets in blocks before placing them in the parent. It's often used for simpler layouts, stacking widgets vertically or horizontally, and making them fill available space. Options like `side` (`'top'`, `'bottom'`, `'left'`, `'right'`), `fill` (`'x'`, `'y'`, `'both'`), and `expand` (`True`/`False`) control its behavior.
2.  **`grid()`:** Arranges widgets in a flexible grid (rows and columns). It's generally more powerful for complex layouts, aligning elements precisely. Options like `row`, `column`, `sticky` (to control alignment within a grid cell, e.g., `'W'`, `'E'`, `'NS'`), `padx`/`pady` (padding), `rowspan`/`columnspan` are used.
3.  **`place()`:** Allows you to specify the exact size and position (x, y coordinates) of widgets. It offers precise control but makes layouts less adaptable to window resizing or content changes, so it's used less often for general application structure.

**The Golden Rule:** You **cannot** mix `pack` and `grid` directly within the *same parent container* (e.g., applying `pack` to one button and `grid` to another button if both buttons are directly inside the *same* frame). Doing so will confuse Tkinter and likely cause your application to hang or behave unpredictably.

**How This Tutorial Uses Them:** This tutorial *follows* the rule by using different managers in *nested* containers:
* The main window (`root`) or major layout panes (like `main_pane`) might use `pack()` to arrange larger sections (like `top_frame` and `text_frame`, or `browser_frame` and `editor_frame`).
* *Inside* one of those sections, like `top_frame`, we then use `grid()` to arrange the labels, entry fields, and buttons neatly in rows and columns relative to each other *within that specific frame*.

This nesting approach is perfectly valid and common in Tkinter applications. The key is that widgets directly inside `top_frame` *only* use `grid`, while `top_frame` itself is positioned within its parent using `pack` (or `main_pane.add()`).
Explanation:

__init__ sets up the main window (tk.Tk()) passed to it.
setup_ui creates and arranges the widgets using ttk.Frame, ttk.Label, ttk.Entry, ttk.Button, tk.Text, and ttk.Scrollbar.

The if __name__ == '__main__': block is standard Python practice. It creates the root window, instantiates our FileManagerView, and starts the Tkinter event loop (mainloop()), which waits for user interactions (like button clicks).

Save this code as view.py in your FileManagerProject directory. You can run it now (python view.py in your activated virtual environment) to see the basic window structure, although the buttons won't do anything yet.

## 5. Structuring the Application: Model-View-Controller (MVC)
Our current view.py only handles the UI. If we added file operations directly into its methods, the class would become very large and mix UI logic (button clicks, text display) with business logic (reading/writing files, validation). This makes the code hard to maintain, test, and extend.

A common solution is the Model-View-Controller (MVC) architectural pattern (or variations like MVP, MVVM). It separates the application into three distinct layers:

Model: Manages the application's data and business logic. In our case, this means all interactions with the file system (creating, reading, writing, deleting, listing files). It knows nothing about the user interface. (file_service.py)
View: Responsible for presenting data to the user and capturing user input. It's the GUI layer (our FileManagerView class). It knows nothing about how files are actually stored or manipulated. It only knows how to display information and trigger actions. (view.py)

Controller: Acts as the intermediary between the Model and the View. It takes user input from the View (e.g., "Save button clicked"), tells the Model what to do (e.g., "Save this content to this file"), gets the result from the Model, and updates the View accordingly (e.g., "Show success message" or "Display error"). (controller.py)
Benefits of MVC:

Separation of Concerns: Each layer has a specific responsibility, making the code easier to understand and modify.

Testability: We can test the Model's file logic independently of the GUI. We can test the Controller's logic by simulating View events and checking interactions with a mock Model.
Maintainability: Changes to the UI (View) are less likely to break the file handling logic (Model), and vice versa.
Let's implement the Model and Controller layers.

5.1 Implementing the Model (file_service.py)
Create a new file named file_service.py. This class will handle all direct file system operations and enforce our rules (e.g., only .txt files within the project directory).

```Python
# file_service.py
from pathlib import Path
import os # For potential errors like Disk full etc.

class FileOperationError(Exception):
    """Custom exception for file operation errors within our application."""
    pass

class FileService:
    """
    Model layer: Handles all direct interactions with the file system
    for text files within a specified base directory.
    Enforces security boundaries and allowed file types.
    """
    ALLOWED_EXTENSIONS = {'.txt'}

    def __init__(self, base_dir: Path):
        """
        Initializes the service with a base directory.
        All operations are restricted to this directory.
        """
        # Ensure base_dir is an absolute path and exists
        self.base_dir = base_dir.resolve()
        if not self.base_dir.is_dir():
            # You might want to create it, but for this tutorial, we assume it exists
            # or raise an error if it doesn't. Let's raise for clarity.
            raise FileOperationError(f"Base directory not found: {self.base_dir}")

    def _validate_path(self, filename: str) -> Path:
        """
        Validates the filename and resolves it to a secure path within base_dir.
        Raises FileOperationError if validation fails (e.g., empty name, invalid
        extension, path traversal attempt, operation on base directory itself).
        """
        if not filename:
            raise FileOperationError("Filename cannot be empty.")

        # Clean whitespace. Allow '/' for now, let resolve() and is_relative_to() handle structure.
        cleaned_filename = filename.strip()
        if not cleaned_filename: # Check again after stripping
             raise FileOperationError("Filename cannot be empty.")

        candidate_path = self.base_dir / cleaned_filename

        # Resolve non-strictly first for security checks before existence checks
        try:
            resolved_path_non_strict = candidate_path.resolve(strict=False)
        except Exception as e:
             # Catch resolution errors early (e.g., filename too long, illegal OS chars)
             # These often indicate invalid filenames regardless of location.
             raise FileOperationError(f"Invalid filename or path format: {e}")

        # --- Security Checks ---
        ACCESS_DENIED_MSG = "Access denied: Path is outside the allowed directory."

        # Check 1: Ensure the resolved path structure is *within* the base directory.
        # This is the primary defense against directory traversal (e.g., '../').
        try:
            # Use resolved_path_non_strict for the check
            if not resolved_path_non_strict.is_relative_to(self.base_dir):
                raise FileOperationError(ACCESS_DENIED_MSG)
        except ValueError: # is_relative_to raises ValueError if paths are on different drives
            # Append details to the standard message for better debugging in tests/logs
            raise FileOperationError(ACCESS_DENIED_MSG + " (Different drive or location)")
        except AttributeError:
            # Fallback for Python < 3.9 - check common ancestor
            # Use resolved_path_non_strict for the check
            if self.base_dir != resolved_path_non_strict and self.base_dir not in resolved_path_non_strict.parents:
                 raise FileOperationError(ACCESS_DENIED_MSG + " (Compatibility check)")

        # Check 2: Prevent direct operation on the base directory itself.
        if resolved_path_non_strict == self.base_dir:
             raise FileOperationError("Operation on the base directory itself is not allowed.")

        # Check 3: Ensure the file extension is allowed (case-insensitive).
        # Check suffix based on the resolved path structure.
        if resolved_path_non_strict.suffix.lower() not in self.ALLOWED_EXTENSIONS:
            raise FileOperationError(f"Invalid file type: Extension '{resolved_path_non_strict.suffix}' is not allowed. Only {self.ALLOWED_EXTENSIONS} are permitted.")

        # Return the structurally validated path.
        # Calling methods (e.g., read, write, delete) should handle FileNotFoundError
        # if they require the file to actually exist at this path. They can use
        # `path.exists()` or `path.resolve(strict=True)` internally if needed.
        return resolved_path_non_strict

    def create(self, filename: str) -> None:
        """Creates a new, empty text file. Fails if the file already exists."""
        path = self._validate_path(filename)
        try:
            # 'x' mode ensures exclusive creation
            with open(path, "x", encoding="utf-8") as f:
                pass # Just create the empty file
        except FileExistsError:
            raise FileOperationError(f"File already exists: {filename}")
        except OSError as e:
            raise FileOperationError(f"Unable to create file '{filename}': {e}")

    def read(self, filename: str) -> str:
        """Reads the content of a text file."""
        path = self._validate_path(filename)
        try:
            if not path.is_file(): # Explicit check if it's a file before reading
                 raise FileNotFoundError()
            return path.read_text(encoding="utf-8")
        except FileNotFoundError:
            raise FileOperationError(f"File not found: {filename}")
        except OSError as e:
            raise FileOperationError(f"Unable to read file '{filename}': {e}")

    def write(self, filename: str, content: str) -> None:
        """Writes (or overwrites) content to a text file."""
        path = self._validate_path(filename)
        try:
            # Check if it exists and IS a file before writing. Don't overwrite directories!
            if path.exists() and not path.is_file():
                raise FileOperationError(f"Cannot write: '{filename}' is not a regular file.")
            path.write_text(content, encoding="utf-8")
        except OSError as e:
            raise FileOperationError(f"Unable to write file '{filename}': {e}")

    def delete(self, filename: str) -> None:
        """Deletes a text file."""
        path = self._validate_path(filename)
        try:
            if not path.is_file(): # Check if it's actually a file before unlinking
                raise FileNotFoundError()
            path.unlink() # Deletes the file
        except FileNotFoundError:
            raise FileOperationError(f"File not found: {filename}")
        except OSError as e:
            raise FileOperationError(f"Unable to delete file '{filename}': {e}")

    def list_files(self) -> list[str]:
        """Returns a sorted list of allowed filenames in the base directory."""
        try:
            files = [
                p.name for p in self.base_dir.iterdir()
                if p.is_file() and p.suffix.lower() in self.ALLOWED_EXTENSIONS
            ]
            return sorted(files)
        except OSError as e:
            raise FileOperationError(f"Unable to list files in directory '{self.base_dir}': {e}")

    def rename(self, old_filename: str, new_filename: str) -> None:
        """Renames a file, performing necessary validations."""
        # Validate the NEW name first to catch invalid extensions
        new_path = self._validate_path(new_filename)

        # Now validate the old filename exists
        old_path = self._validate_path(old_filename)
        if not old_path.is_file():
            raise FileOperationError(f"File not found: {old_filename}")

        if new_path.exists():
            raise FileOperationError(f"Cannot rename: Target file '{new_filename}' already exists.")

        try:
            old_path.rename(new_path)
        except OSError as e:
            raise FileOperationError(
                f"Unable to rename file '{old_filename}' to '{new_filename}': {e}"
            )
```
#### Key points:
We use pathlib.Path for modern, object-oriented path manipulation.
_validate_path is crucial for security. It prevents directory traversal (../) by ensuring the resolved path is still within base_dir using relative_to(). It also enforces the .txt extension and checks for empty/invalid filenames.
All public methods (create, read, write, delete, list_files, rename) first validate the path(s).
They perform the requested file operation using appropriate methods (open with 'x', read_text, write_text, unlink, iterdir, rename).
Specific exceptions (FileExistsError, FileNotFoundError, OSError) are caught and re-raised as our custom FileOperationError, providing a consistent error type for the Controller to handle.

### 5.2 Implementing the Controller (controller.py)
Create controller.py. This class connects the View and the Model. It will be instantiated by the View and will hold an instance of the FileService.

```Python
# controller.py
from pathlib import Path
# Import the Model and its custom exception
from file_service import FileService, FileOperationError

class FileController:
    """
    Controller layer: Mediates between the View (GUI) and the Model (FileService).
    It translates user actions from the View into operations on the Model
    and returns results (or errors) back to the View.
    """
    def __init__(self, base_dir: Path):
        """Initializes the Controller with a FileService instance."""
        try:
            # The Controller creates the Model instance
            self.service = FileService(base_dir)
        except FileOperationError as e:
            # If the base directory itself is invalid, we can't proceed.
            # This error needs to be shown to the user early, likely by the View.
            # We re-raise it for the View's __init__ to catch.
            raise

    # --- Methods corresponding to user actions ---

    def create_file(self, filename: str):
        """Handles request to create a file."""
        # The Controller simply delegates the call to the Model.
        # It catches the Model's specific exception and re-raises it.
        # The View will catch this and display an appropriate message.
        try:
            self.service.create(filename)
        except FileOperationError:
            raise # Re-throw for the View to handle

    def read_file(self, filename: str) -> str:
        """Handles request to read a file."""
        try:
            return self.service.read(filename)
        except FileOperationError:
            raise # Re-throw for the View to handle

    def write_file(self, filename: str, content: str):
        """Handles request to write a file."""
        try:
            self.service.write(filename, content)
        except FileOperationError:
            raise # Re-throw for the View to handle

    def delete_file(self, filename: str):
        """Handles request to delete a file."""
        try:
            self.service.delete(filename)
        except FileOperationError:
            raise # Re-throw for the View to handle

    def list_files(self) -> list[str]:
        """Handles request to list files."""
        try:
            return self.service.list_files()
        except FileOperationError:
            raise # Re-throw for the View to handle

    def rename_file(self, old_filename: str, new_filename: str):
        """Handles request to rename a file."""
        try:
            self.service.rename(old_filename, new_filename)
        except FileOperationError:
            raise # Re-throw for the View to handle
```
Key points:

* The Controller imports the FileService and FileOperationError from the Model.
It takes the base_dir (we'll define this in the View) and creates a FileService instance.
Its methods (create_file, read_file, etc.) directly correspond to the user actions the View will trigger.
Crucially, the Controller methods do very little logic themselves. They primarily:
Receive requests from the View.
Call the appropriate method on the FileService (Model).
Catch `FileOperationError` from the Model and re-raise it. This is a key aspect of the design. The Model (`FileService`) raises its own custom, consistent `FileOperationError` regardless of the specific underlying issue (`FileNotFoundError`, `PermissionError`, `FileExistsError`, general `OSError`, etc.). The Controller simply catches *this specific type* and passes it up to the View. This decouples the View from the low-level details of file system errors; the View only needs to know how to handle the application-specific `FileOperationError` (typically by showing its message to the user).
Return data (like file content or file lists) from the Model back to the View.
The Controller does not import tkinter. It has no knowledge of GUI widgets.
Now we have the Model (file_service.py) handling file logic and the Controller (controller.py) ready to mediate. Next, we'll update the View (view.py) to use the Controller.

## 6. Connecting the Layers and Implementing Features
Let's modify view.py to create and use the FileController, connect the buttons to controller methods, and implement the core features (Create, Open, Save, Delete).

### 6.1 Updating view.py to Use the Controller
Modify view.py as follows:

```Python

# view.py
import tkinter as tk
from tkinter import ttk, messagebox, filedialog, simpledialog
from pathlib import Path
import threading # For background tasks
import queue     # For thread communication

# Import the Controller and its potential initialization error
from controller import FileController, FileOperationError
# Import the Model's error for specific handling if needed later,
# though often the Controller re-raises it uniformly.
# from file_service import FileOperationError as ServiceError # Alias if needed

class FileManagerView:
    """
    Handles the graphical user interface (GUI) components and layout
    using Tkinter. It displays widgets and forwards user actions
    to the Controller.
    """
    def __init__(self, root):
        """Initialize the main window, controller, and UI elements."""
        self.root = root
        self.root.title("Simple Text File Manager")
        self.root.geometry("700x500")

        # --- Define Project Root ---
        # All file operations will be relative to the directory containing view.py
        # Using resolve() makes it an absolute path.
        self.PROJECT_ROOT = Path(__file__).parent.resolve()

        # --- Initialize Controller ---
        try:
            # The View creates the Controller, passing the base directory
            self.controller = FileController(self.PROJECT_ROOT)
        except FileOperationError as e:
            # Handle critical error if Controller couldn't initialize (e.g., base dir invalid)
            messagebox.showerror("Initialization Error", f"Failed to initialize File Controller:\n{e}\nApplication will exit.")
            self.root.quit() # Exit the application
            return # Stop further initialization

        # --- UI State Variables ---
        self.filename_entry = None
        self.text_area = None
        self._current_filename = None # Track the name of the currently loaded/saved file
        self._is_modified = False     # Track unsaved changes
        self._load_queue = queue.Queue() # Queue for results from background threads

        # --- Build the UI ---
        self.setup_ui()

        # --- Bind Events ---
        # Track text modifications
        self.text_area.bind('<<Modified>>', self._on_text_modified)
        # Handle window closing
        self.root.protocol("WM_DELETE_WINDOW", self._on_exit)


    def setup_ui(self):
        """Create and arrange the widgets in the main window."""
        # --- Top Frame for Controls ---
        top_frame = ttk.Frame(self.root, padding="10")
        top_frame.pack(fill='x')

        filename_label = ttk.Label(top_frame, text="Filename:")
        filename_label.grid(row=0, column=0, padx=5, pady=5, sticky='W')

        self.filename_entry = ttk.Entry(top_frame, width=40)
        self.filename_entry.grid(row=0, column=1, padx=5, pady=5, sticky='W')
        # Allow pressing Enter in filename entry to trigger Save
        self.filename_entry.bind('<Return>', lambda event: self._on_save())


        # --- Buttons linked to Controller actions via callbacks ---
        # The 'command' option links a button click to a method (callback)
        create_button = ttk.Button(top_frame, text="Create", command=self._on_create)
        create_button.grid(row=0, column=2, padx=5, pady=5)

        open_button = ttk.Button(top_frame, text="Open", command=self._on_open)
        open_button.grid(row=0, column=3, padx=5, pady=5)

        save_button = ttk.Button(top_frame, text="Save", command=self._on_save)
        save_button.grid(row=0, column=4, padx=5, pady=5)

        delete_button = ttk.Button(top_frame, text="Delete", command=self._on_delete)
        delete_button.grid(row=0, column=5, padx=5, pady=5)

        # --- Main Frame for Text Area and Scrollbar ---
        text_frame = ttk.Frame(self.root, padding="10")
        text_frame.pack(expand=True, fill='both')

        self.text_area = tk.Text(text_frame, wrap='word', undo=True, height=15, width=60)
        self.text_area.pack(side='left', expand=True, fill='both')

        scrollbar = ttk.Scrollbar(text_frame, orient='vertical', command=self.text_area.yview)
        scrollbar.pack(side='right', fill='y')
        self.text_area.config(yscrollcommand=scrollbar.set)

    # --- Callback Methods (Triggered by User Actions) ---

    def _on_create(self):
        """Handles the Create button click."""
        # Prompt user for a filename using a simple dialog

        filename = tkinter.simpledialog.askstring("Create File", "Enter filename (.txt):")

        if not filename: # User cancelled or entered nothing
            return

        # Basic check in View; Controller/Service perform robust validation.
        # Automatically append .txt if the user didn't provide it.
        if not filename.endswith('.txt'):
             filename += '.txt'

        try:
            # Delegate to the Controller
            self.controller.create_file(filename)
            messagebox.showinfo("Success", f"File '{filename}' created successfully.")
            # Update UI: clear editor, set new filename
            self._update_ui_after_load(filename, "") # Treat as loading an empty file
            self._set_modified(False) # New file starts unmodified
        except FileOperationError as e:
            messagebox.showerror("Error", f"Failed to create file:\n{e}")
        except Exception as e: # Catch unexpected errors during UI interaction
             messagebox.showerror("Unexpected Error", f"An unexpected error occurred:\n{e}")


# Inside FileManagerView class in view.py

    def _on_open(self):
        """Handles the Open button click. Uses background thread for loading."""
        if not self._confirm_discard_changes():
            return # User cancelled

        filepath_obj = filedialog.askopenfilename(
            title="Open Text File",
            initialdir=self.PROJECT_ROOT,
            filetypes=[("Text Files", "*.txt"), ("All Files", "*.*")]
        )

        if not filepath_obj: # User cancelled dialog
            return

        filepath = str(filepath_obj) # Ensure it's a string for the thread arg

        # --- Asynchronous Loading ---
        # Reading potentially large files directly in this callback would
        # block the Tkinter main event loop. The GUI would freeze until
        # the file is fully read, creating a poor user experience.
        #
        # Solution:
        # 1. Start a separate 'background' thread to perform the file I/O.
        # 2. The background thread reads the file using the Controller.
        # 3. **Crucially:** Background threads MUST NOT directly modify Tkinter widgets.
        # 4. Use a thread-safe `queue.Queue` for communication:
        #    - Background thread puts the result (file content or error) into the queue.
        # 5. The main GUI thread periodically checks the queue using `root.after()`.
        # 6. When a result is found in the queue, the main thread *safely* updates the GUI widgets.

        # Indicate busy state BEFORE starting thread
        self._set_ui_busy(True)

        # Create and start a background thread
        # 'daemon=True' ensures this thread won't prevent the app from exiting
        # if the main thread finishes.
        thread = threading.Thread(target=self._perform_load_in_background, args=(filepath,), daemon=True)
        thread.start()

        # Start checking the queue for the result shortly (e.g., every 100ms)
        self.root.after(100, self._check_load_queue)

    def _perform_load_in_background(self, filepath: str):
        """
        Task executed in a background thread to read file content.
        Puts the result (or error) into the shared queue.
        IMPORTANT: This method must NOT directly interact with Tkinter widgets.
        """
        try:
            # Derive filename relative to project root for the controller
            relative_path = Path(filepath).relative_to(self.PROJECT_ROOT)
            filename = relative_path.name # Or str(relative_path) if controller expects string

            content = self.controller.read_file(filename)
            # Put success result in queue: (status, data, original_filepath)
            self._load_queue.put(("success", content, filepath))
        except (FileOperationError, ValueError, Exception) as e: # Catch specific and general errors
             # ValueError if relative_to fails (file outside project)
            # Put error result in queue: (status, error_message, original_filepath)
            self._load_queue.put(("error", str(e), filepath))

    def _check_load_queue(self):
        """
        Checks the queue for results from the background load thread.
        Updates the UI if a result is found. Runs in the main Tkinter thread.
        """
        try:
            # Check queue without blocking (get_nowait raises queue.Empty if no item)
            status, payload, filepath = self._load_queue.get_nowait()

            # --- Result received, processing starts ---
            # Re-enable UI elements now that the background task is done
            self._set_ui_busy(False)

            # Process the result from the queue
            if status == "success":
                content = payload
                filename = Path(filepath).name # Get filename part for display
                self._update_ui_after_load(filename, content)
                self._set_modified(False) # File just loaded is unmodified
                # Optionally show success message in a status bar (future enhancement)
            else: # status == "error"
                error_message = payload
                messagebox.showerror("Error Loading File", f"Failed to load '{Path(filepath).name}':\n{error_message}")
                # Optionally clear UI or leave previous content? Clear for now.
                # self._update_ui_after_load(None, "") # Decide on desired behavior

        except queue.Empty:
            # Queue is empty, schedule the next check
            self.root.after(100, self._check_load_queue)
        except Exception as e: # Catch unexpected errors during UI update
             # Ensure UI is re-enabled even if UI update fails
             self._set_ui_busy(False)
             messagebox.showerror("Unexpected Error", f"Error updating UI after load:\n{e}")
             print(f"Error processing load queue item: {e}") # Log for debugging


    def _on_save(self):
        """Handles the Save button click."""
        filename = self.filename_entry.get().strip()
        if not filename:
            # If no filename, maybe trigger Save As? For now, show error.
             messagebox.showerror("Error", "Please enter a filename.")
             # Or call _on_save_as() - requires separate implementation
             return

        # Basic check; Controller/Service perform robust validation.
        # Automatically append .txt if missing and update the entry field.
        if not filename.endswith('.txt'):
            filename += '.txt'
            # Update the UI to reflect the appended extension
            self.filename_entry.delete(0, tk.END)
            self.filename_entry.insert(0, filename)


        # Get content from the text area
        # '1.0' means line 1, character 0. tk.END means the very end of the text.
        # The Text widget often implicitly adds a newline character at the absolute
        # end of its content, so '+ -1c' (plus negative one character) is used
        # to retrieve the content *excluding* that potentially unwanted final newline.
        content = self.text_area.get('1.0', tk.END + '-1c')

        try:
            # Delegate to the Controller
            self.controller.write_file(filename, content)
            messagebox.showinfo("Success", f"File '{filename}' saved successfully.")
            # Update state: current filename and modified status
            self._current_filename = filename
            self._set_modified(False)
        except FileOperationError as e:
            messagebox.showerror("Error Saving File", f"Failed to save '{filename}':\n{e}")
        except Exception as e: # Catch unexpected errors during UI interaction
             messagebox.showerror("Unexpected Error", f"An unexpected error occurred during save:\n{e}")


    def _on_delete(self):
        """Handles the Delete button click."""
        filename = self.filename_entry.get().strip()
        if not filename:
             messagebox.showwarning("Delete File", "Please enter or select a filename to delete.")
             return

        # Ask for confirmation
        if messagebox.askyesno("Confirm Delete", f"Are you sure you want to delete '{filename}'?"):
            try:
                # Delegate to Controller
                self.controller.delete_file(filename)
                messagebox.showinfo("Success", f"File '{filename}' deleted.")
                # Clear the UI if the deleted file was the one loaded
                if filename == self._current_filename:
                    self._update_ui_after_load(None, "") # Clear UI
                    self._set_modified(False)
            except FileOperationError as e:
                messagebox.showerror("Error Deleting File", f"Failed to delete '{filename}':\n{e}")
            except Exception as e: # Catch unexpected errors
                 messagebox.showerror("Unexpected Error", f"An unexpected error occurred during delete:\n{e}")


    # --- UI Update and State Management Helpers ---

    def _update_ui_after_load(self, filename: str | None, content: str):
        """Helper to update filename entry and text area content."""
        # Update filename entry
        self.filename_entry.delete(0, tk.END)
        if filename:
            self.filename_entry.insert(0, filename)

        # Update text area
        self.text_area.delete('1.0', tk.END)
        self.text_area.insert('1.0', content)

        # Update internal state
        self._current_filename = filename
        # Reset undo/redo stack after loading new content
        self.text_area.edit_reset()


    def _on_text_modified(self, event=None):
        """Callback when the text area content is modified."""
        # The Text widget sets its internal modified flag. We check it.
        # This event might fire multiple times; we only care if the state changes to modified.
        if self.text_area.edit_modified() and not self._is_modified:
             self._set_modified(True)


    def _set_modified(self, is_modified: bool):
        """Updates the internal modified state and the window title."""
        self._is_modified = is_modified
        title = "Simple Text File Manager"
        if is_modified:
            # Add asterisk to title to indicate unsaved changes
            self.root.title("*" + title)
        else:
            self.root.title(title)
            # Reset the Text widget's internal flag AFTER we process it
            self.text_area.edit_modified(False)

    def _set_ui_busy(self, busy: bool):
        """Disables/Enables key UI elements during background operations."""
        state = tk.DISABLED if busy else tk.NORMAL
        # Disable/Enable interactive widgets to prevent conflicts during load
        try:
            # Top control buttons (assuming they are stored if needed, or access via parent frame)
            # Let's assume top_frame exists and iterate through its button children
            # This requires storing top_frame as self.top_frame in setup_ui
            # Simpler approach for now: Disable widgets we have direct references to.
            if self.filename_entry:
                self.filename_entry.config(state=state)
            if self.text_area:
                # Make text area read-only instead of disabling completely
                self.text_area.config(state=tk.NORMAL if not busy else tk.DISABLED) # tk.DISABLED makes it unscrollable sometimes, use with care
                # Consider just setting read-only: self.text_area.config(state=tk.NORMAL); self.text_area.config(insertoff=busy) ? No standard way.
                # Disabling is clearest signal, but might impact usability slightly.

            if hasattr(self, 'file_tree'): # Check if tree exists yet
                # We need to disable the tree to prevent selecting while loading
                self.file_tree.config(state=state) # This might not directly work on Treeview, need to investigate
                # Alternative: Unbind select event temporarily? Or disable parent frame?
                # For now, attempt config state, may need refinement.

            # Add other buttons if references are stored (Create, Open, Save, Delete, Rename, Refresh)
            # Example if self.save_button reference was stored in setup_ui:
            # if self.save_button:
            #     self.save_button.config(state=state)

            # Change cursor to indicate busy state
            self.root.config(cursor="watch" if busy else "")

        except tk.TclError as e:
             # Handle potential errors if widgets don't exist or state change fails
             print(f"Warning: Error updating UI busy state: {e}")

    def _confirm_discard_changes(self) -> bool:
        """
        If there are unsaved changes, asks the user to save, discard, or cancel.
        Returns True if it's okay to proceed (saved, discarded, or no changes).
        Returns False if the user cancelled the operation.
        """
        if not self._is_modified:
            return True # No changes, okay to proceed

        response = messagebox.askyesnocancel(
            "Unsaved Changes",
            "You have unsaved changes. Do you want to save them before continuing?"
        )

        if response is True: # Yes -> Save
            self._on_save()
            # Check if save was successful (indicated by _is_modified becoming False)
            return not self._is_modified
        elif response is False: # No -> Discard changes
            return True # Okay to proceed (discarding)
        else: # Cancel (response is None)
            return False # User cancelled the action, do not proceed


    def _on_exit(self):
        """Handles the window close request."""
        if self._confirm_discard_changes():
            self.root.quit() # Close the application


    def run(self):
        """Starts the Tkinter event loop."""
        self.root.mainloop()

# --- Main execution block ---
if __name__ == '__main__':
    main_window = tk.Tk()
    app_view = FileManagerView(main_window)

    # Check if initialization failed in __init__ before running
    if hasattr(app_view, 'controller'):
         app_view.run()
```

## Key Changes in `view.py`

- **Imports:**  
  - Added modules: `pathlib`, `threading`, `queue`, `messagebox`, `filedialog`  
  - Imported classes: `FileController`, `FileOperationError`  

- **`__init__`:**  
  - Defines `PROJECT_ROOT`  
  - Instantiates `FileController`, handling initialization errors  
  - Initializes state variables: `_current_filename`, `_is_modified`, `_load_queue`  
  - Binds the `<<Modified>>` event on the text widget  
  - Handles window close via `WM_DELETE_WINDOW`  

- **`setup_ui`:**  
  - Buttons now call their `_on_…` callbacks (e.g. `command=self._on_create`)  
  - Binds `<Return>` in the filename entry to trigger save  

- **Callback Methods** (`_on_create`, `_on_open`, `_on_save`, `_on_delete`):  
  - Retrieve input from widgets (e.g. `self.filename_entry.get()`, `self.text_area.get()`)  
  - Delegate file operations to `self.controller`  
  - Wrap calls in `try…except FileOperationError` to catch Model/Controller errors  
  - Display results via `messagebox`  
  - Update UI state on success (clear text area, refresh filename entry)  

- **Asynchronous Loading** (`_on_open`, `_perform_load_in_background`, `_check_load_queue`):  
  1. `_on_open` opens a file dialog and starts a background `threading.Thread` for `_perform_load_in_background`.  
  2. `_perform_load_in_background` runs off the main thread, calls `controller.read_file`, and enqueues success or error (never touches Tkinter widgets).  
  3. `_check_load_queue` is scheduled with `root.after()`, polls `self._load_queue` on the main thread, and updates the UI accordingly.  

- **State Management** (`_update_ui_after_load`, `_on_text_modified`, `_set_modified`, `_confirm_discard_changes`, `_on_exit`):  
  - Manage `_current_filename` and `_is_modified` flags  
  - `_set_modified` toggles the window title (adds `*` when unsaved)  
  - `_confirm_discard_changes` prompts to save/discard/cancel on unsaved edits before actions  
  - `_on_exit` invokes `_confirm_discard_changes` before quitting  

---

Now you have a fully functional MVC‑based file manager with **Create**, **Open** (async), **Save**, and **Delete**.  
Run `python view.py` to try it out: create, save, open, edit, and delete `.txt` files in your `FileManagerProject` directory—and watch the UI stay responsive even with large files!

### 6.2 Adding More Features (Directory Browser, Rename, Menus)
Let's enhance the application further.

1. Directory Browser (ttk.Treeview)

We'll add a panel on the left to list the .txt files in the project directory. Clicking a file will load it.

Modify setup_ui in view.py: Add the Treeview.

```Python
# view.py - inside FileManagerView class

    def setup_ui(self):
        """Create and arrange the widgets in the main window."""

        # --- Main Paned Layout (Browser Left, Editor Right) ---
        main_pane = ttk.PanedWindow(self.root, orient=tk.HORIZONTAL)
        main_pane.pack(expand=True, fill='both', padx=10, pady=(0,10))

        # ---- Left Pane: Directory Browser ----
        browser_frame = ttk.Frame(main_pane, padding=(0,0,5,0))
        main_pane.add(browser_frame, weight=1)

        ttk.Label(browser_frame, text="Project Files (.txt)").pack(anchor='w', padx=5, pady=(0,5))

        # This is the attribute smoke test needs:
        self.file_tree = ttk.Treeview(browser_frame, show='tree', selectmode='browse', height=15)
        self.file_tree.pack(expand=True, fill='both', side='top')
        self.file_tree.bind('<<TreeviewSelect>>', self._on_tree_select)

        ttk.Button(browser_frame, text="Refresh", command=self._refresh_file_list).pack(fill='x', pady=(5,0))
        ttk.Button(browser_frame, text="Rename Selected", command=self._on_rename).pack(fill='x', pady=(5,0))

        # ---- Right Pane: Editor ----
        editor_frame = ttk.Frame(main_pane)
        main_pane.add(editor_frame, weight=4)

        # Top controls (filename entry + buttons)
        top_frame = ttk.Frame(editor_frame, padding="5")
        top_frame.pack(fill='x')

        ttk.Label(top_frame, text="Filename:").grid(row=0, column=0, padx=5, pady=5, sticky='W')
        self.filename_entry = ttk.Entry(top_frame, width=40)
        self.filename_entry.grid(row=0, column=1, padx=5, pady=5, sticky='W')
        self.filename_entry.bind('<Return>', lambda e: self._on_save())

        ttk.Button(top_frame, text="Create", command=self._on_create).grid(row=0, column=2, padx=5, pady=5)
        ttk.Button(top_frame, text="Open",   command=self._on_open  ).grid(row=0, column=3, padx=5, pady=5)
        ttk.Button(top_frame, text="Save",   command=self._on_save  ).grid(row=0, column=4, padx=5, pady=5)
        ttk.Button(top_frame, text="Delete", command=self._on_delete).grid(row=0, column=5, padx=5, pady=5)

        # Text area + scrollbar
        text_frame = ttk.Frame(editor_frame, padding="5")
        text_frame.pack(expand=True, fill='both')

        self.text_area = tk.Text(text_frame, wrap='word', undo=True)
        self.text_area.pack(side='left', expand=True, fill='both')
        scrollbar = ttk.Scrollbar(text_frame, orient='vertical', command=self.text_area.yview)
        scrollbar.pack(side='right', fill='y')
        self.text_area.config(yscrollcommand=scrollbar.set)

        # After building UI, populate the file list once
        self._refresh_file_list()

```
Add new methods to FileManagerView:

```Python
# view.py - inside FileManagerView class

    def _refresh_file_list(self):
        """Clears and reloads the file list in the Treeview."""
        # Clear existing items
        for item in self.file_tree.get_children():
            self.file_tree.delete(item)
        # Get list from controller
        try:
            filenames = self.controller.list_files()
            # Populate tree
            for name in filenames:
                # 'iid' is the unique item ID, useful for retrieval
                self.file_tree.insert('', 'end', iid=name, text=name)
        except FileOperationError as e:
            messagebox.showerror("Error Listing Files", f"Could not refresh file list:\n{e}")
        except Exception as e:
             messagebox.showerror("Unexpected Error", f"Error refreshing file list:\n{e}")


# Inside FileManagerView class in view.py

    def _on_tree_select(self, event=None):
        """Handles selection changes in the file Treeview. Loads file asynchronously."""
        selected_items = self.file_tree.selection() # Get tuple of selected item IDs
        if not selected_items:
            return # Nothing selected

        filename = selected_items[0] # Get the first (and only) selected item's ID (which is the filename)

        # Confirm discarding changes before loading new file
        if not self._confirm_discard_changes():
            # If user cancels, prevent the selection change from persisting visually (optional)
            # This requires storing the previously selected item. For simplicity, we omit this,
            # but be aware the visual selection might differ from the loaded file if cancelled.
            try:
                # Attempt to re-select the previously current file if it exists in the tree
                if self._current_filename and self.file_tree.exists(self._current_filename):
                     self.file_tree.selection_set(self._current_filename)
                # else: # If previous doesn't exist or wasn't set, clear selection?
                #     self.file_tree.selection_set(())
            except tk.TclError:
                 pass # Ignore errors if item doesn't exist
            return

        # --- Trigger Asynchronous Loading ---
        # Construct the full path needed by _perform_load_in_background
        full_path = str(self.PROJECT_ROOT / filename)

        # Indicate busy state
        self._set_ui_busy(True)

        # Start background thread (same as _on_open)
        thread = threading.Thread(target=self._perform_load_in_background, args=(full_path,), daemon=True)
        thread.start()

        # Schedule queue check (same as _on_open)
        self.root.after(100, self._check_load_queue)

```

Call _refresh_file_list() in __init__ after setup_ui() is called. (Already added in the snippet above). Also call it after successful Create, Save (if new name), Delete, and Rename operations to keep the list up-to-date.

2. File Renaming

Add a Rename button in setup_ui (e.g., below the file tree or in the top controls). Let's add it below the tree.

```Python
# view.py - inside setup_ui, after refresh_button.pack()

        rename_button = ttk.Button(browser_frame, text="Rename Selected", command=self._on_rename)
        rename_button.pack(fill='x', pady=(5, 0))
```
Add the _on_rename method to FileManagerView:

```Python

# view.py - inside FileManagerView class
# Requires: from tkinter import simpledialog

    def _on_rename(self):
        """Handles renaming the currently selected file."""
        selected_items = self.file_tree.selection()
        if not selected_items:
            messagebox.showwarning("Rename File", "Please select a file from the list to rename.")
            return

        old_filename = selected_items[0]
        # Ensure the filename entry also shows the file to be renamed
        self.filename_entry.delete(0, tk.END)
        self.filename_entry.insert(0, old_filename)


        new_filename = simpledialog.askstring(
            "Rename File",
            f"Enter new name for '{old_filename}':",
            initialvalue=old_filename # Pre-fill dialog with old name
        )

        if not new_filename or new_filename == old_filename:
            return # User cancelled or entered same name

        # Basic check in View
        if not new_filename.endswith('.txt'):
            new_filename += '.txt'

        try:
            # Delegate to controller
            self.controller.rename_file(old_filename, new_filename)
            messagebox.showinfo("Success", f"File renamed to '{new_filename}'")

            # Update UI
            self._refresh_file_list() # Update the tree view
            # Update filename entry and current file if it was the loaded one
            if old_filename == self._current_filename:
                 self._current_filename = new_filename
                 self.filename_entry.delete(0, tk.END)
                 self.filename_entry.insert(0, new_filename)
            # Select the newly renamed file in the tree
            self.file_tree.selection_set(new_filename)


        except FileOperationError as e:
            messagebox.showerror("Error Renaming File", f"Failed to rename file:\n{e}")
            # Refresh list anyway, in case the failed state is reflected
            self._refresh_file_list()
        except Exception as e:
             messagebox.showerror("Unexpected Error", f"Error during rename:\n{e}")
             self._refresh_file_list()
```

3. Menu Bar

Add standard File and Edit menus.

Modify __init__ in view.py: Call _create_menu() and _bind_shortcuts() after setup_ui().

```Python

# view.py - inside FileManagerView.__init__

        # --- Build the UI ---
        self.setup_ui() # Keep this

        # --- Create Menus ---
        self._create_menu() # Add this

        # --- Bind Events & Shortcuts ---
        self.text_area.bind('<<Modified>>', self._on_text_modified)
        self.root.protocol("WM_DELETE_WINDOW", self._on_exit)
        self._bind_shortcuts() # Add this
```
Add _create_menu and _bind_shortcuts methods to FileManagerView:
```Python

# view.py - inside FileManagerView class
# Requires: from tkinter import Menu

    def _create_menu(self):
        """Creates the main menu bar."""
        menubar = Menu(self.root)
        self.root.config(menu=menubar) # Associate menubar with root window

        # --- File Menu ---
        file_menu = Menu(menubar, tearoff=0) # tearoff=0 prevents detaching menu
        menubar.add_cascade(label="File", menu=file_menu) # Add File dropdown to menubar

        # Menu commands
        file_menu.add_command(label="New", accelerator="Ctrl+N", command=self._on_new) # We need _on_new
        file_menu.add_command(label="Open...", accelerator="Ctrl+O", command=self._on_open)
        file_menu.add_command(label="Save", accelerator="Ctrl+S", command=self._on_save)
        # Add Save As later if desired
        file_menu.add_separator()
        file_menu.add_command(label="Rename Selected...", command=self._on_rename) # No common shortcut
        file_menu.add_command(label="Delete Current", accelerator="Ctrl+D", command=self._on_delete)
        file_menu.add_separator()
        file_menu.add_command(label="Exit", accelerator="Ctrl+Q", command=self._on_exit)


        # --- Edit Menu ---
        edit_menu = Menu(menubar, tearoff=0)
        menubar.add_cascade(label="Edit", menu=edit_menu)

        # Use built-in Text widget undo/redo capabilities
        edit_menu.add_command(label="Undo", accelerator="Ctrl+Z", command=self.text_area.edit_undo)
        edit_menu.add_command(label="Redo", accelerator="Ctrl+Y", command=self.text_area.edit_redo)


    def _bind_shortcuts(self):
        """Binds keyboard shortcuts to commands."""
        # File operations
        self.root.bind('<Control-n>', lambda event: self._on_new())
        self.root.bind('<Control-o>', lambda event: self._on_open())
        self.root.bind('<Control-s>', lambda event: self._on_save())
        self.root.bind('<Control-d>', lambda event: self._on_delete()) # If using Ctrl+D
        self.root.bind('<Control-q>', lambda event: self._on_exit())

        # Edit operations (already handled by Text widget if focused, but global bindings help)
        self.root.bind('<Control-z>', lambda event: self.text_area.edit_undo())
        self.root.bind('<Control-y>', lambda event: self.text_area.edit_redo())
        # Note: Bindings like Ctrl+A (Select All), Ctrl+C (Copy), Ctrl+X (Cut), Ctrl+V (Paste)
        # often work by default in the Text widget on Linux/Windows.


    def _on_new(self):
         """Handles File > New command."""
         if not self._confirm_discard_changes():
              return
         # Clear the UI state
         self._update_ui_after_load(None, "")
         self._set_modified(False)
```
Ensure you import Menu from tkinter. Add the _on_new method. Update _on_delete if using Ctrl+D shortcut.
You now have a more feature-rich application with directory browsing, renaming, and standard menus/shortcuts!

## 7. Testing Your Application
Writing automated tests is crucial for professional software development. They help ensure your code works as expected and prevent regressions (breaking existing functionality when adding new features or fixing bugs). We'll use pytest, a popular Python testing framework.

Install pytest:
```Bash

pip install pytest
```

Create Test Files: Create a tests directory in your project root (FileManagerProject/tests/). We'll write separate test files for the Model and Controller. Testing the View directly is more complex (requiring GUI automation tools or intricate mocking); we'll limit View testing to a simple "smoke test" to ensure it starts without crashing.

### 7.1 Unit Tests for the Model (tests/test_file_service.py)
These tests check the FileService logic in isolation, interacting with a temporary file system provided by pytest.

 ```Python

# tests/test_file_service.py
import pytest
from pathlib import Path
import sys

# Add project root to sys.path to allow importing file_service
# This is one way; alternatives include installing your project as a package
PROJECT_ROOT = Path(__file__).parent.parent
sys.path.insert(0, str(PROJECT_ROOT))

# Now import from your project files
from file_service import FileService, FileOperationError

# --- Fixtures ---
# Fixtures are reusable setup functions for tests

@pytest.fixture
def temp_base_dir(tmp_path):
    """Creates a temporary directory for tests using pytest's tmp_path fixture."""
    d = tmp_path / "test_files"
    d.mkdir()
    return d

@pytest.fixture
def service(temp_base_dir):
    """Creates a FileService instance using the temporary directory."""
    return FileService(temp_base_dir)

# --- Test Functions ---

def test_create_read_write_delete_cycle(service, temp_base_dir):
    """Tests the basic CRUD operations in sequence."""
    filename = "test_crud.txt"
    content1 = "Initial content."
    content2 = "Updated content."
    file_path = temp_base_dir / filename

    # 1. Create
    service.create(filename)
    assert file_path.exists()
    assert file_path.read_text(encoding='utf-8') == "" # Should be empty

    # 2. Write (initial)
    service.write(filename, content1)
    assert file_path.read_text(encoding='utf-8') == content1

    # 3. Read
    assert service.read(filename) == content1

    # 4. Write (update)
    service.write(filename, content2)
    assert file_path.read_text(encoding='utf-8') == content2

    # 5. Delete
    service.delete(filename)
    assert not file_path.exists()

def test_create_existing_raises(service):
    """Verify creating a file that already exists raises FileExistsError via FileOperationError."""
    filename = "exists.txt"
    service.create(filename) # Create it once
    with pytest.raises(FileOperationError, match="File already exists"):
        service.create(filename) # Try creating again

def test_read_nonexistent_raises(service):
    """Verify reading a non-existent file raises FileNotFoundError via FileOperationError."""
    with pytest.raises(FileOperationError, match="File not found"):
        service.read("ghost.txt")

def test_delete_nonexistent_raises(service):
    """Verify deleting a non-existent file raises FileNotFoundError via FileOperationError."""
    with pytest.raises(FileOperationError, match="File not found"):
        service.delete("phantom.txt")

def test_invalid_extension_raises(service):
    """Verify operations on files with disallowed extensions raise an error."""
    filename = "image.jpg"
    with pytest.raises(FileOperationError, match="Invalid file type"):
        service.create(filename)
    with pytest.raises(FileOperationError, match="Invalid file type"):
        service.read(filename)
    with pytest.raises(FileOperationError, match="Invalid file type"):
        service.write(filename, "data")
    with pytest.raises(FileOperationError, match="Invalid file type"):
        service.delete(filename)
    with pytest.raises(FileOperationError, match="Invalid file type"):
        service.rename("any.txt", filename) # Also check rename target

def test_directory_traversal_raises(service, temp_base_dir):
    """
    Verify attempts to access files outside the base directory are blocked
    by raising a FileOperationError containing an 'Access denied' message.
    """
    malicious_paths = [
        "../secret.txt",
        "../another_dir/file.txt",
    ]
    # Define a key part of the expected error message
    EXPECTED_ERROR_SUBSTRING = "Access denied" # Be less specific than the full message

    operations_to_test = {
        "create": lambda path: service.create(path),
        "read": lambda path: service.read(path),
        "write": lambda path: service.write(path, "hacked"),
        "delete": lambda path: service.delete(path),
        "rename_source": lambda path: service.rename(path, "new_name.txt"),
        "rename_target": lambda path: service.rename("valid_source.txt", path),
    }

    # Prepare for rename tests
    valid_source_name = "valid_source.txt"
    (temp_base_dir / valid_source_name).touch() # Need a valid source file

    for malicious_path in malicious_paths:
        for op_name, operation in operations_to_test.items():
            print(f"Testing operation '{op_name}' with path: {malicious_path}")
            try:
                operation(malicious_path)
                # If the operation succeeded unexpectedly, fail the test
                pytest.fail(
                    f"Operation '{op_name}' did not raise FileOperationError for path '{malicious_path}'"
                )
            except FileOperationError as e:
                # Check if the exception is the correct type (already guaranteed by except)
                # Assert that the error message contains the key substring
                assert EXPECTED_ERROR_SUBSTRING in str(e), \
                    f"Operation '{op_name}' failed for path '{malicious_path}', " \
                    f"but error message '{e}' did not contain '{EXPECTED_ERROR_SUBSTRING}'"
                print(f"  -> Caught expected error: {e}") # Optional: print caught error
            except Exception as e:
                # Catch any other unexpected exceptions during the operation
                pytest.fail(
                    f"Operation '{op_name}' raised an unexpected exception type for path '{malicious_path}': {type(e).__name__}: {e}"
                )

    # Clean up the dummy file used for rename tests
    (temp_base_dir / valid_source_name).unlink()

    # Note: This test structure doesn't explicitly check the specific message variations
    # (e.g., "Compatibility check", "Different drive") but ensures the core access denial is reported.
    # It also doesn't test the "Operation on base directory" case, which could be a separate test.


def test_list_files(service, temp_base_dir):
    """Tests listing allowed files."""
    # Create some files
    (temp_base_dir / "a.txt").touch()
    (temp_base_dir / "b.txt").touch()
    (temp_base_dir / "c.log").touch() # Disallowed extension
    (temp_base_dir / "subdir").mkdir() # Directory, should be ignored
    (temp_base_dir / "subdir" / "d.txt").touch() # File in subdir, ignored

    expected_files = ["a.txt", "b.txt"]
    actual_files = service.list_files()

    assert sorted(actual_files) == sorted(expected_files)

def test_rename_file(service, temp_base_dir):
    """Tests renaming a file."""
    old_name = "original.txt"
    new_name = "renamed.txt"
    old_path = temp_base_dir / old_name
    new_path = temp_base_dir / new_name

    old_path.write_text("data", encoding='utf-8')

    service.rename(old_name, new_name)

    assert not old_path.exists()
    assert new_path.exists()
    assert new_path.read_text(encoding='utf-8') == "data"

def test_rename_target_exists_raises(service, temp_base_dir):
    """Verify renaming to an existing file name raises an error."""
    file1 = "one.txt"
    file2 = "two.txt"
    (temp_base_dir / file1).touch()
    (temp_base_dir / file2).touch()

    with pytest.raises(FileOperationError, match="Target file.*already exists"):
        service.rename(file1, file2)

def test_rename_invalid_target_extension_raises(service, temp_base_dir):
    """Verify renaming to a disallowed extension raises an error."""
    (temp_base_dir / "source.txt").touch()
    with pytest.raises(FileOperationError, match="Invalid file type"):
        service.rename("source.txt", "target.log")
```
**Note on `sys.path`:** Modifying `sys.path` like this allows the test files inside the `tests/` directory to import modules from the main project directory (like `file_service.py`). While functional for simpler projects like this one, in larger applications, a more standard practice is often to structure the project as an installable package (using a `setup.py` or `pyproject.toml` file) and install it in the virtual environment in "editable" mode (`pip install -e .`). This makes imports work more reliably without manual path manipulation. For this tutorial, the `sys.path` approach is simpler to demonstrate.
## 7.2 Unit Tests for the Controller (tests/test_controller.py)

These tests verify that the FileController correctly calls the appropriate FileService methods and propagates errors. We use mocking to replace the real FileService with a fake one (a "mock object") that records calls made to it. This isolates the Controller logic without needing a real file system. **Why do this? Mocking allows us to test the Controller's interaction logic (Does it call the correct service method? Does it handle errors properly?) in complete isolation, ensuring the test focuses only on the Controller's code. We also use 'monkeypatching' (provided by pytest) to dynamically replace the part of the Controller's code that *creates* the FileService, forcing it to use our mock object instead. This gives us precise control over the dependencies during the test.**

```Python

# tests/test_controller.py
import pytest
from pathlib import Path
from unittest.mock import MagicMock # Pytest uses unittest.mock effectively
import sys

# Add project root to sys.path
PROJECT_ROOT = Path(__file__).parent.parent
sys.path.insert(0, str(PROJECT_ROOT))

# --- Import project files ---
# Import the Controller itself
from controller import FileController
# Import the Model's exception class DIRECTLY from the model file
from file_service import FileOperationError

# --- Fixtures ---

@pytest.fixture
def mock_service():
    """Creates a mock object simulating FileService."""
    # MagicMock automatically creates methods as they are accessed
    # spec=FileService ensures the mock only has methods the real service has (optional but good)
    # from file_service import FileService # import needed if using spec=
    # mock = MagicMock(spec=FileService)
    mock = MagicMock()
    return mock

@pytest.fixture
def controller(tmp_path, mock_service, monkeypatch):
    """
    Creates a FileController instance, patching FileService creation
    to inject the mock_service instead of a real one.
    'monkeypatch' is a pytest fixture for modifying classes/functions during tests.
    """
    # When FileController tries to create FileService(tmp_path)...
    # monkeypatch makes it call `lambda base_dir: mock_service` instead.
    # This returns our pre-made mock object.
    monkeypatch.setattr("controller.FileService", lambda base_dir: mock_service)
    # Now create the controller - it will get the mock service injected
    return FileController(tmp_path) # tmp_path is still needed for Controller init

# --- Test Functions ---

def test_create_file_delegates(controller, mock_service):
    """Verify create_file calls service.create and propagates errors."""
    filename = "new.txt"
    # Test successful delegation
    controller.create_file(filename)
    mock_service.create.assert_called_once_with(filename)

    # Test error propagation
    mock_service.create.side_effect = FileOperationError("Disk full") # Configure mock to raise error
    with pytest.raises(FileOperationError, match="Disk full"):
        controller.create_file(filename)

def test_read_file_delegates(controller, mock_service):
    """Verify read_file calls service.read, returns data, and propagates errors."""
    filename = "existing.txt"
    expected_content = "File data"

    # Test successful delegation and return value
    mock_service.read.return_value = expected_content # Configure mock return
    actual_content = controller.read_file(filename)
    mock_service.read.assert_called_once_with(filename)
    assert actual_content == expected_content

    # Test error propagation
    mock_service.read.side_effect = FileOperationError("Permission denied")
    with pytest.raises(FileOperationError, match="Permission denied"):
        controller.read_file(filename)

def test_write_file_delegates(controller, mock_service):
    """Verify write_file calls service.write and propagates errors."""
    filename = "output.txt"
    content = "Save this"
    # Test successful delegation
    controller.write_file(filename, content)
    mock_service.write.assert_called_once_with(filename, content)

    # Test error propagation
    mock_service.write.side_effect = FileOperationError("Write failed")
    with pytest.raises(FileOperationError, match="Write failed"):
        controller.write_file(filename, content)

def test_delete_file_delegates(controller, mock_service):
    """Verify delete_file calls service.delete and propagates errors."""
    filename = "gone.txt"
    # Test successful delegation
    controller.delete_file(filename)
    mock_service.delete.assert_called_once_with(filename)

    # Test error propagation
    mock_service.delete.side_effect = FileOperationError("File in use")
    with pytest.raises(FileOperationError, match="File in use"):
        controller.delete_file(filename)

def test_list_files_delegates(controller, mock_service):
    """Verify list_files calls service.list_files, returns data, and propagates errors."""
    expected_list = ["a.txt", "b.txt"]

    # Test successful delegation and return value
    mock_service.list_files.return_value = expected_list
    actual_list = controller.list_files()
    mock_service.list_files.assert_called_once()
    assert actual_list == expected_list

    # Test error propagation
    mock_service.list_files.side_effect = FileOperationError("Cannot access directory")
    with pytest.raises(FileOperationError, match="Cannot access directory"):
        controller.list_files()

def test_rename_file_delegates(controller, mock_service):
    """Verify rename_file calls service.rename and propagates errors."""
    old_name = "old.txt"
    new_name = "new_name.txt"
    # Test successful delegation
    controller.rename_file(old_name, new_name)
    mock_service.rename.assert_called_once_with(old_name, new_name)

    # Test error propagation
    mock_service.rename.side_effect = FileOperationError("Rename failed")
    with pytest.raises(FileOperationError, match="Rename failed"):
        controller.rename_file(old_name, new_name)
```

### 7.3 Smoke Test for the View (tests/test_view_smoke.py)
This test just tries to create the main View object to catch any immediate crashing errors during initialization. It doesn't test functionality.

```Python

# tests/test_view_smoke.py
import pytest
import tkinter as tk
import sys
from pathlib import Path

# Add project root to sys.path
PROJECT_ROOT = Path(__file__).parent.parent
sys.path.insert(0, str(PROJECT_ROOT))

# Mock tkinter modules that might cause issues in non-GUI environment if needed
# from unittest.mock import MagicMock
# sys.modules['tkinter.messagebox'] = MagicMock()
# sys.modules['tkinter.filedialog'] = MagicMock()
# sys.modules['tkinter.simpledialog'] = MagicMock()

# Import the View class
from view import FileManagerView

# Fixture to create a dummy base directory for Controller initialization
@pytest.fixture
def dummy_base_dir(tmp_path):
    d = tmp_path / "smoke_test_base"
    d.mkdir()
    # Create dummy files needed by controller/service init perhaps? Not usually.
    return d

# Fixture to provide a Tkinter root window (needed for View init)
@pytest.fixture(scope="function") # 'function' scope ensures fresh window per test
def tk_root():
    # Need a display available. On headless CI, might need xvfb.
    # For local testing, this usually works.
    try:
        root = tk.Tk()
        yield root # Provide the root window to the test
        root.destroy() # Cleanup after test finishes
    except tk.TclError as e:
        pytest.skip(f"Skipping GUI test, Tkinter display error: {e}")


def test_view_initialization(tk_root, dummy_base_dir, monkeypatch):
    """Smoke test: Can FileManagerView be initialized without crashing?"""
    # Prevent mainloop from blocking the test
    monkeypatch.setattr(tk_root, "mainloop", lambda: None)
    # Prevent exit actions during test
    monkeypatch.setattr(tk_root, "quit", lambda: None)

    # Mock controller methods that might be called indirectly during init/setup
    # (e.g., list_files via _refresh_file_list)
    # This avoids needing a fully functional controller/service for this basic test.
    class MockController:
        def list_files(self): return []
        # Add other methods if setup_ui calls them indirectly

    monkeypatch.setattr("view.FileController", lambda base_dir: MockController())

    try:
        # Attempt to initialize the View
        view_instance = FileManagerView(tk_root)
        # Basic assertion: check if key widgets were created (exist as attributes)
        assert isinstance(view_instance.filename_entry, ttk.Entry)
        assert isinstance(view_instance.text_area, tk.Text)
        assert isinstance(view_instance.file_tree, ttk.Treeview)
        # Add more checks for other critical widgets if desired
    except Exception as e:
        pytest.fail(f"FileManagerView initialization failed: {e}")
```
Notes on Smoke Test:
* GUI testing is complex. This smoke test is basic.
* It requires a display environment (like your desktop).
* On headless CI servers, you might need `xvfb` (X Virtual Framebuffer).
* We skip the test if Tkinter fails to initialize (e.g., no display).
* We monkeypatch `mainloop` and `quit` to prevent the test from blocking or exiting unexpectedly.
* We mock the `FileController` used by the View during init to avoid needing the real service/filesystem, focusing only on UI component creation.
* **Crucially, this smoke test only verifies that the `FileManagerView` class can be instantiated without immediate errors and that key widget attributes are created. It does *not* test if the widgets are laid out correctly, if they are visually accurate, or if they respond correctly to user interactions (like button clicks or text input). Verifying actual GUI behavior typically requires more advanced tools or techniques (like dedicated GUI testing frameworks or visual regression testing), which are beyond the scope of this introductory tutorial.**

### 7.4 Running the Tests
Open Terminal: Navigate to your project root (FileManagerProject).
Activate Environment: source venv/bin/activate

Run pytest:
```Bash
pytest -v # -v for verbose output
```
Pytest will automatically discover files named test_*.py in the current directory and subdirectories (tests/) and run the functions named test_*.
You should see output indicating how many tests passed or failed. Green dots or "PASSED" mean success; 'F' or "FAILED" indicates problems, usually with a traceback showing the error location.

## 8. Running Your Application
You've built and tested the application! To run it:

Activate Virtual Environment: If not already active, source venv/bin/activate in your terminal within the FileManagerProject directory.

Run the View Script:
```Bash
python view.py
```
The "Simple Text File Manager" window should appear.
Interact:
Create: Click "Create", enter a filename (e.g., notes.txt), click OK. The file appears in the left panel.
Browse/Open: Click a filename in the left panel. Its content loads in the text area. Or click the "Open" button for a standard file dialog.
Edit: Type in the text area. Notice the * appear in the window title.
Save: Click "Save" (or press Enter in the filename field). The * disappears.
Rename: Select a file in the list, click "Rename Selected", enter a new name.
Delete: Ensure the filename is correct in the entry field (or select from tree), click "Delete", confirm. The file disappears from the list.
Undo/Redo: Use Ctrl+Z / Ctrl+Y while editing text.
Exit: Close the window or use File > Exit. You'll be prompted to save if you have unsaved changes.

## 9. Conclusion and Next Steps
Congratulations! You have successfully built a graphical file manager using Python and Tkinter. You've gone beyond a simple script and implemented:

GUI Development: Using Tkinter widgets, layout managers (pack, grid, PanedWindow), and event handling (command, bind).
MVC Architecture: Separating concerns into Model (file_service.py), View (view.py), and Controller (controller.py) for better organization, testability, and maintainability.

Robust File I/O: Using pathlib, context managers (with), specific error handling (try...except FileOperationError), and security considerations (path validation, extension checks).
Responsiveness: Using background threads (threading, queue) for potentially long-running I/O (_on_open) to prevent the GUI from freezing.

User Experience Features: Undo/redo, unsaved changes tracking (* in title, prompts), directory browsing, renaming, menus, and basic shortcuts.
Automated Testing: Writing unit tests for the Model and Controller using pytest and mocking.
Further Exploration
This project provides a solid foundation. Here are areas you could explore next:

Enhance Robustness:
Add checks for disk space before writing.
Implement more sophisticated error reporting (e.g., a status bar instead of popups for minor info).
Consider file locking if multiple instances could run (more advanced).

More Features:
Save As: Implement a "Save As" function.
Search/Filter: Add a way to search within the file list or file content.
Settings: Save window size/position or the last directory using configparser or JSON.
Syntax Highlighting: Integrate a library for highlighting code within the text area (more complex).
Binary File Handling: Adapt the service/view to support non-text files (requires binary read/write modes and different UI representation).
Improve Testing:
Explore more advanced View testing techniques if needed (though often complex).
Set up Continuous Integration (CI) using platforms like GitHub Actions to run tests automatically on every code push.
Explore Alternatives:
Try building a similar app with other GUI toolkits like PyQt or Kivy to compare.

Investigate Python's asyncio for managing concurrency as an alternative to threading (requires integration with Tkinter's event loop, e.g., using libraries like asyncio-tkinter).
Building software involves continuous learning and refinement. By tackling this project, you've gained practical experience in many core aspects of application development. Keep experimenting and building! Happy coding!


---
# Appendix: Complete Production-Ready Code

Below are the three complete files for the final application. .

---

## 1. file_service.py
```python
from pathlib import Path

class FileOperationError(Exception):
    """Custom exception for file operation errors within our application."""
    pass

class FileService:
    """
    Model layer: Handles all direct interactions with the file system
    for text files within a specified base directory.
    Enforces security boundaries and allowed file types.
    """
    ALLOWED_EXTENSIONS = {'.txt'}

    def __init__(self, base_dir: Path):
        """
        Initializes the service with a base directory.
        All operations are restricted to this directory.
        """
        self.base_dir = base_dir.resolve()
        if not self.base_dir.is_dir():
            raise FileOperationError(f"Base directory not found: {self.base_dir}")

    def _validate_path(self, filename: str) -> Path:
        """
        Validates the filename and resolves it to a secure path within base_dir.
        Raises FileOperationError if validation fails (e.g., empty name, invalid
        extension, path traversal attempt, operation on base directory itself).
        """
        if not filename:
            raise FileOperationError("Filename cannot be empty.")

        # Clean whitespace. Allow '/' for now, let resolve() and is_relative_to() handle structure.
        cleaned_filename = filename.strip()
        if not cleaned_filename: # Check again after stripping
             raise FileOperationError("Filename cannot be empty.")

        candidate_path = self.base_dir / cleaned_filename

        # Resolve non-strictly first for security checks before existence checks
        try:
            resolved_path_non_strict = candidate_path.resolve(strict=False)
        except Exception as e:
             # Catch resolution errors early (e.g., filename too long, illegal OS chars)
             # These often indicate invalid filenames regardless of location.
             raise FileOperationError(f"Invalid filename or path format: {e}")

        # --- Security Checks ---
        ACCESS_DENIED_MSG = "Access denied: Path is outside the allowed directory."

        # Check 1: Ensure the resolved path structure is *within* the base directory.
        # This is the primary defense against directory traversal (e.g., '../').
        try:
            # Use resolved_path_non_strict for the check
            if not resolved_path_non_strict.is_relative_to(self.base_dir):
                raise FileOperationError(ACCESS_DENIED_MSG)
        except ValueError: # is_relative_to raises ValueError if paths are on different drives
            # Append details to the standard message for better debugging in tests/logs
            raise FileOperationError(ACCESS_DENIED_MSG + " (Different drive or location)")
        except AttributeError:
            # Fallback for Python < 3.9 - check common ancestor
            # Use resolved_path_non_strict for the check
            if self.base_dir != resolved_path_non_strict and self.base_dir not in resolved_path_non_strict.parents:
                 raise FileOperationError(ACCESS_DENIED_MSG + " (Compatibility check)")

        # Check 2: Prevent direct operation on the base directory itself.
        if resolved_path_non_strict == self.base_dir:
             raise FileOperationError("Operation on the base directory itself is not allowed.")

        # Check 3: Ensure the file extension is allowed (case-insensitive).
        # Check suffix based on the resolved path structure.
        if resolved_path_non_strict.suffix.lower() not in self.ALLOWED_EXTENSIONS:
            raise FileOperationError(f"Invalid file type: Extension '{resolved_path_non_strict.suffix}' is not allowed. Only {self.ALLOWED_EXTENSIONS} are permitted.")

        # Return the structurally validated path.
        # Calling methods (e.g., read, write, delete) should handle FileNotFoundError
        # if they require the file to actually exist at this path. They can use
        # `path.exists()` or `path.resolve(strict=True)` internally if needed.
        return resolved_path_non_strict

    def create(self, filename: str) -> None:
        """Creates a new, empty text file. Fails if the file already exists."""
        path = self._validate_path(filename)
        try:
            with open(path, 'x', encoding='utf-8'):
                pass
        except FileExistsError:
            raise FileOperationError(f"File already exists: {filename}")
        except OSError as e:
            raise FileOperationError(f"Unable to create file '{filename}': {e}")

    def read(self, filename: str) -> str:
        """Reads the content of a text file."""
        path = self._validate_path(filename)
        try:
            if not path.is_file():
                raise FileNotFoundError()
            return path.read_text(encoding='utf-8')
        except FileNotFoundError:
            raise FileOperationError(f"File not found: {filename}")
        except OSError as e:
            raise FileOperationError(f"Unable to read file '{filename}': {e}")

    def write(self, filename: str, content: str) -> None:
        """Writes (or overwrites) content to a text file."""
        path = self._validate_path(filename)
        try:
            if path.exists() and not path.is_file():
                raise FileOperationError(f"Cannot write: '{filename}' is not a regular file.")
            path.write_text(content, encoding='utf-8')
        except OSError as e:
            raise FileOperationError(f"Unable to write file '{filename}': {e}")

    def delete(self, filename: str) -> None:
        """Deletes a text file."""
        path = self._validate_path(filename)
        try:
            if not path.is_file():
                raise FileNotFoundError()
            path.unlink()
        except FileNotFoundError:
            raise FileOperationError(f"File not found: {filename}")
        except OSError as e:
            raise FileOperationError(f"Unable to delete file '{filename}': {e}")

    def list_files(self) -> list[str]:
        """Returns a sorted list of allowed filenames in the base directory."""
        try:
            files = [
                p.name for p in self.base_dir.iterdir()
                if p.is_file() and p.suffix.lower() in self.ALLOWED_EXTENSIONS
            ]
            return sorted(files)
        except OSError as e:
            raise FileOperationError(f"Unable to list files in directory '{self.base_dir}': {e}")

    def rename(self, old_filename: str, new_filename: str) -> None:
        """Renames a file, performing necessary validations."""
        # Validate the NEW name first to catch invalid extensions
        new_path = self._validate_path(new_filename)

        # Now validate the old filename exists
        old_path = self._validate_path(old_filename)
        if not old_path.is_file():
            raise FileOperationError(f"File not found: {old_filename}")

        if new_path.exists():
            raise FileOperationError(f"Cannot rename: Target file '{new_filename}' already exists.")

        try:
            old_path.rename(new_path)
        except OSError as e:
            raise FileOperationError(
                f"Unable to rename file '{old_filename}' to '{new_filename}': {e}"
            )
```

---

## 2. controller.py
```python
from pathlib import Path
from file_service import FileService, FileOperationError

class FileController:
    """
    Controller layer: Mediates between the View and the Model.
    Translates user actions into file operations and returns results/errors.
    """
    def __init__(self, base_dir: Path):
        try:
            self.service = FileService(base_dir)
        except FileOperationError:
            raise

    def create_file(self, filename: str):
        try:
            self.service.create(filename)
        except FileOperationError:
            raise

    def read_file(self, filename: str) -> str:
        try:
            return self.service.read(filename)
        except FileOperationError:
            raise

    def write_file(self, filename: str, content: str):
        try:
            self.service.write(filename, content)
        except FileOperationError:
            raise

    def delete_file(self, filename: str):
        try:
            self.service.delete(filename)
        except FileOperationError:
            raise

    def list_files(self) -> list[str]:
        try:
            return self.service.list_files()
        except FileOperationError:
            raise

    def rename_file(self, old_filename: str, new_filename: str):
        try:
            self.service.rename(old_filename, new_filename)
        except FileOperationError:
            raise
```

---

## 3. view.py
```python
import tkinter as tk
from tkinter import ttk, messagebox, filedialog, simpledialog, Menu
from pathlib import Path
import threading
import queue

from controller import FileController, FileOperationError

class FileManagerView:
    """
    GUI layer: Presents widgets and forwards user actions to the Controller.
    """
    def __init__(self, root):
        self.root = root
        self.root.title("Simple Text File Manager")
        self.root.geometry("800x600")

        self.PROJECT_ROOT = Path(__file__).parent.resolve()

        try:
            self.controller = FileController(self.PROJECT_ROOT)
        except FileOperationError as e:
            messagebox.showerror(
                "Initialization Error",
                f"Failed to initialize File Controller:\n{e}\nApplication will exit."
            )
            self.root.quit()
            return

        self.filename_entry = None
        self.text_area = None
        self._current_filename = None
        self._is_modified = False
        self._load_queue = queue.Queue()

        self.setup_ui()
        self._create_menu()
        self.text_area.bind('<<Modified>>', self._on_text_modified)
        self.root.protocol("WM_DELETE_WINDOW", self._on_exit)
        self._bind_shortcuts()

    def setup_ui(self):
        main_pane = ttk.PanedWindow(self.root, orient=tk.HORIZONTAL)
        main_pane.pack(expand=True, fill='both', padx=10, pady=10)

        # Directory browser
        browser_frame = ttk.Frame(main_pane, padding=(0,0,5,0))
        main_pane.add(browser_frame, weight=1)

        ttk.Label(browser_frame, text="Project Files (.txt)").pack(
            anchor='w', padx=5, pady=(0,5)
        )
        self.file_tree = ttk.Treeview(
            browser_frame, show='tree', selectmode='browse', height=20
        )
        self.file_tree.pack(expand=True, fill='both')
        self.file_tree.bind('<<TreeviewSelect>>', self._on_tree_select)

        ttk.Button(
            browser_frame, text="Refresh", command=self._refresh_file_list
        ).pack(fill='x', pady=(5,0))

        # Editor pane
        editor_frame = ttk.Frame(main_pane)
        main_pane.add(editor_frame, weight=4)

        # Top controls
        top_frame = ttk.Frame(editor_frame, padding=5)
        top_frame.pack(fill='x')

        ttk.Label(top_frame, text="Filename:").grid(
            row=0, column=0, padx=5, pady=5, sticky='W'
        )
        self.filename_entry = ttk.Entry(top_frame, width=50)
        self.filename_entry.grid(row=0, column=1, padx=5, pady=5, sticky='W')
        self.filename_entry.bind('<Return>', lambda e: self._on_save())

        ttk.Button(
            top_frame, text="Create", command=self._on_create
        ).grid(row=0, column=2, padx=5)
        ttk.Button(
            top_frame, text="Open", command=self._on_open
        ).grid(row=0, column=3, padx=5)
        ttk.Button(
            top_frame, text="Save", command=self._on_save
        ).grid(row=0, column=4, padx=5)
        ttk.Button(
            top_frame, text="Delete", command=self._on_delete
        ).grid(row=0, column=5, padx=5)

        # Text area
        text_frame = ttk.Frame(editor_frame, padding=5)
        text_frame.pack(expand=True, fill='both')

        self.text_area = tk.Text(
            text_frame, wrap='word', undo=True
        )
        self.text_area.pack(side='left', expand=True, fill='both')
        scrollbar = ttk.Scrollbar(
            text_frame, orient='vertical', command=self.text_area.yview
        )
        scrollbar.pack(side='right', fill='y')
        self.text_area.config(yscrollcommand=scrollbar.set)

        self._refresh_file_list()

    def _create_menu(self):
        menubar = Menu(self.root)
        self.root.config(menu=menubar)

        file_menu = Menu(menubar, tearoff=0)
        menubar.add_cascade(label="File", menu=file_menu)
        file_menu.add_command(label="New", accelerator="Ctrl+N", command=self._on_new)
        file_menu.add_command(label="Open...", accelerator="Ctrl+O", command=self._on_open)
        file_menu.add_command(label="Save", accelerator="Ctrl+S", command=self._on_save)
        file_menu.add_separator()
        file_menu.add_command(label="Rename Selected...", command=self._on_rename)
        file_menu.add_command(label="Delete Current", accelerator="Ctrl+D", command=self._on_delete)
        file_menu.add_separator()
        file_menu.add_command(label="Exit", accelerator="Ctrl+Q", command=self._on_exit)

        edit_menu = Menu(menubar, tearoff=0)
        menubar.add_cascade(label="Edit", menu=edit_menu)
        edit_menu.add_command(label="Undo", accelerator="Ctrl+Z", command=self.text_area.edit_undo)
        edit_menu.add_command(label="Redo", accelerator="Ctrl+Y", command=self.text_area.edit_redo)

    def _bind_shortcuts(self):
        self.root.bind('<Control-n>', lambda e: self._on_new())
        self.root.bind('<Control-o>', lambda e: self._on_open())
        self.root.bind('<Control-s>', lambda e: self._on_save())
        self.root.bind('<Control-d>', lambda e: self._on_delete())
        self.root.bind('<Control-q>', lambda e: self._on_exit())
        self.root.bind('<Control-z>', lambda e: self.text_area.edit_undo())
        self.root.bind('<Control-y>', lambda e: self.text_area.edit_redo())

    def _on_new(self):
        if not self._confirm_discard_changes():
            return
        self._update_ui_after_load(None, "")
        self._set_modified(False)

    def _on_create(self):
        filename = simpledialog.askstring("Create File", "Enter filename (.txt):")
        if not filename:
            return
        if not filename.endswith('.txt'):
            filename += '.txt'
        try:
            self.controller.create_file(filename)
            messagebox.showinfo("Success", f"File '{filename}' created successfully.")
            self._update_ui_after_load(filename, "")
            self._set_modified(False)
        except FileOperationError as e:
            messagebox.showerror("Error", f"Failed to create file:\n{e}")

    def _on_open(self):
        if not self._confirm_discard_changes(): return
        filepath = filedialog.askopenfilename(
            title="Open Text File",
            initialdir=self.PROJECT_ROOT,
            filetypes=[("Text Files", "*.txt"), ("All Files", "*.*")]
        )
        if not filepath: return
        threading.Thread(
            target=self._perform_load_in_background,
            args=(filepath,), daemon=True
        ).start()
        self.root.after(100, self._check_load_queue)

    def _perform_load_in_background(self, filepath: str):
        try:
            relative = Path(filepath).relative_to(self.PROJECT_ROOT)
            content = self.controller.read_file(relative.name)
            self._load_queue.put(("success", content, filepath))
        except Exception as e:
            self._load_queue.put(("error", str(e), filepath))

    def _check_load_queue(self):
        """
        Checks the queue for results from the background load thread.
        Updates the UI if a result is found. Runs in the main Tkinter thread.
        """
        try:
            # Check queue without blocking (get_nowait raises queue.Empty if no item)
            status, payload, filepath = self._load_queue.get_nowait()

            # --- Result received, processing starts ---
            # Re-enable UI elements now that the background task is done
            self._set_ui_busy(False)

            # Process the result from the queue
            if status == "success":
                content = payload
                filename = Path(filepath).name # Get filename part for display
                self._update_ui_after_load(filename, content)
                self._set_modified(False) # File just loaded is unmodified
                # Optionally show success message in a status bar (future enhancement)
            else: # status == "error"
                error_message = payload
                messagebox.showerror("Error Loading File", f"Failed to load '{Path(filepath).name}':\n{error_message}")
                # Optionally clear UI or leave previous content? Clear for now.
                # self._update_ui_after_load(None, "") # Decide on desired behavior

        except queue.Empty:
            # Queue is empty, schedule the next check
            self.root.after(100, self._check_load_queue)
        except Exception as e: # Catch unexpected errors during UI update
             # Ensure UI is re-enabled even if UI update fails
             self._set_ui_busy(False)
             messagebox.showerror("Unexpected Error", f"Error updating UI after load:\n{e}")
             print(f"Error processing load queue item: {e}") # Log for debugging

    def _on_save(self):
        filename = self.filename_entry.get().strip()
        if not filename:
            messagebox.showerror("Error", "Please enter a filename.")
            return
        if not filename.endswith('.txt'):
            filename += '.txt'
            self.filename_entry.delete(0, tk.END)
            self.filename_entry.insert(0, filename)
        content = self.text_area.get('1.0', tk.END+'-1c')
        try:
            self.controller.write_file(filename, content)
            messagebox.showinfo("Success", f"File '{filename}' saved successfully.")
            self._current_filename = filename
            self._set_modified(False)
        except FileOperationError as e:
            messagebox.showerror("Error Saving File", f"Failed to save '{filename}':\n{e}")

    def _on_delete(self):
        filename = self.filename_entry.get().strip()
        if not filename:
            messagebox.showwarning("Delete File", "Please enter or select a filename to delete.")
            return
        if messagebox.askyesno("Confirm Delete", f"Are you sure you want to delete '{filename}'?"):
            try:
                self.controller.delete_file(filename)
                messagebox.showinfo("Success", f"File '{filename}' deleted.")
                if filename == self._current_filename:
                    self._update_ui_after_load(None, "")
                    self._set_modified(False)
            except FileOperationError as e:
                messagebox.showerror("Error Deleting File", f"Failed to delete '{filename}':\n{e}")

    def _refresh_file_list(self):
        for item in self.file_tree.get_children():
            self.file_tree.delete(item)
        try:
            for name in self.controller.list_files():
                self.file_tree.insert('', 'end', iid=name, text=name)
        except FileOperationError as e:
            messagebox.showerror("Error Listing Files", f"Could not refresh file list:\n{e}")

    def _on_tree_select(self, event=None):
        """Handles selection changes in the file Treeview. Loads file asynchronously."""
        selected_items = self.file_tree.selection() # Get tuple of selected item IDs
        if not selected_items:
            return # Nothing selected

        filename = selected_items[0] # Get the first (and only) selected item's ID (which is the filename)

        # Confirm discarding changes before loading new file
        if not self._confirm_discard_changes():
            # If user cancels, prevent the selection change from persisting visually (optional)
            # This requires storing the previously selected item. For simplicity, we omit this,
            # but be aware the visual selection might differ from the loaded file if cancelled.
            try:
                # Attempt to re-select the previously current file if it exists in the tree
                if self._current_filename and self.file_tree.exists(self._current_filename):
                     self.file_tree.selection_set(self._current_filename)
                # else: # If previous doesn't exist or wasn't set, clear selection?
                #     self.file_tree.selection_set(())
            except tk.TclError:
                 pass # Ignore errors if item doesn't exist
            return

        # --- Trigger Asynchronous Loading ---
        # Construct the full path needed by _perform_load_in_background
        full_path = str(self.PROJECT_ROOT / filename)

        # Indicate busy state
        self._set_ui_busy(True)

        # Start background thread (same as _on_open)
        thread = threading.Thread(target=self._perform_load_in_background, args=(full_path,), daemon=True)
        thread.start()

        # Schedule queue check (same as _on_open)
        self.root.after(100, self._check_load_queue)

    def _on_rename(self):
        sel = self.file_tree.selection()
        if not sel:
            messagebox.showwarning("Rename File", "Please select a file to rename.")
            return
        old = sel[0]
        new = simpledialog.askstring("Rename File", f"Enter new name for '{old}':", initialvalue=old)
        if not new or new == old: return
        if not new.endswith('.txt'): new += '.txt'
        try:
            self.controller.rename_file(old, new)
            messagebox.showinfo("Success", f"File renamed to '{new}'")
            self._refresh_file_list()
            if old == self._current_filename:
                self._current_filename = new
                self.filename_entry.delete(0, tk.END)
                self.filename_entry.insert(0, new)
            self.file_tree.selection_set(new)
        except FileOperationError as e:
            messagebox.showerror("Error Renaming File", f"Failed to rename file:\n{e}")

    def _update_ui_after_load(self, filename, content):
        self.filename_entry.delete(0, tk.END)
        if filename: self.filename_entry.insert(0, filename)
        self.text_area.delete('1.0', tk.END)
        self.text_area.insert('1.0', content)
        self._current_filename = filename
        self.text_area.edit_reset()

    def _on_text_modified(self, event=None):
        if self.text_area.edit_modified() and not self._is_modified:
            self._set_modified(True)

    def _set_modified(self, state: bool):
        self._is_modified = state
        title = "Simple Text File Manager"
        self.root.title(('*'+title) if state else title)
        if not state:
            self.text_area.edit_modified(False)

    def _set_ui_busy(self, busy: bool):
        """Disables/Enables key UI elements during background operations."""
        state = tk.DISABLED if busy else tk.NORMAL
        # Disable/Enable interactive widgets to prevent conflicts during load
        try:
            # Top control buttons (assuming they are stored if needed, or access via parent frame)
            # Let's assume top_frame exists and iterate through its button children
            # This requires storing top_frame as self.top_frame in setup_ui
            # Simpler approach for now: Disable widgets we have direct references to.
            if self.filename_entry:
                self.filename_entry.config(state=state)
            if self.text_area:
                # Make text area read-only instead of disabling completely
                self.text_area.config(state=tk.NORMAL if not busy else tk.DISABLED) # tk.DISABLED makes it unscrollable sometimes, use with care
                # Consider just setting read-only: self.text_area.config(state=tk.NORMAL); self.text_area.config(insertoff=busy) ? No standard way.
                # Disabling is clearest signal, but might impact usability slightly.

            if hasattr(self, 'file_tree'): # Check if tree exists yet
                # We need to disable the tree to prevent selecting while loading
                self.file_tree.config(state=state) # This might not directly work on Treeview, need to investigate
                # Alternative: Unbind select event temporarily? Or disable parent frame?
                # For now, attempt config state, may need refinement.

            # Add other buttons if references are stored (Create, Open, Save, Delete, Rename, Refresh)
            # Example if self.save_button reference was stored in setup_ui:
            # if self.save_button:
            #     self.save_button.config(state=state)

            # Change cursor to indicate busy state
            self.root.config(cursor="watch" if busy else "")

        except tk.TclError as e:
             # Handle potential errors if widgets don't exist or state change fails
             print(f"Warning: Error updating UI busy state: {e}")

    def _confirm_discard_changes(self) -> bool:
        if not self._is_modified:
            return True
        resp = messagebox.askyesnocancel(
            "Unsaved Changes",
            "You have unsaved changes. Save before continuing?"
        )
        if resp is True:
            self._on_save()
            return not self._is_modified
        return resp is False

    def _on_exit(self):
        if self._confirm_discard_changes():
            self.root.quit()

    def run(self):
        self.root.mainloop()

if __name__ == '__main__':
    root = tk.Tk()
    app = FileManagerView(root)
    if hasattr(app, 'controller'):
        app.run()
```







