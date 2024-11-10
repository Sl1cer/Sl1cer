import pygame
import random

# Initialize the game
pygame.init()

# Game window dimensions
SCREEN_WIDTH = 400
SCREEN_HEIGHT = 600

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

# Game variables
GRAVITY = 0.5
JUMP = -10
PIPE_SPEED = 5
PIPE_WIDTH = 50
PIPE_GAP = 150
BIRD_WIDTH = 40
BIRD_HEIGHT = 30

# Create window
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption('Flappy Bird')

# Load bird image
bird_img = pygame.Surface((BIRD_WIDTH, BIRD_HEIGHT))
bird_img.fill(RED)

# Clock object to control frame rate
clock = pygame.time.Clock()

# Function to create pipes
def create_pipe():
    pipe_height = random.randint(150, 400)
    pipe_top = pygame.Rect(SCREEN_WIDTH, 0, PIPE_WIDTH, pipe_height)
    pipe_bottom = pygame.Rect(SCREEN_WIDTH, pipe_height + PIPE_GAP, PIPE_WIDTH, SCREEN_HEIGHT - pipe_height - PIPE_GAP)
    return pipe_top, pipe_bottom

# Main game loop
def main():
    bird = pygame.Rect(100, SCREEN_HEIGHT//2, BIRD_WIDTH, BIRD_HEIGHT)
    bird_velocity = 0

    pipes = []
    score = 0
    pipe_timer = 0

    running = True
    while running:
        # Fill background
        screen.fill(WHITE)

        # Event handling
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    bird_velocity = JUMP

        # Bird physics
        bird_velocity += GRAVITY
        bird.y += bird_velocity

        # Pipe creation
        pipe_timer += 1
        if pipe_timer > 90:
            pipes.append(create_pipe())
            pipe_timer = 0

        # Move pipes
        for pipe_pair in pipes:
            pipe_pair[0].x -= PIPE_SPEED
            pipe_pair[1].x -= PIPE_SPEED

        # Remove pipes off-screen
        pipes = [p for p in pipes if p[0].x + PIPE_WIDTH > 0]

        # Check for collisions
        for pipe_pair in pipes:
            if bird.colliderect(pipe_pair[0]) or bird.colliderect(pipe_pair[1]):
                running = False  # Game Over
            if pipe_pair[0].x + PIPE_WIDTH == bird.x:
                score += 1  # Score when passing a pipe

        # Check if bird goes out of screen bounds
        if bird.y > SCREEN_HEIGHT or bird.y < 0:
            running = False  # Game Over

        # Draw bird
        screen.blit(bird_img, (bird.x, bird.y))

        # Draw pipes
        for pipe_pair in pipes:
            pygame.draw.rect(screen, GREEN, pipe_pair[0])
            pygame.draw.rect(screen, GREEN, pipe_pair[1])

        # Draw score
        font = pygame.font.SysFont(None, 55)
        score_text = font.render(f"Score: {score}", True, BLACK)
        screen.blit(score_text, (10, 10))

        # Update display
        pygame.display.update()

        # Control frame rate
        clock.tick(30)

    pygame.quit()

# Run the game
if __name__ == "__main__":
    main()
