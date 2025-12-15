# Game-Smaug
игра кликер, основанная на фильме хоббит. Кликая отнимаем хп дракону и в конце получаем приз
import pygame
import sys
pygame.init()
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Dragon Clicker")
clock = pygame.time.Clock()
WHITE = (255, 255, 255)
RED = (200, 50, 50)
GREEN = (50, 200, 50)
BLACK = (0, 0, 0)
font = pygame.font.SysFont("arial", 24)
dragons = [
    pygame.image.load("assets/dragon1.png").convert_alpha(),
    pygame.image.load("assets/dragon2.png").convert_alpha(),
    pygame.image.load("assets/dragon3.png").convert_alpha()

dragons = [pygame.transform.scale(d, (200, 200)) for d in dragons]

sword_img = pygame.image.load("assets/sword.png").convert_alpha()
sword_img = pygame.transform.scale(sword_img, (50, 80))

dragon_index = 0

MAX_HP = 100_000
hp = MAX_HP
damage = 100
clicks = 0

# Позиция дракона
dragon_rect = dragons[0].get_rect(center=(WIDTH // 2, HEIGHT // 2 + 50))
hit_animation = 0
pygame.mouse.set_visible(False)
def draw_hp_bar():
    bar_width = 400
    hp_ratio = max(hp / MAX_HP, 0)
    pygame.draw.rect(screen, RED, (200, 50, bar_width, 25))
    pygame.draw.rect(screen, GREEN, (200, 50, bar_width * hp_ratio, 25))
def next_dragon():
    global dragon_index, hp, dragon_rect
    dragon_index = (dragon_index + 1) % len(dragons)
    hp = MAX_HP
    dragon_rect = dragons[dragon_index].get_rect(center=(WIDTH // 2, HEIGHT // 2 + 50))
# Игровой цикл
running = True
while running:
    screen.fill(WHITE)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.MOUSEBUTTONDOWN:
            if dragon_rect.collidepoint(event.pos):
                hp -= damage
                clicks += 1
                hit_animation = 6

                if hp <= 0:
                    next_dragon()

    # Анимация удара (тряска)
    offset = 5 if hit_animation > 0 else 0
    if hit_animation > 0:
        hit_animation -= 1
    # Дракон
    screen.blit(
        dragons[dragon_index],
        dragon_rect.move(offset, 0)
    )
    # HP бар
    draw_hp_bar()

    # Текст
    hp_text = font.render(f"HP: {hp}", True, BLACK)
    click_text = font.render(f"Клики: {clicks}", True, BLACK)
    screen.blit(hp_text, (200, 80))
    screen.blit(click_text, (20, 20))

    # Меч-курсор
    mouse_x, mouse_y = pygame.mouse.get_pos()
    screen.blit(sword_img, (mouse_x - 10, mouse_y - 70))

    pygame.display.flip()
    clock.tick(60)

pygame.quit()
sys.exit()
