import pygame
import random

pygame.init()

WIDTH, HEIGHT = 500, 500
canvas = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Nyan Cat Game")

clock = pygame.time.Clock()

# Colors
bg_color = (100, 120, 150)
ground_color = (80, 80, 80)

# Cat
nyancat = pygame.image.load("nyancat.png")
nyancat = pygame.transform.scale(nyancat, (80, 80))

cat_x = WIDTH // 2 - 40
cat_y = HEIGHT // 2

velocity = 0
gravity = 0.8
jump_strength = -12

# World
world_speed = 5

# Obstacles
obstacles = []

def spawn_obstacle():
    x = WIDTH + 100
    y = HEIGHT - 100
    w = 40
    h = 60
    return pygame.Rect(x, y, w, h)

# Game loop
running = True
game_over = False

while running:
    clock.tick(60)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and not game_over:
                velocity = jump_strength

            if event.key == pygame.K_r and game_over:
                # restart
                cat_y = HEIGHT // 2
                velocity = 0
                obstacles.clear()
                game_over = False

    if not game_over:
        # Gravity
        velocity += gravity
        cat_y += velocity

        # Floor collision
        if cat_y > HEIGHT - 100:
            cat_y = HEIGHT - 100
            velocity = 0

        # Spawn obstacles
        if random.randint(1, 60) == 1:
            obstacles.append(spawn_obstacle())

        # Move obstacles
        for obs in obstacles:
            obs.x -= world_speed

        # Remove off-screen obstacles
        obstacles = [obs for obs in obstacles if obs.x > -50]

        # Collision
        cat_rect = pygame.Rect(cat_x, cat_y, 80, 80)
        for obs in obstacles:
            if cat_rect.colliderect(obs):
                game_over = True

    # Draw
    canvas.fill(bg_color)

    # Ground
    pygame.draw.rect(canvas, ground_color, (0, HEIGHT - 50, WIDTH, 50))

    # Obstacles
    for obs in obstacles:
        pygame.draw.rect(canvas, (200, 50, 50), obs)

    # Cat
    canvas.blit(nyancat, (cat_x, cat_y))

    # Game over text
    if game_over:
        font = pygame.font.SysFont(None, 40)
        text = font.render("Game Over! Press R", True, (255, 255, 255))
        canvas.blit(text, (100, 200))

    pygame.display.update()

pygame.quit()
