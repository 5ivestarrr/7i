import pygame
import time
import random

pygame.init()

# Inställningar för fönstret
width = 800
height = 600
display = pygame.display.set_mode((width, height))
pygame.display.set_caption("Snake Game")

# Färger
white = (255, 255, 255)
black = (0, 0, 0)
red = (255, 0, 0)
green = (0, 255, 0)

# Snake-variabler
snake_block = 10
snake_speed = 15

# Funktion för att rita ormen
def our_snake(snake_block, snake_list):
    for x in snake_list:
        pygame.draw.rect(display, green, [x[0], x[1], snake_block, snake_block])

# Funktion för att visa text på skärmen
def our_text(msg, color, size):
    font = pygame.font.SysFont(None, size)
    screen_text = font.render(msg, True, color)
    display.blit(screen_text, [width / 6, height / 2])

# Funktion för att köra spelet
def game_loop():
    game_over = False
    game_close = False

    # Initial position för ormen
    lead_x = width / 2
    lead_y = height / 2

    lead_x_change = 0
    lead_y_change = 0

    # Lista för att hålla reda på ormens position
    snake_list = []
    length_of_snake = 1

    # Position för maten
    foodx = round(random.randrange(0, width - snake_block) / 10.0) * 10.0
    foody = round(random.randrange(0, height - snake_block) / 10.0) * 10.0

    while not game_over:

        while game_close:
            display.fill(white)
            our_text("Du förlorade! Tryck på C för att spela igen eller Q för att avsluta", red, 30)
            our_snake(snake_block, snake_list)
            pygame.display.update()

            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:
                        game_over = True
                        game_close = False
                    if event.key == pygame.K_c:
                        game_loop()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    lead_x_change = -snake_block
                    lead_y_change = 0
                elif event.key == pygame.K_RIGHT:
                    lead_x_change = snake_block
                    lead_y_change = 0
                elif event.key == pygame.K_UP:
                    lead_y_change = -snake_block
                    lead_x_change = 0
                elif event.key == pygame.K_DOWN:
                    lead_y_change = snake_block
                    lead_x_change = 0

        # Kontrollera om ormen kolliderar med skärmens kanter
        if lead_x >= width or lead_x < 0 or lead_y >= height or lead_y < 0:
            game_close = True

        lead_x += lead_x_change
        lead_y += lead_y_change

        # Rita ormen och maten
        display.fill(white)
        pygame.draw.rect(display, red, [foodx, foody, snake_block, snake_block])
        snake_head = []
        snake_head.append(lead_x)
        snake_head.append(lead_y)
        snake_list.append(snake_head)

        if len(snake_list) > length_of_snake:
            del snake_list[0]

        for segment in snake_list[:-1]:
            if segment == snake_head:
                game_close = True

        our_snake(snake_block, snake_list)

        pygame.display.update()

        # Kontrollera om ormen äter maten
        if lead_x == foodx and lead_y == foody:
            foodx = round(random.randrange(0, width - snake_block) / 10.0) * 10.0
            foody = round(random.randrange(0, height - snake_block) / 10.0) * 10.0
            length_of_snake += 1

        # Sätt hastigheten på ormen
        pygame.time.Clock().tick(snake_speed)

    pygame.quit()
    quit()

game_loop()
