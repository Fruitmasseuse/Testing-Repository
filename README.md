import curses
import random

# Set up the window
screen = curses.initscr()
curses.curs_set(0)
height, width = screen.getmaxyx()
window = curses.newwin(height, width, 0, 0)
window.keypad(1)
window.timeout(100)

# Initial snake position and food position
snake = [[height // 2, width // 4], [height // 2, width // 4 - 1], [height // 2, width // 4 - 2]]
food = [height // 2, width // 2]
window.addch(food[0], food[1], curses.ACS_PI)

# Initial direction (right)
key = curses.KEY_RIGHT
direction = key

while True:
    # Get the next key input
    next_key = window.getch()
    key = key if next_key == -1 else next_key

    # Update the snake's direction
    if key in [curses.KEY_RIGHT, curses.KEY_LEFT, curses.KEY_UP, curses.KEY_DOWN]:
        direction = key

    # Calculate new head position
    head = [snake[0][0], snake[0][1]]
    if direction == curses.KEY_RIGHT:
        head[1] += 1
    if direction == curses.KEY_LEFT:
        head[1] -= 1
    if direction == curses.KEY_UP:
        head[0] -= 1
    if direction == curses.KEY_DOWN:
        head[0] += 1

    # Insert new head and check for collision with boundaries
    snake.insert(0, head)
    if (
        head[0] in [0, height]
        or head[1] in [0, width]
        or head in snake[1:]
    ):
        curses.endwin()
        quit()

    # Check if snake has eaten the food
    if head == food:
        food = None
        while food is None:
            new_food = [
                random.randint(1, height - 1),
                random.randint(1, width - 1),
            ]
            food = new_food if new_food not in snake else None
        window.addch(food[0], food[1], curses.ACS_PI)
    else:
        tail = snake.pop()
        window.addch(tail[0], tail[1], ' ')

    # Draw the snake
    window.addch(snake[0][0], snake[0][1], curses.ACS_CKBOARD)
