import turtle
import time
import random

# --- Game Setup ---
WIDTH, HEIGHT = 600, 600
CELL_SIZE = 20
DELAY = 0.1 # Game speed control
score, high_score = 0, 0

# Set up the screen
screen = turtle.Screen()
screen.setup(width=WIDTH, height=HEIGHT)
screen.bgcolor("#add8e6") # Light blue background
screen.title("Snake Game")
screen.tracer(0) # Turns off automatic screen updates

# --- Snake Head ---
head = turtle.Turtle()
head.shape("square")
head.color("white")
head.penup()
head.goto(0, 0)
head.direction = "stop"

# --- Food ---
food = turtle.Turtle()
food.shape("circle")
food.color("red")
food.penup()
food.goto(0, 100) # Initial food position

# --- Scoreboard ---
scoreboard = turtle.Turtle()
scoreboard.speed(0)
scoreboard.shape("square")
scoreboard.color("black")
scoreboard.penup()
scoreboard.hideturtle()
scoreboard.goto(0, HEIGHT/2 - 40)
scoreboard.write(f"Score: {score}  High Score: {high_score}", align="center", font=("Arial", 24, "normal"))

# --- Functions ---
def go_up():
    if head.direction != "down":
        head.direction = "up"
def go_down():
    if head.direction != "up":
        head.direction = "down"
def go_left():
    if head.direction != "right":
        head.direction = "left"
def go_right():
    if head.direction != "left":
        head.direction = "right"

def move():
    if head.direction == "up":
        y = head.ycor()
        head.sety(y + CELL_SIZE)
    if head.direction == "down":
        y = head.ycor()
        head.sety(y - CELL_SIZE)
    if head.direction == "left":
        x = head.xcor()
        head.setx(x - CELL_SIZE)
    if head.direction == "right":
        x = head.xcor()
        head.setx(x + CELL_SIZE)

# --- Keyboard Bindings ---
screen.listen()
screen.onkeypress(go_up, "Up")
screen.onkeypress(go_down, "Down")
screen.onkeypress(go_left, "Left")
screen.onkeypress(go_right, "Right")
screen.onkeypress(go_up, "w")
screen.onkeypress(go_down, "s")
screen.onkeypress(go_left, "a")
screen.onkeypress(go_right, "d")

# --- Main Game Loop ---
while True:
    screen.update()

    # Check for collision with border
    if head.xcor() > WIDTH/2 - 10 or head.xcor() < -WIDTH/2 + 10 or head.ycor() > HEIGHT/2 - 10 or head.ycor() < -HEIGHT/2 + 10:
        time.sleep(1)
        head.goto(0, 0)
        head.direction = "stop"

        # Hide the segments
        for segment in segments:
            segment.goto(1000, 1000) # Move off-screen
        segments.clear()

        # Reset score
        score = 0
        scoreboard.clear()
        scoreboard.write(f"Score: {score}  High Score: {high_score}", align="center", font=("Arial", 24, "normal"))

    # Check for collision with food
    if head.distance(food) < CELL_SIZE:
        # Move the food to a new random location
        x = random.randint(-WIDTH/2 + CELL_SIZE, WIDTH/2 - CELL_SIZE)
        y = random.randint(-HEIGHT/2 + CELL_SIZE, HEIGHT/2 - CELL_SIZE)
        food.goto(x, y)

        # Add a new segment to the snake body
        new_segment = turtle.Turtle()
        new_segment.speed(0)
        new_segment.shape("square")
        new_segment.color("grey")
        new_segment.penup()
        segments.append(new_segment)

        # Increase the score
        score += 10
        if score > high_score:
            high_score = score
        scoreboard.clear()
        scoreboard.write(f"Score: {score}  High Score: {high_score}", align="center", font=("Arial", 24, "normal"))

    # Move the end segments first in reverse order
    for index in range(len(segments)-1, 0, -1):
        x = segments[index-1].xcor()
        y = segments[index-1].ycor()
        segments[index].goto(x, y)

    # Move segment 0 to where the head is
    if len(segments) > 0:
        x = head.xcor()
        y = head.ycor()
        segments[0].goto(x, y)

    move()

    # Check for head collision with body segments
    for segment in segments:
        if segment.distance(head) < CELL_SIZE:
            time.sleep(1)
            head.goto(0, 0)
            head.direction = "stop"
            for segment in segments:
                segment.goto(1000, 1000)
            segments.clear()
            score = 0
            scoreboard.clear()
            scoreboard.write(f"Score: {score}  High Score: {high_score}", align="center", font=("Arial", 24, "normal"))

    time.sleep(DELAY)

screen.mainloop()
