# snake-by-lopei
this is snake by lopei

(CODE)
import pygame
import random

# Initialize Pygame
pygame.init()

# Colors
WHITE = (255, 255, 255)
RED = (213, 50, 80)
GREEN = (0, 255, 0)
BLUE = (50, 153, 213)

# Set screen width and height
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600

# Set window mode
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption('Snake Game')

clock = pygame.time.Clock()

snake_block = 20
snake_speed = 15
level = 1

font_style = pygame.font.SysFont(None, 50)
made_by_font = pygame.font.SysFont(None, 60)  # Increased font size for "Made by Lopei"

def our_snake(snake_list):
    for x, y in snake_list:
        pygame.draw.rect(screen, GREEN, [x, y, snake_block, snake_block])

def message(msg, color):
    mesg = font_style.render(msg, True, color)
    screen.blit(mesg, [SCREEN_WIDTH / 6, SCREEN_HEIGHT / 3])

def is_collision(x1, y1, x2, y2):
    return x1 == x2 and y1 == y2

def spawn_food():
    foodx = random.randrange(0, SCREEN_WIDTH - snake_block, snake_block)
    foody = random.randrange(0, SCREEN_HEIGHT - snake_block, snake_block)
    return foodx, foody

def increase_speed(level):
    return 10 + (level * 5)

def game_loop():
    global snake_speed, level
    game_over = False
    game_close = False
    paused = False

    x1 = SCREEN_WIDTH / 2
    y1 = SCREEN_HEIGHT / 2

    x1_change = 0
    y1_change = 0

    snake_list = []
    length_of_snake = 1

    foodx, foody = spawn_food()

    score = 0

    while not game_over:

        while game_close:
            screen.fill(BLUE)
            message("You lost, press Q to quit and C to replay.", RED)
            pygame.display.update()

            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:
                        game_over = True
                        game_close = False
                    elif event.key == pygame.K_c:
                        game_loop()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    x1_change = -snake_block
                    y1_change = 0
                elif event.key == pygame.K_RIGHT:
                    x1_change = snake_block
                    y1_change = 0
                elif event.key == pygame.K_UP:
                    y1_change = -snake_block
                    x1_change = 0
                elif event.key == pygame.K_DOWN:
                    y1_change = snake_block
                    x1_change = 0
                elif event.key == pygame.K_TAB:
                    paused = not paused

        if paused:
            pygame.time.wait(100)  # Pause for smoother experience
            continue

        x1 += x1_change
        y1 += y1_change

        if x1 >= SCREEN_WIDTH or x1 < 0 or y1 >= SCREEN_HEIGHT or y1 < 0:
            game_close = True

        screen.fill(BLUE)
        pygame.draw.rect(screen, RED, [foodx, foody, snake_block, snake_block])

        snake_head = [x1, y1]
        snake_list.append(snake_head)

        if len(snake_list) > length_of_snake:
            del snake_list[0]

        for segment in snake_list[:-1]:
            if is_collision(x1, y1, segment[0], segment[1]):
                game_close = True

        our_snake(snake_list)

        # Display score and level
        score_font = pygame.font.SysFont(None, 40)
        score_text = score_font.render("Score: " + str(score), True, WHITE)
        screen.blit(score_text, [10, 10])
        level_text = score_font.render("Level: " + str(level), True, WHITE)
        screen.blit(level_text, [10, 40])

        # Display "Made by Lopei" text
        made_by_text = made_by_font.render("Made by Lopei", True, WHITE)
        screen.blit(made_by_text, [10, SCREEN_HEIGHT - 60])  # Adjusted y position for larger text

        pygame.display.update()

        if is_collision(x1, y1, foodx, foody):
            foodx, foody = spawn_food()
            length_of_snake += 1
            score += 10
            if score >= 100:
                level += 1
                snake_speed = increase_speed(level)
                score = 0

        clock.tick(snake_speed)

    pygame.quit()
    quit()

game_loop()
