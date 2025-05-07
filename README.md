# ESCAPE-ROOM

import pygame
import sys
import random

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
SCREEN = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Escape Room Game")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GRAY = (200, 200, 200)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)

# Fonts
FONT = pygame.font.Font(None, 36)
SMALL_FONT = pygame.font.Font(None, 24)

# Game Variables
current_room = 1
inventory = []
attempts = 0
max_attempts = 5
room1_timer = 600  # 10 minutes in seconds
room1_timer_running = False
room1_quiz_completed = False
room1_code = None
room2_quiz_completed = False
room2_timer = 720  # 12 minutes in seconds for Room 2
room2_timer_running = False
room3_quiz_completed = False
room3_timer = 1080  # 18 minutes in seconds for Room 3
room3_timer_running = False
game_over_message = None

# Quiz Data for Room 1 (6 Questions)
room1_quizzes = [
    {
        "question": "Which data structure follows the LIFO (Last-In, First-Out) principle?",
        "options": ["Queue", "Stack", "Linked List", "Tree"],
        "correct_answer": "Stack"
    },
    {
        "question": "What is the time complexity of searching for an element in a balanced binary search tree?",
        "options": ["O(n)", "O(1)", "O(log n)", "O(n^2)"],
        "correct_answer": "O(log n)"
    },
    {
        "question": "Which sorting algorithm has the best average-case time complexity?",
        "options": ["Bubble Sort", "Insertion Sort", "Merge Sort", "Selection Sort"],
        "correct_answer": "Merge Sort"
    },
    {
        "question": "What is a graph data structure?",
        "options": ["A linear collection of data elements", "A hierarchical data structure", "A collection of nodes with edges connecting them", "A data structure that stores key-value pairs"],
        "correct_answer": "A collection of nodes with edges connecting them"
    },
    {
        "question": "Which data structure is best suited for implementing a priority queue?",
        "options": ["Array", "Linked List", "Heap", "Stack"],
        "correct_answer": "Heap"
    },
    {
        "question": "What is the purpose of a hash function?",
        "options": ["To encrypt data", "To compress data", "To map keys to indices in a hash table", "To sort data"],
        "correct_answer": "To map keys to indices in a hash table"
    }
]
random.shuffle(room1_quizzes)
current_quiz_index = 0

# Quiz Data for Room 2 (Java - 12 Questions)
room2_quizzes = [
    {
        "question": "Which keyword is used to define a constant in Java?",
        "options": ["const", "final", "static", "immutable"],
        "correct_answer": "final"
    },
    {
        "question": "What is the purpose of the 'super' keyword in Java?",
        "options": ["To call a method in the current class", "To call a method in the parent class", "To create a new object", "To define a static variable"],
        "correct_answer": "To call a method in the parent class"
    },
    {
        "question": "Which of the following is NOT a primitive data type in Java?",
        "options": ["int", "boolean", "String", "double"],
        "correct_answer": "String"
    },
    {
        "question": "What is the purpose of the 'try-catch' block in Java?",
        "options": ["To define a loop", "To handle exceptions", "To declare a variable", "To define a class"],
        "correct_answer": "To handle exceptions"
    },
    {
        "question": "What is the difference between '==' and '.equals()' in Java?",
        "options": ["They are the same", "'==' compares object references, '.equals()' compares object content", "'==' compares object content, '.equals()' compares object references", "'.equals()' is only used for strings"],
        "correct_answer": "'==' compares object references, '.equals()' compares object content"
    },
    {
        "question": "Which of the following is an example of polymorphism in Java?",
        "options": ["Inheritance", "Method Overloading", "Encapsulation", "Abstraction"],
        "correct_answer": "Method Overloading"
    },
    {
        "question": "What is the purpose of the 'static' keyword in Java?",
        "options": ["To create a constant", "To create an object", "To make a variable accessible to all instances of a class", "To define a method that cannot be overridden"],
        "correct_answer": "To make a variable accessible to all instances of a class"
    },
    {
        "question": "What is the difference between an interface and an abstract class in Java?",
        "options": ["They are the same", "An interface can have method implementations, an abstract class cannot", "An abstract class can have method implementations, an interface cannot", "Interfaces can only have constant variables"],
        "correct_answer": "An abstract class can have method implementations, an interface cannot"
    },
    {
        "question": "What is the purpose of the 'finally' block in a try-catch block?",
        "options": ["It is executed only if an exception is caught", "It is executed only if no exception is caught", "It is always executed, regardless of whether an exception is caught or not", "It is used to define a new exception"],
        "correct_answer": "It is always executed, regardless of whether an exception is caught or not"
    },
    {
        "question": "What is the purpose of garbage collection in Java?",
        "options": ["To optimize code", "To prevent memory leaks", "To encrypt data", "To manage threads"],
        "correct_answer": "To prevent memory leaks"
    },
    {
        "question": "What is the difference between ArrayList and LinkedList in Java?",
        "options": ["They are the same", "ArrayList is faster for insertions and deletions, LinkedList is faster for accessing elements", "ArrayList is faster for accessing elements, LinkedList is faster for insertions and deletions", "LinkedList can only store strings"],
        "correct_answer": "ArrayList is faster for accessing elements, LinkedList is faster for accessing elements, LinkedList is faster for insertions and deletions"
    },
    {
        "question": "What is the purpose of the 'volatile' keyword in Java?",
        "options": ["To create a constant", "To make a variable thread-safe", "To optimize code", "To define a method that cannot be overridden"],
        "correct_answer": "To make a variable thread-safe"
    }
]
random.shuffle(room2_quizzes)
room2_current_quiz_index = 0

# Quiz Data for Room 3 (Database Systems & MySQL - 20 Questions)
room3_quizzes = [
    {
        "question": "What does ACID stand for in database transactions?",
        "options": ["Atomicity, Consistency, Isolation, Durability", "Accuracy, Consistency, Integrity, Durability", "Atomicity, Correctness, Isolation, Dependence", "Accuracy, Correctness, Integrity, Dependence"],
        "correct_answer": "Atomicity, Consistency, Isolation, Durability"
    },
    {
        "question": "What is the purpose of normalization in database design?",
        "options": ["To increase data redundancy", "To reduce data redundancy and improve data integrity", "To speed up query execution", "To encrypt data"],
        "correct_answer": "To reduce data redundancy and improve data integrity"
    },
    {
        "question": "What is a primary key?",
        "options": ["A foreign key", "A key that uniquely identifies each record in a table", "A key used for encryption", "A key used for sorting data"],
        "correct_answer": "A key that uniquely identifies each record in a table"
    },
    {
        "question": "What is a foreign key?",
        "options": ["A primary key", "A key that references a primary key in another table", "A key used for encryption", "A key used for sorting data"],
        "correct_answer": "A key that references a primary key in another table"
    },
    {
        "question": "What is the purpose of an index in a database?",
        "options": ["To store data", "To speed up data retrieval", "To encrypt data", "To enforce data integrity"],
        "correct_answer": "To speed up data retrieval"
    },
    {
        "question": "What is a database transaction?",
        "options": ["A single SQL statement", "A sequence of operations treated as a single logical unit of work", "A backup of the database", "A report generated from the database"],
        "correct_answer": "A sequence of operations treated as a single logical unit of work"
    },
    {
        "question": "Which SQL command is used to retrieve data from a database?",
        "options": ["INSERT", "UPDATE", "DELETE", "SELECT"],
        "correct_answer": "SELECT"
    },
    {
        "question": "Which SQL command is used to insert new data into a database?",
        "options": ["INSERT", "UPDATE", "DELETE", "SELECT"],
        "correct_answer": "INSERT"
    },
    {
        "question": "Which SQL command is used to modify existing data in a database?",
        "options": ["INSERT", "UPDATE", "DELETE", "SELECT"],
        "correct_answer": "UPDATE"
    },
    {
        "question": "Which SQL command is used to remove data from a database?",
        "options": ["INSERT", "UPDATE", "DELETE", "SELECT"],
        "correct_answer": "DELETE"
    },
    {
        "question": "In MySQL, which command is used to create a new database?",
        "options": ["CREATE DATABASE", "NEW DATABASE", "MAKE DATABASE", "BUILD DATABASE"],
        "correct_answer": "CREATE DATABASE"
    },
    {
        "question": "In MySQL, which command is used to create a new table?",
        "options": ["CREATE TABLE", "NEW TABLE", "MAKE TABLE", "BUILD TABLE"],
        "correct_answer": "CREATE TABLE"
    },
    {
        "question": "In MySQL, which data type is used to store whole numbers?",
        "options": ["VARCHAR", "TEXT", "INT", "DATE"],
        "correct_answer": "INT"
    },
    {
        "question": "In MySQL, which data type is used to store text strings?",
        "options": ["VARCHAR", "TEXT", "INT", "DATE"],
        "correct_answer": "VARCHAR"
    },
    {
        "question": "In MySQL, what is the purpose of the 'AUTO_INCREMENT' attribute?",
        "options": ["To automatically back up the database", "To automatically encrypt data", "To automatically generate a unique numeric value for a column", "To automatically sort data"],
        "correct_answer": "To automatically generate a unique numeric value for a column"
    },
    {
        "question": "In MySQL, which command is used to add a new column to an existing table?",
        "options": ["ADD COLUMN", "INSERT COLUMN", "ALTER TABLE ADD COLUMN", "NEW COLUMN"],
        "correct_answer": "ALTER TABLE ADD COLUMN"
    },
    {
        "question": "In MySQL, which command is used to remove a column from an existing table?",
        "options": ["DELETE COLUMN", "REMOVE COLUMN", "ALTER TABLE DROP COLUMN", "DROP COLUMN"],
        "correct_answer": "ALTER TABLE DROP COLUMN"
    },
    {
        "question": "In MySQL, which command is used to modify the data type of a column?",
        "options": ["CHANGE TYPE", "MODIFY TYPE", "ALTER TABLE MODIFY COLUMN", "UPDATE TYPE"],
        "correct_answer": "ALTER TABLE MODIFY COLUMN"
    },
    {
        "question": "In MySQL, what is the purpose of the 'JOIN' clause?",
        "options": ["To combine rows from two or more tables based on a related column", "To sort data", "To filter data", "To encrypt data"],
        "correct_answer": "To combine rows from two or more tables based on a related column"
    },
    {
        "question": "In MySQL, which command is used to delete a table?",
        "options": ["DELETE TABLE", "REMOVE TABLE", "DROP TABLE", "ERASE TABLE"],
        "correct_answer": "DROP TABLE"
    }
]
random.shuffle(room3_quizzes)
room3_current_quiz_index = 0

# Input Box
input_box = pygame.Rect(100, 500, 600, 30)
input_text = ''
active = False

# Quiz Buttons
quiz_buttons = []
quiz_active = False

# Timer Text
timer_text = "Time Remaining: 10:00"

# Room Description
room_description = ""

# Add maze walls for a Pac-Man-like theme
maze_walls = [
    pygame.Rect(50, 50, 700, 10),  # Top horizontal wall
    pygame.Rect(50, 540, 700, 10),  # Bottom horizontal wall
    pygame.Rect(50, 50, 10, 500),  # Left vertical wall
    pygame.Rect(740, 50, 10, 500),  # Right vertical wall
    pygame.Rect(200, 200, 400, 10),  # Middle horizontal wall
    pygame.Rect(200, 300, 10, 100),  # Vertical wall in the middle
    pygame.Rect(590, 300, 10, 100)   # Vertical wall in the middle-right
]

def draw_text(text, font, color, surface, x, y):
    textobj = font.render(text, 1, color)
    textrect = textobj.get_rect()
    textrect.topleft = (x, y)
    surface.blit(textobj, textrect)

def get_room_description():
    global room1_code
    if current_room == 1:
        if not room1_quiz_completed:
            return "You are in a dimly lit room. There's a table and a locked door.  You see a note on the table.  You must answer the quizzes to unlock the door."
        else:
            return f"You are in a dimly lit room. The door is locked with a code. The code is: {room1_code}. Enter the code to unlock the door."
    elif current_room == 2:
        if not room2_quiz_completed:
            return "You are in a library. Shelves are filled with books. You see a strange symbol on one of the books.  There's also a computer terminal.  Perhaps you need to answer some questions to proceed. You have 12 minutes."
        else:
            return "You are in a library. Shelves are filled with books. The computer terminal is unlocked.  Enter the code to proceed."
    elif current_room == 3:
        if not room3_quiz_completed:
            return "You are in a laboratory. Beakers and test tubes are everywhere. There's a keypad on the exit door. It seems to require knowledge of database systems. You have 18 minutes."
        else:
            return "You are in a laboratory. Beakers and test tubes are everywhere. The keypad is unlocked. Enter the code to escape!"
    else:
        return "Congratulations! You escaped!"

def process_command(command):
    global current_room, attempts, room1_quiz_completed, room1_code, room2_quiz_completed, room3_quiz_completed, game_over_message
    global room2_timer_running, room3_timer_running, room1_timer_running, room_description

    command = command.lower()

    if current_room == 1:
        if not room1_timer_running:
            start_room1_timer()

        if not room1_quiz_completed:
            if "read note" in command or "start quiz" in command:
                start_quiz(room=1)
            else:
                room_description = "You should read the note or start the quiz."
        else:
            if f"enter code {room1_code}" in command:
                current_room = 2
                stop_room1_timer()
                start_room2_timer()
                room_description = get_room_description()  # Update room description
            else:
                room_description = "Incorrect code."

    elif current_room == 2:
        if not room2_quiz_completed:
            if "examine symbol" in command:
                room_description = "The symbol looks like a Java code snippet.  It seems you need to answer some Java questions."
            elif "start quiz" in command:
                start_quiz(room=2)
            else:
                room_description = "You should examine the symbol or start the quiz."
        else:
            if "h2o" in command:
                current_room = 3
                stop_room2_timer()
                start_room3_timer()
                room_description = get_room_description()  # Update room description
            else:
                room_description = "I don't understand that command."

    elif current_room == 3:
        if not room3_quiz_completed:
            if "start quiz" in command:
                start_quiz(room=3)
            else:
                room_description = "You need to start the quiz to unlock the keypad."
        else:
            if "enter code 1234" in command:
                stop_room3_timer()
                game_over("Congratulations! You Escaped!")
            else:
                attempts += 1
                if attempts >= max_attempts:
                    game_over("You ran out of attempts. You are trapped!")
                else:
                    room_description = f"Incorrect code. Attempts remaining: {max_attempts - attempts}"
    else:
        room_description = "Congratulations! You escaped!"

def start_quiz(room):
    global quiz_active, quiz_buttons
    quiz_active = True
    quiz_buttons = []
    show_quiz_question(room)

def show_quiz_question(room):
    global room1_quizzes, current_quiz_index, room2_quizzes, room2_current_quiz_index, room3_quizzes, room3_current_quiz_index, quiz_buttons, room_description

    quiz_buttons = []

    if room == 1:
        quiz = room1_quizzes[current_quiz_index]
    elif room == 2:
        quiz = room2_quizzes[room2_current_quiz_index]
    elif room == 3:
        quiz = room3_quizzes[room3_current_quiz_index]
    else:
        print("Invalid room number for quiz.")
        return

    question = quiz["question"]
    options = quiz["options"]

    # Create buttons for each option
    button_width = 200
    button_height = 50
    start_x = (WIDTH - (len(options) * button_width + (len(options) - 1) * 20)) // 2  # Distribute buttons evenly
    start_y = 400

    for i, option in enumerate(options):
        x = start_x + i * (button_width + 20)
        y = start_y
        button_rect = pygame.Rect(x, y, button_width, button_height)
        quiz_buttons.append((button_rect, option, room))  # Store the room number with the button

    room_description = question  # Set the question as the room description

def check_answer(answer, room):
    global room1_quizzes, current_quiz_index, room1_quiz_completed, room1_timer
    global room2_quizzes, room2_current_quiz_index, room2_quiz_completed, room2_timer
    global room3_quizzes, room3_current_quiz_index, room3_quiz_completed, room3_timer
    global quiz_active, room_description

    if room == 1:
        quiz = room1_quizzes[current_quiz_index]
        correct_answer = quiz["correct_answer"]

        if answer == correct_answer:
            if current_quiz_index < len(room1_quizzes) - 1:
                current_quiz_index += 1
                show_quiz_question(room)
            else:
                complete_room1_quiz()
                quiz_active = False
                room_description = get_room_description()  # Update room description
        else:
            room1_timer -= 60  # Subtract 1 minute (60 seconds)
            if room1_timer < 0:
                room1_timer = 0
            update_timer()
            room_description = "Incorrect answer.  1 minute has been deducted."

    elif room == 2:
        quiz = room2_quizzes[room2_current_quiz_index]
        correct_answer = quiz["correct_answer"]

        if answer == correct_answer:
            if room2_current_quiz_index < len(room2_quizzes) - 1:
                room2_current_quiz_index += 1
                show_quiz_question(room)
            else:
                complete_room2_quiz()
                quiz_active = False
                room_description = get_room_description()  # Update room description
        else:
            room2_timer -= 60  # Subtract 1 minute (60 seconds)
            if room2_timer < 0:
                room2_timer = 0
                game_over("Time's up in the library! You are trapped!")
            update_timer(room=2)
            room_description = "Incorrect answer.  1 minute has been deducted."

    elif room == 3:
        quiz = room3_quizzes[room3_current_quiz_index]
        correct_answer = quiz["correct_answer"]

        if answer == correct_answer:
            if room3_current_quiz_index < len(room3_quizzes) - 1:
                room3_current_quiz_index += 1
                show_quiz_question(room)
            else:
                complete_room3_quiz()
                quiz_active = False
                room_description = get_room_description()  # Update room description
        else:
            room3_timer -= 60  # Subtract 1 minute (60 seconds)
            if room3_timer < 0:
                room3_timer = 0
                game_over("Time's up in the laboratory! You are trapped!")
            update_timer(room=3)
            room_description = "Incorrect answer.  1 minute has been deducted."

    else:
        print("Invalid room number for answer check.")

def complete_room1_quiz():
    global room1_quiz_completed, room1_code, current_quiz_index, room_description
    room1_quiz_completed = True

    # Generate a random 4-digit code
    room1_code = str(random.randint(1000, 9999))
    current_quiz_index = 0
    room_description = get_room_description()  # Update room description

def complete_room2_quiz():
    global room2_quiz_completed, room2_current_quiz_index, room_description
    room2_quiz_completed = True
    room2_current_quiz_index = 0
    room_description = get_room_description()  # Update room description

def complete_room3_quiz():
    global room3_quiz_completed, room3_current_quiz_index, room_description
    room3_quiz_completed = True
    room3_current_quiz_index = 0
    room_description = get_room_description()  # Update room description

def start_room1_timer():
    global room1_timer_running
    room1_timer_running = True
    pygame.time.set_timer(pygame.USEREVENT + 1, 1000)  # Start timer event
    update_timer()

def stop_room1_timer():
    global room1_timer_running
    room1_timer_running = False
    pygame.time.set_timer(pygame.USEREVENT + 1, 0)  # Stop timer event

def start_room2_timer():
    global room2_timer_running
    room2_timer_running = True
    pygame.time.set_timer(pygame.USEREVENT + 2, 1000)  # Start timer event
    update_timer(room=2)

def stop_room2_timer():
    global room2_timer_running
    room2_timer_running = False
    pygame.time.set_timer(pygame.USEREVENT + 2, 0)  # Stop timer event

def start_room3_timer():
    global room3_timer_running
    room3_timer_running = True
    pygame.time.set_timer(pygame.USEREVENT + 3, 1000)  # Start timer event
    update_timer(room=3)

def stop_room3_timer():
    global room3_timer_running
    room3_timer_running = False
    pygame.time.set_timer(pygame.USEREVENT + 3, 0)  # Stop timer event

def update_timer(room=1):
    global room1_timer, room1_timer_running, room2_timer, room2_timer_running, room3_timer, room3_timer_running, game_over_message, timer_text

    if room == 1:
        if room1_timer_running:
            minutes = room1_timer // 60
            seconds = room1_timer % 60
            timer_text = f"Time Remaining: {minutes:02}:{seconds:02}"
            room1_timer -= 1
            if room1_timer < 0:
                game_over("Time's up! You are trapped in Room 1!")
            return
    elif room == 2:
        if room2_timer_running:
            minutes = room2_timer // 60
            seconds = room2_timer % 60
            timer_text = f"Time Remaining: {minutes:02}:{seconds:02}"
            room2_timer -= 1
            if room2_timer < 0:
                game_over("Time's up in the library! You are trapped!")
            return
    elif room == 3:
        if room3_timer_running:
            minutes = room3_timer // 60
            seconds = room3_timer % 60
            timer_text = f"Time Remaining: {minutes:02}:{seconds:02}"
            room3_timer -= 1
            if room3_timer < 0:
                game_over("Time's up in the laboratory! You are trapped!")
            return

def game_over(message):
    global game_over_message, active, room1_timer_running, room2_timer_running, room3_timer_running, room_description
    game_over_message = message
    active = False
    stop_room1_timer()
    stop_room2_timer()
    stop_room3_timer()
    room_description = message  # Set the game over message as the room description

# Initial Room Description
room_description = get_room_description()

# Game Loop
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.MOUSEBUTTONDOWN:
            if not quiz_active:
                if input_box.collidepoint(event.pos):
                    active = not active
                else:
                    active = False
            else:
                for button_rect, option, room in quiz_buttons:
                    if button_rect.collidepoint(event.pos):
                        check_answer(option, room)
                        break
        if event.type == pygame.KEYDOWN:
            if active:
                if event.key == pygame.K_RETURN:
                    process_command(input_text)
                    input_text = ''
                elif event.key == pygame.K_BACKSPACE:
                    input_text = input_text[:-1]
                else:
                    input_text += event.unicode
        if event.type == pygame.USEREVENT + 1 and room1_timer_running:
            update_timer(room=1)
        if event.type == pygame.USEREVENT + 2 and room2_timer_running:
            update_timer(room=2)
        if event.type == pygame.USEREVENT + 3 and room3_timer_running:
            update_timer(room=3)

    # Drawing
    SCREEN.fill(BLACK)  # Set background to black

    # Draw maze walls
    for wall in maze_walls:
        pygame.draw.rect(SCREEN, BLUE, wall)

    # Room Description
    draw_text(room_description, FONT, WHITE, SCREEN, 50, 10)

    # Timer
    draw_text(timer_text, SMALL_FONT, WHITE, SCREEN, 600, 10)

    if game_over_message:
        draw_text("Game Over!", FONT, RED, SCREEN, WIDTH // 2 - 100, HEIGHT // 2 - 50)
        draw_text(game_over_message, FONT, RED, SCREEN, WIDTH // 2 - 300, HEIGHT // 2 + 10)
    elif quiz_active:
        # Draw Quiz Elements
        pygame.draw.rect(SCREEN, GRAY, (50, 150, WIDTH - 100, 200))  # Quiz Area Background
        draw_text(room_description, FONT, WHITE, SCREEN, 75, 175)  # Question Text

        for button_rect, option, room in quiz_buttons:
            pygame.draw.rect(SCREEN, GRAY, button_rect)
            draw_text(option, SMALL_FONT, BLACK, SCREEN, button_rect.x + 10, button_rect.y + 10)
    else:
        # Input Box
        pygame.draw.rect(SCREEN, WHITE, input_box, 2)
        text_surface = FONT.render(input_text, True, WHITE)
        SCREEN.blit(text_surface, (input_box.x + 5, input_box.y + 5))
        input_box.w = max(200, text_surface.get_width() + 10)

    pygame.display.flip()

pygame.quit()
sys.exit()



